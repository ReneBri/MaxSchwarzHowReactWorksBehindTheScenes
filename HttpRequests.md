# HTTP Requests

**How to (not) Connect to a Database**
Your React app should NEVER talk directly with a database. Doing this exposes database credentials. Simply open up dev tools and read the code.

Instead access all these DB requests through the backend.

With HTTP requests we can use the inbuild fetch API or a third party API like Axios.

When we fetch data it returns a promise and eventually json data. On the response object that is returned there is a parsing method called .json() this is what we use to parse out data.

Here's an example:

    const fetchMoviesHandler = () => {
    	fetch("https://swapi.dev/api/films/")
    		.then((response) => {
    			return response.json();
    		})
    		.then((data) => {
    			const transformedData = data.results.map((el) => {
    				return {
    					id: el.episode_id,
    					title: el.title,
    					openingText: el.opening_crawl,
    					releaseDate: el.release_date,
    				};
    			});
    			setMovies(transformedData);
    		});
    };

For error handling errors: if we receive back a error code in the 200's range, it is a successful request, otherwise if we get back 400 or 500 responses it means we have a error response, for example if we try to access something which we are not authorised to access.

Since the server wont throw an error when we get something back like a 404, we need to throw our own error in our try/catch block if an error code is received.

One of the benefits of Axios is that it throws errors for error status codes.

In a code block, once we throw an error the rest of the code after the error is not executed, rather our error handling is triggered.

Here is a bare bnes example of how to make a post request from within React:

    async function addMovieHandler(movie) {
    	const response = await fetch(
    		"https://react-http-709d2-default-rtdb.europe-west1.firebasedatabase.app/movies.json",
    		{
    			method: "POST",
    			body: JSON.stringify(movie),
    			header: {
    				ContentType: "application/json",
    			},
    		}
    	);
    	const data = await response.json();
    	console.log(data);
    }

This has no error handling but you get the point.

Also, some cool syntax for pushing objects to an array is this:

    const loadedMovies = [];

    // This is an object full of objects
    for (const key in data) {
        loadedMovies.push({
            id: key,
            title: data[key].title,
            openingText: data[key].openingText,
            releaseDate: data[key].releaseDate,
        });
    }
