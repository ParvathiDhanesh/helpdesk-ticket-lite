# Helpdesk Ticket Lite

A small IT helpdesk system built on the Microsoft Power Platform (Dataverse, Power Apps, Power Automate, Power BI) using the free Power Apps Developer Plan.

Employees raise IT issues (laptop not booting, Wi-Fi down). Each ticket gets a deadline from its priority, is assigned to a technician, is resolved, and is closed with the user's feedback. A Power BI report shows ticket volume and resolution time.

## What it does

1. An employee creates a ticket from the app (Edit screen) or from the Help screen.
2. A flow sets the **Due Date** from the priority and adds a "Ticket received" comment.
3. **Critical** tickets also start an approval request for a manager.
4. A coordinator assigns a technician and moves the status: New, Assigned, In Progress.
5. When the status becomes **Resolved**, a flow stamps **Resolved On** and asks the user for feedback.
6. The user submits a 1 to 5 rating and a comment in the app, and a flow sets the status to **Closed**.
7. An hourly flow marks open tickets past their Due Date as **SLA Breached**.
8. Power BI reads the same tables and shows the totals.

## Contents

| File | What is inside |
|---|---|
| `HelpdeskLite` | The exported `HelpdeskLite` solution (tables, canvas app, flows) |
| `HelpdeskLite.pbix` | The Power BI report |
| `HelpdeskFAQ.txt` | The knowledge file for the Copilot Studio agent |

## Data model (Dataverse)

| Table | Purpose | Main columns |
|---|---|---|
| **Ticket** | One row per IT issue | Title, Description, Category, Priority, Ticket Status, Assigned To, Due Date, Resolved On, Resolution Hours, CSAT Rating, CSAT Feedback, SLA Breached, Requester Email |
| **Technician** | Support staff | Name, Email, Specialty, Is Available |
| **Comment** | Notes on a ticket | Comment Text, Ticket |
| **FAQ** | Answers for the Help screen | Keyword, Answer |

**Relationships (both many-to-one)**
- Ticket → Technician, through the **Assigned To** lookup
- Comment → Ticket, through the **Ticket** lookup

**Choice columns**
- Category: Hardware, Network, Software, Access, Other
- Priority: Low, Medium, High, Critical
- Ticket Status: New, Assigned, In Progress, Resolved, Closed

## Canvas app: Helpdesk Lite

| Screen | Purpose |
|---|---|
| **Browse** | Ticket list with title search, status filter and priority filter. Status text is colour-coded, and the due date turns red when an open ticket is overdue. |
| **Details** | All fields of one ticket, its comments, an Add comment box, and a feedback panel for resolved tickets |
| **Edit** | Create a new ticket or update an existing one |
| **Help** | Answers common questions by keyword from the FAQ table, and creates a ticket if there is no answer |

## Flows (Power Automate)

| Flow | Trigger | What it does |
|---|---|---|
| **HD - On ticket created** | A ticket is added | Sets the Due Date by priority and adds a comment |
| **HD - On ticket resolved** | Status changes to Resolved | Stamps Resolved On and adds a feedback request comment |
| **HD - Close ticket on feedback** | A rating is saved | Sets the status to Closed |
| **HD - Critical ticket approval** | A Critical ticket is added | Asks a manager to approve or reject, then comments or closes the ticket |
| **HD - SLA breach checker** | Every hour | Marks open, overdue tickets as SLA Breached |

**SLA targets (Due Date from priority)**

| Priority | Time allowed |
|---|---|
| Critical | 4 hours |
| High | 8 hours |
| Medium | 24 hours |
| Low | 72 hours (default) |

## Power BI report

- Tickets by category
- Tickets by priority
- Average resolution time (hours)
- Ticket status funnel

## How to import the solution

1. Open make.powerapps.com and choose a Dataverse environment (the free Developer Plan works).
2. Go to **Solutions → Import solution** and choose the zip file.
3. Sign in to the connections when asked (Dataverse, Approvals).
4. Open each flow and turn it on if it is off.
5. Open the app and check that the data sources load.

Choice values (for example 123000003 for Resolved) depend on the publisher's choice prefix. The flows use these numbers in their filters, so check them in the table's column settings after importing.

## Built with

Microsoft Dataverse, Power Apps (canvas), Power Automate, Power BI Desktop, and Copilot Studio (configured only).
