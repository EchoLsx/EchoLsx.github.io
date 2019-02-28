---
title: 小程序的wx.request请求封装
catalog: true
date: 2018-09-30 15:51:04
subtitle:
header-img:
tags: 
    - 小程序 
    - JavaScript
---

*经常会遇到要对请求的某个code做统一的处理，比如headers带Token的时候*
 所以自己封装了个wx.request的插件

#### 1、使用promise封装

 ```javascript

 const app = getApp()
// const Promise = require('./es6-promise.js')
/**
 * 创建网络服务
 * 每一个服务方法返回一个promise
 * @function netRequest
 * @param   {object}        config                 - 配置
 * @param   {string}        config.url             - 请求服务器地址(带协议)
 * @param   {string}        config.method          - 请求方式 
 * @param   {Object}        config.header          - 设置请求的 header ，header 中不能设置 Referer
 * @param   {Object}        config.data            - 请求的data数据
 * @return  {Object}        返回一个网络服务
 */
function netRequest(config) {
    let promise = new Promise(function(resolve, reject) {
        wx.showNavigationBarLoading()
        // let session_key = wx.getStorageSync(SESSION_KEY) // 如果用的是Storage的形式存储的话
        let token = app.globalData.token ? app.globalData.token : '';
        if (token) {
            config.header['Authorization'] = token  // Authorization这个键名可以和后端商量
        }
        wx.request({
          url: config.url,
          data: config.data,
          method: config.method,
          header: config.header,
          success: function (res) {
            if(res.statusCode == 200 && res.data.code!==401) {
              resolve(res.data);
              return
            }
            //401:需要登录状态
            if (res.statusCode == 200 && res.data.code == 401) {
            //返回登录页面
              wx.showToast({
                title: 'token过期，请重新登录',
                icon: 'none', 
                duration: 1500,
                mask: true
              })
              setTimeout(()=>{
                wx.navigateTo({
                    url: '../login/login', // 跳转到登录页面
                })
              },1500)
              reject(res);
              return
            }
          },
          fail: function (res) {
            reject(res);
          },
          complete() {
            wx.hideNavigationBarLoading()
          }
        });
    });
    return promise;
}

export default netRequest;
 
 ```


##### 2、调用方式
 ```javascript
 import netRequest from '../../utils/netRequest.js';

 ...

 getList(){
    let url = API.domain + API.houseList;
    let obj = {
      url: url,
      method: "GET",
      header:{
        //   xxx:xxx
      },
      data: {
        userIndexCode: app.globalData.userIndexCode,
      }
    };
    netRequest(obj).then(
      res => {
         console.log(res);
        }
      }).catch( err => {
        // 这里是reject的处理
        wx.showToast({
          title: err,
          icon: 'loading', 
          duration: 1500,
          mask: true
        })
    })
  }

 ```

