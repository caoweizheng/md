## elementUI table添加合计之后fixed固定栏，滚动条出现挡住，无法滚动

 ```

	.el-table__fixed {
	  height: 100% !important;
	}
	.el-table__body-wrapper{
	z-index: 2;
	}

	// this.$nextTick(()=>{
    //   this.$refs['multiline-table'].doLayout()
    // })


```