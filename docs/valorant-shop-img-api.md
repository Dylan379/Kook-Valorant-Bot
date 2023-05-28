## 欢迎您使用阿狸瓦洛兰特商店Api

Api是一个网页链接，能够方便的被用户使用或被开发者调用，以实现特定功能。

> Api展示页 https://val.musnow.top/
>
> ✨在线测试和调用 https://valorant-shop.apifox.cn/
>
> Api根连接 https://val.musnow.top/api/v2/
>
> Api备用连接 https://val1.musnow.top/api/v2/


和阿狸机器人一样，**此api后台不会打印任何params参数**。只要链接不被攻击，你的账户密码只有调用者看得到！保证了隐私不泄露。

> 阿狸帮助频道有一个免费的token，加入帮助频道即可领取；
>
> 有任何问题，也欢迎加入帮助频道与我交流🎶 [kook帮助服务器邀请链接](https://kook.top/gpbTwZ)
>
> 独立Api token获取🎉 [爱发电](https://afdian.net/item/31f60e884a1c11ed9cff52540025c377)

| 接口 | 说明                  | 状态 |
| ---------- | --------------------- | -------- |
| /shop-img  | 登录，直接返回并跳转商店的图片  | 正常   |
| /shop  | 登录，以`json`格式返回商店图片url或者riot原始响应  | 正常   |
| /shop-draw  | 无需登录，提供4个皮肤uuid 返回图片url  | 正常   |
| /tfa  | 邮箱验证接口，需和`/shop-url`接口配合使用 | 正常   |

## 1.注意事项

自定义背景图的url请使用国内可以直接访问的图床，如`阿里云OSS/路过图床/白嫖kook的图床`等。否则无法获取到图片链接的内容，api会报错；

> * 路过图床：https://imgse.com/
> * kook图床白嫖教程：[点我](https://img.kookapp.cn/assets/2022-12/nICYcewY8a0u00yt.png)

欢迎大家向朋友宣传此Api，感谢支持！

## 2.shop-img

> 该接口和其他接口的最大区别在于，它是一个`一次性`接口，后台不会进行登录信息的cookie缓存，也不支持2fa用户；

查询每日商店的链接如下，GET调用 `/shop-img` 接口，浏览器会直接跳转图片，适合普通用户快捷查看当日每日商店。

~~~
https://val.musnow.top/api/v2/shop-img?token=API的密钥&account=账户&passwd=密码
~~~

补充好上面的链接后，直接丢浏览器里面打开就OK。可以浏览器收藏一下，方便后续查看！

* 添加第四个参数`&img_src=图片url`，此参数用于自定义背景图
* 添加第五个参数`&img_ratio=图片比例`，将此参数设置为`1`，代表背景图是 `1-1` 的正方形，最终的成果图也将是正方形；默认比例为 `16-9`
* 注：如果选择 `1-1` 的返回图，则图中不会带有vp/rp信息


若要添加自定义背景图，则链接应该如下
~~~
https://val.musnow.top/api/v2/shop-img?token=API的密钥&account=账户&passwd=密码&img_src=背景图片链接
~~~

如果背景图是正方形（1-1）
~~~
https://val.musnow.top/api/v2/shop-img?token=API的密钥&account=账户&passwd=密码&img_src=背景图片链接&img_ratio=1
~~~

自定义背景图请求示例（16-9）

~~~
https://val.musnow.top/api/v2/shop-img?token=API的密钥&account=账户&passwd=密码&img_src=https://img.kookapp.cn/assets/2022-09/KV5krdRx080qo0f0.jpg
~~~

结果示例图（16-9）

<img src="../screenshot/val_api_img2.png" weight="400px" alt="16-9-img-result">

结果示例图（1-1）

<img src="../screenshot/val_api_img1.png" weight="300px" hight ="300px" alt="1-1-img-result">

在参数中添加url参数，当其为0的时候，不会直接进行303跳转，而是返回包含图片url的json字符串。

~~~json
{
    "code": 0, 
    "message": "https://img.kaiheila.cn/attachments/2022-10/12/1GaII87UTd0zk0k0.png", 
    "info": "商店图片获取成功"
}
~~~


## 3.开发者接口

由于服务器是个6m小水管，再加上刚开放的时候本地缓存不足，画图耗时长。所以响应很慢，大约10秒。所以，我估摸着也没有开发者愿意用这种慢吞吞的api吧？

好处就是后台包装了**图片处理+riot登录**，你只需要传入账户密码，剩下的交给api解决！

>注：只有`code 0`才是获取正常，`200/400` 都是有错误，会返回错误的原因。

### 3.1 shop

如果你是开发者，请使用`/shop`来获取`json`格式的结果；

注意，请求此接口之前，请先请求 `/login` 和 `/tfa`

~~~
https://val.musnow.top/api/v2/shop
~~~

请求方法： `POST`

| body参数 | 说明                  | 参数类型 |是否必填 |
| ---------- | --------------------- | -------- | -------- |
| token      | API token             | string|是       |
| account    | 拳头账户              | string |是       |
| img_src    | 自定义背景图的url链接 | string | 否       |
| img_ratio    | 自定义返回图比例，值为1代表正方形 | int |否       |
| raw    | 设置为1，获取Riot接口的原始响应（不画图） | int | 否       |

```python
# 用户需要原始uuid
# raw参数在params中，且该参数不为0
isRaw = ('raw' in params and str(params['raw']) != '0')
# 判断是否有指定图片比例
# img_ratio在params中，且该参数不为非1
isimgRatio = ( 'img_ratio' not in params or str(params['img_ratio']) != '1')
```

返回示例

~~~json
{
    "code": 0, 
    "message": "https://img.kaiheila.cn/attachments/2022-10/12/1GaII87UTd0zk0k0.png", 
    "info": "商店图片获取成功"
}
~~~

指定了raw参数的返回示例过长，参考 [shop-raw-resp.json](./shop-raw-resp.json)

* `["storefront"]["SkinsPanelLayout"]` 是每日商店的结果，内部包含了4个皮肤uuid、皮肤价格、商店剩余刷新时间
* `["storefront"]["BonusStore"]` 是夜市，包含了皮肤uuid，折扣力度;如果夜市没有开启，则不会有该字段
* `["wallet"]` 是用户的钱包，直接提供了vp和rp字段

获取到uuid后，您可以根据 [valorant-api](https://valorant-api.com/) 提供的接口，获取到皮肤的图片、各语言皮肤名字、和皮肤的等级（蓝紫
橙）

其中需要注意的是，商店返回的结果是level0的uuid，并不是皮肤的主uuid。也就是说，您需要使用 `weapons/skinlevels` 接口来查询皮肤的情况

```
https://valorant-api.com/v1/weapons/skinlevels/
```
skinlevels 接口的返回值如下，提供了皮肤的名字和图片

```json
{
    "status":200,
    "data":{
        "uuid":"155ba654-4afa-1029-9e71-e0b6962d5410","displayName":"Snowfall Wand",
        "levelItem":null,
        "displayIcon":"https://media.valorant-api.com/weaponskinlevels/155ba654-4afa-1029-9e71-e0b6962d5410/displayicon.png",
        "streamedVideo":null,
        "assetPath":"ShooterGame/Content/Equippables/Melee/Snowglobe/Levels/Melee_Snowglobe_Lv1_PrimaryAsset"
    }
}
```
个人更加建议，使用 `weapons/skins` 将所有皮肤的键值缓存到本地，直接在本地遍历查找
```
https://valorant-api.com/v1/weapons/skins
```

因为 `weapons/skinlevels` 接口返回的内容中，不包含皮肤的等级uuid，这需要你在本地遍历后找到 `"contentTierUuid"`, 再调用如下接口获取到皮肤的等级

```
https://valorant-api.com/v1/competitivetiers/{competitivetierUuid}
```

![resp-exp](https://img.kookapp.cn/assets/2023-02/LHAo1meQAP1jo0rj.png)

还有一件事！部分皮肤返回结果中，是不带皮肤的图片的（我真的不理解为什么会这样）这也需要你遍历本地找皮肤图片！

### 3.2 login

该接口用于登录，后台将会根据account将用户的登录信息缓存到内存中

~~~
https://val.musnow.top/api/v2/login
~~~

请求方法：`POST`

速率限制：`10r/m`

| body参数 | 说明                  | 参数类型 |是否必填 |
| ---------- | --------------------- | -------- | -------- |
| token      | API token             | string|是       |
| account    | 拳头账户              | string |是       |
| passwd   | 拳头账户密码             | string |是       |

返回示例（登陆成功）
```json
{"code": 0,"info": "登录成功！", "message": "auth success"}
```
返回示例（需要邮箱验证）

```json
{"code": 0, "info": "2fa用户，请使用/tfa接口提供邮箱验证码", "message": "need provide email verify code"}
```

### 3.3 tfa

此接口用于两步验证，适用于开启了邮箱验证的用户；

您需要先请求 `/login` 接口，在用户获取到验证码后，再请求本接口；若在10min内没有收到 `/tfa` 接口请求，后台会以**邮箱验证超时**关闭该账户的会话。

~~~
https://val.musnow.top/api/v2/tfa
~~~

请求方法：`POST`

| body参数 | 说明                  | 参数类型 |是否必填 |
| ---------- | --------------------- | -------- | -------- |
| token      | API token             | string|是       |
| account    | 拳头账户              |string  |是       |
| vcode   | 邮箱验证码 |  string  | 是       |

这里的account参数是为了在api后台对应上需要2fa的用户，写入验证码。

返回示例

~~~json
{ "code": 0, "message": "2fa auth success", "info": "2fa用户登录成功！"}
~~~

### 3.4 shop-draw

这个接口更加适合在本地管理用户的登录信息，本地调用riot api获取用户`商店皮肤/vp/rp`后，再调用此接口，直接返回图片url

请求方法：`GET`

速率限制：`10r/m`

| params参数 | 说明                  | 参数类型 |是否必填 |
| ---------- | --------------------- | -------- | -------- |
| token      | API token             | string|是       |
| list_shop    | 4个皮肤uuid      | list |是       |
| vp   | vp | int | 否       |
| rp   | rp  | int | 否       |
| img_src    | 自定义背景图的url链接 | string |否       |
| img_ratio    | 自定义返回图比例，值为1代表正方形 | int |否       |

其中 `list_shop` 为riot商店返回值中的以下字段，传入 `["SkinsPanelLayout"]["SingleItemOffers"]` 即可

```json
{
  "SkinsPanelLayout":{
    "SingleItemOffers":[
        "aeb0ea2e-4f50-6b34-27b0-f2a755d27f6a",
        "a88a4c80-4913-a111-0fba-878adddd381a",
        "6e37a33a-416e-fcc0-ceb8-7784e18fbfe9",
        "708abbc6-4579-4452-4293-07ba45e78979"
    ],
    "SingleItemOffersRemainingDurationInSeconds":60193
  }
}
```

vp/rp只有16-9的图片需要，如果设置了`img_ratio`为`'1'`，则无需给予vp/rp参数

返回示例
~~~json
{
    "code": 0, 
    "message": "https://img.kaiheila.cn/attachments/2022-10/12/1GaII87UTd0zk0k0.png", 
    "info": "商店图片获取成功"
}
~~~

### 3.4 shop-cmt

该接口用于更新leancloud数据库中的ShopCmt，该数据需要在 8am 并发进行修改。leancloud本身并不提供线程安全处理，就此将所有需要修改ShopCmt的操作统一到本端进行

请求方法：`POST`

| params参数 | 说明                  | 参数类型 |是否必填 |
| ---------- | --------------------- | -------- | -------- |
| token      | API token             | string |是       |
| best    | 当日最好用户的商店信息     | json |是       |
| worse   | 当日最差用户的商店信息 | json | 是       |
| platform   | 来源平台 | string | 是       |

best/worse应该包含如下字段，其中`user_id`如果么有可以留空（但是不要少这个字段），rating为当前商店4个皮肤的平均分（如一个皮肤么有评分，则不计入平均分计算）

```json
{
    "user_id": "用户id",
    "rating": 97.0,
    "list_shop": []
}
```
返回示例 

```json
{"code": 0, "info": "ShopCmp更新成功", "message": true}
```


## 4.Python示例代码

### 示例代码1：shop

~~~python
import requests

url = "https://val.musnow.top/api/v2/shop"
params = {
    "token":"api-token",
    "account": "拳头账户",
    "passwd": "拳头密码",
    "img-src": "https://img.kookapp.cn/assets/2022-09/KV5krdRx080qo0f0.jpg"
}
res = requests.post(url,json=params) # 请求api
return res.json()
~~~

运行即可获得商店返回结果

~~~~
{'code': 0, 'message': 'https://img.kookapp.cn/attachments/2023-01/15/mLjpR95mZ20rs0rs.png', 'info': '商店图片获取成功'}
~~~~

本地循环请求测试，非2fa用户相应时间约为`10-12s`

```
[start test]
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/6jt8l2pkxL0rs0rs.png'}
12  time:  11.670713091999914
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/6jt8l2pkxL0rs0rs.png'}
11  time:  10.637970628999938
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/6jt8l2pkxL0rs0rs.png'}
10  time:  11.477466089000018
```

### 示例代码2：shop-draw

```python
def ApiRq2(list_shop:list,background='',img_ratio='0'):
    url = "https://val.musnow.top/api/v2/shop-draw"
    params = {
        "token":"api-token",
        "list_shop": list_shop,
        "img_src": background,
        "img_ratio": img_ratio
    }
    res = requests.get(url,params=params) # 请求api
    return res.json()

# 参数
shop = ["49cea67c-4552-13c2-6b4b-8ba07761504e","9d501eec-4084-5d44-32ef-6e8ed5b0ed49","6f2aefab-439d-140a-4dc6-87818e2d72cd","279e0a89-4dd6-b135-cef9-8ebb1df6f2ac"]
img_url = "https://img.kookapp.cn/assets/2023-01/l7Q7WQIaE40xc0xc.jpg"
res = ApiRq2(shop,img_url,'1')
print(res)
```
结果

```
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/03/pSMrv6vCkh0rs0rs.png'}
```

本地循环请求测试，用时约为4-5s，相对来说较友好
```
[start test]
time:  4.115649149000092
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/xgbRjMQeLQ0rs0rs.png'}
time:  4.091482147000079
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/xgbRjMQeLQ0rs0rs.png'}
time:  3.8343799629999467
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/xgbRjMQeLQ0rs0rs.png'}
time:  3.845521912999857
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/xgbRjMQeLQ0rs0rs.png'}
time:  3.9116134020000572
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/xgbRjMQeLQ0rs0rs.png'}
time:  3.822338727999977
{'code': 0, 'info': '商店图片获取成功', 'message': 'https://img.kookapp.cn/attachments/2023-02/06/xgbRjMQeLQ0rs0rs.png'}
```

### 示例代码3：shop-cmp

```python
def ApiRq3(best,worse,platform):
    url = "https://val.musnow.top/api/v2/shop-cmp"
    params = {
        "token":"api-token",
        "best":best,
        "worse":worse,
        "platform":platform
    }
    res = requests.post(url,json=params) # 请求api
    print(res)
    return res.json()

# 调用
ret = ApiRq3({
      "user_id": "这是一个测试用例",
      "rating": 100.0,
      "list_shop": [
        "c9678d8c-4327-f397-b0ec-dca3c3d6fb15",
        "901425cd-405a-d189-3516-ba954965e559",
        "9f6e4612-433b-aea9-1683-3db7aee90848",
        "4845a7ab-4120-ae1c-aec1-9e915a7424b1"
      ]
    },{
      "user_id": "这是一个测试用例",
      "rating": 20.0,
      "list_shop": [
        "155ba654-4afa-1029-9e71-e0b6962d5410",
        "68ee5c6c-4424-e95a-f46f-c08ec2dfeb97",
        "353c1e5f-4258-c49a-c0d6-319ad33bffea",
        "e57317ac-4a93-50a9-30e9-93a098513fa9"
      ]
    },'qqchannel')
print(ret)
```

结果

```
<Response [200]>
{'code': 0, 'info': 'ShopCmp更新成功', 'message': True}
```