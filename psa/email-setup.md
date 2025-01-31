---
title: "Inbound & Outbound Email Configuration"
layout: default
---

# Inbound & Outbound Email Configuration

Learn how to configure and manage email settings in our PSA system for streamlined communication with clients. Proper email configuration ensures tickets, tasks, and notifications are automatically routed and tracked.

## Table of Contents
1. [Overview](#overview)
2. [Outbound Email Setup](#outbound-email-setup)
3. [Inbound Email Setup](#inbound-email-setup)
4. [Testing & Validation](#testing--validation)
5. [Common Issues & FAQs](#common-issues--faqs)

---

## Overview

Our PSA platform integrates with external email systems to automate:
- **Outbound**: Notifications to clients about newly opened tickets, project updates, or invoices.
- **Inbound**: Emails from clients auto-converted into tickets, assigned to the proper queue or project.

## Outbound Email Setup

1. **SMTP Settings**: Go to **Admin** > **Email Settings** > **Outbound**.  
2. Enter your **SMTP server** hostname (e.g., `smtp.example.com`), port (e.g., `587`), and **credentials** (username and password).  
3. **Secure Connection**: Choose **TLS** or **SSL** if required by your mail server.  
4. **Test Connection**: Click **Send Test Email** to confirm everything is working.  
5. (Optional) **Default Templates**: Adjust the email templates the PSA uses for welcome messages, invoice reminders, or project updates.

## Inbound Email Setup

1. **Email Parser**: In **Admin** > **Email Settings** > **Inbound**, enable the email parsing feature.  
2. **Mailbox Address**: Provide the mailbox the PSA should poll (e.g., `support@yourcompany.com`).  
3. **Credentials**: Enter the IMAP/POP3 details, including server address (e.g., `imap.example.com`), port, username, and password.  
4. **Automatic Ticket Creation**: Turn on the toggle to create tickets from inbound messages.  
5. **Auto-Assignment Rules**:
   - **Sender Domain**: Emails from a recognized client domain might route to the client’s existing project.  
   - **Subject Tags**: If the subject contains `[Billing]`, route to the Billing queue.

## Testing & Validation

1. **Send a Test Email**: From a client email address, send a message to the configured mailbox (e.g., `support@yourcompany.com`).  
2. **Check PSA**: Confirm a new ticket or message was created under the correct client/project.  
3. **Outbound Reply**: Respond within the PSA and verify the client receives the email.

## Common Issues & FAQs

- **Authentication Errors**: Double-check credentials and ports. Some mail servers require an App Password or 2FA.  
- **Firewall Blockages**: Ensure your PSA’s IP can communicate with the mail server.  
- **Emails Not Converting to Tickets**: Confirm the email parsing rules (domain, subject tags, etc.) are correct.  
- **Spam Filters**: Outbound emails might get flagged if DNS entries (SPF, DKIM) aren’t set up for your domain.

---

### Need More Help?

- Consult your **mail server admin** for port or security protocol questions.  
- Check our [New Client Setup](./new-client-setup.md) guide for end-to-end onboarding steps.  
- For advanced email template customization, reach out to your PSA administrator or see the official documentation.
