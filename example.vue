<template>
<div class="overtimeSet">
	<div class="ket-form">
		<div class="ket-wrap">
			<div class="wrap-title">案例一：工作日加班等级设置</div>
			<div class="wrap-place">
				<div class="place-label">
					加班等级：
				</div>
				<div class="place-content" style="display: block;">
					<div style="margin-bottom:10px">
						<el-checkbox v-model="disabled1">完成编辑</el-checkbox>
					</div>
					<span 
						:style="{
							'color': color[range.colorIndex], 
							'border-color': color[range.colorIndex]
						}"
						class="rangetag" 
						v-for="(range, rangeIndex) in (rangeModel || [])" 
						:key="rangeIndex+'250'">
						{{range.name}}
						<i v-show="!disabled1" title="删除" @click="delRange(rangeIndex)" class="el-icon-close"></i>
					</span>
					<el-popover
						placement="top"
						@after-leave="()=>{
							addRangeName = ''
							colorRgb = ''
						}"
						@show="()=>{
							addRangeIndex = +rangeModel.length + 1
						}"
						v-model="addvisible"
						trigger="click">
						<el-input style="width: 120px;" maxlength="10" v-model="addRangeName" placeholder="添加名称"></el-input>
						<div style="position: relative; display: inline-block">
							<el-select style="width: 90px;" v-model="colorRgb" placeholder="颜色等级">
								<el-option
									v-for="item in colorComp"
									:key="item"
									label="-"
									:value="item">
									<span :style="{width: '80px', height: '40px', display: 'block', background: item}">
									</span>
								</el-option>
							</el-select>
							<div style="position: absolute; left: 10px; top: 10px; width: 40px; height: 12px;" :style="{background: colorRgb}"></div>
						</div>
						<el-select style="width: 120px;" v-model="addRangeIndex" placeholder="插入位置">
							<el-option
								v-for="item in (rangeModel.length*1 + 1)"
								:key="item+'xxx'"
								:label="'放入第'+item+'位'"
								:value="item">
							</el-option>
						</el-select>
						<el-button @click="addRangeConfirm" size="mini" type="primary">确定</el-button>
						<i  v-show="!disabled1" title="增加加班等级" style="color: #1e91e3; cursor: pointer" class="el-icon-plus" slot="reference"></i>
					</el-popover>
                    <div style="margin-top: 10px">
                        <rangeControl 
							:disabled="disabled1"
							:colorItemTextFn="(curLine,l)=>{
								return {
									text: `<span style='color: #fff; padding: 2px 4px; background: #333'>${curLine.data[l].name}</span>`,
									title: curLine.data[l].name,
								}
							}"
							:colorItemTopValueFn="(curLine,l)=>'共'+curLine.data[l].len+'h'"
							@colorItemActive="e=>c(e)"
							:tipTextFn="(rangeData, curLine, colorIndex, isLast)=>{
								if(isLast){
									return rangeData[curLine].data[colorIndex].left + rangeData[curLine].data[colorIndex].len;
								}else{
									return rangeData[curLine].data[colorIndex].left;
								}
							}"
							ref="rangeControl" />
                    </div>
				</div>
			</div>
		</div>




		
	</div>

	
	<div class="ket-form">
		<div class="ket-wrap">
			<div class="wrap-title">案例二：周末补课排班</div>
			<div class="wrap-place">
				<div class="place-label"></div>
				<div class="place-content" style="display: block;">
					<!-- <div style="margin-bottom:10px">
						<el-checkbox v-model="disabled2">完成编辑</el-checkbox>
					</div>
					<el-popover
						placement="top"
						@after-leave="()=>{

						}"
						@show="()=>{
							courseName= ''
							teacher= ''
							courseColorIndex= ''
							addTimeIndex= ''
							addLine= ''
						}"
						v-model="addvisible2"
						trigger="click">
						<el-input style="width: 120px;" maxlength="10" v-model="courseName" placeholder="课程名称"></el-input>
						<el-input style="width: 120px;" maxlength="10" v-model="teacher" placeholder="授课老师"></el-input>
						<div style="position: relative; display: inline-block">
							<el-select style="width: 90px;" v-model="courseColorIndex" placeholder="颜色">
								<el-option
									v-for="(item, index) in color"
									:key="item"
									label="-"
									:value="index">
									<span :style="{width: '80px', height: '40px', display: 'block', background: item}">
									</span>
								</el-option>
							</el-select>
							<div style="position: absolute; left: 10px; top: 10px; width: 40px; height: 12px;" :style="{background: color[courseColorIndex]}"></div>
						</div>
						<el-select style="width: 120px;" v-model="addTimeIndex" placeholder="上课时间">
							<el-option
								v-for="(item, index) in hData"
								:key="item+'xxx'"
								:label="item"
								:value="index">
							</el-option>
						</el-select>
						<el-select style="width: 120px;" v-model="addLine" placeholder="星期">
							<el-option
								v-for="(item, index) in vData"
								:key="item+'xxx'"
								:label="item"
								:value="index">
							</el-option>
						</el-select>
						<el-button @click="addCourseConfirm" size="mini" type="primary">确定</el-button>
						<i v-show="!disabled2" title="增加课程" style="color: #1e91e3; cursor: pointer" class="el-icon-plus" slot="reference"></i>
					</el-popover> -->
                    <div style="margin-top: 10px">
                        <rangeControl 
							:disabled="true"
							:colorItemTextFn="(curLine,l)=>{
								return {
									text: curLine.data[l].name,
									title: curLine.data[l].name,
								}
							}"
							:colorItemTopValueFn="(curLine,l)=>{
								if(curLine.data[l].extraData){
									return curLine.data[l].extraData.teacher
								}else{
									return ''
								}
							}"
							@colorItemActive="e=>c(e)"
							:tipTextFn="(rangeData, curLine, colorIndex, isLast)=>''"
							ref="rangeControl2" />
                    </div>
				</div>
			</div>
		</div>




		
	</div>
	
</div>
</template>

<script>
export default {
	name: "example",
	computed: {
		colorComp(){
			var colors = [];
			for(var i=0; i<this.color.length; i++){
				var chunzai = false;
				for(var j=0; j<this.rangeModel.length; j++){
					if(this.rangeModel[j].colorIndex == i){
						chunzai = true;
						break ;
					}
				}
				if(!chunzai){
					colors.push(this.color[i])
				}
			}
			return colors;
		},


	},
	data() {
		return {
			// 加班等级-初始化数据
			disabled1: false,
			color: [],
			rangeModel: [],
			range: [
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 5},{name:'等级2',code:2,id:2,colorIndex:2,len: 5},{name:'等级3',code:3,id:3,colorIndex:3,len: 5},{name:'等级4',code:4,id:4,colorIndex:4,len: 4}]
				},
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 5},{name:'等级2',code:2,id:2,colorIndex:2,len: 5},{name:'等级3',code:3,id:3,colorIndex:3,len: 5},{name:'等级4',code:4,id:4,colorIndex:4,len: 4}]
				},
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 5},{name:'等级2',code:2,id:2,colorIndex:2,len: 5},{name:'等级3',code:3,id:3,colorIndex:3,len: 5},{name:'等级4',code:4,id:4,colorIndex:4,len: 5}]
				},
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 5},{name:'等级2',code:2,id:2,colorIndex:2,len: 5},{name:'等级3',code:3,id:3,colorIndex:3,len: 5},{name:'等级4',code:4,id:4,colorIndex:4,len: 5}]
				},
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 4},{name:'等级2',code:2,id:2,colorIndex:2,len: 4},{name:'等级3',code:3,id:3,colorIndex:3,len: 4},{name:'等级4',code:4,id:4,colorIndex:4,len: 5}]
				},
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 0},{name:'等级2',code:2,id:2,colorIndex:2,len: 4},{name:'等级3',code:3,id:3,colorIndex:3,len: 4},{name:'等级4',code:4,id:4,colorIndex:4,len: 5}]
				},
				{
					start: 0,
					data: [{name:'等级1',code:1,id:1,colorIndex:1,len: 0},{name:'等级2',code:2,id:2,colorIndex:2,len: 0},{name:'等级3',code:3,id:3,colorIndex:3,len: 0},{name:'等级4',code:4,id:4,colorIndex:4,len: 0}]
				},
				
			],
			colorRgb: '',
			addRangeIndex: '',
			addRangeName: '',
			addvisible: false,

			// 上课时间
			range2: [
				{
					start: 0,
					data: [{name: '语文',len:1,colorIndex:3, extraData:{teacher: '网民'}},{name: '数学',len:1,colorIndex:4, extraData:{teacher: '小王'}},{name: '英语',len:1,colorIndex:5, extraData:{teacher: 'joke'}},{name: '',len:1,code: 'rangeBlank',colorIndex:6},{name: '体育',len:1,colorIndex:6, extraData:{teacher: '强子'}}]
				},
				{
					start: 0,
					data: [{name: '语文',len:1,colorIndex:3, extraData:{teacher: '网民'}},{name: '语文',len:1,colorIndex:3, extraData:{teacher: '网民'}},{name: '数学',len:1,colorIndex:4},{name: '另外安排',len:2,colorIndex:10, extraData:{teacher: ''}}]
				},
			],
			vData: ['星期六','星期日'],
			hData: ['8:30-9:30','10:30-11:30','13:00-14:00','14:30-15:30','16:00-17:00'],
			disabled2: false,
			addvisible2: false, 
			courseName: '',
			teacher: '', 
			courseColorIndex: '', 
			addTimeIndex: '', 
			addLine: '', 
			

		};
	},
	mounted() {
		this.$nextTick(async () => {
            this.$store.commit("pageLoadingFn", false);
            this.$refs.rangeControl.init(this.range, {
				hideFirstLeftDrag: true,
				allHideDrag: false,
				vData: ['星期一','星期二','星期三','星期四','星期五','星期六','星期日'],
				hData: [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23],
				unitWidth: 30,
				hDataUnit: 'h',
				vhTitle: '时间',
				rangeCount: 2,
				titleTextAlign: 'left',
				lastTitle: '24',
			})
			this.color = this.$refs.rangeControl.getColors();
			this.rangeModel = this.$refs.rangeControl.getData()[0].data;

			this.$refs.rangeControl2.init(this.range2, {
				hideFirstLeftDrag: false,
				allHideDrag: false,
				vData: this.vData,
				hData: this.hData,
				unitWidth: 120,
				hDataUnit: '',
				vhTitle: '上课时间',
				rangeCount: 1,
				isHalf: false,
				titleTextAlign: 'center',
				lastTitle: '',
			})


		});
	},
	methods: {
		c(...e){console.log(e)},
		addRangeConfirm(){
			if(!this.addRangeName){
				return this.$message.warning('请输入名称')
			}
			if(this.colorRgb === ''){
				return this.$message.warning('请选择颜色')
			}
			var cindex = '';
			for(var i=0; i<this.color.length; i++){
				if(this.color[i] == this.colorRgb){
					cindex = i;
					break ;
				}
			}
			this.$refs.rangeControl.addColorItem(
				this.addRangeName, 
				this.addRangeIndex, 
			{
				colorIndex: cindex,
				defaultLen: 1,
			});
			this.rangeModel = this.$refs.rangeControl.getData()[0].data;
			this.addvisible = false
		},
		delRange(rangeIndex){
			this.confirmDialog('确认删除该项？').then(()=>{
				this.$refs.rangeControl.removeColorItem(rangeIndex)
				this.rangeModel = this.$refs.rangeControl.getData()[0].data;
			});
		},


		addCourseConfirm(){
			this.$refs.rangeControl2.addColorItem(
				this.courseName, 
				this.addTimeIndex, 
				{
					code: '',
					colorIndex: this.courseColorIndex, //颜色索引，表示什么颜色
					lineNum: this.addLine, //默认-1，表示插入全部的行
					defaultLen: 1, //默认色块的长度
					extraData: {
						teacher: this.teacher,
					}
				}
			)
			this.addvisible2 = false;
		},


	}
};
</script>

<style lang="less" scoped>
.rangetag{
	display: inline-block;
	font-size: 14px;
	padding: 4px 6px;
	border: 1px solid #ddd;
	margin-right: 10px;
	i{
		font-size: 12px;
		cursor: pointer;
		display: inline-block;
		margin-left: 5px;
	}
}
</style>
