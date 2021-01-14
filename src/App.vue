<template>
    <div id="app">
        <p>这是我的第一个d3项目，求给力</p>
        <div id="container"></div>
    </div>
</template>

<script>
    import * as d3 from 'd3';
    export default {
        name: 'App',
        components: {

        },
        methods: {
            draw(data){
                //预先定义好，svg的大小，以及 在svg内部作画的时候 各种padding的值
                let padding = {top:20,bottom:20,left:30,right:20},svgWidth = 400,svgHeight = 403,
                _rectWidth = 35,rectWidth = 30;
                //使用d3创建一个svg元素
                let svg = this.svg = d3.select('#container').append('svg');
                //设置svg的宽高
                svg.attr('width',svgWidth).attr('height',svgHeight);
                //使用d3添加rect元素作为柱状图的柱子
                let update = this.svg.selectAll('rect').data(data),enter = update.enter(),
                exit = update.exit();
                //定义好比例尺，x轴比例尺使用的是scaleBand，支持定义域是散列值，但是值域可以是连续值，适用于柱状图
                let xScale = d3.scaleBand().domain(d3.range(data.length)).rangeRound([0,svgWidth-padding.left-padding.right]).paddingInner(0.2).paddingOuter(0.2),
                yScale = d3.scaleLinear().domain([0,d3.max(data)]).range([svgHeight-padding.top-padding.bottom,0]),
                //定义好需要使用的x轴 y轴
                xAxis = d3.axisBottom(xScale).tickSizeOuter(0).tickSizeInner(6),
                yAxis = d3.axisLeft(yScale).tickSizeOuter(0).tickSizeInner(5).ticks(10),
                //定义好x轴 y轴需要应用到的元素 为什么要用元素g 官方的说法是 不让 svg元素显得混乱
                gYAxis = svg.append('g').attr('transform',`translate(${padding.left},${padding.top})`),
                gXAxis = svg.append('g').attr('transform',`translate(${padding.left},${svgHeight-padding.bottom})`);
                //将轴线画出来
                xAxis(gXAxis);
                yAxis(gYAxis);
                //先画柱子 还是 先画Y轴，对于Y轴的值域要求是不一样的，两者刚好是反着来的，切记不要忘了
                yScale.range([0,svgHeight-padding.top-padding.bottom])
                update.attr('x',(d,i)=>{
                    return padding.left + xScale(i)
                }).attr('y',(d)=>{
                    return svgHeight - (padding.bottom + yScale(d))
                }).attr('width',xScale.bandwidth()).attr('height',(d)=>{
                    return yScale(d)
                })
                enter.append('rect').attr('x',(d,i)=>{
                    return padding.left + xScale(i)
                }).attr('y',(d)=>{
                    return svgHeight - (padding.bottom + yScale(d))
                }).attr('width',xScale.bandwidth()).attr('height',(d)=>{
                    return yScale(d)
                })
                exit.remove();

                let textUpdate = this.svg.selectAll('text').data(data);
                textUpdate.enter().append('text').attr('x',(d,i)=>{
                    return padding.left + _rectWidth*i
                }).attr('y',(d)=>{
                    return svgHeight - (padding.bottom + d)
                }).attr('dx',rectWidth/2).attr('fill','#fff').attr('dy','1em').attr('text-anchor','middle').attr('font-size','12').text(d=>d)
            }
        },
        mounted() {
            let data = [14,24,23,15,19,36,25];
            this.draw(data)
            // let quantize = d3.scale.quantize().domain([0,2,4,10]).range([1,100])
            // let paint = d3.scaleLinear().range(["yellow", "red"])
            // console.log(paint(1))
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>