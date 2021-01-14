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
                let padding = {top:20,bottom:20,left:30,right:20},svgWidth = 400,svgHeight = 403,
                _rectWidth = 35,rectWidth = 30;
                let svg = this.svg = d3.select('#container').append('svg');
                svg.attr('width',svgWidth).attr('height',svgHeight);
                let update = this.svg.selectAll('rect').data(data),enter = update.enter(),
                exit = update.exit();
                let xScale = d3.scaleBand().domain(d3.range(data.length)).rangeRound([0,svgWidth-padding.left-padding.right]).paddingInner(0.2).paddingOuter(0.2),
                yScale = d3.scaleLinear().domain([0,d3.max(data)]).range([svgHeight-padding.top-padding.bottom,0]),
                xAxis = d3.axisBottom(xScale).tickSizeOuter(0).tickSizeInner(6),
                yAxis = d3.axisLeft(yScale).tickSizeOuter(0).tickSizeInner(5).ticks(10),
                gYAxis = svg.append('g').attr('transform',`translate(${padding.left},${padding.top})`),
                gXAxis = svg.append('g').attr('transform',`translate(${padding.left},${svgHeight-padding.bottom})`);
                xAxis(gXAxis);
                yAxis(gYAxis);
                //画柱子的时候 这个比例尺要和画坐标轴的时候颠倒一下=====切记切记，不然容易掉坑里
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