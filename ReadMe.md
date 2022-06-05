## 小程序期末项目

### 没什么用的练习



1. 数组合并：(先展开老的数组，在添加新的数组)
```
datalist:[...this.data.datalist,this.data.mytext]
```

2. btnList(event){}
3. 传参：`data-index="{{index}}"`
4. 截断数组：`this.dataset.splice(arr,1)`



[json-server]: https://github.com/typicode/json-server

install JSON Server

```
npm install -g json-server
```

Create a `db.json` file with some data

```
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

Start JSON Server

```
json-server --watch db.json --port 5000
```

Now if you go to http://localhost:3000/posts/1, you'll get

```
{ "id": 1, "title": "json-server", "author": "typicode" }
```





### 废话少说 直接实战

request 封装

- 根目录创建 util\request.js

  ```
  function request(){
    return new Promise((resolve,reject)=>{
      wx.request({
        url: 'url',
        success: (res) => {
          resolve(res.data)
        },
        fail: (err) => {
          reject(err)
        },
      })
    })
  }
  ```

  ```
  使用
  import request from "../../util/request.js"
  ```



1.首页模块-轮播

```
本地资源
http://localhost:3000/images/0/a.jpg
```

2.查询数据

```
http://localhost:3000/goods?_page=5&_limit=2
```

3.懒加载

```
onReachBottom() {
        // 为页面进行懒加载
        this.current++
        console.log("到底了");
        wx.request({
            url: `http://localhost:3000/goods?_page=${this.current}&_limit=5`,
            method: "GET",
            success: (result) => {
                console.log(result.data);
                this.setData({
                    // 这里不能直接覆盖原数组，要进行数组合并
                    // goodList:result.data
                    goodList:[...this.data.goodList,...result.data]
                })
            },
            fail: (res) => {},
          })

    },
```

4.搜索框

5.导航栏标题

`wx.setNavigationBarTitle`

6.模糊查询 k

`http:localhost:3000/goods?title_like=k`

7.获取用户信息

```
wx.getUserProfile({
            desc: '用于完善会员资料', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
            success: (res) => {
                console.log(res.userInfo);
                wx.setStorageSync('token', res.userInfo)
                // this.setData({
                //     userInfo: res.userInfo,
                //     hasUserInfo: true
                // })
                wx.navigateTo({
                  url: '/pages/telgorm/telform',
                  success: (result) => {},
                  fail: (res) => {},
                })
            }
        })
```

8.数据更新

```
request ==> method:"put"
```

```
// 如果查询结果为空数组，表示该商品用户没有加入购物车
                    if (res.data.length === 0) {
                        wx.request({
                            url: 'http://localhost:3000/carts',
                            data: {
                                "username": nickName['nickName'],
                                "tel": tel,
                                "goodId": goodId,
                                "number": 1,
                                "checked": false
                            },
                            method: "POST",
                            success: (res) => {
                                console.log("数据追加");
                            },
                        })
                    // 如果数组有结果，则表示当前商品已经加入购物车，需要把商品数量+1
                    }else{
                        wx.request({
                          url: `http://localhost:3000/carts/${res.data[0].id}`,
                          data: {
                              // 这里不能覆盖原先的数据
                              ...res.data[0],number:res.data[0].number+1
                          },
                          method: "PUT",
                          success: (res) => {
                            wx.showToast({
                              title: '商品数+1',
                            })
                          },
                          fail: (res) => {},
                        })
                    }
```

9.回退

```
wx.navigationBack({
	delta:2
})
```

