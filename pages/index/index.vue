<template>

    <view class="content">
        <button @click="getUserInfo()">请登录</button>
        <text>{{this.nickName}}</text>
        <!-- 其他功能（未开发） -->
        <view class="wrap">
            <u-row gutter="16">
            	<u-col span="4">
            		<view class="demo-layout bg-purple">私信</view>
            	</u-col>
            	<u-col span="4">
            		<view class="demo-layout bg-purple-light">附近</view>
            	</u-col>
            	<u-col span="4">
            		<view class="demo-layout bg-purple-dark">好友</view>
            	</u-col>
            </u-row>

        </view>
        <!-- 发表留言 -->
        <view class="LeaveMessage" v-for="(res, index) in commentList" :key="res.id">
            <view class="information">
                <u-avatar :src="res.url" mode="square" class="avatar"></u-avatar>
                <text class="UserName">{{res.name}}</text>
				<view class="ubutton">
					<u-button size="mini" @click="chooseImage()">添加图片</u-button>
				</view>
				
				<!-- <u-icon name="plus-circle" color="#2979ff" size="28" @click="chooseImage()"></u-icon> -->
            </view>
            <view class="editmessage">
				<view class="showmessage">
					<textarea name="" id="" cols="30" rows="10" v-model="content"></textarea>
					<view class="showpic">
						<img :src="img" alt="" srcset="">
					</view>
				</view>
                
                <button @click="publish()" >发表留言</button>
                <!-- <button @click="chooseImage()">上传图片</button> -->
            </view>
        </view>

        <!-- 个人留言记录展示 -->
        <view v-if="flag">
            <view class="comment" v-for="(res, index) in commentList" :key="res.id">
                <view class="" v-for="(item,index) in testlist">
                    <view class="top">
                        <image :src="res.url" mode="aspectFill"></image>
                        <view class="name">{{res.name}}</view>
                    </view>
                    <view class="content">
                        {{item.txt}}
                    </view>
                </view>
            </view>
        </view>
        <view class="showtips" v-else>
            <view>快来发表你的留言吧~</view>
        </view>

    </view>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                commentList: [], //个人信息
                testlist: [], //留言记录+个人id
                content: '', //当前留言内容
                flag: false,
                nickName: '',
                tuPiansrc: '',
				img:''
				
            }
        },
        onLoad() {
            this.getComment();

        },
        methods: {
            // 个人信息
            //根据自己的昵称获取曾经的留言
            getComment() {
                this.commentList = [{
                    id: 1,
                    name: '昵称',
                    url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg', //用户头像**
                }]
            },
			
            // 发布留言功能
            publish() {
                console.log("发布时间" + Date().toLocaleString)
                uniCloud.callFunction({
                    name: 'fun',
                    data: {
                        api: 'publish', //调用publish方法
                        content: this.content, //文本框输入的内容
                        publishTime: new Date().toLocaleString(), //获取当前时间
                        tuPiansrc: this.tuPiansrc //上传图片ID
                    }
                }).then(res => {
                    console.log(res)
                    //发布后自动刷新当前页面
                    var pages = getCurrentPages();
                    var currPage = pages[pages.length - 1];
                    uni.reLaunch({
                        url: currPage.$page.fullPath
                    })
                })
            },
			
            getUserInfo() {
                uni.getUserProfile({
                    desc: '用于获取用户资料', // 这个参数是必须的
                    success: res => {
                        console.log("用户信息", res.rawData);
                        console.log(res);
                        uni.setStorage({
                            key: "userinfo",
                            data: JSON.stringify(res.userInfo)
                        });
                        /* 页面跳转，回到个人中心*/
                        uni.reLaunch({
                            url: "/pages/help/help"
                        });
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
            }, //getUserInfo

            //上传图片
            chooseImage() {
                uni.chooseImage({
                    count: 1, //1张图片
                    sizeType: ['compressed'], //压缩图
                    success: (res) => {
                        let tempFilePaths = res.tempFilePaths;
						this.img=res.tempFilePaths;
                        //对每一张图片
                        tempFilePaths.forEach((item) => {
                            //上传到云存储
                            uniCloud.uploadFile({
                                //图片的临时地址
                                filePath: item,
                                //云存储字段
                                cloudPath: 'tuPian.jpg',
                            }).then(res => {
                                //输出图片的fileID
                                console.log("fileID=" + res.fileID)
                                //this.tuPiansrc就是图片地址
                                this.tuPiansrc = res.fileID;
                            })
                        })
                    }
                })
            } //chooseImage

        } //methods
    }
</script>

<style lang="scss">
    .content {
        background: -webkit-linear-gradient(top, #E5EED3, white);
        height: 100%;
        width: 100%;
        padding: 0rpx;
        margin: 0rpx;
		
    }

    // 其他功能（未开发）
    .wrap {
        padding: 24rpx;

        .u-row {
            margin: 40rpx 0;
        }

        .demo-layout {
            height: 80rpx;
            border-radius: 8rpx;
            text-align: center;
            line-height: 80rpx;
            background: #F1E6A7;
            color: gray;
        }

    }


    // 发表留言
    .LeaveMessage {
        .img {
            height: 200px;
            width: 200px;
        }

        padding-inline: 50rpx;

        .information {
			display: block;
			font-size: 0;
			content: " ";
			clear: both;
			.ubutton{
				float:right;
				
			}
            .UserName {
                font-size: 18px;
                padding-left: 24rpx;
                font-weight: bold;
                bottom: 0rpx;
            }

            .Signature {
                padding-left: 24rpx;
                color: gray;
            }

            .hiddenInput {
                display: none;
            }
        }

        .editmessage {
            margin-top: 24rpx;
            height: 100%;
            width: 100%;
            border-radius: 20px;
            background: -webkit-linear-gradient(top, #F1E6A7, white);
			.showpic img{
				width: 200rpx;
				height: 200rpx;
				padding: 24rpx;
			}
        }
		
		
        .editmessage textarea {
            padding: 24rpx;
			height: 150rpx;
        }

        .editmessage button {
            background-color: #FBDA95;
            color: #9a9a9a;
            border: 0rpx;
        }
    }


    // 个人留言记录展示
    .comment {
        padding-inline: 50rpx;
        padding-block: 30rpx;

        .top {
            display: flex;

            image {
                width: 64rpx;
                height: 64rpx;
                border-radius: 50%;
                background-color: #f2f2f2;
            }

            .name {
                padding-left: 24rpx;
                line-height: 64rpx
            }
        }

        .content {
            height: 300rpx;
            background: -webkit-linear-gradient(top, #CDE1A7, white);
            border-radius: 20px;
            margin-top: 15rpx;
            padding: 18rpx;
        }

    }

    .showtips {
        height: 300rpx;
        width: 100%;
        display: flex;
        color: #9a9a9a;

        view {
            margin: auto;
        }
    }
</style>
