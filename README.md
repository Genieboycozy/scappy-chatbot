.github/workflows
from flask import Flask, request, jsonify, render_template_string

app = Flask(__name__)

# HTML template for chatbot integration
html_template = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Scapy</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f4f4f9;
        }
        .chat-container {
            width: 100%;
            max-width: 400px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
            background: white;
            overflow: hidden;
        }
        .chat-header {
            background: #4CAF50;
            color: white;
            padding: 10px;
            text-align: center;
        }
        .chat-messages {
            height: 300px;
            overflow-y: auto;
            padding: 10px;
            background: #f9f9f9;
        }
        .chat-input {
            display: flex;
            padding: 10px;
            border-top: 1px solid #ddd;
            background: white;
        }
        .chat-input input {
            flex: 1;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        .chat-input button {
            margin-left: 10px;
            padding: 10px 15px;
            background: #4CAF50;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="chat-container">
        <div class="chat-header">Scapy</div>
        <div class="chat-messages" id="messages"></div>
        <div class="chat-input">
            <input type="text" id="userInput" placeholder="Type your message here...">
            <button onclick="sendMessage()">Send</button>
        </div>
    </div>
    <script>
        const messagesContainer = document.getElementById('messages');

        function appendMessage(sender, text) {
            const messageDiv = document.createElement('div');
            messageDiv.textContent = `${sender}: ${text}`;
            messagesContainer.appendChild(messageDiv);
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }

        async function sendMessage() {
            const userInput = document.getElementById('userInput');
            const userMessage = userInput.value;
            if (!userMessage) return;

            appendMessage('You', userMessage);
            userInput.value = '';

            const response = await fetch('/chat', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ message: userMessage })
            });

            const data = await response.json();
            appendMessage('Bot', data.reply);
        }

        // Initial message
        appendMessage('Bot', 'Hi! What type of landscaping service are you looking for today?');
    </script>
</body>
</html>
"""

# Backend logic for chatbot
@app.route('/')
def home():
    return render_template_string(html_template)

@app.route('/chat', methods=['POST'])
def chat():
    user_message = request.json.get('message', '').lower()

    if "lawn care" in user_message:
        reply = "Great! We offer mowing, edging, and fertilizing. Would you like a free quote?"
    elif "garden design" in user_message:
        reply = "Wonderful! We can design custom gardens. Would you like to schedule a consultation?"
    elif "tree service" in user_message:
        reply = "We provide tree trimming, removal, and health assessments. How can we assist you?"
    else:
        reply = "I can help with lawn care, garden design, or tree services. What do you need?"

    return jsonify({"reply": reply})

if __name__ == '__main__':
    app.run(debug=True)

