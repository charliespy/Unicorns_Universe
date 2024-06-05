<script>
    import { onMount } from "svelte";
    import { data_chart } from "../data/data_chart.js";

    let chartContainer;
    let resizeHandler;
    const split = 0.8;

    onMount(() => {
        const options = {
            actions: false,
            renderer: "canvas",
        };

        window["vegaEmbed"]("#vis", data_chart, options).then((result) => {
            resizeHandler = () => {
                const width = window.innerWidth * 0.5 * 0.8;
                // const width = chartContainer.clientWidth;
                const height = width * 0.75;
                result.view.width(width).height(height).runAsync();
            };

            resizeHandler(); // Call initially to set size
            window.addEventListener("resize", resizeHandler);
        });
        // .catch(console.error);

        return () => {
            if (resizeHandler) {
                window.removeEventListener("resize", resizeHandler);
            }
        };
    });

    // onMount(async () => {
    //     window["vegaEmbed"]("#vis", data_chart, { actions: false })
    //         .then((result) => {
    //             console.log(result);
    //         })
    //         .catch(console.error);
    // });
</script>

<main>
    <div id="vis" class="left"></div>
    <div class="right">
        <h1>Comparing Countries with the World</h1>
        <p>
            We then compared the industry proportions of each country with the
            global proportion with <a
                href="https://c3.ai/glossary/data-science/root-mean-square-error-rmse/"
                >Root Mean Squared Error (RMSE)</a
            >, which provides us a numerical estimate of the variance between a
            country and the world, emphasizing countries with larger
            discrepancies. Each country's RMSE is calculated by taking the root
            of the sum of squared errors over the number of industries.
        </p>
        <h2>
            With this metric, United States 🇺🇸 has the most balanced industry
            structure with the lowest RMSE of 0.005, and Switzerland 🇨🇭 is the
            least balanced country with an RMSE of 0.165.
        </h2>
        <!-- <h2>
            With this metric, United States 🇺🇸 has the most balanced industry
            structure with the lowest RMSE of 0.005, and Switzerland 🇨🇭 is the
            least balanced country with an RMSE of 0.165.
        </h2> -->
    </div>
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

    .right {
        flex: 1.2;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        box-sizing: border-box;
        margin-right: 5%;
    }

    .left {
        flex: 0.8;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100%;
        margin-right: 3%;
    }

    #vis {
        width: 100%;
        height: 100%;
    }

    h1 {
        width: 100%;
        font-family: "Roboto", sans-serif;
        font-size: 2.3em;
        margin-bottom: 2%;
        text-align: left;
    }

    h2 {
        font-family: "Roboto", sans-serif;
        font-size: 1.2em;
        margin-bottom: 0.5em;
        line-height: 1.5;
        margin-bottom: 2%;
    }

    p {
        width: 100%;
        font-family: "Roboto", sans-serif;
        font-size: 1.2em;
        color: #666;
        text-align: left;
        line-height: 1.5;
        margin-bottom: 2%;
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
