<template>
  <div class="app-container">
    <h1>ECUN gene editing research platform</h1>
    <el-row>
      <el-button type="success"
                 @click="tomanage()">管理页面入口
      </el-button>
    </el-row>
    <el-row>
      <el-col :span="12">
        <el-input placeholder="请输入搜索内容"
                  v-model="input"
                  clearable>
        </el-input></el-col>
      <el-col :span="12">
        <el-button type="primary"
                   icon="el-icon-search"
                   @click="searchByVal()">搜索
        </el-button>
      </el-col>
    </el-row>
    <br>
    <span>Resource Application</span>
    <el-checkbox-group v-model="checkboxGroup1"
                       border>
      <el-checkbox-button v-for="purpose in AppOptions"
                          :label="purpose"
                          :key="purpose.id"
                          v-if="purpose.type!==null">{{purpose.type}}</el-checkbox-button>
    </el-checkbox-group>
    Resource Type
    <el-checkbox-group v-model="checkboxGroup2"
                       border>
      <el-checkbox-button v-for="kind in TypeOptions"
                          :label="kind"
                          :key="kind.id"
                          v-if="kind.type!==null">{{kind.type}}</el-checkbox-button>
    </el-checkbox-group>

    <el-table :data="tableData"
              :header-cell-style="{'text-align':'center'}"
              style="width: 100%">
      <el-table-column label="name"
                       width="450">
        <template slot-scope="scope">
          <div slot="reference"
               class="name-wrapper">
            <el-button type="primary"
                       @click="changeDetail(scope.row.id)"
                       plain>{{ scope.row.name }}</el-button>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="introduction"
                       width="400">
        <template slot-scope="scope">
          <i class="el-icon-notebook-1" />
          <span style="margin-left: 10px"
                class="keep-all">{{ scope.row.introdution }}</span>
        </template>
      </el-table-column>
    </el-table>
    <!--这里是分页部分-->
    <el-pagination v-if="isShow"
                   :current-page.sync="currentPage"
                   background
                   layout="prev, pager, next"
                   :total="total"
                   :page-size="pageSize"
                   @current-change="changePage" />
    <el-button type="success"
               @click="tofeedback()">feedback
    </el-button>

  </div>
</template>

<script>
import { getBrands, editBrand } from '@/api/brand.js'
import { getResources, getResourceByval, getResourceApp, getResourceType } from '@/api/resourse.js'
const cityOptions = ['STRA', 'On-Target', 'Off-Target'];
const kindOptions = ['Data resources', 'Webservers', 'Software'];

export default {
  data () {
    return {
      input: '',
      // 这两个box是用于复选的
      checkboxGroup1: [],
      checkboxGroup2: [],
      purposes: cityOptions,
      kinds: kindOptions,

      total: 1,
      pageSize: 1,
      // 默认不显示分页
      isShow: false,
      tableData: [],
      AppOptions: [],
      TypeOptions: [],
    }
  },
  created: function () {
    // 打开网页时调用
    getResources(1, this.input, [], []).then((response) => {
      console.log(response)
      this.tableData = response.data.items.records
      // 总记录数
      this.total = response.data.items.total
      // 每页显示的条数
      this.pageSize = response.data.items.size
      // 网络请求成功后，显示分页
      this.isShow = true
    })
    getResourceApp().then((response) => {
      this.AppOptions = response.data.apps
    })
    getResourceType().then((response) => {
      this.TypeOptions = response.data.type
    })
  },
  methods: {
    tomanage () {
      console.log("innnnnn!")
      // 跳转到添加页面，同时传递品牌id，方便在添加页面查询品牌信息，并显示
      this.$router.push('/dashboard')
    },
    tofeedback () {
      this.$router.push('/feedback')
    },
    // 换页
    changePage (pageNum) {
      // 在这里才传参数
      var box1 = this.checkboxGroup1.map(function (obj) {
        return obj.type;
      });
      var box2 = this.checkboxGroup2.map(function (obj) {
        return obj.type;
      });
      getResources(pageNum, this.input, box1, box2).then((response) => {
        this.tableData = response.data.items.records
      })
    },
    // 点击进入详情页
    changeDetail (id) {
      console.log(id)
      this.$router.push('/resource/show/' + id)
    },
    //查询函数
    searchByVal () {
      console.log(this.input)
      //数组的map方法
      var box1 = this.checkboxGroup1.map(function (obj) {
        return obj.type;
      });
      console.log(box1)
      var box2 = this.checkboxGroup2.map(function (obj) {
        return obj.type;
      });
      console.log(box2)
      //getResources(1, this.input, [], []).then((response) => {
      getResources(1, this.input, box1, box2).then((response) => {
        console.log(response)
        this.tableData = response.data.items.records
        // 总记录数
        this.total = response.data.items.total
        // 每页显示的条数
        this.pageSize = response.data.items.size
        // 网络请求成功后，显示分页
        this.isShow = true
        this.currentPage = 1;
      })
      /*
        getResourceByval(this.input).then((response) => {
          this.tableData = response.data.items.records
          this.total = response.data.items.total
          // 每页显示的条数
          this.pageSize = response.data.items.size
          // 网络请求成功后，显示分页
          this.isShow = true
        })*/
    }
  }
}
</script>

<style>
.keep-all {
  word-break: keep-all;
}
</style>