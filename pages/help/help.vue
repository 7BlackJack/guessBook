<template>
    <view class="personal">
        <view class="header">
            <!-- 登录状态 -->
            <view v-if="userInfo.nickName">
                <image class="img" :src="userInfo.avatarUrl" mode=""></image>
                <text>{{userInfo.nickName}}</text>
                <button class="exit" @click="exit" type="warn">退出登录</button>

            </view>


            <!-- 未登录的 -->
            <view v-else>
                <image class="img" src="../../static/user.png" mode=""></image>
                <text class="login" @click="toLogin">去登录</text>

            </view>


        </view>

    </view>
</template>

<script>
    export default {
        data() {
            return {
                userInfo: {}, //个人信息
            };
        },
        mounted() {
            uni.getStorage({
                key: "userinfo",
                success: res => {
                    console.log(res.data);
                    this.userInfo = JSON.parse(res.data);
                }
            })
        },
        methods: {
            toLogin() {
                // 关闭所有页面， 跳转到新页面
                uni.reLaunch({
                    url: "/pages/login/login"
                })
            },
            exit() {
                uni.setStorage({
                    key: "userinfo",
                    data: "",
                })
                uni.reLaunch({
                    url: "/pages/index/index"
                });
            }
        }
    }
</script>

<style lang="scss">
    .personal {
        .header {
            line-height: 200rpx;
            height: 300rpx;
            background-color: #EED7B5;
            display: flex;

            .img {
                height: 100rpx;
                width: 100rpx;
                vertical-align: middle;
                margin: 50rpx;
            }

            .exit {
                height: 80rpx;
            }
        }
    }
</style>
