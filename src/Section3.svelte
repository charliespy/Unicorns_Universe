<script>
    export let id;
</script>

<body>
    <section {id} class="section">
        <h1>Section 3</h1>
        <p>Content for section 3</p>
    </section>
</body>

<style>
    .section {
        height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
        background: lightcoral;
        scroll-snap-align: start;
    }
</style>