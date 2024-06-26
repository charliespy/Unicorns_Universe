<script>
    import { onMount } from "svelte";
    import { data_sunburst } from "../data/data_sunburst.js";
    import * as d3 from "d3";

    function drawSunburst(d3, data) {
        const container = document.getElementById("sunburst");
        const width = container.clientWidth;
        const height = container.clientHeight;
        const radius = Math.min(width, height) / 6;

        const color = d3.scaleOrdinal(
            d3.quantize(d3.interpolateRainbow, data.children.length + 1),
        );

        const hierarchy = d3
            .hierarchy(data)
            .sum((d) => d.value)
            .sort((a, b) => b.value - a.value);
        const root = d3.partition().size([2 * Math.PI, hierarchy.height + 1])(
            hierarchy,
        );
        root.each((d) => (d.current = d));

        const arc = d3
            .arc()
            .startAngle((d) => d.x0)
            .endAngle((d) => d.x1)
            .padAngle((d) => Math.min((d.x1 - d.x0) / 2, 0.005))
            .padRadius(radius * 1.5)
            .innerRadius((d) => d.y0 * radius)
            .outerRadius((d) => Math.max(d.y0 * radius, d.y1 * radius - 1));

        const svg = d3
            .select("#sunburst")
            .append("svg")
            .attr("width", width)
            .attr("height", height)
            .attr("viewBox", [-width / 2, -height / 2, width, height])
            .style("font", "11px sans-serif");

        const path = svg
            .append("g")
            .selectAll("path")
            .data(root.descendants().slice(1))
            .join("path")
            .attr("fill", (d) => {
                while (d.depth > 1) d = d.parent;
                return color(d.data.name);
            })
            .attr("fill-opacity", (d) =>
                arcVisible(d.current) ? (d.children ? 0.6 : 0.4) : 0,
            )
            .attr("pointer-events", (d) =>
                arcVisible(d.current) ? "auto" : "none",
            )
            .attr("d", (d) => arc(d.current));

        path.filter((d) => d.children)
            .style("cursor", "pointer")
            .on("click", clicked);

        const format = d3.format(",d");
        path.append("title").text(
            (d) =>
                `${d
                    .ancestors()
                    .map((d) => d.data.name)
                    .reverse()
                    .join("/")}\n${format(d.value)}`,
        );

        const labelGroup = svg
            .append("g")
            .attr("pointer-events", "none")
            .attr("text-anchor", "middle")
            .style("user-select", "none");

        const labelOutline = labelGroup
            .selectAll("text")
            .data(root.descendants().slice(1))
            .join("text")
            .attr("class", "label-outline")
            .attr("dy", "0.35em")
            .attr("stroke", "white")
            .attr("stroke-width", 3)
            .attr("stroke-linejoin", "round")
            .attr("fill", "none")
            .attr("stroke-opacity", (d) => +labelVisible(d.current)) 
            .attr("transform", (d) => labelTransform(d.current))
            .text((d) => d.data.name);

        const labelText = labelGroup
            .selectAll("text.text")
            .data(root.descendants().slice(1))
            .join("text")
            .attr("class", "label-text")
            .attr("dy", "0.35em")
            .attr("fill", "black")
            .attr("fill-opacity", (d) => +labelVisible(d.current))
            .attr("transform", (d) => labelTransform(d.current))
            .text((d) => d.data.name);

        const parent = svg
            .append("circle")
            .datum(root)
            .attr("r", radius)
            .attr("fill", "none")
            .attr("pointer-events", "all")
            .on("click", clicked);

        function clicked(event, p) {
            parent.datum(p.parent || root);

            root.each(
                (d) =>
                    (d.target = {
                        x0:
                            Math.max(
                                0,
                                Math.min(1, (d.x0 - p.x0) / (p.x1 - p.x0)),
                            ) *
                            2 *
                            Math.PI,
                        x1:
                            Math.max(
                                0,
                                Math.min(1, (d.x1 - p.x0) / (p.x1 - p.x0)),
                            ) *
                            2 *
                            Math.PI,
                        y0: Math.max(0, d.y0 - p.depth),
                        y1: Math.max(0, d.y1 - p.depth),
                    }),
            );

            const t = svg.transition().duration(750);

            path.transition(t)
                .tween("data", (d) => {
                    const i = d3.interpolate(d.current, d.target);
                    return (t) => (d.current = i(t));
                })
                .filter(function (d) {
                    return (
                        +this.getAttribute("fill-opacity") ||
                        arcVisible(d.target)
                    );
                })
                .attr("fill-opacity", (d) =>
                    arcVisible(d.target) ? (d.children ? 0.6 : 0.4) : 0,
                )
                .attr("pointer-events", (d) =>
                    arcVisible(d.target) ? "auto" : "none",
                )
                .attrTween("d", (d) => () => arc(d.current));

            labelOutline
                .filter(function (d) {
                    return (
                        +this.getAttribute("stroke-opacity") ||
                        labelVisible(d.target)
                    );
                })
                .transition(t)
                .attr("stroke-opacity", (d) => +labelVisible(d.target))
                .attrTween("transform", (d) => () => labelTransform(d.current));

            labelText
                .filter(function (d) {
                    return (
                        +this.getAttribute("fill-opacity") ||
                        labelVisible(d.target)
                    );
                })
                .transition(t)
                .attr("fill-opacity", (d) => +labelVisible(d.target))
                .attrTween("transform", (d) => () => labelTransform(d.current));
        }

        function arcVisible(d) {
            return d.y1 <= 3 && d.y0 >= 1 && d.x1 > d.x0;
        }

        function labelVisible(d) {
            return (
                d.y1 <= 3 && d.y0 >= 1 && (d.y1 - d.y0) * (d.x1 - d.x0) > 0.07
            );
        }

        function labelTransform(d) {
            const x = (((d.x0 + d.x1) / 2) * 180) / Math.PI;
            const y = ((d.y0 + d.y1) / 2) * radius;
            return `rotate(${x - 90}) translate(${y},0) rotate(${x < 180 ? 0 : 180})`;
        }

        return svg.node();
    }

    onMount(() => {
        drawSunburst(d3, data_sunburst);
    });
</script>

<main>
    <div class="left">
        <h1>Explore Unicorns 🔎</h1>
        <p>Now here is the chance for you to explore unicorns on your own.</p>
        <p>Click a node to zoom in 🔍➕,</p>
        <p>or the center to zoom out 🔍➖.</p>
    </div>
    <div id="sunburst" class="right"></div>
</main>

<style>
    main {
        display: flex;
        align-items: center;
        justify-content: space-between;
        height: 100vh;
        width: 100vw;
        padding: 3%;
        box-sizing: border-box;
        scroll-snap-align: start;
    }

    .left {
        flex: 0.8;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: left;
        box-sizing: border-box;
        margin: 5%;
    }

    .right {
        flex: 1.2;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100%;
        margin-right: 5%;
    }

    #sunburst {
        width: 100%;
        height: 100%;
        /* max-width: 90vmin;
        max-height: 90vmin; */
    }

    h1 {
        font-family: "Roboto", sans-serif;
        font-size: 2.5em;
        margin-bottom: 0.5em;
    }

    p {
        font-family: "Roboto", sans-serif;
        font-size: 1.2em;
        color: #666;
        text-align: left;
        margin-bottom: 1%;
    }

    @media (max-width: 768px) {
        .section {
            flex-direction: column;
            padding: 10px;
        }

        .left,
        .right {
            flex: none;
            width: 100%;
            text-align: center;
            padding: 0;
        }

        .left {
            margin-bottom: 20px;
        }

        .right {
            margin-right: 0;
        }
    }
</style>
