## **Message API**

### **Description:**
This **API** allows you to send a message to a specified recipient using either a regular message (text, image, video, etc.) or a template message. The API leverages the WhatsApp Business API to send messages securely.

### **Base URL:**
```
https://api.growby.net/v3/messages
```
### **Header content**
```
{
  "ApiKey":<growby-api-key> // you can access your ApiKey from growby API setup page.
}
```
### **Request JSON format**
```
{
  {
  "from": "string",  // string, phone number with country code e.g. 91XXXXXXXXXX or +91XXXXXXXXXX
  "to": "string",    // string, phone number with country code e.g. 91XXXXXXXXXX or +91XXXXXXXXXX
  
  "message": {           // object, optional
    "type": "string",     // string, message type ("text", "image", "document", "video")
    "text": "string",     // string, max length: 4096 characters (only for type "text")
    "link": "string",     // string, valid URL (for media types like image, video, document)
    "filename": "string" // string, max length: 256 characters (only for type "documents")
  },
  
  "message_template": {   // object, optional (only one of "message" or "message_template" can be provided)
    "name": "string",     // string, template name, max length: 512 characters
    "langcode": "string", // string, valid language code (e.g., "en_US")
    "components": [         // array, list of template components (header, body, footer, button, carousel)
      {
        "type": "string",  // string, component type ("header", "body", "footer", "button", "carousel")
        "parameters": [    // array, parameters for the component (e.g., text or media for header)
          {
            "format": "string",  // string, format of parameter ("text", "image", "document", etc.)
            "value": "string"    // string, max length: 1024 characters (parameter text or media)
          }
        ]
      },
      {
        "type": "string",   // string, component type ("header", "body", "footer", "button", "carousel")
        "parameters": [     // array, parameters for the body
          {
            "format": "string",  // string, format of parameter ("text")
            "value": "string"    // string, max length: 1024 characters (body text)
          }
        ]
      },
      {
        "type": "string",   // string, component type ("button")
        "parameters": [     // array, parameters for the button
          {
            "format": "string",  // string, format of parameter ("text")
            "value": "string",   // string, max length: 1024 characters (button text)
            "sub_type": "string" // string, button sub_type ("url", "quick_reply")
          }
        ]
      },
      {
        "type": "string",   // string, component type ("carousel")
        "cards": [           // array, list of cards in the carousel (max 10 cards)
          {
            "index": "string", // string, card index (required for each card)
            "card_components": [  // array, components of the card (header, body, button)
              {
                "type": "string",  // string, component type ("header")
                "parameters": [    // array, parameters for header
                  {
                    "format": "string",  // string, format of parameter ("text")
                    "value": "string"    // string, max length: 1024 characters (header text)
                  }
                ]
              },
              {
                "type": "string",   // string, component type ("body")
                "parameters": [     // array, parameters for body
                  {
                    "format": "string",  // string, format of parameter ("text")
                    "value": "string"    // string, max length: 1024 characters (body text)
                  }
                ]
              },
              {
                "type": "string",   // string, component type ("button")
                "parameters": [     // array, parameters for button
                  {
                    "format": "string",  // string, format of parameter ("text")
                    "value": "string",   // string, max length: 1024 characters (button text)
                    "sub_type": "string" // string, button sub_type ("url", "quick_reply")
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  },
  "show_in_inbox": "boolean"  // boolean, optional (true or false) 
}
```

### **Response JSON format**
#### **Success**
```
{
  "statuscode": 200,  // HTTP status code 200 for success
  "response": "Message sent",  // Descriptive message indicating success
  "data": <MessageObject>
}

```
#### **Fail**
```
{
  "statuscode": 201,  // HTTP status code 201 for error
  "response":<ErrorMessage>,  // Error message describing the issue
  "data": <ErrorReason> // Reason for the error
}
```

### **Some examples**
#### **Template message** (Standard)
If you want to send standard template message here is how you need to construct the JSON.
##### **Request JSON**
```
{
  "from": "91XXXXXXXXX",
  "to": "91XXXXXXXXX",
  "message_template": {
    "name": <your-template-name>,
    "langcode": "en_US", // language code the template is written in.
    "components": [
      {
        "type": "header",
        "parameters": [
          {
            "format": "text", // can be media type e.g. "image", "video", "document".
            "value": "Order #98765" // link for media if media type.
          }
        ]
      },
      {
        "type": "body",
        "parameters": [
          {
            "format": "text",
            "value": "Customer"
          }
        ]
      },
      // remember that your buttons should be in correct sequence as defined in your template.
      {
        "type": "button",
        "sub_type": "url",
        "parameters": [
          {
            "format": "text",
            "value": "Track Order"
          }
        ]
      },
      {
        "type": "button" // if the button is of sub type "quick_reply", just specify the button in correct sequence.
      }
      // More buttons can come here, the index can increase as we add more buttons, maximum 3 buttons allowed.
  }
  "show_in_inbox": true // can be false if not required to show in Growby platform's inbox
}

```
#### **Template message** (Carousel)
If you want to send carousel template message, pay attention on how you create the carousel components in JSON.
##### **Request JSON**
```
{
  "from": "91XXXXXXXXXX",
  "to": "91XXXXXXXXX",
  "message_template": {
    "name": <your-template-name>,
    "langcode": "en_US",
    "components": [
      {
        "type": "body",
        "parameters": [
          {
            "format": "text",
            "value": "Order #1234"
          }
        ]
      },
      {
        "type": "carousel",
        "cards": [     // define all the card inside card list, minimum 2 and maximum 10
          {
            "index": "0",
            "card_components": [
              {
                "type": "header",
                "parameters": [
                  {
                    "format": "text",
                    "value": "Deal 1"
                  }
                ]
              },
              {
                "type": "body",
                "parameters": [
                  {
                    "format": "text",
                    "value": "Get 10% off on your next order."
                  }
                ]
              },
              {
                "type": "button",
                "parameters": [
                  {
                    "format": "text",
                    "value": "Shop Now",
                    "sub_type": "url"
                  }
                ]
              }
            ]
          },
          {
            "index": "1",
            "card_components": [
              {
                "type": "header",
                "parameters": [
                  {
                    "format": "text",
                    "value": "Deal 2"
                  }
                ]
              },
              {
                "type": "body",
                "parameters": [
                  {
                    "format": "text",
                    "value": "Free shipping on orders over $50."
                  }
                ]
              },
              // remember that your buttons should be in correct sequence as defined in your template. minimum 1 and maximum 2 button allowed in carousel cards
              {
                "type": "button",
                "sub_type": "url",
                "parameters": [
                  {
                    "format": "text",
                    "value": "Track Order"
                  }
                ]
              },
              {
                "type": "button" // if the button is of sub type "quick_reply", just specify the button in correct sequence.
              }
              // More buttons can come here, the index can increase as we add more buttons, maximum 3 buttons allowed.
            ]
          }
        ]
      }
    ]
  },
  "show_in_inbox": true // can be false if not required to show in Growby platform's inbox
}
```
#### **Simple text message**
##### **Request JSON**
```
{
  "from": "91XXXXXXXXX",
  "to": "91XXXXXXXXX",
  "message": {
    "type": "text",
    "text": "This is a simple text message."
  }
}
```
#### **Simple document message**
##### **Request JSON**
```
{
  "from": "91XXXXXXXXX",
  "to": "91XXXXXXXXX",
  "message": {
    "type": "document", // can be "image", "text", "video".
    "text": "Here is the document you requested.",    // optional, define your caption for media here.
    "link": "https://example.com/files/sample.pdf", // link to your media if media type.
    "filename": "sample.pdf" // optional for doucment.
  }
}
```

### **Supported media**
- Image (jpeg, png), max size 5 MB
- Video (mp4, 3gp), max size 16 MB
- Document (pdf), max size 100 MB

