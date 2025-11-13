# **AI-Powered Expense Splitting Automation n8n**

An automated expense management system built with n8n that processes natural language emails, tracks shared expenses, and calculates balances using Google Gemini AI.

##  **Features**

* **Natural Language Processing**: Send emails like "Split 800 rs between me, User B, and User C for birthday party" \- no structured format required  
* **Automated Email Responses**: Receive instant confirmations when expenses are recorded  
* **Smart Balance Queries**: Ask "How much does User B owe me?" and get accurate calculations  
* **Date/Event Filtering**: Query specific expenses like "How much do I owe for farewell party?"  
* **Double-Entry Accounting**: Accurate net balance calculations accounting for who paid each expense  
* **Persistent Storage**: All expenses stored in Google Sheets for easy access and sharing

##  **Tech Stack**

* **n8n** \- Workflow automation platform  
* **Google Gemini AI** \- Natural language understanding  
* **Google Sheets API** \- Data persistence  
* **Gmail/IMAP** \- Email integration  
* **JavaScript** \- Custom business logic

##  **Prerequisites**

* n8n account (cloud or self-hosted)  
* Google account with API access  
* Gmail or IMAP email account  
* Google Gemini API key

##  **Quick Start**

### **1\. Clone the Repository**

git clone \[https://github.com/yourusername/expense-splitting-automation.git\](https://github.com/yourusername/expense-splitting-automation.git)  
cd expense-splitting-automation

### **2\. Set Up Google Services**

**Get Gemini API Key:**

1. Go to [Google AI Studio](https://aistudio.google.com/app/apikey)  
2. Create a new API key  
3. Save it securely (you'll add it to the n8n workflow)

**Create Google Sheet:**

1. Create a new Google Sheet  
2. Add headers: Date | Expense Item | Total Expense | User A | User B | User C | User D | Paid By  
3. Copy the Sheet ID from the URL (e.g., .../d/1QJbej14...RNuLk/edit...)

**Enable Gmail API:**

1. Go to [Google Cloud Console](https://console.cloud.google.com/)  
2. Enable Gmail API  
3. Create OAuth credentials

### **3\. Import Workflow to n8n**

1. Open n8n  
2. Click **Import from File**  
3. Select workflow/expense-splitting-workflow.json  
4. Configure credentials:  
   * Gmail OAuth  
   * Google Sheets OAuth  
   * Add Gemini API key (inside the "Build Gemini Request" node)

### **4\. Configure Email Trigger**

Update the IMAP Email Trigger node:

* **Host**: imap.gmail.com  
* **Port**: 993  
* **Email**: Your Gmail address  
* **Password**: App-specific password (not your regular password)

### **5\. Activate Workflow**

Click the toggle to activate. The workflow will now monitor your inbox\!

##  **Usage Examples**

(Assumes "me" is configured as "User A" in the workflow prompt)

### **Recording Expenses**

Send an email to your configured inbox:

Split 500 rs between me, User B & User C for lunch meeting.

Split 700 rs with User B & User D for birthday cake.  
But only add 30% to User D, keep the rest with me.

Split 1200 rs between me, User B, User C and User D  
for team dinner on Nov 5th 2025\. User C paid.

### **Querying Balances**

How much does User B owe me?

How much do I owe User D for birthday cake?

How much does User B owe me for farewell party on Oct 30th?

##  **Architecture**

### **Workflow Overview**

Email Received  
    ↓  
Debug & Extract Sender  
    ↓  
Build Gemini AI Request  
    ↓  
Parse AI Response  
    ↓  
Is it a Query? ─┬─ NO → Record Expense → Update Sheet → Send Confirmation  
                │  
                └─ YES → Calculate Balance → Send Query Response

### **Key Components**

1. **Email Trigger (IMAP)**: Monitors inbox for new emails  
2. **Debug Email**: Extracts sender address and email body  
3. **Build Gemini Request**: Constructs AI prompt with email content  
4. **Parse JSON**: Extracts structured data from AI response  
5. **Is Query?**: Routes to expense or query flow  
6. **Format for Sheet**: Normalizes data for Google Sheets  
7. **Calculate Balance**: Implements double-entry accounting logic  
8. **Email Responses**: Automated confirmations and query results

### **Data Flow**

**Expense Flow:**

Email → AI Parsing → Format Data → Google Sheets → Confirmation Email

**Query Flow:**

Email → AI Parsing → Read Sheet → Calculate Balance → Response Email

##  **Balance Calculation Logic**

The system uses double-entry accounting:

1. **Track who paid** for each expense (Paid By column)  
2. **Track each person's share** (individual columns)  
3. **Calculate net balance**:  
   * If A paid and B's share \= 200 → B owes A 200  
   * If B paid and A's share \= 50 → A owes B 50  
   * Net: B owes A 150 (200 \- 50\)

See docs/ARCHITECTURE.md for detailed logic.

##  **Troubleshooting**

### **Email not triggering workflow**

* Ensure workflow is activated (green toggle)  
* Check IMAP credentials are correct  
* Verify email format is set to "Resolved"

### **AI parsing errors**

* Check Gemini API key is valid (inside "Build Gemini Request" node)  
* Ensure API quota not exceeded  
* Try simpler email text

### **Balance calculations wrong**

* Verify "Paid By" column is filled correctly  
* Check person names match exactly (case-sensitive)  
* Review "Calculate Balance" node logs

See docs/TROUBLESHOOTING.md for more solutions.

##  **Example Data**

**Google Sheet Structure:**

| Date | Expense Item | Total | User A | User B | User C | User D | Paid By |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| 2025-10-29 | Lunch | 500 | 166.67 | 166.67 | 166.66 | 0 | User A |
| 2025-10-30 | Birthday | 800 | 200 | 200 | 200 | 200 | User B |

**Query Result:**

"How much does User B owe me?"  
→ "User A owes User B Rs 33.33"

Calculation:  
\- Row 1: User A paid, User B's share is 166.67 → User B owes User A 166.67  
\- Row 2: User B paid, User A's share is 200.00 → User A owes User B 200.00  
\- Net (from User A's perspective): \+166.67 (money in) \- 200.00 (money out) \= \-33.33  
\- Result: User A owes User B Rs 33.33

##  **Known Limitations**

* Currently supports 4 fixed people (User A, User B, User C, User D)  
* Email must be in plain text (HTML not fully supported)  
* Requires manual Google Sheet column setup  
* AI parsing accuracy depends on email clarity

##  **Future Enhancements**

* \[ \] Dynamic participant management (add/remove people)  
* \[ \] Multi-currency support  
* \[ \] Receipt image processing with OCR  
* \[ \] Settlement suggestions (optimal payment plan)  
* \[ \] Web dashboard for visualization  
* \[ \] Export reports (PDF, CSV)  
* \[ \] Recurring expense templates  
* \[ \] Split by percentage or custom amounts

##  **Contributing**

Contributions welcome\! Please:

1. Fork the repository  
2. Create a feature branch (git checkout \-b feature/amazing-feature)  
3. Commit changes (git commit \-m 'Add amazing feature')  
4. Push to branch (git push origin feature/amazing-feature)  
5. Open a Pull Request

##  **License**

This project is licensed under the MIT License \- see [LICENSE](https://github.com/mansimarisher/ai-expense-splitting-automation-n8n/blob/main/LICENSE) file.

##  **Author**

**Your Name**

* GitHub: [@mansimarisher](https://github.com/mansimarisher)  
* LinkedIn: [Your Profile](https://www.linkedin.com/in/mansimar-singh26/)  
* Email: mansimar31@gmail.com

##  **Acknowledgments**

* Assignment provided by \[Company Name\] (Optional)  
* n8n community for workflow automation resources  
* Google Gemini AI for natural language processing  
* Splitwise for inspiration

##  **Screenshots**

### **Workflow Canvas**

<img width="1742" height="764" alt="Screenshot 2025-11-14 005626" src="https://github.com/user-attachments/assets/718b80b1-c907-43f6-bcf4-632c72cfdc28" />


### **Sample Email Input**

<img width="531" height="353" alt="864c62c5-e293-45d5-8f96-5b58b6450722" src="https://github.com/user-attachments/assets/888213d2-953e-4a15-bda4-de672759c8b4" />



### **Google Sheet Output**

<img width="866" height="609" alt="Screenshot 2025-11-14 015431" src="https://github.com/user-attachments/assets/8558c6d7-59ae-49b7-be34-684a54a9a9a3" />





**⭐ If you found this project helpful, please star the repository\!**
