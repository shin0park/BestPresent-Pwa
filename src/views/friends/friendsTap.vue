<template>
    <div>
        <section class="friendsTap">
            <div class="titleBox">
                <h1 class="title">친구</h1>
                <button class="addFriend" @click="showModal = true"><i class="material-icons">
                    person_add
                </i></button>
            </div>
            <div class="searchBox">
                <i class="material-icons">
                    search
                </i>
                <input type="text" placeholder="친구 검색" class="searchInput" v-model="searchValue">
            </div>
            <div class="user" @click="onClickUserProfile">
                <span class="userImg"><img v-bind:src=userProfile></span>
                <h3 class="userName">{{userID}}</h3>
            </div>
            <div class="friendsBirthday" v-if="!searchValue">
                <h2 class="friendsTitle">생일인 친구</h2>
                <birthday-friends v-for="(birthday, index) in birthdayList" :key="index"
                                  :birthday-index="index"></birthday-friends>
            </div>
            <div class="friends">
                <h2 class="friendsTitle">친구</h2>
                <friends-list v-for="(friend, index) in friendsList" :key="index" :friends-index="index"
                              :search-value="searchValue"></friends-list>
            </div>
            <AddFriendModal v-if="showModal" @close="showModal = false">
                <h3 slot="header" style="flex-grow: 1">친구추가</h3>
                <button slot="header" class="clearBtn" @click="showModal = false">
                    <i class="material-icons">
                        clear
                    </i>
                </button>
            </AddFriendModal>
        </section>
    </div>
</template>

<script>
    import friendsList from './friendsList'
    import birthdayFriends from './birthdayFriends'
    import AddFriendModal from "./AddFriendModal";
    export default {
        components: {
            'friendsList': friendsList,
            'birthdayFriends': birthdayFriends,
            'AddFriendModal': AddFriendModal
        },
        computed: {
            userID() {
                return this.$user.displayName;
            },
            userProfile() {
                return this.$user.profile;
            },
            friendsList() {
                return this.$user.friendsList;
            },
            birthdayList() {
                return this.$user.birthdayList;
            }
        },
        data() {
            return {
                searchValue: "",
                userProfileImg: this.$user.profile,
                showModal: false,
            }
        },
        methods: {
            onClickUserProfile() {
                window.scrollTo(0, 0);
                this.$router.push({name: 'userProfile'});
            },
        },
        async mounted() {
            const email = this.$user.email;
            let flag = await this.$api.readUser(email);
            if (flag.profile !== false) {
                this.$user.profile = await this.$storage.getUrl(`image/profile/${email}`);
            }
        }
    }
</script>

<style scoped>
    .friendsTap {
        padding: 0 16px;
        margin-top: 30px;
        width: 100%;
        box-sizing: border-box;
    }
    .titleBox::after {
        display: block;
        content: "";
        clear: both;
    }
    .titleBox {
        margin-bottom: 16px;
    }
    .title {
        margin: 0 0 0 10px;
        font-size: 21px;
        font-weight: 900;
        float: left;
        line-height: 40px;
    }
    .clearBtn {
        position: absolute;
        top: 10px;
        right: 10px;
    }
    .addFriend {
        float: right;
        height: 40px;
        width: 40px;
        margin-right: 10px;
        vertical-align: middle;
    }
    .searchBox {
        position: relative;
        margin-bottom: 30px;
        padding: 10px 18px 0 50px;
        border-radius: 20px;
        background-color: white;
        width: 100%;
        height: 40px;
        box-shadow: 2px 2px 6px #bdbdbd;
        box-sizing: border-box;
    }
    .searchBox .material-icons {
        position: absolute;
        left: 14px;
        top: 8px;
    }
    .searchInput {
        width: 100%;
        font-size: 14px;
        font-weight: 400;
    }
    .user {
        display: flex;
        padding-bottom: 20px;
        border-bottom: 1px solid #eeeeee;
    }
    .userImg {
        flex-basis: 70px;
        height: 70px;
        align-self: center;
        margin-left: 10px;
    }
    .userImg img {
        width: 100%;
        height: 100%;
        border-radius: 50%;
    }
    .userName {
        flex-grow: 1;
        align-self: center;
        padding: 0 24px;
        font-size: 16px;
        font-weight: 900;
    }
    .friendsBirthday {
        padding: 20px 0;
        border-bottom: 1px solid #eeeeee;
    }
    .friendsTitle {
        margin: 0 0 10px 10px;
        font-size: 12px;
        font-weight: 400;
        color: #757575;
    }
    .friendsImg img {
        width: 100%;
    }
    .friends {
        padding: 20px 0;
    }
</style>