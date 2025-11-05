# IEEE-HKN Event Reminder System

**Automated Discord notifications for Google Calendar events**

Version 2.2 | IEEE-HKN Epsilon Beta Chapter at ASU

[![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-V8-blue.svg)](https://developers.google.com/apps-script)
[![Version](https://img.shields.io/badge/version-2.0.0-green.svg)](WEBMASTER_GUIDE.md)
[![Status](https://img.shields.io/badge/status-production-success.svg)](WEBMASTER_GUIDE.md)

---

## Overview

This system automatically monitors the IEEE-HKN chapter's Google Calendar and sends timely reminder notifications to Discord at strategic intervals before events (2 weeks, 1 week, and 1 day).

## Key Features

- **Automated Reminders**: Sends notifications at 2 weeks, 1 week, and 1 day before events
- **Smart Scheduling**: Runs every 5 minutes with intelligent event processing
- **Meeting Link Extraction**: Automatically detects and includes Zoom, Google Meet, and Teams links
- **Duplicate Prevention**: Tracks sent reminders to avoid notification spam
- **Error Handling**: Self-recovering system with comprehensive error reporting
- **Testing Suite**: Built-in tools for verification and troubleshooting

## Technology Stack

- **Platform**: Google Apps Script (V8 Runtime)
- **APIs**: Google Calendar API, Discord Webhooks
- **Language**: JavaScript (ES6+)
- **Integration**: Google Workspace, Discord

## Quick Start

1. Open [Google Apps Script](https://script.google.com)
2. Create a new project or open existing "Event Reminder" project
3. Configure Discord webhook URL in the CONFIG object
4. Run `setupAutomaticReminders()` function
5. Grant necessary permissions
6. Verify setup with `runComprehensiveTest()`

## Documentation

**For complete setup, configuration, and maintenance instructions, see [WEBMASTER_GUIDE.md](WEBMASTER_GUIDE.md)**

The comprehensive guide includes:
- Detailed setup instructions
- Configuration options
- Testing procedures
- Maintenance protocols
- Troubleshooting solutions
- Code modification examples
- Security best practices

## System Requirements

- Google Workspace account with calendar access
- Discord server with webhook creation permissions
- Google Apps Script execution permissions

## File Structure

```
Reminder/
├── README.md                 # This file
├── WEBMASTER_GUIDE.md        # Comprehensive documentation
└── eventReminder.js          # Main system code
```

## Support

**For Issues**: Refer to the troubleshooting section in [WEBMASTER_GUIDE.md](WEBMASTER_GUIDE.md)

**External Resources**:
- [Google Apps Script Documentation](https://developers.google.com/apps-script)
- [Discord Webhooks Guide](https://discord.com/developers/docs/resources/webhook)

## Maintenance

**For Current Webmasters**: Please review the [WEBMASTER_GUIDE.md](WEBMASTER_GUIDE.md) thoroughly before making any modifications to the system.

**For Incoming Webmasters**: Schedule a walkthrough with the outgoing webmaster and reference the guide for complete system documentation.

## License

Maintained by IEEE-HKN Epsilon Beta Chapter Webmasters at Arizona State University.

---

**Last Updated**: November 2025
**Current Webmaster**: Vishesh Singh Rajput (2024-2025, 2025-2026)
