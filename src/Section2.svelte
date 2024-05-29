<script>
    export let id;
</script>

<section {id} class="section">
    <h1>Section 2</h1>
    <br>
    <p>Content for section 2</p>
</section>

<style>
    .section {
        height: 100vh;
        width: 100vw;
        display: flex;
        align-items: center;
        justify-content: center;
        background: lightseagreen;
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

    h1, p {
        margin: 0;
        color: #fff;
    }
</style>
