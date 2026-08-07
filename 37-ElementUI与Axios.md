Element UI 是一个前端 UI 组件，预先封装好了很多 UI 的标签，可以直接使用

在线文档：https://element.eleme.cn/#/zh-CN/component/drawer

1、引入 Element UI 的 css、js

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
	</head>
	<body>
		<div id="app">
			<h1>{{title}}</h1>
			<input v-model="title" />
			<button @click="test">{{name}}</button>
			<el-button type="primary" icon="el-icon-eleme" @click="test">主要按钮</el-button>
			 <el-radio v-model="radio" label="1">备选项1</el-radio>
			 <el-radio v-model="radio" label="2">备选项2</el-radio>
			 <div>
				 <el-checkbox-group v-model="checkList">
				     <el-checkbox label="复选框 A"></el-checkbox>
				     <el-checkbox label="复选框 B"></el-checkbox>
				     <el-checkbox label="复选框 C"></el-checkbox>
				     <el-checkbox label="禁用" disabled></el-checkbox>
				     <el-checkbox label="选中且禁用" disabled></el-checkbox>
				   </el-checkbox-group>
			 </div>
			 <div style="width: 300px;">
				 <el-input v-model="input" placeholder="请输入内容"></el-input>
			 </div>
			 <di>
				 <el-input-number v-model="num" @change="handleChange" :min="1" :max="10" label="描述文字"></el-input-number>
			 </di>
			 <div>
				 <el-select v-model="value" placeholder="请选择">
				     <el-option
				       v-for="item in options"
				       :key="item.value"
				       :label="item.label"
				       :value="item.value">
				     </el-option>
				   </el-select>
			 </div>
			 <div>
				 <el-switch
				   v-model="val"
				   active-color="#13ce66"
				   inactive-color="#ff4949">
				 </el-switch>
			 </div>
			 <div class="block">
			     <span class="demonstration">默认</span>
			     <el-slider v-model="value1"></el-slider>
			   </div>
			   <div>
				   <el-time-select
				     v-model="time"
				     :picker-options="{
				       start: '08:30',
				       step: '00:10',
				       end: '12:30'
				     }"
				     placeholder="选择时间">
				   </el-time-select>
			   </div>
			   <div>
				   <el-date-picker
				         v-model="date"
				         type="date"
				         placeholder="选择日期"
						 :picker-options="pickerOptions">
				       </el-date-picker>
			   </div>
			   <div>
				   <el-table
				   v-loading="loading"
				       :data="tableData"
				       stripe
				       style="width: 100%">
				       <el-table-column
				         prop="date"
				         label="日期"
				         width="180">
				       </el-table-column>
				       <el-table-column
				         prop="name"
				         label="姓名"
				         width="180">
				       </el-table-column>
				       <el-table-column
				         prop="address"
				         label="地址">
				       </el-table-column>
					   <el-table-column
					         fixed="right"
					         label="操作"
					         width="100">
					         <template slot-scope="scope">
					           <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
					           <el-button type="text" size="small">编辑</el-button>
					         </template>
					       </el-table-column>
				     </el-table>
			   </div>
			   <div>
				   <el-tree :data="data" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
			   </div>
			   <el-pagination
			     background
			     layout="prev, pager, next"
			     :total="1000">
			   </el-pagination>
			   <div>
				   <el-badge :value="12" class="item">
				     <el-button size="small">评论</el-button>
				   </el-badge>
				   <el-badge :value="3" class="item">
				     <el-button size="small">回复</el-button>
				   </el-badge>
				   <el-badge :value="1" class="item" type="primary">
				     <el-button size="small">评论</el-button>
				   </el-badge>
				   <el-badge :value="2" class="item" type="warning">
				     <el-button size="small">回复</el-button>
				   </el-badge>
			   </div>
			   <el-row>
			     <el-col :sm="12" :lg="6">
			       <el-result icon="success" title="成功提示" subTitle="请根据提示进行操作">
			         <template slot="extra">
			           <el-button type="primary"  @click="test" size="medium">返回</el-button>
			         </template>
			       </el-result>
			     </el-col>
			     <el-col :sm="12" :lg="6">
			       <el-result icon="warning" title="警告提示" subTitle="请根据提示进行操作">
			         <template slot="extra">
			           <el-button type="primary" size="medium">返回</el-button>
			         </template>
			       </el-result>
			     </el-col>
			     <el-col :sm="12" :lg="6">
			       <el-result icon="error" title="错误提示" subTitle="请根据提示进行操作">
			         <template slot="extra">
			           <el-button type="primary" size="medium">返回</el-button>
			         </template>
			       </el-result>
			     </el-col>
			     <el-col :sm="12" :lg="6">
			       <el-result icon="info" title="信息提示" subTitle="请根据提示进行操作">
			         <template slot="extra">
			           <el-button type="primary" size="medium">返回</el-button>
			         </template>
			       </el-result>
			     </el-col>
			   </el-row>
			   <div>
				   <el-button type="text" @click="open">点击打开 Message Box</el-button>
			   </div>
			   <div>
				   <el-radio-group v-model="direction">
				     <el-radio label="ltr">从左往右开</el-radio>
				     <el-radio label="rtl">从右往左开</el-radio>
				     <el-radio label="ttb">从上往下开</el-radio>
				     <el-radio label="btt">从下往上开</el-radio>
				   </el-radio-group>
			   </div>
			   <el-button @click="drawer = true" type="primary" style="margin-left: 16px;">
			     点我打开
			   </el-button>
			   <el-drawer
			     title="我是标题"
			     :visible.sync="drawer"
			     :direction="direction"
			     :before-close="handleClose">
			     <span>我来啦!</span>
			   </el-drawer>
		</div>
	</body>
	<script src="js/vue.js"></script>
	<script src="js/elementui.js"></script>
	<script>
		new Vue({
			el: '#app',
			//数据
			data(){
				return {
					loading: false,
					title:'123',
					name:'测试按钮',
					radio: '2',
					checkList:'',
					input:'',
					num:'',
					value:'',
					options: [{
					          value: '选项1',
					          label: '黄金糕'
					        }, {
					          value: '选项2',
					          label: '双皮奶'
					        }, {
					          value: '选项3',
					          label: '蚵仔煎'
					        }, {
					          value: '选项4',
					          label: '龙须面'
					        }, {
					          value: '选项5',
					          label: '北京烤鸭'
					        }],
					val:'',
					value1:'',
					time:'',
					date:'',
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
					  },
					  {
						text: '一周后',
						onClick(picker) {
						  const date = new Date();
						  date.setTime(date.getTime() + 3600 * 1000 * 24 * 7);
						  picker.$emit('pick', date);
						}
					  }
					  ]
						},
					tableData: [{
							  date: '2016-05-02',
							  name: '王小虎',
							  address: '上海市普陀区金沙江路 1518 弄'
							}, {
							  date: '2016-05-04',
							  name: '王小虎',
							  address: '上海市普陀区金沙江路 1517 弄'
							}, {
							  date: '2016-05-01',
							  name: '王小虎',
							  address: '上海市普陀区金沙江路 1519 弄'
							}, {
							  date: '2016-05-03',
							  name: '王小虎',
							  address: '上海市普陀区金沙江路 1516 弄'
					}],
					data: [{
					          label: '一级 1',
					          children: [{
					            label: '二级 1-1',
					            children: [{
					              label: '三级 1-1-1',
								  children:[
									  {
										  label:'四级 1-1-1-1'
									  }
								  ]
					            }]
					          },
							  {
							    label: '二级 1-1',
							    children: [{
							      label: '三级 1-1-1',
							  								  children:[
							  									  {
							  										  label:'四级 1-1-1-1'
							  									  }
							  								  ]
							    }]
							  }
							  ]
					        }, {
					          label: '一级 2',
					          children: [{
					            label: '二级 2-1',
					            children: [{
					              label: '三级 2-1-1'
					            }]
					          }, {
					            label: '二级 2-2',
					            children: [{
					              label: '三级 2-2-1'
					            }]
					          }]
					        }, {
					          label: '一级 3',
					          children: [{
					            label: '二级 3-1',
					            children: [{
					              label: '三级 3-1-1'
					            }]
					          }, {
					            label: '二级 3-2',
					            children: [{
					              label: '三级 3-2-1'
					            }]
					          }]
					        }],
							drawer: false,
							        direction: 'rtl',
					       
				}
			},
			//方法
			methods:{
				test(){
					alert(this.date)
				},
				handleChange(value){
					alert(value)
				},
				handleClick(row){
					console.log(row)
				},
				handleNodeClick(data) {
				        console.log(data);
				      },
			  open() {
					  this.$alert('这是一段内容', '添加', {
						confirmButtonText: '确定',
						callback: action => {
						  alert('ok')
						}
					  });
					}
			},
			//初始化方法
			// created() {
			// 	alert(000)
			// }
		})
	</script>
</html>
```

前端

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
		<div id="app">
			<el-table
			    :data="tableData"
			    stripe
			    style="width: 100%">
			    <el-table-column
			      prop="id"
			      label="编号"
			      width="180">
			    </el-table-column>
			    <el-table-column
			      prop="title"
			      label="标题"
			      width="180">
			    </el-table-column>
			    <el-table-column
			      prop="content"
			      label="内容"
				  width="280">
			    </el-table-column>
				<el-table-column
				  prop="createtime"
				  label="时间"
				  width="180">
				</el-table-column>
				<el-table-column
				  prop="opername"
				  label="操作员">
				</el-table-column>
			    <el-table-column label="操作">
				  <template slot-scope="scope">
					<el-button type="primary" icon="el-icon-plus"
					  @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
					<el-button
					  type="danger"
					  icon="el-icon-folder-delete"
					  @click="handleDelete(scope.$index, scope.row)">删除</el-button>
				  </template>
				</el-table-column>
			  </el-table>
			  <el-pagination style="margin-top: 20px;float: right;"
			  			   background
			  			   layout="prev, pager, next"
			  			   :page-size="pageSize"
			  			   :total="total"
			  			   :current-page.sync="currentPage"
			  			   @current-change="page">
			  </el-pagination>
		</div>
	</head>
	<body>
	</body>
	<script src="js/vue.js"></script>
	<script src="js/elementui.js"></script>
	<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
	<script>
		new Vue({
			el: '#app',
			//数据
			data(){
				return {
					tableData:'',
					pageSize: 2,
					total: '',
					currentPage: 1
				}
			},
			//方法
			methods:{
				page(){
					let _this = this
					axios.get('http://localhost:8080/list/'+this.currentPage+'/'+this.pageSize).then(function (resp) {
						_this.tableData = resp.data.list
						_this.total = resp.data.total
					});
				}
			},
			//初始化方法
			created() {
				let _this = this
				axios.get('http://localhost:8080/list/1/'+this.pageSize).then(function (resp) {
					_this.tableData = resp.data.list
					_this.total = resp.data.total
				});
			}
		})
	</script>
</html>
```

```java
@RestController
public class SysNewsController {

    @Autowired
    private SysNewsService newsService;

    @GetMapping("/list/{page}/{size}")
    public Map list(@PathVariable("page") Integer page, @PathVariable("size") Integer size){
        Page<SysNews> queryPage = new Page<>(page, size);
        Page<SysNews> resultPage = this.newsService.page(queryPage);
        long total = resultPage.getTotal();
        List<SysNews> list = resultPage.getRecords();
        Map<String,Object> map = new HashMap<>();
        map.put("total", total);
        map.put("list", list);
        return map;
    }

}
```