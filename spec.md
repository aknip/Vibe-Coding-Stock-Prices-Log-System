---
# **📘 Stock Performance Monitoring Web Application — Specification**

---
## **🧭 Project Overview**
This web application allows a user to monitor and log the performance of various stocks manually. Prices are scraped from the website "https://www.tagesschau.de/wirtschaft/boersenkurse/" and stored in a local SQLite database. The application provides a responsive, clean user interface for viewing, managing, and evaluating stocks and their price history.

---
## **⚙️ Tech Stack**
| **Layer**  | **Technology**                         |
| ---------- | -------------------------------------- |
| Frontend   | React (with Tailwind CSS)              |
| Backend    | Node.js (Express)                      |
| Scraping   | Puppeteer (headless Chrome)            |
| Database   | SQLite                                 |
| State Mgmt | Vanilla JavaScript (React state/hooks) |
| Styling    | Tailwind UI                            |
|Deployment|Local only|

---

## **🖥️ UI Layout and Navigation**
### **Top-Level Navigation Bar**
- **Home**
- **Stocks**
- **Evaluation**
- **Help** (with submenu)

---
## **1. 🏠 Home Page**
- **Two side-by-side charts**:
    - 📈 **Line chart**: Performance of the full portfolio (last 30 days)
    - 🥧 **Pie chart**: Portfolio distribution by stock
- Fully responsive for desktop/tablet/mobile

---

## **2. 📈 Stocks Page**
### **Features:**
- **Hierarchical list**:
    - Stock header with columns: Name, Ticker Symbol, Country, URL
    - Under each stock: log entries with Date, Time, Price (ask), Currency
- **Buttons**:
    - Stock level: Edit, Delete, Get price
    - Log level: Edit, Delete
- **Search bar** (full-text across all fields)
- **Create new stock** (button at bottom)
### **Create/Edit Stock Form (Popup)**
- Fields: Name, Ticker Symbol, Country, URL
- All fields are **required**
- URL must return 200 OK before saving
### **Logging Stock Price**
- Button click triggers scraping via Puppeteer
- Scraper retries **up to 2 times** on failure
- On success:
    - Log entry saved with Date, Time, Price (ask), Currency
    - Toast message (top-right):
        - Success: "Current price: 123,00 EUR"
        - Error: "Error while retrieving data: (Error details...)"
    - Action is logged (see “User Actions”)

---

## **3. 📊 Evaluation Page**
### **Subnavigation:**
- Only one item for now: **Raw Data** (more to be added later)
### **Raw Data Table:**
- Flat table of all stock price logs
- Columns: Name, Ticker Symbol, Country, Date, Time, Price, Currency
- Features:
    - 100 entries per page
    - Pagination: Page 1, 2, 3...
    - Sortable columns
    - Full-text search
    - **Download (XLSX)** button below table
---
## **4. ❓ Help Page**
### **Submenu:**
- **FAQ**
    - List of common questions and answers (e.g., how to create a stock)
- **User Actions**
    - Table with all logged user actions
### **User Actions Table:**
- Columns: Date, Time, User Action, Details, Result
- Features:
    - 100 entries per page
    - Pagination
    - Sortable columns
    - Full-text search
    - **Download (XLSX)** button

---

## **📦 Data Models**
### **1.** 
### **Stock**
```
{
  id: string;
  name: string;
  ticker: string;
  country: string;
  url: string;
}
```
### **2.** 
### **PriceLog**
```
{
  id: string;
  stockId: string;
  date: string;
  time: string;
  price: number;
  currency: string;
}
```
### **3.** 
### **UserActionLog**
```
{
  id: string;
  date: string;
  time: string;
  action: string;
  details: string;
  result: string; // e.g., "Success", "Error: <message>"
}
```

---

## **🛠️ Functional Requirements**
- Create, edit, delete stock entries
- Scrape prices using Puppeteer
- Retry scraping twice before reporting error
- Store each price in a separate PriceLog entry
- Log every user interaction in UserActionLog
- Full-text search on Stocks, Logs, Evaluations, and Logs
- All tables are paginated and sortable
- XLSX download for:
    - Stock Logs
    - Raw Evaluation Data
    - User Actions
- Toast notifications for all actions
- Dark/Light mode toggle with cookie-based preference memory

---

## **🔐 Non-Functional Requirements**
- No login/authentication required
- SQLite as persistent storage
- Responsive design (mobile, tablet, desktop)
- Browser support: all modern browsers (Chrome, Firefox, Safari, Edge)
- Only English language, consistent date/time formats

---

## **🧪 Testing Plan**
### **Unit Testing**
- Component rendering (e.g., stock table, forms)
- Utility functions (sorting, data parsing)
### **Integration Testing**
- Add/Edit/Delete stock
- Scrape and log price (mock Puppeteer)
- Logging system
### **End-to-End (E2E) Testing**
- Simulate real user flows:
    - Add stock → Fetch price → View in evaluation → Download
    - Switch themes → Reload → Check theme persisted via cookie
### **Error Testing**
- Invalid URL response
- Scraping failure after retries
- Missing form fields

---

## **🧹 Manual Maintenance**
- Database backups: manual
- Deployment: runs locally, no CI/CD required

---

## **✅ Summary**
This specification defines a complete, self-contained stock monitoring app that uses manual user interactions to fetch and track stock prices, with clean UX, durable backend, detailed logging, and extensible evaluation tools.

---
