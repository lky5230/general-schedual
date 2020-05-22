# general_schedual

> 基于vue的通用排班核心控件，可自定义多种排班、排班、排期等业务场景

![image](https://github.com/lky5230/general_schedual/blob/master/demo.png)
![image](https://github.com/lky5230/general_schedual/blob/master/demo2.png)

#### JS代码
```javascript
<template>
	<div>
		/**
		* EXAMPLE
		*/
		<rangeControl  
			@colorItemActive="colorItemActive"
			:disabled="false"
			:colorItemTextFn="(curLine,l)=>{
				return {
					text: `<span style='background: #333; color: #fff; padding: 2px'>${curLine.data[l].name}</span>`,
					title: curLine.data[l].name
				}
			}"
			:colorItemTopValueFn="(curLine,l)=>''"
			:tipTextFn="(rangeData, curLine, colorIndex, isLast)=>''"
			ref="rangeControl">
		</rangeControl>
	</div>
</template>

<script>
export default {
	data() {
		return {
			//图形数据，注意数据格式必须是这种
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
				}
				//... vData有多少个，这里就有多少条
			]
		};
	},
	mounted() {
	
		/* 
		* 参数1：图形数据（一般传到服务端完整保存它，前端获取它直接用）
		* 参数2：配置
		*/
		this.$refs.rangeControl.init(this.range, {});
		
		/* 有初始图形数据的初始化
		this.$refs.rangeControl.init(
			[
				{
					start: 0,
					data: [
						{name: '等级1', code: 'level1', len: 1, id: 0, colorIndex: 3},
						{name: '等级2', code: 'level2', len: 1, id: 1, colorIndex: 8},
						{name: '等级3', code: 'level3', len: 1, id: 2, colorIndex: 3}
					]
				}, 
				{
					start: 0,
					data: [
						{name: '等级1', code: 'level1', len: 2, id: 0, colorIndex: 2},
						{name: '等级2', code: 'level2', len: 0, id: 1, colorIndex: 1},
						{name: '等级3', code: 'level3', len: 1, id: 2, colorIndex: 6}
					]
				}, 
			],
			{
				hData: [0,1,2,3,4,5,6,7,8,9],  // 横向时间数据
				vData: ['星期一','星期二'],  // 纵向文本数据
			}
		)
		*/
		
	},
	methods: {
		//点中某个色块
		colorItemActive(..e){}
	}
}
</script>
```

#### 默认配置
```javascript
{
	// 左上角标题
	vhTitle: '时间',
	
	// 横向数据
	hData: ['hData1','hData2','hData3','hData4','hData5','hData6','hData7','hData8','hData9','hData10'],
	
	// 横向数据后缀
	hDataUnit: '',
	
	// 纵向文本数据
	vData: ['vData1','vData2','vData3','vData4','vData5'],
	
	//内置10种颜色序列
	color: ['#71cf73', '#badf5f', '#0adddd', '#5797f0','#ffcb75',  '#e59332', '#ff8040', '#9f5000', '#8826e3', '#f13f31'],
	
	// 分段间隔显示
	rangeCount: 1,
	
	// 每单位像素
	unitWidth: 30,
	
	//允许0.5单位
	isHalf: true,
	
	//隐藏第一个左边的拖拽
	hideFirstLeftDrag: false,
	
	//隐藏全部拖拽
	allHideDrag: false,
	
	// 是否松散型拖拽（拖拽到其他色块时是否停止，还是一起移动）
	looseDrag: true,
	
	//顶部横向标题的显示位置 ( left、 center )
	titleTextAlign: 'left',
	
	//顶部横向最右侧的额外标题（一般用于连续时间末尾显示）
	lastTitle: '',
}
```
#### 色块的数据格式
```javascript
//【色块的数据格式】
    colorItem : {
        // 图形数据相关
        name: colorItem[j].name,
        code: colorItem[j].code, //其中 code="rangeBlank" 表示是空白的占位色块
        len: 0,
        colorIndex: 0,
        //图形色块内部id
        id: 0,

        // extraData字段放入业务数据、额外数据
        extraData: {}
    }
```

#### props
```javascript
	props: {
		disabled: {
		    default: false,
		},
		maxWidth: {
		    default: 'auto',
		}, 
		maxHeight: {
		    default: 'auto',
		},
		/** 每行合并显示的配置
		 * {    //编码
			code_1: { 
			    top: 0,//比例
			    height: 1,//比例
			    zIndex: 1
			},
			code_5: {
			    top: 0,
			    height: 1,
			    zIndex: 1
			},
			code_2: {
			    top: 0.2,
			    height: 0.6,
			    zIndex: 2
			},
			code_4: {
			    top: 0.2,
			    height: 0.6,
			    zIndex: 3
			},
			code_3: {
			    top: 0.2,
			    height: 0.6,
			    zIndex: 3
			},
		    } 
		 * */
		 //若要将每行合并成一行显示，则配置它
		combineAllLine: {
		    default: false,
		},
		//是否显示每行操作列
		showOperationBtn: {
		    default: true,
		},
		//是否显示顶部
		showhDataHead: {
		    default: true,
		},
		//颜色块内部显示文本，其中text可以是html
		colorItemTextFn: {
		    type: Function,
		    //参数（当前行实例，当前色块索引）
		    default: (curLine, curLineIndex)=>{
			return {
			    text: curLine.data[curLineIndex].name,
			    title: curLine.data[curLineIndex].name,
			};
		    }
		},
		//颜色块上面显示值
		colorItemTopValueFn: {
		    type: Function,
		    //参数（当前行实例，当前色块索引）
		    default: (curLine, curLineIndex)=>{
			return curLine.data[curLineIndex].len;
		    }
		},
		//拖拽时，指示线顶部提示文本
		tipTextFn: {
		    type: Function,
		    //参数（图形数据，当前行数，当前色块索引，是否是最后一个拖拽线）
		    default: (rangeData, curLineNum, colorIndex, isLast=false)=>{
			return rangeData[curLineNum].data[colorIndex].left;
		    }
		},
	}
```
#### 主要的实例方法

```javascript
#### 事件
	add（添加色块完成事件，参数是添加完毕后的图形数据）
	remove（删除色块完成事件，参数是删除完毕后的图形数据）
	drag（拖拽色块左右把手的事件，参数是更新后的图形数据）
	colorItemActive（点击色块时触发，参数1：色块所在行，参数2：色块所在列索引）
	wrapMouseOut（鼠标离开整个容器时触发）
	lineMouseOver（每行mouseover时触发，参数：点击的行数）
	lineClick（参数：点击的行数）
```

```javascript

//初始化控件
this.$refs.rangeControl.init(data, opt)

//横轴上插入名称，name表示名称，index表示插入位置，参数3是可选配置
this.$refs.rangeControl.addColorItem(name, index, {
	/**
	 * 图形相关
	 */
	code = '',
	colorIndex = 0, //颜色索引，表示什么颜色
	lineNum = -1, //默认-1，表示插入全部的行，0表示只在第一行插入，1表示只在第2行插入
	defaultLen = 0, //默认色块的长度

	/**
	 * 额外数据
	 */
	extraData = {} //业务数据建议统一放这里
})

//每个横轴上删除位置在index上的名称，参数1是删除项在横轴的的索引，参数2默认-1，表示所有行都删除index项，0表示只删除第一行的index项
this.$refs.rangeControl.removeColorItem(index, lineNum)

//获取颜色序列
this.$refs.rangeControl.getColors()

//获取实时的图形数据
this.$refs.rangeControl.getData()

//获取点中的色块
this.$refs.rangeControl.getActiveColorItem()

//获取实时的配置
this.$refs.rangeControl.getOpt()

//在具有maxHeight时，获取图形上下滚动条的距离
this.$refs.rangeControl.getScroll()
        
```

