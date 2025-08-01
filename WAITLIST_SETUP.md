# DreamGarden Waitlist Setup Guide

## Option 1: Google Sheets Integration (Recommended)

This is the easiest way to collect waitlist submissions and view them in a spreadsheet.

### Step 1: Create Google Sheet
1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet called "DreamGarden Waitlist"
3. Add these headers in row 1:
   - A1: `Timestamp`
   - B1: `Name`
   - C1: `Email`
   - D1: `Location`

### Step 2: Create Google Apps Script
1. In your Google Sheet, go to **Extensions > Apps Script**
2. Replace the default code with this:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const timestamp = new Date();
    
    // Get form data safely
    let name = '';
    let email = '';
    let location = '';
    
    if (e && e.parameter) {
      name = e.parameter.name || '';
      email = e.parameter.email || '';
      location = e.parameter.location || '';
    }
    
    // Save to spreadsheet
    sheet.appendRow([timestamp, name, email, location]);
    
    // Send confirmation email to user
    const subject = 'Welcome to DreamGarden Waitlist! 🌱';
    const body = `
Hi ${name},

Thanks for joining the DreamGarden waitlist! You're now on the list to be among the first to experience Australia's AI-powered gardening companion.

What happens next:
• We'll keep you updated on development progress
• You'll get early access when we launch in September 2025
• You'll receive exclusive gardening tips while you wait

In the meantime, check out our features: https://dreamgarden.app/features

Happy gardening!
The DreamGarden Team

---
You can unsubscribe anytime by replying to this email.
  `;
    
    // Send email from your custom domain
    MailApp.sendEmail({
      to: email,
      from: "hello@dreamgardenapp.com",
      subject: subject,
      body: body
    });
    
    // Send notification to you (optional)
    const notificationSubject = 'New DreamGarden Waitlist Signup';
    const notificationBody = `
New waitlist signup:

Name: ${name}
Email: ${email}
Location: ${location}
Date: ${timestamp.toLocaleString()}

Total waitlist size: ${sheet.getLastRow() - 1}
  `;
    
    MailApp.sendEmail({
      to: "hello@dreamgardenapp.com", // or your personal email
      subject: notificationSubject,
      body: notificationBody
    });
    
    return ContentService.createTextOutput('Success');
    
  } catch (error) {
    return ContentService.createTextOutput('Error: ' + error.message);
  }
}
```

3. Click **Deploy > New deployment**
4. Choose **Web app** as the type
5. Set **Execute as**: "Me"
6. Set **Who has access**: "Anyone"
7. Click **Deploy**
8. Copy the **Web app URL** (it will look like: `https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec`)

### Step 3: Update Your Website
1. Open `index.html`
2. Find line with `YOUR_SCRIPT_ID` in the form action
3. Replace `YOUR_SCRIPT_ID` with your actual script ID from the URL

### Step 4: Test
1. Submit the form on your website
2. Check your Google Sheet - you should see the submission appear

---

## Option 2: Email Notifications

If you prefer email notifications instead of a spreadsheet:

### Step 1: Create Google Apps Script
Use this code instead:

```javascript
function doPost(e) {
  const data = e.parameter;
  
  const name = data.name || '';
  const email = data.email || '';
  const location = data.location || '';
  
  const subject = 'New DreamGarden Waitlist Signup';
  const body = `
    New waitlist signup:
    
    Name: ${name}
    Email: ${email}
    Location: ${location}
    Date: ${new Date().toLocaleString()}
  `;
  
  MailApp.sendEmail('your-email@example.com', subject, body);
  
  return ContentService.createTextOutput('Success');
}
```

---

## Option 3: Formspree (Alternative)

If you prefer a third-party service:

1. Go to [Formspree](https://formspree.io)
2. Create a free account
3. Create a new form
4. Replace the form action in `index.html` with your Formspree endpoint
5. Submissions will be sent to your email

---

## Option 4: Netlify Forms (If hosted on Netlify)

If you deploy to Netlify, you can use their built-in form handling:

1. Change the form action to: `action="/.netlify/functions/submit-waitlist"`
2. Create a `netlify.toml` file in your project root
3. Add form handling configuration

---

## Viewing Your Waitlist

### Google Sheets Method:
- Open your Google Sheet anytime to see all submissions
- Sort by timestamp to see newest first
- Export to CSV if needed
- Share with team members

### Email Method:
- Check your email for notifications
- Set up email filters to organize submissions

---

## Security Notes:
- The current setup is basic but functional
- For production, consider adding CAPTCHA
- Consider rate limiting for spam protection
- Add email validation on the server side

---

## Next Steps:
1. Choose your preferred method
2. Follow the setup instructions
3. Test the form submission
4. Monitor your waitlist growth!

Need help? Contact: hello@dreamgardenapp.com 