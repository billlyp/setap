Live Feed
==================

This page is dedicated to the live feed section of the app.

getFeed
------------------

.. code-block:: javascript

    async function getFeed() {
        try{
            const response = await fetch('/livefeed/file');
            if (response.ok) {
                return await response.json();
            } else {
                console.error('Failed to retrieve posts:', response.status, response.statusText);
            }
        } catch (error) {
            console.error('An error occurred while retrieving the posts:', error);
        }
    };


**A detailed breakdown of the function:**

1. Within the try block, the function makes a fetch request to the server to retrieve the live feed posts.

    .. code-block:: javascript
        const response = await fetch('/livefeed/file');
        if (response.ok) {
            return await response.json();
        } else {
            console.error('Failed to retrieve posts:', response.status, response.statusText);
        }

2. If the response is successful, the function returns the JSON data.

buildFeed
----------

.. code-block:: javascript

    async function buildFeed() {
        const posts = await getFeed();
        console.log(posts);
        const feed = document.querySelector('.feed');

        for (const post of posts) {
            const postBox = document.createElement('div');
            postBox.classList.add('post-box');

            const postHeader = document.createElement('div');
            postHeader.classList.add('post-header');

            const username = document.createElement('h3');
            username.id = 'post-username';
            username.textContent = post.name;

            const postDate = document.createElement('p');
            postDate.id = 'post-date';
            postDate.textContent = post.timeStamp;

            const postContent = document.createElement('p');
            postContent.id = 'post-content';
            postContent.textContent = post.postText; // Accessing the postText property

            postHeader.appendChild(username);
            postHeader.appendChild(postDate);

            postBox.appendChild(postHeader);
            postBox.appendChild(postContent);

            feed.appendChild(postBox); // Append postBox to the feed div
        }
    }

**A detailed breakdown of the function:**

1. The function calls the getFeed function to retrieve the posts.

    .. code-block:: javascript
        const posts = await getFeed();

2. The function then selects the feed div element using querySelector.

    .. code-block:: javascript
        const feed = document.querySelector('.feed');

3. The function then iterates over each post in the posts array and creates the necessary HTML elements to display the post.

    .. code-block:: javascript
        for (const post of posts) {

4. The function creates a div element for the post and adds the 'post-box' class to it.

    .. code-block:: javascript
        const postBox = document.createElement('div');
        postBox.classList.add('post-box');

5. The function creates a div element for the post header and adds the 'post-header' class to it.

    .. code-block:: javascript
        const postHeader = document.createElement('div');
        postHeader.classList.add('post-header');

6. The function creates an h3 element for the username and sets the text content to the post's name property.

    .. code-block:: javascript
        const username = document.createElement('h3');
        username.id = 'post-username';
        username.textContent = post.name;

7. The function creates a p element for the post date and sets the text content to the post's timeStamp property.

    .. code-block:: javascript
        const postDate = document.createElement('p');
        postDate.id = 'post-date';
        postDate.textContent = post.timeStamp;

8. The function creates a p element for the post content and sets the text content to the post's postText property.

    .. code-block:: javascript
        const postContent = document.createElement('p');
        postContent.id = 'post-content';
        postContent.textContent = post.postText;

9. The function appends the username and post date elements to the post header.

    .. code-block:: javascript
        postHeader.appendChild(username);
        postHeader.appendChild(postDate);

10. The function appends the post header and post content elements to the post box.

    .. code-block:: javascript
        postBox.appendChild(postHeader);
        postBox.appendChild(postContent);

11. The function appends the post box to the feed div.

    .. code-block:: javascript
        feed.appendChild(postBox);

postToFeed
------------

.. code-block:: javascript

    function postToFeed() {
        const content = document.getElementById('postContent').value;
        if (!content) {
            alert('Please write something to post!');
        } else {
            const feed = document.getElementById('feed');
            const postElement = document.createElement('div');
            postElement.classList.add('post');
            postElement.textContent = content;
            feed.prepend(postElement);
            document.getElementById('postContent').value = '';
            
            fetch('/api/posts', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ content })
            })
            .then(response => {
                if (response.ok) {
                    console.log('Post sent to the server successfully!');
                } else {
                    console.error('Failed to send post to the server.');
                }
            })
            .catch(error => {
                console.error('An error occurred while sending the post:', error);
            });
        }
    };


**A detailed breakdown of the function:**

1. The function retrieves the content of the post from the input field with the id 'postContent'.

    .. code-block:: javascript
        const content = document.getElementById('postContent').value;

2. The function checks if the content is empty and displays an alert if it is.

    .. code-block:: javascript
        if (!content) {
            alert('Please write something to post!');
        } else {

3. If the content is not empty, the function creates a new div element for the post and adds the 'post' class to it.

    .. code-block:: javascript
        const postElement = document.createElement('div');
        postElement.classList.add('post');
        postElement.textContent = content;

4. The function prepends the post element to the feed div.

    .. code-block:: javascript
        feed.prepend(postElement);

5. The function clears the input field after posting the content.

    .. code-block:: javascript
        document.getElementById('postContent').value = '';

6. The function makes a POST request to the server with the post content in the body.

    .. code-block:: javascript
        fetch('/api/posts', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ content })
            })
            .then(response => {
                if (response.ok) {
                    console.log('Post sent to the server successfully!');
                } else {
                    console.error('Failed to send post to the server.');
                }
            })
            .catch(error => {
                console.error('An error occurred while sending the post:', error);
            });

7. The function logs a success message if the post is sent successfully and an error message if it fails.

sendPostToServer
-------------------

.. code-block:: javascript

    async function sendPostToServer(postText){
        // prepare data to send
        let newPostData ={
            senderId: getUserId,
            name: getUserName,
            postText: postText,
            timeStamp: getTimeStamp()
        }
        try{
            const response = await fetch('/livefeed/post', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(newPostData)
            });
            if (response.ok) {
                console.log('Post sent to the server successfully!');
                console.log(newPostData);
            } else {
                console.error('Failed to send post to the server.');
            };
        } catch (error) {
            console.error('An error occurred while sending the post:', error);
        };
    };

**A detailed breakdown of the function:**

1. The function takes the postText as an argument and prepares the data to send to the server.

    .. code-block:: javascript
        let newPostData ={
            senderId: getUserId,
            name: getUserName,
            postText: postText,
            timeStamp: getTimeStamp()
        }

2. The function makes a POST request to the server with the new post data in the body.

    .. code-block:: javascript
        const response = await fetch('/livefeed/post', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(newPostData)
        });

3. The function logs a success message if the post is sent successfully and an error message if it fails.

    .. code-block:: javascript
        if (response.ok) {
            console.log('Post sent to the server successfully!');
            console.log(newPostData);
        } else {
            console.error('Failed to send post to the server.');
        };

4. The function catches any errors that occur during the process and logs them.

    .. code-block:: javascript
        } catch (error) {
            console.error('An error occurred while sending the post:', error);
        };

createPost
----------------

.. code-block:: javascript

    function createPost() {
        let postText = document.getElementById("postContent").value;
        const feed = document.querySelector(".feed");

        if (!postText.trim()) {
            console.error('Post content cannot be empty.');
            return;
        }

        sendPostToServer(postText);

        const postBox = document.createElement('div');
        postBox.classList.add('post-box');

        const postHeader = document.createElement('div');
        postHeader.classList.add('post-header');

        const username = document.createElement('h3');
        username.id = 'post-username';
        username.textContent = getUserName;

        const postDate = document.createElement('p');
        postDate.id = 'post-date';
        postDate.textContent = getTimeStamp();

        const postContent = document.createElement('p');
        postContent.id = 'post-content';
        postContent.textContent = postText;

        postHeader.appendChild(username);
        postHeader.appendChild(postDate);

        postBox.appendChild(postHeader);
        postBox.appendChild(postContent);

        feed.appendChild(postBox);
    }

**A detailed breakdown of the function:**

1. The function retrieves the post content from the input field with the id 'postContent'.

    .. code-block:: javascript
        let postText = document.getElementById("postContent").value;

2. The function checks if the post content is empty or only contains whitespace characters.

    .. code-block:: javascript
        if (!postText.trim()) {
            console.error('Post content cannot be empty.');
            return;
        }

3. The function calls the sendPostToServer function to send the post to the server.

    .. code-block:: javascript
        sendPostToServer(postText);

4. The function creates the necessary HTML elements to display the new post.

    .. code-block:: javascript
        const postBox = document.createElement('div');
        postBox.classList.add('post-box');

        const postHeader = document.createElement('div');
        postHeader.classList.add('post-header');

        const username = document.createElement('h3');
        username.id = 'post-username';
        username.textContent = getUserName;

        const postDate = document.createElement('p');
        postDate.id = 'post-date';
        postDate.textContent = getTimeStamp();

        const postContent = document.createElement('p');
        postContent.id = 'post-content';
        postContent.textContent = postText;

        postHeader.appendChild(username);
        postHeader.appendChild(postDate);

        postBox.appendChild(postHeader);
        postBox.appendChild(postContent);

        feed.appendChild(postBox);
        }
