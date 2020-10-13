<template>
    <div>
        <h2>栏目管理</h2>
        <!-- 按钮-->
        <div>
            <el-button type="primary" size="small"  @click="toAddHandler">添加</el-button>
            <el-button type="danger"  size="small">批量删除</el-button>
        </div>
        <!--/按钮结束-->
        <!-- 表格-->
        <el-table :data="category">
            <el-table-column width="50" prop="all" label="全选"><el-checkbox v-model="checked"></el-checkbox></el-table-column>
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格结束-->
        <!-- 分页-->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页结束-->
        <!-- 模态框-->
        <el-dialog 
            :title="title"
            :visible.sync="visible"
            width="30%">
            <!-- 测试{{form}} -->
            <el-form :model="form" label-width="80px">
                <el-form-item label="栏目名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="序号">
                    <el-input v-model="form.num"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- /模态框结束-->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            //vue实例创建完毕
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
            //this为当前vue实例
            //将查询结果设置到category中this指向外部函数的this
            this.category=response.data;
        })
        },
        submitHandler(){
            //this.from 对象 --- 字符串 ---> 后台 {type:'category',age:2}
            //json字符串 '{"type":"category","age":12}'
            //request.post(url,this.form)
            //查询字符串: type=category&age=12
            //通过request与后台进行交互，并且携带参数
            let url = "http://localhost:6677/category/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                //调用后台接口,完成删除操作
                let url = "http://localhost:6677/category/deleteById?id="+id;
                request.get(url).then((response)=>{
                    //1.刷新数据
                    this.loadData();
                    //2.提示结束
                   this.$message({
                        type: 'success',
                        message: response.message
                });
                });
            })
        },
        toUpdateHandler(row){
            //模态框表单中显示当前行的信息
            this.form = row;
            this.title = "修改评论";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            //将form变为初始值
            this.form = {
                type:"category"
            }
            this.title = "添加栏目";
            this.visible = true;
        },
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            title:"添加栏目",
            textarea: '',
            visible:false,
            category:[],
            form:{
                type:"category"
            }
        }
    },
    created(){
        //this为当前vue实例
        //将查询结果设置到category中this指向外部函数的this
        this.loadData();
    }
}
</script>

<style scoped>

</style>