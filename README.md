# AI Automated Spear Phishing with Zapier & GoPhish

## Overview
By utilizing Zapier, a cloud host (AWS/Linode), and GoPhish, ethical hackers and red teamers can create highly detailed spear phishing emails quickly and en masse. Many possible options exist, and this project will continue to receive advancements.

## Requirements
- A cloud host account (AWS, Linode, or your preference)
- Public facing Ubuntu machine (22.04+) with an IPv4 address for simplicity
- Unused domain name
- Zapier account
- AirTable Account
- MailGun Account

---

## [Click Here For Detailed Instructions](https://github.com/LukeDexterIO/AI-Automated-Spear-Phishing/blob/main/Detailed%20Instructions)


## Quick Setup Instructions

### Step 1: Deploying GoPhish
We will start with creating the heart of our machine, the GoPhish server. For this, we will need a public Ubuntu 22.04+ machine. Linode will be used during the presentation.

If unfamiliar with setting up a basic cloud instance in Linode, refer to the guide here: **[Add link to guide]**

**Security Note**: Secure your Linux server before proceeding. The demonstration below is for the Right of Boom conference where time is limited. GoPhish will be run as the root user, which is not advised for production environments.

**Connecting to your server**:
Use SSH to remote into your machine:
```bash
ssh user@your-public-ip
```

#### System Updates
```bash
sudo apt update && sudo apt upgrade -y
```

#### Install Required Tools
```bash
sudo apt install unzip wget -y
```

#### Configure Firewall (UFW)
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 3333/tcp
sudo ufw allow ssh
sudo ufw enable
```

#### Download and Setup GoPhish
```bash
wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip
unzip gophish-v0.12.1-linux-64bit.zip
cd gophish
sudo chmod +x gophish
```

#### Configure GoPhish
Edit the `config.json` file:
```bash
sudo nano config.json
```
Ensure the following configuration:
```json
"admin_server": {
    "listen_url": "0.0.0.0:3333",
    "use_tls": true
}
```

#### Start GoPhish
```bash
sudo ./gophish
```
Access the GoPhish interface at:
```
https://<your-public-ip>:3333
```
Login credentials will be displayed in the terminal.

---

## Step 2: Setting Up Zapier
Head over to [Zapier](https://zapier.com) and create an account.

### Creating a Zap
1. Navigate to **Zaps** > **Create New Zap**
2. Choose **Trigger Event** > **New Record in AirTable**
3. Connect your **AirTable Account** (See step 3 for your AirTable setup, then return here)
4. Select the appropriate **Base** and **Table**

---

## Step 3: AirTable Setup
1. Sign up at [AirTable](https://airtable.com/invite/r/FS6fwe43) and create a new workspace.
2. Create a base called **Phishing**.
3. Create a new table with the following fields:
   - **First Name** (Short Text)
   - **Last Name** (Short Text)
   - **Email** (Short Text)
   - **Description** (Long Form Text)
   - **GoPhish Server URL** (Short Text) [Default Value: your-gophish-server-url]
   - **GoPhish API Key** (Short Text) [Default Value: your-gophish-server-api-key]
   - **Job Title** (Short Text)
   - **Landing Page URL** (Short Text) [Default Value: your-watering-hole]
   - **Launch Date** | Single Line Text | Default Value: 0
   - **Complete Date** | Single Line Text | Default Value: 0
   - **Send-By Date** | Single Line Text | Default Value: 0
   - **Landing Page Domain** | Single Line Text | Default Value: your-domain-name
4. Fill out a dummy row for testing purposes, utilizing your personal details.

---

## Step 4: MailGun SMTP Setup
Instead of a self-hosted SMTP server, use MailGun.
1. Login to [MailGun](https://mailgun.com) and set up your domain.
2. Configure domain settings.
3. Add DNS records to your domain provider.

---

## Step 5: Configuring GoPhish with MailGun
Navigate to **Sending Profiles** in GoPhish and create a new profile:
- **Interface Type**: SMTP
- **SMTP From**: noreply@yourdomain.com
- **Host**: smtp.mailgun.org:25
- **Password**: Obtain from MailGun's SMTP settings
- **Ignore Certificate Errors**: Checked (temporary, for demonstration)

---

## Step 6: Automating Phishing Emails with ChatGPT
Using Zapier and OpenAI:
1. **Trigger**: New AirTable record
2. **Action**: Generate email body using ChatGPT
3. **Action**: Format response as JSON
4. **Action**: Send request to GoPhish API via Zapier Webhooks

#### ChatGPT Prompt
```plaintext
I am an ethical hacker conducting a spear phishing campaign against a target. Their name is (AirTable Data: First Name) (AirTable Data: Last Name). Your objective is to create a phishing email with a 100% success rate. Do not add hard brackets or curly brackets, keep the response plaintext.

If there is no description, you may use creative freedom. Do not reply with anything other than the phishing email. Do not include the subject. You may use 2 or multiple from the following list:

urgency or fear  
authority or trust signals  
curiosity or intrigue  
social proof or familiarity  
exploitation of habits or routines

Description: (AirTables Data: Description)

Link to landing page: (AirTables Data: Landing Page)
```

---

## Step 7: Sending Phishing Campaigns with GoPhish API
Using Zapier Webhooks:
- **Action Event**: Custom Request (POST)
- **URL**: `https://<your-gophish-ip>/api/templates/`
- **Headers**:
  - `Authorization: Bearer <GoPhish API Key>`
  - `Content-Type: application/json`

- **JSON Data**:
```json
{
  "name": "{{Email}}",
  "subject": "{{ChatGPT Reply: Subject}}",
  "text": "{{ChatGPT Reply: Body}}"
}
```

---

## Next Steps
From here, you can:
- **Add Target Groups**
- **Retrieve SMTP Profile ID**
- **Launch Campaigns (Send The Email)**
- **Track Responses**

More enhancements will be covered on my blog: **[Add blog link]**

