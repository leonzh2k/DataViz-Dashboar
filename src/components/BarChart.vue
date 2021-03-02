<!-- Your HTML goes here -->
<template>
    <div id="bar-chart-container" >
        <svg class="bar-chart-container" :viewBox="myViewBox()" id="svg-bar-chart" style="borderTop: 2px black solid;">
        <g transform='translate(20, 100)' id="bar-chart-group">
            <text id="bar-chart-title" transform="translate(330, -50)" font-size="18" font-weight="bold">Avg Income By Education Level In The World</text>
            <g id="bar-chart-x-axis" transform="translate(120, 350)"  ></g>
            <text 
                id="bar-chart-x-axis-label" 
                transform="translate(870, 390)" 
                font-weight="bold">
                Avg. Income
            </text>
            <g id="bar-chart-y-axis" transform='translate(120,0)' font-weight="bold"></g>
            <text id="bar-chart-y-axis-label" transform='translate(-10,-20)' font-weight="bold">Education Level</text>
            <text id="no-data-notice" visibility="hidden" font-size="48px" font-weight="bold" transform="translate(400, 180)">No Data</text>
        </g>
        <!-- <g id="legend" transform="translate(80, 800)">
            <rect fill="blue" width="100" height="100" transform="translate(80, 0)"></rect>
            <rect fill="green" width="100" height="100" transform="translate(80, 0)"></rect>
            <text>Domestic Passengers</text>
            <text>International Passengers</text>
        </g> -->
        <foreignObject x="800" y="-110" width="200" height="200">
            <div id="bar-chart-view-selection-wrapper" >
                <div style="textAlign: left;">
                    <label for="men">Men</label>
                    <input type="radio" name="genderType" id="men"/>
                </div>
                <div style="textAlign: left;"> 
                    <label for="women">Women</label>
                    <input type="radio" name="genderType" id="women"/>
                </div>
                <!-- <div style="textAlign: left;"> 
                    <label for="web-frameworks">Web Frameworks</label>
                    <input type="radio" name="stackType" id="web-frameworks"/>
                </div> -->
            </div>
        </foreignObject>
        </svg>
        
        
    </div>
    
</template>

<script>
import * as d3 from 'd3'

export default {
    name: 'View3', // Feel free to rename this and the file
    props: {
        dataset: {
            required: true
        },
        title: {
            required: true
        }
    },
    data() {
        return {
            xScale: null,
            yScale: null,
            MenButtonChecked: true,
            WomenButtonChecked: false,
        }
    },
    watch: {
        //runs every time dataset prop changes
        dataset() {
            console.log("received bar data: ", this.dataset)
            // console.log("dataset updated")
            // console.log("dataset updated")
            // console.log("dataset updated")
            // console.log("dataset updated")
            
            this.update();
        },
    },
    mounted() {
        this.init()
        
        console.log("bar loaded")
        
    },
    methods: {
        init() {
            console.log(d3) // This can be savely removed
            // This will be called when the page loads.
            // You can load your data and setup D3 here
            this.xScale = d3.scaleLinear();
            this.yScale = d3.scaleBand();
            d3.select("#bar-chart-x-axis-label").text("Avg. Income")
            d3.select("#bar-chart-y-axis-label").text("Education Level")
            this.createRadioSelectionHandlers();
            this.update();
        },
        update() {
            console.log("curr data: ", this.dataset)
            if (this.title.includes("Lao People's")) {
                d3.select("#bar-chart-title").text("Avg Income by Education Level in Laos")
            }
            else {
                d3.select("#bar-chart-title").text("Avg Income by Education Level in " + this.title)
            }
            
            if (this.MenButtonChecked) {
                let noData = true;
                this.dataset.forEach((eduType) => {
                    if (eduType.AvgIncomeMen != 0) {
                        noData = false;
                    }
                })
                if (noData) {
                    console.log('no data')
                    // d3.select("#bar-chart-group").attr("visibility", "hidden");
                    let xAxis = d3.select("#bar-chart-x-axis");
                    let yAxis = d3.select("#bar-chart-y-axis");
                    xAxis.attr("visibility", "hidden");
                    yAxis.attr("visibility", "hidden");
                    let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                    let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                    xAxisLabel.attr("visibility", "hidden");
                    yAxisLabel.attr("visibility", "hidden");
                    d3.selectAll(".bar").attr("visibility", "hidden");
                    d3.select("#no-data-notice").attr("visibility", "visible")
                    // d3.select("#bar-chart-y-axis-label").remove();
                    return
                }
                let xAxis = d3.select("#bar-chart-x-axis");
                let yAxis = d3.select("#bar-chart-y-axis");
                xAxis.attr("visibility", "visible");
                yAxis.attr("visibility", "visible");
                let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                xAxisLabel.attr("visibility", "visible");
                yAxisLabel.attr("visibility", "visible");
                d3.selectAll(".bar").attr("visibility", "visible");
                d3.select("#no-data-notice").attr("visibility", "hidden")
                d3.select("#men").property("checked", true);
                // render bars for men
                this.yScale
                    .domain(d3.map(this.dataset, (d) => d.educationType))
                    .range([0, 350])
                    .padding(0.2);
                this.xScale
                    .domain([0, d3.max(this.dataset, (d) => d.AvgIncomeMen)])
                    .nice()
                    .range([0, 800]);
                this.renderAxes();
                this.renderMenBars();
                    // this.renderMenYAxis();

            }
            else {
                console.log("women data: ", this.dataset)
                let noData = true;
                this.dataset.forEach((eduType) => {
                    if (eduType.AvgIncomeWomen != 0) {
                        noData = false;
                    }
                })
                if (noData) {
                    console.log('no data')
                    // d3.select("#bar-chart-group").attr("visibility", "hidden");
                    let xAxis = d3.select("#bar-chart-x-axis");
                    let yAxis = d3.select("#bar-chart-y-axis");
                    xAxis.attr("visibility", "hidden");
                    yAxis.attr("visibility", "hidden");
                    let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                    let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                    xAxisLabel.attr("visibility", "hidden");
                    yAxisLabel.attr("visibility", "hidden");
                    d3.selectAll(".bar").attr("visibility", "hidden");
                    d3.select("#no-data-notice").attr("visibility", "visible")
                    // d3.select("#bar-chart-y-axis-label").remove();
                    return
                }
                let xAxis = d3.select("#bar-chart-x-axis");
                let yAxis = d3.select("#bar-chart-y-axis");
                xAxis.attr("visibility", "visible");
                yAxis.attr("visibility", "visible");
                let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                xAxisLabel.attr("visibility", "visible");
                yAxisLabel.attr("visibility", "visible");
                d3.selectAll(".bar").attr("visibility", "visible");
                d3.select("#no-data-notice").attr("visibility", "hidden")
                d3.select("#men").property("checked", true);
                //render woman bars
                console.log("rendreing")
                d3.select("#women").property("checked", true);
                this.yScale
                    .domain(d3.map(this.dataset, (d) => d.educationType))
                    .range([0, 350])
                    .padding(0.2);
                this.xScale
                    .domain([0, d3.max(this.dataset, (d) => d.AvgIncomeWomen)])
                    .nice()
                    .range([0, 800]);
                this.renderAxes();
                this.renderWomenBars();
                    // this.renderWomenYAxis();
            }
        },
        renderAxes() {
            let xAxis = d3.axisBottom(this.xScale);
            d3.select("#bar-chart-x-axis").call(xAxis);

            let yAxis = d3.axisLeft(this.yScale);
            d3.select("#bar-chart-y-axis").call(yAxis);
        },
        renderMenBars() {
            d3.select(`#bar-chart-group`)
            // .select(".plot")
            .selectAll("rect")
            .data(this.dataset)
            .join("rect")
            .transition()
            .duration(200)
            .attr("x", (d) => this.xScale(0))
            .attr("y", (d) => this.yScale(d.educationType))
            .attr("fill", "rgb(90, 82, 255)")
            .attr("width", (d) => this.xScale(d.AvgIncomeMen))
            .attr("height", 20)
            .attr('transform', 'translate(120, 5)')
            .attr('class', 'bar')
            // .attr('transform', 'rotate(-90)')
        },
        renderWomenBars() {
            d3.select(`#bar-chart-group`)
            // .select(".plot")
            .selectAll("rect")
            .data(this.dataset)
            .join("rect")
            .transition()
            .duration(200)
            .attr("x", (d) => this.xScale(0))
            .attr("y", (d) => this.yScale(d.educationType))
            .attr("fill", "hsl(337, 100%, 65%)")
            .attr("width", (d) => {
                console.log(d.AvgIncomeWomen)
                console.log(this.xScale(d.AvgIncomeWomen))
                return this.xScale(d.AvgIncomeWomen)
            })
            .attr("height", 20)
            .attr('transform', 'translate(120, 5)')
            .attr('class', 'bar')
            // .attr('transform', 'rotate(-90)')
        },
        createRadioSelectionHandlers() {
            d3.select("#men").on("click",() => {
                if (this.MenButtonChecked) {
                    // this.domesticVsInternationalButtonChecked = false;
                    // d3.select("#domestic-vs-international").property("checked", false);
                    // console.log("unchecked")
                    // this.checkRadioButtonSelectionStatus();
                }
                else {
                    this.MenButtonChecked = true;
                    d3.select("#men").property("checked", true);
                    this.WomenButtonChecked = false;
                    d3.select("#women").property("checked", false);
                    console.log('men checked')
                    // render bars for men
                    let noData = true;
                    this.dataset.forEach((eduType) => {
                        if (eduType.AvgIncomeMen != 0) {
                            noData = false;
                        }
                    })
                    if (noData) {
                        console.log('no data')
                        // d3.select("#bar-chart-group").attr("visibility", "hidden");
                        let xAxis = d3.select("#bar-chart-x-axis");
                        let yAxis = d3.select("#bar-chart-y-axis");
                        xAxis.attr("visibility", "hidden");
                        yAxis.attr("visibility", "hidden");
                        let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                        let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                        xAxisLabel.attr("visibility", "hidden");
                        yAxisLabel.attr("visibility", "hidden");
                        d3.selectAll(".bar").attr("visibility", "hidden");
                        d3.select("#no-data-notice").attr("visibility", "visible")
                        // d3.select("#bar-chart-y-axis-label").remove();
                        return
                    }
                    let xAxis = d3.select("#bar-chart-x-axis");
                    let yAxis = d3.select("#bar-chart-y-axis");
                    xAxis.attr("visibility", "visible");
                    yAxis.attr("visibility", "visible");
                    let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                    let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                    xAxisLabel.attr("visibility", "visible");
                    yAxisLabel.attr("visibility", "visible");
                    d3.selectAll(".bar").attr("visibility", "visible");
                    d3.select("#no-data-notice").attr("visibility", "hidden")
                    
                    this.yScale
                        .domain(d3.map(this.dataset, (d) => d.educationType))
                        .range([0, 350])
                        .padding(0.2);
                    this.xScale
                        .domain([0, d3.max(this.dataset, (d) => d.AvgIncomeMen)])
                        .nice()
                        .range([0, 800]);
                    this.renderAxes();
                    this.renderMenBars();

                }
                
            })
            d3.select("#women").on("click",() => {
                if (this.WomenButtonChecked) {
                    // this.arrivalVsDepartureButtonChecked = false;
                    // d3.select("#arrival-vs-departure").property("checked", false);

                    // console.log("unchecked");
                    // this.checkRadioButtonSelectionStatus();
                }
                else {
                    this.WomenButtonChecked = true;
                    d3.select("#women").property("checked", true);
                    this.MenButtonChecked = false;
                    d3.select("#men").property("checked", false);
                    console.log("women checked")
                    // this.showArrivalVsDepartureBars();
                    //render woman bars
                    d3.select("#women").property("checked", true);
                    console.log("women data: ", this.dataset)
                    let noData = true;
                    this.dataset.forEach((eduType) => {
                        if (eduType.AvgIncomeWomen != 0) {
                            noData = false;
                        }
                    })
                    if (noData) {
                        console.log('no data')
                        // d3.select("#bar-chart-group").attr("visibility", "hidden");
                        let xAxis = d3.select("#bar-chart-x-axis");
                        let yAxis = d3.select("#bar-chart-y-axis");
                        xAxis.attr("visibility", "hidden");
                        yAxis.attr("visibility", "hidden");
                        let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                        let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                        xAxisLabel.attr("visibility", "hidden");
                        yAxisLabel.attr("visibility", "hidden");
                        d3.selectAll(".bar").attr("visibility", "hidden");
                        d3.select("#no-data-notice").attr("visibility", "visible")
                        // d3.select("#bar-chart-y-axis-label").remove();
                        return
                    }
                    let xAxis = d3.select("#bar-chart-x-axis");
                    let yAxis = d3.select("#bar-chart-y-axis");
                    xAxis.attr("visibility", "visible");
                    yAxis.attr("visibility", "visible");
                    let xAxisLabel = d3.select("#bar-chart-x-axis-label");
                    let yAxisLabel = d3.select("#bar-chart-y-axis-label");
                    xAxisLabel.attr("visibility", "visible");
                    yAxisLabel.attr("visibility", "visible");
                    d3.selectAll(".bar").attr("visibility", "visible");
                    d3.select("#no-data-notice").attr("visibility", "hidden")
                    this.yScale
                        .domain(d3.map(this.dataset, (d) => d.educationType))
                        .range([0, 350])
                        .padding(0.2);
                    this.xScale
                        .domain([0, d3.max(this.dataset, (d) => d.AvgIncomeWomen)])
                        .nice()
                        .range([0, 800]);
                    this.renderAxes();
                    this.renderWomenBars();

                }
                
            })
        },
        myViewBox() {
            return `0 0 1000 500`
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* Add your CSS here */
#bar-chart-container {
    /* border: 2px red solid; */
    position: relative;
}
#svg-bar-chart {
    width: 100%;
    height: 100%;
    margin: 0 auto;
    /* border: 2px palegreen solid; */
    /* font-weight: bold; */
}
#bar-chart-view-selection-wrapper {
    font-size: 18px;
    font-weight: bold;
    position: absolute;
    bottom: 10px;
    right: 10px;
    display: flex;
    flex-direction: column;
    /* visibility: hidden;  */
    border: 2px rgb(150, 150, 150) solid;
    width: 100px;
    background-color: whitesmoke;
}
</style>