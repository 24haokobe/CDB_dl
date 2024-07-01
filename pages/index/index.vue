<template>
    <view class="content">
       <view class="avatarWrapper">
       	<view class="avatat">
       		<image class="img" src="../../static/logo.png" mode="widthFix"></image>
       	</view>
       </view>
	   <view class="form">
	   	<view class="inputWrapper">
	   		<input class="input" @input="inputContext" type="text" name="yonghumin" placeholder="请输入用户名">
	   	</view>
		<view class="inputWrapper">
			<input class="input" @input="inputContext1" type="password" name="mima" placeholder="请输入密码">
		</view>
		<view>
			<button form-type="submit" class="loginBtn" @tap="formSubmit()">登录</button>
		</view>
	   </view>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                title: 'Hello',
				 yonghuming:"",
				    mima:"",
					data1:" "
            }
        },
		onShow() {
		   // this.pages = getCurrentPages()
		   // this.currPage = this.pages[this.pages.length - 1] //当前页
		   // // 直接用this.currPage.data去取B页面传递过来的数据
		   // this.chooseName = this.currPage.data.chooseName
		},
        onLoad: function (opts) {
			// var paraString = location.search;
			// var paras = paraString.split("=");
			// 			console.log("paras=" + paras[1])
        },
		methods: {
			inputContext(e){
				this.yonghuming = e.target.value;
			},
			inputContext1(e){
				this.mima = e.target.value;
			},
			getParam(path, name) {
			        var reg = new RegExp("(^|\\?|&)" + name + "=([^&]*)(\\s|&|$)", "i");
			        if (reg.test(path))
			
			        return unescape(RegExp.$2.replace(/\+/g, " "));
			
			        return "";
			
			        },
		      /**发布提交 */
		      formSubmit(e) {
		        var that = this;
				// let local = location.href;
				// let devSn = this.getParam(local, "devSn");
				var paraString = location.search;
				var paras = paraString.split("=");
				console.log("paras=" + paras[1])
				console.log(this.yonghuming);
				console.log(this.mima);	
						uni.request({
						  url:"/api/kaying/v1/huabang/login?userCode=" + this.yonghuming + "&password=" + this.mima,
						  method: 'GET',
						  success: (res) => {
											console.log("res.data=" + res.data.data);
											that.data1 = res.data;
											var urlid = "../shouye/shouye?id=" + paras[1] + "&userCode=" + this.yonghuming + "&Authorization=" + res.data.data + "&password=" + this.mima;
						console.log("urlid=" + urlid);
										  if(res.data.desc=="成功"){
											  uni.reLaunch ({
											      url:urlid
											      })    
						    }
						    else{
						    uni.showToast({
						      title: '用户名或密码错误',
						      icon: 'none'
						    });    
						    }
						  }
						})
		      }
        }
    }
</script>

<style>
   .content{
	   background:#377EB4;
	   width: 100vw;
	   height: 100vh;
   }
   .avatarWrapper{
	   height: 30vh;
	   width: 100vw;
	   display: flex;
	   justify-content: center;
	   align-items: flex-end;
   }
   .avatat{
	   width: 200upx;
	   height: 200upx;
	   overflow: hidden;
   }
   .avatat .img{
	   width: 100%;
   }
   .form{
	   padding: 0 100upx;
	   margin-top: 80px;
   }
   .inputWrapper{
	   width: 100%;
	   height: 80upx;
	   background: white;
	   border-radius: 20px;
	   box-sizing: border-box;
	   padding: 0 20px;
	   margin-top: 25px;
   }
   .inputWrapper .input{
	   width: 100%;
	   height: 100%;
	   font-size: 15px;
	   text-align: center;
   }
   .loginBtn{
       width: 100%;
       height: 80upx;
       background: #77B3D7;
       border-radius: 50upx;
       margin-top: 50px;
       display: flex;
       justify-content: center;
       align-items: center;
	   color: white;
     }
     .loginBtn .btnValue{
       color: white;
     }
</style>
