# 前端开发


## 新建数据模型
1）	切换到iUAP开发视图，工程上右键选择新建下的新建数据模型
 
2）	输入文件名为product.model，点击下一步
 
3）选择实体类为Product，点击下一步
 
4）使用默认的UTF-8编码，点击完成
 
5）	工具生成数据模型文件，如下图
 
## 新建iuap页面

1）	在webapp的pages下右键新建iuap页面，点击下一步
 
 
也可以切换到iUAP开发视图，在webapp下的pages目录右键，选择新建下的iUAP页面
 
2）	输入页面目录名称为product，勾选使用数据模型，点击下一步
 
3）选择数据模型为Product，勾选右下角的选择，点击完成
 
4）工具为用户生成功能模块的js文件和html片段，所在位置如下图
 
## 调整模块的js文件

1）	定义js前端的datatable数据模型，修完成改后如下

```
mainDataTable: new u.DataTable({
	meta: {
		'productid':{
			type:'string'
		},
		'productname': {
			type: 'string'
		},
		'productnum': {
			type: 'integer'
		},
		'price': {
			type: 'float'
		},
		'supplier': {
			type: 'string'
		}, //供应商
		'prodate': {
			type: 'date'
		}, //生成日期
		'orgin': {
			type: 'string'
		} //原产地
	},
	pageSize: 10
})
```

2）	定义事件方法，如查询、添加、返回等，例如删除方法：

```
delRow: function() {
	var selectArray = viewModel.mainDataTable.selectedIndices();
	if (selectArray.length < 1) {
		u.messageDialog({
			msg: "请选择要删除的行!",
			title: "提示",
			btnText: "OK"
		});
		return;
	}
	u.confirmDialog({
	    msg: "是否确认删除？",
	    title: "测试确认",
	    onOk: function () {
		$.ajax({
			url:ctrlBathPath+'/del',
			type:'post',
			data:{data:JSON.stringify(viewModel.mainDataTable.getSimpleData({type:'select',fields:['productid']}))},
			success: function(){
				u.showMessage('删除成功！');
				viewModel.events.queryMain();
			}
		});
	    },
	    onCancel: function () {
		
	    }
	});
}
```

3）	对应html片段，绑定事件，完整示例product.js代码如下：

```
define(['jquery', 'knockout', 'text!pages/product/product.html', 'uui'], function($, ko, template) {

	var ctrlBathPath = ctx+'/product';
	var app, viewModel, datas;
	var viewModel = {
		md: document.querySelector('#demo-mdlayout'),
		editoradd: '',
		searchText: ko.observable(''),
		mainDataTable: new u.DataTable({
			meta: {
				'productid':{
					type:'string'
				},
				'productname': {
					type: 'string'
				},
				'productnum': {
					type: 'integer'
				},
				'price': {
					type: 'float'
				},
				'supplier': {
					type: 'string'
				}, //供应商
				'prodate': {
					type: 'date'
				}, //生成日期
				'orgin': {
					type: 'string'
				} //原产地
			},
			pageSize: 10
		}),
		infodata: new u.DataTable({
			meta: {
				'productid':{
					type:'string'
				},
				'productname': {
					type: 'string'
				},
				'productnum': {
					type: 'integer',
					precision: 0,
					min: 0
				},
				'price': {
					type: 'float',
					precision: 2,
					min: 0
				},
				'supplier': {
					//供应商
					type: 'string'
				},
				'prodate': {
					//生成日期
					type: 'date'
				}, 
				'orgin': {
					//原产地
					type: 'string'
				} 
			}
		}),
		
		events: {
			//查询主数据
			queryMain: function(){
				var queryData = {};
				var searchValue = viewModel.searchText();
				var key = 'search_LIKE_productname';
				queryData[key] = searchValue;
		        
		        queryData["pageIndex"] = viewModel.mainDataTable.pageIndex();
		        queryData["pageSize"] = viewModel.mainDataTable.pageSize();
				$.ajax({
					type : 'GET',
					url : ctrlBathPath+'/page',
					data : queryData,
					dataType : 'json',
					success : function(result) {
						var data = result.data;
						if(data!=null){
							viewModel.mainDataTable.setSimpleData(data.content);
							viewModel.mainDataTable.totalPages(data.totalPages);
						} else {
							
						}
					}
				});
			},
			//搜索
			search: function(){
				this.events.queryMain();
			},
			searchKeyUp: function(model,event){
				if (event.keyCode == '13'){
					if(u.isIE){
						event.target.blur();
						this.events.queryMain();
						event.target.focus();
					} else {
						this.events.queryMain();
					}
				}
				return true;
			},
			afterAdd:function(element, index, data){
	            if (element.nodeType === 1) {
	                u.compMgr.updateComp(element);
	            }
	        },
			goBack: function() {
				viewModel.md['u.MDLayout'].dBack();
			},
			goPage: function(pathStr) {
				viewModel.md['u.MDLayout'].dGo(pathStr);
			},
			beforeAdd: function() {
				viewModel.editoradd = 'add';
				viewModel.infodata.clear();
				viewModel.infodata.createEmptyRow();
				viewModel.infodata.setRowSelect(0);
				viewModel.md['u.MDLayout'].dGo('addPage');
			},
			addOrEditRow: function() {
				var self = this;
				var _meta = this.mainDataTable.meta;
				var addInfo = this.infodata.getAllRows();
				var url = '';
				var postData = {};
				if (this.editoradd === 'add') {
					url = ctrlBathPath+'/save';
					postData = JSON.stringify(this.infodata.getSimpleData()[0]);
				} else {
					url = ctrlBathPath+'/update';
					postData = JSON.stringify(self.infodata.getSimpleData({type:'select'})[0]);
				}
				$.ajax({
					url:url,
					type:'POST',
					contentType: 'application/json',
					data:postData,
					success:function(res){
						if (res.flag == 'success'){
							if (self.editoradd === 'add'){
								self.mainDataTable.addSimpleData(res.data);
							}else{
								var curRow = viewModel.mainDataTable.getCurrentRow();
								curRow.setSimpleData(viewModel.infodata.getCurrentRow().getSimpleData(), 'upd');

							}
							viewModel.md['u.MDLayout'].dBack();
							u.showMessage('保存成功！');								
						}else{
							u.showMessageDialog(res.msg);
						}
					},
					error:function(){
						//待显示错误信息
					}
				});
				
			},
			
			beforeEdit: function(id) {
				viewModel.mainDataTable.setRowSelect(id);
				viewModel.infodata.setSimpleData(viewModel.mainDataTable.getSimpleData({
					type: 'select'
				}));
				viewModel.editoradd = 'edit';
				viewModel.md['u.MDLayout'].dGo('addPage');
			},
			
			delRow: function() {
				var selectArray = viewModel.mainDataTable.selectedIndices();
				if (selectArray.length < 1) {
					u.messageDialog({
						msg: "请选择要删除的行!",
						title: "提示",
						btnText: "OK"
					});
					return;
				}
		        u.confirmDialog({
		            msg: "是否确认删除？",
		            title: "测试确认",
		            onOk: function () {
		                $.ajax({
		                	url:ctrlBathPath+'/del',
		                	type:'post',
		                	data:{data:JSON.stringify(viewModel.mainDataTable.getSimpleData({type:'select',fields:['productid']}))},
		                	success: function(){
		                		u.showMessage('删除成功！');
		                		viewModel.events.queryMain();
		                	}
		                });
		            },
		            onCancel: function () {
		                
		            }
		        });
			},
			viewRow: function(id) {
				//如果样式列表不含有checkbox说明不是第一列
				if (event.target.classList.toString().indexOf('checkbox') < 0) { 
					viewModel.mainDataTable.setRowFocus(id);
					viewModel.md['u.MDLayout'].dGo('showPage');
				}
			},
			pageChangeFunc: function(index){
				viewModel.mainDataTable.pageIndex(index);
				viewModel.events.queryMain();		
			},
			sizeChangeFunc: function(size){
				viewModel.mainDataTable.pageSize(size);
				viewModel.events.queryMain();
			}
		}
	};

	var init = function(params) {
		var app = u.createApp({
			el: '#content',
			model: viewModel
		});
		viewModel.md = document.querySelector('#demo-mdlayout');
	    viewModel.events.queryMain();
	};

	return {
		'model': viewModel,
		'template': template,
		'init': init
	};
});
```



