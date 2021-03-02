<!-- Your HTML goes here -->
<template>
    <div overflow="auto" id="bubble-chart-wrapper">
        <svg class="bubble-chart-container" :viewBox="myViewBox()" id="svg-bubble-chart" style="borderRight: 2px black solid;">
        <g transform='translate(3, 120)' >
            <text id="bubble-chart-title" transform="translate(150, -50)" font-weight="bold">Avg Income vs. Avg Years of Professional Coding Experience For Countries in 2020</text>
            <g id="bubble-chart-x-axis" transform="translate(80, 800)"></g>
            <text id="bubble-chart-x-axis-label" transform="translate(400, 850)" font-weight="bold">Avg Years Coding Professionally</text>
            <g id="bubble-chart-y-axis" transform='translate(80,0)'></g>
            <text id="bubble-chart-y-axis-label" transform='translate(13, 500) rotate(-90)' font-weight="bold">Avg. Income (USD)</text>

        </g>
        <g id="bubble-chart-color-legend" transform="translate(800, 125)">
            <rect fill="white" height="280" width = "150"></rect>
            <text transform="translate(45, 25)" style="font-weight: bold;">Legend</text>
            <rect fill="blue" height="20" width = "20" transform="translate(10, 40)"></rect>
            <text transform="translate(35, 55)" style="font-weight: bold;">North America</text>
            <rect fill="green" height="20" width = "20" transform="translate(10, 70)"></rect>
            <text transform="translate(35, 85)" style="font-weight: bold;">South America</text>
            <rect fill="orange" height="20" width = "20" transform="translate(10, 100)"></rect>
            <text transform="translate(35, 115)" style="font-weight: bold;">Africa</text>
            <rect fill="brown" height="20" width = "20" transform="translate(10, 130)"></rect>
            <text transform="translate(35, 145)" style="font-weight: bold;">Europe</text>
            <rect fill="purple" height="20" width = "20" transform="translate(10, 160)"></rect>
            <text transform="translate(35, 175)" style="font-weight: bold;">Asia</text>
            <rect fill="red" height="20" width = "20" transform="translate(10, 190)"></rect>
            <text transform="translate(35, 205)" style="font-weight: bold;">Oceania</text>
            <text transform="translate(30, 240)" style="font-weight: bold;">Bubble Size</text>
            <text transform="translate(70, 257)" style="font-weight: bold;">=</text>
            <text transform="translate(20, 270)" style="font-weight: bold;"># Respondents</text>
        </g>
        </svg>
        <!-- if (d.countryContinent == "North America") 
                            return "blue"
                        if (d.countryContinent == "South America") 
                            return "green"
                        if (d.countryContinent == "Africa") 
                            return "yellow"
                        if (d.countryContinent == "Europe") 
                            return "brown"
                        if (d.countryContinent == "Asia") 
                            return "purple"
                        if (d.countryContinent == "Oceania") 
                            return "red" -->
    </div>
</template>

<script>
import * as d3 from 'd3'
// const d3 = Object.assign({}, require("d3-format"), require("d3-geo"), require("d3-geo-projection"), require("d3-cloud"));
export default {
    name: 'BubbleChart', // Feel free to rename this and the file
    props: {
        // Feel free to add props to communicate between components
        data: {
            required: true
        },
        xAttribute: {
            required: true
        },
        yAttribute: {
            required: true
        },
        height: {
            required: true
        },

        width: {
            required: true
        },
    },
    data() { //like properties of objects?
        return {
            xScale: null,
            yScale: null,
        }
    },
    mounted() {
        console.log("mounted")
        this.init()
    },
    methods: {
        init() {
            console.log(d3) // This can be savely removed
            // This will be called when the page loads.
            // You can load your data and setup D3 here
            this.xScale = d3.scaleLinear().domain(d3.extent(this.data, d => d[this.xAttribute])).range([0, 900])
            this.yScale = d3.scaleLinear().domain(d3.extent(this.data, d => d[this.yAttribute])).range([800, 0])

            this.render();
        },
        render() {
            this.renderXAxis();
            this.renderYAxis();
            this.renderDataPoints();
            this.renderTooltip();
            this.addTooltips();
            this.addClickFunctionality();
        },
        renderXAxis() {
            let xAxis = d3.axisBottom(this.xScale)
                            .ticks(this.width / 100)
            d3.select("#bubble-chart-x-axis").call(xAxis);
        },
        renderYAxis() {
            let yAxis = d3.axisLeft(this.yScale)
                            .ticks(this.height / 100)
            d3.select("#bubble-chart-y-axis").call(yAxis);
        }, 
        renderDataPoints() {
            //have to do this since using this. in lineGraphSVG will refer to lineGraphSVG
            //not the component (means you have to copy component props over)
            let scopedXScale = this.xScale;
            let scopedYScale = this.yScale;
            let scopedData = this.data; //this.data 
            let scopedXAttribute = this.xAttribute;
            let scopedYAttribute = this.yAttribute;
            // console.log("scopedXScale", scopedXScale);
            let lineGraphSVG = d3.select("#svg-bubble-chart")
            lineGraphSVG
                .append("g")
                .selectAll("dot")
                .data(scopedData)
                .enter()
                .append("circle")
                    .attr("cx", function(d) { 
                        // console.log("data", scopedData)
                        // console.log(this.xScale)
                        // console.log("x pos: ", d[scopedXAttribute])
                        return scopedXScale(d[scopedXAttribute]) 
                    } ) //plug a certain date into xScale to get the position on graph
                    .attr("avgYearsCodePro", function(d) {
                        return d[scopedXAttribute]
                    })
                    .attr("avgIncomeUSD", function(d) {
                            return d[scopedYAttribute]
                    })
                    .attr("countryName", function(d) {
                            return d.countryName
                    })
                    .attr("cy", function(d) { return scopedYScale(d[scopedYAttribute]) } )
                    .attr("r", function(d) { 
                        let radius = Math.sqrt(d.numberOfRespondentsWithValidYearsCodePro / Math.PI);
                        if (radius < 3) {
                            radius = 3;
                        }
                        return Math.floor(radius)
                    })
                    .attr("visibility", () => {
                        
                        // // if (isNaN(d.avgIncomeUSD)) 
                        // //     return "hidden"
                        // if (d.numberOfRespondentsWithValidYearsCodePro < 10) {
                        //     return "hidden"
                        // }
                        return "visible"
                            
                    })
                    .style("fill", function(d) {
                        if (d.countryContinent == "North America") 
                            return "blue"
                        if (d.countryContinent == "South America") 
                            return "green"
                        if (d.countryContinent == "Africa") 
                            return "orange"
                        if (d.countryContinent == "Europe") 
                            return "brown"
                        if (d.countryContinent == "Asia") 
                            return "purple"
                        if (d.countryContinent == "Oceania") 
                            return "red"
                    })
                    .style("opacity", "0.7")
                    .attr("stroke", "black")
                    .attr("transform", "translate(83, 118)")
                    .attr("class", "bubble-chart-tooltip-point")
            console.log("dots shown")

            // let tooltips = d3.selectAll(".bubble-chart-tooltip-point")
            // tooltips.each((d) => {
            //     // console.log(d
            //     if (isNaN(d.avgIncomeUSD)) {
            //         console.log(`${d.countryName} is nan`)
            //         console.log(this)
            //     }
            // })
        // .attr("class", "point");
        },
        addTooltips() {
            d3.selectAll(".bubble-chart-tooltip-point")
            .on("mouseover", function(e) {
                d3.select(this).style("cursor", "pointer");
                let mouseXPos = d3.pointer(e)[0]
                let mouseYPos = d3.pointer(e)[1]
                console.log("mouse position: ", d3.pointer(e))
                d3.select(this).attr("stroke-width", "3")
                // d3.select(this).attr("r", function(d)  {
                //     return Math.floor(d.numberOfRespondentsWithValidYearsCodePro / 50)
                // })

                let tooltipAvgYearsCodePro = d3.select("#tooltip-avg-years-pro-coding");
                let avgYearsCodePro = "Avg Years Coding Professionally: " + String(d3.select(this).attr("avgYearsCodePro"))
                tooltipAvgYearsCodePro.text(avgYearsCodePro)

                let tooltipAvgIncome = d3.select("#tooltip-avg-income");
                let avgIncome = "Avg Income: " + String(d3.select(this).attr("avgIncomeUSD"));
                tooltipAvgIncome.text(avgIncome)

                let tooltipCountryName = d3.select("#tooltip-country-name");
                let countryName = "Country: " + String(d3.select(this).attr("countryName"));
                tooltipCountryName.text(countryName)

                let tooltipArea = d3.select("#tooltip-area");

                //data points close to the right side have tooltips cut off
                if (Number(d3.select(this).attr("cx")) > 840) {
                    tooltipArea.attr("transform", "translate(" + String(Number(d3.select(this).attr("cx")) - 280) + ", " + String(Number(d3.select(this).attr("cy")) + 80) + ")")
                    tooltipCountryName.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx"))+ 10) + ", " + String(Number(tooltipArea.attr("cy")) + 25) + ")")
                    tooltipAvgYearsCodePro.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx"))+ 10) + ", " + String(Number(tooltipArea.attr("cy")) + 45) + ")")
                    tooltipAvgIncome.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx"))+ 10) + ", " + String(Number(tooltipArea.attr("cy")) + 65) + ")")
                }
                else if (mouseXPos < 5) {
                    console.log("too far left")
                    tooltipArea.attr("transform", "translate(" + String(Number(mouseXPos) + 10) + ", " + String(Number(mouseYPos) + 30) + ")")
                    tooltipCountryName.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 25) + ")")
                    tooltipAvgYearsCodePro.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 45) + ")")
                    tooltipAvgIncome.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 65) + ")")
                }
                //if the data point is too far left 
                else if (Number(d3.select(this).attr("cx")) < 100) {
                    console.log("too far left")
                    tooltipArea.attr("transform", "translate(" + String(Number(mouseXPos) - 30) + ", " + String(Number(mouseYPos) + 30) + ")")
                    tooltipCountryName.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 25) + ")")
                    tooltipAvgYearsCodePro.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 45) + ")")
                    tooltipAvgIncome.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 65) + ")")
                }
                
                else if (Number(d3.select(this).attr("cy")) < 5) {
                    console.log("too far up")
                    tooltipArea.attr("transform", "translate(" + String(Number(mouseXPos) - 80) + ", " + String(Number(mouseYPos) + 140) + ")")
                    tooltipCountryName.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 25) + ")")
                    tooltipAvgYearsCodePro.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 45) + ")")
                    tooltipAvgIncome.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 65) + ")")
                }
                else if (mouseXPos > 770) {
                    tooltipArea.attr("transform", "translate(" + String(Number(d3.select(this).attr("cx")) - 280) + ", " + String(Number(d3.select(this).attr("cy")) + 80) + ")")
                    tooltipCountryName.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx"))+ 10) + ", " + String(Number(tooltipArea.attr("cy")) + 25) + ")")
                    tooltipAvgYearsCodePro.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx"))+ 10) + ", " + String(Number(tooltipArea.attr("cy")) + 45) + ")")
                    tooltipAvgIncome.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx"))+ 10) + ", " + String(Number(tooltipArea.attr("cy")) + 65) + ")")
                }
                else {
                    tooltipArea.attr("transform", "translate(" + String(Number(mouseXPos) - 80) + ", " + String(Number(mouseYPos) + 20) + ")")
                    tooltipCountryName.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 25) + ")")
                    tooltipAvgYearsCodePro.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 45) + ")")
                    tooltipAvgIncome.attr("transform", "translate(" + String(Number(tooltipArea.attr("cx")) + 10) + ", " + String(Number(tooltipArea.attr("cy")) + 65) + ")")
                }
                console.log("blah")
                tooltipArea.attr("visibility", "visible")



                return d3.select(this).attr("date")
            })
            //make normal color after stop hoverin gover
            d3.selectAll(".bubble-chart-tooltip-point").on("mouseout", function() {
                d3.select(this).style("cursor", "default");
                let tooltipArea = d3.select("#tooltip-area");
                tooltipArea.attr("visibility", "hidden")

            // let tooltipDate = d3.select("#tooltip-date");
            // let tooltipPassengers = d3.select("#tooltip-total-passengers");
            // tooltipDate.attr("visibility", "hidden")
            // tooltipPassengers.attr("visibility", "hidden")
            d3.select(this).attr("stroke-width", "1")
            d3.select(this).attr("r", function(d)  {
                    let radius = Math.sqrt(d.numberOfRespondentsWithValidYearsCodePro / Math.PI);
                    if (radius < 3) {
                            radius = 3;
                        }
                    return Math.floor(radius)
                })
            })
        console.log("attached event listener to points")
        // console.log(selectedTerminal);
        },
        // <g id="tooltip-area" >
        //         <rect height="40" width="160" fill="gray"></rect>
        //         <text id="tooltip-date"></text>
        //         <text id="tooltip-total-passengers"></text>
        //     </g>
        renderTooltip() {
            let svgLineChart = d3.select("#svg-bubble-chart");
            svgLineChart
                .append("g")
                    .attr("id", "tooltip-area")
                    .append("rect")
                        .attr("height", 80)
                        .attr("width", 350)
                        .attr("rx", 15)
                        .attr("fill", "black")
                        .attr("opacity", 0.7)
                        


            let tooltipArea = d3.select("#tooltip-area");
            // console.log("tooltip",tooltipArea)
            tooltipArea
                .append("text")
                    .attr("id", "tooltip-country-name")
                    .attr("fill", "white")
                    .attr("font-weight", "bold")
            tooltipArea
                .append("text")
                    .attr("id", "tooltip-avg-years-pro-coding")
                    .attr("fill", "white")
                    .attr("font-weight", "bold")
            tooltipArea
                .append("text")
                    .attr("id", "tooltip-avg-income")
                    .attr("fill", "white")
                    .attr("font-weight", "bold")

                        //hides the tooltip initially

            tooltipArea.attr("visibility", "hidden")

        },
        passDataUp(d) {
            // console.log("data", d)
            this.$emit('data-point-selected', d)
        },
        addClickFunctionality() {
            // thisCopy = this;
            d3.selectAll(".bubble-chart-tooltip-point").on("click", (event, d) => { //has to be arrow function or else this won't refer to the actual data point
                this.passDataUp(d);
                // console.log("data:", d);
            })
        },
        myViewBox() {
            return `0 0 1000 ${this.height}`
        }          
    }
    
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#svg-bubble-chart {
    width: 100%;
    height: 100%;
    
    /* border: 2px palegreen solid; */
    /* font-weight: bold; */
}
#tooltip-area {
    fill: white;
}
#bubble-chart-wrapper {
    position: relative;
}
#bubble-chart-color-legend {
    /* border: 2px red solid; */
    /* position: absolute; */
    top: 1px;
    right: 1px;
}


</style>