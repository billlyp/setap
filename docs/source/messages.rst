
Messages
=======================

This page is dedicated to the code functions in messages.


displayChat
-----------------------

.. code-block:: javascript

    /**
     * Asynchronously fetches and displays chats.
     * 
     * @async
     * @function displayChats
     * @example
     * // Example usage:
     * displayChats();
     */
    async function displayChats() {
        const contact = document.querySelector('.contact-list');

        try {
            const response = await fetch('/chat/files');
            if (!response.ok) {
                throw new Error('Failed to fetch chat files');
            }
            const chatFiles = await response.json();

            for (let i = 0; i < chatFiles.length; i++) {
                const personCounter = i + 1;
                const chatData = await fetchChatData(personCounter);

                // extract unique sender IDs from the chat data
                const senderIds = chatData.map(message => message.senderId);

                // check if the current user's ID is included in the sender IDs
                if (senderIds.includes(getUserId)) {
                    const uniqueSenderIds = [...new Set(senderIds)];

                    // check if the chat is a group chat (more than two participants)
                    if (uniqueSenderIds.length > 2) {
                        const newPerson = document.createElement("div");
                        newPerson.classList.add("personSelector");

                        const nameElement = document.createElement("p");
                        const lastMsgElement = document.createElement("p");

                        nameElement.id = "name";
                        lastMsgElement.id = "lastMessage";

                        const senderNames = uniqueSenderIds.map(senderId => {
                            const message = chatData.find(message => message.senderId === senderId);
                            return message ? message.sender : "Unknown Sender";
                        });

                        const concatenatedNames = senderNames.join(' + ');
                        nameElement.textContent = concatenatedNames;

                        newPerson.appendChild(nameElement);
                        newPerson.appendChild(lastMsgElement);

                        contact.appendChild(newPerson);

                        newPerson.addEventListener('click', function() {
                            selectedChat = personCounter;
                            logChat(personCounter);
                        });
                    } else if (uniqueSenderIds.length === 2) {
                        const otherSender = uniqueSenderIds.find(senderId => senderId !== getUserId);
                        const otherSenderData = chatData.find(message => message.senderId === otherSender);

                        const newPerson = document.createElement("div");
                        newPerson.classList.add("personSelector");

                        const nameElement = document.createElement("p");
                        const lastMsgElement = document.createElement("p");

                        nameElement.id = "name";
                        lastMsgElement.id = "lastMessage";

                        nameElement.textContent = otherSenderData ? otherSenderData.sender : "Unknown Sender";

                        newPerson.appendChild(nameElement);
                        newPerson.appendChild(lastMsgElement);

                        contact.appendChild(newPerson);

                        newPerson.addEventListener('click', function() {
                            selectedChat = personCounter;
                            logChat(personCounter);
                        });
                    }
                }
            }
        } catch (error) {
            console.error('Error generating person selectors:', error);
        }
    }

**A detailed breakdown of the function is as follows:**

1. The function fetches chat JSON files from the server using the fetch API.

    .. code-block:: javascript

        const response = await fetch('/chat/files');
        if (!response.ok) {
            throw new Error('Failed to fetch chat files');
        }
        const chatFiles = await response.json();

2. The function iterates through the chat files and fetches chat data for each chat.

    .. code-block:: javascript

        for (let i = 0; i < chatFiles.length; i++) {
            const personCounter = i + 1;
            const chatData = await fetchChatData(personCounter);

3. The function checks if it should display the chat to the user based on comparing the userId client side and the senderId from the JSON data.
    
    .. code-block:: javascript

        const senderIds = chatData.map(message => message.senderId);
        if (senderIds.includes(getUserId)) {

4. The function checks if the current user's ID is included in the sender IDs.
    
    .. code-block:: javascript

        const uniqueSenderIds = [...new Set(senderIds)];

5. If the chat is a group chat (more than two participants), a new person selector element is created and appended to the contact list.
        
    .. code-block:: javascript

        if (uniqueSenderIds.length > 2) {
            const newPerson = document.createElement("div");
            newPerson.classList.add("personSelector");

            const nameElement = document.createElement("p");
            const lastMsgElement = document.createElement("p");

            nameElement.id = "name";
            lastMsgElement.id = "lastMessage";

            const senderNames = uniqueSenderIds.map(senderId => {
                const message = chatData.find(message => message.senderId === senderId);
                return message ? message.sender : "Unknown Sender";
            });

            const concatenatedNames = senderNames.join(' + ');
            nameElement.textContent = concatenatedNames;

            newPerson.appendChild(nameElement);
            newPerson.appendChild(lastMsgElement);

            contact.appendChild(newPerson);

            newPerson.addEventListener('click', function() {
                selectedChat = personCounter;
                logChat(personCounter);
            });
        }

6. If the chat is a one-on-one chat, the other sender's data is extracted and a new person selector element is created and appended to the contact list.
    
    .. code-block:: javascript

        else if (uniqueSenderIds.length === 2) {
            const otherSender = uniqueSenderIds.find(senderId => senderId !== getUserId);
            const otherSenderData = chatData.find(message => message.senderId === otherSender);

            const newPerson = document.createElement("div");
            newPerson.classList.add("personSelector");

            const nameElement = document.createElement("p");
            const lastMsgElement = document.createElement("p");

            nameElement.id = "name";
            lastMsgElement.id = "lastMessage";

            nameElement.textContent = otherSenderData ? otherSenderData.sender : "Unknown Sender";

            newPerson.appendChild(nameElement);
            newPerson.appendChild(lastMsgElement);

            contact.appendChild(newPerson);

            newPerson.addEventListener('click', function() {
                selectedChat = personCounter;
                logChat(personCounter);
            });
        }

7. If there is an error, it is logged to the console.

    
    .. code-block:: javascript
        } catch (error) {
        console.error('Error generating person selectors:', error);
        }


fetchChatData
-----------------------

    .. code-block:: javascript
        /**
         * Asynchronously fetches chat data for a specific person.
         * 
         * @async
         * @function fetchChatData
         * @param {number} personCounter - The person counter.
         * @returns {Promise<Array>} The chat data.
         * @example
         * // Example usage:
         * fetchChatData(1);
         */
        async function fetchChatData(personCounter) {
            try {
            const response = await fetch(`/chat/data/${personCounter}`);
            if (!response.ok) {
            throw new Error('Failed to fetch chat data');
            }
                return await response.json();
            } catch (error) {
                console.error('Error fetching chat data:', error);
            return [];
            }
        }

**A detailed breakdown of the function is as follows:**

1. The function fetches chat data for a specific person from the server using the fetch API.

    .. code-block:: javascript

        const response = await fetch(`/chat/data/${personCounter}`);
        if (!response.ok) {
            throw new Error('Failed to fetch chat data');
        }
        return await response.json();

2. If there is an error, it is logged to the console.
    
        .. code-block:: javascript
    
            } catch (error) {
                console.error('Error fetching chat data:', error);
                return [];
            }

clearChatHistory
---------------------
.. code-block:: javascript

    /**
     * Clears the chat history.
     * 
     * @function clearChatHistory
     * @example
     * // Example usage:
     * clearChatHistory();
     */
    function clearChatHistory() {
        const chatMessageHistory = document.getElementById("chatMessageHistory");
        chatMessageHistory.innerHTML = "";
    }

youAreLoggedInAs
------------------
.. code-block:: javascript

    /**
     * Displays the current user's name in the chat.
     * 
     * @function youAreLoggedInAs
     * @example
     * // Example usage:
     * youAreLoggedInAs();
     */
    function youAreLoggedInAs() {
        const loggedInAs = document.getElementById('loggedInAs');
        loggedInAs.textContent = 'Logged in as: ' + getUserName;
    }

buildMessageHistory
-------------------
    .. code-block:: javascript
        /**
         * Builds the message history.
         * 
         * @function buildMessageHistory
         * @example
         * // Example usage:
         * buildMessageHistory();
         */
        function buildMessageHistory(chatData) {
            const chatMessageHistory = document.getElementById("chatMessageHistory");
            const theirMessageClassName = 'theirMessage';
            const yourMessageClassName = 'yourMessage';
            let isGroupChat = false;

  
            if (chatData.length > 0 && chatData[0].text === '') {
                isGroupChat = true;
            }

            if (chatData.length === 0) {
                return;
            } 

            let identifierMessageCount = 0;
            for (let i = 0; i < chatData.length; i++) {
                const message = chatData[i];
    
                if (isGroupChat && message.text === '') {
                identifierMessageCount++;
                continue;
                }
    
                if (!isGroupChat && identifierMessageCount < 2) {
                  identifierMessageCount++;
                  continue;
                }
    
                //if user id is the same as the sender id, then give the message the class of yourMessage, else give it the class of theirMessage
                const messageClass = message.senderId === getUserId ? yourMessageClassName : theirMessageClassName;
                //same here, except it will display 'You' if the sender id matches
                const senderName = message.senderId === getUserId ? 'You' : message.sender;
                const newMessage = createMessageElement(senderName, message.text, message.timestamp, messageClass);
                chatMessageHistory.appendChild(newMessage);
            }
        }
**A detailed breakdown of the function is as follows:**

1. Initalizes variables.

    .. code-block:: javascript

        const chatMessageHistory = document.getElementById("chatMessageHistory");
        const theirMessageClassName = 'theirMessage'; //applies this class to messages sent by the other person
        const yourMessageClassName = 'yourMessage'; //applies this class to messages sent by the user
        let isGroupChat = false;

2. Checks if the chat is a group chat.

    .. code-block:: javascript

        if (chatData.length > 0 && chatData[0].text === '') {
            isGroupChat = true;
        }
3. Checks if the chat data is empty.

    .. code-block:: javascript

        if (chatData.length === 0) {
            return;
        }
4. Iterates through the chat data and builds the message history.

    .. code-block:: javascript

        let identifierMessageCount = 0;
        for (let i = 0; i < chatData.length; i++) {
            const message = chatData[i];

            if (isGroupChat && message.text === '') {
                identifierMessageCount++;
                continue;
            }

            if (!isGroupChat && identifierMessageCount < 2) {
                identifierMessageCount++;
                continue;
            }

            const messageClass = message.senderId === getUserId ? yourMessageClassName : theirMessageClassName;
            const senderName = message.senderId === getUserId ? 'You' : message.sender;
            const newMessage = createMessageElement(senderName, message.text, message.timestamp, messageClass);
            chatMessageHistory.appendChild(newMessage);
        }

5. If the sender ID in a specific message in the JSON matches the user ID, the message is given the class of yourMessage; otherwise, it is given the class of theirMessage.

    .. code-block:: javascript

        const messageClass = message.senderId === getUserId ? yourMessageClassName : theirMessageClassName;
6. adds the message to the chatMessageHistory element.

    .. code-block:: javascript

        chatMessageHistory.appendChild(newMessage);

getTimeStamp
------------

    .. code-block:: javascript
        /**
         * Gets the current timestamp.
         * 
         * @function getTimeStamp
         * @returns {string} The formatted timestamp.
         * @example
         * // Example usage:
         * getTimeStamp();
         */
        function getTimeStamp() {
        const currentDate = new Date();

        let hours = currentDate.getHours();
        const ampm = hours >= 12 ? 'pm' : 'am';
        hours = hours % 12 || 12;

        const minutes = currentDate.getMinutes();
        const day = currentDate.getDate();
        const month = currentDate.getMonth() + 1;
        const year = currentDate.getFullYear();

        const formattedTimeStamp = `${hours}:${minutes < 10 ? '0' : ''}${minutes} ${ampm} ${day}/${month}/${year}`;
        return formattedTimeStamp;
        }

**A detailed breakdown of the function is as follows:**

1. The function gets the current timestamp.

    .. code-block:: javascript

        const currentDate = new Date();

2. The function gets the current hour and formats it.
    
    .. code-block:: javascript
    
        let hours = currentDate.getHours();
        const ampm = hours >= 12 ? 'pm' : 'am';
        hours = hours % 12 || 12;

3. The function gets the current minutes.
        
    .. code-block:: javascript
        
        const minutes = currentDate.getMinutes();
        const day = currentDate.getDate();
        const month = currentDate.getMonth() + 1;
        const year = currentDate.getFullYear();

4. The function formats and returns the timestamp.
        
    .. code-block:: javascript
    
        const formattedTimeStamp = `${hours}:${minutes < 10 ? '0' : ''}${minutes} ${ampm} ${day}/${month}/${year}`;
        return formattedTimeStamp;