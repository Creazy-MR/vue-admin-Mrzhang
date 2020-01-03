<template>
    <!--顾客管理  -->
    <div>
        <el-button  type="success"  size="small" @click="toAddHandler" >
            添加
        </el-button>
        <el-button type="danger" size="small">
            批量删除
        </el-button>
       
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <!-- <el-table-column prop="gender" label="性别"></el-table-column> -->
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
               <template v-slot="slot">
                   <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                   <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

               </template>
            </el-table-column>
            
        </el-table>
        <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
        <el-dialog
                :title="title"
                :visible.sync="visible"
                width="60%" >
                {{form}}
                <el-form :model="form" label-width="80px">
                     <el-form-item label="用户名">
                        <el-input  v-model="form.username">

                        </el-input>
                    </el-form-item>

                    <el-form-item label="密码">
                        <el-input type="password" v-model="form.password">

                        </el-input>
                    </el-form-item>

                    <el-form-item label="真实姓名">
                        <el-input v-model="form.realname">

                        </el-input>
                    </el-form-item>
                    <el-form-item label="手机号">
                        <el-input  v-model="form.telephone">

                        </el-input>
                    </el-form-item>

                </el-form>
                <span slot="footer" class="dialog-footer">
                    <el-button @click="closeModalHandler" size="small">取 消</el-button>
                    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
                </span>
            </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
            let url="http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //this指向外部函数的this
            this.customers=response.data;
        })
        },
        toAddHandler(){
            this.form={
                type:"customer"
            }
            this.title="录入客人信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toUpdateHandler(row){
            this.title = "修改客人信息"
            this.form=row;
            this.visible=true;
        },
        submitHandler(){
                //与后台进行交互
                let url="http://localhost:6677/customer/saveOrUpdate";
                // request.post(url,this.form)
                request({
                    //小括号方法调用
                    url,
                    method:"Post",
                    headers:{
                        //查询字符串 type=customer&age=12
                        //通过request与后台交互
                        "Content-Type":"application/x-www-form-urlencoded"
                    },
                    data:querystring.stringify(this.form)
                }).then((response)=>{

                    this.closeModalHandler();
                    this.loadData();
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
            //调用后台接口，实现删除
            let url="http://localhost:6677/customer/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新页面
                this.loadData();
                //提示信息
                this.$message({
   
                    type: 'success',
                    message: '删除成功!'+id
                });
            })
          
        })
        }
    },
    data(){
        //用于存放要向网页中显示的数据
        return{
            visible:false,
            title:"录入客人信息",
            customers:[
             
            ],
            form:{
                type:"customer"
            }
        }
    },
    created(){
        this.loadData();
    }
}
</script>
<style scoped>

</style>