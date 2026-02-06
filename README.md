# 🤖 AI WhatsApp Appointment Setter

An intelligent, automated appointment booking system powered by AI that handles the entire appointment scheduling process through WhatsApp. Built with n8n, this workflow integrates Google Calendar, Google Sheets, and Gmail to create a seamless booking experience.

[![n8n](https://img.shields.io/badge/n8n-Workflow-FF6D5A?logo=n8n)](https://n8n.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🌟 Features

### Core Functionality
- **🗣️ Voice & Text Support**: Handles both text messages and voice notes via WhatsApp
- **🤖 AI-Powered Conversations**: Natural language processing for smooth booking interactions
- **📅 Smart Scheduling**: Automatically finds available time slots and manages calendar conflicts
- **🌍 Multi-Timezone Support**: Converts time zones automatically for global clients
- **💾 Data Persistence**: Stores all appointment data in Google Sheets
- **📧 Email Confirmations**: Sends automated confirmation emails to clients
- **🧠 Conversation Memory**: Maintains context throughout the booking conversation

### Advanced Features
- **Voice Transcription**: Converts WhatsApp voice messages to text using OpenAI Whisper
- **Smart Time Zone Handling**: Respects DST and converts between user's local time and business hours
- **Duplicate Prevention**: Checks for existing customers by email before creating new records
- **Flexible Office Hours**: Configurable business hours with lunch break support
- **Custom Appointment Duration**: Default 60-minute slots, customizable per request

## 🏗️ Architecture

```
WhatsApp Message → Switch (Text/Audio) → AI Agent → Calendar/Sheets/Gmail → Response
                        ↓
                   Audio Processing
                   (Whisper API + Cleanup)
```

### Workflow Components

1. **WhatsApp Trigger**: Receives incoming messages
2. **Message Router**: Separates text and voice messages
3. **Voice Processor**: Transcribes and cleans audio messages
4. **AI Agent**: Powered by **Google Gemini** or **OpenAI GPT** (your choice) with tool access to:
   - Google Calendar (read, create, delete events)
   - Google Sheets (read, add, update rows)
   - Gmail (send confirmations)
5. **Memory System**: Maintains conversation context per user
6. **Response Handler**: Sends formatted replies via WhatsApp

### 🤖 AI Model Options

This workflow supports **two AI providers** - choose the one that fits your needs:

| Feature | Google Gemini | OpenAI GPT |
|---------|---------------|------------|
| **Cost** | ⭐⭐⭐ Free tier available | 💰 Pay per token |
| **Speed** | ⚡ Very fast | ⚡ Fast |
| **Quality** | ✅ Excellent | ✅ Excellent |
| **Tool Calling** | ✅ Native support | ✅ Native support |
| **Best For** | Budget-conscious, high volume | Advanced reasoning, complex tasks |

**Default**: The workflow comes configured with **Google Gemini** for cost-effectiveness, but you can easily switch to **OpenAI GPT-4** or **GPT-4o-mini**.

## 📋 Prerequisites

Before setting up this workflow, ensure you have:

- [n8n](https://n8n.io/) (self-hosted or cloud)
- **WhatsApp Business API** account
- **Google Cloud Platform** account with APIs enabled:
  - Google Calendar API
  - Google Sheets API
  - Gmail API
- **AI Provider** (choose one or both):
  - **Option A**: [Google Gemini API](https://ai.google.dev/) key (Recommended - Free tier available)
  - **Option B**: [OpenAI API](https://platform.openai.com/) key (GPT-4, GPT-4o, or GPT-4o-mini)
- **OpenAI API** key (for voice transcription with Whisper - required if using voice messages)

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/ai-whatsapp-appointment-setter.git
cd ai-whatsapp-appointment-setter
```

### 2. Import into n8n

1. Open your n8n instance
2. Go to **Workflows** → **Import from File**
3. Select `ai_appointment_setter_workflow.json`
4. Click **Import**

### 3. Configure Credentials

Set up the following credentials in n8n:

#### WhatsApp Business API
- Navigate to **Credentials** → **Add Credential**
- Select **WhatsApp Trigger API**
- Enter your WhatsApp Business credentials

#### Google Services
For each Google service (Calendar, Sheets, Gmail):
1. Create OAuth2 credentials in Google Cloud Console
2. Add credentials in n8n
3. Authorize access

#### AI Models
Choose your preferred AI provider:

**Option A: Google Gemini (Recommended)**
- **Google Gemini**: Add your Gemini API key
  - Get it from: https://ai.google.dev/
  - Models: `gemini-pro`, `gemini-1.5-flash`, `gemini-1.5-pro`
  - Free tier: 60 requests per minute

**Option B: OpenAI GPT**
- **OpenAI**: Add your OpenAI API key
  - Get it from: https://platform.openai.com/api-keys
  - Models: `gpt-4o-mini` (recommended), `gpt-4o`, `gpt-4-turbo`
  - Pricing: Pay per token

**For Voice Transcription (Required if using voice messages)**
- **OpenAI Whisper**: Add your OpenAI API key (same as above)
  - Model: `whisper-1`

### 4. Create Google Sheet

Create a Google Sheet with the following columns:

| Email | Name | Phone | Location | Topic | Appointment Date |
|-------|------|-------|----------|-------|------------------|

Update the workflow with your Google Sheet ID in the following nodes:
- "Get row(s) in sheet in Google Sheets"
- "Google Sheets add rows"
- "Google Sheets update rows"

Replace `YOUR_GOOGLE_SHEET_ID` with your actual Sheet ID.

### 5. Configure Google Calendar

Update the calendar email address in:
- "Calendar read"
- "Calendar create"
- "Calendar delete"

Replace `your-calendar@gmail.com` with your actual calendar email.

### 6. Set Business Hours

Modify the AI Agent's system message to match your business hours:

```
Monday to Friday
Morning block: 09:00-12:00
Afternoon block: 13:00-17:00
```

Update the time zone from "Pakistan Time Zone" to your local timezone.

### 7. Activate the Workflow

1. Review all credential connections
2. Test with a sample WhatsApp message
3. Activate the workflow

---

## 🔄 Switching Between AI Models

The workflow is pre-configured with **Google Gemini**, but you can easily switch to **OpenAI GPT**:

### To Switch to OpenAI GPT:

1. **Open the AI Agent node**
2. **Disconnect the Google Gemini Chat Model** node
3. **Add an OpenAI Chat Model** node:
   - Type: `@n8n/n8n-nodes-langchain.lmChatOpenAi`
   - Configure with your OpenAI credentials
   - Select model: `gpt-4o-mini` (recommended) or `gpt-4o`
4. **Connect it to the AI Agent** node (ai_languageModel input)
5. **Save and test**

### To Use Both (Advanced):

You can create two separate workflows:
- One for **Gemini** (free tier, high volume)
- One for **GPT-4o** (complex queries, premium service)

Use a Switch node to route based on message complexity or customer tier.

---

## 💰 Cost Comparison

| Scenario | Google Gemini | OpenAI GPT-4o-mini | OpenAI GPT-4o |
|----------|---------------|-------------------|---------------|
| **100 appointments/month** | Free | ~$2-3 | ~$8-12 |
| **1,000 appointments/month** | Free* | ~$20-30 | ~$80-120 |
| **Voice messages (100)** | N/A | ~$5 (Whisper) | ~$5 (Whisper) |

*Google Gemini free tier: 60 requests/minute, 1,500 requests/day

**Recommendation**: Start with **Google Gemini** (free), switch to **GPT-4o-mini** if you need more advanced features.

## 💬 Usage

### Booking an Appointment

Users can start a conversation by sending a WhatsApp message to your business number:

**Example Conversation:**

```
User: Hi, I'd like to book an appointment

Bot: Would you like to book an appointment?

User: Yes

Bot: Great! Please provide your email address.

User: john@example.com

Bot: Thank you! What's your full name?

User: John Doe

Bot: Got it! What's your phone number?

User: +1234567890

Bot: What's your location or time zone?

User: New York, EST

Bot: What would you like to discuss during your appointment?

User: Website redesign consultation

Bot: Here are the next available time slots:
- Thursday at 10:00 AM
- Thursday at 2:00 PM
- Friday at 11:00 AM
- Friday at 3:00 PM
- Monday at 9:00 AM

User: Thursday at 2:00 PM works for me

Bot: Perfect! Your appointment has been confirmed for Thursday at 2:00 PM EST. 
You'll receive a confirmation email shortly.
```

### Voice Messages

Users can also send voice notes! The system will:
1. Download the audio file
2. Transcribe using OpenAI Whisper
3. Clean up the transcription
4. Process as a normal text message

## 🔧 Customization

### Modify Appointment Duration

In the "Calendar create" node, update:
```json
"end": "={{ $fromAI(\"end\", \"date and time for when the event should end\") }}"
```

### Change AI Behavior

Edit the system message in the "AI Agent" node to customize:
- Greeting messages
- Information collection order
- Available time slots
- Response style

### Add More Google Sheets Columns

1. Update the schema in "Google Sheets add rows" and "Google Sheets update rows"
2. Modify the AI prompt to collect additional information

### Configure Email Template

Customize the Gmail confirmation message in the AI Agent's system prompt:

```
The email must include:
- Confirmed appointment date and time (in user's time zone)
- User's name and topic of discussion
- Your custom branding/signature
- Cancellation/rescheduling instructions
```

## 🔍 Troubleshooting

### Common Issues

**WhatsApp not receiving messages:**
- Verify WhatsApp Business API credentials
- Check webhook configuration
- Ensure workflow is activated

**Calendar events not being created:**
- Confirm Google Calendar API is enabled
- Check calendar permissions
- Verify time format in requests

**Voice transcription failing:**
- Validate OpenAI API key
- Check audio file format support
- Review API rate limits

**Data not saving to Google Sheets:**
- Confirm Sheet ID is correct
- Check column names match exactly
- Verify Google Sheets API permissions

### Debug Mode

Enable debug mode in n8n to view:
- Node execution data
- Error messages
- API responses

## 📊 Data Storage

All appointment data is stored in Google Sheets with the following structure:

- **Email**: Unique identifier for each customer
- **Name**: Customer's full name
- **Phone**: Contact number
- **Location**: Time zone or physical location
- **Topic**: Discussion topic/reason for appointment
- **Appointment Date**: Scheduled date and time (in business timezone)

## 🔐 Security Considerations

- **API Keys**: Store securely using n8n's credential system
- **Data Privacy**: Ensure Google Sheets has appropriate access restrictions
- **WhatsApp Security**: Use official WhatsApp Business API only
- **Email Privacy**: Consider encryption for sensitive appointment details

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Development Setup

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [n8n](https://n8n.io/) - Workflow automation platform
- [Google Gemini](https://ai.google.dev/) - AI language model (default)
- [OpenAI GPT](https://openai.com/) - Alternative AI language model
- [OpenAI Whisper](https://openai.com/research/whisper) - Speech recognition
- [WhatsApp Business API](https://business.whatsapp.com/) - Messaging platform

## 📧 Support

For questions or issues:
- Open an [Issue](https://github.com/yourusername/ai-whatsapp-appointment-setter/issues)
- Contact: hassankhann900@gmail.com

## 🗺️ Roadmap

- [ ] Multi-language support
- [ ] SMS fallback option
- [ ] Calendar sync with multiple calendars
- [ ] Automated reminders 24 hours before appointments
- [ ] Rescheduling and cancellation via WhatsApp
- [ ] Payment integration
- [ ] Analytics dashboard
- [ ] Slack notifications for new bookings

---

**Made with ❤️ by Muhammad Hassan**

⭐ Star this repo if you find it helpful!
