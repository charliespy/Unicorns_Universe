<script>
    import { onMount } from "svelte";
    import * as d3 from "d3";

    let emojis = ["🦄", "🌍", "💡", "📈", "💰", "🚀", "🎉", "🏆"];
    let width = window.innerWidth;
    let height = window.innerHeight;

    function createFloatingEmojis() {
        const svg = d3
            .select("#emojiCanvas")
            .attr("width", width)
            .attr("height", height);

        const emojiElements = svg
            .selectAll("text")
            .data(emojis)
            .enter()
            .append("text")
            .text((d) => d)
            .attr("font-size", "3em")
            .attr("x", () => Math.random() * width)
            .attr("y", () => Math.random() * height)
            .style("fill-opacity", 0.8)
            .datum(() => ({
                dx: Math.random() * 1.5 + 0.5,
                dy: Math.random() * 1.5 + 0.5,
            }));

        function bounce() {
            emojiElements.each(function (d) {
                const emoji = d3.select(this);
                let x = +emoji.attr("x");
                let y = +emoji.attr("y");

                x += d.dx;
                y += d.dy;

                if (x > width - 50 || x < 0) d.dx = -d.dx;
                if (y > height - 50 || y < 0) d.dy = -d.dy;

                emoji.attr("x", x).attr("y", y);
            });

            requestAnimationFrame(bounce);
        }

        bounce();
    }

    onMount(() => {
        createFloatingEmojis();
    });
</script>

<main>
    <div class="content">
        <h1>Which Country's Unicorn Companies Showcase Greater Industry Diversity?</h1>
        <h2>Assessing the Industrial Variety in Startups Valued Over One Billion Dollars Across Nations</h2>
        <p>
            Explore the factors that contribute to a startup becoming a unicorn.
            This website provides insights and data on the most successful
            unicorns in the world on features like <strong
                >country, valuation, industry and more</strong
            >.
        </p>
    </div>
    <svg id="emojiCanvas"></svg>
</main>

<style>
    main {
        height: 100vh;
        width: 100vw;
        display: flex;
        justify-content: center;
        align-items: center;
        position: relative;
        overflow: hidden;
        /* background: #fff; */
    }

    .content {
        position: absolute;
        z-index: 10;
        text-align: center;
    }

    h1 {
        font-family: "Roboto", sans-serif;
        font-size: 3em;
        margin-bottom: 0.5em;
    }

    h2 {
        font-family: "Roboto", sans-serif;
        font-size: 2em;
        margin-bottom: 1em;
    }

    p {
        font-family: "Roboto", sans-serif;
        font-size: 1.2em;
        color: #666;
        max-width: 600px;
        margin: 0 auto;
    }

    svg {
        position: absolute;
        top: 0;
        left: 0;
    }
</style>
