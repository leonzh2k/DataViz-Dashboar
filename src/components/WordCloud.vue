<!-- Your HTML goes here -->
<template>
    <div id="word-cloud-container" style="position: relative; ">
        <svg id="word-cloud-svg" > <!--style="position: relative; border: 2px orange solid;" -->
            <g>
                <text id="die" transform="translate(250, 20)" font-weight="bold" font-size="15px">Popularity of Languages In </text>
            </g>
        </svg>
        <div id="view-selection-wrapper" >
            <div style="textAlign: left;">
                <label for="languages">Languages</label>
                <input type="radio" name="stackType" id="languages"/>
            </div>
            <div style="textAlign: left;"> 
                <label for="databases">Databases</label>
                <input type="radio" name="stackType" id="databases"/>
            </div>
            <div style="textAlign: left;"> 
                <label for="web-frameworks">Web Frameworks</label>
                <input type="radio" name="stackType" id="web-frameworks"/>
            </div>
        </div>
    </div>
    
</template>

<script>
import * as d3 from 'd3'
import cloud from 'd3-cloud'
// const d3 = Object.assign({}, require("d3-format"), require("d3-geo"), require("d3-cloud"), require("d3-selection"), require("d3-scale"));
export default {
    name: 'WordCloud', // Feel free to rename this and the file
    props: {
        dataset: {
            required: true
        },
        title: {
            required: true
        }
        // Feel free to add props to communicate between components
    },
    data() {
        return {
            languagesButtonChecked: true,
            databasesButtonChecked: false,
            webFrameworksButtonChecked: false
        }
    },
    watch: {
        //runs every time dataset prop changes
        dataset() {
            console.log("dataset updated")
            this.update();
        },
    },
    mounted() {
        this.init()

    },
    methods: {
        init() {
            this.createRadioSelectionHandlers();
            // console.log("layout:", d3.layout)
            console.log("initiated but not updated")
            //do the world
            this.update();
        },
        update() {
            console.log("received data: ", this.dataset)
            
            // console.log(this.dataset.countryName)
            d3.select("#actual-word-cloud").remove();
            if (this.title.includes("Lao People's")) {
                d3.select("#die").html("Popularity of Languages In Laos")
            }
            else {
                d3.select("#die").html("Popularity of Languages In " + this.title)
            }
            
            //check which checbox was checked previously
            if (this.languagesButtonChecked) {
                d3.select("#languages").property("checked", true);
                this.renderLanguageCloud();
            }
            else if (this.databasesButtonChecked) {
                this.renderDatabaseCloud();
            }
            else if (this.webFrameworksButtonChecked) {
                this.renderWebFrameworkCloud();
            }
            else {
                console.log("show no cloud")
            }
            
        },
        renderLanguageCloud() {
            if (this.title.includes("Lao People's")) {
                d3.select("#die").html("Popularity of Languages In Laos")
            }
            else {
                d3.select("#die").html("Popularity of Languages In " + this.title)
            }
            console.log("renderning world oucld")
            //replace this with dataset later
            // var myWords = [{word: "Running", size: "10"}, {word: "Surfing", size: "20"}, {word: "Climbing", size: "50"}, {word: "Kiting", size: "30"}, {word: "Sailing", size: "20"}, {word: "Snowboarding", size: "60"} ]
            let datasetCpy = this.dataset[0];
            console.log("curr dataset: ", datasetCpy)
            // set the dimensions and margins of the graph
            var margin = {top: 10, right: 10, bottom: 10, left: 10},
                width = 450 - margin.left - margin.right,
                height = 450 - margin.top - margin.bottom;

            // append the svg object to the body of the page
            var svg = d3.select("#word-cloud-svg")
            .append("svg")
                .attr("width", 800)
                .attr("height", 400)
                .attr("id", "actual-word-cloud")
                // .attr("transform", "translate(0, 1000)")
            // .append("g")
                // .attr("transform", "translate(700,0)");

            // Constructs a new cloud layout instance. It run an algorithm to find the position of words that suits your requirements
            // Wordcloud features that are different from one word to the other must be here
            var layout = cloud()
                .size([400, 300])
                .words(datasetCpy.map(function(d) { return {language: d.language, popularity:d.popularity}; }))
                .padding(5)        //space between words
                .rotate(function() { return ~~(Math.random() * 2) })
                .fontSize(function(d) { return d.popularity; })      // font size of words
                .on("end", draw);
                layout.start();

            // This function takes the output of 'layout' above and draw the words
            // Wordcloud features that are THE SAME from one word to the other can be here
            function draw(words) {
                svg
                    .append("g")
                    .attr("transform", "translate(400, 200)")
                    .selectAll("text")
                        .data(words)
                    .enter().append("text")
                        .style("font-size", function(d) { 
                            if (d.popularity < 10) {
                                return 10;
                            }
                            if (d.popularity > 50) {
                                return 50;
                            }
                            return d.popularity; 
                            
                        })
                        .style("opacity", function(d) {
                            // if (d.popularity < 20) {
                            //     return 1.00
                            // }
                            if (d.popularity > 30) {
                                return 0.30
                            }
                            if (d.popularity > 20 && d.popularity < 30) {
                                return 0.40
                            }
                            return (100 - d.popularity) / 100
                        })
                        .style("font-weight", function(d) {
                            if (d.popularity < 20) {
                                return 700
                            }
                            return 100
                        })
                        .style("fill", "black") //#69b3a2
                        .attr("text-anchor", "middle")
                        .style("font-family", "Arial")
                        .attr("transform", function(d) {
                        return "translate(" + [d.x, d.y] + ")rotate(" + d.rotate + ")";
                        })
                        .text(function(d) { return d.language; });
                console.log("render finished")
                // d3.select("#actual-word-cloud")
                //     .attr("transform", "translate(2000, 0)")
            }
        },

        renderDatabaseCloud() {
            if (this.title.includes("Lao People's")) {
                d3.select("#die").html("Popularity of Databases In Laos")
            }
            else {
                d3.select("#die").html("Popularity of Databases In " + this.title)
            }
            console.log("renderning world oucld")
            //replace this with dataset later
            // var myWords = [{word: "Running", size: "10"}, {word: "Surfing", size: "20"}, {word: "Climbing", size: "50"}, {word: "Kiting", size: "30"}, {word: "Sailing", size: "20"}, {word: "Snowboarding", size: "60"} ]
            let datasetCpy = this.dataset[1];
            console.log("curr dataset: ", datasetCpy)
            // set the dimensions and margins of the graph
            var margin = {top: 10, right: 10, bottom: 10, left: 10},
                width = 450 - margin.left - margin.right,
                height = 450 - margin.top - margin.bottom;

            // append the svg object to the body of the page
            var svg = d3.select("#word-cloud-svg").append("svg")
                .attr("width", 800)
                .attr("height", 500)
                .attr("id", "actual-word-cloud")
            // .append("g")
            //     .attr("transform", "translate(200,0)");

            // Constructs a new cloud layout instance. It run an algorithm to find the position of words that suits your requirements
            // Wordcloud features that are different from one word to the other must be here
            var layout = cloud()
                .size([300, 300])
                .words(datasetCpy.map(function(d) { return {database: d.database, popularity:d.popularity}; }))
                .padding(10)        //space between words
                .rotate(function() { return ~~(Math.random() * 2) })
                .fontSize(function(d) { return d.popularity; })      // font size of words
                .on("end", draw);
                layout.start();

            // This function takes the output of 'layout' above and draw the words
            // Wordcloud features that are THE SAME from one word to the other can be here
            function draw(words) {
                svg
                    .append("g")
                    .attr("transform", "translate(400, 200)")
                    .selectAll("text")
                        .data(words)
                    .enter().append("text")
                        .style("font-size", function(d) { 
                            if (d.popularity < 10) {
                                return 10;
                            }
                            if (d.popularity > 50) {
                                return 50;
                            }
                            return d.popularity; 
                            
                        })
                        .style("opacity", function(d) {
                            // if (d.popularity < 20) {
                            //     return 1.00
                            // }
                            if (d.popularity > 30) {
                                return 0.30
                            }
                            if (d.popularity > 20 && d.popularity < 30) {
                                return 0.40
                            }
                            return (100 - d.popularity) / 100
                        })
                        .style("font-weight", function(d) {
                            if (d.popularity < 20) {
                                return 700
                            }
                            return 100
                        })
                        .style("fill", "black") //#69b3a2
                        .attr("text-anchor", "middle")
                        .style("font-family", "Arial")
                        .attr("transform", function(d) {
                        return "translate(" + [d.x, d.y] + ")rotate(" + d.rotate + ")";
                        })
                        .text(function(d) { return d.database; });
                console.log("render finished")
                d3.select("#actual-word-cloud")
                    .attr("transform", "translate(2000, 0)")
            }
        },

        renderWebFrameworkCloud() {
            if (this.title.includes("Lao People's")) {
                d3.select("#die").html("Popularity of Web Frameworks In Laos")
            }
            else {
                d3.select("#die").html("Popularity of Web Frameworks In " + this.title)
            }
            console.log("renderning world oucld")
            //replace this with dataset later
            // var myWords = [{word: "Running", size: "10"}, {word: "Surfing", size: "20"}, {word: "Climbing", size: "50"}, {word: "Kiting", size: "30"}, {word: "Sailing", size: "20"}, {word: "Snowboarding", size: "60"} ]
            let datasetCpy = this.dataset[2];
            console.log("curr dataset: ", datasetCpy)
            // set the dimensions and margins of the graph
            var margin = {top: 10, right: 10, bottom: 10, left: 10},
                width = 450 - margin.left - margin.right,
                height = 450 - margin.top - margin.bottom;

            // append the svg object to the body of the page
            var svg = d3.select("#word-cloud-svg")
                .append("svg")
                .attr("width", 800)
                .attr("height", 500)
                .attr("id", "actual-word-cloud")
                // .attr("translate", "transform(0, 0)")
                // .attr("fill", "black")
            // .append("g")
            //     .attr("transform", "translate(0,0)")
                // .attr("fill", "black")
            // Constructs a new cloud layout instance. It run an algorithm to find the position of words that suits your requirements
            // Wordcloud features that are different from one word to the other must be here
            var layout = cloud()
                .size([600, 200])
                .words(datasetCpy.map(function(d) { return {language: d.webFramework, popularity:d.popularity}; }))
                .padding(10)        //space between words
                .rotate(function() { return ~~(Math.random() * 2) })
                .fontSize(function(d) { return d.popularity; })      // font size of words
                .on("end", draw);
                layout.start();

            // This function takes the output of 'layout' above and draw the words
            // Wordcloud features that are THE SAME from one word to the other can be here
            function draw(words) {
                svg
                    .append("g")
                    .attr('id', 'word-cloud-group')
                    .attr("transform", "translate(400, 200)")
                    .selectAll("text")
                        .data(words)
                    .enter().append("text")
                        .style("font-size", function(d) { 
                            if (d.popularity < 10) {
                                return 10;
                            }
                            if (d.popularity > 50) {
                                return 50;
                            }
                            return d.popularity; 
                            
                        })
                        .style("opacity", function(d) {
                            // if (d.popularity < 20) {
                            //     return 1.00
                            // }
                            if (d.popularity > 30) {
                                return 0.30
                            }
                            if (d.popularity > 20 && d.popularity < 30) {
                                return 0.40
                            }
                            return (100 - d.popularity) / 100
                        })
                        .style("font-weight", function(d) {
                            if (d.popularity < 20) {
                                return 700
                            }
                            return 100
                        })
                        .style("fill", "black") //#69b3a2
                        .attr("text-anchor", "middle")
                        .style("font-family", "Arial")
                        .attr("transform", function(d) {
                        return "translate(" + [d.x, d.y] + ")rotate(" + d.rotate + ")";
                        })
                        .text(function(d) { return d.language; });
                console.log("render finished")
                // d3.select("#actual-word-cloud")
                //     .attr("transform", "translate(2000, 0)")
                // d3.select("#word-cloud-group")
                //     .append("rect")
                //     .attr("fill", "red")
                //     .attr("height", "100")
                //     .attr("width", "100")
                //     .attr("translate", "transform(-100,0)")
            }
        },
        createRadioSelectionHandlers() {
            d3.select("#languages").on("click",() => {
                // console.log("languages clicked")
                if (this.languagesButtonChecked) {
                    // this.languagesButtonChecked = false;
                    // d3.select("#languages").property("checked", false);
                    // console.log("unchecked")
                    // this.checkRadioButtonSelectionStatus();
                }
                else {
                    // console.log("languages checked")
                    this.languagesButtonChecked = true;
                    d3.select("#languages").property("checked", true);
                    this.databasesButtonChecked = false;
                    this.webFrameworksButtonChecked = false;
                    d3.select("#databases").property("checked", false);
                    d3.select("#web-frameworks").property("checked", false);
                    d3.select("#actual-word-cloud").remove();
                    this.renderLanguageCloud();
                }
                
            })

            d3.select("#databases").on("click",() => {
                // console.log("database clicked")
                if (this.databasesButtonChecked) {
                    // this.databaseButtonChecked = false;
                    // d3.select("#arrival-vs-departure").property("checked", false);

                    // console.log("unchecked");
                    // this.checkRadioButtonSelectionStatus();
                }
                else {
                    // console.log("database checked")
                    this.databasesButtonChecked = true;
                    d3.select("#databases").property("checked", true);
                    this.languagesButtonChecked = false;
                    this.webFrameworksButtonChecked = false;
                    d3.select("#languages").property("checked", false);
                    d3.select("#web-frameworks").property("checked", false);
                    d3.select("#actual-word-cloud").remove();
                    this.renderDatabaseCloud();

                }
                
            })

            d3.select("#web-frameworks").on("click",() => {
                // console.log("web-framework clicked")
                if (this.webFrameworksButtonChecked) {
                    // this.databaseButtonChecked = false;
                    // d3.select("#arrival-vs-departure").property("checked", false);

                    // console.log("unchecked");
                    // this.checkRadioButtonSelectionStatus();
                }
                else {
                    // console.log("fra eowrk checked")
                    this.webFrameworksButtonChecked = true;
                    d3.select("#web-frameworks").property("checked", true);
                    this.languagesButtonChecked = false;
                    this.databasesButtonChecked = false;
                    d3.select("#languages").property("checked", false);
                    d3.select("#databases").property("checked", false);
                    d3.select("#actual-word-cloud").remove();
                    this.renderWebFrameworkCloud();

                }
                
            })
        },

    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
/* Add your CSS here */
#word-cloud-svg {
    /* margin-top: 10px; */
    width: 100%;
    height: 100%;
    /* border: 2px black solid; */
}
/* #actual-word-cloud {
    filter: drop-shadow(1px 1px 0px purple) drop-shadow(-1px 1px 0px purple) drop-shadow(1px -1px 0px purple) drop-shadow(-1px -1px 0px purple);
} */
#word-cloud-container {
    /* border: 2px rgb(255, 102, 0) solid;
    background-color: black;
    position: relative; */
    /* width: 100%;
    height: 100%; */
}
#view-selection-wrapper {
    font-size: 12px;
    font-weight: bold;
    position: absolute;
    top: 1px;
    right: 0px;
    display: flex;
    flex-direction: column;
    /* visibility: hidden;  */
    border: 2px rgb(150, 150, 150) solid;
    width: 125px;
    background-color: whitesmoke;
}
</style>