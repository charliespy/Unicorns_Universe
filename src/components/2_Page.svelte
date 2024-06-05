<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import { data_chart } from '../data/industry_percentages.js';

  let data = data_chart;

  onMount(() => {
      createChart();
  });

  function createChart() {
      const margin = { top: 20, right: 30, bottom: 40, left: 150 };
      const width = 800 - margin.left - margin.right;
      const height = 500 - margin.top - margin.bottom;

      const svg = d3.select("#chart")
          .append("svg")
          .attr("width", width + margin.left + margin.right)
          .attr("height", height + margin.top + margin.bottom)
          .append("g")
          .attr("transform", `translate(${margin.left},${margin.top})`);

      const industries = Object.keys(data[0]).filter(d => d !== 'Country');

      // Sort countries by the smallest dominant industry percentage
      data.sort((a, b) => {
          const maxA = Math.max(...industries.map(industry => a[industry]));
          const maxB = Math.max(...industries.map(industry => b[industry]));
          return maxA - maxB;
      });

      const y = d3.scaleBand()
          .range([0, height])
          .domain(data.map(d => d.Country))
          .padding(0.1);

      svg.append("g")
          .call(d3.axisLeft(y));

      const x = d3.scaleLinear()
          .range([0, width])
          .domain([0, 1]);

      svg.append("g")
          .attr("transform", `translate(0,${height})`)
          .call(d3.axisBottom(x).tickFormat(d3.format(".0%")));

      // Create stacked bars
      svg.append("g")
          .selectAll("g")
          .data(data)
          .enter()
          .append("g")
          .attr("transform", d => `translate(0,${y(d.Country)})`)
          .selectAll("rect")
          .data(d => industries.map(key => ({ key, value: d[key], country: d.Country })))
          .enter()
          .append("rect")
          .attr("x", (d, i, nodes) => {
              const cumulative = nodes.slice(0, i).reduce((sum, n) => sum + d3.select(n).data()[0].value, 0);
              return x(cumulative);
          })
          .attr("width", d => x(d.value))
          .attr("height", y.bandwidth())
          .attr("fill", (d, i) => d3.schemeCategory10[i % 10])
          .on("mouseover", function(event, d) {
              const tooltip = d3.select("#tooltip");
              tooltip.style("opacity", 1)
                  .html(`
                      <strong>Country:</strong> ${d.country}<br>
                      <strong>Industry Distribution:</strong><br>
                      ${industries.map(key => `${key}: ${(d3.format(".0%")(data.find(c => c.Country === d.country)[key]))}`).join('<br>')}
                  `);
          })
          .on("mousemove", function(event) {
              const tooltip = d3.select("#tooltip");
              const tooltipHeight = tooltip.node().offsetHeight;
              const tooltipWidth = tooltip.node().offsetWidth;

              let left = event.pageX + 15;
              let top = event.pageY - 28;

              if (event.pageY + tooltipHeight > window.innerHeight) {
                  top = event.pageY - tooltipHeight - 10;
              }

              if (event.pageX + tooltipWidth > window.innerWidth) {
                  left = event.pageX - tooltipWidth - 10;
              }

              tooltip.style("left", `${left}px`)
                  .style("top", `${top}px`);
          })
          .on("mouseout", function() {
              d3.select("#tooltip").style("opacity", 0);
          });
  }
</script>

<main>
  <section class="section">
      <div class="text-container">
          <h1>Percentage Distribution of Unicorn Industries 🌍</h1>
          <p>This graph shows the percentage distribution of different industries within each country with <strong>more than 5 unicorns</strong>. The countries are ordered by the percentage of their most dominant industry, with the smallest dominant industry on top.</p>
          <p>Hover over the bars to see the industry names.</p>
          <h2>France 🇫🇷 is the most diverse country, while Switzerland 🇨🇭 is the most homogeneous country for industry distribution of unicorns.</h2>
        </div>
      <div id="chart" class="chart-container"></div>
      <div id="tooltip" class="tooltip"></div>
  </section>
</main>

<style>
  .section {
      height: 100vh;
      width: 100vw;
      display: flex;
      align-items: center;
      justify-content: space-between;
      scroll-snap-align: start;
      padding: 20px;
      box-sizing: border-box;
      position: relative;
  }

  .text-container {
      width: 40%;
      margin-left: 5%;
  }

  .chart-container {
      width: 60%;
  }

  /* Add responsiveness */
  @media (max-width: 768px) {
      .section {
          flex-direction: column;
          align-items: flex-start;
      }

      .text-container, .chart-container {
          width: 100%;
      }
  }

  h1 {
      font-family: "Roboto", sans-serif;
      font-size: 2.0em;
      margin-bottom: 0.5em;
      
  }

  h2 {
      font-family: "Roboto", sans-serif;
      font-size: 1.3em;
      margin-bottom: 0.5em;
      line-height: 1.5;

  }

  p {
      font-family: "Roboto", sans-serif;
      font-size: 1.2em;
      color: #666;
      line-height: 1.5;

  }

  svg {
      font-family: "Roboto", sans-serif;
  }

  .tooltip {
      position: absolute;
      text-align: left;
      width: auto;
      height: auto;
      padding: 10px;
      background: lightsteelblue;
      border: 0px;
      border-radius: 8px;
      pointer-events: none;
      opacity: 0;
      font-family: "Roboto", sans-serif;
      font-size: 1em;
  }
</style>
