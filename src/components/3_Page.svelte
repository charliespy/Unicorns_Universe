<script>
    import { onMount } from "svelte";
    import { data_chart } from "../data/data_chart.js";


    let chartContainer;
    let resizeHandler;
    const split = 0.8;

    onMount(() => {
        const options = {
            actions: false,
            renderer: "canvas"
        };

        window["vegaEmbed"]("#vis", data_chart, options)
            .then((result) => {
                resizeHandler = () => {
                    const width = window.innerWidth * 0.5 * 0.8;
                    // const width = chartContainer.clientWidth; 
                    const height = width * 0.75;
                    result.view.width(width).height(height).runAsync();
                };

                resizeHandler(); // Call initially to set size
                window.addEventListener('resize', resizeHandler);
            })
            // .catch(console.error);

        return () => {
            if (resizeHandler) {
                window.removeEventListener('resize', resizeHandler);
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
        <h1>Another Balancedness Metric: RMSE</h1>
        <p>
            For this metric, we calculated the <b>Root Mean Squared Error (RMSE)</b>
            of each country, which provides us a numerical estimate of the variance
            between the industry distribution within a country and the global industry
            distribution, emphasizing countries with larger discrepancies. In this
            context, a lower RMSE value indicates a more balanced distribution of
            industries within a country
        </p>
        <p>
            We visualized the results using a bar plot to compare national
            market dynamics against the global landscape. Notably, <b>United States</b>
            exhibits the lowest RMSE value at 0.005, indicating a balanced
            industry structure. Conversely, <b>Switzerland</b> displays the highest
            RMSE value, signaling significant deviation from global industry
            distributions.
        </p>
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
        flex: .8;
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
        margin: 2%;
        text-align: left;
    }

    p {
        width: 100%;
        font-family: "Roboto", sans-serif;
        font-size: 1.2em;
        color: #666;
        text-align: left;
        line-height: 1.5;
        margin: 2%;
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
