# 宜搭跨应用数据 API

调用方式：`this.utils.yida.<函数名>(params)`

适用场景：在宜搭自定义页面中跨表单、跨流程查询和写入业务数据。

不包含内容：

- `this.utils.*` 工具类 API
- `this.utils.router.*` 路由 API
- `this.state` / `this.setState`
- `this.dataSourceMap.*`
- `this.$(fieldId).*` 组件实例 API

上述运行时 API 统一整理在 [yida-js-api.md](./yida-js-api.md) 中，避免重复检索。

所有接口返回 Promise，统一使用 `.then()` 和 `.catch()` 处理结果和异常。

---

## 文档定位

| 文档 | 主要内容 | 适用问题 |
| :--- | :--- | :--- |
| `yida-api.md` | `this.utils.yida.*` 表单/流程数据接口 | 需要新建、更新、删除、搜索表单或流程数据 |
| `yida-js-api.md` | 页面运行时 JS API 与组件 API | 需要控制页面状态、组件、路由、对话框、工具函数 |

---

## 目录

- [表单操作类 API](#表单操作类-api)
  - [saveFormData](#saveformdata) - 新建表单实例
  - [updateFormData](#updateformdata) - 更新表单实例
  - [searchFormDataIds](#searchformdataids) - 搜索表单实例 ID 列表
  - [getFormComponentDefinationList](#getformcomponentdefinationlist) - 获取表单定义
  - [deleteFormData](#deleteformdata) - 删除表单实例
  - [getFormDataById](#getformdatabyid) - 查询表单实例详情
  - [searchFormDatas](#searchformdatas) - 搜索表单实例详情列表
- [流程操作类 API](#流程操作类-api)
  - [startProcessInstance](#startprocessinstance) - 流程发起
  - [updateProcessInstance](#updateprocessinstance) - 流程实例更新
  - [deleteProcessInstance](#deleteprocessinstance) - 删除流程实例
  - [getProcessInstances](#getprocessinstances) - 获取流程实例详情列表
  - [getProcessInstanceIds](#getprocessinstanceids) - 搜索流程实例 ID 列表
  - [getProcessInstanceById](#getprocessinstancebyid) - 获取流程实例详情

---

## 表单操作类 API

### saveFormData

**描述**：新建表单实例。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 是 | 表单 ID | `FORM-XXX` |
| appType | String | 是 | 应用 ID | `APP_XXX` |
| formDataJson | String | 是 | 表单数据 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |

**formDataJson 示例**：

```json
{
  "textField_jcr0069m": "danhang",
  "textareaField_jcr0069n": "duohang",
  "numberField_jcr0069o": 1,
  "radioField_jcr0069p": "选项一",
  "selectField_jcr0069q": "选项一",
  "checkboxField_jcr0069r": [
    "选项二",
    "选项三"
  ],
  "multiSelectField_jcr0069s": [
    "选项二",
    "选项三"
  ],
  "dateField_jcr0069t": 1516636800000,
  "cascadeDate_jcr0069u": [
    "1514736000000",
    "1517328000000"
  ],
  "employeeField_jcr0069x": [
    "xxxxx"
  ],
  "citySelectField_jcr0069y": [
    "110000",
    "110100",
    "110101"
  ],
  "departmentField_jcr0069z": 1123456,
  "cascadeSelectField_jcr006a0": [
    "part",
    "part_b"
  ],
  "attachmentField_jna1lvyb": [
    {
      "downloadUrl": "https://www.aliwork.com/fileHandle?appType=default_tianshu_app&fileName=edd07ca9-1d2e-44b5-98fe-c1e16202f90d.txt&instId=&type=download",
      "name": "test.txt",
      "previewUrl": "https://www.aliwork.com/inst/preview?appType=default_tianshu_app&fileName=test.txt&fileSize=4&downloadUrl=edd07ca9-1d2e-44b5-98fe-c1e16202f90d.txt",
      "url": "https://www.aliwork.com/fileHandle?appType=default_tianshu_app&fileName=edd07ca9-1d2e-44b5-98fe-c1e16202f90d.txt&instId=&type=download",
      "ext": "txt"
    }
  ],
  "tableField_jcr006a1": [
    {
      "cascadeDate_jcr006aa": [
        "1514736000000",
        "1517328000000"
      ],
      "cascadeSelectField_jcr006ae": [
        "product",
        "product_a"
      ],
      "checkboxField_jcr006a7": [
        "选项一",
        "选项二",
        "选项三"
      ],
      "citySelectField_jcr006ac": [
        "120000",
        "120100",
        "120102"
      ],
      "dateField_jcr006a9": 1517328000000,
      "departmentField_jcr006ad": ["1123456"],
      "employeeField_jcr006ab": [
        "yyyyy",
        "xxxxx"
      ],
      "multiSelectField_jcr006a8": [
        "选项一",
        "选项二",
        "选项三"
      ],
      "numberField_jcr006a4": 2,
      "radioField_jcr006a5": "选项二",
      "selectField_jcr006a6": "选项三",
      "textField_jcr006a2": "子表单下单行",
      "textareaField_jcr006a3": "子表单下多行"
    }
  ]
}
```

**返回值**：

| 字段 | 类型 | 描述 |
| :--- | :--- | :--- |
| success | Boolean | 请求是否成功 |
| result | String | 实例 ID |
| errorMsg | String | 错误信息 |
| errorCode | String | 错误码 |

**返回值示例**：

```json
{
  "result": "FINST-XXX",
  "success": true
}
```

**请求示例**：

```javascript
this.utils.yida.saveFormData({
  formUuid: 'FORM-XXX',
  appType: pageConfig.appType,
  formDataJson: JSON.stringify({
    textField_m1g4dcpy: '单行文本',
    textareaField_m1g4dcpz: '多行文本',
  }),
}).then((res) => {
  console.log('新建结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### updateFormData

**描述**：更新表单实例中的指定字段值。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formInstId | String | 是 | 表单实例 ID | `FINST-xxx` |
| updateFormDataJson | String | 是 | 待更新表单数据 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |
| useLatestVersion | String | 否 | 是否使用最新版本 | `y` |

**请求示例**：

```javascript
this.utils.yida.updateFormData({
  formInstId: 'FINST-xxx',
  updateFormDataJson: JSON.stringify({
    textField_m1g4dcpy: '单行文本',
    textareaField_m1g4dcpz: '多行文本',
  }),
  useLatestVersion: 'y',
}).then((res) => {
  console.log('更新成功', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### searchFormDataIds

**描述**：根据条件搜索表单实例 ID 列表。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 是 | 表单 ID | `FORM-XXX` |
| currentPage | Number | 否 | 当前页，默认 1 | `1` |
| pageSize | Number | 否 | 每页记录数，默认 10，最大 100 | `10` |
| searchFieldJson | String | 否 | 查询条件 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |

**请求示例**：

```javascript
this.utils.yida.searchFormDataIds({
  formUuid: 'FORM-XXX',
  currentPage: 1,
  pageSize: 10,
  searchFieldJson: JSON.stringify({
    textField_m1g4dcpy: '单行文本',
  }),
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### getFormComponentDefinationList

**描述**：获取表单定义。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 是 | 表单 ID | `FORM-XXX` |
| version | String | 否 | 版本号 | `""` |

**请求示例**：

```javascript
this.utils.yida.getFormComponentDefinationList({
  formUuid: 'FORM-XXX',
  version: '',
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### deleteFormData

**描述**：删除表单实例。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 是 | 表单 ID | `FORM-XXX` |

**请求示例**：

```javascript
this.utils.yida.deleteFormData({
  formUuid: 'FORM-XXX',
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### getFormDataById

**描述**：根据表单实例 ID 查询表单实例详情。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formInstId | String | 是 | 表单实例 ID | `FINST-xxxx` |

**请求示例**：

```javascript
this.utils.yida.getFormDataById({
  formInstId: 'FINST-xxxx',
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### searchFormDatas

**描述**：根据条件搜索表单实例详情列表。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 是 | 表单 ID | `FORM-XXX` |
| searchFieldJson | String | 否 | 根据表单组件值查询的 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |
| currentPage | Number | 否 | 当前页，默认 1 | `1` |
| pageSize | Number | 否 | 每页记录数，默认 10，最大 100 | `10` |
| originatorId | String | 否 | 数据提交人工号 | `'2134'` |
| createFrom | String | 否 | 创建时间起始，格式 `yyyy-MM-dd` | `'2024-01-01'` |
| createTo | String | 否 | 创建时间结束，格式 `yyyy-MM-dd` | `'2024-02-01'` |
| modifiedFrom | String | 否 | 修改时间起始，格式 `yyyy-MM-dd` | `'2024-01-01'` |
| modifiedTo | String | 否 | 修改时间结束，格式 `yyyy-MM-dd` | `'2024-02-01'` |
| dynamicOrder | String | 否 | 排序字段 | `'{"numberField_1ac":"+"}'` |

**searchFieldJson 示例**：

```json
{
  "textField_jcr0069m": "danhang",
  "textareaField_jcr0069n": "duohang",
  "numberField_jcr0069o": ["1", "10"],
  "radioField_jcr0069p": "选项一",
  "selectField_jcr0069q": "选项一",
  "checkboxField_jcr0069r": ["选项二"],
  "multiSelectField_jcr0069s": ["选项二", "选项三"],
  "dateField_jcr0069t": [1514736000000, 1517414399000],
  "cascadeDate_jcr0069u": [
    [1514736000000, 1517414399000],
    [1514736000000, 1517414399000]
  ],
  "employeeField_jcr0069x": ["xxxxx"],
  "citySelectField_jcr0069y": ["110000", "110100", "110101"],
  "departmentField_jcr0069z": ["1123456"],
  "cascadeSelectField_jcr006a0": ["part", "part_b"],
  "tableField_jcr006a1": "子表单数据"
}
```

**返回值**：

| 字段 | 类型 | 描述 |
| :--- | :--- | :--- |
| currentPage | Number | 当前页 |
| totalCount | Number | 符合条件的实例总数 |
| data | Array | 实例详情列表 |

**请求示例**：

```javascript
this.utils.yida.searchFormDatas({
  formUuid: 'FORM-XXX',
  searchFieldJson: '',
  currentPage: 1,
  pageSize: 10,
  originatorId: '',
  createFrom: '2024-01-01',
  createTo: '2024-02-01',
  modifiedFrom: '2024-01-01',
  modifiedTo: '2024-02-01',
  dynamicOrder: '',
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

**返回值示例（data 字段）**：

```json
{
  "gmtModified": "2018-01-24 11:22:01",
  "formUuid": "FORM-XXX",
  "formInstId": "FINST-xxx",
  "formData": {
    "numberField_jcr0069o": 1,
    "multiSelectField_jcr0069s": ["选项三", "选项二"],
    "textareaField_jcr0069n": "duohang",
    "employeeField_jcr0069x": ["xxxx"],
    "departmentField_jcr0069z": "xxxx",
    "cascadeDate_jcr0069u": ["1514736000000", "1517328000000"],
    "cascadeSelectField_jcr006a0": ["part", "part_b"],
    "tableField_jcr006a1": [
      {
        "departmentField_jcr006ad": "xxxx",
        "cascadeDate_jcr006aa": ["1514736000000", "1517328000000"],
        "selectField_jcr006a6": "选项三",
        "citySelectField_jcr006ac": ["天津", "天津市", "河东区"],
        "radioField_jcr006a5": "选项二",
        "employeeField_jcr006ab": ["xxxxxx", "yyyyyy"],
        "dateField_jcr006a9": 1517328000000,
        "textField_jcr006a2": "子表单下单行",
        "textareaField_jcr006a3": "子表单下多行",
        "cascadeSelectField_jcr006ae": ["product", "product_a"],
        "numberField_jcr006a4": 2,
        "checkboxField_jcr006a7": ["选项一", "选项三", "选项二"],
        "multiSelectField_jcr006a8": ["选项一", "选项三", "选项二"]
      }
    ],
    "selectField_jcr0069q": "选项一",
    "citySelectField_jcr0069y": ["北京", "北京市", "东城区"],
    "checkboxField_jcr0069r": ["选项三", "选项二"],
    "textField_jcr0069m": "danhang",
    "radioField_jcr0069p": "选项一",
    "dateField_jcr0069t": 1516636800000
  },
  "originator": {
    "name": {
      "pureEn_US": "userEnglishName",
      "en_US": "userEnglishName",
      "zh_CN": "userName",
      "type": "i18n"
    },
    "userId": "xxxx"
  }
}
```

---

## 流程操作类 API

### startProcessInstance

**描述**：发起流程实例。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 是 | 表单 ID | `FORM-XXX` |
| processCode | String | 是 | 流程编码 | `TPROC--xxx` |
| deptId | String | 否 | 部门 ID | `''` |
| formDataJson | String | 是 | 表单数据 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |

**请求示例**：

```javascript
this.utils.yida.startProcessInstance({
  formUuid: 'FORM-XXX',
  processCode: 'TPROC--xxx',
  deptId: '',
  formDataJson: JSON.stringify({
    textField_xxx: '单行文本',
    textareaField_xxx: '多行文本',
  }),
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### updateProcessInstance

**描述**：更新流程实例数据。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| processInstanceId | String | 是 | 流程实例 ID | `f30233fb-xxx-9ee530` |
| updateFormDataJson | String | 是 | 待更新表单数据 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |

**请求示例**：

```javascript
this.utils.yida.updateProcessInstance({
  processInstanceId: 'f30233fb-xxx-9ee530',
  updateFormDataJson: JSON.stringify({
    textField_xxx: '单行文本',
    textareaField_xxx: '多行文本',
  }),
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### deleteProcessInstance

**描述**：删除流程实例。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| processInstanceId | String | 是 | 流程实例 ID | `f30233fb-xxx-9ee530` |

**请求示例**：

```javascript
this.utils.yida.deleteProcessInstance({
  processInstanceId: 'f30233fb-xxx-9ee530',
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### getProcessInstances

**描述**：根据搜索条件获取流程实例详情列表。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 否 | 表单 ID | `FORM-XXX` |
| taskId | String | 否 | 任务 ID | `'2199132092'` |
| instanceStatus | String | 否 | 实例状态 | `'RUNNING'` |
| approvedResult | String | 否 | 审批结果 | `'agree'` |
| currentPage | Number | 否 | 当前页，默认 1 | `1` |
| pageSize | Number | 否 | 每页记录数，默认 10，最大 100 | `10` |
| originatorId | String | 否 | 流程发起人工号 | `'2134'` |
| createFrom | String | 否 | 创建时间起始，格式 `yyyy-MM-dd` | `'2024-01-01'` |
| createTo | String | 否 | 创建时间结束，格式 `yyyy-MM-dd` | `'2024-02-01'` |
| modifiedFrom | String | 否 | 修改时间起始，格式 `yyyy-MM-dd` | `'2024-01-01'` |
| modifiedTo | String | 否 | 修改时间结束，格式 `yyyy-MM-dd` | `'2024-02-01'` |
| searchFieldJson | String | 否 | 表单字段查询条件 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |

**请求示例**：

```javascript
this.utils.yida.getProcessInstances({
  formUuid: 'FORM-XXX',
  taskId: '2199132092',
  instanceStatus: 'RUNNING',
  approvedResult: 'agree',
  currentPage: 1,
  pageSize: 10,
  originatorId: '2134',
  createFrom: '2024-01-01',
  createTo: '2024-02-01',
  modifiedFrom: '2024-01-01',
  modifiedTo: '2024-02-01',
  searchFieldJson: JSON.stringify({
    textField_xxx: '单行文本',
  }),
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### getProcessInstanceIds

**描述**：根据条件搜索流程实例 ID 列表。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| formUuid | String | 否 | 表单 ID | `FORM-XXX` |
| taskId | String | 否 | 任务 ID | `'2199132092'` |
| instanceStatus | String | 否 | 实例状态 | `'RUNNING'` |
| approvedResult | String | 否 | 审批结果 | `'agree'` |
| currentPage | Number | 否 | 当前页，默认 1 | `1` |
| pageSize | Number | 否 | 每页记录数，默认 10，最大 100 | `10` |
| originatorId | String | 否 | 流程发起人工号 | `'2134'` |
| createFrom | String | 否 | 创建时间起始，格式 `yyyy-MM-dd` | `'2024-01-01'` |
| createTo | String | 否 | 创建时间结束，格式 `yyyy-MM-dd` | `'2024-02-01'` |
| modifiedFrom | String | 否 | 修改时间起始，格式 `yyyy-MM-dd` | `'2024-01-01'` |
| modifiedTo | String | 否 | 修改时间结束，格式 `yyyy-MM-dd` | `'2024-02-01'` |
| searchFieldJson | String | 否 | 表单字段查询条件 JSON 字符串 | `JSON.stringify({ textField_xxx: '值' })` |

**请求示例**：

```javascript
this.utils.yida.getProcessInstanceIds({
  formUuid: 'FORM-XXX',
  taskId: '2199132092',
  instanceStatus: 'RUNNING',
  approvedResult: 'agree',
  currentPage: 1,
  pageSize: 10,
  originatorId: '2134',
  createFrom: '2024-01-01',
  createTo: '2024-02-01',
  modifiedFrom: '2024-01-01',
  modifiedTo: '2024-02-01',
  searchFieldJson: JSON.stringify({
    textField_xxx: '单行文本',
  }),
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

### getProcessInstanceById

**描述**：根据实例 ID 获取流程实例详情。

**参数**：

| 参数名 | 类型 | 是否必填 | 描述 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| processInstanceId | String | 是 | 流程实例 ID | `f30233fb-xxx-530` |

**请求示例**：

```javascript
this.utils.yida.getProcessInstanceById({
  processInstanceId: 'f30233fb-xxx-530',
}).then((res) => {
  console.log('请求结果', res);
}).catch(({ message }) => {
  this.utils.toast({ title: message, type: 'error' });
});
```

---

## 使用建议

- 当问题包含“表单实例”“流程实例”“跨表单查询”“流程发起”等关键词时，优先查本文件。
- 当问题包含“页面状态”“按钮点击”“组件赋值”“弹窗”“路由跳转”等关键词时，优先查 [yida-js-api.md](./yida-js-api.md)。
- 若同时需要“查询数据 + 更新页面组件”，可先在本文件定位数据接口，再到 `yida-js-api.md` 查组件 API。
