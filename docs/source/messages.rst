
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



