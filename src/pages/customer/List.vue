<template>
    <div>
         <h2>顾客管理</h2>
        <!-- 按钮 --><div>
        <el-button type="primary" size="small" @click.prevent="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        </div>
        <!-- 按钮 -->

        <!-- 表格开始 -->
        <el-table :data="customers">
            <el-table-column label="编号" prop="id"></el-table-column>  
            <el-table-column label="姓名" prop="realname"></el-table-column>          
            <el-table-column label="联系方式" prop="telephone"></el-table-column>         
            <el-table-column label="操作">
                <template v-slot="slot">  
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpadteHandler(slot.row)">修改</a>
                </template>
                </el-table-column>         
        </el-table>
        <!-- 表格结束 -->

        <!-- 分页开始 -->
        <el-pagination
        layout="prev, pager, next"
        :total="50">
        </el-pagination>
        <!-- 分页结束 -->

        <!-- 模态框开始 -->
        <el-dialog
        :title="title"
        :visible.sync="visible"
        width="50%"
            >
            ---{{form}}
        <el-form :model="form" label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"></el-input>
            </el-form-item>
            <el-form-item label="密码">
                <el-input v-model="form.password" type="password"></el-input>
            </el-form-item>
            <el-form-item label="真实姓名">
                <el-input v-model="form.realname"></el-input>
            </el-form-item>
            <el-form-item label="手机号">
                <el-input v-model="form.telephone"></el-input>
            </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
            <el-button @click.prevent="closeModalHandler" size="small">取 消</el-button>
            <el-button type="primary" @click.prevent="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
    <!-- 模态框结束 -->
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers
            this.customers=response.data;
        })
        },
        submitHandler(){
            //this.form对象--字符串-->后台{type:"customer",age:12}
            //json字符串'{"type":"customer","age:12"}'
            //request.post(url,this.form)
            //查询字符串 type=customer&age=12
            //通过request与后台进行交互，并且要携带参数
            let url="http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toUpadteHandler(row){
            //模态框的表单中显示当前行的信息
            this.form=row;
            this.title="修改顾客信息";
            this.visible=true;
        },
         closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
                type:"customer"
            }
            this.title="录入顾客信息";
            this.visible=true;
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口完成删除操作
            let url="http://localhost:6677/customer/deleteById?id="+id;
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

        }
       
    },
    //用于存放要向网页中显示的数据 
    data(){
        return{
            title:"录入客户信息",
            visible:false,
            customers:[],
            form:{
                type:"customer"
            }
        }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕
        this.loadData();

    }
    
}
</script>
<style scoped>

</style>