# MCP iCal Server

<div align="center">

🗓️ Natural Language Calendar Management for macOS

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://choosealicense.com/licenses/mit/)
[![Python 3.12+](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/)
[![MCP Compatible](https://img.shields.io/badge/MCP-Compatible-purple.svg)](https://modelcontextprotocol.io)

</div>

## 🌟 Overview

Transform how you interact with your macOS calendar using natural language! The mcp-ical server leverages the Model Context Protocol (MCP) to turn your calendar management into a conversational experience.

```
You: "What's my schedule for next week?"
Claude: "Let me check that for you..."
[Displays a clean overview of your upcoming week]

You: "Add a lunch meeting with Sarah tomorrow at noon"
Claude: "✨ 📅 Created: Lunch with Sarah Tomorrow, 12:00 PM"
```

## ✨ Features

### 📅 Event Creation
Transform natural language into calendar events instantly!

```
"Schedule a team lunch next Thursday at 1 PM at Bistro Garden"
↓
📎 Created: Team Lunch
   📅 Thursday, 1:00 PM
   📍 Bistro Garden
```

#### Supported Features:
- Custom calendar selection
- Location and notes
- Smart reminders
- Recurring events

#### Power User Examples:
```
🔄 Recurring Events:
"Set up my weekly team sync every Monday at 9 AM with a 15-minute reminder"

📝 Detailed Events:
"Schedule a product review meeting tomorrow from 2-4 PM in the Engineering calendar, 
add notes about reviewing Q1 metrics, and remind me 1 hour before"

📱 Multi-Calendar Support:
"Add a dentist appointment to my Personal calendar for next Wednesday at 3 PM"
```

### 🔍 Smart Schedule Management & Availability
Quick access to your schedule with natural queries:

```
"What's on my calendar for next week?"
↓
📊 Shows your upcoming events with smart formatting

"When am I free to schedule a 2-hour meeting next Tuesday?"
↓
🕒 Available time slots found:
   • Tuesday 10:00 AM - 12:00 PM
   • Tuesday 2:00 PM - 4:00 PM
```

### ✏️ Intelligent Event Updates
Modify events naturally:

```
Before: "Move tomorrow's team meeting to 3 PM instead"
↓
After: ✨ Meeting rescheduled to 3:00 PM
```

#### Update Capabilities:
- Time and date modifications
- Calendar transfers
- Location updates
- Note additions
- Reminder adjustments
- Recurring pattern changes

### 📊 Calendar Management
- View all available calendars
- Smart calendar suggestions
- Seamless Google Calendar integration when configured with iCloud

> 💡 **Pro Tip**: Since you can create events in custom calendars, if you have your Google Calendar synced with you iCloud Calendar, you can use this MCP server to create events in your Google Calendar too! Just specify the google calendar when creating/updating events

## 🚀 Quick Start

> 💡 **Note**: While these instructions focus on setting up the MCP server with Claude for Desktop, this server can be used with any MCP-compatible client. For more details on using different clients, see [the MCP documentation](https://modelcontextprotocol.io/quickstart/client).

### Prerequisites
- [uv package manager](https://github.com/astral-sh/uv)
- macOS with Calendar app configured
- An MCP client - [Claude for desktop](https://claude.ai/download) is recommend 

### Installation

Whilst this MCP server can be used with any MCP compatible client, the instructions below are for use with Claude for desktop.

1. **Clone and Setup**
```bash
# Clone the repository
git clone https://github.com/Omar-V2/mcp-ical.git
cd mcp-ical

# Install dependencies
uv sync
```

2. **Configure Claude for Desktop**

Create or edit `~/Library/Application\ Support/Claude/claude_desktop_config.json`:

```json
{
    "mcpServers": {
        "mcp-ical": {
            "command": "uv",
            "args": [
                "--directory",
                "/ABSOLUTE/PATH/TO/PARENT/FOLDER/mcp-ical",
                "run",
                "mcp-ical"
            ]
        }
    }
}
```

3. **Launch Claude for Calendar Access**

> ⚠️ **Critical**: Claude must be launched from the terminal to properly request calendar permissions. Launching directly from Finder will not trigger the permissions prompt.

Run the following command in your terminal.

```bash
/Applications/Claude.app/Contents/MacOS/Claude
```

### Manually Grant Calendar Access

Alternatively, if you're willing to use an undocumented macOS feature, you can [manually grant Claude calendar access](https://lenticular.zone/macos-tcc-claude-mcp/). The following method has been tested on macOS 15.4 and requires full disk access for your terminal emulator.

> ⚠️ **Warning**: This method modifies the TCC database directly, which is not recommended by Apple. Use at your own risk.

> ⚠️ Modifying the TCC database directly can potentially cause system instability or security issues. This is an unofficial method that bypasses Apple's permission system, and future macOS updates may change how permissions work. Always back up your TCC database before making changes (as shown below), and proceed at your own risk. If something goes wrong, you may need to reset all TCC permissions or restore from backup.

```bash
# Backup TCC database to your desktop
# This is a critical step! Always backup your TCC database before making changes.
cp ~/Library/Application\ Support/com.apple.TCC/TCC.db ~/Desktop/TCC_backup.db

# Grant Calendar access to Claude
# This command modifies the TCC database to allow access for the specified bundle identifier.
sqlite3 ~/Library/Application\ Support/com.apple.TCC/TCC.db <<EOF
REPLACE INTO access 
VALUES(
 'kTCCServiceCalendar',
 'com.anthropic.claudefordesktop',
 0,
 2,
 2,
 2,
 NULL,
 NULL,
 0,
 'UNUSED',
 NULL,
 16,
 CAST(strftime('%s','now') AS INTEGER),
 NULL,
 NULL,
 'UNUSED',
 0
);
EOF
```

After allowing `com.anthropic.claudefordesktop` access to the Calendar service, you can reopen Claude.app as you normally would.

If you want to revert the changes made to the TCC database, you can use macOS's built-in `tccutil` command to reset the Calendar permissions for Claude:

```bash
# Reset Calendar permissions for Claude
tccutil reset Calendar com.anthropic.claudefordesktop
```

4. **Start Using!**
```
Try: "What's my schedule looking like for next week?"
```

> 🔑 **Note**: When you first use a calendar-related command, macOS will prompt for calendar access. This prompt will only appear if you launched Claude from the terminal as specified above.
## 🧪 Testing

> ⚠️ **Warning**: Tests will create temporary calendars and events. While cleanup is automatic, only run tests in development environments.

```bash
# Install dev dependencies
uv sync --dev

# Run test suite
uv run pytest tests
```

## 🐛 Known Issues

### Recurring Events
- Non-standard recurring schedules may not always be set correctly
- Better results with Claude 3.5 Sonnet compared to Haiku
- Reminder timing for recurring all-day events may be off by one day

## 🤝 Contributing

Feedback and contributions are welcomed! Here's how you can help:

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request


## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built with [Model Context Protocol](https://modelcontextprotocol.io)
- MacOS Calendar integration built with [PyObjC](https://github.com/ronaldoussoren/pyobjc)
