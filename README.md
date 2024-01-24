# Chatwoot Prohibited Words Filter

This customization guide outlines the steps to filter out messages containing prohibited words in the Chatwoot messaging platform. The customization is done on both the frontend (Vue.js) and the backend (Ruby on Rails).

## Overview

This customization aims to enhance Chatwoot by preventing the sending and storing of messages that contain certain prohibited words. The customization covers both the frontend and backend aspects of Chatwoot.

## Frontend Customization

### 1. Frontend Configuration
 ## Frontend Customization - Part 1
#### File Path: `/chatwoot/app/javascript/widget/store/modules/conversation/action.js`

Update the `sendMessage` action in the `action.js` file to include a check for prohibited words. This check is performed before dispatching the message for sending.

```javascript


  sendMessage: async ({ dispatch }, params) => {
    const badPhrases = ['ugly', 'fuck you','fuck' ,'x']; 

    if (badPhrases.some(badPhrase => params.content.trim().toLowerCase().includes(badPhrase.toLowerCase()))) {
      console.log('Bad phrase detected. Message not sent.');
    } else {
     
      const { content, replyTo } = params;
  
     
      const message = createTemporaryMessage({ content, replyTo });
   
      dispatch('sendMessageWithData', message);
    }
  },
```

### 1. Frontend Configuration : Another way

#### File Path: `/chatwoot/app/javascript/widget/api/endPoints.js`

In this part of the customization, we modify the `sendMessage` function in the `endPoints.js` file to perform additional checks for prohibited words before constructing and sending the message. This check is crucial in ensuring that messages with inappropriate content are not sent from the frontend.

```javascript
// ... other actions and methods ...

/**
 * Customized sendMessage function to include a check for prohibited words.
 *
 * @param {string} content - The content of the message.
 * @param {string} replyTo - The ID of the message being replied to.
 * @returns {Object|null} - Returns the message parameters if no prohibited words are found, otherwise returns null.
 */
const sendMessage = (content, replyTo) => {
  const referrerURL = window.referrerURL || '';
  const search = buildSearchParamsWithLocale(window.location.search);
  const badWords = ['fuck', 'x', 'badword'];  
  const containsBadWord = badWords.some(badWord => content.toLowerCase().trim().includes(badWord.toLowerCase()));

  if (containsBadWord) {
    console.log('You cannot send a message containing bad words.');
    return null; 
  }

  // Construct the message parameters
  return {
    url: `/api/v1/widget/messages${search}`,
    params: {
      message: {
        content,
        reply_to: replyTo,
        timestamp: new Date().toString(),
        referer_url: referrerURL,
      },
    },
  };
};

// ... other actions and methods ...
## Backend Customization for User & Agent Messages

### 1. Backend Configuration for User Messages

#### File Path: `/chatwoot/app/controllers/api/v1/widget/messages_controller.rb`

In this customization, we modify the `MessagesController` under the Widget API to include a check for prohibited words before creating a new message in the conversation for user messages.

```ruby
 

class Api::V1::Widget::MessagesController < Api::V1::Widget::BaseController
  before_action :set_conversation, only: [:create]
  before_action :set_message, only: [:update]
  before_action :check_prohibited_words, only: [:create]

  # ... other actions and methods ...

  private

  def check_prohibited_words
    prohibited_words = ['fuck', 'ugly'] # Add your list of prohibited words here

    if prohibited_words.any? { |word| params.dig(:message, :content).to_s.downcase.include?(word) }
      render json: { error: 'Message contains prohibited words' }, status: :unprocessable_entity
    end
  end

  # ... other private methods ...
end
 

## Backend Configuration for Agent Messages

### File Path: `/chatwoot/app/controllers/api/v1/accounts/conversations/messages_controller.rb`

In this customization, we modify the `MessagesController` under the Account Conversations API to include a check for prohibited words before creating a new message in the conversation for agent messages.

```ruby
# ... other actions and methods ...

class Api::V1::Accounts::Conversations::MessagesController < Api::V1::Accounts::Conversations::BaseController
  before_action :check_prohibited_words, only: [:create]

  # ... other actions and methods ...

  private

  def check_prohibited_words
    prohibited_words = ['fuck', 'ugly'] # Add your list of prohibited words here

    if prohibited_words.any? { |word| params.dig(:message, :content).to_s.downcase.include?(word) }
      render json: { error: 'Message contains prohibited words' }, status: :unprocessable_entity
    end
  end

  # ... other private methods ...
end

# ... other actions and methods ...

changing logs
You can change logo by changing image inside the codebase. files located under app/public/brand-assets folder. and also change the name logo inside /home/chatwoot/chatwoot/config/installation_config.yml

