## 通用列表 

### 列表引入使用
采用mixin方式引入页面中混合；同一页面可以引入多个。相同数据页面中的优先级高于mixin里的定义的相同值，可以覆盖初始设置。


```js
import commonList from '@/mixin/common-list.js'
import commonForm from '@/mixin/common-form.js'
export default {
    mixins: [commonList, commonForm]
}
```

### 表格配置
common-list 自定义属性

| 参数 | 类型 | 说明 | 默认值 |
| --- | --- | --- | --- |
| option |  Object | 表格配置数据，具体配置看下表option 表格配置; | {page: true,selection: true,showBorder: true,align:'center',menuAlign:'center',column:[]}|
| currentObj | Object | 当前选中条的所有数据，在新增和编辑是提交的是这个对象| {}|
| listData | Object | 接口返回的列表数据| {}|
| listParmas | String | 列表查询参数， 必需包含*page*、*limit* 当前页码和每页条数 | {page: 1,limit: 10,sort: 'createTime',order: 'desc'} |
| searchForm | String | 搜索框查询对象 | {}} |
| selectedIds | String | 多选选中id；使用,分割 | '' |
| addUrl | String | 列表接口 | null |
| addUrl | String | 新增接口 | null |
| editUrl | String | 编辑接口 | null|
| delUrl | String | 删除接口，可传多个id，用逗号分隔；具体看接口要求 | null |

common-list 方法
| 方法名 | 说明 | 
| --- | --- |
| GetList | 获取列表数据 |
| currentChange | 查询第N页数据 |
|  sizeChange | 改变每页显示条数 |
|  rowSave | 新增提交一条数据 |
|  rowUpdate | 修改提交一条数据 |
|  rowDel | 删除传入的id数据 |
|  refresh | 刷新列表，会带上搜索栏内和分页数据重新查询 |
|  searchChange | 根据搜索条件查询列表 |
|  selectionChange | 根据选中的条数，给`selectedIds`赋值选中数据的id，以逗号分隔 |

### option 表格配置
|参数	|说明	|类型	|可选值	|默认值|
|---|---|---|---|---|
|width	|表格宽度	|Number	|—	|100%|
|height	|表格高度	|Number	|—	|auto|
|header	|头部显隐	|Boolean	|true / false	|true|
|size	|控件大小	|String	|medium / small / mini	|medium|
|title	|表格标题	|String	|—	|表格标题|
|saveBtnTitle	|弹出新增按钮标题	|String	|—	|新增|
|updateBtnTitle	|弹出框更新按钮标题	|String	|—	|修改|
|cancelBtnTitle	|弹出框取消按钮标题	|String	|—	|取消|
|dialogFullscreen	|是否为全屏 Dialog	|Boolean	|true / false	|false|
|dialogEscape	|是否可以通过按下 ESC 关闭 Dialog	|Boolean	|true / false	|true|
|dialogClickModal	|是否可以通过点击 modal 关闭 Dialog	|Boolean	|true / false	|true|
|dialogCloseBtn	|是否显示关闭按钮	|Boolean	|true / false	|true|
|dialogModal	|是否需要遮罩层	|Boolean	|true / false	|true|
|dialogWidth	|弹出表单的弹窗宽度	|String / Number	|-	|50%|
|maxHeight	|表格最大高度	|Number	|—	|auto|
|calcHeight	|表格高度差（主要用于减去其他部分让表格高度自适应）	|Number	|—	|auto|
|border	|表格边框	|Boolean	|true / false	|false|
|selection	|行可勾选	|Boolean	|true / false	|false|
|expand	|是否展开折叠行	|Boolean	|true / false	|false|
|empty-text	|空数据时显示的文本内容，也可以通过 slot="empty" 设置	|String	|-	|暂无数据|
|index	|是否显示表格序号（根据分页会自动计算，比如每页 10 行，到了第二页就会从 11 开始记数）	|Boolean	|true / false	|false|
|indexLabel	|序号的标题	|String	|—	|#|
|stripe	|是否显示表格的斑马条纹	|Boolean	|true / false	|false|
|showHeader	|是否显示表格的表头	|Boolean	|true / false	|true|
|showSummary	|是否在表尾显示合计行	|Boolean	|true / false	|false|
|sumColumnList	|表格合计需要配置的字段	|Array	|-	|-|
|defaultSort	|表格的排序字段{prop: 'date', order: 'descending'}prop 默认排序字段，order 排序方式	|Object	|—	|-|
|align	|表格列齐方式	|String	|left / center /right	|left|
|menu	|是否显示操作菜单栏	|Boolean	|true / false	|true|
|menuWidth	|操作菜单栏的宽度	|Number	|-	|240|
|menuAlign	|菜单栏对齐方式	|String	|left / center /right	|left|
|labelWidth	|弹出表单的 label 宽度	|Number	|-	|110|
|formWidth	|表单的宽度	|String / Number	|-	|100%|
|dicData	|传入本次需要的静态字典（在 column 中 dicData 写对象 key 值即可加载）	|Object	|-	|-|
|dicUrl	|字典的网络请求接口（例如配置/xxx/xx/,这样的格式，在 column 中 dicData 写加载的字典，自动替换 key 挂载请求）	|String	|-	|-|
|addBtn	|添加按钮	|Boolean	|true / false	|true|
|delBtn	|删除按钮	|Boolean	|true / false	|true|
|editBtn	|编辑按钮	|Boolean	|true / false	|true|
|viewBtn	|查看按钮	|Boolean	|true / false	|false|
|dateBtn	|日期组件按钮	|Boolean	|true / false	|false|
|dateDefault	|日期控件默认的值	|Boolean	|true / false	|false|
|menuType	|操作栏菜单按钮类型	|String	|button / icon / text / menu	|button|
|menuBtnTitle	|菜单按钮的文字	|String	|-	|功能|
|searchBtn	|搜索显隐按钮（当 column 中有搜索的属性，或则 searchsolt 为 true 时自定义搜索启动起作用）	|Boolean	|true / false	|true|
|columnBtn	|列显隐按钮	|Boolean	|true / false	|true|
|refreshBtn	|刷新按钮	|Boolean	|true / false	|true|
|cellBtn	|表格单元格可编辑（当 column 中有搜索的属性中有 cell 为 true 的属性启用，只对 type 为 select 和 input 有作用)	|Boolean	|true / false	|true|
|selectClearBtn	|清空选中按钮（当 selection 为 true 起作用）	|Boolean	|true / false	|true|

### **（弃用）**defaultTable 采用组件方式生成表单列表 

```html
<default-table :option="option"
               :gridReqParmas="gridReqParmas"
               :listUrl="manageApi.roleGrid"
               :addUrl="manageApi.roleAdd"
               :editUrl="manageApi.roleUpdate"
               :delUrl="manageApi.role"
               :isDeleteSelected="true"
>
</default-table>
```

| 参数 | 类型 | 说明 | 默认值 |
| --- | --- | --- | --- |
| option |  Object | 表格配置数据，具体配置看下表option 表格配置; | {page: true,selection: true,showBorder: true,align:'center',menuAlign:'center',column:[]}|
| gridReqParams | Object | 列表查询参数， 必需包含*page*、*limit* 当前页码和每页条数| {page: 1,limit: 10,sort: 'createTime', order: 'desc'}|
| listUrl | String | 列表接口 | null |
| addUrl | String | 新增接口 | null |
| editUrl | String | 编辑接口 | null|
| delUrl | String | 删除接口 | null |
| isDeleteSelected | Boolean | 是否显示全部删除按钮 | false|


## 通用表单

### 表单混入使用
采用mixin方式引入页面中混合；同一页面可以引入多个。相同数据页面中的优先级高于mixin里的定义的相同值，可以覆盖初始设置。


```js
import commonForm from '@/mixin/common-form.js'
export default {
    mixins: [commonForm]
}
```

### 表单配置
common-list 自定义属性

| 参数 | 类型 | 说明 | 默认值 |
| --- | --- | --- | --- |
| formName |  String | 表单组件上自定义的名称**(ref=formName)** | 无 |
| formId | Number | 列表页选中条数据的id| 0 |
| form | Object | 表单数据，通常通过接口获取 | {}|
| formDataKeys | Array | 如果接口返回数据不是在同一级下，嵌套在不同的子对象中，这里可以填写子对象名称，合并到同一个对象中使用 | [] |
| isShowForm | Boolean | 显示隐藏弹出框 | false |
| formOption | Object | 表单配置参数，内置的column配置显示哪些字段和对应的类型，下表详细 | {} |
| formUrl | String | 表单详细接口 | '' |
| formNewUrl | String | 表单新增页面查询接口 | '' |
| formAddUrl | String | 表单新增页面提交保存接口 | '' |
| formUpdateUrl | String | 表单编辑页面提交编辑接口 | '' |

common-list 方法

| 方法名 | 说明 |
| --- | --- |
| GetDetail | 获取表单数据 |
| postForm | 提交表单数据 |
|  resetForm | 清空表单 |
|  showDialogForm | 打开表单弹出层 |

[查看avue表单文档](https://avue.top/#/component/form-doc)
