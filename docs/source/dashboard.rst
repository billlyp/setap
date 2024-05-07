Dashboard
================

This page is dedicated to the functions within the dashboard

init
----------------
    .. code-block:: javascript

        function init() {
            const liveFeed = document.querySelector('#liveFeed');
            liveFeed.addEventListener('click', () => {
                window.location.href = '../livefeed';
            });
            const messages = document.querySelector('#messages');
            messages.addEventListener('click', () => {
                window.location.href = '../messages';
            });
        }

Initalizes the dashboard page and sets up the event listeners for the live feed and messages buttons


displayLiveFeed
----------------
.. code-block:: javascript

    function displayLiveFeed() {
        const liveFeed = document.querySelector('#liveFeed');
        liveFeed.addEventListener('click', () => {
            // Fetch data from the server
            fetch('/api/livefeed')
            .then(response => response.json())
            .then(data => {
                // Update the dashboard with the fetched data
                // Replace the following code with your logic to update the dashboard
                console.log('Live feed data:', data);
            })
            .catch(error => {
                console.error('Error fetching live feed data:', error);
            });
        });

        const messages = document.querySelector('#messages');
        messages.addEventListener('click', () => {
            window.location.href = '../messages';
        });
    }

**A detailed breakdown of the function:**

1. The function is called when the live feed button is clicked

    .. code-block:: javascript
        const liveFeed = document.querySelector('#liveFeed');
        liveFeed.addEventListener('click', () => {

2. The function fetches data from the server using the `/api/livefeed` endpoint

    .. code-block:: javascript
        fetch('/api/livefeed')

3. The response is converted to JSON format

    .. code-block:: javascript
        .then(response => response.json())

4. The data is logged to the console

    .. code-block:: javascript
        .then(data => {
            console.log('Live feed data:', data);
        })

5. If there is an error, it is logged to the console

    .. code-block:: javascript
        .catch(error => {
            console.error('Error fetching live feed data:', error);
        });

6. The messages button is set up to redirect to the messages page when clicked

    .. code-block:: javascript
        const messages = document.querySelector('#messages');
        messages.addEventListener('click', () => {
            window.location.href = '../messages';
        });
