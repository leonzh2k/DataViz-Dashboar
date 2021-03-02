<!-- This file will be used to layout and connect your views -->
<template>
  <div id="app">
      <div id="loading-text">Loading...</div>
      <!-- <script type="application/javascript" defer src="https://d3js.org/d3.v6.js"></script> -->
      <!-- This is only a basic setup to display your views. Use HTML and CSS to create your layout using these basic commands -->
      <BubbleChart 
        id="bubble-chart"
        v-if='overviewDataset' 
        :data='overviewDataset' 
        xAttribute="avgYearsCodePro"
        yAttribute="avgIncomeUSD"
        :width='1000' 
        :height='1000'
        @data-point-selected="dataPointSelected"
      />
      <WordCloud 
        id="word-cloud"
        :dataset='detailView1Dataset'
        :title='detailView1Title'
        v-if='overviewDataset' 
      />
      <BarChart 
        id="bar-chart"
        v-if='overviewDataset' 
        :dataset='detailView2Dataset'
        :title='detailView2Title'
      />
  </div>
</template>

<script>
// Try to use descriptive names like 'OverviewScatterPlot' or 'DetailLineChart'
import * as d3 from 'd3'
// const d3 = Object.assign({}, require("d3-format"), require("d3-geo"), require("d3-geo-projection"), require("d3-cloud"));
import BubbleChart from './components/BubbleChart.vue'
import WordCloud from './components/WordCloud.vue'
import BarChart from './components/BarChart.vue'


export default {
  name: 'App',
  components: {
    BubbleChart,
    WordCloud,
    BarChart,
  },
  mounted() {
    console.log("mounted")
    // let d3Script = document.createElement('script')
    // d3Script.setAttribute('src', 'https://d3js.org/d3.v6.js')
    // document.head.appendChild(d3Script)
  },
  data() {
    return {
      overviewDataset: null,
      detailView1Dataset: null,
      detailView1Title: "The World",
      detailView2Dataset: null,
      detailView2Title: "The World",
      subsetViewCountry: null,
    }
  },
  created() {
    // filter data here
    let countriesContinents;
    // let languages = [];
    // let educationTypes = [];
    d3.csv('./data/Countries-Continents.csv', d3.autoType)
      .then(data => {
        
        countriesContinents = data;
        // console.log("countries continetns", data)
      })

    d3.csv('./data/survey_results_public.csv', d3.autoType)
      .then(data => {
        // console.log("data: ", data)
        let uniqueCountries = [];
        let totalPeopleWhoUsedALanguage = 0;
        let totalPeopleWhoUsedADatabase = 0;
        let totalPeopleWhoUsedAWebFramework = 0;
        data.forEach(element => {
          // educationTypes.push(element.EdLevel + ";")
          if (!uniqueCountries.includes(element.Country)) {
            uniqueCountries.push(element.Country)
          }
          //get total users
          if (element.LanguageWorkedWith != "NA") {
            totalPeopleWhoUsedALanguage++;
          }
          if (element.DatabaseWorkedWith != "NA") {
            totalPeopleWhoUsedADatabase++;
          }
          if (element.WebframeWorkedWith != "NA") {
            totalPeopleWhoUsedAWebFramework++;
          }
        });
        // console.log("people who put down a language: ", totalPeopleWhoUsedALanguage)
        // console.log("people who put down a database: ", totalPeopleWhoUsedADatabase)
        // console.log("people who put down a webframwork: ", totalPeopleWhoUsedAWebFramework)
        // console.log("webFrameworks 1: ", webFrameworks)
        // educationTypes = educationTypes.join('')
        // console.log("webFrameworks 2: ", webFrameworks);
        // educationTypes = educationTypes.split(';')
        // console.log("webFrameworks 3: ", webFrameworks);
        let uniqueLanguages = ["C#", "HTML/CSS", "JavaScript", "Swift", "Objective-C", "Python", "Ruby", "SQL", "Java", "PHP", "C", "TypeScript", "Bash/Shell/PowerShell", "Kotlin", "R", "VBA", "Perl", "Scala", "C++", "Go", "Haskell", "Rust", "Dart", "Julia", "Assembly"];
        let uniqueDatabases = [
          "Elasticsearch", 
          "Microsoft SQL Server", 
          "Oracle",
          "MySQL",
          "PostgreSQL",
          "Redis",
          "SQLite",
          "MariaDB",
          "Firebase",
          "MongoDB",
          "IBM DB2",
          "DynamoDB",
          "Cassandra",
          "Couchbase"
        ];
        let uniqueWebFrameworks = ["ASP.NET", "ASP.NET Core", "Ruby on Rails", "Flask", "jQuery", "Angular", "Angular.js", "Django", "React.js", "Vue.js", "Gatsby", "Spring", "Express", "Symfony", "Laravel", "Drupal"];
        let uniqueEducationTypes = ["Master’s degree (M.A., M.S., M.Eng., MBA, etc.)", "Bachelor’s degree (B.A., B.S., B.Eng., etc.)", "Secondary school (e.g. American high school, German Realschule or Gymnasium, etc.)", "Professional degree (JD, MD, etc.)", "Some college/university study without earning a degree", "Associate degree (A.A., A.S., etc.)", "Other doctoral degree (Ph.D., Ed.D., etc.)", "Primary/elementary school", "I never completed any formal education"];
        // educationTypes.forEach((educationType) => {
        //   if (!uniqueEducationTypes.includes(educationType)) {
        //     uniqueEducationTypes.push(educationType)
        //   }
        // })
        // uniqueEducationTypes = uniqueEducationTypes.filter(devType => devType != "NA" && devType != "")
        // console.log("Unique devTypes: ", uniqueEducationTypes)
        let dataForEachCountry = uniqueCountries.map(d => {
          return {
            countryName: d,
            countryContinent: null,
            numberOfRespondents: 0,
            numberOfRespondentsWithValidIncome: 0,
            numberOfRespondentsWithValidYearsCodePro: 0,
            numberOfRespondentsWithValidLanguage: 0,
            numberOfRespondentsWithValidDatabase: 0,
            numberOfRespondentsWithValidWebFramework: 0,
            avgAge1stCode: 0,
            avgYearsCodePro: 0,
            avgIncomeUSD: 0
          }
        });
        //add language, database, webframework frequencies to country data
        dataForEachCountry.forEach(d => {
          uniqueLanguages.forEach(language => {
            d[language] = 0
          })
          uniqueDatabases.forEach(database => {
            d[database] = 0
          })
          uniqueWebFrameworks.forEach(webFramework => {
            d[webFramework] = 0
          })
          uniqueEducationTypes.forEach(educationType => {
            d[educationType + " Total Income"] = 0
            d[educationType + " # Respondents"] = 0
            d[educationType + " Total Income (Men)"] = 0
            d[educationType + " # Respondents (Men)"] = 0
            d[educationType + " Total Income (Women)"] = 0
            d[educationType + " # Respondents (Women)"] = 0
          })
        })
        //assign countries to continents.
        dataForEachCountry.forEach(d => {
          countriesContinents.forEach(country => {
            if (d.countryName == country.Country) {
              d.countryContinent = country.Continent
            }
          })
        })

        data.forEach(element => {
          dataForEachCountry.forEach(country => {
            if (element.Country == country.countryName) {
              country.numberOfRespondents += 1;
              if (element.ConvertedComp == "NA") {
                
                country.avgIncomeUSD += 0;
              }
              else {
                country.numberOfRespondentsWithValidIncome += 1;
                country.avgIncomeUSD += element.ConvertedComp;
              }
              if (element.YearsCodePro == "NA" || element.YearsCodePro == "Less than 1 year") {
                country.avgYearsCodePro += 0;
              }
              else {
                if (element.YearsCodePro == "More than 50 years") {
                  country.avgYearsCodePro += 50;
                }
                else {
                  country.avgYearsCodePro += element.YearsCodePro;
                }
                country.numberOfRespondentsWithValidYearsCodePro += 1;
              }
              
              if (element.LanguageWorkedWith == "NA") {
                let die = 0;
                die++
              }
              else {
                country.numberOfRespondentsWithValidLanguage++;
                let languagesUsed = element.LanguageWorkedWith.split(";")
                languagesUsed.forEach(language => {
                  country[language]++;
                })
              }

              if (element.DatabaseWorkedWith == "NA") {
                let die = 0;
                die++
              }
              else {
                country.numberOfRespondentsWithValidDatabase++;
                let databasesUsed = element.DatabaseWorkedWith.split(";")
                databasesUsed.forEach(database => {
                  country[database]++;
                })
              }

              if (element.WebframeWorkedWith == "NA") {
                let die = 0;
                die++
              }
              else {
                country.numberOfRespondentsWithValidWebFramework++;
                let webFrameworkUsed = element.WebframeWorkedWith.split(";")
                webFrameworkUsed.forEach(webFramework => {
                  country[webFramework]++;
                })
              }
              //must have valid education level and income
              if (element.EdLevel == "NA" || element.ConvertedComp == "NA" || (element.Gender != "Man" && element.Gender != "Woman")) {
                let die = 0;
                die++
              }
              else {
                
                country[element.EdLevel + " Total Income"] += element.ConvertedComp;
                country[element.EdLevel + " # Respondents"]++;

                if (element.Gender == "Man") {
                  country[element.EdLevel + " Total Income (Men)"] += element.ConvertedComp;
                  country[element.EdLevel + " # Respondents (Men)"]++;
                }
                if (element.Gender == "Woman") {
                  country[element.EdLevel + " Total Income (Women)"] += element.ConvertedComp;
                  country[element.EdLevel + " # Respondents (Women)"]++;
                }
              }
              
            }
          })
        })

        dataForEachCountry.forEach(country => {
          uniqueEducationTypes.forEach(eduType => {
            country[eduType + " Avg Income"] = Math.floor(country[eduType + " Total Income"] / country[eduType + " # Respondents"]);
            country[eduType + " Avg Income (Men)"] = Math.floor(country[eduType + " Total Income (Men)"] / country[eduType + " # Respondents (Men)"]);
            country[eduType + " Avg Income (Women)"] = Math.floor(country[eduType + " Total Income (Women)"] / country[eduType + " # Respondents (Women)"]);
          })
          
          country.avgYearsCodePro = Math.floor(country.avgYearsCodePro / country.numberOfRespondentsWithValidYearsCodePro);
          country.avgIncomeUSD = Math.floor(country.avgIncomeUSD / country.numberOfRespondentsWithValidIncome);
        })

        //this is so smaller bubbles are rendered after bigger ones, so every bubble can be accessed
        dataForEachCountry.sort(function(country1, country2) {
          return country2.numberOfRespondentsWithValidYearsCodePro - country1.numberOfRespondentsWithValidYearsCodePro;
        })

        // dataForEachCountry.forEach(country => {
        //   if (country.country == "NA") {
        //     console.log("NA countr", country)
        //     // console.log(`${country.countryName} is in wrong format`)
        //   }
        // })
        // dataForEachCountry.forEach(country => {
        //   console.log(`${country.countryName}'s respondents: ${country.numberOfRespondents}`)
        // })
        d3.select("#loading-text").remove();
        dataForEachCountry = dataForEachCountry.filter(country => !isNaN(country.avgIncomeUSD) && !isNaN(country.avgYearsCodePro))
        this.overviewDataset = dataForEachCountry;
        console.log("final dataset: ", this.overviewDataset)
        this.loadInitialViews(data, dataForEachCountry, totalPeopleWhoUsedALanguage, totalPeopleWhoUsedADatabase, totalPeopleWhoUsedAWebFramework);
      })
  },
  methods: {
    loadInitialViews(originalData, dataset, totalPeopleWhoUsedALanguage, totalPeopleWhoUsedADatabase, totalPeopleWhoUsedAWebFramework) {
      //initially we display a "worldview" for the word cloud and bar chart?
      let uniqueLanguages = ["C#", "HTML/CSS", "JavaScript", "Swift", "Objective-C", "Python", "Ruby", "SQL", "Java", "PHP", "C", "TypeScript", "Bash/Shell/PowerShell", "Kotlin", "R", "VBA", "Perl", "Scala", "C++", "Go", "Haskell", "Rust", "Dart", "Julia", "Assembly"];
      let uniqueDatabases = [
        "Elasticsearch", 
        "Microsoft SQL Server", 
        "Oracle",
        "MySQL",
        "PostgreSQL",
        "Redis",
        "SQLite",
        "MariaDB",
        "Firebase",
        "MongoDB",
        "IBM DB2",
        "DynamoDB",
        "Cassandra",
        "Couchbase"
      ];
      let uniqueWebFrameworks = ["ASP.NET", "ASP.NET Core", "Ruby on Rails", "Flask", "jQuery", "Angular", "Angular.js", "Django", "React.js", "Vue.js", "Gatsby", "Spring", "Express", "Symfony", "Laravel", "Drupal"];
      // let totalLanguageUsers = 0, totalDatabaseUsers = 0,  totalWebFrameWorkUsers = 0;
      let languageData = {};
      uniqueLanguages.forEach(language => {
        languageData[language] = 0;
        // totalLanguageUsers
      })
      let databaseData = {};
      uniqueDatabases.forEach(database => {
        databaseData[database] = 0;
      })
      let webFrameworkData = {};
      uniqueWebFrameworks.forEach(framework => {
        webFrameworkData[framework] = 0;
      })
      dataset.forEach(country => {
        let keys = Object.keys(country);
        keys.forEach(key => {
          if (uniqueLanguages.includes(key)) {
            languageData[key] += country[key];
            return;
          }
          if (uniqueDatabases.includes(key)) {
            databaseData[key] += country[key];
            return;
          }
          if (uniqueWebFrameworks.includes(key)) {
            webFrameworkData[key] += country[key];
            return;
          }
        })
      })
      // console.log("final datas:")
      // console.log(languageData)
      // console.log(databaseData)
      // console.log(webFrameworkData)

      let finalLanguageData = uniqueLanguages.map(language => {
        return {
          language: language,
          popularity: (languageData[language] / totalPeopleWhoUsedALanguage) * 80
        }
      })
      let finalDatabaseData = uniqueDatabases.map(database => {
        return {
          database: database,
          popularity: (databaseData[database] / totalPeopleWhoUsedADatabase) * 100
        }
      })
      let finalWebFrameworkData = uniqueWebFrameworks.map(webFramework => {
        return {
          webFramework: webFramework,
          popularity: (webFrameworkData[webFramework] / totalPeopleWhoUsedAWebFramework) * 100
        }
      })
      this.detailView1Dataset = [finalLanguageData, finalDatabaseData, finalWebFrameworkData];
      console.log("final dataset: ", this.detailView1Dataset)
      this.detailView1Title = "The World";

      //create world bar chart
      let uniqueEducationTypes = ["Master’s degree (M.A., M.S., M.Eng., MBA, etc.)", "Bachelor’s degree (B.A., B.S., B.Eng., etc.)", "Secondary school (e.g. American high school, German Realschule or Gymnasium, etc.)", "Professional degree (JD, MD, etc.)", "Some college/university study without earning a degree", "Associate degree (A.A., A.S., etc.)", "Other doctoral degree (Ph.D., Ed.D., etc.)", "Primary/elementary school", "I never completed any formal education"];
      let educationData = uniqueEducationTypes.map(eduType => {
        return {
          educationType: eduType,
          AvgIncome: 0,
          totalRespondents: 0,
          AvgIncomeMen: 0,
          totalMenRespondents: 0,
          AvgIncomeWomen: 0,
          totalWomenRespondents: 0
        }
      })
      console.log("init edu data: ", educationData)
      originalData.forEach(d => {
        if (d.EdLevel == "NA" || d.ConvertedComp == "NA" || (d.Gender != "Man" && d.Gender != "Woman")) {
          let die = 0;
          die++;
        }
        else {
          educationData.forEach(eduType => {
            if (d.EdLevel == eduType.educationType) {
              eduType.AvgIncome += d.ConvertedComp;
              eduType.totalRespondents++;
              if (d.Gender == "Man") {
                eduType.AvgIncomeMen += d.ConvertedComp;
                eduType.totalMenRespondents++
              }
              if (d.Gender == "Woman") {
                eduType.AvgIncomeWomen += d.ConvertedComp;
                eduType.totalWomenRespondents++
              }
            }
          })
        }
        
      })
      educationData.forEach(eduType => {
        eduType.AvgIncome = Math.floor(eduType.AvgIncome / eduType.totalRespondents)
        eduType.AvgIncomeMen = Math.floor(eduType.AvgIncomeMen / eduType.totalMenRespondents)
        eduType.AvgIncomeWomen = Math.floor(eduType.AvgIncomeWomen / eduType.totalWomenRespondents)
      })
      educationData.forEach(eduType => {
        if (eduType.educationType.includes("Master’s")) {
          eduType.educationType = "Master’s"
          eduType.sortOrder = 3;
        }
        if (eduType.educationType.includes("Bachelor’s")) {
          eduType.educationType = "Bachelor’s"
          eduType.sortOrder = 4;
        }
        if (eduType.educationType.includes("Secondary school")) {
          eduType.educationType = "Secondary school"
          eduType.sortOrder = 7;
        }
        if (eduType.educationType.includes("Professional degree")) {
          eduType.educationType = "Professional"
          eduType.sortOrder = 2;
          
        }
        if (eduType.educationType.includes("Some college/university")) {
          eduType.educationType = "Some college/university"
          eduType.sortOrder = 6;
        }
        if (eduType.educationType.includes("Associate degree")) {
          eduType.educationType = "Associate"
          eduType.sortOrder = 5;
        }
        if (eduType.educationType.includes("doctoral")) {
          eduType.educationType = "Doctorate"
          eduType.sortOrder = 1;
        }
        if (eduType.educationType.includes("elementary school")) {
          eduType.educationType = "Primary school"
          eduType.sortOrder = 8;
        }
        if (eduType.educationType.includes("I never completed any formal education")) {
          eduType.educationType = "No formal education"
          eduType.sortOrder = 9;

        }
      })
      educationData.sort(function(edu1, edu2) {
          return edu1.sortOrder - edu2.sortOrder;
        })
      console.log("final edu data: ", educationData)
      this.detailView2Dataset = educationData;
      
    },
    //change props(data) that will be passed to bar chart
    dataPointSelected(d) {
      console.log("Data passed to app: ", d)
      let uniqueLanguages = ["C#", "HTML/CSS", "JavaScript", "Swift", "Objective-C", "Python", "Ruby", "SQL", "Java", "PHP", "C", "TypeScript", "Bash/Shell/PowerShell", "Kotlin", "R", "VBA", "Perl", "Scala", "C++", "Go", "Haskell", "Rust", "Dart", "Julia", "Assembly"];
      let uniqueDatabases = [
        "Elasticsearch", 
        "Microsoft SQL Server", 
        "Oracle",
        "MySQL",
        "PostgreSQL",
        "Redis",
        "SQLite",
        "MariaDB",
        "Firebase",
        "MongoDB",
        "IBM DB2",
        "DynamoDB",
        "Cassandra",
        "Couchbase"
      ];
      let uniqueWebFrameworks = ["ASP.NET", "ASP.NET Core", "Ruby on Rails", "Flask", "jQuery", "Angular", "Angular.js", "Django", "React.js", "Vue.js", "Gatsby", "Spring", "Express", "Symfony", "Laravel", "Drupal"];
      let dataKeys = Object.keys(d);
      let totalLanguageUsers = d.numberOfRespondentsWithValidLanguage, totalDatabaseUsers = d.numberOfRespondentsWithValidDatabase,  totalWebFrameWorkUsers = d.numberOfRespondentsWithValidWebFramework;
      dataKeys = dataKeys.filter(key => !key.includes("avg") && !key.includes("country") && !key.includes("number"))
      // console.log("datakeys: ", dataKeys)
      
      // dataKeys.forEach(key => {
      //   if (uniqueLanguages.includes(key)) {
      //     totalLanguageUsers += d[key]
      //   }
      //   if (uniqueDatabases.includes(key)) {
      //     totalDatabaseUsers += d[key]
      //   }
      //   if (uniqueWebFrameworks.includes(key)) {
      //     totalWebFrameWorkUsers += d[key]
      //   }
      // })
      let languageData = dataKeys.map(language => {
        if (uniqueLanguages.includes(language)) {
          return {
            language: language,
            popularity: (d[language] / totalLanguageUsers) * 80 //constant scale to make all words bigger
          }
        }
        return null;
      })
      let databaseData = dataKeys.map(database => {
        if (uniqueDatabases.includes(database)) {
          return {
            database: database,
            popularity: (d[database] / totalDatabaseUsers) * 100 //constant scale to make all words bigger
          }
        }
        return null;
      })
      let webFrameworkData = dataKeys.map(webFramework => {
        if (uniqueWebFrameworks.includes(webFramework)) {
          return {
            webFramework: webFramework,
            popularity: (d[webFramework] / totalWebFrameWorkUsers) * 100 //constant scale to make all words bigger
          }
        }
        return null;
      })
      languageData = languageData.filter(language => language != null)
      databaseData = databaseData.filter(database => database != null)
      webFrameworkData = webFrameworkData.filter(webFramework => webFramework != null)
      // console.log("language data: ", languageData)
      // console.log("database data: ", databaseData)
      // console.log("webframe data: ", webFrameworkData)

      // console.log("data to put in bar chart: ", subsetData)
      this.detailView1Dataset = [languageData, databaseData, webFrameworkData];
      // console.log("final dataset: ", this.detailView1Dataset)
      this.detailView1Title = d.countryName;
      

      //make the bar chart dataset
      let uniqueEducationTypes = [
        "Master’s degree (M.A., M.S., M.Eng., MBA, etc.)", 
        "Bachelor’s degree (B.A., B.S., B.Eng., etc.)", 
        "Secondary school (e.g. American high school, German Realschule or Gymnasium, etc.)", 
        "Professional degree (JD, MD, etc.)", 
        "Some college/university study without earning a degree", 
        "Associate degree (A.A., A.S., etc.)", 
        "Other doctoral degree (Ph.D., Ed.D., etc.)", 
        "Primary/elementary school", 
        "I never completed any formal education"
      ];
      let educationData = uniqueEducationTypes.map(eduType => {
        return {
          educationType: eduType,
          AvgIncome: 0,
          totalRespondents: 0,
          AvgIncomeMen: 0,
          totalMenRespondents: 0,
          AvgIncomeWomen: 0,
          totalWomenRespondents: 0
        }
      })
      // console.log("init edu data: ", educationData)
      dataKeys.forEach(key => {
        // console.log("key: ", key)
        educationData.forEach(eduType => {
          // console.log("eduType:", eduType.educationType)
          if (key == eduType.educationType + " Avg Income") {
            if (isNaN(d[key])) {
              ;
            }
            else {
              eduType.AvgIncome += d[key]
            }
            
            // eduType.totalRespondents++
          }
          if (key == eduType.educationType + " Avg Income (Men)") {
            if (isNaN(d[key])) {
              ;
            }
            else {
              eduType.AvgIncomeMen += d[key]

            }
            // eduType.totalMenRespondents++
          }
          if (key == eduType.educationType + " Avg Income (Women)") {
            if (isNaN(d[key])) {
              ;
            }
            else {
              eduType.AvgIncomeWomen += d[key]

            } 
            // eduType.totalWomenRespondents++
          }
          if (key == eduType.educationType + " # Respondents") {
            // eduType.AvgIncomeWomen += d[key]
            if (isNaN(d[key])) {
              ;
            }
            else {
              eduType.totalRespondents += d[key]
            }
            
          }
          if (key == eduType.educationType + " # Respondents (Men)") {
            // eduType.AvgIncomeWomen += d[key]
            if (isNaN(d[key])) {
              ;
            }
            else {
                        eduType.totalMenRespondents += d[key]


            }
          }
          if (key == eduType.educationType + " # Respondents (Women)") {
            // eduType.AvgIncomeWomen += d[key]
            if (isNaN(d[key])) {
              ;
            }
            else {
            eduType.totalWomenRespondents += d[key]

            }
          }
        })
      })

      educationData.forEach(eduType => {
        if (eduType.educationType.includes("Master’s")) {
          eduType.educationType = "Master’s"
          eduType.sortOrder = 3;
        }
        if (eduType.educationType.includes("Bachelor’s")) {
          eduType.educationType = "Bachelor’s"
          eduType.sortOrder = 4;
        }
        if (eduType.educationType.includes("Secondary school")) {
          eduType.educationType = "Secondary school"
          eduType.sortOrder = 7;
        }
        if (eduType.educationType.includes("Professional degree")) {
          eduType.educationType = "Professional"
          eduType.sortOrder = 2;
          
        }
        if (eduType.educationType.includes("Some college/university")) {
          eduType.educationType = "Some college/university"
          eduType.sortOrder = 6;
        }
        if (eduType.educationType.includes("Associate degree")) {
          eduType.educationType = "Associate"
          eduType.sortOrder = 5;
        }
        if (eduType.educationType.includes("doctoral")) {
          eduType.educationType = "Doctorate"
          eduType.sortOrder = 1;
        }
        if (eduType.educationType.includes("elementary school")) {
          eduType.educationType = "Primary school"
          eduType.sortOrder = 8;
        }
        if (eduType.educationType.includes("I never completed any formal education")) {
          eduType.educationType = "No formal education"
          eduType.sortOrder = 9;

        }
      })
      educationData.sort(function(edu1, edu2) {
          return edu1.sortOrder - edu2.sortOrder;
      })
      // educationData = educationData.filter(eduType => eduType.totalRespondents != 0)
      console.log("selected educationData: ", educationData)
      this.detailView2Title = d.countryName;

      console.log("final edu data: ", educationData)
      this.detailView2Dataset = educationData;
    }
  }//d[dataKeys[iter]],
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
  display: grid;
  position: relative;
  grid-template-columns: 50% 50%;
  grid-template-rows: 50% 50%;
  /* border: 2px pink solid; */
  background-color: rgb(221, 221, 221);
  /* padding-right: 0; */
  margin: 0;
  height: 770px;
  width: 100%;
}

#bubble-chart {
  /* stroke: black; */
  grid-row-start: 1;
  grid-row-end: 3;

  /* border: 2px red dotted; */
  margin: 0px;
  /* width: 800px; */
  height: 100%;
}

#word-cloud {
  grid-row-start: 1;
  grid-row-end: 1;
  /* stroke: black; */
  /* border: 2px green dotted; */
  margin: 0px;
  /* width: 800px; */
  height: 100%; 
}

#bar-chart {
  grid-row-start: 2;
  grid-row-end: 2;
  /* stroke: black; */
  /* border: 2px rgb(255, 255, 0) dotted; */
  margin: 0px;
  /* width: 800px; */
  height: 100%; 
  /* align-self: flex-end; */
}

/* https://stackoverflow.com/questions/19461521/how-to-center-an-element-horizontally-and-vertically */

#loading-text {
    font-size: 48px;
    position: absolute;
    top: 50%;
    left: 50%;
    -moz-transform: translateX(-50%) translateY(-50%);
    -webkit-transform: translateX(-50%) translateY(-50%);
    transform: translateX(-50%) translateY(-50%);
}
</style>
