
# API 规范

### 接口命名规范
- 请求方法规范：只能定义 GET, POST 请求
  - GET 请求用于读取数据，天然幂等
  - POST 用于请求逻辑。需要考虑幂等
- uri 规范
  - 接口需要自解释。从接口的命名，即可猜测接口的用途。
  - 一级为后端模块名，如：sys,cas,cms
  - 二级为前端模块名，如：cuatomer, admin, manager
  - 二级为 public 的，为免鉴权接口，三级为前端模块名。
  - 特殊用途接口可不按一级二级三级规范，但建议按此规范
  - 后缀规范：
    - /page 分页查询
    - /info 详情查询
    - /save 保存（新增修改一体，依赖于id判断）
    - /new 新增（仅有新增需求时）
    - /update 修改（仅有修改需求时）
    - /remove 删除（需要考虑单个及批量）


### 接口参数规范
- 若接口不支持，忽略对应参数

参数名称 | 参数含意 | 类型 | 默认值
---|---|---|---
token | [header]用户token | String | NaN
pageNo | 页码 | Integer | 1
pageSize | 每页数 | Integer | 10
orderBy | 排序字段 | String | id desc
timeFrom | 时间范围开始 | yyyy-MM-dd HH\:mm\:ss | NaN
timeTo | 时间范围结束 | yyyy-MM-dd HH\:mm\:ss | NaN
dateRangeType | 时间范围类型 | HOUR("时"),DAY("天"),YESTERDAY("周"), MONTH("月"),QUATER("季"),YEAR("年"); | NaN


### 接口的返回值规范
参数名称 | 参数值 | 参数含意
---|---|---
code |-1 | 系统错误，需要开发人员处理 
x | 0 | 业务提示，只需反馈给页面
x | 1 | 功能无异常，有业务状态返回
x | \> 1 | 请看code 对照表
msg | 异常消息 | code不为1时有返回
requestTime | 请求时间 | 请求进入  controller 的时间
responeTime | 响应时间 | 请求在Controller 进行set 结果的时间
costTime | 时间消耗 | 在 Controller 内消耗的时间，单位为毫秒
data  | 详情 | 正常返回时为业务数据【无具体业务数据时返回 true】
==分页== | ==查询== | ==有返回== 
pageNo | Integer | 页码
pageSize | Integer | 每页数
offset | Integer | 偏移量
totalPage | Integer | 总页码数
totalCount | Integer | 叫数据条数
rows | Array | 具体的业务数据



# 返回特殊 code 代码对照表

代码 | 提示语 | 说明
---|---|---
10001 | token 为空！ | 接口需要登录才能访问，header 要附带正确的 token
10002 | token 不正确或已失效！ | 接口需要登录才能访问,并且附带的 token 不正确或已失效
10003 | 非法传输 token！ | 将 token 放在了不合理的位置
10004 | 非法长度的 token！ | token 出现了其他干扰字符
10005 | token 签名效验失败！ | token 内容被修改
x | x | x
20001 | 用户登录环境改变！ | 用户终端改变，不再允许操作，需要重新登录
20002 | api url can not be cors | 接口地址不被允许使用
20003 | origin url can not be cors | 前端域名不被允许使用
20004 | err routers, check the uri! | 错误的路由
x | x | x
30001 | 登录名或密码错误 | 防止用户猜测，用户名和密码错误都使用同一个提示
30002 | 图片验证码错误 | 图片验证码错误
30003 | 需要图片验证码 | 需要图片验证码
30004 | 短信验证码错误 | 短信验证码错误
x | x | x
40001 | 操作需要带数据版本号！ | 需要提交 version 字段
40002 | 数据不存在或已不是最新的！ | 数据id 不正确或已被更新
40003 | 操作需要带数据标识！ | 需要带id 操作
x | x | x
50001 | 订单支付超时已自动取消，请重新下单！ | 订单超时
50002 | 订单已完成支付，请不要重复支付！ | 订单已支付
50003 | 订单状态异常，不能支付！ | 订单其他异常状态

### 权限校验
- 所有需要验证权限的接口，都需要验证 token，需要在 header 中传值。
- 对于只能使用一次的 token, 请从临时 token 接口获取token。临时 token 只能获取一次，只能 ? 传参
- 后端确定前端为哪个站点，是通过 headers 里面的 Origin 获取的，可选在 ? 上附加此参数。如果需要区分 Origin，在 postman 上可以进行模拟