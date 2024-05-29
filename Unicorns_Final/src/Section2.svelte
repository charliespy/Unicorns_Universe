<script>
    export let id;
</script>

<body>
    <section {id} class="section">
        <h1>Section 2</h1>
        <p>Content for section 2</p>
    </section>
</body>

<style>
    .section {
        height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
        background: lightseagreen;
        scroll-snap-align: start;
    }
</style>