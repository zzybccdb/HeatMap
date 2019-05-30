<template>
    <v-container>
        <v-layout row >
            <v-flex xs12>
                <v-btn small v-on:click="updateData('ALL-NO.csv')">ALL-NO</v-btn>
                <v-btn small v-on:click="updateData('ALL-YES.csv')">ALL-YES</v-btn>
                <v-btn small v-on:click="updateData('country-No.csv')">country-No</v-btn>
                <v-btn small v-on:click="updateData('country yes.csv')">country yes</v-btn>
                <v-btn small v-on:click="updateData('METRO-NO.csv')">METRO-NO</v-btn>
                <v-btn small v-on:click="updateData('METRO-YES.csv')">METRO-YES</v-btn>
            </v-flex>
        </v-layout>
        <div ref="heatmap" id='heatmap'></div>
    </v-container>
</template>

<script>
    export default {
        data(){
            return {
            }
        },
        created(){
            let vm = this
            vm.svg = undefined
        },
        methods:{
            // 初始化图表的长宽间隔属性,绑定 div 
            d3InitSet(){
                let vm = this;
                let d3 = vm.$d3
                // 设定图表长宽,四周间隔
                let margin = {top: 30, right: 30, bottom: 30, left: 30}
                vm.width = 950 - margin.left - margin.right
                vm.height = 450 - margin.top - margin.bottom

                // 绑定页面 div, 在 div 下新增 svg
                vm.svg = d3.select("#heatmap")
                    .append("svg")
                    .attr("id",'my_heatmap')
                    .attr("width", vm.width + margin.left + margin.right)
                    .attr("height", vm.height + margin.top + margin.bottom)
                    .append("g")
                    .attr("transform","translate(" + margin.left + "," + margin.top + ")")


                // 设定 heatmap 的轴线 label
                let labelX = []
                let labelY = []
                for(let i = 1; i <=12; i++){
                    labelY.push(''+i)
                    labelX.push(''+(2*i-2))
                    labelX.push(''+(2*i-1))
                } 
                // labelX = ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"]
                // labelY = ["v1", "v2", "v3", "v4", "v5", "v6", "v7", "v8", "v9", "v10"]
                // 设定轴线, 并绑定到 this 下
                // scaleBand 将定义域对应到 range 上
                vm.X = d3.scaleBand()
                    .range([ 0, vm.width ])
                    .domain(labelX)
                    .padding(0.01)
                
                vm.Y = d3.scaleBand()
                    .range([vm.height,0])
                    .domain(labelY)
                    .padding(0.01)

                // 颜色比例尺设定
                vm.color = d3.scaleLinear()
                    .range(["#ffffff", "#C424B1"])
                    .domain([0,1])
            },
            // 绘制轴线上的 ticks, 不画出轴线
            drawTicks(){
                let vm = this
                let d3 = vm.$d3
                
                // 设定轴线信息
                vm.Xaxis = d3.axisBottom(vm.X)
                vm.Yaxis = d3.axisLeft(vm.Y)

                // 在 svg 下加入两个 group, 这个 group 包含两个轴线的信息
                vm.svg.append("g").attr("id","axis").attr("transform", "translate(0," + vm.height + ")").call(vm.Xaxis).call(g => g.select(".domain").remove())
                vm.svg.append("g").call(vm.Yaxis).call(g => g.select(".domain").remove())
            },
            // 载入资料预设载入
            dataLoad(){
                let vm = this
                let d3 = vm.$d3
                // 这里注意下,如果训练教材 code 写法是,且无法顺利执行,请参考这里的写法

                // d3.csv("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/heatmap_data.csv", function(data) {

                //   svg.selectAll()
                //       .data(data, function(d) {return d.group+':'+d.variable;})
                //       .enter()
                //       .append("rect")
                //       .attr("x", function(d) { return x(d.group) })
                //       .attr("y", function(d) { return y(d.variable) })
                //       .attr("width", x.bandwidth() )
                //       .attr("height", y.bandwidth() )
                //       .style("fill", function(d) { return myColor(d.value)} )

                // })
                d3.csv("ALL-NO.csv").then((data)=>{
                    vm.svg.selectAll("rect")
                        .data(data)
                        .enter()
                        .append("rect")
                        .attr("x", function(d) { return vm.X(d.HOUR) })
                        .attr("y", function(d) { return vm.Y(d.MONTH) })
                        .attr("width", vm.X.bandwidth() )
                        .attr("height", vm.Y.bandwidth() )
                        .style("fill", function(d) { return vm.color(d.percent)} )         
                })           
            },
            // 更新资料
            updateData(filename){
                let vm = this
                let d3 = vm.$d3
                d3.csv(filename).then((data)=>{
                    vm.svg.selectAll("rect").data(data).style("fill", function(d) { return vm.color(d.percent)} )      
                })
            }
        },
        mounted(){
            console.log("begin draw d3 heatmap")
            let vm = this
            vm.d3InitSet()
            vm.drawTicks()
            vm.dataLoad()
            window.vm = vm
        }
    }
</script>

<style>
line{
    visibility:hidden
}
</style>