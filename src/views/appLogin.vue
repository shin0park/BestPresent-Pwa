<template>
    <div class="appLogin">
        <div class="flex-box">
            <div class="appTitle">
                <div>
                    <img src="../assets/B.P.png">
                </div>
                <div>
                    <img src="../assets/bestpresent.png">
                </div>
                <div>
                    <img src="../assets/bestpick.png">
                </div>
                <div>
                    <img src="../assets/bestpleasure.png">
                </div>
            </div>
            <div class="btnBox">
                <button class="facebookBtn" v-on:click="facebookLogin()"><img src="../assets/facebookLogo.png">Facebook
                </button>
                <button class="googleBtn" v-on:click="googleLogin()"><img src="../assets/googleLogo.png">Google</button>
            </div>
        </div>
    </div>
</template>
<script>
    import router from '../router'
    import profile_img from '../assets/defalut_profile.png';

    export default {
        data() {
            return {
                test: {},
                result: ""
            }
        },
        computed: {
        },
        methods: {
            successLogin() {
                this.$router.push({name: 'friendsTap'});
                this.$emit('changeIsLogin');
            },
            async isUser(uid, uname) {
                let flag = await this.$api.readUser(uid);
                //가입
                if (flag == null) {
                    await this.$api.addUser(uid, uname);
                    alert("가입되었습니다!");
                    this.$user.email = uid;
                    this.$user.displayName = uname;
                    this.$user.login = true;
                    this.$emit('changeIsLogin');
                    flag = await this.$api.readUser(uid);
                }
                await this.$api.updateFriendsList(uid);
                this.$user.birthRaw = flag.birth;
                if (flag.birth === "false" || flag.birth === false) {
                    this.$user.birth = `생일을 입력해주세요`;
                } else {
                    let birthday = flag.birth.split("-");
                    this.$user.birth = `${birthday[0]}월 ${birthday[1]}일`;
                }
                if (flag.profile === "false" || flag.birth === false) {
                    this.$user.profile = await this.$storage.getUrl(`image/profile/defalut_profile.png`);
                } else {
                    this.$user.profile = await this.$storage.getUrl(`image/profile/${uid}`);
                }

                const friendsList = await this.$api.readFriendsList(uid);
                this.$user.friendsList = friendsList;
                for(let i = 0; i < friendsList.length; i++){
                    if (friendsList[i].profile === false || friendsList[i].profile === "false") {
                        this.$user.friendProfile.push(await this.$storage.getUrl(`image/profile/defalut_profile.png`));
                    } else {
                        this.$user.friendProfile.push(await this.$storage.getUrl(`image/profile/${friendsList[i].id}`));
                    }
                }
                const birthdayList = await this.$api.addBirthdayFriend(uid);
                this.$user.birthdayList = birthdayList;
                for(let i = 0; i < birthdayList.length; i++){
                    if (birthdayList[i].profile === false || birthdayList[i].profile === "false") {
                        this.$user.birthdayProfile.push(await this.$storage.getUrl(`image/profile/defalut_profile.png`));
                    } else {
                        this.$user.birthdayProfile.push(await this.$storage.getUrl(`image/profile/${birthdayList[i].id}`));
                    }
                }

            },
            async facebookLogin() {
                const res = await this.$auth.facebookLogin();
                const uid = (res.user.providerData[0].email == null) ? (res.user.providerData[0].uid) : (res.user.providerData[0].email);
                const uname = res.user.displayName;
                await this.isUser(uid, uname);
                this.$user.email = uid;
                this.$user.displayName = uname;
                this.$user.login = true;
                this.result = "로그인 성공";
                await this.successLogin();
            },
            async googleLogin() {
                const res = await this.$auth.googleLogin();
                await this.isUser(res.user.email, res.user.displayName);
                this.$user.email = res.user.email;
                this.$user.displayName = res.user.displayName;
                this.$user.login = true;
                this.result = "로그인 성공";
                await this.successLogin();
            }
        }
    }
</script>
<style scoped>
    .appLogin {
        display: flex;
        justify-content: center;
        background-image: url("../assets/login.png");
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
        text-align: center;
        height: 100%
    }

    .flex-box {
        align-self: center;
    }

    .appTitle {
        margin-bottom: 168px;
    }

    .appTitle > div {
        margin-bottom: 6px;
    }

    button {
        position: relative;
        width: 256px;
        height: 48px;
        font-size: 16px;
        font-weight: 900;
        line-height: 48px;
        box-sizing: border-box;
    }

    .facebookBtn {
        background-color: #4267B2;
        padding-left: 16px;
        margin-bottom: 28px;
        color: white;
    }

    .googleBtn {
        background-color: white;
        padding-left: 14px;
        color: #757575;
    }

    button img {
        position: absolute;
        top: 11px;
        left: 30px;
    }
</style>