---
title: "하울 안드로이드 인스타그램 클론 코딩 #12 유저 페이지 구현 #3"
excerpt: "프로필 이미지 변경 및 팔로우 기능 구현"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-05-15T20:40:15+09:00
toc: true
---

## UserFragment

**프로필 클릭 리스너**

```kotlin
override fun onCreateView(
    inflater: LayoutInflater,
    container: ViewGroup?,
    savedInstanceState: Bundle?
): View? {
    // ...
    fragmentView?.account_iv_profile?.setOnClickListener {
        var photoPickerIntent = Intent(Intent.ACTION_PICK)
        photoPickerIntent.type = "image/*"
        activity?.startActivityForResult(photoPickerIntent, PICK_PROFILE_FROM_ALBUM)
    }
    // ...
}
```

사용자의 프로필 이미지 클릭 리스너를 지정.

**startActivityForResult**: 프로필 이미지 클릭시 이미지를 선택하는 액티비티를 실행하고 결과를 반환.

실행한 액티비티의 결과는 MainActivity의 onActivityResult() 에서 수신한다.



**onActivityResult(MainActivity)**

```kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)

    if (requestCode == UserFragment.PICK_PROFILE_FROM_ALBUM && resultCode == Activity.RESULT_OK) {
        var imageUri = data?.data
        var uid = FirebaseAuth.getInstance().currentUser?.uid
        var storageRef =
            FirebaseStorage.getInstance().reference.child("userProfileImages").child(uid!!)
        storageRef.putFile(imageUri!!).continueWithTask {
            return@continueWithTask storageRef.downloadUrl
        }.addOnSuccessListener { uri ->
            var map = HashMap<String, Any>()
            map["image"] = uri.toString()
            FirebaseFirestore.getInstance().collection("profileImages").document(uid).set(map)
        }
    }
}
```

UserFragment의 **account_iv_profile** click listener에서 **startActivityForResult의** **콜백함수**

액티비티가 결과가 정상이고 요청 코드가 account_iv_profile click listener 인 경우 if 문 내부를 수행한다.

프로필 계정의 uid로 firebase storage 객체를 얻는다. 

storage의 userProfileImages 폴더에 선택한 이미지를 저장하고, 이미지의 downloadUrl을 DB에 사용자 별로 저장한다.



**getProfileImage()**

```kotlin
fun getProfileImage() {
    firestore?.collection("profileImages")?.document(uid!!)
        ?.addSnapshotListener { documentSnapshot, firebaseFirestoreException ->
            if (documentSnapshot == null) return@addSnapshotListener
            if (documentSnapshot.data != null) {
                var url = documentSnapshot?.data!!["image"]
                Glide.with(activity!!).load(url).apply(RequestOptions().circleCrop())
                    .into(fragmentView?.account_iv_profile!!)
            }
        }
}
```

프래그먼트가 실행될 때 호출되며 DB에서 프로필 이미지의 Url을 받아서 프로필 이미지 뷰에 지정한다.



**FollowDTO**

```kotlin
data class FollowDTO(
    var followerCount: Int = 0,
    var followers: MutableMap<String, Boolean> = HashMap(),

    var followingCount: Int = 0,
    var followings: MutableMap<String, Boolean> = HashMap()
)
```

사용자별로 팔로잉 수와 팔로워 수를 저장하는 data class



**requestFollow**

```kotlin
fun requestFollow() {
    var tsDocFollowing = firestore?.collection("users")?.document(currentUserId!!)
    firestore?.runTransaction { transaction ->
        var followDTO = transaction.get(tsDocFollowing!!).toObject(FollowDTO::class.java)
        if (followDTO == null) {
            followDTO = FollowDTO()
            followDTO!!.followingCount = 1
            followDTO!!.followings[uid!!] = true

            transaction.set(tsDocFollowing, followDTO)
            return@runTransaction
        }

        if (followDTO.followings.containsKey(uid)) {
            followDTO?.followingCount = followDTO?.followingCount - 1
            followDTO?.followings?.remove(uid)
        } else {
            followDTO?.followingCount = followDTO?.followingCount + 1
            followDTO?.followings[uid!!] = true
        }
        transaction.set(tsDocFollowing, followDTO)
        return@runTransaction
    }

    var tsDocFollower = firestore?.collection("users")?.document(uid!!)
    firestore?.runTransaction { transaction ->
        var followDTO = transaction.get(tsDocFollower!!).toObject((FollowDTO::class.java))
        if (followDTO == null) {
            followDTO = FollowDTO()
            followDTO!!.followerCount = 1
            followDTO!!.followers[currentUserId!!] = true

            transaction.set(tsDocFollower, followDTO!!)
            return@runTransaction
        }

        if (followDTO!!.followers.containsKey(currentUserId)) {
            followDTO!!.followerCount = followDTO!!.followerCount - 1
            followDTO!!.followers.remove(currentUserId!!)
        } else {
            followDTO!!.followerCount = followDTO!!.followerCount + 1
            followDTO!!.followers[currentUserId!!] = true
        }
        transaction.set(tsDocFollower, followDTO!!)
        return@runTransaction
    }
}
```

팔로우 버튼의 클릭 리스너.

팔로우 버튼을 누르면 현재 계정의 팔로우 데이터와 대상의 팔로잉 데이터가 변경되어야 한다.

각각의 경우에서 DB에 데이터가 없다면 새로 생성하고 팔로잉 및 팔로워 숫자를 수정하는 코드.



**getFollowerAndFollowing**

```kotlin
fun getFollowerAndFollowing() {
    firestore?.collection("users")?.document(uid!!)
        ?.addSnapshotListener { documentSnapshot, firebaseFirestoreException ->
            if (documentSnapshot == null) return@addSnapshotListener
            var followDTO = documentSnapshot.toObject(FollowDTO::class.java)
            if (followDTO?.followingCount != null) {
                fragmentView?.account_tv_following_count?.text =
                    followDTO?.followingCount?.toString()
            }
            if (followDTO?.followerCount != null) {
                fragmentView?.account_tv_follower_count?.text = followDTO?.followerCount?.toString()
                if (followDTO?.followers?.containsKey(currentUserId!!)) {
                    fragmentView?.account_btn_follow_signout?.text =
                        getString(R.string.follow_cancel)
                    fragmentView?.account_btn_follow_signout?.background?.setColorFilter(
                        ContextCompat.getColor(activity!!, R.color.colorLightGray),
                        PorterDuff.Mode.MULTIPLY
                    )
                } else {
                    if (uid != currentUserId) {
                        fragmentView?.account_btn_follow_signout?.text =
                            getString(R.string.follow)
                        fragmentView?.account_btn_follow_signout?.background?.colorFilter = null
                    }
                }
            }
        }
}
```

프래그먼트가 시작할 때 실행되며 현재 팔로워와 팔로잉 수를 DB에서 받아오고 변경시 실시간으로 변경 사항이 적용된다.

[snapshot listener 참고](https://firebase.google.com/docs/firestore/query-data/listen?hl=ko#kotlin)

