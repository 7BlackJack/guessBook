<template>
    <view>
		
        <button open-type="getUserInfo" @click="getUser" type="primary">登录</button>
    </view>
</template>

<script>
    export default {
        data() {
            return {

            };
        },


        methods: {
            getUser() {
                uni.getUserProfile({
                    desc: "用于获取用户资料",
                    success: res => {
                        console.log(res);
                        uni.setStorage({
                            key: "userinfo",
                            data: JSON.stringify(res.userInfo)
                        });
                        /* 页面跳转，回到个人中心*/
						uni.switchTab({
						    url: "/pages/help/help",
						})
                    },
                    fail: err => {
                        console.log(err)
                        uni.showToast({
                            title: "获取失败",
                            icon: "error",
                            duration: 2000,
                        });
                    }
                })
				
            }
        }
    }
</script>

<style lang="scss">

</style>
