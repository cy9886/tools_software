## 研效云3.0

负责模块：

​	租户注册/计费/授权管理页面铺设

​	成员管理  树状结构 el-table

​	应用服务  

​	题库管理

预算执行及滚动预测系统

负责模块：

​	执行汇总查询/执行填报查询/执行填报 - 研发投入

#### el-form ####

阻止表单默认提交事件 @submit.native.prevent


```javascript
 <el-form ref="form" :model="form" :rules="rules" label-width="80px" class="main-dialog-form"
                             :label-position="labelPosition" @submit.native.prevent>
```

#### 如果不想要某字段

delete xxxx

#### vue图片上传

```javascript
<div class="company-img" :class="{'active' : imageUrl}">
                        <!-- <img v-if="imageUrl" :src="imageUrl" alt="" style="width:162px;height:60px;"> -->
                        <el-image :src="imageUrl" :preview-src-list="[imageUrl]" v-if="imageUrl" style="width:162px;height:60px;">
                        </el-image>
                        <!-- <el-image :src="require('@/assets/images/companyLogo.png')"
                                  :preview-src-list="[`@/assets/images/companyLogo.png`]" v-else>
                        </el-image> -->
                        <img src="@/assets/images/companyLogo.png" alt="" v-else>
                    </div>
                    <el-upload class="avatar-uploader" action="https://jsonplaceholder.typicode.com/posts/"
                               :show-file-list="false" :auto-upload="false" :on-change="uploadChange" :limit="limitNum" ref="upload">
                        <div class="click-upload">
                            <img src="@/assets/images/update.png" alt="" style="height:27px;margin-top:15px;margin-left: 19px;">
                            <p>点击上传</p>
                        </div>
                    </el-upload>
 data () {
        return {
            imageUrl: '', // 图片地址
    	},
async uploadChange (file) {
            const isPic = file.raw.type === 'image/jpeg' || file.raw.type === 'image/jpg' || file.raw.type === 'image/png';
            // 文件不可大于300kb
            // const isLt300K = file.size / 1024 / 1024 < 0.3;
            const isLt300K = file.size / 1024 / 2000 <= 1;
            if (!isPic) {
                this.$message.error('上传头像图片仅支持 PNG, JPEG, JPG 格式!');
                this.$refs.upload.clearFiles(); // 如果文件类型不符  则清除该数据
                return;
            }
            if (!isLt300K) {
                this.$message.error('上传头像图片大小不能超过 300kb!');
                this.$refs.upload.clearFiles();
                return;
            }
            if (this.$refs.upload.uploadFiles.length > 1) {
                this.$refs.upload.uploadFiles.pop();
            }
            this.imageUrl = URL.createObjectURL(file.raw);
            var formData = new FormData();
            formData.append('logoFile', file.raw);
            formData.append('tenantId', this.userInfo.frontTenantId);
            await this.$api.EnterpriseSettingsManagement.updateLogoFile(formData);
            this.$message.success('信息修改成功');
        }
```

#### element-ui el-input 无法使用 v-model修饰符

![image-20220414135845251](C:\Users\19735\Desktop\image-20220414135845251.png)



解决：使用blur方法在失去焦点时进行trim（）

```javascript
<el-form
    ref="form"
    :model="formData"
    :rules="formRules"
    style="width:80%;margin-top:20px;"
    size="small"
    label-width="100px"
>
    <el-form-item label="当前节点" prop="moduleId">
        <el-input 
        readonly
        v-model="formData.moduleId" 
        @blur="formData.moduleId=$event.target.value.trim()"
        ></el-input>
    </el-form-item>
    <el-form-item label="帮助URL">
        <el-input 
        placeholder="帮助URL"
        @blur="formData.helpUrl=$event.target.value.trim()"
        v-model="formData.helpUrl"></el-input>
    </el-form-item>
    <el-form-item label="模块描述">
        <el-input 
        type="textarea" 
        placeholder="编辑模块描述..."
        @blur="formData.moduleDesc=$event.target.value.trim()"
        v-model="formData.moduleDesc"></el-input>
    </el-form-item>
    <el-form-item v-if="control.save">
        <el-button type="primary" @click="save">保存</el-button>
    </el-form-item>
</el-form>
```



#### 正则校验金额 0-2位

```javascript
/^(([0-9]+)|([0-9]+\.[0-9]{0,2}))$/
```

```javascript
  <el-form :rules="rules" :model="balanceRecharge" ref="form">
                <el-row>
                    <el-col :span="24">
                        <el-form-item label="充值金额" prop="rechargeAmount">
                            <el-input placeholder="请输入金额" v-model="balanceRecharge.rechargeAmount">
                                <template slot="append">元</template>
                            </el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="24">
                        <el-form-item label="单据号" prop="orderNumber">
                            <el-input placeholder="请输入充值资金相关单据号" v-model="balanceRecharge.orderNumber">
                            </el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="24">
                        <el-form-item label="备注信息">
                            <el-input placeholder="请输入备注信息" v-model="balanceRecharge.remark">
                            </el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
            </el-form>
   export default {
    name: 'BalanceRecharge',
    data () {
        // 金额验证
        let validateAmount = (rule, value, callback) => {
            if (value) {
                // let reg = /^[1-9]*[1-9][0-9]*$/;
                let reg = /^(([1-9]{1}\d*)|(0{1}))(\.\d{0,2})$/;
                if (!reg.test(value)) {
                    callback(new Error('请输入正确的金额!'));
                } else {
                    callback();
                }
            } else {
                callback(new Error('请输入正确的金额!'));
            }
        };
        return {
            dialogVisible: false,
            balanceRecharge: {
                rechargeAmount: 0,
                remark: '',
                tenantId: '',
                orderNumber: ''
            },
            rules: {
                rechargeAmount: [
                    { validator: validateAmount, trigger: ['blur', 'change'], required: true }
                ],
                orderNumber: [
                    { message: '单据号为必填项', trigger: 'blur', required: true }
                ]
            }
        };
    },
    computed: {
        ...mapState(['userInfo'])
    },
    methods: {
        /**
         * @author: wangcy
         * @description: 关闭弹窗
         * @param {} {*}
         */
        handleClose () {
            this.dialogVisible = false;
            Object.assign(this.$data, this.$options.data());
        },
        handleSure () {
            this.$refs.form.validate(async (valid) => {
                if (valid) {
                    await this.$api.TenantRegistrationManagement.balanceRecharge(this.balanceRecharge);
                    this.$emit('get-info');
                    this.handleClose();
                } else {
                    return false;
                }
            });
        }
    }
};
```

#### el-form表单验证 validator不实现

```javascript
 var vm = this;
        let validTeamName = async (rule, value, callback) => {
            if (vm.error.msg && vm.error.value == value) {
                callback(new Error(vm.error.msg));
            } else {
                callback();
            }
        };
   export default {
   		data() {
   			 error: {
                msg: '',
                value: ''
            }
   		}
   }
   methods:{
   		 this.$refs.form.validate(async (valid) => {
   		 	if(valid) {
   		 		 if (this.flag === 'add') {
                        try {
                            await this.$api.TenantRegistrationManagement.addTenant(this.tenant);
                            this.$emit('get-list');
                            this.handleClose();
                        } catch (error) {
                            this.error = {
                                msg: error.msg,
                                value: this.tenant.teamName
                            };
                            this.$refs['form'].validate();
                            this.loading = false;
                        }
                    } else {
                        try {
                            await this.$api.TenantRegistrationManagement.updateTenantById(this.tenant);
                            this.$emit('get-list', this.tenant.id);
                            this.handleClose();
                        } catch (error) {
                            this.error = {
                                msg: error.msg,
                                value: this.tenant.teamName
                            };
                            this.$refs['form'].validate();
                            this.loading = false;
                        }
                    }
   		 	}
   		 },
         handleClose () {
            clearInterval(this.times);
            this.dialogVisible = false;
            Object.assign(this.$data, this.$options.data()); //表单全部清空的问题  可使用form清空自带属性  resetFields()
      		this.$refs.form.resetFields()
        }
   }
```

#### 金额整数拼接 .00

```javascript
 const res1 = await this.$api.AccountManagement.getBillmanagementTenant(this.userInfo.frontTenantId);
            let strArr = String(res1.data.balance).split('.');
            res1.data.balance = strArr.length > 1 ? res1.data.balance : res1.data.balance + '.00';
```

#### 对象中添加字段 递归

```javascript
addLevel (array, i = 1) {
            let tableList = (arr, i) => {
                arr.forEach(item => {
                    item.level = i;
                    if (item.children && item.children.length) {
                        return this.addLevel(item.children, i + 1);
                    }
                });
            };
            return tableList(array, i);
        },
 created() {
 	 this.addLevel(this.treeData); // 数组
 }
```

#### el-tree树组件 点击箭头展开关闭节点  展开的是所有子孙节点

```javascript
 <el-tree :data="data" :props="defaultProps" @node-click="handleNodeClick" class="checked-bottom-tree"
                     v-loading="loading" default-expand-all node-key="id" ref="tree" :expand-on-click-node="false"
                     :default-expanded-keys="expandArray" @node-expand="handleNodeExpand">
</el-tree>
methods: {
	 handleNodeExpand (data,node,component) {
            for (var i = 0; i < this.$refs.tree.store._getAllNodes().length; i++) {
                this.$refs.tree.store._getAllNodes()[i].expanded = true;
            }
        }
    ----------------------------------------------------------------------
     for (var i = 0; i < node.childNodes.length; i++) {
                node.childNodes[i].expanded = true;
            }
}
```

#### 使用Vuemark-down插件 以及内容回显

https://baijiahao.baidu.com/s?id=1712105004896312874&wfr=spider&for=pc

```javascript
编辑页面：
   <mavon-editor class="edit-box" v-model="examination.questionStem" :subfield="false" placeholder="请输入考试题干" ishljs :toolbarsFlag="false" scrollStyle @change="changeText">
   </mavon-editor>
  changeText (value, render) {
            this.examination.questionStem = render;
        }
预览页面：
    <mavon-editor class="edit-box" v-model="questionStems" :subfield="false" ishljs :toolbarsFlag="false" scrollStyle :editable="false">
   	</mavon-editor>
```

#### select传自己本身的参数

```javascript
  <el-select placeholder="测评场景" multiple collapse-tags clearable v-model="examinationCenter.scence"
                               @change="(value) => selectLabel(value,3)">
                        <el-option v-for="item in evaluationScene" :key="item.id" :label="item.labelName" :value="item.id">
                        </el-option>
                    </el-select>
    async getSelectList (type) {
            let data = {
                type: type,
                tenantId: this.userInfo.frontTenantId
            };
            const res1 = await this.$api.EvaluationExaminationManagement.queryEcaluationModel(data);
            console.log(res1);
            if (type === 3) { // 测评岗位
                this.assessmentPost = res1.data;
            } else if (type === 4) { // 测评场景
                this.evaluationScene = res1.data;
            }
        },
        selectLabel (selectArray, type) {
            if (type === 3) { // 测评岗位

            } else if (type === 4) { // 测评场景

            }
        },
```

```javascript

```

#### 获取url中数据

```javascript
 getQueryVariable (variable) {
            var query = window.location.search.substring(1);
            var vars = query.split('&');
            for (var i = 0; i < vars.length; i++) {
                var pair = vars[i].split('=');
                if (pair[0] == variable) { return pair[1]; }
            }
            return (false);
        },
 data() {
     currentId : ''
 }
	created(){
        this.currentId = this.getQueryVariable('id')
    }
```

#### vue中使用原生跳转页面

```javascript
  <el-button class="table-btn" type="link" v-if="row.examCondition === 1 || row.examCondition === 2" @click="enertExam(row)">进入
                            </el-button> 
enertExam (row) {
            let dom = document.createElement('a');
            dom.setAttribute('href', `${this.url}?id=${row.id}`); // 动态添加属性
            dom.click();
        },
```

#### 时间戳转换为天/时/分

```javascript
 data () {
        return {
            examinationOpeningTime: '', // 开始时间
            examinationClosingTime: '', // 结束时间
            timeRemain: 0, // 剩余时间
            day: 0, // 天
            hour: 0, // 时
            minute: 0, // 分
            timer: null // 定时器
        };
  watch: {
        timeRemain (newVal) {
            if (newVal) {
                let day = parseInt(newVal / (60 * 60 * 24 * 1000)); // 当前所剩天数
                let hour = parseInt((newVal - day * 60 * 60 * 24 * 1000) / (60 * 60 * 1000)); // 当前所剩小时数
                let minute = parseInt((newVal - day * 60 * 60 * 24 * 1000 - hour * 60 * 60 * 1000) / (60 * 1000)); // 当前所剩分钟数
                this.day = day;
                this.hour = hour;
                this.minute = minute;
            }
        }
    },
        beforeDestroy() {
            clearInterval(this.timer);
        },
   methods:{
       setCountDown () {
            this.timer = setInterval(() => {
                this.timeRemain -= 60 * 1000;
                if (this.timeRemain <= 0) {
                    clearInterval(this.timer);
                    this.timeRemain = 0;
                    this.personCondition = 2;
                    this.statusUrl = require('../../assets/images/examination/image4.png');
                }
            }, 60 * 1000);
        },
   }
```

#### 阿拉伯数字转大写

```javascript
  convertToChinaNum (num) {
            var arr1 = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九'];
            var arr2 = ['', '十', '百', '千', '万', '十', '百', '千', '亿', '十', '百', '千', '万', '十', '百', '千', '亿'];// 可继续追加更高位转换值
            if (!num || isNaN(num)) {
                return '零';
            }
            var english = num.toString().split('');
            var result = '';
            for (var i = 0; i < english.length; i++) {
                var desI = english.length - 1 - i;// 倒序排列设值

                result = arr2[i] + result;

                var arrIndex = english[desI];

                result = arr1[arrIndex] + result;
            }
            // 将【零千、零百】换成【零】 【十零】换成【十】
            result = result.replace(/零(千|百|十)/g, '零').replace(/十零/g, '十');
            // 合并中间多个零为一个零
            result = result.replace(/零+/g, '零');
            // 将【零亿】换成【亿】【零万】换成【万】
            result = result.replace(/零亿/g, '亿').replace(/零万/g, '万');
            // 将【亿万】换成【亿】
            result = result.replace(/亿万/g, '亿');
            // 移除末尾的零
            result = result.replace(/零+$/, '');
            // 将【零一十】换成【零十】
            // result = result.replace(/零一十/g, '零十');
            // 貌似正规读法是零一十
            // 将【一十】换成【十】
            result = result.replace(/^一十/g, '十');
            return result;
        }
```

#### 监测当前页面切换到其他页面的操作

https://blog.csdn.net/hejingfang123/article/details/119256691

```javascript
//安装插件
npm i vue-visibility-change -S
//导入main.js

import visibility from 'vue-visibility-change';
 
Vue.use(visibility);
//全局使用
visibility.change((evt, hidden) => {
 
  console.log('global callback: ' + hidden);
 
});
//局部使用
<template>
 
    <div v-visibility-change="change">
 
        ...
 
    </div>
 
</template>
methods:{
 
    change(evt, hidden) {
 
        //hidden为true的时候，表示从当前页面切换到别的页面
 
        if(hidden === true){
 
          console.log('离开当前页了！')
 
        }
 
        //hidden为false的时候，表示从别的页面切换回当前页面
 
        if(hidden === false){
 
          console.log('回到当前页了！')
 
        }
 
    },
}
```

#### 正则匹配文字中空格以及回车

```javascript
  obj.questionStem = obj.questionStem.replace(/\n/g, '<br/>');
  obj.questionStem = obj.questionStem.replace(/\s/g, '&nbsp;');
```



#### 拖拽div移动

```javascript
<template>
    <div>
        <div ref="wrapper" class="drag-bar-wrapper">
            <div ref="header" class="drag-bar-header">
                <!-- 头部区域 -->
                 <slot name="header" />

            </div>
             <div class="drag-bar-content">
                <!-- 主内容区域 -->
                <slot name="default" />
            </div>
            <div class="drag-bar-footer"> 
                <!-- 底部区域 -->
                 <slot name="footer" />
            </div> 
        </div>
    </div>
</template>
export default {
    data() {
        return {
            wrapperDom: null,
      headerDom: null,
      disX: 0,
      disY: 0,
      minLeft: 0,
      maxLeft: 0,
      minTop: 0,
      maxTop: 0,
      prevLeft: 0,
      prevTop: 0
        }
    },
    methods: {
    initDrag () {
      this.wrapperDom = this.$refs.wrapper
      this.headerDom = this.$refs.header
      this.headerDom.addEventListener('mousedown', this.onMousedown, false)// 点击头部区域拖拽
    },
    onMousedown (e) {
      this.disX = e.clientX - this.headerDom.offsetLeft
      this.disY = e.clientY - this.headerDom.offsetTop

      this.minLeft = this.wrapperDom.offsetLeft
      this.minTop = this.wrapperDom.offsetTop

      this.maxLeft =
                window.innerWidth - this.minLeft - this.wrapperDom.offsetWidth
      this.maxTop =
                window.innerHeight - this.minTop - this.wrapperDom.offsetHeight

      const { left, top } = getComputedStyle(this.wrapperDom, false)
      this.prevLeft = parseFloat(left)
      this.prevTop = parseFloat(top)

      document.addEventListener('mousemove', this.onMousemove, false)
      document.addEventListener('mouseup', this.onMouseup, false)
      document.body.style.userSelect = 'none' // 消除拖拽中选中文本干扰
    },
    onMousemove (e) {
      let left = e.clientX - this.disX
      let top = e.clientY - this.disY

      if (-left > this.minLeft) {
        left = -this.minLeft
      } else if (left > this.maxLeft) {
        left = this.maxLeft
      }

      if (-top > this.minTop) {
        top = -this.minTop
      } else if (top > this.maxTop) {
        top = this.maxTop
      }

      this.wrapperDom.style.left = this.prevLeft + left + 'px'
      this.wrapperDom.style.top = this.prevTop + top + 'px'
    },
    onMouseup () {
      document.removeEventListener('mousemove', this.onMousemove, false)
      document.removeEventListener('mouseup', this.onMouseup, false)
      document.body.style.userSelect = 'auto' // 恢复文本可选中
    }
  }
}
```

#### el-tree检测文字 显示不全 则使用el-toolTip

```javascript
  <el-tooltip effect="dark" :content="data.labelName" placement="top-end" :open-delay="300"
                                :disabled="isShowTooltip">
                        <span :ref="data.labelName" @mouseover="onMouseOver(data.labelName)">{{`${data.labelName}`}}</span>
                    </el-tooltip>
  onMouseOver (str) {
            const parentWidth = this.$refs[str].parentNode.offsetWidth;
            const contentWidth = this.$refs[str].offsetWidth;
            this.isShowTooltip = contentWidth < parentWidth;
        },
```

#### 子组件中更改父组件的变量  不可直接修改  会导致数据错误

```javascript
 <el-card>
                <p class="question">题干</p>
                <div class="text-content" v-html="questionStem1"></div>
</el-card>
 props: {
        questionStems: Object,
        questionTypes: Number
    },
    data () {
        return {
            dialogDetailVisible: false,
            questionStem1: ''
        };
    },
    watch: {
        dialogDetailVisible (val) {
            if (val) {
                if (Number(this.questionStems.questionTypes === 1)) { // 填空题 将百分号替换成在此作答
                    let questionStem = this.questionStems.questionStem;
                    this.questionStem1 = questionStem.replace(/\%([^(\%|\%)]*)\%/g, `(<span class="answer-item" contenteditable="true" style="display: inline;padding: 0 10px; color:#eb5757;">在此作答</span>)`);
                } else { // 其余按照正常显示即可
                    this.questionStem1 = this.questionStems.questionStem;
                }
            }
        }
    }
声明一个新的变量  让这个变量接收对应的值  修改该变量
```



#### el-tree默认选中第一级节点   新增之后 选中新增后的节点

```javascript
在拿到树形控件的列表之后  去调用子组件中的默认选中的节点方法 在新增之后  获取id  将id赋给新增标签  随后默认选中该节点id即可
父组件
async created () {
        await this.getQuestionSortLabel();
        this.$nextTick(() => {
            // 默认选中第一个节点
            this.$refs.EvaluationOrganizationTree.defaultClick(this.examinationTreeData[0].id);
        });
        // await this.getExamQuestionList();
    },  
async getList (form) {
            this.addItemId = null;
            await this.getQuestionSortLabel();
            this.checkObj = {};
            this.$nextTick(() => {
                this.getItemId(this.examinationTreeData, form.parentId, form.labelName);
                this.$refs.EvaluationOrganizationTree.defaultClick(this.addItemId);
            });
        },
        /**
         * @author: wangcy
         * @description: 获取新增数据id
         */
        getItemId (arr, parentId, labelName) {
            arr.forEach(item => {
                if (!this.addItemId) {
                    if (parentId) {
                        if (item.id == parentId) { // 如果当前id与父id相等 当前数据为子级
                            item.children.forEach(child => {
                                if (child.labelName == labelName) {
                                    this.addItemId = child.id;
                                }
                            });
                        } else {
                            if (item.children && item.children.length) {
                                this.getItemId(item.children, parentId, labelName);
                            }
                        }
                    } else { // 新增的为第一级
                        if (item.labelName == labelName) {
                            this.addItemId = item.id;
                        }
                    }
                }
            });
        },
子组件
  <el-tree :data="title === '考题分类标签' ? examinationTreeData : treeData" default-expand-all node-key="id" ref="tree"
                     @node-click="handleNodeClick" :expand-on-click-node="false" @node-expand="handleNodeExpand"
                     :current-node-key="currentNodeKeys" :default-expanded-keys="defaultExpandedKeys" :filter-node-method="filterNode"
                     :props="title === '考题分类标签' ? defaultProps : modalProps">
                <span class="custom-tree-node" slot-scope="{ node, data }">
                    <el-tooltip effect="dark" :content="data.labelName" placement="top-end" :open-delay="300"
                                :disabled="isShowTooltip">
                        <span :ref="data.labelName" :id="`tree${data.id}`"
                              @mouseover="onMouseOver(data.labelName)">{{`${data.labelName}`}}</span>
                    </el-tooltip>
                    <div class="el-img" v-if="getOperability(data)">
                        <img class="el-edit" src="@/assets/images/rewrite.png" alt="" @click.stop="editText(node)">
                        <img class="el-delete" src="@/assets/images/delete.png" alt="" @click.stop="deleteText(node)">
                    </div>
                </span>
            </el-tree>
methods:{
     defaultClick (id) {
            this.currentNodeKeys = id;
            this.defaultExpandedKeys = [id];
            this.$nextTick(() => {
                let dom = document.getElementById(`tree${id}`);
                dom.click();
            });
        }
}
  
```

## 预算执行及滚动预测

#### 表格使用v-if之后 表格错乱 给每一列添加key属性

```javascript
<el-table-column prop="yearCarryout" class-name="wrap-header-column" show-overflow-tooltip min-width="83px"
                v-if="Number(isShow) === 1" key="yearCarryout">
                <template #header>
                    <div>
                        预计全年 执行
                        <span style="font-size: 12px; color: #999999">(万元)</span>
                    </div>
                </template>
                <template #default="scope">
                    <div class="money">
                        <span>{{ getTthousandNum(scope.row.yearCarryout) }}</span>
                    </div>
                </template>
            </el-table-column>

```



#### el-table刷新页面 表头高度闪动

```javascript
 beforeUpdate () {
        this.$nextTick(() => {
            this.$refs.table.doLayout();
        });
    },
```

#### el-date-picker 只可以选择当前日期之后的

```javascript
    <el-date-picker v-model="reportForm.reportWindow" type="daterange" range-separator="至"
                                start-placeholder="开始日期" end-placeholder="结束日期" @change="handleChange"
                                :picker-options="pickerOptions">
                            </el-date-picker>
 data() {
 	return {
 		  pickerOptions: {
                disabledDate (time) {
                    return time.getTime() < Date.now() - 1000 * 60 * 60 * 24;//如果没有后面的-8.64e7就是不可以选择今天的 
                }
            },
 	}
 }
== 结束时间不可早于开始时间 ==
     <el-date-picker v-model="item.strDate" type="date" placeholder="选择日期"
                                                        :picker-options="StartPickerDisabled">
                                        </el-date-picker>
    StartPickerDisabled: {
                // 开始时间限制
                disabledDate: time => {
                    let beginVal = this.carts.list[0].endDate;
                    if (beginVal) {
                        return time.getTime() > beginVal;
                    }
                }
            },
            EndPickerDisabled: {
                // 结束时间限制
                disabledDate: time => {
                    let beginVal = this.carts.list[0].strDate;
                    if (beginVal) {
                        return time.getTime() < beginVal;
                    }
                }
            }
```

#### el-table-column 搭配 el-input 在聚焦时输入内容复原 失焦时 显示两位小数

```javascript
 <el-table ref="table" :data="tableData" @cell-click="cellclick">
 	     <el-table-column prop="budgetAmount" width="142" align="right" class-name="amount-column" show-overflow-tooltip
                :key="Math.random()">
                <template #header>
                    <span>年度预算</span>
                    <span style="font-size: 12px; color: #999999">(万元)</span>
                </template>
                <template #default="scope">
                    <!-- <el-input-number v-model.number="scope.row.budgetAmount"
                        @blur="defaultValue(scope.row,scope.$index,'budgetAmount')"
                        v-if="scope.row.typeId != '1' && scope.row.type !== '合计' && scope.row.type !== '总计' && isOpen === '1'"
                        :controls="false" :min="0" :precision='2' @change="cellChange">
                        {{scope.row.budgetAmount}}
                    </el-input-number> -->
                    <el-input-number v-model.number="scope.row.budgetAmount"
                        @blur="defaultValue(scope.row,scope.column,scope.$index,'budgetAmount','editbudgetAmount')"
                        v-if="scope.row.typeId != '1' && scope.row.editbudgetAmount && scope.row.type !== '合计' && scope.row.type !== '总计' && isOpen === '1'"
                        :controls="false" :min="0" @change="cellChange">
                    </el-input-number>
                    <span v-else>{{keepTwoDecimals(scope.row.budgetAmount)}}</span>
                </template>
            </el-table-column>
 </el-table>
 cellclick (row, column, cell, event) {
            if (this.isOpen == '0') return;
            // 一层结构
            // 禁止编辑列表格
            this.curValue = row[column.property];
            this.$set(row, `edit${column.property}`, true);
            // 自动聚焦输入框
            setTimeout(() => {
                if (cell.querySelector('input')) {
                    cell.querySelector('input').focus();
                }
            }, 200);
        },
defaultValue (row, column, index, type, flag) {
            console.log(row, flag);
            row[flag] = false
        },
```

#### el-select 点击三角触发失焦事件

```javascript
 <el-table-column show-overflow-tooltip header-align="center" prop="code" label="项目类别">
                <template #default="scope">
                    <el-select v-if="scope.row.editcode" v-model="scope.row.code" @blur="defaultValue(scope.row, scope.column, scope.$index, 'code', 'editcode')">
                        <el-option v-for="item in projectTypeList" :key="item.code" :label="item.value" :value="item.code">
                        </el-option>
                    </el-select>
                    <span v-else>{{scope.row.projectTypeName}}</span>
                </template>
            </el-table-column>
/deep/ .el-select {
    .el-input__suffix-inner {
        pointer-events: none;
    }
}
```

## 研效云主站

##### 鼠标悬浮 样式转换

```javascript
         <div class="card" v-for="item in stationList" :key="item.id">
                    <!-- 正面 -->
                    <div class="front-card">
                        <img src="@/assets/images/devcorp_website/devcorp/card1.png" alt="">
                        <div class="card-content">
                            <div class="icon">
                                <img :src="item.icon" alt="">
                            </div>
                            <img class="line" src="@/assets/images/devcorp_website/devcorp/line.png" alt="">
                            <p>{{item.title}}</p>
                        </div>
                    </div>
                    <!-- 背面 -->
                    <div class="back-card" v-for="child in backList" :key="child.id" v-if="item.id == child.id">
                        <img src="@/assets/images/devcorp_website/devcorp/card3.png" alt="">
                        <div class="card-content">
                            <div class="icon">
                                <img :src="child.icon" alt="">
                            </div>
                            <img class="line" src="@/assets/images/devcorp_website/devcorp/line1.png" alt="">
                            <p>{{child.title}}</p>
                        </div>
                    </div>
                </div>
样式
       .card {
                position: relative;
                .front-card {
                    position: absolute;
                    left: 0;
                    top: 0;
                    backface-visibility: hidden;
                    transition: all 0.5s;
                    overflow: hidden;
                    border-radius: 8px;
                    box-shadow: 0px 3px 12px 1px rgba(34, 87, 207, 0.2);
                    & > img {
                        width: 100%;
                        height: 100%;
                    }
                }
                .back-card {
                    position: relative;
                    left: 0;
                    top: 0;
                    backface-visibility: hidden;
                    transform: rotateY(-180deg);
                    transition: all 0.5s;
                    overflow: hidden;
                    & > img {
                        width: 100%;
                        height: 100%;
                    }
                    .card-content {
                        left: 27% !important;
                    }
                    p {
                        color: #fff !important;
                    }
                }
                .front-card,
                .back-card {
                    width: 14vw;
                    height: 29vh;
                }
                &:hover .front-card {
                    transform: rotateY(-180deg);
                }
                &:hover .back-card {
                    transform: rotateY(-360deg);
                }
                .card-content {
                    position: absolute;
                    top: 20%;
                    left: 32%;
                    display: flex;
                    flex-direction: column;
                    align-items: center;
            }
```

##### hash与history的区别

```
hash # 显示  不会将#后路径带给后台请求 兼容性好
history 无#显示  兼容性略差
项目上线
使用history模式  造成404 后端nginx/connect-history-api-callback
```

## ES6特性

1. Map数据结构   ==键的类型不局限于字符串  可以为对象或其他==

   ```javascript
   let map = new Map();
   map.get() //获取当前键所对用的值
   const test = new Map([
       [0,'哈哈'],
       [1,'哈哈1'],
       [2,'哈哈2']
   ])
   test.get(2)
   '哈哈2'
   ------------------------------------------------
   Map.set()
   const set = new Map()
   const o = {p : 'hello world!'}
   ------------------------------
   set.set(o, 'hello world 的值')
   key: {p: 'hello world!'}
   value: "hello world 的值"
   set.get(o) //'hello world 的值'
   ```

### 下载文件请求头配置

```javascript
export async function fetchDownLoad (url, data = {}) {
    return await axios.post(url, data, {
        responseType: 'blob',
        headers: {
            'Content-Type': 			        'application/json;charset=UTF-8',
            'Download-Request': 'Download-Request' // 多加的请求头
        }
    });
}
```

## 数字化非电量保护装置校验管理系统

#### 监听屏幕大小

```javascript
 mounted () {
        this.screenWidth = document.body.clientWidth;
        window.onresize = () => {
            return (() => {
                this.screenWidth = document.body.clientWidth;
            })();
        };
    },
    watch: {
        screenWidth (val) {
            if (val <= 1745) {
                this.isCollapse = true;
            } else {
                this.isCollapse = false;
            }
        }
    },
```



#### 合并单元格

```javascript
		 <el-table :data="indicationErrorList" :span-method="objectSpanMethod"></el-table>
		/**
         * @author: wangcy
         * @description: 示值误差单元格合并
         */
        objectSpanMethod ({ row, column, rowIndex, columnIndex }) {
            if (columnIndex === 5 || columnIndex == 6) { // 合并第五列和第六列
                return {
                    rowspan: 8, // 起始位置开始合并8行
                    colspan: 1 // 合并1列
                };
            }
        },
```

#### vxe-grid 选择 分页

```javascript
<vxe-grid border :column-config="{resizable: true}" :row-config="{isHover: true}"
                      :checkbox-config="{reserve: true, trigger: 'row', checkRowKeys:selectedListIds}" ref="xTable1" :data="tableData" @checkbox-all="selectAllEvent"
                      @checkbox-change="selectChangeEvent" :loading="loading" :columns="columns" row-id="eqptCode">
            </vxe-grid>
data() {
    return {
        columns: [ //字段配置项
                { type: 'checkbox', width: 47 },
                { type: 'seq', width: 62, title: '序号', align: 'center' },
                { field: 'belongingSubstationName', title: '所属变电站', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'installationPosition', title: '安装位置', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'eqptName', title: '仪器名称', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'model', title: '型号', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'sourceTypeName', title: '来源分类', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'manufactory', title: '生产厂家', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'eqptCode', title: '出厂编号', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'measureMethodName', title: '测量方式', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'measureRange', title: '测量范围', showOverflow: true, minWidth: 130, align: 'center' },
                { field: 'accuracyLevelName', title: '准确度等级', showOverflow: true, minWidth: 130, align: 'center' }
            ],
         selectedList: [], // 当前所选项
         selectedListIds: [] // 当前所选项id
    }
},
methods:{
  /**
         * @author: wangcy
         * @description: 选中所有行
         */
        selectAllEvent ({ checked, records, reserves }) {
            // const records = this.$refs.xTable1.getCheckboxRecords();
            // console.log(checked ? '所有勾选事件' : '所有取消事件', records);
            // this.selectedList = records;
            // 全选中时
            if (checked) {
                // 第一次选数据，还未进行翻页时
                if (reserves.length == 0) {
                    this.selectedListIds = records.map(v => v.eqptCode);
                    this.selectedList = records;
                } else {
                    // eqptCode集合，翻页存在已选中的数据时,拼接新选中的数据
                    this.selectedListIds = [...reserves.map(v => v.eqptCode), ...records.map(v => v.eqptCode)];
                    // 数据集合，翻页存在已选中的数据时,拼接新选中的数据
                    this.selectedList = [...reserves, ...records];
                }
            } else {
                // 取消全选时,直接将翻页数据赋值，当前页数据不用加上
                this.selectedList = reserves;
                this.selectedListIds = reserves.map(v => v.eqptCode);
            }
        },
        /**
         * @author: wangcy
         * @description: 选择某行
         */
        selectChangeEvent ({ checked, records, reserves, row }) {
            // const records = this.$refs.xTable1.getCheckboxRecords(); // 当前所选参数  数组
            // // 需要考虑  分页
            // console.log(checked ? '勾选事件' : '取消事件', records);
            // this.selectedList = records;
            // console.log(checked ? '勾选事件' : '取消事件');
            // console.log('当前选中的数据：', records);
            // console.log('翻页时其他页的数据：', reserves);
            // console.log('当前选中的数据行：', row);
            if (checked) {
                // 第一次选数据，还未进行翻页时
                if (reserves.length == 0) {
                    this.selectedListIds = records.map(v => v.eqptCode);
                    this.selectedList = records;
                } else {
                    // eqptCode集合，翻页存在已选中的数据时,拼接新选中的数据
                    this.selectedListIds = [...reserves.map(v => v.eqptCode), ...records.map(v => v.eqptCode)];
                    // 数据集合，翻页存在已选中的数据时,拼接新选中的数据
                    this.selectedList = [...reserves, ...records];
                }
            } else {
                // 取消选中时
                // 找到对应eqptCode 并删除
                let idIndex = this.selectedListIds.indexOf(row.eqptCode);
                if (idIndex > -1) {
                    // 删除取消选中删除指定元素eqptCode
                    this.$delete(this.selectedListIds, idIndex);
                }

                let dataIndex = null;
                for (let i = 0; i < this.selectedList.length; i++) {
                    if (this.selectedList[i].eqptCode == row.eqptCode) {
                        dataIndex = i;
                        break;
                    }
                }
                // 删除取消选中的元素整个对象
                this.$delete(this.selectedList, dataIndex);
            }
        },  
} 链接转base64前端下载
```

#### 链接转base64前端下载

```javascript
fetch(row.path).then(async res => res.blob()).then((blob) => {
                const a = document.createElement('a');
                a.style.display = 'none';
                a.href = URL.createObjectURL(blob);
                a.download = row.name; // 文件名
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
            });
```

#### el-input 整数小数位数限制 整数只能输入6位 小数只能输入4位

```javascript
<el-input v-model="row.avgReverseStroke" type="number" 
class="mo-input--number" @mousewheel.native.prevent @input="checkNumber($event,row,'avgReverseStroke')" 
oninput="if(isNaN(value)) { value = parseFloat(value) } if(value.indexOf('.')>0){value=value.slice(0,value.indexOf('.')+5)}">
</el-input>
checkNumber (value, row, key) {
            row[key] =
                ('' + value) // 第一步：转成字符串
                    .replace(/[^\d^\.]+/g, '') // 第二步：把不是数字，不是小数点的过滤掉
                    .replace(/^0+(\d)/, '$1') // 第三步：第一位0开头，0后面为数字，则过滤掉，取后面的数字
                    .replace(/^\./, '0.') // 第四步：如果输入的第一位为小数点，则替换成 0. 实现自动补全
                .match(/^\d*(\.?\d{0,4})/g)[0] || ''; // 第五步：最终匹配得到结果 以数字开头，只有一个小数点，而且小数点后面只能有0到4位小数
            let num = (value + '').split('.'); // 截取小数点位数
            if (value.indexOf('.') > 0) { // 如果是小数 则只能输入6位整数 + 4位小数
                row[key] = num[0].slice(0, 6) + '.' + num[1];
            } else {
                row[key] = (row[key] + '').slice(0, 6);
            }
        },
```

#### 前端导出pdf格式 不分页

```javascript
utils/htmlToPdf.js
// 导出页面为PDF格式
import html2Canvas from 'html2canvas';
import JsPDF from 'jspdf';
export default {
    install (Vue, options) {
        Vue.prototype.getPdf = function () {
            const title = this.exportPDFtitle;
            window.pageYoffset = 0;
            document.documentElement.scrollTop = 0;
            document.body.scrollTop = 0;
            html2Canvas(document.getElementById('pdfDom'), {
                backgroundColor: 'white',
                useCORS: true, // 支持图片跨域
                scale: 1, // 设置放大的倍数
                width: document.getElementById('pdfDom').scrollWidth,
                scrollY: 0,
                scrollX: 0,
                // height:100000,
                windowHeight: document.getElementById('pdfDom').scrollHeight
            }).then((canvas) => {
                // 生成pdf导出
                var shareContent = document.querySelector('#pdfDom');
                var width = shareContent.offsetWidth / 4;
                var height = shareContent.offsetHeight / 4;
                // console.log(height);
                var pageData = canvas.toDataURL('image/jpeg', 1.0);
                var img = new Image();
                img.setAttribute('src', pageData);
                img.onload = function () {
                    // 获取dom高度、宽度
                    img.width = img.width / 2;
                    img.height = img.height / 2;
                    img.style.transform = 'scale(0.5)';
                    if (width > height) {
                        // 此可以根据打印的大小进行自动调节
                        // eslint-disable-next-line
                        var pdf = new JsPDF("", "mm", [width * 0.3, height * 0.3]);
                    } else {
                        // eslint-disable-next-line
                        var pdf = new JsPDF("", "mm", [width * 0.3, height * 0.3]);
                    }
                    pdf.addImage(pageData, 'jpeg', 0, 0, width * 0.3, height * 0.3);
                    pdf.save(title + '.pdf');
                };
            });
        };
    }
};

```

```javascript
 <div>
            <el-button type="main" @click="downloadPDF"><i class="el-icon-plus" style="margin-right: 5px"></i> 下载pdf</el-button>
        </div>
        <div id="pdfDom">
            131</div>
 downloadPDF () {
            this.$nextTick(() => { this.getPdf(); });
        },
```

#### uniapp中解决web-view 导航样式适配问题
```
<web-view :src="websiteUrl" :style="webviewStyle"></web-view>
data() {
			return {
				websiteUrl: '',
				// webview 样式，根据不同平台设置不同的值
				webviewStyle: {
					top: uni.getSystemInfoSync().statusBarHeight + 44 + "px",
					left: "0px",
				}
			}
		},
```

#### 解决切换国际化表单校验错误提示不修复 页面无感刷新
```
    利用provide和inject解决
    https://juejin.cn/post/7188103333440127037
```

