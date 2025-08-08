# WhatsApp-Driven Google Drive Assistant (n8n Workflow)

This repository contains an n8n workflow that creates a powerful WhatsApp-based assistant for managing Google Drive files. Users can send simple text commands via WhatsApp to list, delete, move, and get AI-powered summaries of their documents.

This project was created as a submission for an internship task, demonstrating complex workflow automation, API integration, and robust error handling within the n8n platform.

---

## 🎥 Demo Video

*<[-- PASTE YOUR 5-MINUTE DEMO VIDEO LINK HERE (YouTube or Loom) --]>*

---

## ✨ Features

* **File & Folder Listing (`LIST`):** Get a list of all files and folders within a specified Google Drive folder.
* **Secure File Deletion (`DELETE`):** Delete files by name with a mandatory `CONFIRM` keyword to prevent accidents.
* **File Organization (`MOVE`):** Move files from one location to another using a clear `>>` separator.
* **AI-Powered Summaries (`SUMMARY`):** Get concise, bullet-point summaries of `.txt`, `.pdf`, and `.docx` files, powered by the Google Gemini API.
* **Full Audit Trail:** Every command and its outcome is automatically logged in a Google Sheet for easy tracking.
* **Robust Error Handling:** The assistant provides user-friendly feedback for invalid commands, non-existent files, or unsupported file types.

---

## 🛠️ Tech Stack

* **Automation Platform:** n8n
* **Hosting:** Render
* **WhatsApp API:** Twilio Sandbox for WhatsApp
* **Cloud Storage:** Google Drive API
* **Logging:** Google Sheets API
* **AI Summarization:** Google Gemini API

---

## 🚀 Setup and Installation

Follow these steps to deploy and run the assistant.

### **Prerequisites**

* A [Render](https://render.com/) account (free tier is sufficient, but note its limitations).
* A [Twilio](https://www.twilio.com/) account.
* A [Google Cloud Platform](https://cloud.google.com/) account.

### **Step 1: Google Cloud Platform Setup**

1.  **Create a New Project:** Go to the [Google Cloud Console](https://console.cloud.google.com/) and create a new project.
2.  **Enable APIs:** In the navigation menu, go to `APIs & Services > Library`. Search for and **enable** the following three APIs:
    * `Google Drive API`
    * `Google Sheets API`
    * `Vertex AI API` (This is for Gemini)
3.  **Create OAuth 2.0 Credentials (for Drive & Sheets):**
    * Go to `APIs & Services > OAuth consent screen`.
    * Choose **External** and create a consent screen. Fill in the required details.
    * Add the scopes for Drive (`.../auth/drive`) and Sheets (`.../auth/spreadsheets`).
    * Add your email as a "Test user".
    * Go to `APIs & Services > Credentials`, click `+ Create Credentials`, and select `OAuth client ID`.
    * Choose **Web application**.
    * Under `Authorized redirect URIs`, you will later add the URL from your n8n instance.
    * Copy your **Client ID** and **Client Secret**.
4.  **Create Gemini API Key:**
    * Go to [Google AI Studio](https://aistudio.google.com/).
    * Click **"Get API key"** and create a new key.
    * Copy your **API Key**.
5.  **Create Logging Spreadsheet:**
    * Go to [sheets.google.com](https://sheets.google.com/) and create a new spreadsheet.
    * Set the headers in the first row: `Timestamp`, `Command`, `Parameters`, `Status`, `Details`.
    * Copy the **Spreadsheet ID** from the URL.

### **Step 2: Twilio Setup**

1.  Log in to your Twilio account.
2.  Navigate to **Messaging > Try it out > Send a WhatsApp message**.
3.  Follow the on-screen instructions to connect your phone number to the Twilio Sandbox by sending the provided join code to the Twilio number.
4.  Keep this page open; you will need your **Account SID** and **Auth Token** from the dashboard.

### **Step 3: Deploy n8n on Render**

1.  Click the button below to deploy n8n to Render.

    [![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/n8n-io/n8n-render)

2.  Follow the on-screen instructions. In the **"Environment"** section during setup, click **"+ Add Environment Variable"** and add the following:
    * **Key:** `WEBHOOK_URL`
    * **Value:** `https://your-app-name.onrender.com/` (Replace `your-app-name` with the service name you chose on Render).

    *Note on Data Persistence:* The free Render plan does not include a persistent disk, meaning your workflow and credentials will be lost if the service restarts. For a stable deployment, upgrading to a paid plan and adding a persistent disk is required.

### **Step 4: Configure n8n Credentials**

1.  Open your newly deployed n8n instance.
2.  Go to the **Credentials** section and add the following four credentials:
    * **Google Drive API (OAuth2):** Use the Client ID and Secret from Step 1.3. The Redirect URL will be shown here; copy it and add it to your Google Cloud OAuth Client ID settings.
    * **Google Sheets API (OAuth2):** Use the same Client ID and Secret as the Google Drive credential. Re-authenticate to grant Sheets permissions.
    * **Google Gemini API:** Use the API Key from Step 1.4.
    * **Twilio API:** Use your Account SID and Auth Token from your Twilio dashboard.

### **Step 5: Import and Activate Workflow**

1.  In your n8n canvas, go to **File > Import from File**.
2.  Select the `workflow.json` file from this repository.
3.  The workflow will load. You will need to manually reconnect each node to the credentials you just created.
4.  Click **`Save`**.
5.  Toggle the **`Active`** switch at the top right to ON.

### **Step 6: Connect Twilio to n8n**

1.  In the n8n workflow, click on the **Webhook** node (the first node).
2.  Copy the **Production URL**.
3.  Go back to your Twilio Sandbox settings page.
4.  Paste the n8n Production URL into the **"When a message comes in"** field and set the method to `HTTP POST`.
5.  Save the Twilio configuration. Your assistant is now live!

---

## 🤖 Command Syntax

Send the following commands to your Twilio number via WhatsApp.

### **LIST**
Lists all files and folders inside a specified top-level folder.
* **Format:** `LIST /<folder_name>`
* **Example:** `LIST /Meeting Agendas`

### **DELETE**
Deletes a file from anywhere in your Google Drive. Requires a confirmation keyword for safety.
* **Format:** `DELETE <file_name> CONFIRM`
* **Example:** `DELETE quarterly-report.pdf CONFIRM`

### **MOVE**
Moves a source file into a destination folder. You must use `>>` as a separator.
* **Format:** `MOVE /<source_file_with_path> >> /<destination_folder_with_path>`
* **Example:** `MOVE /Drafts/report v1.docx >> /Final Reports`

### **SUMMARY**
Generates an AI-powered summary of a specific file or all supported files within a folder.
* **Format (File):** `SUMMARY /<file_name_with_extension>`
* **Format (Folder):** `SUMMARY /<folder_name>`
* **Example (File):** `SUMMARY /research_paper.pdf`
* **Example (Folder):** `SUMMARY /Meeting Notes`

---

## 📄 License

This project is licensed under the MIT License.
