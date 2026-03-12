# 宜搭 JS-API

来源：[宜搭 JS-API 官方文档](https://docs.aliwork.com/docs/developer/api/yidaAPI)

调用场景：宜搭页面的 JS 面板、变量绑定弹框、自定义页面生命周期和事件函数。

适用范围：

- 页面状态管理：`this.state`、`this.setState`
- 页面运行时：`this.dataSourceMap.*`、`this.reloadDataSource()`
- 工具类：`this.utils.*`
- 路由：`this.utils.router.*`
- 组件实例：`this.$(fieldId).*`

不包含内容：

- `this.utils.yida.*` 跨应用表单/流程数据接口

上述跨应用数据接口统一整理在 [yida-api.md](./yida-api.md) 中。

---

## 文档定位

| 文档 | 主要内容 | 适用问题 |
| :--- | :--- | :--- |
| `yida-js-api.md` | 页面运行时、工具类、路由、组件实例 API | 需要操作页面状态、组件、对话框、路由、数据源 |
| `yida-api.md` | `this.utils.yida.*` 表单/流程数据接口 | 需要读写表单实例或流程实例 |

---

## 目录

- [开始之前](#开始之前)
- [全局变量 API](#全局变量-api)
  - [this.state.xxx](#thisstatexxx)
  - [this.setState()](#thissetstate)
- [远程数据 API](#远程数据-api)
  - [this.dataSourceMap.xxx.load()](#thisdatasourcemapxxxload)
  - [this.reloadDataSource()](#thisreloaddatasource)
- [JS 调用 API](#js-调用-api)
  - [this.methodName()](#thismethodname)
- [工具类相关 API](#工具类相关-api)
  - [this.utils.dialog()](#thisutilsdialog)
  - [this.utils.formatter()](#thisutilsformatter)
  - [this.utils.getDateTimeRange(when, type)](#thisutilsgetdatetimerangewhen-type)
  - [this.utils.getLocale()](#thisutilsgetlocale)
  - [this.utils.getLoginUserId()](#thisutilsgetloginuserid)
  - [this.utils.getLoginUserName()](#thisutilsgetloginusername)
  - [this.utils.isMobile()](#thisutilsismobile)
  - [this.utils.isSubmissionPage()](#thisutilsissubmissionpage)
  - [this.utils.isViewPage()](#thisutilsisviewpage)
  - [this.utils.loadScript()](#thisutilsloadscript)
  - [this.utils.openPage()](#thisutilsopenpage)
  - [this.utils.previewImage()](#thisutilspreviewimage)
  - [this.utils.toast()](#thisutilstoast)
- [路由相关 API](#路由相关-api)
  - [this.utils.router.push()](#thisutilsrouterpush)
  - [this.utils.router.replace()](#thisutilsrouterreplace)
  - [this.utils.router.getQuery()](#thisutilsroutergetquery)
  - [this.utils.router.stringifyQuery()](#thisutilsrouterstringifyquery)
- [组件通用 API](#组件通用-api)
  - [this.$(fieldId).get(prop)](#thisfieldidgetprop)
  - [this.$(fieldId).set(prop, value)](#thisfieldidsetprop-value)
- [表单组件 API](#表单组件-api)
  - [this.$(fieldId)](#thisfieldid)
  - [this.$(fieldId).getValue()](#thisfieldidgetvalue)
  - [this.$(fieldId).setValue()](#thisfieldidsetvalue)
  - [this.$(fieldId).reset()](#thisfieldidreset)
  - [this.$(fieldId).getBehavior()](#thisfieldidgetbehavior)
  - [this.$(fieldId).setBehavior()](#thisfieldidsetbehavior)
  - [this.$(fieldId).resetBehavior()](#thisfieldidresetbehavior)
  - [this.$(fieldId).validate()](#thisfieldidvalidate)
  - [this.$(fieldId).disableValid()](#thisfieldiddisablevalid)
  - [this.$(fieldId).enableValid()](#thisfieldidenablevalid)
  - [this.$(fieldId).setValidation()](#thisfieldidsetvalidation)
  - [this.$(fieldId).resetValidation()](#thisfieldidresetvalidation)
- [Dialog 组件 API](#dialog-组件-api)
  - [this.$(fieldId).show()](#thisfieldidshow)
  - [this.$(fieldId).hide()](#thisfieldidhide)

---

## 开始之前

官方文档强调：这些 API 默认运行在宜搭页面的执行上下文中，最外层函数里的 `this` 指向正确，但嵌套普通函数时要小心 `this` 丢失。

**推荐写法**：

```javascript
export function setSomeValue() {
  const status = this.state.status;
  const newStatus = status + 1;
  this.setState({ status: newStatus });
  this.$('numberField_xxx').setValue(newStatus);
}
```

**嵌套函数里避免 this 丢失**：

```javascript
export function setSomeValue(value) {
  const that = this;

  this.dataSourceMap.xxx.load(function (ret) {
    that.$('numberField_xxx').setValue(ret);
  });

  this.dataSourceMap.xxx.load((ret) => {
    this.$('numberField_xxx').setValue(ret);
  });
}
```

---

## 全局变量 API

### this.state.xxx

**描述**：获取页面级全局变量的值，风格与 React 一致。

**说明**：

| 项 | 说明 |
| :--- | :--- |
| `xxx` | 一般为页面数据源变量名称 |

**请求示例**：

```javascript
export function getState() {
  const status = this.state.status;
  console.log(`status: ${status}`);
}
```

---

### this.setState()

**描述**：设置页面级全局变量并触发页面重新渲染。

**注意**：

- 不要使用 `this.state.a = b` 直接修改状态。
- 官方明确提示这种写法后续升级不保证兼容。

**请求示例**：

```javascript
export function setStateValue() {
  this.setState({
    status: 'loading',
    text: '加载中...'
  });
}
```

---

## 远程数据 API

### this.dataSourceMap.xxx.load()

**描述**：手动调用指定远程数据源，`xxx` 是数据源面板中配置的数据源名称。

**说明**：

| 项 | 说明 |
| :--- | :--- |
| 返回值 | `Promise` |
| 参数 | 会与数据源配置中的请求参数进行 merge 后发出请求 |

**请求示例**：

```javascript
export function fetchData() {
  this.dataSourceMap.getDataList.load({
    pageSize: 10,
    page: this.state.currentPage
  }).then((res) => {
    if (res) {
      console.log('fetchData', res);
    }
  }).catch(() => {
    this.utils.toast({
      type: 'error',
      title: '请求失败！'
    });
  });
}
```

---

### this.reloadDataSource()

**描述**：重新请求所有 `auto load = true` 的远程 API。

**返回值**：`Promise`

**请求示例**：

```javascript
export function reload() {
  this.reloadDataSource().then(() => {
    this.utils.toast({
      type: 'success',
      title: '刷新成功！'
    });
  });
}
```

---

## JS 调用 API

### this.methodName()

**描述**：在动作面板中调用其他 JS 函数。

**说明**：

| 项 | 说明 |
| :--- | :--- |
| `methodName` | 其他导出函数的名称 |

**请求示例**：

```javascript
export function hello(params) {
  this.utils.toast({
    title: `hello ${params}`,
    type: 'success'
  });
}

export function onClickInvoke() {
  const value = this.$('textField_k1u12o6l').getValue();
  this.hello(value);
}
```

---

## 工具类相关 API

### this.utils.dialog()

**描述**：弹出对话框，需手动关闭。

**参数**：

| 参数名 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| type | String | `'alert'` | 对话框类型：`alert` / `confirm` / `show` |
| title | String | - | 标题 |
| content | String \| ReactNode | - | 内容，可传 HTML 或 JSX |
| hasMask | Boolean | `true` | 是否显示遮罩 |
| footer | Boolean | `true` | 是否显示底部按钮 |
| footerAlign | String | `'right'` | 按钮对齐：`left` / `center` / `right` |
| footerActions | Array | - | 按钮顺序，如 `['cancel', 'ok']` |
| onOk | Function | - | 点击确定回调 |
| onCancel | Function | - | 点击取消回调 |

**返回值**：对话框实例对象，可调用 `hide()`。

**请求示例**：

```javascript
export function popDialog() {
  this.utils.dialog({
    type: 'confirm',
    title: 'title',
    content: 'content',
    onOk: () => {},
    onCancel: () => {},
  });
}

export function closeDialog() {
  const dialog = this.utils.dialog({});
  dialog.hide();
}
```

---

### this.utils.formatter()

**描述**：常用格式化函数，用于日期、金额、手机号、银行卡号等格式转换。

**常见格式**：

| 类型 | 示例 | 输出示意 |
| :--- | :--- | :--- |
| `date` | `this.utils.formatter('date', new Date(), 'YYYY-MM-DD')` | `2022-01-29` |
| `date` | `this.utils.formatter('date', new Date(), 'YYYY/MM/DD')` | `2022/01/29` |
| `date` | `this.utils.formatter('date', new Date(), 'YYYY-MM-DD HH:mm:ss')` | `2022-01-29 13:01:02` |
| `money` | `this.utils.formatter('money', '10000.99', ', ')` | `10, 000.99` |
| `cnmobile` | `this.utils.formatter('cnmobile', '+8615652988282')` | `+86 1565 2988 282` |
| `card` | `this.utils.formatter('card', '1565298828212233')` | `1565 2988 2821 2233` |

**请求示例**：

```javascript
export function format() {
  const formatDate = this.utils.formatter('date', new Date(), 'YYYY-MM-DD');
  const formatDate2 = this.utils.formatter('date', new Date(), 'YYYY-MM-DD HH:mm:ss');
  const formatMoney = this.utils.formatter('money', '10000.99', ', ');
  const formatPhoneNumber = this.utils.formatter('cnmobile', '+8615652988282');
  const formatCardNumber = this.utils.formatter('card', '1565298828212233');

  console.log(formatDate, formatDate2, formatMoney, formatPhoneNumber, formatCardNumber);
}
```

---

### this.utils.getDateTimeRange(when, type)

**描述**：获取当前或指定日期的开始、结束时间戳区间。

**参数**：

| 参数名 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| when | Number \| Date | `new Date()` | 指定日期 |
| type | String | `'day'` | 区间类型：`year` / `month` / `week` / `day` / `date` / `hour` / `minute` / `second` |

**返回值**：`[start, end]`

**请求示例**：

```javascript
export function search() {
  const [dayStart, dayEnd] = this.utils.getDateTimeRange();
  const [monthStart, monthEnd] = this.utils.getDateTimeRange(new Date(), 'month');

  console.log(`dayStart: ${dayStart}, dayEnd: ${dayEnd}`);
  console.log(`monthStart: ${monthStart}, monthEnd: ${monthEnd}`);
}
```

---

### this.utils.getLocale()

**描述**：获取当前页面语言环境。

**返回值**：`String`，如 `zh_CN`

**请求示例**：

```javascript
export function locale() {
  const locale = this.utils.getLocale();
  console.log(`locale: ${locale}`);
}
```

---

### this.utils.getLoginUserId()

**描述**：获取登录用户 ID。

**返回值**：`String`

**请求示例**：

```javascript
export function getUserInfo() {
  const userId = this.utils.getLoginUserId();
  console.log(`userId: ${userId}`);
}
```

---

### this.utils.getLoginUserName()

**描述**：获取登录用户名称。

**返回值**：`String`

**请求示例**：

```javascript
export function getUserInfo() {
  const userName = this.utils.getLoginUserName();
  console.log(`userName: ${userName}`);
}
```

---

### this.utils.isMobile()

**描述**：判断当前访问环境是否是移动端。

**返回值**：`Boolean`

**请求示例**：

```javascript
export function someFunctionName() {
  console.log('isMobile', this.utils.isMobile());
}
```

---

### this.utils.isSubmissionPage()

**描述**：判断当前页面是否是数据提交页面。

**返回值**：`Boolean`

**请求示例**：

```javascript
export function someFunctionName() {
  console.log('isSubmissionPage', this.utils.isSubmissionPage());
}
```

---

### this.utils.isViewPage()

**描述**：判断当前页面是否是数据查看页面。

**返回值**：`Boolean`

**请求示例**：

```javascript
export function someFunctionName() {
  console.log('isViewPage', this.utils.isViewPage());
}
```

---

### this.utils.loadScript()

**描述**：动态加载远程脚本。

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| url | String | 是 | 远程脚本地址 |

**返回值**：`Promise`

**请求示例**：

```javascript
export function didMount() {
  this.utils.loadScript('https://g.alicdn.com/code/lib/qrcodejs/1.0.0/qrcode.min.js').then(() => {
    var qrcode = new QRCode(document.getElementById('qrcode'), {
      text: 'http://jindo.dev.naver.com/collie',
      width: 128,
      height: 128,
      colorDark: '#000000',
      colorLight: '#ffffff',
      correctLevel: QRCode.CorrectLevel.H
    });
  });
}
```

---

### this.utils.openPage()

**描述**：打开新页面。

**说明**：

- 在钉钉环境下，会优先使用钉钉 API 打开，体验更友好。

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| url | String | 是 | 页面地址，可为相对路径 |

**请求示例**：

```javascript
export function someFunctionName() {
  this.utils.openPage('/workbench');
}
```

---

### this.utils.previewImage()

**描述**：图片预览。

**说明**：

- 官方示例展示了通过 `current` 指定当前预览图片。

**请求示例**：

```javascript
export function previewImg() {
  this.utils.previewImage({
    current: 'https://img.alicdn.com/tfs/TB1JUnZ2GL7gK0jSZFBXXXZZpXa-260-192.png_.webp'
  });
}
```

---

### this.utils.toast()

**描述**：轻量信息提醒，显示一段时间后自动消失。

**参数**：

| 参数名 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| type | String | `'notice'` | `success` / `warning` / `error` / `notice` / `help` / `loading` |
| title | String | - | 提示内容 |
| size | String | `'medium'` | `medium` / `large` |
| duration | Number | - | 显示时长，`loading` 类型无效 |

**返回值**：

- 普通提示：无须手动处理
- `loading` 类型：返回关闭函数

**请求示例**：

```javascript
export function popToast() {
  this.utils.toast({
    title: 'success',
    type: 'success',
    size: 'large',
  });
}

export function showLoadingToast() {
  const close = this.utils.toast({
    title: '加载中',
    type: 'loading',
    size: 'large',
  });

  setTimeout(close, 3000);
}
```

---

## 路由相关 API

### this.utils.router.push()

**描述**：页面跳转，并将记录压入路由栈，可用浏览器回退。

**签名**：

```javascript
function push(path, params, blank, isUrl, type) {}
```

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| path | String | 是 | 跳转地址，可以是完整 URL、URL 片段或 pageId |
| params | Object | 否 | 查询参数对象 |
| blank | Boolean | 否 | 是否新开页面，默认 `false` |
| isUrl | Boolean | 否 | 是否按 URL 解析，默认 `false` |
| type | String | 否 | `push` 或 `replace` |

**请求示例**：

```javascript
export function pushUrl() {
  this.utils.router.push('https://www.aliwork.com', {
    fromSource: 'customPage'
  });
}
```

---

### this.utils.router.replace()

**描述**：页面替换跳转，不会进入新的历史记录。

**等价写法**：

```javascript
this.utils.router.push(path, params, false, false, 'replace');
```

**请求示例**：

```javascript
export function replaceUrl() {
  this.utils.router.replace('https://www.aliwork.com', {
    fromSource: 'customPage'
  });
}
```

---

### this.utils.router.getQuery()

**描述**：获取页面 URL 参数。

**签名**：

```javascript
function getQuery(key, queryStr) {}
```

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| key | String | 否 | 传入则返回对应值，否则返回完整参数对象 |
| queryStr | String | 否 | 默认取 `location.search + location.hash`，支持传入自定义查询串 |

**返回值**：`Record<string, string> | string | undefined`

**请求示例**：

```javascript
export function getQuery() {
  const fromSource = this.utils.router.getQuery('fromSource');
  console.log(`fromSource: ${fromSource}`);
}
```

---

### this.utils.router.stringifyQuery()

**描述**：将对象序列化为 URL 查询参数字符串。

**请求示例**：

```javascript
export function stringifyQuery() {
  const params = {
    name: 'yida',
    gender: 'm'
  };
  const urlStr = this.utils.router.stringifyQuery(params);
  console.log(`urlParams: ${urlStr}`);
}
```

---

## 组件通用 API

在使用组件 API 前，需要先拿到组件 `fieldId`。官方特别提醒：不要依赖文档中未公开的私有属性或私有方法。

### this.$(fieldId).get(prop)

**描述**：获取组件属性值。

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| fieldId | String | 是 | 组件唯一标识 |
| prop | String | 是 | 组件属性名 |

**注意**：

- 不要使用 `this.$(fieldId).xxx` 直接读取私有属性。

**请求示例**：

```javascript
export function getAttribute() {
  const content = this.$('text_kyz78exo').get('content');
  console.log(`text content: ${content}`);
}
```

---

### this.$(fieldId).set(prop, value)

**描述**：设置组件属性值。

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| prop | String | 是 | 组件属性名 |
| value | Any | 是 | 要设置的值 |

**注意**：

- 不要使用 `this.$(fieldId).xxx = xxx` 直接赋值。

**请求示例**：

```javascript
export function setAttribute() {
  this.$('text_kyz78exo').set('maxLine', 5);
}
```

---

## 表单组件 API

### this.$(fieldId)

**描述**：获取组件实例。

**说明**：

| 项 | 说明 |
| :--- | :--- |
| `fieldId` | 组件唯一标识 |

---

### this.$(fieldId).getValue()

**描述**：获取表单组件当前值。

**请求示例**：

```javascript
export function getValue() {
  const value = this.$('textField_kyz78exp').getValue();
  console.log(`input value: ${value}`);
}
```

---

### this.$(fieldId).setValue()

**描述**：设置表单组件值。

**参数说明**：

```javascript
interface IOptions {
  doNotValidate: boolean;
  formatted: boolean;
  triggerChange: boolean;
}
```

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| value | Any | 是 | 需要设置的值 |
| options | IOptions | 否 | 可选配置项 |

**请求示例**：

```javascript
export function setValue() {
  this.$('textField_kyz78exp').setValue('hello world');
}
```

---

### this.$(fieldId).reset()

**描述**：重置表单组件值。

**参数**：

| 参数名 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| toDefault | Boolean | `true` | 是否重置到默认值 |

**请求示例**：

```javascript
export function reset() {
  this.$('textField_kyz78exp').reset();
}
```

---

### this.$(fieldId).getBehavior()

**描述**：获取表单组件当前状态。

**返回值可选项**：

| 值 | 含义 |
| :--- | :--- |
| `NORMAL` | 正常态 |
| `READONLY` | 只读态 |
| `DISABLED` | 禁用态 |
| `HIDDEN` | 隐藏态 |

**请求示例**：

```javascript
export function getBehavior() {
  const behavior = this.$('textField_kyz78exp').getBehavior();
  console.log(`text behavior: ${behavior}`);
}
```

---

### this.$(fieldId).setBehavior()

**描述**：设置表单组件状态。

**请求示例**：

```javascript
export function setBehavior() {
  this.$('textField_kyz78exp').setBehavior('DISABLED');
}
```

---

### this.$(fieldId).resetBehavior()

**描述**：重置表单组件状态。

**请求示例**：

```javascript
export function resetBehavior() {
  this.$('textField_kyz78exp').resetBehavior();
}
```

---

### this.$(fieldId).validate()

**描述**：执行一次组件校验。

**签名**：

```javascript
function validate(callback) {}
```

**回调签名**：

```javascript
function ValidateCallback(errors, values) {}
```

**参数说明**：

| 参数名 | 类型 | 说明 |
| :--- | :--- | :--- |
| errors | `string[] \| null` | 错误信息 |
| values | `object \| null` | 组件值 |

**请求示例**：

```javascript
export function validate() {
  this.$('textField_kyz78exp').validate((errors, values) => {
    console.log(JSON.stringify({ errors, values }, null, 2));
  });
}
```

**校验失败输出示例**：

```json
{
  "errors": {
    "textField_kyz78exp": {
      "errors": [
        "输入框不是一个合法的手机号码格式"
      ]
    }
  },
  "values": {
    "textField_kyz78exp": "33"
  }
}
```

---

### this.$(fieldId).disableValid()

**描述**：关闭表单组件校验。

**请求示例**：

```javascript
export function disableValid() {
  this.$('textField_kyz78exp').disableValid();
}
```

---

### this.$(fieldId).enableValid()

**描述**：开启表单组件校验。

**参数**：

| 参数名 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| doValidate | Boolean | `false` | 是否立即执行校验 |

**请求示例**：

```javascript
export function enableValid() {
  this.$('textField_kyz78exp').enableValid(true);
}
```

---

### this.$(fieldId).setValidation()

**描述**：设置表单组件校验规则。

**规则结构**：

```javascript
interface IRule {
  type: string;
  param: any;
  message: string;
}
```

**参数**：

| 参数名 | 类型 | 是否必填 | 说明 |
| :--- | :--- | :--- | :--- |
| rules | `IRule[]` | 是 | 校验规则数组 |
| doValidate | Boolean | 否 | 是否立即执行校验，默认 `false` |

**支持的校验类型**：

| 规则 | 配置示例 |
| :--- | :--- |
| 必填 | `{"type": "required"}` |
| 最小长度 | `{"type": "minLength", "param": "23"}` |
| 最大长度 | `{"type": "maxLength", "param": "23"}` |
| 邮箱 | `{"type": "email"}` |
| 手机 | `{"type": "mobile"}` |
| 网址 | `{"type": "url"}` |
| 最小值 | `{"type": "minValue", "param": "3"}` |
| 最大值 | `{"type": "maxValue", "param": "3"}` |
| 自定义函数 | `{"type": "customValidate", "param": (value, rule) => true}` |

**请求示例**：

```javascript
export function setValidation() {
  this.$('textField_kyz78exp').setValidation([{
    type: 'required'
  }, {
    type: 'maxLength',
    param: '10'
  }, {
    type: 'customValidate',
    param: (value, rule) => {
      if (/^\d*$/.test(value)) {
        return true;
      }
      return rule.message;
    },
    message: '只能输入数字'
  }]);
}
```

---

### this.$(fieldId).resetValidation()

**描述**：恢复组件原有校验规则。

**参数**：

| 参数名 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| doValidate | Boolean | `false` | 是否立即执行校验 |

**请求示例**：

```javascript
export function resetValidation() {
  this.$('textField_kyz78exp').resetValidation(true);
}
```

---

## Dialog 组件 API

### this.$(fieldId).show()

**描述**：显示指定对话框，支持显示后回调。

**请求示例**：

```javascript
export function openDialog() {
  this.$('dialog_kyz78exr').show(() => {
    console.log('Dialog is open');
  });
}
```

---

### this.$(fieldId).hide()

**描述**：关闭指定对话框。

**请求示例**：

```javascript
export function closeDialog() {
  this.$('dialog_kyz78exr').hide();
}
```

---

## 说明

- 本文档整理自官方页面结构，便于在技能中快速检索和复制示例。
- `yida-js-api.md` 侧重宜搭页面运行时 JS API。
- `yida-api.md` 仍然保留跨应用数据访问与 `this.utils.yida.*` 相关参考。
- 当问题属于“页面内怎么控制组件/状态/路由/弹窗”时优先查本文件。
- 当问题属于“怎么读写宜搭业务数据或流程实例”时优先查 [yida-api.md](./yida-api.md)。
