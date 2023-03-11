<template>
  <div class="app-container">
    <el-row>
      <el-button type="success"
                 @click="tomanage()">管理页面入口
      </el-button>
    </el-row>
    <el-input placeholder="请输入搜索内容"
              v-model="input"
              clearable>
    </el-input>
    <el-button type="primary"
               icon="el-icon-search"
               @click="searchByVal()">搜索
    </el-button>
    <br>
    <span>Resource Application</span> 
    <el-checkbox-group v-model="checkboxGroup1"
                       border>        
      <el-checkbox-button v-for="purpose in purposes"
                          :label="purpose"
                          :key="purpose">{{purpose}}</el-checkbox-button>
    </el-checkbox-group>
    Resource Type
    <el-checkbox-group v-model="checkboxGroup2"
                       border>     
      <el-checkbox-button v-for="kind in kinds"
                          :label="kind"
                          :key="kind">{{kind}}</el-checkbox-button>
    </el-checkbox-group>

    <el-table :data="tableData"
              style="width: 100%">
      <el-table-column label="名称"
                       width="300">
        <template slot-scope="scope">
          <div slot="reference"
               class="name-wrapper">
            <el-button type="primary"
                       @click="changeDetail(scope.row.id)"
                       plain>{{ scope.row.name }}</el-button>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="简介"
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
                   background
                   layout="prev, pager, next"
                   :total="total"
                   :page-size="pageSize"
                   @current-change="changePage" />
  </div>
</template>

<script>
import { getBrands, editBrand } from '@/api/brand.js'
import { getResources, getResourceByval } from '@/api/resourse.js'
const cityOptions = ['STRA', 'On-Target', 'Off-Target'];
const kindOptions = ['Data resources', 'Webservers', 'Software'];

export default {
  data () {
    return {
      input: '',

      checkboxGroup1: [''],
      checkboxGroup2: [''],
      purposes: cityOptions,
      kinds: kindOptions,

      total: 1,
      pageSize: 1,
      // 默认不显示分页
      isShow: false,
      tableData: []
    }
  },
  created: function () {
    // 打开网页时调用
    getResources().then((response) => {
      console.log(response)
      this.tableData = response.data.items.records
      // 总记录数
      this.total = response.data.items.total
      // 每页显示的条数
      this.pageSize = response.data.items.size
      // 网络请求成功后，显示分页
      this.isShow = true
    })
  },
  methods: {
    tomanage () {
      console.log("innnnnn!")
      // 跳转到添加页面，同时传递品牌id，方便在添加页面查询品牌信息，并显示
      this.$router.push('/dashboard')
    },
    // 换页
    changePage (pageNum) {
      // 在这里才传参数
      getResources(pageNum).then((response) => {
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
      getResourceByval(this.input).then((response) => {
        this.tableData = response.data.items.records
      })
    }
  }
}
</script>

<style>
.keep-all {
  word-break: keep-all;
}
</style>