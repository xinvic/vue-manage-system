<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-calendar"></i> 图书
                </el-breadcrumb-item>
                <el-breadcrumb-item>添加图书</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="form-box">
                <el-form ref="form" :model="form" label-width="80px">
                    <el-form-item label="书籍名称">
                        <el-input v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="分类">
                        <el-select v-model="form.categoryId" placeholder="请选择">
                            <el-option
                                v-for="(item,index) in categoryList"
                                :key="index"
                                :label="item.name"
                                :value="item.id"
                            ></el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item style="width:200px " label="价格">
                        <el-input v-model="form.price"></el-input>
                    </el-form-item>
                     <el-form-item style="width:200px " label="几成新">
                        <el-input v-model="form.depreciation"></el-input>
                    </el-form-item>
                    <el-form-item style="width:200px " label="评分">
                        <el-input v-model="form.score"></el-input>
                    </el-form-item>
                    <el-form-item style="width:200px " label="作者">
                        <el-input v-model="form.author"></el-input>
                    </el-form-item>
                    <el-form-item style="width:200px " label="库存">
                        <el-input v-model="form.store"></el-input>
                    </el-form-item>
                    <el-form-item label="描述">
                        <el-input type="textarea" rows="5" v-model="form.description"></el-input>
                    </el-form-item>
                    <el-form-item style="width:200px">
                        <el-upload
                            class="upload-demo"
                            style="width:100px height:100px"
                            action="http://localhost:8088/v1/manage/bookInfo/upload"
                            :on-success="success"
                        >
                            <el-button size="small" type="primary">上传封面</el-button>
                        </el-upload>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">添加图书</el-button>
                        <el-button>取消</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>
    </div>
</template>

<script>
import VueCropper from 'vue-cropperjs';
export default {
    name: 'baseform',
    data() {
        return {
            form: {
                name: '',
                categoryId: '',
                score: '',
                price: '',
                store: '',
                author: '',
                description: '',
                image: '',
                depreciation:''
            },
            categoryList: []
        };
    },
    components: {
        VueCropper
    },
    created() {
        this.axios
            .get('/home/category')
            .then(res => {
                console.log(res.data);
                this.categoryList = res.data.data;
            })
            .catch(res => {
                console.log(res.data);
            });
    },
    methods: {
        success(response) {
            this.form.image = response;
        },
        onSubmit() {
           this.axios
            .post('/manage/bookInfo',this.form)
            .then(res => {
                console.log(res.data);
            })
            .catch(res => {
                console.log(res.data);
            });
            this.$message.success('提交成功！');
        }
    }
};
</script>
<style >
.el-upload-list--picture-card .el-upload-list__item {
  height: 90px;
  width: 90px;
}
.el-upload--picture-card {
  height: 90px;
  width: 90px;
  line-height: 100px;
}
</style>