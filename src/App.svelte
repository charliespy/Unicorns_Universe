<script>
    import { onMount } from 'svelte';
    import Welcome from "./Welcome.svelte";
    import Section1 from "./Section1.svelte";
    import Section2 from "./Section2.svelte";

    let currentSection = 0;
    const sections = ['section1', 'section2', 'section3'];

    const scrollToSection = (index) => {
        const section = document.getElementById(sections[index]);
        if (section) {
            section.scrollIntoView({ behavior: 'smooth' });
            currentSection = index;
        }
    };

    onMount(() => {
        scrollToSection(0);
    });

    const nextSection = () => {
        if (currentSection < sections.length - 1) {
            scrollToSection(currentSection + 1);
        }
    };

    const prevSection = () => {
        if (currentSection > 0) {
            scrollToSection(currentSection - 1);
        }
    };
</script>

<main>
    <section class="section" id="section1">
        <Welcome id="section1" />
    </section>
    <section class="section" id="section2">
        <Section1 id="section2" />
    </section>
    <section class="section" id="section3">
        <Section2 id="section3" />
    </section>
    <div class="controls">
        <button on:click={prevSection} disabled={currentSection === 0}>Previous</button>
        <div class="progress-bar">
            <div class="progress" style="height: {((currentSection + 1) / sections.length) * 100}%"></div>
        </div>
        <button on:click={nextSection} disabled={currentSection === sections.length - 1}>Next</button>
    </div>
</main>

<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        overflow: hidden; /* Prevent body scrolling */
    }

    main {
        height: 100vh;
        width: 100vw;
        overflow-y: hidden; /* Hide scroll bar */
        position: relative;
        display: flex;
        flex-direction: column;
        align-items: center;
        scroll-snap-type: y mandatory;
    }

    .section {
        scroll-snap-align: start;
        height: 100vh;
        width: 100vw;
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 0px; 
        box-sizing: border-box;
    }

    @media (max-width: 768px) {
        .section {
            padding: 10px;
            flex-direction: column; /* Stack content vertically on small screens */
        }
    }

    main {
        background: linear-gradient(120deg, #fdfbfb 0%, #ebedee 100%);
    }

    .section:nth-child(odd) {
        background: #ffffff;
    }

    .section:nth-child(even) {
        background: #f7f7f7;
    }

    .controls {
        position: fixed;
        right: 10px;
        top: 50%;
        transform: translateY(-50%);
        display: flex;
        flex-direction: column;
        align-items: center;
        z-index: 10;
    }

    .controls button {
        margin: 5px 0;
        padding: 10px 20px;
        background-color: #007BFF;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
    }

    .controls button:disabled {
        background-color: #cccccc;
    }

    .progress-bar {
        width: 10px;
        height: 200px;
        background-color: #e0e0e0;
        border-radius: 5px;
        margin: 10px 0;
        position: relative;
    }

    .progress {
        width: 100%;
        background-color: #007BFF;
        border-radius: 5px;
        position: absolute;
        bottom: 0;
    }

    @media (max-width: 768px) {
        .controls {
            right: 50%;
            top: auto;
            bottom: 10px;
            transform: translateX(50%);
            flex-direction: row; /* Arrange controls horizontally */
        }

        .controls button {
            margin: 0 5px;
        }

        .progress-bar {
            height: 10px;
            width: 200px;
            margin: 0 10px;
        }

        .progress {
            height: 100%;
            width: auto;
        }
    }
</style>
