<template>
  <div class="my-importment-news-show">
    <el-table
      :data="tableData"
      max-height="600"
      stripe
      style='width: 100%'>
      <el-table-column
        prop="date"
        label="时间"
        width="250">
        <template slot-scope="scope">
          <el-date-picker
            v-model="scope.row.time"
            align="right"
            type="date"
            placeholder="选择日期"
            :picker-options="pickerOptions">
          </el-date-picker>
        </template>
      </el-table-column>
      <el-table-column
        prop="title"
        label="标题"
        width="280">
        <template slot-scope="scope">
          <el-input v-model="scope.row.title" placeholder="请输入内容"></el-input>
        </template>
      </el-table-column>
      <el-table-column
        prop="content"
        label="内容">
        <template slot-scope="scope">
          <el-input type="textarea" :rows="2" v-model="scope.row.content" placeholder="请输入内容"></el-input>
        </template>
      </el-table-column>
      <el-table-column
        label="操作"
        width="100">
        <template slot-scope="scope">
          <el-button @click="handleClick(scope.row)" type="text" size="small">确认</el-button>
          <el-button type="text" size="small" @click="handleDeleteClick(scope.row,scope.$index)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <i @click="addOne()" class="el-icon-plus" style="font-size: 2rem;margin:0.5rem;"></i>
  </div>
</template>
<script>

export default {
  name: 'HomepageAlert',
  data() {
    return {
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now();
        },
        shortcuts: [{
          text: '今天',
          onClick(picker) {
            picker.$emit('pick', new Date());
          }
        }, {
          text: '昨天',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24);
            picker.$emit('pick', date);
          }
        }, {
          text: '一周前',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
            picker.$emit('pick', date);
          }
        }]
      },
      dialogImageUrl: '',
      dialogVisible: false,
      tableData: [
      ]
    }
  },
  components:{
  },
  methods: {
    onSubmit() {
      console.log('submit!');
    },
    handleAvatarSuccess(res, file) {
      console.log(file)
      this.imageUrl = URL.createObjectURL(file.raw);
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg';
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!');
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!');
      }
      return isJPG && isLt2M;
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    addOne(){
      var myDate = new Date();
      this.tableData.unshift(
        {
          index: myDate.getTime(),
          time: '',
          title: '',
          content: '',
        }
      )
    },
    handleClick(data){
      var that = this
      console.log(data)
      jQuery.post(
        'https://husteicstu.cn:3000/message',
        data,
        function (res) {
          console.log(res)
          that.$message({
            message: "提交成功",
            type: "success"
          })
        }
      )
    },
    handleDeleteClick(data,index){
      var that = this
      this.tableData.splice(index, 1);
      console.log(data.index)
      jQuery.post(
        'https://husteicstu.cn:3000/message/delete',
        {index: data.index},
        function (res) {
          console.log(res)
          if(res.code == "-1"){
            that.$message({
              message: "删除失败",
              type: "success"
            })
          }
          else{
            that.$message({
              message: "删除成功",
              type: "success"
            })
            jQuery.get(
              'https://husteicstu.cn:3000/message',
              function (res) {
                console.log(res.data)
                that.tableData = res.data
              }
            )
          }
        }
      )
    },
  },
  mounted(){

  },
  created(){
    var that = this
    jQuery.get(
      'https://husteicstu.cn:3000/message',
      function (res) {
        console.log(res.data)
        that.tableData = res.data
      }
    )
  }
}
</script>

<style>
.my-importment-news-show-form-photo{
  margin-left: 80px;
  margin-bottom: 3rem;
}
.my-importment-news-show{
  display: flex;
  width: 90%;
  padding: 3rem 5rem;
  flex-direction: column;
}
.my-importment-news-show-form{
  width: 80%;
}
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
