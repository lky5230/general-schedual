# general_schedual

> 基于vue的通用连续排班控件

![image](https://github.com/lky5230/general_schedual/blob/master/demo.png)

#### JS代码
```javascript
<template>
	<div>
		<rangeControl ref="rangeControl"></rangeControl>
	</div>
</template>

<script>
export default {
	data() {
		return {
      			rangeModel: [],
			range: [
				{
					start: 0,
					data: []
				},
				{
					start: 0,
					data: []
				},
				{
					start: 0,
					data: []
				},
				{
					start: 0,
					data: []
				},
				{
					start: 0,
					data: []
				},
				{
					start: 0,
					data: []
				},
				{
					start: 0,
					data: []
				},
			],

		};
	},
	mounted() {
	
		/* 
		参数1是图形数据格式（一般传到服务端完整保存它，前端获取它直接用），传null会自动初始化一个空白图形数据格式
		参数2是配置，其中colorItem是连续分段的名称数组（ 必传，形式如 [{name: '', code: ''}] ）
		*/
		this.$refs.rangeControl.init(this.range, {
			colorItem: this.rangeModel
		})
		
		/* 有初始数据的初始化
		this.$refs.rangeControl.init(
			[
				{
					start: 0,
					data: [
						{name: '等级1', code: 'level1', len: 1, id: 0, colorIndex: 0},
						{name: '等级2', code: 'level2', len: 1, id: 1, colorIndex: 1}
					]
				}, 
				{
					start: 0,
					data: [
						{name: '等级1', code: 'level1', len: 2, id: 0, colorIndex: 0},
						{name: '等级2', code: 'level2', len: 0, id: 1, colorIndex: 1}
					]
				}, 
			]
			{
				colorItem: [{name: '等级1', code: 'level1'},{name: '等级2', code: 'level2'}],
				hData: [0,1,2,3,4,5,6,7,8,9],  // 横向时间数据
				vData: ['星期一','星期二'],  // 纵向文本数据
			}
		)
		*/
		
	},
}
</script>
```

#### 默认配置
```javascript
{
	vhTitle: '时间',  // 左上角标题
	hData: [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23],  // 横向连续数据
	hDataUnit: 'h',//横向数据显示的后缀名称
	vData: ['星期一','星期二','星期三','星期四','星期五','星期六','星期日'],  // 纵向文本数据
  
	/* 如 [{name: '等级1', code: 'level1'}] */
	colorItem: [],  
  
	color: ['#71cf73', '#badf5f', '#0adddd', '#5797f0','#ffcb75',  '#e59332', '#ff8040', '#9f5000', '#8826e3', '#f13f31'],  //默认颜色序列
	rangeCount: 2,  // 分段显示的间隔
	unitWidth: 20,  // 每单位宽度
	isHalf: true,  //是否允许拖拽0.5个单位
	hideFirstLeftDrag: false,  //隐藏第一个左边的拖拽
	allHideDrag: false,  //隐藏全部拖拽
	// 是否松散型拖拽，就是拖拽时碰到两边其他颜色块时会不会停止，默认不停止
	looseDrag: true,
}
```

#### 实例方法
```javascript

//初始化控件
this.$refs.rangeControl.init(data, opt)

//每个横轴上插入一个名称，name表示名称，index表示插入位置
this.$refs.rangeControl.addColorItem(name, index)

//每个横轴上删除位置在index上的名称
this.$refs.rangeControl.removeColorItem(index)

//获取颜色序列
this.$refs.rangeControl.getColors()

//获取实时的图形数据
this.$refs.rangeControl.getData()

//返回实时的colorItems，也就是横向颜色块有哪些
this.$refs.rangeControl.getColorItems()
        
```

