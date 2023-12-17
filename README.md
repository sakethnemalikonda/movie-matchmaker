<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Movie Search Engine</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      margin: 0;
      padding: 0;
      background-color: #f4f4f4;
    }

    header {
      background-color: #333;
      color: #fff;
      text-align: center;
      padding: 1em 0;
    }

    main {
      max-width: 800px;
      margin: 20px auto;
      padding: 20px;
      background-color: #fff;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      border-radius: 8px;
    }

    h1 {
      color: #333;
    }

    label {
      display: block;
      margin: 10px 0;
    }

    input[type="text"] {
      width: 100%;
      padding: 10px;
      margin-bottom: 20px;
      box-sizing: border-box;
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    button {
      background-color: #333;
      color: #fff;
      padding: 10px 15px;
      border: none;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #555;
    }

    #results {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
    }

    .movie {
      width: calc(33.333% - 20px);
      margin-bottom: 20px;
      box-sizing: border-box;
      border: 1px solid #ddd;
      border-radius: 8px;
      overflow: hidden;
    }

    .movie img {
      width: 100%;
      height: auto;
      border-bottom: 1px solid #ddd;
    }

    .movie-details {
      padding: 15px;
    }

    .movie-details h3 {
      margin-bottom: 8px;
      font-size: 1.2em;
      color: #333;
    }

    .movie-details p {
      margin: 0;
      color: #555;
    }

    .pagination {
      margin-top: 20px;
      text-align: center;
    }

    .pagination-link {
      display: inline-block;
      margin: 0 5px;
      padding: 5px 10px;
      background-color: #333;
      color: #fff;
      cursor: pointer;
      transition: transform 0.3s ease;
    }

    .pagination-link:hover {
      background-color: #555;
      transform: scale(1.1);
    }

    .error-message {
      color: #ff0000;
    }

    @media only screen and (max-width: 600px) {
      main {
        padding: 10px;
      }
      .movie {
        width: 100%;
      }
    }

    button:hover,
    .pagination-link:hover {
      transform: scale(1.1);
    }

    .movie:hover {
      box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
    }

    label,
    input[type="text"],
    button {
      font-size: 16px;
    }

    input[type="text"] {
      border: 1px solid #ccc;
      border-radius: 4px;
    }

    button:hover {
      background-color: #444;
      color: #fff;
    }

    .movie-details {
      padding: 15px;
    }

    .movie-details h3 {
      margin-bottom: 8px;
      font-size: 1.2em;
    }

    .movie-details p {
      color: #555;
    }

    .cast-section,
    .crew-section {
      margin-top: 15px;
    }

    .cast-section h4,
    .crew-section h4 {
      font-size: 1.1em;
      margin-bottom: 10px;
    }

    a {
      display: inline-block;
      text-decoration: none;
      color: #333;
      background-color: #eee;
      padding: 8px 12px;
      border-radius: 4px;
      margin-right: 10px;
      margin-bottom: 10px;
      transition: background-color 0.3s ease;
      font-size: 14px;
    }

    a:hover {
      background-color: #ddd;
      color: #555;
    }

    a + a {
      margin-left: 10px;
      margin-bottom: 10px;
    }

    #loadingSpinner {
      border: 4px solid rgba(0, 0, 0, 0.1);
      border-radius: 50%;
      border-top: 4px solid #333;
      width: 30px;
      height: 30px;
      animation: spin 0.8s linear infinite;
      margin: 20px auto;
    }

    @keyframes spin {
      0% {
        transform: rotate(0deg);
      }

      100% {
        transform: rotate(360deg);
      }
    }
  </style>
</head>

<body>

  <header>
    <h1>Movie Search Engine</h1>
  </header>

  <main>
    <label for="searchInput">Enter a movie title:</label>
    <input type="text" id="searchInput">

    <button id="searchButton">Search</button>

    <div id="results"></div>
    <div id="pagination" class="pagination"></div>
  </main>

  <script>
    const tmdbApiKey = '0f899c6339e24f97761b78ca78d92c4a'; // Replace with your TMDb API key

    let currentPage = 1;

    // Function to handle movie search
    const searchMovies = () => {
      const searchTerm = document.getElementById('searchInput').value;

      const tmdbApiUrl = `https://api.themoviedb.org/3/search/movie?api_key=${tmdbApiKey}&query=${searchTerm}&page=${currentPage}`;

      fetch(tmdbApiUrl)
        .then(response => {
          if (!response.ok) {
            throw new Error('Network response was not ok');
          }
          return response.json();
        })
        .then(data => {
          displayResults(data.results);
          displayPagination(data.total_results);
        })
        .catch(error => {
          console.error('Error fetching TMDb data:', error);
          displayError('An error occurred. Please try again.');
        });
    };

    // Event listener for search button click
    document.getElementById('searchButton').addEventListener('click', searchMovies);

    // Event listener for Enter key press in the input field
    document.getElementById('searchInput').addEventListener('keyup', (event) => {
      if (event.key === 'Enter') {
        searchMovies();
      }
    });

    // Function to display search results
    const displayResults = (movies) => {
      const resultsContainer = document.getElementById('results');
      resultsContainer.innerHTML = '';

      if (movies) {
        movies.forEach(movie => {
          const movieElement = document.createElement('div');
          movieElement.classList.add('movie');
          movieElement.innerHTML = `<img src="https://image.tmdb.org/t/p/w185${movie.poster_path}" alt="${movie.title} Poster">
                                    <div class="movie-details">
                                      <h3>${movie.title}</h3>
                                      <p>${movie.release_date}</p>
                                      <p class="story">${movie.overview}</p>
                                      <button onclick="getTmdbDetails(${movie.id})">Get Cast & Crew Details</button>
                                      <a href="#" onclick="getTrailer('${movie.id}', '${movie.title}')">Watch Trailer</a>
                                      <a href="#" onclick="getWikipedia('${movie.title}')">Wikipedia</a>
                                      <div id="${movie.id}-details" style="display: none;"></div>
                                    </div>`;
          resultsContainer.appendChild(movieElement);
        });
      } else {
        resultsContainer.innerHTML = '<p>No results found.</p>';
      }
    };

    // Function to display pagination links
    const displayPagination = (totalResults) => {
      const paginationContainer = document.getElementById('pagination');
      paginationContainer.innerHTML = '';

      const totalPages = Math.ceil(totalResults / 20);

      for (let i = 1; i <= totalPages; i++) {
        const pageLink = document.createElement('span');
        pageLink.classList.add('pagination-link');
        pageLink.textContent = i;
        pageLink.addEventListener('click', () => {
          currentPage = i;
          searchMovies();
        });
        paginationContainer.appendChild(pageLink);
      }
    };

    // Function to display error message
    const displayError = (message) => {
      const resultsContainer = document.getElementById('results');
      resultsContainer.innerHTML = `<p class="error-message">${message}</p>`;
    };

    // Function to get TMDb details
    const getTmdbDetails = (movieId) => {
      const detailsContainer = document.getElementById(`${movieId}-details`);

      // Check if details are already visible
      if (detailsContainer.style.display === 'block') {
        // If details are visible, hide them and return
        detailsContainer.style.display = 'none';
        return;
      }

      // If details are not visible, fetch and display them
      const tmdbApiUrl = `https://api.themoviedb.org/3/movie/${movieId}?api_key=${tmdbApiKey}&append_to_response=credits`;

      fetch(tmdbApiUrl)
        .then(response => {
          if (!response.ok) {
            throw new Error('Network response was not ok');
          }
          return response.json();
        })
        .then(data => {
          const cast = data.credits.cast.map(actor => {
            return `<div>
                      <img src="https://image.tmdb.org/t/p/w185${actor.profile_path}" alt="${actor.name} Photo">
                      <p>${actor.name}</p>
                    </div>`;
          }).join('');

          const crew = data.credits.crew.map(crewMember => {
            return `<div>
                      <img src="https://image.tmdb.org/t/p/w185${crewMember.profile_path}" alt="${crewMember.name} Photo">
                      <p>${crewMember.name} (${crewMember.job})</p>
                    </div>`;
          }).join('');

          detailsContainer.innerHTML = `<div class="cast-section"><h4>Cast</h4>${cast}</div>
                                        <div class="crew-section"><h4>Crew</h4>${crew}</div>`;

          // Make details container visible
          detailsContainer.style.display = 'block';
        })
        .catch(error => {
          console.error('Error fetching TMDb data:', error);
          detailsContainer.innerHTML = '<p class="error-message">Error fetching cast and crew details.</p>';
          detailsContainer.style.display = 'block'; // Display error message
        });
    };

    // Function to open YouTube trailer search
    const getTrailer = (movieId, movieTitle) => {
      const searchQuery = encodeURIComponent(`${movieTitle} official trailer`);
      const youtubeSearchUrl = `https://www.youtube.com/results?search_query=${searchQuery}`;
      window.open(youtubeSearchUrl, '_blank');
    };

    // Function to open Wikipedia search
    const getWikipedia = (movieTitle) => {
      const searchQuery = encodeURIComponent(`${movieTitle} film`);
      const wikipediaSearchUrl = `https://en.wikipedia.org/wiki/Special:Search?search=${searchQuery}`;
      window.open(wikipediaSearchUrl, '_blank');
    };
  </script>

</body>

</html>
