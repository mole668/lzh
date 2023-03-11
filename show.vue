<template>
  <div class="app-container">
    <div>
      <el-button type="primary"
                 @click="tomainpage()"
                 icon="el-icon-s-home">返回首页</el-button>
    </div>
    <el-container>
      <el-header>资源名称</el-header>
      <h1 class="style-two">{{ resource.name }}</h1>
      <el-main>
        <p>简介</p>
        <div class="centered-text">{{ resource.introdution }}</div>
        <p>详细介绍</p>
        <span>{{ resource.description }}</span>
        <p>所属分类</p>
        <span>{{ resource.category }}</span>
        <p>资源链接</p>
        <a v-bind:href="resource.link" target="_blank">{{ resource.link }}</a>
      </el-main>
    </el-container>
    <el-form ref="resource"
             :model="resource"
             label-width="80px">

    </el-form>
  </div>
</template>
<script>
import { getCategories, getCategoriesByParentId } from '@/api/category'
import { addBrand, getBrandById } from '@/api/brand'
import { getResourceById } from '@/api/resourse'

export default {
  data () {
    return {
      form: {
        name: '',
        letter: '',
        image: '',
        categoryId: '',
        link: ''
      },
      props: {
        lazy: true,
        lazyLoad: this.lazyLoad
      },
      resource: {
        id: '',
        name: '',
        category: '',
        description: '',
        introdution: '',
        link: ''
      }
    }
  },
  // 页面被加载时会调用此方法 (如果直接点击添加品牌，也会调用此方法)
  created () {
    // 取出从列表页面传递过来的品牌id
    if (this.$route.params.id) {
      const id = this.$route.params.id
      console.log(id)
      getResourceById(id).then(res => {
        this.resource = res.data.resource;
      })
      console.log(this.resource.id)
      // 根据id请求品牌信息
    }
  },
  methods: {
    tomainpage () {
      console.log("innnnnn!")
      // 跳转到添加页面，同时传递品牌id，方便在添加页面查询品牌信息，并显示
      this.$router.push('/')
    },

    // 表单提交调用的方法
    onSubmit () {
      // form中存储了所有的用户填写的信息，包括图片地址
      console.log(this.form)
      // 将信息传送到后端，存储到数据库
      addBrand(this.form).then(res => {
        this.$message({
          message: '添加成功',
          type: 'success'
        })
      })
      // 跳转到品牌列表
      this.$router.push({ path: '/brand/list' })
    },
    // 图片上传成功调用的方法
    handleAvatarSuccess (res) {
      // res为后端返回的图片地址，将地址设置给imageUrl用于显示
      this.form.image = 'http://localhost/' + res
    },
    // 图片上传之前调用的方法
    beforeAvatarUpload () { },
    // 加载分类
    lazyLoad (node, resolve) {
      const { level } = node
      // 如果level为0则加载顶级分类
      if (level == 0) {
        getCategories().then(res => {
          // 后端传回的数据的key为name，当前组件需要的key为label
          // 通过map方法处理传回的每个元素，生成新的数组
          const categories = res.data.items.map(item => {
            return {
              value: item.id,
              label: item.name,
              leaf: false
            }
          })
          // 将处理后的数据显示到界面
          resolve(categories)
        })
        // 子节点
      } else {
        getCategoriesByParentId(node.data.value).then(res => {
          // 后端传回的数据的key为name，当前组件需要的key为label
          // 通过map方法处理传回的每个元素，生成新的数组
          const categories = res.data.items.map(item => {
            return {
              value: item.id,
              label: item.name,
              // 当level大于2时，已经是最后一级了
              leaf: level >= 2
            }
          })
          // 将处理后的数据显示到界面
          resolve(categories)
        })
      }
      /*
      elementui官方示例代码
      setTimeout(() => {
        const nodes = Array.from({ length: level + 1 }).map((item) => ({
          value: 1,
          label: `选项1`,
          leaf: level >= 2,
        }));
        // 通过调用resolve将子节点数据返回，通知组件数据加载完成
        resolve(nodes);
      }, 1000);
      */
    }
  }
}
</script>

<style>
.el-header {
  background-color: #b3c0d1;
  color: white;
  text-align: center;
  line-height: 60px;
}
.centered-text {
  text-align: center;
}
.el-main {
  background-color: rgb(105, 127, 202);
  text-align: center;
}
.el-main p {
  margin-bottom: 10px;
}
.el-main a {
  color: #409eff;
}
h1.style-two {
  font-style: italic;
  color: red;
  text-align: center;
}
</style>
