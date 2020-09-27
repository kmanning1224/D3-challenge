# D3 Homework - Data Journalism and D3

![Newsroom](https://i.gyazo.com/18f2deba50650ddf346c430c2b214124.png)

## Process
### I started by setting up my SVG parameters for the D3 Plot

```
let svgWidth = 900;
let svgHeight = 600;

let margin = {
  top: 20,
  right: 40,
  bottom: 80,
  left: 100
};

let width = svgWidth - margin.left - margin.right;
let height = svgHeight - margin.top - margin.bottom;

let svg = d3.select("#scatter")
.append("svg")
.attr("width", svgWidth)
.attr("height", svgHeight);

let chartGroup = svg.append("g")
.attr("transform", `translate(${margin.left}, ${margin.top})`);

```

### From there I imported the CSV and created my Plot
```
//import data
d3.csv("assets/data/data.csv")
.then (function(censusdata) {
  censusdata.forEach(function(data) {
    data.poverty = +data.obesity;
    data.healthcare = +data.healthcare;
  });
  let xLinearScale = d3.scaleLinear()
  .domain([7, d3.max(censusdata, i => i.obesity)])
  .range([0, width]);

  let yLinearScale = d3.scaleLinear()
  .domain([0, d3.max(censusdata, i => i.healthcare)])
  .range([height,0]);

  let bottomaxis = d3.axisBottom(xLinearScale);
  let leftaxis = d3.axisLeft(yLinearScale);


  chartGroup.append("g")
  .attr("transform", `translate(0, ${height})`)
  .call(bottomaxis);
  chartGroup.append("g")
  .call(leftaxis);
  let circlesGroup = chartGroup.selectAll("circle")
  .data(censusdata)
  .enter()
  .append("circle")
  .attr("cx", i => xLinearScale(i.obesity))
  .attr("cy", i => yLinearScale(i.healthcare))
  .attr("r", 25)
  .attr("fill", "pink")
  .attr("opacity",".5");

  let circletext = chartGroup.selectAll()
  .data(censusdata)
  .enter()
  .append("text")
  .attr("x", i => xLinearScale(i.obesity))
  .attr("y", i => yLinearScale(i.healthcare))
  .style("font-size", "15px")
  .style("text-align", "circle")
  .style("background-color", "white")
  .style("fill", "black")
  .text(i => (i.abbr));

  //create tool tip
  let toolTip = d3.tip()
  .attr("class", "tooltip")
  .style("position", "center")
  .offset([80, -60])
  .html(function(i) {
    return (`${i.state}<hr> Income (Average) : $ ${i.income} <br> <br> Healthcare (%): ${i.healthcare}`)
  });
  circlesGroup.call(toolTip);

  //mousover event
  circletext.on("mouseover", function(data){
    toolTip.show(data)
  })

  .on("mouseout", function(data, index) {
    toolTip.hide(data)
  });

  //axis labels

  chartGroup.append("text")
  .attr("transform", "rotate(-90)")
  .attr("y", 0 - margin.left)
  .attr("x", 0 - (height / 2))
  .attr("dy", "1em")
  .attr("class", "axisText")
  .text("(%) Lacks Healthcare");

  chartGroup.append("text")
  .attr("transform", `translate(${width / 2}, ${height + margin.top + 25})`)
  .attr("class", "axisText")
  .text("Income Bracket");
});
```



