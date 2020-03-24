<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 书目管理
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <!-- <el-button
                    type="primary"
                    icon="el-icon-delete"
                    class="handle-del mr10"
                    @click="delAllSelection"
                >批量删除</el-button>-->
                <el-select
                    @change="getDataByCate(categoryId)"
                    v-model="categoryId"
                    placeholder="类别"
                    class="handle-select mr10"
                >
                    <el-option
                        v-for="(item,index) in categoryList"
                        :key="index"
                        :label="item.name"
                        :value="item.id"
                    ></el-option>
                </el-select>
                <el-input v-model="query.name" placeholder="书名" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
            </div>
            <el-table
                :data="tableData"
                border
                class="table"
                ref="multipleTable"
                header-cell-class-name="table-header"
                @selection-change="handleSelectionChange"
            >
                <!-- <el-table-column type="selection" width="55" align="center"></el-table-column> -->
                <el-table-column prop="id" label="ID" width="55" align="center"></el-table-column>
                <el-table-column prop="name" label="书名"></el-table-column>
                <el-table-column label="价格">
                    <template slot-scope="scope">￥{{scope.row.price}}</template>
                </el-table-column>
                <el-table-column label="封面" align="center">
                    <template slot-scope="scope">
                        <el-image
                            class="table-td-thumb"
                            :src="scope.row.image"
                            :preview-src-list="[scope.row.image]"
                        ></el-image>
                    </template>
                </el-table-column>
                <el-table-column prop="author" label="作者"></el-table-column>
                <el-table-column prop="store" label="库存" align="center"></el-table-column>
                <el-table-column prop="score" label="评分"></el-table-column>
                <el-table-column label="操作" width="180" align="center">
                    <template slot-scope="scope">
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.$index, scope.row)"
                        >编辑</el-button>
                        <el-button
                            type="text"
                            icon="el-icon-delete"
                            class="red"
                            @click="handleDelete(scope.$index, scope.row)"
                        >删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="query.pageIndex"
                    :page-size="query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
                ></el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="书名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="作者">
                    <el-input v-model="form.author"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price"></el-input>
                </el-form-item>
                <el-form-item label="库存">
                    <el-input v-model="form.store"></el-input>
                </el-form-item>
                <el-form-item label="评分">
                    <el-input v-model="form.score"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import { fetchData } from '../../api/index';
export default {
    name: 'basetable',
    data() {
        return {
            query: {
                address: '',
                name: '',
                pageIndex: 1,
                pageSize: 5
            },
            tableData: [],
            multipleSelection: [],
            delList: [],
            editVisible: false,
            pageTotal: 0,
            form: {},
            idx: -1,
            id: -1,
            categoryList: [],
            categoryId: ''
        };
    },
    created() {
        this.getData();
        this.getCategory();
    },
    methods: {
        // 获取 easy-mock 的模拟数据
        getData() {
            // fetchData(this.query).then(res => {
            //     console.log(res);
            //     this.tableData = res.list;
            //     this.pageTotal = res.pageTotal || 50;
            // });
            this.axios
                .get('/manage/bookInfo?page=' + this.query.pageIndex + '&size=' + this.query.pageSize)
                .then(res => {
                    this.tableData = res.data.list;
                    this.pageTotal = res.data.total;
                    this.query.pageIndex = res.data.pageNum;
                    this.query.pageSize = res.data.pageSize;
                    console.log(res.data);
                })
                .catch(res => {
                    console.log(res.data);
                });
        },
        getDataByCate(categoryId) {
            this.axios
                .get('/home/bookInfo/' + categoryId + '?page=' + this.query.pageIndex + '&size=' + this.query.pageSize)
                .then(res => {
                    this.tableData = res.data.list;
                    this.pageTotal = res.data.total;
                    this.query.pageIndex = res.data.pageNum;
                    this.query.pageSize = res.data.pageSize;
                    console.log(res.data);
                })
                .catch(res => {
                    console.log(res.data);
                });
        },
        // 触发搜索按钮
        handleSearch() {
            this.$set(this.query, 'pageIndex', 1);
            this.axios
                .get('/home/bookInfo/search?search=' + this.query.name)
                .then(res => {
                    this.tableData = res.data;
                    this.pageTotal = res.data.length;
                     this.query.pageIndex = 1;
                     this.query.pageSize = 10;
                    console.log(res.data);
                })
                .catch(res => {
                    console.log(res.data);
                });
        },
        // 删除操作
        handleDelete(index, row) {
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
                .then(() => {
                    console.log(this.tableData[index].id);
                    this.axios
                        .delete('/manage/bookInfo/' + this.tableData[index].id)
                        .then(res => {
                            if (res.data == true) {
                                this.$message.success('删除成功');
                                this.tableData.splice(index, 1);
                            } else {
                                this.$message.error('删除失败');
                            }
                        })
                        .catch(res => {
                            console.log(res.data);
                        });
                })
                .catch(() => {});
        },
        getCategory() {
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
        // 多选操作
        handleSelectionChange(val) {
            this.multipleSelection = val;
        },
        delAllSelection() {
            const length = this.multipleSelection.length;
            let str = '';
            this.delList = this.delList.concat(this.multipleSelection);
            for (let i = 0; i < length; i++) {
                str += this.multipleSelection[i].name + ' ';
            }
            this.$message.error(`删除了${str}`);
            this.multipleSelection = [];
        },
        // 编辑操作
        handleEdit(index, row) {
            this.idx = index;
            this.form = row;
            this.editVisible = true;
        },
        // 保存编辑
        saveEdit() {
            this.editVisible = false;

            this.axios
                .put('/manage/bookInfo', this.form)
                .then(res => {
                    if (res.data == true) {
                        this.$message.success(`修改第 ${this.idx + 1} 行成功`);
                    } else {
                        this.$message.error(`修改第 ${this.idx + 1} 行失败`);
                    }
                })
                .catch(res => {
                    console.log(res.data);
                });
            this.$set(this.tableData, this.idx, this.form);
        },
        // 分页导航
        handlePageChange(val) {
            if (this.categoryId == '') {
                this.$set(this.query, 'pageIndex', val);
                this.getData();
            } else {
                this.$set(this.query, 'pageIndex', val);
                this.getDataByCate(this.categoryId);
            }
        }
    }
};
</script>

<style scoped>
.handle-box {
    margin-bottom: 20px;
}

.handle-select {
    width: 120px;
}

.handle-input {
    width: 300px;
    display: inline-block;
}
.table {
    width: 100%;
    font-size: 14px;
}
.red {
    color: #ff0000;
}
.mr10 {
    margin-right: 10px;
}
.table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
}
</style>
