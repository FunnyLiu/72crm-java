<template>
  <el-container>
    <el-header class="nav-container">
      <navbar :navIndex="0"
              @nav-items-click="navClick"></navbar>
    </el-header>
    <el-container>
      <el-aside width="auto"
                class="aside-container">
        <sidebar :items="sidebarItems"
                 createButtonTitle="快速创建"
                 mainRouter="workbench">
          <div slot="add"
               class="quick-add">
            <div class="quick-add-content">
              <p v-for="(item, index) in list"
                 :key="index"
                 @click="addSkip(item)">
                <i class="wukong"
                   :class="'wukong-' + item.icon"></i>
                <span>{{item.label}}</span>
              </p>
            </div>
          </div>
        </sidebar>
      </el-aside>
      <el-main id="workbench-main-container"
               style="padding:15px;">
        <app-main></app-main>
      </el-main>
    </el-container>
    <!-- create approval category select modal -->
    <examine-category-select :show="showCategorySelect"
                             @select="selcetExamineCategory"
                             @close="showCategorySelect=false"></examine-category-select>
    <examine-create-view v-if="isCreate"
                         :categoryId="createInfo.categoryId"
                         :categoryTitle="createInfo.title"
                         @hiden-view="isCreate = false"></examine-create-view>
  </el-container>
</template>

<script>
import workbenchRouter from '@/router/modules/workbench'
import { Navbar, Sidebar, AppMain } from './components'
import ExamineCreateView from '@/views/OAManagement/examine/components/examineCreateView'
import ExamineCategorySelect from '@/views/OAManagement/examine/components/examineCategorySelect'

export default {
  name: 'Layout',
  components: {
    //head layout ui
    Navbar,
    //side layout ui
    Sidebar,
    AppMain,
    ExamineCategorySelect,
    ExamineCreateView
  },
  data() {
    return {
      addDialog: false,
      list: [
        {
          label: '日志',
          icon: 'log',
          img: require('@/assets/img/add_journal.png')
        },
        {
          label: '审批',
          icon: 'examine',
          img: require('@/assets/img/add_examine.png')
        },
        {
          label: '任务',
          icon: 'task',
          img: require('@/assets/img/add_task.png')
        },
        {
          label: '日程',
          icon: 'schedule',
          img: require('@/assets/img/add_schedule.png')
        },
        {
          label: '公告',
          icon: 'notice',
          img: require('@/assets/img/add_notice.png')
        }
      ],
      // 新建
      showCategorySelect: false,
      isCreate: false, //是创建
      createInfo: {} // 创建所需要的id 标题名信息
    }
  },
  computed: {
    sidebarItems() {
      return workbenchRouter.children
    }
  },
  methods: {
    navClick(index) {},
    // 新增跳转
    addSkip(val) {
      //handle nav router redirect     
      switch (val.label) {
        case '日志':
          this.$router.push({ path: 'journal', query: { routerKey: 1 } })
          break
        case '审批':
          this.showCategorySelect = true
          break
        case '任务':
          this.$router.push({ path: 'task', query: { routerKey: 1 } })
          break
        case '日程':
          this.$router.push({ path: 'schedule-new', query: { routerKey: 1 } })
          break
        case '公告':
          this.$router.push({ path: 'notice-new', query: { routerKey: 1 } })
          break
      }
    },
    // 审批类型选择
    selcetExamineCategory(item) {
      this.createInfo = item
      this.isCreate = true
    }
  }
}
</script>

<style lang="scss" scoped>
@import './styles/common.scss';
.el-container {
  min-height: 0;
}
.aside-container {
  position: relative;
  background-color: #2d3037;
  box-sizing: border-box;
  border-right: solid 1px #e6e6e6;
  overflow: visible;
}

.nav-container {
  padding: 0;
  box-shadow: 0px 1px 2px #dbdbdb;
  z-index: 100;
  min-width: 1200px;
}
.quick-add {
  height: 178px;
}
</style>
