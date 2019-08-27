<template>
  <section class="main-layout classification-layout bg-white main-inner pd20">
      <section class="mg-t10 over-scroll" style="max-height: 700px;">
        <classifyLayout  v-if='classifyArr.length!==0' v-model="vm" @updateClass='updateClass' @addSub='addSub' @deletes='deletes' :node="classifyArr.root"></classifyLayout>
      </section>
     <section class="pd-t10">
       <button type="button" @click="addClass" class="btn btn-primary">添加</button>
     </section>
     <section class="global-layout" v-if="isShowLayout">
        <section class="layout bg-white dom-center border-lines border-round pd15">
          <section class="mg-b10 mg-t10">
             <span class="form-label">行业分类：</span>
             <input type="text" v-model="className" class="form-control inline-block defualt-w mg-r15">
          </section>
          <section class="mg-b15">
             <span class="form-label">模板：</span>
             <input type="text" v-model="classModel" class="form-control inline-block defualt-w mg-r15">
          </section>
          <section class="mg-b10 text-center">
            <button @click="toggleLayout" class="btn btn-danger mg-r15">取消</button>
            <button @click="save" :disabled="isDisabled" class="btn btn-primary" v-if='isAdd'>保存</button>
            <button :disabled="isDisabled" @click="sureUpdate" class="btn btn-primary" v-else>确认修改</button>
          </section>
        </section>
     </section>
  </section>
</template>

<script>
import classifyLayout from './components/classify-node';
import ClassifyData from './utils/classify-util';

export default {
  data () {
    return {
      classifyLists: [],
      classifyArr: [],
      isShowLayout: false,
      className: '',
      classModel: '',
      isAdd: true,
      vm: null,
      isAddSub: false,
      curItem: ''
    };
  },
  created () {
  },
  methods: {
    toggleLayout () {
      this.isShowLayout = !this.isShowLayout;
    },
    save () { // 保存分类
      let self = this;
      let obj = {
        name: self.className,
        model: self.classModel,
        isClose: true,
        children: []
      };
      if (this.isAddSub) { // 子分类
        obj.id = this.curItem.id + '-' + (this.curItem.children.length + 1);
        this.classifyArr.addChildren(this.curItem.id, obj);
      } else { // 大分类
        obj.id = self.classifyLists.length + 1;
        this.classifyLists.push(obj);
        let data = {
          id: '',
          children: self.classifyLists
        };
        this.classifyArr = new ClassifyData(data);
      }
      this.toggleLayout();
    },
    deletes (item) { // 删除分类
      this.classifyArr.delChildren(item.id);
    },
    updateClass (item) { // 显示弹窗，并赋值要修改的数据于input中
      this.toggleLayout();
      this.curItem = item;
      this.dealData(false, item.name, item.model);
    },
    addSub (item) { // 添加子分类
      this.curItem = item;
      this.isAddSub = true;
      this.addClass('sub');
    },
    addClass (type) { // 添加分类
      this.dealData(true, '', '');
      this.toggleLayout();
      let temp = type || '';
      if (temp !== 'sub') this.isAddSub = false;
    },
    dealData (flag, m, n) { // 统一处理弹窗里的input赋值数据
      this.isAdd = flag;
      this.className = m;
      this.classModel = n;
    },
    sureUpdate () { // 确认修改分类
      if (this.classifyLists.length) {
        this.classifyArr.updateContent(this.curItem.id, this.className, this.classModel);
        this.toggleLayout();
      }
    }
  },
  computed: {
    isDisabled () {
      if (this.className.trim() === '' || this.classModel.trim() === '') {
        return true;
      } else {
        return false;
      }
    }
  },
  components: {
    classifyLayout
  }
}
</script>

<style scoped="scoped" lang="scss">
@import "./styles/css/reset.css";
@import "./styles/scss/base.scss";
@import "./styles/css/table.css";
.classification-layout{
    .line-arrow{
        height: 22px;
        width: 22px;
        line-height: 20px;
        font-size: 18px;
    }
    .mg-t5{
        margin-top: 5px;
    }
    .text-btn{
        span:hover{
            text-decoration: underline;
        }
    }
    .layout{
        width: 450px;
    }
    .line-w{
        min-width: 200px;
    }
}
</style>
