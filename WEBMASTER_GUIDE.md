# IEEE-HKN EVENT REMINDER SYSTEM
**Documentation for Webmasters**
**Version 2.2**

---

## TABLE OF CONTENTS

1. [Overview](#1-overview)
2. [What This System Does](#2-what-this-system-does)
3. [How It Works](#3-how-it-works)
4. [Getting Started](#4-getting-started)
5. [Configuration Guide](#5-configuration-guide)
6. [Testing & Verification](#6-testing--verification)
7. [Maintenance & Monitoring](#7-maintenance--monitoring)
8. [Troubleshooting](#8-troubleshooting)
9. [Modifying the System](#9-modifying-the-system)
10. [Important Notes & Best Practices](#10-important-notes--best-practices)

---

## 1. OVERVIEW

This is an automated event reminder system built with Google Apps Script that monitors the IEEE-HKN chapter's Google Calendar and sends timely notifications to your **Discord and Slack** channels simultaneously.

### KEY FEATURES:

- Automatically sends reminders at 3 intervals: 2 weeks, 1 week, and 1 day before events
- **Dual platform support**: Sends to both Discord and Slack at the same time
- Runs every 5 minutes in the background
- Extracts meeting links (Zoom, Google Meet, Microsoft Teams) from events
- Prevents duplicate reminders with smart tracking
- Includes comprehensive error handling and self-recovery
- Built-in testing tools for verification
- Independent enable/disable controls for each platform

---

## 2. WHAT THIS SYSTEM DOES

### REMINDER SCHEDULE:

**[2 WEEKS] 2 WEEKS BEFORE EVENT (14 days)**
- Mentions: @everyone
- Time Window: ±2 hours (triggers anytime within 2 hours of the target)
- Purpose: Early awareness for planning

**[1 WEEK] 1 WEEK BEFORE EVENT (7 days)**
- Mentions: @everyone
- Time Window: ±2 hours
- Purpose: Mid-point reminder for preparation

**[1 DAY] 1 DAY BEFORE EVENT (24 hours)**
- Mentions: @here
- Time Window: ±1 hour
- Purpose: Final reminder for immediate action

### EXAMPLE TIMELINE:

If you have an event on November 20th at 6:00 PM:
- November 6th: 2-week reminder sent
- November 13th: 1-week reminder sent
- November 19th: 1-day reminder sent

---

## 3. HOW IT WORKS

### TECHNICAL FLOW:

1. A time-based trigger runs the system every 5 minutes
2. System fetches events from "HKN Events" calendar for the next 15 days
3. For each event, it calculates the time until the event starts
4. Checks if any reminder interval matches (2 weeks, 1 week, or 1 day)
5. Verifies the reminder hasn't been sent already (duplicate prevention)
6. Creates formatted messages for both Discord and Slack
7. Sends messages to Discord (via webhook) and Slack (via API) simultaneously
8. Records that the reminder was sent to prevent duplicates

### SMART FEATURES:

- **[CACHE] CACHING:** Calendar information is cached for 1 hour to improve performance
- **[RETRY] RETRY LOGIC:** If Discord is down, it will retry up to 3 times
- **[BATCH] BATCH PROCESSING:** Handles up to 10 events per check to avoid timeouts
- **[GUARD] DUPLICATE PREVENTION:** Each reminder is tracked for 12 hours to prevent spam
- **[MAINT] MAINTENANCE MODE:** Can be disabled temporarily without deleting triggers
- **[CLEAN] AUTO-CLEANUP:** Old reminder records are automatically cleaned every 30 days

---

## 4. GETTING STARTED

### PREREQUISITES:

1. Access to IEEE-HKN ASU Google Workspace account
2. A Google Calendar named "HKN Events" (or update the CONFIG)
3. Admin access to the IEEE-HKN Discord server
4. Discord webhook URL for the announcements channel
5. Slack workspace with app creation permissions
6. Slack bot token and channel ID (with scopes: chat:write, chat:write.public, channels:read, chat:write.customize)

### INITIAL SETUP STEPS:

**[STEP 1] Open Google Apps Script**
- Go to: script.google.com
- Open the existing "Event Reminder" project
- OR create new project and paste the eventReminder.js code

**[STEP 2] Configure Discord Webhook**
- In Discord: Server Settings > Integrations > Webhooks
- Create a webhook for your announcements channel
- Copy the webhook URL
- In the script, update line 7 with your webhook URL:
  ```
  DISCORD_WEBHOOK_URL: 'https://discord.com/api/webhooks/YOUR_WEBHOOK_HERE'
  ```

**[STEP 3] Set Calendar Name**
- Verify your calendar is named "HKN Events"
- OR update line 14 to match your calendar name:
  ```
  CALENDAR_NAME: 'Your Calendar Name Here'
  ```

**[STEP 4] Run Initial Setup**
- In Apps Script editor, select function: setupAutomaticReminders
- Click "Run" button
- Grant permissions when prompted
- You should receive a Discord message confirming setup

**[STEP 5] Configure Slack (Optional)**
- Create Slack app at api.slack.com/apps with required bot scopes
- Install app to workspace and obtain Bot User OAuth Token
- Add bot to your Slack channel
- Add `SLACK_BOT_TOKEN` and `SLACK_CHANNEL_ID` to CONFIG (lines 10-11)
- Set `SLACK_ENABLED: true` in CONFIG.FEATURES (line 57)
- Test with: `testSlackIntegration()`

**[STEP 6] Verify Installation**
- Run function: runComprehensiveTest
- Check the execution log (View > Logs)
- Verify Discord received a test message
- Verify Slack received a test message (if enabled)

### ACTIVATION:

Once setup is complete, the system runs automatically and sends to both platforms!

---

## 5. CONFIGURATION GUIDE

All configuration is in the CONFIG object at the top of eventReminder.js (Lines 6-53)

### BASIC SETTINGS:

**[CONFIG] DISCORD_WEBHOOK_URL**
- Your Discord webhook URL
- Get this from Discord server settings
- IMPORTANT: Keep this secure!

**[CONFIG] SLACK_BOT_TOKEN**
- Your Slack bot OAuth token (starts with `xoxb-`)
- Get this from Slack App settings
- IMPORTANT: Keep this secure!

**[CONFIG] SLACK_CHANNEL_ID**
- Your Slack channel ID (format: C############)
- Find in channel details
- Can be testing or production channel

**[CONFIG] CALENDAR_NAME**
- Name of your Google Calendar
- Default: 'HKN Events'
- Must match exactly (case-sensitive)

**[CONFIG] TIMEZONE**
- Timezone for event times
- Default: 'America/Phoenix' (Arizona/MST)
- Change only if your chapter moves locations

**[CONFIG] CHAPTER_NAME**
- Full chapter name for messages
- Default: 'IEEE-HKN Epsilon Beta Chapter'

**[CONFIG] BOT_NAME**
- Name that appears in Discord
- Default: 'Eve'
- Shows in footer of messages

### REMINDER SETTINGS:

You can customize when reminders are sent by editing the REMINDERS object:

```javascript
TWO_WEEKS: {
  target: 14,          // Days before event
  window: 2,           // Hour window for triggering (±2 hours)
  mention: '@everyone' // Who to ping in Discord
}

WEEK: {
  target: 7,
  window: 2,
  mention: '@everyone'
}

DAY: {
  target: 1,           // 1 day = 24 hours
  window: 1,
  mention: '@here'     // @here only pings online members
}
```

### ADVANCED SETTINGS:

**[SETTING] MAX_RETRIES: 3**
- How many times to retry if Discord webhook fails

**[SETTING] RETRY_DELAY: 2000**
- Milliseconds to wait between retries (2000 = 2 seconds)

**[SETTING] BATCH_SIZE: 10**
- Maximum events to process per 5-minute check
- Prevents timeout errors on large calendars

**[SETTING] CLEANUP_DAYS: 30**
- How long to keep reminder history (days)
- Older records are automatically deleted

### FEATURES (Feature Flags):

**[FLAG] ERROR_NOTIFICATIONS: true**
- Send error messages to Discord when problems occur

**[FLAG] DEBUG_MODE: false**
- Enable verbose logging (set to true for troubleshooting)

**[FLAG] DRY_RUN: false**
- Test mode: runs logic but doesn't send Discord messages

**[FLAG] RECURRING_EVENT_SUPPORT: true**
- Handle recurring calendar events

**[FLAG] FILTER_CANCELLED_EVENTS: true**
- Skip cancelled events automatically

**[FLAG] SLACK_ENABLED: true**
- Enable/disable Slack integration
- Set to false to send only to Discord
- Set to true to send to both Discord and Slack simultaneously

---

## 6. TESTING & VERIFICATION

### BUILT-IN TESTING FUNCTIONS:

The system includes several testing functions. To run them:

1. Open Apps Script editor
2. Select function from dropdown menu
3. Click Run button
4. Check Logs (View > Logs) and Discord for results

### AVAILABLE TEST FUNCTIONS:

**[TEST] runComprehensiveTest()**
- Tests all major components
- Verifies Discord connection
- Verifies Slack connection (if enabled)
- Checks calendar access
- Validates reminder logic
- RECOMMENDED: Run this after any configuration changes

**[TEST] testWithNextEvent()**
- Finds your next calendar event
- Shows what reminder would be sent
- Does NOT actually send (dry run)
- RECOMMENDED: Run before going live

**[TEST] forceImmediateReminder(eventTitle, reminderType)**
- Manually trigger a reminder for testing
- Example: forceImmediateReminder('General Meeting', 'WEEK')
- Actually sends to Discord and Slack (if enabled)
- USE CAREFULLY: Will ping your Discord and Slack channels

**[TEST] getSystemStatus()**
- Returns current system health metrics
- Shows last execution time, error count, trigger status
- Useful for quick health checks

**[TEST] testDiscordConnection()**
- Sends a simple test message to Discord
- Verifies webhook is working
- Quick connectivity test

**[TEST] testSlackIntegration()**
- Sends a formatted test message to Slack
- Verifies bot token and channel configuration
- Tests Slack API connectivity
- RECOMMENDED: Run after Slack setup or channel changes

### MANUAL TESTING PROCEDURE:

**Discord Testing:**
1. Create a test event in "HKN Events" calendar 2 weeks from now
2. Run: testWithNextEvent()
3. Check logs - should show your test event and calculated reminders
4. Run: forceImmediateReminder('Your Test Event', 'TWO_WEEKS')
5. Check Discord - should receive a formatted reminder message
6. Delete test event from calendar

**Slack Testing:**
1. Run: testSlackIntegration()
2. Check your Slack channel for test message
3. Verify message format and bot appearance

---

## 7. MAINTENANCE & MONITORING

### REGULAR MONITORING:

**[WEEKLY] WEEKLY CHECKS:**
- Verify reminders are being sent (check Discord and Slack history)
- Review any error messages in Discord
- Confirm calendar events are being picked up

**[MONTHLY] MONTHLY CHECKS:**
- Run getSystemStatus() to check system health
- Review Google Apps Script quota usage:
  - Go to: script.google.com > Project > Execution log
  - Ensure you're not hitting quota limits
- Verify triggers are still active:
  - Apps Script editor > Triggers (clock icon on left)
  - Should see one trigger running checkEventReminders every 5 minutes

### AUTOMATIC MAINTENANCE:

The system performs automatic maintenance:
- Cleans up old reminder records (>30 days)
- Self-recovers from temporary failures
- Handles cancelled events automatically
- Manages cache expiration

### MANUAL MAINTENANCE TASKS:

**[MAINT] cleanupOldReminders()**
- Manually trigger cleanup of old reminder history
- Normally runs automatically, but can force if needed

**[MAINT] enableMaintenanceMode() / disableMaintenanceMode()**
- Temporarily pause the reminder system
- Useful during calendar migrations or testing
- Doesn't delete triggers, just skips execution

**[MAINT] deleteAllTriggers()**
- Completely stops the automation
- CAUTION: Only use when decommissioning or troubleshooting
- Must run setupAutomaticReminders() again to restart

---

## 8. TROUBLESHOOTING

### [ISSUE] PROBLEM: No reminders being sent

**SOLUTIONS:**
1. Check if triggers are active:
   - Apps Script > Triggers (clock icon)
   - Should see checkEventReminders running every 5 minutes
2. Verify maintenance mode is off:
   - Run: disableMaintenanceMode()
3. Check calendar name matches:
   - Verify CONFIG.CALENDAR_NAME matches your actual calendar
4. Look for errors in execution log:
   - Apps Script > Executions
   - Look for red error indicators

### [ISSUE] PROBLEM: Discord messages not appearing

**SOLUTIONS:**
1. Test webhook connection:
   - Run: testDiscordConnection()
2. Verify webhook URL is correct:
   - Check line 7 in eventReminder.js
   - URL should start with https://discord.com/api/webhooks/
3. Check webhook hasn't been deleted in Discord:
   - Discord > Server Settings > Integrations > Webhooks
4. Verify webhook has permission to post:
   - Check channel permissions

### [ISSUE] PROBLEM: Slack messages not appearing

**SOLUTIONS:**
1. Test Slack connection:
   - Run: testSlackIntegration()
2. Verify SLACK_ENABLED is true:
   - Check CONFIG.FEATURES.SLACK_ENABLED (line 57)
3. Verify bot token is correct:
   - Check CONFIG.SLACK_BOT_TOKEN (line 10)
4. Verify channel ID is correct:
   - Check CONFIG.SLACK_CHANNEL_ID (line 11)
5. Ensure bot is added to the channel:
   - In Slack: `/invite @Event Reminder Bot`
   - Or: Channel settings > Integrations > Add apps
6. Check bot permissions:
   - Bot needs: chat:write, chat:write.public, channels:read, chat:write.customize
   - Verify at [api.slack.com/apps](https://api.slack.com/apps)

**Common Slack Errors:**
- **"not_in_channel"**: Bot hasn't been invited to the channel
- **"invalid_auth"**: Bot token is incorrect or expired
- **"channel_not_found"**: Channel ID is incorrect

### [ISSUE] PROBLEM: Duplicate reminders being sent

**SOLUTIONS:**
1. Check if multiple triggers exist:
   - Apps Script > Triggers
   - Delete duplicates if found
2. Verify reminder history is being stored:
   - Run: getSystemStatus()
   - Check "Properties Status" in logs
3. Clear reminder history if corrupted:
   - Run: PropertiesService.getScriptProperties().deleteAllProperties()
   - Then run: setupAutomaticReminders()

### [ISSUE] PROBLEM: Wrong calendar being monitored

**SOLUTIONS:**
1. Update CALENDAR_NAME in CONFIG (line 14)
2. Clear calendar cache:
   - Run: CacheService.getScriptCache().remove('calendarId')
3. Re-run setup:
   - Run: setupAutomaticReminders()

### [ISSUE] PROBLEM: Execution time limit exceeded

**SOLUTIONS:**
1. Reduce BATCH_SIZE in CONFIG (line 43)
2. Limit calendar to only upcoming events (archive old ones)
3. Run cleanupOldReminders() to free up processing time

### [ISSUE] PROBLEM: Meeting links not being extracted

**SOLUTIONS:**
1. Verify link is in event description, not just title
2. Check if link format is supported:
   - Zoom: zoom.us/j/ or asu.zoom.us
   - Google Meet: meet.google.com
   - Teams: teams.microsoft.com or teams.live.com
3. Links in private/confidential comments won't be extracted

### ERROR CODES IN DISCORD:

If you see error messages in Discord, they include codes:

**[ERROR] "CAL_ERROR" - Calendar access issue**
- Check calendar name and permissions

**[ERROR] "DISCORD_ERROR" - Discord webhook failed**
- Verify webhook URL and permissions

**[ERROR] "TRIGGER_ERROR" - Trigger setup failed**
- Check Apps Script quotas and permissions

**[ERROR] "PROCESS_ERROR" - General processing error**
- Check execution logs for details

---

## 9. MODIFYING THE SYSTEM

### COMMON MODIFICATIONS:

### [MODIFY] CHANGE REMINDER INTERVALS:

Edit the REMINDERS object in CONFIG (lines 15-30)

Example - Add a 3-day reminder:

```javascript
THREE_DAYS: {
  target: 3,
  window: 1,
  mention: '@here'
}
```

### [MODIFY] CHANGE MESSAGE FORMAT:

**Discord Messages:**
Edit createEnhancedDiscordMessage() function (line 422)

The message structure uses Discord embeds:
- title: Event name
- description: Event details and meeting link
- color: Message border color (currently blue: 0x3498DB)
- fields: Additional info sections
- footer: Bot signature

Example - Add location to message:
In the fields array, add:

```javascript
{
  name: '[LOCATION] Location',
  value: event.getLocation() || 'TBD',
  inline: true
}
```

**Slack Messages:**
Edit createSlackMessage() function (line 575)

The message structure uses Slack Block Kit:
- header: Event title
- section blocks: Event details with markdown
- context: Footer information

Both message formats mirror each other to maintain consistency across platforms.

### [MODIFY] CHANGE EXECUTION FREQUENCY:

Currently runs every 5 minutes. To change:

1. Delete existing trigger:
   - Apps Script > Triggers > Delete all

2. Edit setupAutomaticReminders() function (line 820)
   - Change: .everyMinutes(5)
   - Options: .everyMinutes(1-30), .everyHours(1-24)

3. Re-run: setupAutomaticReminders()

   **[WARNING]** More frequent = more quota usage

### [MODIFY] ADD NEW EVENT SOURCES:

To monitor multiple calendars, edit getIEEECalendar() (line 197)

Add additional calendar fetching:

```javascript
const primaryCal = CalendarApp.getCalendarById('calendar@group.calendar.google.com');
const secondaryCal = CalendarApp.getCalendarById('other@group.calendar.google.com');
```

Then merge events in checkEventReminders()

### [MODIFY] CUSTOMIZE ERROR NOTIFICATIONS:

Edit handleError() function (line 689)

You can change:
- Error message format
- Which errors get reported to Discord
- Add email notifications:
  ```javascript
  MailApp.sendEmail('webmaster@hkn.asu.edu', 'Error Alert', errorMsg);
  ```

### [MODIFY] ADD LOGGING TO GOOGLE SHEETS:

For persistent logging, add to processEventBatch():

```javascript
const sheet = SpreadsheetApp.openById('YOUR_SHEET_ID').getSheetByName('Logs');
sheet.appendRow([new Date(), event.getTitle(), reminderType, 'Sent']);
```

---

## 10. IMPORTANT NOTES & BEST PRACTICES

### SECURITY:

**[SECURITY] CRITICAL SECURITY PRACTICES:**

1. **PROTECT THE WEBHOOK URL AND TOKENS**
   - Never share publicly or commit to public repositories
   - If Discord webhook exposed, regenerate immediately in Discord
   - If Slack bot token exposed, regenerate in Slack App settings
   - Consider storing in Script Properties instead of code:
     ```javascript
     PropertiesService.getScriptProperties().setProperty('webhookUrl', 'URL');
     PropertiesService.getScriptProperties().setProperty('slackToken', 'TOKEN');
     ```

2. **LIMIT SCRIPT ACCESS**
   - Only share with current webmasters
   - Remove access when webmasters graduate/resign
   - Use Google Workspace admin controls

3. **AUDIT PERMISSIONS**
   - Review what permissions the script has
   - Only grant minimum necessary permissions
   - Verify Slack bot scopes are minimal (chat:write, chat:write.public, channels:read)
   - Regular audit of Apps Script authorization

### BEST PRACTICES:

**[PRACTICE] TEST BEFORE DEPLOYING**
- Always run runComprehensiveTest() after changes
- Use testWithNextEvent() to preview messages
- Test with dummy events first

**[PRACTICE] DOCUMENT YOUR CHANGES**
- Add comments explaining why you changed something
- Update version number at top of file
- Keep a changelog of modifications

**[PRACTICE] BACKUP BEFORE MAJOR CHANGES**
- File > Make a copy before editing
- Name it with date: "Event Reminder - Backup 2024-11-05"
- Keep at least 2 previous versions

**[PRACTICE] MONITOR REGULARLY**
- Check Discord and Slack weekly for any error messages
- Review execution logs monthly
- Verify reminders are being sent on time to both platforms

**[PRACTICE] HANDLE TRANSITIONS SMOOTHLY**
- Document any custom modifications for next webmaster
- Do a hands-on walkthrough with incoming webmaster
- Keep contact info for 1 semester after transition

### QUOTA LIMITS:

Google Apps Script has daily quotas. Current limits for consumer accounts:

- UrlFetch calls: 20,000/day
- Script runtime: 90 min/day
- Triggers: 20 time-based triggers

This system uses approximately:
- 288 executions/day (every 5 minutes)
- ~2-6 UrlFetch calls/day (when sending reminders to Discord and Slack)
- 1 time-based trigger

You're well within limits, but be aware if scaling up.

### CALENDAR BEST PRACTICES:

- **[CALENDAR]** Use clear event titles
- **[CALENDAR]** Always include meeting links in description
- **[CALENDAR]** Set proper event times (system uses start time)
- **[CALENDAR]** Mark cancelled events properly (system filters them)
- **[CALENDAR]** Use recurring events when appropriate
- **[CALENDAR]** Include event location when relevant

### DISCORD BEST PRACTICES:

- **[DISCORD]** Use @everyone sparingly (2 weeks, 1 week)
- **[DISCORD]** Use @here for urgent reminders (1 day)
- **[DISCORD]** Keep webhook in appropriate channel
- **[DISCORD]** Don't delete webhook without updating script
- **[DISCORD]** Create a dedicated bot/webhook channel for testing

### SLACK BEST PRACTICES:

- **[SLACK]** Use <!everyone> sparingly (2 weeks, 1 week)
- **[SLACK]** Use <!channel> for urgent reminders (1 day)
- **[SLACK]** Always add bot to channel before enabling
- **[SLACK]** Test with a dedicated testing channel first
- **[SLACK]** Keep bot token secure and regenerate if exposed
- **[SLACK]** Verify bot has minimum required permissions only

### TROUBLESHOOTING WORKFLOW:

When something goes wrong:

1. Check Discord and Slack for any error messages from the bot
2. Open Apps Script > Executions > Look for errors
3. Run getSystemStatus() to check overall health
4. Run testDiscordConnection() and testSlackIntegration() to verify connectivity
5. Check this documentation for specific error solutions
6. Review recent changes to calendar or script
7. If stuck, refer to version history to see what changed

---

## QUICK REFERENCE COMMANDS

### SETUP & ACTIVATION:
```
setupAutomaticReminders()      - Initial setup and trigger creation
```

### TESTING:
```
runComprehensiveTest()         - Full system test (Discord & Slack)
testWithNextEvent()            - Preview next reminder
testDiscordConnection()        - Test Discord webhook
testSlackIntegration()         - Test Slack bot and connectivity
getSystemStatus()              - Check system health
```

### MAINTENANCE:
```
enableMaintenanceMode()        - Pause system temporarily
disableMaintenanceMode()       - Resume system
cleanupOldReminders()          - Clear old reminder records
deleteAllTriggers()            - Stop all automation (CAUTION)
```

### MANUAL OPERATIONS:
```
forceImmediateReminder(title, type)  - Send reminder manually
Example: forceImmediateReminder('Meeting', 'WEEK')
```

---

## SUPPORT & RESOURCES

### INTERNAL RESOURCES:
- This documentation file
- eventReminder.js source code (heavily commented)
- Previous webmasters' documentation (if available)

### EXTERNAL RESOURCES:
- Google Apps Script Documentation: developers.google.com/apps-script
- Discord Webhook Guide: discord.com/developers/docs/resources/webhook
- Slack API Documentation: api.slack.com/docs
- Apps Script Calendar Service: developers.google.com/apps-script/reference/calendar

### GETTING HELP:

1. Review this documentation thoroughly
2. Check execution logs in Apps Script for specific errors
3. Search Google Apps Script documentation
4. Contact previous webmaster if available
5. IEEE-HKN national webmaster resources
6. Stack Overflow: [google-apps-script] tag

---

## VERSION HISTORY

**Version 2.2 (Current)**
- Dual platform support: Discord and Slack integration
- Comprehensive error handling
- Discord integration with rich embeds
- Slack integration with Block Kit messages
- Meeting link extraction (Zoom, Meet, Teams)
- Maintenance mode feature
- Extensive testing utilities
- Performance monitoring
- Auto-cleanup of old records

Previous versions: See code comments in eventReminder.js

---

## END OF DOCUMENTATION

**Last Updated:** November 2025
**Maintained by:** IEEE-HKN Epsilon Beta Chapter Webmasters
**Current Webmaster:** Vishesh Singh Rajput (2024-2025, 2025-2026)
**Contact:** webmaster@hkn.asu.edu

For questions, suggestions, or issues, please reach out to current chapter leadership or refer to IEEE-HKN national resources.

Good luck, and thank you for maintaining this system for the chapter!

---
