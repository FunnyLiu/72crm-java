<template>
  <div class="journal oa-bgcolor">
    <div class="btn-select">
      <div class="select-group">
        <label>审批类型</label>
        <el-select v-model="categoryType"
                   placeholder="请选择"
                   size="mini">
          <el-option v-for="item in categoryOptions"
                     :key="item.categoryId"
                     :label="item.title"
                     :value="item.categoryId">
          </el-option>
        </el-select>
      </div>
      <el-button type="primary"
                 class="new-btn"
                 @click="newBtn">新建审批</el-button>
    </div>
    <el-tabs v-model="activeName"
             @tab-click="tabClick">
      <el-tab-pane :label="item.label"
                   :name="item.key"
                   v-for="(item, index) in tabsData"
                   :key="index">
        <v-content :by="item.key"
                   :categoryId="categoryType"
                   :ref="'tabcontent' + item.key"
                   id="examine-list-box"
                   @reset="reset"
                   @edit="editDetail">
        </v-content>
      </el-tab-pane>
    </el-tabs>
    <examine-category-select :show="showCategorySelect"
                             @select="selcetExamineCategory"
                             @close="showCategorySelect=false"></examine-category-select>
    <examine-create-view v-if="isCreate"
                         :categoryId="createInfo.categoryId"
                         :categoryTitle="createInfo.title"
                         :action="createAction"
                         @save-success="createSaveSuccess"
                         @hiden-view="hideView"></examine-create-view>
  </div>
</template>

<script>
import { oaExamineCategoryList } from '@/api/oamanagement/examine' // 审批类型数据
import VContent from './components/content'
import ExamineCategorySelect from './components/examineCategorySelect'
import ExamineCreateView from './components/examineCreateView'

export default {
  components: {
    VContent,
    ExamineCategorySelect,
    ExamineCreateView
  },
  data() {
    return {
      loading: false,
      activeName: 'my',
      tabsData: [
        { label: '我发起的', key: 'my' },
        { label: '我审批的', key: 'examine' }
      ],
      // 审批类型
      categoryType: '',
      categoryOptions: [],
      // 新建
      showCategorySelect: false,
      isCreate: false, //是创建
      createAction: { type: 'save' },
      createInfo: {} // 创建所需要的id 标题名信息
    }
  },
  mounted() {
    this.getExamineCategoryList()
  },
  methods: {
    // 审批类型列表
    getExamineCategoryList() {
      this.loading = true
      oaExamineCategoryList()
        .then(res => {
          this.loading = false
          this.categoryOptions = [{ categoryId: '', title: '全部' }].concat(
            res.data
          )
        })
        .catch(() => {
          this.loading = false
        })
    },
    // 重置按钮
    reset() {
      this.categoryType = ''
    },
    editDetail(item) {
      item.title = item.categoryName
      this.createInfo = item
      this.createAction = { type: 'update', id: item.examineId, data: item }
      this.isCreate = true
    },
    // 创建
    newBtn() {
      this.showCategorySelect = true
    },
    // 审批类型选择
    selcetExamineCategory(item) {
      this.createInfo = item
      this.createAction = { type: 'save' }
      this.isCreate = true
    },
    createSaveSuccess() {
      this.$refs.tabcontentmy[0].searchBtn()
    },
    hideView() {
      this.isCreate = false
    },
    tabClick(val) {}
  }
}
</script>

<style scoped lang="scss">
@import '../styles/tabs.scss';

.journal {
  overflow: auto;
  position: relative;
  .btn-select {
    position: absolute;
    top: 10px;
    right: 40px;
    z-index: 999;
    .select-group {
      display: inline-block;
      .el-select {
        margin: 0 20px 0 10px;
        width: 116px;
      }
    }
  }
  .el-tabs {
    height: 100%;
    display: flex;
    flex-direction: column;
  }
  .el-tabs /deep/ .el-tabs__content {
    padding: 0 30px;
    flex: 1;
    display: flex;
    flex-direction: column;
    // overflow: auto;
    .el-tab-pane {
      flex: 1;
      display: flex;
      flex-direction: column;
    }
  }
}
</style>
