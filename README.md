<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Joann Chatbot</title>
  <style>
    /* General Styles */
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
    }

    /* Chatbot Container */
    #chatbot-container {
      position: fixed;
      bottom: 20px;
      right: 20px;
      width: 300px;
      height: 400px;
      background-color: #f9f9f9;
      border: 1px solid #ccc;
      border-radius: 10px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
      display: none;
      flex-direction: column;
    }

    /* Chatbot Header */
    #chatbot-header {
      background-color: #007bff;
      color: white;
      padding: 10px;
      border-top-left-radius: 10px;
      border-top-right-radius: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    #chatbot-header h3 {
      margin: 0;
      font-size: 16px;
    }

    #minimize-btn {
      background: none;
      border: none;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }

    /* Chat Area */
    #chat-area {
      flex: 1;
      padding: 10px;
      overflow-y: auto;
      border-bottom: 1px solid #ccc;
    }

    .message {
      margin-bottom: 10px;
      padding: 8px;
      border-radius: 5px;
      max-width: 80%;
    }

    .message.user {
      background-color: #007bff;
      color: white;
      margin-left: auto;
    }

    .message.bot {
      background-color: #e9e9e9;
      color: #333;
      margin-right: auto;
    }

    .message.bot a {
      color: #007bff;
      text-decoration: underline;
    }

    /* Input Area */
    #input-area {
      display: flex;
      padding: 10px;
      background-color: #fff;
      border-bottom-left-radius: 10px;
      border-bottom-right-radius: 10px;
    }

    #user-input {
      flex: 1;
      padding: 5px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    #send-btn {
      margin-left: 10px;
      padding: 5px 10px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    /* Floating Chat Button */
    #chat-btn {
      position: fixed;
      bottom: 20px;
      right: 20px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      font-size: 18px;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    }
  </style>
</head>
<body>
  <!-- Floating Chat Button -->
  <button id="chat-btn">💬</button>

  <!-- Chatbot Container -->
  <div id="chatbot-container">
    <!-- Chatbot Header -->
    <div id="chatbot-header">
      <h3>Joann Chatbot</h3>
      <button id="minimize-btn">➖</button>
    </div>

    <!-- Chat Area -->
    <div id="chat-area">
      <div class="message bot">Hello! Welcome to Joann support. How can I help you today?</div>
    </div>

    <!-- Input Area -->
    <div id="input-area">
      <input type="text" id="user-input" placeholder="Type a message...">
      <button id="send-btn">Send</button>
    </div>
  </div>

  <script>
    // DOM Elements
    const chatBtn = document.getElementById('chat-btn');
    const chatbotContainer = document.getElementById('chatbot-container');
    const minimizeBtn = document.getElementById('minimize-btn');
    const chatArea = document.getElementById('chat-area');
    const userInput = document.getElementById('user-input');
    const sendBtn = document.getElementById('send-btn');

    // Toggle Chat Window
    chatBtn.addEventListener('click', () => {
      chatbotContainer.style.display = chatbotContainer.style.display === 'flex' ? 'none' : 'flex';
    });

    // Minimize Chat Window
    minimizeBtn.addEventListener('click', () => {
      chatbotContainer.style.display = 'none';
    });

    // Send Message
    sendBtn.addEventListener('click', sendMessage);
    userInput.addEventListener('keypress', (e) => {
      if (e.key === 'Enter') sendMessage();
    });

    // Keyword-Based Responses
    const responses = [
      // Order-Related Questions
      { keywords: ['track', 'order'], 
        response: 'You can track your order by logging into your Joann account and visiting the "Order History" section. You’ll receive a tracking number via email once your order ships.' },
      
      { keywords: ['cancel', 'modify', 'order'],
        response: 'Orders can only be canceled or modified if they haven’t entered the shipping process. Please contact Joann Customer Service immediately for assistance.' },

      // Shipping & Delivery
      { keywords: ['shipping', 'options', 'cost'],
        response: 'Joann offers standard, expedited, and in-store pickup options. Shipping costs vary based on order size and delivery speed. Free shipping may be available for qualifying orders.' },
      
      { keywords: ['how long', 'delivery', 'ship'],
        response: 'Standard shipping typically takes 3–7 business days. Expedited options may arrive sooner. Delivery times depend on your location and product availability.' },

      // Returns & Exchanges
      { keywords: ['return', 'policy'],
        response: 'Most unopened items can be returned within 90 days with a receipt. Final sale items (e.g., clearance) are non-returnable. Visit the <a href="https://www.joann.com/returns-exchanges/" target="_blank">Returns & Exchanges</a> page for details.' },
      
      { keywords: ['return', 'online', 'store'],
        response: 'Bring your item, receipt, and packing slip to any Joann store. Ensure the item is in its original condition.' },

      // Product & Inventory
      { keywords: ['available', 'store', 'online'],
        response: 'Check real-time availability by entering your ZIP code on the product page. For online purchases, add the item to your cart to confirm stock.' },
      
      { keywords: ['backorder'],
        response: 'Some items may be available for backorder. If this option is listed on the product page, you can place an order for future delivery.' },

      // Product Pricing Information
      { keywords: ['low', 'price', 'products'],
        response: 'Joann offers a variety of low-price products, typically under $10. These include crafting essentials like yarn, fabric scraps, and basic tools. Check out the <a href="https://www.joann.com/clearance/" target="_blank">Clearance section</a> for great deals!' },
      
      { keywords: ['medium', 'price', 'products'],
        response: 'Medium-price products at Joann range from $10 to $50. This includes items like sewing machines, premium yarns, and crafting kits. Visit the <a href="https://www.joann.com/shop/" target="_blank">Shop section</a> to explore.' },
      
      { keywords: ['high', 'price', 'products'],
        response: 'High-price products at Joann are typically above $50. These include high-end sewing machines, large furniture pieces, and bulk crafting supplies. Check out the <a href="https://www.joann.com/shop/" target="_blank">Shop section</a> for more details.' },

      // Coupons & Promotions
      { keywords: ['use', 'coupon', 'promo'],
        response: 'Apply your coupon code at checkout under the "Promo Code" field. Ensure the coupon is valid for the items in your cart.' },
      
      { keywords: ['weekly', 'coupons'],
        response: 'Weekly coupons are available on the Joann website, mobile app, or via email newsletter. <a href="https://www.joann.com/coupons/" target="_blank">Sign up for alerts</a> to receive them directly!' },

      // Store Information
      { keywords: ['store', 'hours'],
        response: 'Use the <a href="https://www.joann.com/store-locator/" target="_blank">Store Locator</a> tool on Joann.com and enter your location to view hours, address, and contact details for your nearest store.' },
      
      { keywords: ['classes', 'workshops'],
        response: 'Many Joann stores host crafting classes! Check the <a href="https://www.joann.com/classes-events/" target="_blank">Classes & Events</a> section or contact your local store for schedules.' },

      // Account & Technical Support
      { keywords: ['reset', 'password'],
        response: 'Click "Forgot Password?" on the login page and follow the instructions sent to your email to reset your password.' },
      
      { keywords: ['website', 'loading', 'cart'],
        response: 'Clear your browser cache or try accessing Joann.com in a different browser. If the issue persists, contact <a href="https://www.joann.com/help/contact-us/">Customer Support</a>.' },

      // Gift Cards
      { keywords: ['gift card', 'balance'],
        response: 'Visit the <a href="https://www.joann.com/gift-cards/balance/" target="_blank">Gift Card Balance</a> page and enter your card number and PIN.' },
      
      { keywords: ['use', 'gift card', 'online'],
        response: 'Yes! Enter your gift card number and PIN during checkout under the payment section.' },

      // General fallbacks
      { keywords: ['hello', 'hi', 'hey'], 
        response: 'Hello! Welcome to Joann support. How can I help you today?' },
      
      { keywords: ['bye', 'goodbye'],
        response: 'Thank you for contacting Joann! Happy crafting!' },
      
      { keywords: ['help'],
        response: 'I can help with order tracking, returns, product availability, coupons, store hours, and more! Ask me anything.' }
    ];

    // Send Message Function
    function sendMessage() {
      const userMessage = userInput.value.trim().toLowerCase();
      if (userMessage === '') return;

      // Display User Message
      chatArea.innerHTML += `<div class="message user">${userInput.value}</div>`;

      // Find and Display Bot Response
      let botResponse = `I'm sorry, I don't have information on that. For more complex inquiries, please visit <a href="https://www.joann.com/help/" target="_blank">Joann Help Center</a> or contact customer support at 1-888-739-4120.`;
      
      for (const item of responses) {
        if (item.keywords.some(keyword => userMessage.includes(keyword))) {
          botResponse = item.response;
          break;
        }
      }
      
      chatArea.innerHTML += `<div class="message bot">${botResponse}</div>`;
      
      // Clear Input and Scroll to Bottom
      userInput.value = '';
      chatArea.scrollTop = chatArea.scrollHeight;
    }
  </script>
</body>
</html>
