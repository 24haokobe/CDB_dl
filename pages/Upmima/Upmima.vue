<template>
    <view class="changepwd">
        <view labelPosition="left" name="fr" labelWidth="80" :model="model1" :rules="rules" ref="form1" class="zd">
            <view label="新密码" name="form1" prop="userInfo.newpwd" borderBottom ref="item1">
                <input class="inputx" name="text1" @input="inputContext" type="password" placeholder="请输入新密码" inputAlign="left" v-model="model1.userInfo.newpwd"
                    border="none"></input>
            </view>
            <view label="确认密码" name="form2" prop="userInfo.newpwdr" borderBottom ref="item1">
                <input class="inputx" name="text2" @input="inputContext1" type="password" placeholder="请再次输入新密码" inputAlign="left" v-model="model1.userInfo.newpwdr"
                    border="none"></input>
            </view>
        </view>
        <button class="mt50" @click="submit" type="primary">修改</button>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                model1: {
                    userInfo: {
                        oldpwd: '',
                        newpwd: '',
                        newpwdr: '',
						devSn1:'',
						userCode1:'',
						Authorization1:'',
                    },
                },
                rules: {
                    'userInfo.newpwd': {
                        type: 'string',
                        required: true,
                        message: '请再次新密码',
                        trigger: ['blur', 'change']
                    },
                    'userInfo.newpwdr': {
                        type: 'string',
                        required: true,
                        message: '请再次输入新密码',
                        trigger: ['blur', 'change']
                    },
                },
            };
        },
        methods: {
			onLoad :function(e){
				this.devSn1 = e.devSn
				this.userCode1 = e.userCode
				this.Authorization1 = e.Authorization
				console.log("devSn=" + e.devSn)
				console.log("userCode=" + e.userCode)
				console.log("Authorization=" + this.Authorization1)
			},
			inputContext(e){
				this.newpwd = e.target.value;
			},
			inputContext1(e){
				this.newpwdr = e.target.value;
			},
            submit() {
				var pass = this;
				console.log("pass.newpwd=" + pass.newpwd);
				console.log("pass.newpwdr=" + pass.newpwdr)
				if(pass.newpwd !=null && pass.newpwdr != null){
					if(pass.newpwd !="88888888" || pass.newpwdr != "88888888"){
						if(pass.newpwd == pass.newpwdr){
							uni.request({
								url:"/api/kaying/v1/huabang/modifyPwd?userCode=" + pass.userCode1 + "&userPwd=" + pass.newpwd,
								header:{
										'Authorization':pass.Authorization1
								},
								method:'POST',
								success: (res) => {
									console.log(res.data)
									if(res.data.msg == "操作成功"){
										uni.showToast({
											title: '修改成功，请重新登录',
											icon:'none',//不要图标
											duration: 1000//1后消失
										}),
										uni.reLaunch({
											url:"../index/index?devSn=" + pass.devSn1
										})
									}
									}
							})
						}else{
							uni.showToast({
								title: '两次密码不一样，请重新输入密码',
								icon:'none',//不要图标
								duration: 2000//1后消失
							});
					}
				}else{
					uni.showToast({
						title: '密码不能设置为88888888',
						icon:'none',//不要图标
						duration: 2000//1后消失
					});
				}
				}else{
					uni.showToast({
						title: '密码不能为空',
						icon:'none',//不要图标
						duration: 2000//1后消失
					});
				}
            },
			getBack(name) {
			   // 获取当前页上的栈（数组形式）
			   var pages = getCurrentPages()
			   //上一页面 
			   var prevPage = pages[pages.length - 3] // （pages.length - 3 上上页 pages.length - 1当前页，以此类推）
			   prevPage.setData({
			       //直接给上一个页面赋值
			       chooseName: name // 注意： name必须是A页面data定义过得字段
			   })
			   // 返回上一页
			   uni.navigateBack({delta: 2})	
			}
        }
    }
</script>

<style>
    .changepwd {
        width: 690rpx;
        margin: 0 auto;
    }

.zd{
	padding-top: 70rpx;
}
    .inputx {
        width: 100%;
		height: 50rpx;
		padding-top: 30rpx;
		border-radius: 0 16rpx 16rpx 0;
		border-bottom: 2rpx solid #bababa;
        text-align: left;
    }

    .mt50 {
        margin-top: 50rpx;
    }
</style>
