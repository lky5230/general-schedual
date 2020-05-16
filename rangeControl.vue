<template>
	<div :key="rangeKey" class="week-range-wrap">

        <div class="title-text" :class="{opac: opacityTitle}">
            <div class="title-v0">{{opt.vhTitle}}</div>
            <div 
                class="title-data" 
                :style="{width: opt.unitWidth + 'px'}" 
                v-for="(hItem, hIndex) in hDataComp"
                :key="hIndex+'title-vvv'">
                <span class="span-text" v-show="hIndex % (opt.rangeCount || 1)==0">
                    {{hItem}}{{opt.hDataUnit}}
                </span>
                <!-- 最后一个文本 -->
                <span class="span-last" v-if="hDataComp.length - 1 == hIndex">
                    {{hDataComp[hIndex]*1 + 1}}{{opt.hDataUnit}}
                </span>
            </div>
        </div>

        <div style="position: relative;">
            <div class="week-wrap" v-for="line in vDataComp.length" :key="line+'vDataComp'">
                <div class="title-v nowrap">

                    <span :title="vDataComp[line - 1]">{{vDataComp[line - 1]}}</span>
                    <div class="operation-btn">
                        <div class="operation">
                            <!-- 复制到上一行 -->
                            <div class="copy-up-icon" v-show="line != 1" @click="copyToPrev(line-1)" title="复制到上一行">
                                <i class="el-icon-top"></i>
                            </div>
                            
                            <!-- 恢复按钮 -->
                            <div class="reset-icon" @click="resetLine(line-1)" title="恢复">
                                <i class="el-icon-refresh-left"></i>
                            </div>

                            <!-- 复制到下一行 -->
                            <div class="copy-down-icon" v-show="line != res.length" @click="copyToNext(line-1)" title="复制到下一行">
                                <i class="el-icon-bottom"></i>
                            </div>
                        </div>
                    </div>
                    

                </div>
                <div :ref="'wrap' + line" class="title-data2-wrap">

                    <!-- 背景格子 -->
                    <div
                        class="title-data2" 
                        :class="{
                            last: hIndex == hDataComp.length - 1
                        }"
                        :style="{width: opt.unitWidth + 'px'}" 
                        v-for="(hItem, hIndex) in hDataComp"
                        :key="hIndex+'hDataComp'">
                    </div>

                    <!-- 颜色格子 -->
                    <template v-for="(colorItem, colorIndex)  in colorItemComp">
                        <div
                            class="colorItem"
                            v-if="res[line - 1].data[colorIndex]"
                            :title="res[line - 1].data[colorIndex].name+'：'+res[line - 1].data[colorIndex].len"
                            :style="{
                                width: res[line - 1].data[colorIndex].len * opt.unitWidth + 'px',
                                left: res[line - 1].data[colorIndex].left * opt.unitWidth + 'px',
                                background: opt.color[res[line - 1].data[colorIndex].index]
                            }"
                            :class="{
                                last: colorIndex == colorItemComp.length-1 && colorItemComp.length > 1,
                                first: colorIndex == 0 && colorItemComp.length > 1
                            }"
                            :key="colorItem + colorIndex + 'colorItemComp250'">
                            <span class="tip" :style="{color: opt.color[res[line - 1].data[colorIndex].index]}">{{res[line - 1].data[colorIndex].len}}</span>
                            <span class="text nowrap">{{res[line - 1].data[colorIndex].name}}</span>
                        </div>
                    </template>

                    <!-- 拖拽线条 -->
                    <template v-for="(colorItem, colorIndex)  in colorItemComp">
                         <div
                            v-show="
                                !opt.allHideDrag
                                &&
                                (colorIndex == 0? !opt.hideFirstLeftDrag: true)
                            "
                            v-if="res[line - 1].data[colorIndex]"
                            :key="colorItem + colorIndex + '-line250'"
                            class="right-drag"
                            :ref="'line-'+(line-1)+colorIndex"
                            @mousedown.stop.prevent="
                                drag(
                                    line - 1, 
                                    colorIndex,
                                    $refs['wrap' + line],
                                    $refs['line-'+(line-1)+colorIndex],
                                    $event
                                )
                            "
                            :style="{
                                'z-index': 250 + colorIndex,
                                left: res[line - 1].data[colorIndex].left * opt.unitWidth - 5 + 'px',
                                height: 40/(fixDragLine['line_'+(line-1)]['colorIndex_'+colorIndex+'_repeat'].repeat) + 'px',
                                top: (40*(fixDragLine['line_'+(line-1)]['colorIndex_'+colorIndex+'_repeat'].topIndex)/(fixDragLine['line_'+(line-1)]['colorIndex_'+colorIndex+'_repeat'].repeat)) + 'px',
                            }">

                            <span 
                                v-show="fixDragLine['line_'+(line-1)]['colorIndex_'+colorIndex+'_repeat'].repeat != 1" 
                                class="red-btn"
                                :style="{
                                    background: opt.color[colorIndex],
                                    border: '1px solid #fff'
                                }">
                            </span>

                            <span :style="{background: opt.color[colorIndex]}" class="drag-line"></span>

                        </div>
                    </template>


                    <!-- 最后一根、拖拽线条 -->
                    <template v-if="res[line - 1].data[colorItemComp.length - 1]">
                        <div class="right-drag"
                            :ref="'line'+line+'-last'"
                            v-show="!opt.allHideDrag"
                            @mousedown.stop.prevent="
                                dragLast(
                                    line - 1, 
                                    colorItemComp.length - 1,
                                    $refs['wrap' + line],
                                    $refs['line'+line+'-last'],
                                    $event
                                )
                            "
                            :style="{
                                'z-index': 250 + 100,
                                left: (+res[line - 1].data[colorItemComp.length - 1].left + +res[line - 1].data[colorItemComp.length - 1].len) * opt.unitWidth - 5 + 'px',
                                height: 40/(fixDragLine['line_'+(line-1)]['colorIndex_last_repeat'].repeat) + 'px',
                                top: (40*(fixDragLine['line_'+(line-1)]['colorIndex_last_repeat'].topIndex)/(fixDragLine['line_'+(line-1)]['colorIndex_last_repeat'].repeat)) + 'px',
                            }">

                            <span 
                                v-show="fixDragLine['line_'+(line-1)]['colorIndex_last_repeat'].repeat != 1"
                                :style="{
                                    background: '#dedede',
                                    border: '1px solid #fff'
                                }" 
                                class="red-btn">
                            </span>

                            <span style="background: #fff" class="drag-line"></span>

                        </div>
                    </template>
                </div>
            </div> 
            <!-- 垂直时间指示线 -->
            <div class="v-tip-line" :style="{left: tipLineLeft+'px'}" v-show="opacityTitle">
                <span>{{tipLineData}}{{opt.hDataUnit}}</span>
            </div>
        </div>

	</div>
</template>

<script>
export default {
    name: "rangeControl",
	data() {
		return {
            // 垂直指示线
            opacityTitle: false,
            tipLineLeft: 0,
            tipLineData: 0,

            rangeKey: 0,
            opt: {},
            // 原始数据
            _res: [],
            // 原始name索引
            _colorItemIndex: [],
            //
            res: [],
		};
    },
    computed: {
        hDataComp(){
            var d = this.opt.hData || []
            return d;
        },
        vDataComp(){
            var d = this.opt.vData || []
            return d;
        },
        colorItemComp(){
            var d = this.opt.colorItem || []
            return d;
        },
        //拖拽线合并处理
        fixDragLine(){
            var res = this.jsonClone(this.res);
            var _t250 = this.opt.colorItem || [];
            var colorItemComp = this.jsonClone(_t250);
            var dragLineMap = {};
            for(var i=0; i<res.length; i++){
                dragLineMap['line_'+i] = {};
                var lastRepeat = 1, lastTopIndex = 0;
                for(var j=0; j<res[i].data.length; j++){
                    dragLineMap['line_'+i]['colorIndex_'+j+'_repeat'] = {
                        repeat: 1, 
                        topIndex: 0
                    };
                    var line_col = dragLineMap['line_'+i]['colorIndex_'+j+'_repeat'];
                    var repeatArr = [j];
                    for(var k=0; k<res[i].data.length; k++){
                        if(k != j && (res[i].data[k].left*10).toFixed(0) == (res[i].data[j].left*10).toFixed(0)){
                            line_col.repeat = line_col.repeat + 1;
                            repeatArr.push(k);
                        }
                    }
                    repeatArr.sort();
                    // 看repeatArr最大拖拽线和最右拖拽线是否重合
                    if(this.colorItemComp.length != 0 && repeatArr.includes(res[i].data.length - 1)){
                        var colRightIndex = repeatArr[repeatArr.length - 1];
                        if(res[i].data[colRightIndex].len == 0){
                            line_col.repeat = line_col.repeat + 1;
                            lastRepeat = line_col.repeat;
                            repeatArr.push(999999999);
                            lastTopIndex = repeatArr.length - 1;
                        }
                    }
                    for(var m=0; m<repeatArr.length; m++){
                        if(repeatArr[m] == j){
                            line_col.topIndex = m;
                            break ;
                        }
                    }
                }
                if(this.colorItemComp.length != 0){
                    dragLineMap['line_'+i]['colorIndex_last_repeat'] = {
                        repeat: lastRepeat, 
                        topIndex: lastTopIndex
                    }
                }
            }
            return dragLineMap;
        },
    },
	mounted() {
		this.$nextTick(async () => {
            
		});
	},
	methods: {
        jsonClone(d){
            return JSON.parse(JSON.stringify(d))
        },
        initRes(){
            var res = [];
            for(var i=0; i<this.vDataComp.length; i++){
                var colorItem = this.colorItemComp;
                var colorItemLength = colorItem.length;
                res[i] = {start: 0, data: []};
                for(var j=0; j<colorItemLength; j++){
                    res[i].data[j] = {
                        name: colorItem[j],
                        len: 0,
                        index: j,
                    };
                };
            };
            return res;
        },
        setDragLeftPos(data){
            var d = this.jsonClone(data);
            for(var i=0; i<d.length; i++){
                for(var j=0; j<d[i].data.length; j++){
                    var fullLeft = +d[i].start;
                    var sumLeft = 0;
                    for(var k=0; k<j; k++){
                        sumLeft += +d[i].data[k].len;
                    }
                    d[i].data[j].left = fullLeft + sumLeft;
                }
            };
            return d;
        },
		init(data, options){
            var defaultOpt = {
                // 左上角标题
                vhTitle: '时间',
                // 横向时间数据
                hData: [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23],
                hDataUnit: 'h',
                // 纵向文本数据
                vData: ['星期一','星期二','星期三','星期四','星期五','星期六','星期日'],
                // 需要彩色分段的名称，并反映顺序关系
                colorItem: [],
                color: ['#71cf73', '#badf5f', '#0adddd', '#5797f0','#ffcb75',  '#e59332', '#ff8040', '#9f5000', '#8826e3', '#f13f31'],
                // 分段间隔显示
                rangeCount: 2,
                // 每单位像素
                unitWidth: 20,
                //允许0.5单位
                isHalf: true,
                //隐藏第一个左边的拖拽
                hideFirstLeftDrag: false,
                //隐藏全部拖拽
				allHideDrag: false,
            };
            if(options){
                var opt = this.jsonClone(options)
                this.opt = {...defaultOpt, ...opt}
            }else{
                this.opt = defaultOpt;
            };
            //设置索引，用于颜色、顺序
            for(var i=0; i<data.length; i++){
                for(var j=0; j<data[i].data.length; j++){
                    var c = data[i].data[j];
                    if(c.index == undefined){
                        c.index = j;
                    }
                }
            }
            if(data){
                this.res = this.setDragLeftPos(this.jsonClone(data));
            }else{
                this.res = this.setDragLeftPos(this.initRes());
            }

            this._res = this.jsonClone(this.res)
            this._colorItemIndex = this.jsonClone(this.opt.colorItem.map((item, index)=>{
                return {
                    name: item,
                    index: index,
                }
            }))

            this.rangeKey = Date.now();
        },
        //添加一项，返回添加是否成功
        addColorItem(name, index){
            var res = this.jsonClone(this.res);
            var insertStatus = true;
            for(var i=0; i<res.length; i++){
                for(var j=0; j<res[i].data.length; j++){
                    if(res[i].data[j].name == name){
                        insertStatus = false;
                        break ;
                    }
                }    
            };
            if(insertStatus == false) return false;
            //插入该项
            this.opt.colorItem.splice(index-1, 0, name);
            for(var i=0; i<res.length; i++){
                res[i].data.splice(index-1, 0, {
                    name,
                    len: 1,
                })
            };
            //重新设置索引，用于颜色及顺序
            for(var i=0; i<res.length; i++){
                for(var j=0; j<res[i].data.length; j++){
                    var c = res[i].data[j];
                    c.index = j;
                }
            };
            //设置位置
            this.res = this.setDragLeftPos(res);
            //重置原始记录
            this._res = this.jsonClone(this.res)
            this._colorItemIndex = this.jsonClone(this.opt.colorItem.map((item, index)=>{
                return {
                    name: item,
                    index: index,
                }
            }))
            return true;
        },
        //删除一项，返回删除是否成功
        removeColorItem(name, index){
            var res = this.jsonClone(this.res);
            //插入该项
            this.opt.colorItem.splice(index, 1);
            for(var i=0; i<res.length; i++){
                res[i].data.splice(index, 1)
            };
            //重新设置索引，用于颜色及顺序
            for(var i=0; i<res.length; i++){
                for(var j=0; j<res[i].data.length; j++){
                    var c = res[i].data[j];
                    c.index = j;
                }
            };
            //设置位置
            this.res = this.setDragLeftPos(res);
            //重置原始记录
            this._res = this.jsonClone(this.res)
            this._colorItemIndex = this.jsonClone(this.opt.colorItem.map((item, index)=>{
                return {
                    name: item,
                    index: index,
                }
            }))
        },
        drag(dataLine, colorIndex, wrapEl, lineEl, e){
            var res2 = this.jsonClone(this.res)
            if(wrapEl instanceof Array) wrapEl = wrapEl[0];
            if(lineEl instanceof Array) lineEl = lineEl[0];
            const initClientX = e.clientX;
            const wrapWidth = wrapEl.offsetWidth;
            let x = null;
            //指示线
            this.opacityTitle = true;
            this.tipLineLeft = +lineEl.offsetLeft + 84;
            this.tipLineData = res2[dataLine].data[colorIndex].left;
            document.onmousemove = e2 => {
                var res = this.jsonClone(res2)
                e2.preventDefault();
                x = e2.clientX - initClientX;
                var disCount;
                if(this.opt.isHalf){
                    var count = (x/this.opt.unitWidth).toFixed(1);
                    var zhenshu = (count*1).toFixed(0);
                    if(count - zhenshu >= 0.5){
                        disCount = +zhenshu + 1
                    }else if(count - zhenshu >= 0.1){
                        disCount = +zhenshu + 0.5
                    }else{
                        disCount = +zhenshu
                    }
                }else{
                    disCount = Math.round(x/this.opt.unitWidth);
                }
                if(colorIndex == 0){
                    //第1个
                    var start = res[dataLine].start;
                    var len = res[dataLine].data[colorIndex].len;
                    var new_start = +start + +disCount;
                    var new_len = +len - +disCount;
                    if(new_start < 0){
                        //小于0
                        res[dataLine].start = 0;
                        res[dataLine].data[colorIndex].len = +len + +start;
                    }else if(new_start > +start + +len){
                        //大于加len
                        res[dataLine].start = +start + +len;
                        res[dataLine].data[colorIndex].len = 0;
                    }else{
                        res[dataLine].start = new_start;
                        res[dataLine].data[colorIndex].len = new_len;
                    }
                }else{
                    //不是第1个
                    var start = res[dataLine].start;
                    var prev_len = res[dataLine].data[colorIndex - 1].len;
                    var len = res[dataLine].data[colorIndex].len;
                    var prev_new_len = +prev_len + +disCount;
                    var new_len = +len - +disCount;
                    if(prev_new_len < 0){
                        res[dataLine].data[colorIndex - 1].len = 0;
                        res[dataLine].data[colorIndex].len = +prev_len + +len;
                    }else if(new_len < 0){
                        res[dataLine].data[colorIndex - 1].len = +prev_len + +len;
                        res[dataLine].data[colorIndex].len = 0;
                    }else{
                        res[dataLine].data[colorIndex - 1].len = prev_new_len;
                        res[dataLine].data[colorIndex].len = new_len;
                    }
                }
                this.res = this.setDragLeftPos(res);
                //指示线
                this.tipLineLeft = +lineEl.offsetLeft + 84;
                this.tipLineData = +this.res[dataLine].data[colorIndex].left
            };
            document.onmouseup = e2 => {
                e2.preventDefault();
                this.opacityTitle = false;
                document.onmousemove = null;
                document.onmouseup = null;
            };  
        },
        dragLast(dataLine, colorLength, wrapEl, lineEl, e){
            var res2 = this.jsonClone(this.res)
            if(wrapEl instanceof Array) wrapEl = wrapEl[0];
            if(lineEl instanceof Array) lineEl = lineEl[0];
            const initClientX = e.clientX;
            const wrapWidth = wrapEl.offsetWidth;
            let x = null;
            //指示线
            this.opacityTitle = true;
            this.tipLineLeft = +lineEl.offsetLeft + 84;
            this.tipLineData = +res2[dataLine].data[colorLength].left + +res2[dataLine].data[colorLength].len;
            document.onmousemove = e2 => {
                var res = this.jsonClone(res2)
                e2.preventDefault();
                x = e2.clientX - initClientX;
                
                var disCount;
                if(this.opt.isHalf){
                    var count = (x/this.opt.unitWidth).toFixed(1);
                    var zhenshu = (count*1).toFixed(0);
                    if(count - zhenshu >= 0.5){
                        disCount = +zhenshu + 1
                    }else if(count - zhenshu >= 0.1){
                        disCount = +zhenshu + 0.5
                    }else{
                        disCount = +zhenshu
                    }
                }else{
                    disCount = Math.round(x/this.opt.unitWidth);
                }
                var start = res[dataLine].start;
                var cur_len = res[dataLine].data[colorLength].len;
                var cur_left = res[dataLine].data[colorLength].left;
                var full_len = +cur_len + +cur_left + +disCount;
                if(full_len > +this.opt.hData[this.opt.hData.length - 1] + 1){
                    res[dataLine].data[colorLength].len = this.opt.hData[+this.opt.hData.length - 1] + 1 - cur_left;
                }else if(+cur_len + +disCount < 0){
                    res[dataLine].data[colorLength].len = 0;
                }else{
                    res[dataLine].data[colorLength].len = +cur_len + +disCount;
                }
                
                this.res = this.setDragLeftPos(res);
                //指示线
                this.tipLineLeft = +lineEl.offsetLeft + 84;
                this.tipLineData = +this.res[dataLine].data[colorLength].left + +this.res[dataLine].data[colorLength].len;
            };
            document.onmouseup = e2 => {
                e2.preventDefault();
                this.opacityTitle = false;
                document.onmousemove = null;
                document.onmouseup = null;
            }; 
        },
        resetLine(line){
            var res = this.jsonClone(this.res);
            res[line] = this._res[line];
            this.res = res;
        },
        copyToNext(curLine){
            var res = this.jsonClone(this.res);
            res[curLine + 1] = res[curLine];
            this.res = res;
        },
        copyToPrev(curLine){
            var res = this.jsonClone(this.res);
            res[curLine - 1] = res[curLine];
            this.res = res;
        },
        getData(){
            return this.jsonClone(this.res)
        },
        getColors(){
            return this.opt.color;
        },
        getColorItems(){
            var d = this.jsonClone(this.opt.colorItem);
            return d;
        },
        getIndexByName(name){
            for(var i=0; i<this._colorItemIndex.length; i++){
                if(this._colorItemIndex[i].name == name){
                    return this._colorItemIndex[i].index;
                }
            }
        },
        //显示隐藏某些颜色段，但是不改变 colorItem
        showSomeNames(names){
            var res = this.jsonClone(this.res);
            for(var i=0; i<res.length; i++){
                for(var j=res[i].data.length-1; j>=0; j--){
                    var exist_name = false;
                    for(var k=0; k<names.length; k++){
                        if(names[k] == res[i].data[j].name){
                            exist_name = true;
                            break ;
                        }
                    }
                    if(exist_name){

                    }else{
                        res[i].data.splice(j, 1)
                    }
                }
                for(var j=0; j<names.length; j++){
                    var exist_name = false;
                    for(var k=0; k<res[i].data.length; k++){
                        if(res[i].data[k].name == names[j]){
                            exist_name = true;
                        }
                    }
                    if(exist_name){

                    }else{
                        var index = this.getIndexByName(names[j]);
                        res[i].data.splice(index, 0, {
                            name: names[j],
                            index: index,
                            len: 0,
                        })
                    }
                }
            };
            this.opt.colorItem = this._colorItemIndex.filter(item=>{
                for(var i=0; i<names.length; i++){
                    if(names[i] == item.name){
                        return true
                    }
                }
                return false
            }).map(item=>{
                return item.name;
            })
            this.res = this.setDragLeftPos(res);
        },

	}
};
</script>

<style lang="less" scoped>
.week-range-wrap{
    background: #393939;
    color: #ccc;
    border: 1px solid #4b4b4b;
    .v-tip-line{
        position: absolute;
        height: calc(100% + 10px);
        width: 2px;
        top: -10px;
        border-right: 1px dashed #919191;
        border-left: none;
        span{
            position: absolute;
            bottom: calc(100% + 7px);
            font-size: 12px;
            color: #fff;
            font-size: 12px;
            left: -6px;
        }
    }
    .title-text{
        display: flex;
        justify-content: stretch;
        align-items: center;
        height: 24px;
        line-height: 24px;
        background: #222;
        &.opac{
            color: rgba(255,255,255,.2);
        }
        .title-v0{
            flex: none;
            width: 80px;
            text-align: center;
            font-size: 12px;
        }
        .title-data{
            font-size: 12px;
            flex: none;
            position: relative;
            height: 24px;
            .span-text{
                position: absolute;
                left: -6px;
                top: 0px;
            }
            .span-last{
                position: absolute;
                right: 0px;
                top: 0px;
            }
        }
    }
    .week-wrap{
        display: flex;
        justify-content: stretch;
        align-items: center;
        height: 40px;
        line-height: 40px;
        margin-top: 10px;
        &:nth-of-type(1){
            margin-top: 10px;
        }
        .title-v{
            flex: none;
            width: 80px;
            text-align: right;
            font-size: 12px;
            position: relative;
            text-indent: 16px;
            padding-right: 10px;
            .operation-btn{
                display: none;
                position: absolute;
                top: 0px;
                width: 18px;
                height: 40px;
                > .operation{
                    height: 100%;
                    width: 100%;
                    display: flex;
                    align-items: center;
                    justify-content: space-between;
                    flex-direction: column;
                }
                
            }
            .copy-up-icon,
            .copy-down-icon,
            .reset-icon{
                font-size: 12px;
                color: #aaa;
                flex: none;
                width: 13px;
                height: 13px;
                background: transparent;
                cursor: pointer;
                border: 1px solid transparent;
                display: flex;
                align-items: center;
                justify-content: center;
                text-indent: 0px;
                &:hover{
                    color: #1e91e3;
                    border-color: #1e91e3;
                }
            }
            
        }
        .title-data2-wrap{
            display: flex;
            justify-content: stretch;
            align-items: stretch;
            position: relative;
            .right-drag{
                position: absolute;
                width: 10px;
                background: transparent;
                cursor: e-resize;
                .red-btn{
                    position: absolute;
                    width: 10px;
                    height: 8px;
                    z-index: 99;
                    top: calc(50% - 4px);
                    left: calc(50% - 5px);
                }
            }
            .drag-line{
                position: absolute;
                height: 100%;
                width: 1px;
                left: 50%;
                top: 0px;
            }
            .colorItem{
                height: 26px;
                top: 7px;
                position: absolute;
                z-index: 201;
                &.last{
                    
                }
                &.first{
                    
                }
                .tip{
                    position: absolute;
                    z-index: 999;
                    left: 50%;
                    height: 12px;
                    line-height: 12px;
                    top: -18px;
                    color: white;
                    font-size: 12px;
                    opacity: 0.95;
                }
                .text{
                    display: block;
                    height: 26px;
                    line-height: 26px;
                    color: #fff;
                    font-size: 12px;
                    text-align: center;
                }
            }
        }
        .title-data2{
            flex: none;
            position: relative;
            height: 40px;
            border-left: 1px solid #222;
            border-bottom: 1px solid #222;
            border-top: 1px solid #222;
            &.last{
                border-right: 1px solid #222;
            }
            &:after{
                position: absolute;
                height: 100%;
                width: 1px;
                border-right: 1px dashed #222;
                left: 50%;
                top: 0px;
                content: ' ';
            }
        }
        &:hover{
            background: #484848;
            .operation-btn{
                display: block;
            }
        }
    }
}
</style>
