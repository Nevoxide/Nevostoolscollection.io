<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Music Genre Explorer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Calm Harmony -->
    <!-- Application Structure Plan: The application is structured as a two-column layout on larger screens and a stacked layout on mobile. A persistent navigation sidebar on the left allows users to select a music genre. The main content area on the right dynamically updates to display detailed information about the selected genre. This master-detail structure was chosen for its high usability, allowing users to easily and quickly compare different genres without losing context or needing to scroll extensively. A summary chart is included at the bottom to provide an alternative, quantitative view of the data. This non-linear exploration encourages user interaction and discovery. -->
    <!-- Visualization & Content Choices: 
        - Report Info: Full list of genres. Goal: Navigation/Organization. Viz/Method: Clickable styled buttons in a sidebar. Interaction: Click to update main content. Justification: Provides clear, persistent navigation. Library: HTML/CSS/JS.
        - Report Info: Genre details (Explanation, Instruments, Iconic Thing). Goal: Inform. Viz/Method: Dynamic text blocks in a main content panel. Interaction: Content updates based on navigation selection. Justification: Centralizes information display for focused reading. Library: JS DOM manipulation.
        - Report Info: Key Instruments list. Goal: Inform/Compare. Viz/Method: Styled "pills" or "tags". Interaction: Static display. Justification: Enhances visual appeal and scannability over a plain list. Library: HTML/Tailwind CSS.
        - Report Info: Number of instruments per genre (derived). Goal: Compare. Viz/Method: Horizontal Bar Chart. Interaction: Static visual comparison. Justification: Adds a quantitative, at-a-glance comparison of instrumental complexity, enhancing the report's value. Library: Chart.js (Canvas).
    -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            height: 400px;
            max-height: 50vh;
        }
        @media (max-width: 768px) {
            .chart-container {
                height: 300px;
            }
        }
        .nav-button.active {
            background-color: #f59e0b; /* amber-500 */
            color: #1c1917; /* stone-900 */
            font-weight: 600;
        }
    </style>
</head>
<body class="bg-stone-100 text-stone-800">

    <div class="container mx-auto p-4 md:p-8">
        <header class="text-center mb-8">
            <h1 class="text-4xl md:text-5xl font-bold text-stone-900 mb-2">Interactive Music Genre Explorer</h1>
            <p class="text-lg text-stone-600">Click on a genre to explore its characteristics and sound.</p>
        </header>

        <main class="flex flex-col md:flex-row gap-8">
            
            <nav id="genre-nav" class="md:w-1/4 flex flex-row md:flex-col flex-wrap gap-2">
            </nav>

            <div id="content-area" class="md:w-3/4 bg-white rounded-lg shadow-lg p-6 md:p-8 min-h-[300px]">
                <div id="genre-content">
                </div>
            </div>
        </main>

        <section class="mt-12 bg-white rounded-lg shadow-lg p-6 md:p-8">
            <h2 class="text-2xl font-bold text-center mb-4 text-stone-900">Instrument Count Comparison</h2>
            <p class="text-center text-stone-600 mb-6 max-w-3xl mx-auto">This chart provides a quick visual comparison of the number of key instruments listed for each genre in our guide. It offers one way to see how the instrumental focus varies across different styles of music.</p>
            <div class="chart-container">
                <canvas id="instrumentChart"></canvas>
            </div>
        </section>

    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const genreData = [
                {
                    name: 'Rock',
                    explanation: "A broad genre that originated in the United States in the 1950s. It's characterized by a strong backbeat and often centers around the electric guitar. Rock can range from simple, upbeat songs to complex, virtuosic compositions.",
                    instruments: ['Electric guitar', 'Bass guitar', 'Drums', 'Vocals'],
                    iconicThing: 'The electric guitar riff, which is a short, memorable musical phrase.'
                },
                {
                    name: 'Pop',
                    explanation: 'Short for "popular music," this genre is defined by its commercial appeal and catchiness. Pop songs are typically structured to be accessible, with memorable choruses and simple, relatable lyrics.',
                    instruments: ['Electronic synthesizers', 'Drum machines', 'Vocals'],
                    iconicThing: 'A catchy chorus and a simple, repeatable structure.'
                },
                {
                    name: 'Jazz',
                    explanation: 'A genre that originated in African American communities in New Orleans, Louisiana, in the late 19th and early 20th centuries. It is known for its improvisation, complex harmony, and syncopated rhythms.',
                    instruments: ['Saxophone', 'Trumpet', 'Piano', 'Upright bass', 'Drums'],
                    iconicThing: 'Improvisation, where musicians spontaneously create new melodies during a performance.'
                },
                {
                    name: 'Blues',
                    explanation: 'A genre that originated in the Deep South of the United States, rooted in the spirituals and work songs of African American communities. It is known for its emotional lyrics and distinctive chord progressions, often following a 12-bar structure.',
                    instruments: ['Guitar (Acoustic/Electric)', 'Harmonica', 'Vocals'],
                    iconicThing: 'The use of "blue notes"—subtle pitches that give the melody a melancholic or soulful feel.'
                },
                {
                    name: 'Classical',
                    explanation: 'A genre that encompasses a wide range of music produced over a long period, from the 11th century to the present. It is typically characterized by complex musical forms, orchestration, and a focus on harmony and counterpoint.',
                    instruments: ['Orchestra (Strings, Woodwinds, Brass, Percussion)', 'Piano', 'Choir'],
                    iconicThing: 'Symphonies and operas, which are large-scale compositions for an orchestra or a combination of orchestra and singers.'
                },
                {
                    name: 'Hip-Hop',
                    explanation: 'A cultural and musical movement that emerged in the Bronx, New York City, in the 1970s. It is defined by its rhythmic and rhyming speech, known as rapping, which is performed over a beat.',
                    instruments: ['Turntables', 'Drum machines', 'Synthesizers'],
                    iconicThing: 'Rapping, where the rhythmic delivery of lyrical content is the central focus.'
                },
                {
                    name: 'EDM',
                    explanation: 'A broad range of percussive electronic music genres made primarily for nightclubs, raves, and festivals. It is characterized by its electronic instrumentation and repetitive rhythms designed for dancing.',
                    instruments: ['Synthesizers', 'Drum machines', 'Samplers', 'Sequencers'],
                    iconicThing: 'A "drop", which is a climactic moment in a song where the beat becomes more intense and often introduces new sonic elements.'
                },
                {
                    name: 'Reggae',
                    explanation: 'A music genre that originated in Jamaica in the late 1960s. It is known for its distinctive offbeat rhythm, known as the "skank", and its lyrics often touch on social and political themes.',
                    instruments: ['Bass guitar', 'Drums', 'Electric guitar', 'Piano/Organ'],
                    iconicThing: 'The offbeat rhythm played by the guitar or piano, which gives it a relaxed, forward-moving feel.'
                },
                {
                    name: 'R&B',
                    explanation: 'A genre that combines elements of gospel, blues, and jazz. It is characterized by its soulful vocals, strong rhythms, and often romantic or emotional lyrics.',
                    instruments: ['Vocals', 'Piano', 'Bass guitar', 'Drums', 'Saxophone'],
                    iconicThing: 'Powerful, soulful vocals with melisma (singing multiple notes on a single syllable) and a smooth, emotional delivery.'
                },
                {
                    name: 'Country',
                    explanation: 'A genre that originated in the rural Southern United States. It blends folk music with ballads and dance tunes, often featuring lyrical themes of heartbreak, patriotism, and working-class life.',
                    instruments: ['Acoustic guitar', 'Fiddle', 'Banjo', 'Steel guitar', 'Harmonica'],
                    iconicThing: 'The twangy vocal style and the frequent use of the steel guitar, which creates a distinct, gliding sound.'
                }
            ];

            const navContainer = document.getElementById('genre-nav');
            const contentContainer = document.getElementById('genre-content');
            
            function displayGenre(genreName) {
                const genre = genreData.find(g => g.name === genreName);
                if (!genre) return;

                const instrumentsHtml = genre.instruments.map(instrument => 
                    `<span class="inline-block bg-amber-200 text-amber-800 text-sm font-semibold mr-2 mb-2 px-3 py-1 rounded-full">${instrument}</span>`
                ).join('');

                contentContainer.innerHTML = `
                    <h2 class="text-3xl font-bold mb-4 text-stone-900">${genre.name}</h2>
                    <p class="text-stone-700 leading-relaxed mb-6">${genre.explanation}</p>
                    
                    <h3 class="text-xl font-semibold mb-3 text-stone-800">Key Instruments</h3>
                    <div class="flex flex-wrap mb-6">
                        ${instrumentsHtml}
                    </div>

                    <h3 class="text-xl font-semibold mb-3 text-stone-800">Iconic Feature</h3>
                    <div class="bg-stone-100 border-l-4 border-amber-500 p-4 rounded-r-lg">
                        <p class="text-stone-800 italic">"${genre.iconicThing}"</p>
                    </div>
                `;
            }

            function setActiveButton(button) {
                document.querySelectorAll('.nav-button').forEach(btn => {
                    btn.classList.remove('active');
                });
                button.classList.add('active');
            }

            genreData.forEach(genre => {
                const button = document.createElement('button');
                button.textContent = genre.name;
                button.className = 'nav-button w-full text-left p-3 rounded-md bg-white shadow-sm hover:bg-amber-100 transition-colors duration-200';
                button.addEventListener('click', () => {
                    displayGenre(genre.name);
                    setActiveButton(button);
                });
                navContainer.appendChild(button);
            });

            function renderChart() {
                const ctx = document.getElementById('instrumentChart').getContext('2d');
                const labels = genreData.map(g => g.name);
                const data = genreData.map(g => g.instruments.length);

                new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: labels,
                        datasets: [{
                            label: 'Number of Key Instruments',
                            data: data,
                            backgroundColor: 'rgba(245, 158, 11, 0.6)', // amber-500 with opacity
                            borderColor: 'rgba(245, 158, 11, 1)', // amber-500
                            borderWidth: 1
                        }]
                    },
                    options: {
                        indexAxis: 'y',
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            legend: {
                                display: false
                            },
                            tooltip: {
                                callbacks: {
                                    label: function(context) {
                                        let label = context.dataset.label || '';
                                        if (label) {
                                            label += ': ';
                                        }
                                        if (context.parsed.x !== null) {
                                            label += context.parsed.x;
                                        }
                                        return label;
                                    }
                                }
                            }
                        },
                        scales: {
                            x: {
                                beginAtZero: true,
                                grid: {
                                    color: 'rgba(0,0,0,0.05)'
                                }
                            },
                            y: {
                                grid: {
                                    display: false
                                }
                            }
                        }
                    }
                });
            }

            const firstButton = navContainer.querySelector('.nav-button');
            if (firstButton) {
                firstButton.click();
            }
            
            renderChart();
        });
    </script>
</body>
</html>
