# processed_phishing_dataset_30000 — Column Descriptions

This file documents each column in `processed_phishing_dataset_30000.csv` for Power BI use.

| Column | Type | Example | Description / Notes |
|---|---:|---|---|
| Email_ID | Integer | 1 | Row identifier. Use as a primary key when needed. |
| Sender_Email | Text | user12345@gmail.com | Synthesized sender email. Contains PII-like values; consider anonymizing for public sharing. Can be split to get domain/local-part. |
| Sender_Domain | Text | gmail.com | Extracted domain from sender. Primary field for domain-risk analysis and grouping. |
| Subject | Text | "Your account needs verification" | Email subject (max 250 chars). Useful for keyword search, phrase extraction, and topic analysis. |
| Email_Type | Category | Phishing / Legitimate | Label for analysis and segmentation. Values: `Phishing`, `Legitimate`. Use for filtering and stacked visuals. |
| Attack_Type | Category | Credential Theft | Inferred attack category. Values: `Credential Theft`, `Malware`, `Scam`, `Fake Invoice`, `Unknown`, `None` (for legitimate). Use as a dimension in breakdowns. |
| Received_Date | Date (YYYY-MM-DD) | 2024-01-15 | Date the message was received. Format: `YYYY-MM-DD`. Create a Date dimension table in Power BI and mark as Date. Use for time-series visuals. |
| User_Targeted | Category | Employee / Customer | Indicates whether the email targeted an employee or customer. Useful for segmentation. |
| Country | Text | India | Inferred sender country (may be noisy). Use for map visuals and regional analysis. |
| Link_Count | Integer | 2 | Number of detected links. Numeric measure — useful for thresholds (e.g., suspicious if >=2). |
| Attachment | Category | Yes / No | Whether the message likely had an attachment. Useful for malware-phishing analysis. |
| Original_Sender | Text | "John Doe <john@company.com>" | Raw sender string from source CSV for traceability and further parsing. |
| Original_Receiver | Text | "support@company.com" | Raw receiver string from source CSV. Useful for recipient-role analysis. |
| Original_Label | Text | "phishing" | Original label or class from source dataset (may be noisy). Keep for auditing and alternate labeling strategies. |

**Power BI quick notes**
- Create a Date table and relate `Received_Date` to it for accurate time intelligence (month, quarter, YTD).
- Mark `Received_Date` as a Date data type; cast `Link_Count` to Whole Number; `Email_Type`, `Attack_Type`, `User_Targeted`, `Attachment`, `Sender_Domain`, `Country` as Text (or Category). 
- Recommended measures to add:
  - **Total Emails**: COUNTROWS(dataset)
  - **Phishing Count**: CALCULATE(COUNTROWS(dataset), dataset[Email_Type] = "Phishing")
  - **Phishing Rate**: DIVIDE([Phishing Count], [Total Emails])
  - **Avg Links per Email**: AVERAGE(dataset[Link_Count])
  - **% With Attachment**: DIVIDE(CALCULATE(COUNTROWS(dataset), dataset[Attachment]="Yes"), [Total Emails])
- Suggested visuals: time-series (Phishing Count by month), stacked bar (Attack_Type by Email_Type), map (Country by Phishing Count), table (Top Sender_Domain by Phishing Count).

**Data quality / improvement suggestions**
- Validate and normalize `Sender_Domain` (trim, lowercase, remove subdomains where appropriate).
- Enrich `Country` via WHOIS or IP/domain geolocation for higher accuracy.
- Add `Recipient_Role` (if derivable) to better distinguish `Employee` vs `Customer` targets.
- Preserve original message timestamps (if available) including time-of-day for finer trend analysis.
- Consider flagging synthetic PII (like `Sender_Email`) if the dataset will be shared externally.

File: processed_phishing_dataset_30000.csv — located in the same folder as this Markdown file.
