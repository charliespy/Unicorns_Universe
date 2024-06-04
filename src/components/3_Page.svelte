<script>
    import { onMount } from 'svelte';
    import { data_chart } from '../data/data_chart';
  
    onMount(async () => {  
      window['vegaEmbed']('#vis', data_chart, {actions: false}).then(result => {
          console.log(result);
      }).catch(console.error);
    });
  </script>

<main>
    <section class="section">
        <div id="vis"></div>
        <!-- <h1>Section 3</h1>
        <p>Content for section 3</p> -->
    </section>
</main>

<style>
    .section {
        height: 100vh;
        width: 100vw;
        display: flex;
        align-items: center;
        justify-content: center;
        scroll-snap-align: start;
        padding: 20px;
        box-sizing: border-box;
    }

    /* Add responsiveness */
    @media (max-width: 768px) {
        .section {
            padding: 10px;
        }
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
    }
</style>
