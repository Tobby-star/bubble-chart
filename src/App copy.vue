<template>
  <div id="app">
    <svg id="bubble-chart" width="954" height="450" />
  </div>
</template>

<script>
import * as d3 from "d3";
import render from "d3-render";
export default {
  name: "App",
  components: {},
  data() {
    return {
      data: null,
      covidData: null,
      colours: {
        pink: "#D8352A",
        red: "#D8352A",
        blue: "#48509E",
        green: "#02A371",
        yellow: "#F5A623",
        hyperGreen: "#19C992",
        purple: "#B1B4DA",
        orange: "#F6E7AD",
        charcoal: "#383838",
      },
      countries: null,
    };
  },
  methods: {
    drawType() {
      const continents = [
          {
            id: "AF",
            name: "Africa",
            fill: this.colours.purple,
            colour: this.colours.charcoal,
          },
          {
            id: "AS",
            name: "Asia",
            fill: this.colours.yellow,
            colour: this.colours.charcoal,
          },
          {
            id: "EU",
            name: "Europe",
            fill: this.colours.blue,
            // colour: this.colours.charcoal
          },
          {
            id: "NA",
            name: "North America",
            fill: this.colours.pink,
          },
          {
            id: "OC",
            name: "Oceania",
            fill: this.colours.orange,
            colour: this.colours.charcoal,
          },
          {
            id: "SA",
            name: "South America",
            fill: this.colours.green,
            // colour: colours.charcoal
            // fill: colours.blue
          },
        ];
      console.log("begin");

      const circleComponent = ({ r, cx, cy, fill, duration }) => {
        return {
          append: "circle",
          r,
          cx,
          cy,
          fill,
          duration,
        };
      };

      const textComponent = ({
        key,
        text,
        x = 0,
        y = 0,
        fontWeight = "bold",
        fontSize = "12px",
        textAnchor = "middle",
        fillOpacity = 1,
        colour,
        r,
        duration = 1000,
      }) => {
        return {
          append: "text",
          key,
          text,
          x,
          y,
          textAnchor,
          fontFamily: "sans-serif",
          fontWeight,
          fontSize,
          fillOpacity: { enter: fillOpacity, exit: 0 },
          fill: colour,
          duration,
          style: {
            pointerEvents: "none",
          },
        };
      };

      const format = (value) => {
        const newValue = d3.format("0.2s")(value);
        if (newValue.indexOf("m") > -1) {
          return parseInt(newValue.replace("m", "")) / 1000;
        }
        return newValue;
      };

      const labelComponent = ({ isoCode, countryName, value, r, colour }) => {
        // Don't show any text for radius under 12px
        if (r < 12) {
          return [];
        }
        //console.log(r);
        const circleWidth = r * 2;
        const nameWidth = countryName.length * 10;
        const shouldShowIso = nameWidth > circleWidth;
        const newCountryName = shouldShowIso ? isoCode : countryName;
        const shouldShowValue = r > 18;

        let nameFontSize;

        if (shouldShowValue) {
          nameFontSize = shouldShowIso ? "10px" : "12px";
        } else {
          nameFontSize = "8px";
        }

        return [
          textComponent({
            key: isoCode,
            text: newCountryName,
            fontSize: nameFontSize,
            y: shouldShowValue ? "-0.2em" : "0.3em",
            fillOpacity: 1,
            colour,
          }),
          ...(shouldShowValue
            ? [
                textComponent({
                  key: isoCode,
                  text: format(value),
                  fontSize: "10px",
                  y: shouldShowIso ? "0.9em" : "1.0em",
                  fillOpacity: 0.7,
                  colour,
                }),
              ]
            : []),
        ];
      };

      this.$axios.get("country.csv").then((res) => {
        this.countries = d3.csvParse(res.data);
        //console.log(this.countries);
      });

      const labelComponentRadius = 50;
      const bubbleComponentRadius = 50;

      const width = 954;
      const height = 450;

      const bubbleComponent = ({
        name,
        id,
        value,
        r,
        x,
        y,
        fill,
        colour,
        duration = 1000,
      }) => {
        return {
          append: "g",
          key: id,
          transform: {
            enter: `translate(${x + 1},${y + 1})`,
            exit: `translate(${width / 2},${height / 2})`,
          },
          duration,
          delay: Math.random() * 300,
          children: [
            circleComponent({ key: id, r, fill, duration }),
            ...labelComponent({
              key: id,
              countryName: name,
              isoCode: id,
              value,
              r,
              colour,
              duration,
            }),
          ],
        };
      };

      const data = [
        {
          name: "Brazil",
          id: "BRA",
          value: 526447.0,
          fill: "#02A371",
          colour: "white",
        },
        {
          name: "Italy",
          id: "ITA",
          value: 233197.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "India",
          id: "IND",
          value: 198706.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Germany",
          id: "DEU",
          value: 182028.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Iran",
          id: "IRN",
          value: 154445.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "France",
          id: "FRA",
          value: 152091.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Chile",
          id: "CHL",
          value: 105159.0,
          fill: "#02A371",
          colour: "white",
        },
        {
          name: "Mexico",
          id: "MEX",
          value: 93435.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Canada",
          id: "CAN",
          value: 91694.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "China",
          id: "CHN",
          value: 84154.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Pakistan",
          id: "PAK",
          value: 76398.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Belgium",
          id: "BEL",
          value: 59029.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Bangladesh",
          id: "BGD",
          value: 49534.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Netherlands",
          id: "NLD",
          value: 46545.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Belarus",
          id: "BLR",
          value: 43403.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Ecuador",
          id: "ECU",
          value: 39994.0,
          fill: "#02A371",
          colour: "white",
        },
        {
          name: "Colombia",
          id: "COL",
          value: 30493.0,
          fill: "#02A371",
          colour: "white",
        },
        {
          name: "Kuwait",
          id: "KWT",
          value: 27762.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Indonesia",
          id: "IDN",
          value: 26940.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Egypt",
          id: "EGY",
          value: 26384.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Ireland",
          id: "IRL",
          value: 25062.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Dominican Republic",
          id: "DOM",
          value: 17572.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Argentina",
          id: "ARG",
          value: 17402.0,
          fill: "#02A371",
          colour: "white",
        },
        {
          name: "Israel",
          id: "ISR",
          value: 17219.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Japan",
          id: "JPN",
          value: 16930.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Austria",
          id: "AUT",
          value: 16663.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Afghanistan",
          id: "AFG",
          value: 15750.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Oman",
          id: "OMN",
          value: 12223.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Bahrain",
          id: "BHR",
          value: 11804.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Denmark",
          id: "DNK",
          value: 11699.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Kazakhstan",
          id: "KAZ",
          value: 11571.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Nigeria",
          id: "NGA",
          value: 10578.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Bolivia",
          id: "BOL",
          value: 10531.0,
          fill: "#02A371",
          colour: "white",
        },
        {
          name: "Armenia",
          id: "ARM",
          value: 10009.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Algeria",
          id: "DZA",
          value: 9513.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Czech Republic",
          id: "CZE",
          value: 9302.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Norway",
          id: "NOR",
          value: 8411.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Moldova",
          id: "MDA",
          value: 8360.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Ghana",
          id: "GHA",
          value: 8070.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Malaysia",
          id: "MYS",
          value: 7857.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Morocco",
          id: "MAR",
          value: 7833.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Australia",
          id: "AUS",
          value: 7204.0,
          fill: "#F6E7AD",
          colour: "#383838",
        },
        {
          name: "Finland",
          id: "FIN",
          value: 6885.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Iraq",
          id: "IRQ",
          value: 6868.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Cameroon",
          id: "CMR",
          value: 6397.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Azerbaijan",
          id: "AZE",
          value: 5662.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Honduras",
          id: "HND",
          value: 5362.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Guatemala",
          id: "GTM",
          value: 5336.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Hungary",
          id: "HUN",
          value: 3921.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Guinea",
          id: "GIN",
          value: 3844.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Congo",
          id: "COD",
          value: 3194.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Cote d'Ivoire",
          id: "CIV",
          value: 2951.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Greece",
          id: "GRC",
          value: 2918.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Gabon",
          id: "GAB",
          value: 2655.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "El Salvador",
          id: "SLV",
          value: 2582.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Bosnia and Herzegovina",
          id: "BIH",
          value: 2523.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Bulgaria",
          id: "BGR",
          value: 2513.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Macedonia",
          id: "MKD",
          value: 2315.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Croatia",
          id: "HRV",
          value: 2246.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Haiti",
          id: "HTI",
          value: 2226.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Cuba",
          id: "CUB",
          value: 2083.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Kenya",
          id: "KEN",
          value: 2021.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Estonia",
          id: "EST",
          value: 1870.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Kyrgyz Republic",
          id: "KGZ",
          value: 1845.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Nepal",
          id: "NPL",
          value: 1798.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Lithuania",
          id: "LTU",
          value: 1678.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Guinea-Bissau",
          id: "GNB",
          value: 1339.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Mali",
          id: "MLI",
          value: 1315.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Equatorial Guinea",
          id: "GNQ",
          value: 1306.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Ethiopia",
          id: "ETH",
          value: 1257.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Lebanon",
          id: "LBN",
          value: 1233.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "New Zealand",
          id: "NZL",
          value: 1154.0,
          fill: "#F6E7AD",
          colour: "#383838",
        },
        {
          name: "Albania",
          id: "ALB",
          value: 1143.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Costa Rica",
          id: "CRI",
          value: 1084.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Latvia",
          id: "LVA",
          value: 1071.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Central African Republic",
          id: "CAF",
          value: 1069.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Niger",
          id: "NER",
          value: 958.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Burkina Faso",
          id: "BFA",
          value: 881.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Madagascar",
          id: "MDG",
          value: 826.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Georgia",
          id: "GEO",
          value: 796.0,
          fill: "#48509E",
          colour: "white",
        },
        {
          name: "Chad",
          id: "TCD",
          value: 790.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Nicaragua",
          id: "NIC",
          value: 759.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Jordan",
          id: "JOR",
          value: 746.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Congo the",
          id: "COG",
          value: 611.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Jamaica",
          id: "JAM",
          value: 588.0,
          fill: "#D8352A",
          colour: "white",
        },
        {
          name: "Mauritania",
          id: "MRT",
          value: 530.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Malawi",
          id: "MWI",
          value: 336.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Mauritius",
          id: "MUS",
          value: 335.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Liberia",
          id: "LBR",
          value: 296.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Mozambique",
          id: "MOZ",
          value: 254.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Benin",
          id: "BEN",
          value: 243.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Myanmar",
          id: "MMR",
          value: 228.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Mongolia",
          id: "MNG",
          value: 185.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Libya",
          id: "LBY",
          value: 168.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Cambodia",
          id: "KHM",
          value: 125.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Angola",
          id: "AGO",
          value: 86.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Burundi",
          id: "BDI",
          value: 63.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Eritrea",
          id: "ERI",
          value: 39.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Botswana",
          id: "BWA",
          value: 38.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Gambia",
          id: "GMB",
          value: 25.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Namibia",
          id: "NAM",
          value: 25.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
        {
          name: "Laos",
          id: "LAO",
          value: 19.0,
          fill: "#F5A623",
          colour: "#383838",
        },
        {
          name: "Lesotho",
          id: "LSO",
          value: 2.0,
          fill: "#B1B4DA",
          colour: "#383838",
        },
      ];

      const pack = (data) =>
        d3
          .pack()
          .size([width - 2, height - 2])
          .padding(2)(d3.hierarchy({ children: data }).sum((d) => d.value));

      const renderBubbleChart = (selection, data) => {
        const root = pack(data);

        const renderData = root.leaves().map((d) => {
          return bubbleComponent({
            id: d.data.id,
            name: d.data.name,
            value: d.data.value,
            r: d.r,
            x: d.x,
            y: d.y,
            fill: d.data.fill,
            colour: d.data.colour,
          });
        });
        return render(selection, renderData);
      };

      const renderBubbleChartContainer = (data) => {
        return renderBubbleChart("#bubble-chart", data);
      };

      const getIsoDate = (date) => {
        const date1 = new Date(date);
        return date1.toISOString().split("T")[0];
      };

      this.$axios.get("data.csv").then((res) => {
        this.covidData = d3.csvParse(res.data);
        console.log(this.covidData);
        const covidData1 = this.covidData
        const countries1 = this.countries;

        function getDataBy({
          dataKey,
          date,
          selectedContinents,
          order,
          minimumPopulation,
        }) {
          return (
            covidData1
              .filter((d) => d)
              .filter((d) => d.iso_code !== "OWID_WRL")
              // Filter out countries with populations under 1 million
              .filter((d) => d.population > parseInt(minimumPopulation))
              .filter((d) => {
                return d.date === getIsoDate(date);
              })
              .filter((d) => d[dataKey])
              .filter((d) => {
                const country = countries1.find((c) => c.iso3 === d.iso_code);
                const continent = continents.find((c, i) => {
                  if (!country) {
                    return false;
                  }

                  return c.id === country.continentCode;
                });

                if (!continent) {
                  return false;
                }

                return selectedContinents.includes(continent.id);
              })
              .map((d) => {
                const country = countries1.find((c) => c.iso3 === d.iso_code);
                const continent = continents.find(
                  (c) => c.id === country.continentCode
                );

                const name = country.shortName || country.name;

                return {
                  name,
                  id: country.iso3,
                  value: d[dataKey],
                  fill: continent.fill,
                  colour: continent.colour || "white",
                };
              })
              .filter((d) => d.value !== "0.0")
              .sort(function (a, b) {
                const mod = order === "desc" ? -1 : 1;
                return mod * (a.value - b.value);
              })
          );
        }

        const dataKey = "total_cases";
        const dates = d3.map(covidData1, (d) => d.date).keys();
        
        const selectedContinents = ["AF", "AS", "EU", "NA", "OC", "SA"];
        const minimumPopulation = 0;
        const order = "desc";
        console.log(this.covidData);
        for (var i = 0; i < dates.length; i++) {
          (function (i) {
            setTimeout(function () {
              const date = dates[i];
              console.log(date);
              const data = getDataBy({
                dataKey,
                date,
                selectedContinents,
                minimumPopulation,
                order,
              });
              renderBubbleChartContainer(data);
            }, 2000 * i);
          })(i);
        }
      });
    },
  },
  mounted() {
    this.drawType();
  },
};
</script>

<style>
</style>
