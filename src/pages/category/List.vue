<template>
    
    <div>
        <el-button  type="button"  size="small" @click="toAddHandler" >
            添加
        </el-button>
        <el-button type="danger" size="small">
            批量删除
        </el-button>
       
        <el-table :data="category">
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
                     <el-form-item label="栏目名称">
                        <el-input  v-model="form.name">

                        </el-input>
                    </el-form-item>

                
                    <el-form-item label="序号">
                        <el-input  v-model="form.num">

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
            let url="http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //this指向外部函数的this
            this.category=response.data;
        })
        },
        toAddHandler(){
            this.title="录入栏目信息"
            this.visible=true;
            this.form={
                type:"category"
            }
        },
        closeModalHandler(){
            this.visible=false;
        },
        toUpdateHandler(){
            this.title = "修改栏目信息"
             this.form=row;
                this.visible=true;
        },
        submitHandler(){
                //与后台进行交互
                let url="http://localhost:6677/category/saveOrUpdate";
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
            let url="http://localhost:6677/category/deleteById?id="+id;
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
            title:"录入栏目信息",
            category:[
             
            ],
            form:{
                type:"category"
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