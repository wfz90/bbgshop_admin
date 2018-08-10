<template>
	<div class="content-page">
		<div class="content-nav">
			<el-breadcrumb class="breadcrumb" separator="/">
				<el-breadcrumb-item :to="{ path: '/dashboard' }">首页</el-breadcrumb-item>
				<el-breadcrumb-item>商品管理</el-breadcrumb-item>
				<el-breadcrumb-item>商品分类</el-breadcrumb-item>
			</el-breadcrumb>
			<div class="operation-nav">
				<router-link to="/dashboard/CategoryUpdatePage">
					<el-button type="primary" icon="plus">添加分类</el-button>
				</router-link>
			</div>
		</div>
		<div class="content-main">
			<div class="form-table-box">
				<el-tree
				  :data="treeData"
				  :props="defaultProps"
					node-key="id"
				  accordion
					:render-content="renderContent">
				</el-tree>
				<!-- <el-table :data="tableData" style="width: 100%" border stripe>
					<el-table-column prop="name" label="分类名称">
						<template slot-scope="scope">
							{{ scope.row.level == 2 ? '　' : '' }} {{scope.row.name}}
						</template>
					</el-table-column>
					<el-table-column prop="is_show" label="是否显示" width="100">
						<template slot-scope="scope">
							{{ scope.row.is_show == 1 ? '是' : '否' }}
						</template>
					</el-table-column>
					<el-table-column prop="sort_order" label="排序" width="80">
					</el-table-column>
					<el-table-column label="操作" width="140">
						<template slot-scope="scope">
							<el-button size="small" @click="handleRowEdit(scope.$index, scope.row)">编辑</el-button>
							<el-button size="small" type="danger" @click="handleRowDelete(scope.$index, scope.row)">删除</el-button>
						</template>
					</el-table-column>
				</el-table> -->
			</div>
		</div>
	</div>
</template>

<script>

  export default {
    data() {
      return {
        page: 1,
        total: 0,
        filterForm: {
          name: ''
        },
				defaultProps: {
					children: 'children',
					label: 'label',
				},
				treeData: [],
        tableData: []
      }
    },
    methods: {
			renderContent(h, { node, data, store }) {
        return (
          <span>
            <span>
							<span style="font-size:14px;">{node.label}</span>
							<span style="font-size:10px;color:#757575;padding-left:15px;">Order_{data.sort_order}</span>
              <span style="font-size:10px;color:#757575;padding-left:15px;">{data.is_show == 0 ? '未启用' : '已启用'}</span>
            </span>
            <span style="float: right; margin-right: 20px">
              <el-button size="small" on-click={ () => this.handleRowEdit(store, data) }>编辑</el-button>
              <el-button size="small" type="danger" on-click={ () => this.handleRowDelete(store, data) }>删除</el-button>
            </span>
          </span>);
      },
      handlePageChange(val) {
        this.page = val;
        //保存到localStorage
        localStorage.setItem('brandPage', this.page)
        localStorage.setItem('brandFilterForm', JSON.stringify(this.filterForm));
        this.getList()
      },
      handleRowEdit(store, data) {
        this.$router.push({ name: 'CategoryUpdatePage', query: { id: data.id } })
      },
      handleRowDelete(store, data) {
        this.$confirm('确定要删除?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.axios.post('category/destory', { id: data.id }).then((response) => {
            console.log(response.data)
            if (response.data.errno === 0) {
              this.$message({
                type: 'success',
                message: '删除成功 !'
              });
							store.remove(data);
              // this.getList();
            }
          })
        }).catch(() => {
					this.$message({
						type: 'info',
						message: '取消删除 !'
					});
				});
      },
      onSubmitFilter() {
        this.page = 1
        this.getList()
      },
      getList() {
        this.axios.get('category', {
          params: {
            page: this.page,
            name: this.filterForm.name
          }
        }).then((response) => {
					console.log(response);
          this.tableData = response.data.data
					this.datareload()
        })
      },
			datareload() {
				for (var i = 0; i < this.tableData.length; i++) {
					if (this.tableData[i].parent_id == 0) {
						// console.log(this.tableData[i].name);
						let obj = {}
						let children = []
						obj.label = this.tableData[i].name
						obj.id = this.tableData[i].id
						obj.sort_order = this.tableData[i].sort_order
						obj.is_show = this.tableData[i].is_show
						for (var j = 0; j < this.tableData.length; j++) {
							let childer = []
							if (this.tableData[j].parent_id !== 0 && this.tableData[j].parent_id == this.tableData[i].id) {
								let list = {}
								list.label = this.tableData[j].name
								list.id = this.tableData[j].id
								list.sort_order = this.tableData[j].sort_order
								list.is_show = this.tableData[j].is_show
								children.push(list)
							}
						}
						obj.children = children
						this.treeData.push(obj)
					}
				}
				console.log(this.treeData);
			},
    },
    components: {

    },
    mounted() {

      this.getList();
    }
  }

</script>

<style scoped>
.sub-category .el-table__expanded-cell{
	padding: 0;
}
</style>
