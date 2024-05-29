<script>
    import Section1 from "./Section1.svelte";
    import Section2 from "./Section2.svelte";
    import Section3 from "./Section3.svelte";
</script>

<main>
    <Section1 id="section1" />
    <Section2 id="section2" />
    <Section3 id="section3" />
</main>

<style>
    main {
        overflow-y: scroll;
        height: 100%;
        scroll-snap-type: y mandatory;
    }

    .section {
        scroll-snap-align: start;
    }
</style>