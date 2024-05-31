<script>
</script>

<main>
    <section class="section">
        <h1>Section 2</h1>
        <p>Content for section 2</p>
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
