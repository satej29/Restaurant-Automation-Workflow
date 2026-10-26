# SATEJ Restaurant — AI Food Ordering Assistant

An AI-powered food ordering assistant built with **n8n**, **Google Gemini**, **Google Sheets**, and **WhatsApp**. The workflow is designed to answer common restaurant questions, check food inventory, and record orders in a Google Sheet.
[Demo Video](https://drive.google.com/file/d/1VfbNCnjIFzNblAN60ZxyG6fQk_nA_Ho0/view?usp=sharing)
## Features

- **AI chat assistant:** Uses Google Gemini through an n8n AI Agent.
- **Order collection:** Collects the customer's name, food item, and quantity.
- **Inventory lookup:** Reads available food items from the Google Sheets `Inventory` sheet.
- **FAQ support:** Reads restaurant information from the `FAQ` sheet.
- **Order logging:** Appends order details to the `Orders` sheet.
- **Conversation memory:** Uses n8n Simple Memory with a context window of 10.
- **WhatsApp workflow nodes:** Includes a WhatsApp message trigger and send-message node.

## Technology Stack

- [n8n](https://n8n.io/) — workflow automation
- Google Gemini — language model
- Google Sheets — inventory, FAQ, and order records
- WhatsApp integration for n8n — incoming and outgoing messages

## Workflow Overview

The supplied workflow JSON contains these main nodes:

| Node | Purpose |
|---|---|
| When chat message received | Starts the AI chat workflow |
| AI Agent | Applies the restaurant assistant instructions and uses connected tools |
| Google Gemini Chat Model | Provides the language model |
| Simple Memory | Maintains recent conversation context |
| Get Inventory | Reads the Inventory sheet |
| Get FAQ | Reads the FAQ sheet |
| Post Order | Appends order details to the Orders sheet |
| WhatsApp Trigger | Listens for incoming WhatsApp messages |
| Send message | WhatsApp send-message node |

The exported workflow contains both a chat-trigger path and a WhatsApp-trigger path. Check the connections and configure the WhatsApp response mapping before relying on the WhatsApp path in production.

## Google Sheets Setup

The workflow references a spreadsheet named **Food Delivery System** with these tabs:

### `Inventory`
Keep the food catalogue and available quantities here. The AI should check this sheet before confirming an order.

### `FAQ`
Store short answers to common questions such as:
- Restaurant opening hours
- Delivery time
- Accepted payment methods

### `Orders`
The configured append operation maps these columns:

- `Customer Name`
- `Food Item`
- `Quantity Ordered`
- `Order Date`
- `Status`

The workflow instructions also mention a `Description` field, but the supplied append-node mapping does not currently include it. Add and map that column if you need to store the description.

## Assistant Behaviour

The configured system instructions tell the assistant to:

1. Welcome customers and offer ordering, FAQ, and order/stock-check options.
2. Ask for the customer's name, item, and quantity when taking an order.
3. Check inventory before confirming an order.
4. Record orders in the Orders sheet.
5. Answer FAQs briefly.
6. Look up order status or stock when requested.
7. Direct cancellation requests to the restaurant owner rather than cancelling automatically.
8. Keep replies short, friendly, and suitable for chat.

**Important:** Verify the assistant's behaviour with test data before using it with real customers. In particular, make sure unavailable items are not logged as confirmed orders and that order-status and stock lookups work as expected.

## Setup

### 1. Requirements

- An n8n instance (cloud or self-hosted)
- A Google account with access to the Food Delivery System spreadsheet
- A Google Gemini API credential configured in n8n
- WhatsApp credentials and webhook configuration, if using WhatsApp

### 2. Import the workflow

1. Open n8n.
2. Import the supplied workflow JSON file.
3. Open each node and review its settings.
4. Reconnect credentials in your own n8n instance; exported credential references may not work in another account.
5. Save the workflow.

### 3. Configure Google Sheets

1. Open the Google Sheets nodes.
2. Select your own spreadsheet and the correct `Inventory`, `FAQ`, and `Orders` tabs.
3. Confirm the sheet column names match the node mappings.
4. Test each Sheets node with sample data.

### 4. Configure Google Gemini

1. Open the Google Gemini Chat Model node.
2. Select a valid Gemini model available to your account.
3. Add or select your own Google Gemini API credential.
4. Test the model connection.

The model name in an exported workflow can become unavailable or change. Choose a currently available model in your n8n instance if necessary.

### 5. Configure WhatsApp (optional)

1. Configure the WhatsApp Trigger and Send message nodes using your own supported WhatsApp Business Platform credentials.
2. Set up the required Meta app, phone number, permissions, and webhook settings.
3. Map the incoming message text and sender details into the AI Agent.
4. Map the AI Agent's response and the recipient phone number into the Send message node.
5. Test with an authorized test number before enabling the workflow.

A WhatsApp Trigger and a Send message node alone do not guarantee a complete end-to-end flow. Confirm the trigger-to-agent-to-send connections and required fields in your n8n version.

## Running and Testing

Test these scenarios before going live:

- Send `Hi` and check the welcome response.
- Place an order for an item that is in stock.
- Try ordering an item that is out of stock.
- Ask a common FAQ.
- Check stock for a known food item.
- Check an order using a valid order identifier, if order IDs are implemented.
- Request cancellation and verify the assistant gives the configured instructions.
- Confirm the correct data is written to Google Sheets.
- Test WhatsApp delivery separately from the n8n chat trigger.

## Security Notes

- **Never commit API keys, access tokens, passwords, or private credentials to GitHub.**
- Reconnect credentials through n8n's credential manager.
- Review spreadsheet sharing permissions and WhatsApp webhook access.
- Avoid publishing personal customer information in a public repository.
- The workflow instructions contain a restaurant-owner contact number. Review whether it should be public before publishing this project.

## Current Limitations to Review

- The supplied workflow has separate chat and WhatsApp trigger nodes; confirm the intended production path is connected end to end.
- The `Post Order` node maps five columns and does not map the `Description` field mentioned in the assistant instructions.
- The configured model identifier may need to be changed to a currently available Gemini model.
- Order IDs, cancellation updates, and delivered-status updates are not visibly implemented by the supplied nodes.
- Ensure inventory checks and order-write conditions are enforced by the workflow, not only by the assistant prompt.

## Project Structure

```text
.
├── README.md
└── workflow.json   # Exported n8n workflow (use your actual filename)
```

## License

No license is specified. Add a license file if you plan to distribute or open-source this project.
