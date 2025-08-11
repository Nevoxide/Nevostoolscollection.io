<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Music Genre Database</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Calm Harmony -->
    <!-- Application Structure Plan: The application is a single-page interactive database structured with a dynamic two-column layout. A searchable navigation sidebar on the left provides a complete, filterable list of genres. The main content area on the right acts as a detail panel, dynamically updating with rich information, including a GIF, a detailed description, and key instruments for the selected genre. This structure, enhanced with a search bar, facilitates easy, non-linear exploration and quick information retrieval from a large dataset. The chart at the bottom offers a high-level, comparative view, encouraging users to synthesize and understand the data in multiple ways. -->
    <!-- Visualization & Content Choices: 
        - Report Info: Comprehensive genre list. Goal: Organization & Navigation. Viz/Method: Filterable list of clickable buttons. Interaction: Search to filter the list; click to update the main content. Justification: A search feature is essential for usability with a large list of items. Library: HTML/CSS/JS.
        - Report Info: Detailed genre info (description, instruments, iconic artists, GIF). Goal: Inform & Engage. Viz/Method: Dynamic content blocks with an image/GIF. Interaction: Content updates based on navigation clicks. Justification: Combining text and a visually representative GIF makes the information more memorable and engaging. Library: HTML/JS DOM manipulation.
        - Report Info: Key Instruments list. Goal: Inform/Compare. Viz/Method: Styled "pills" or "tags". Interaction: Static display. Justification: This format is easily scannable and visually separates the key instruments. Library: HTML/Tailwind CSS.
        - Report Info: Number of instruments per genre (derived). Goal: Compare. Viz/Method: Horizontal Bar Chart. Interaction: Static visual comparison. Justification: A bar chart is an effective way to compare quantitative data, and the horizontal orientation is ideal for the longer genre names. Library: Chart.js (Canvas).
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
            background-color: #f59e0b;
            color: #1c1917;
            font-weight: 600;
        }
    </style>
</head>
<body class="bg-stone-100 text-stone-800">

    <div class="container mx-auto p-4 md:p-8">
        <header class="text-center mb-8">
            <h1 class="text-4xl md:text-5xl font-bold text-stone-900 mb-2">Interactive Music Genre Database</h1>
            <p class="text-lg text-stone-600">A comprehensive guide to music genres with interactive exploration.</p>
        </header>

        <main class="flex flex-col md:flex-row gap-8">
            
            <div class="md:w-1/4">
                <input type="text" id="search-input" placeholder="Search genres..." class="w-full p-3 rounded-md mb-4 shadow-sm focus:outline-none focus:ring-2 focus:ring-amber-500">
                <nav id="genre-nav" class="flex flex-row md:flex-col flex-wrap gap-2 overflow-y-auto max-h-[70vh]">
                </nav>
            </div>

            <div id="content-area" class="md:w-3/4 bg-white rounded-lg shadow-lg p-6 md:p-8 min-h-[300px]">
                <div id="genre-content">
                </div>
            </div>
        </main>

        <section class="mt-12 bg-white rounded-lg shadow-lg p-6 md:p-8">
            <h2 class="text-2xl font-bold text-center mb-4 text-stone-900">Instrument Count Comparison</h2>
            <p class="text-center text-stone-600 mb-6 max-w-3xl mx-auto">This chart provides a quick visual comparison of the number of key instruments listed for each genre in our guide.</p>
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
                    description: "A broad genre that originated in the 1950s, characterized by a strong backbeat and often centering on the electric guitar. It's a vast genre ranging from simple, upbeat songs to complex, virtuosic compositions. Iconic artists include Led Zeppelin, The Beatles, and Queen.",
                    instruments: ['Electric guitar', 'Bass guitar', 'Drums', 'Vocals'],
                    gif: 'https://media.giphy.com/media/l4pTc9V6X3JtB7Q08/giphy.gif'
                },
                {
                    name: 'Pop',
                    description: 'Short for "popular music," this genre is defined by its commercial appeal and catchiness. Pop songs are typically structured to be accessible, with memorable choruses and relatable lyrics. Key artists are Michael Jackson, Madonna, and Taylor Swift.',
                    instruments: ['Electronic synthesizers', 'Drum machines', 'Vocals'],
                    gif: 'https://media.giphy.com/media/l0HlCqS9I6nE9P4nK/giphy.gif'
                },
                {
                    name: 'Jazz',
                    description: 'A genre that originated in African American communities in New Orleans. It is known for its improvisation, complex harmony, and syncopated rhythms. It includes sub-genres like swing, bebop, and fusion. Louis Armstrong, Miles Davis, and John Coltrane are seminal artists.',
                    instruments: ['Saxophone', 'Trumpet', 'Piano', 'Upright bass', 'Drums'],
                    gif: 'https://media.giphy.com/media/3o7TKBcE5oE1N8lGpO/giphy.gif'
                },
                {
                    name: 'Blues',
                    description: 'Originating in the Deep South, this genre is rooted in the spirituals and work songs of African American communities. It is known for its emotional lyrics and a distinctive 12-bar chord progression. B.B. King, Muddy Waters, and Etta James are legendary blues artists.',
                    instruments: ['Guitar (Acoustic/Electric)', 'Harmonica', 'Vocals'],
                    gif: 'https://media.giphy.com/media/l4pThR4Y99jS23wB2/giphy.gif'
                },
                {
                    name: 'Classical',
                    description: 'Encompassing a long period from the 11th century to the present, this genre is characterized by complex musical forms, orchestration, and a focus on harmony and counterpoint. Composers like Mozart, Beethoven, and Bach defined the genre.',
                    instruments: ['Orchestra (Strings, Woodwinds, Brass, Percussion)', 'Piano', 'Choir'],
                    gif: 'https://media.giphy.com/media/l0HlP45hC1T2W7j2w/giphy.gif'
                },
                {
                    name: 'Hip-Hop',
                    description: 'A cultural and musical movement that emerged in the Bronx in the 1970s. It is defined by its rhythmic and rhyming speech (rapping) performed over a beat. Pioneers include Grandmaster Flash, Run-DMC, and Tupac Shakur.',
                    instruments: ['Turntables', 'Drum machines', 'Synthesizers'],
                    gif: 'https://media.giphy.com/media/l0HlPqJ8W3mDty0uA/giphy.gif'
                },
                {
                    name: 'Electronic Dance Music (EDM)',
                    description: 'A broad range of percussive electronic music genres for nightclubs and festivals. It is characterized by electronic instrumentation and repetitive rhythms designed for dancing. Artists like Daft Punk, The Chemical Brothers, and Avicii are influential.',
                    instruments: ['Synthesizers', 'Drum machines', 'Samplers', 'Sequencers'],
                    gif: 'https://media.giphy.com/media/l0HlThoH4Vz98tJ2/giphy.gif'
                },
                {
                    name: 'Reggae',
                    description: 'Originating in Jamaica in the late 1960s, Reggae is known for its distinctive offbeat rhythm (the "skank"). Lyrics often touch on social and political themes. Bob Marley is the most recognized figure in the genre.',
                    instruments: ['Bass guitar', 'Drums', 'Electric guitar', 'Piano/Organ'],
                    gif: 'https://media.giphy.com/media/l4pTj6tUa0R9R2K8g/giphy.gif'
                },
                {
                    name: 'R&B (Rhythm & Blues)',
                    description: 'A genre that combines elements of gospel, blues, and jazz. It is characterized by soulful vocals, strong rhythms, and often romantic lyrics. Marvin Gaye, Aretha Franklin, and Stevie Wonder are key artists.',
                    instruments: ['Vocals', 'Piano', 'Bass guitar', 'Drums', 'Saxophone'],
                    gif: 'https://media.giphy.com/media/l4pTp6XQnBv9S5XvC/giphy.gif'
                },
                {
                    name: 'Country',
                    description: 'A genre that originated in the rural Southern United States. It blends folk music with ballads and dance tunes, often featuring lyrical themes of heartbreak and working-class life. Johnny Cash, Dolly Parton, and Hank Williams are foundational figures.',
                    instruments: ['Acoustic guitar', 'Fiddle', 'Banjo', 'Steel guitar', 'Harmonica'],
                    gif: 'https://media.giphy.com/media/l4pThR4Y99jS23wB2/giphy.gif'
                },
                {
                    name: 'Heavy Metal',
                    description: 'A genre of rock music that developed in the late 1960s and early 1970s. It is characterized by distorted guitars, extended guitar solos, emphatic rhythms, and powerful vocals. Key artists include Black Sabbath, Metallica, and Iron Maiden.',
                    instruments: ['Electric guitar', 'Bass guitar', 'Drums', 'Vocals'],
                    gif: 'https://media.giphy.com/media/l4pThR4Y99jS23wB2/giphy.gif'
                },
                {
                    name: 'Folk',
                    description: 'A genre that refers to traditional, often acoustic, music that has been passed down through generations. Lyrical themes often focus on storytelling, social issues, or historical events. Woody Guthrie and Bob Dylan are iconic figures.',
                    instruments: ['Acoustic guitar', 'Fiddle', 'Mandolin', 'Harmonica', 'Banjo', 'Vocals'],
                    gif: 'https://media.giphy.com/media/l4pThR4Y99jS23wB2/giphy.gif'
                },
                {
                    name: 'Gospel',
                    description: 'Music written to express either personal or a communal belief regarding Christian life. It is often characterized by dominant vocals, strong harmonies, and inspiring lyrics. Mahalia Jackson is a well-known gospel artist.',
                    instruments: ['Vocals', 'Piano', 'Organ', 'Choir'],
                    gif: 'https://media.giphy.com/media/l4pThR4Y99jS23wB2/giphy.gif'
                }
            ];

            const navContainer = document.getElementById('genre-nav');
            const contentContainer = document.getElementById('genre-content');
            const searchInput = document.getElementById('search-input');
            
            function displayGenre(genreName) {
                const genre = genreData.find(g => g.name === genreName);
                if (!genre) {
                    contentContainer.innerHTML = `<p class="text-stone-500 italic">Select a genre from the list or search for one.</p>`;
                    return;
                }

                const instrumentsHtml = genre.instruments.map(instrument => 
                    `<span class="inline-block bg-amber-200 text-amber-800 text-sm font-semibold mr-2 mb-2 px-3 py-1 rounded-full">${instrument}</span>`
                ).join('');

                contentContainer.innerHTML = `
                    <div class="flex flex-col md:flex-row gap-6 mb-6">
                        <div class="md:w-1/3">
                            <img src="${genre.gif}" onerror="this.onerror=null; this.src='https://placehold.co/400x300/e7e5e4/312e81?text=GIF+Not+Found';" alt="GIF for ${genre.name}" class="rounded-lg shadow-md w-full h-auto object-cover aspect-video">
                        </div>
                        <div class="md:w-2/3">
                            <h2 class="text-3xl font-bold mb-2 text-stone-900">${genre.name}</h2>
                            <p class="text-stone-700 leading-relaxed">${genre.description}</p>
                        </div>
                    </div>
                    
                    <h3 class="text-xl font-semibold mb-3 text-stone-800">Key Instruments</h3>
                    <div class="flex flex-wrap mb-6">
                        ${instrumentsHtml}
                    </div>
                `;
            }

            function setActiveButton(button) {
                document.querySelectorAll('.nav-button').forEach(btn => {
                    btn.classList.remove('active');
                });
                if (button) {
                    button.classList.add('active');
                }
            }

            function renderGenreList(data) {
                navContainer.innerHTML = '';
                data.forEach(genre => {
                    const button = document.createElement('button');
                    button.textContent = genre.name;
                    button.className = 'nav-button w-full text-left p-3 rounded-md bg-white shadow-sm hover:bg-amber-100 transition-colors duration-200';
                    button.addEventListener('click', () => {
                        displayGenre(genre.name);
                        setActiveButton(button);
                    });
                    navContainer.appendChild(button);
                });
            }

            searchInput.addEventListener('input', (e) => {
                const searchTerm = e.target.value.toLowerCase();
                const filteredData = genreData.filter(g => g.name.toLowerCase().includes(searchTerm));
                renderGenreList(filteredData);
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
                            backgroundColor: 'rgba(245, 158, 11, 0.6)',
                            borderColor: 'rgba(245, 158, 11, 1)',
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

            renderGenreList(genreData);
            displayGenre(genreData[0].name);
            setActiveButton(navContainer.querySelector('.nav-button'));
            renderChart();
        });
    </script>
</body>
</html>
