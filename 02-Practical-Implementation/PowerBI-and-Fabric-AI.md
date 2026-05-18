---
title: Microsoft Power BI & Fabric AI Deep Dive
tags: 
  - chapter
  - PowerBI
  - fabric
  - data
  - copilot
  - intermediate
difficulty: intermediate
last_updated: 2026-04-25
time_to_read: 20 minutes
related:
  - "[[AI-for-Data-Analysts]]"
  - "[[Prompt-Engineering-Playbook]]"
  - "[[Security-and-Privacy]]"
---
# Microsoft Power BI & Fabric AI Deep Dive

> **TL;DR for the Busy IT Pro:**  
> Copilot in Power BI cannot magically fix bad data. It generates DAX and builds visuals, but it relies entirely on a perfectly structured Semantic Model. If your columns are named `cust_tx_dt_1`, the AI will fail. Clean your data, build a Star Schema, and write descriptions for every field.

---
## What You'll Learn

- [ ] Disambiguating the Microsoft AI Ecosystem (Fabric vs. Power BI vs. Azure OpenAI)
- [ ] How to prepare your Semantic Model so Copilot actually works
- [ ] Using Copilot for DAX generation and PySpark ETLs
- [ ] How to integrate custom Azure OpenAI calls directly into Power Query

---
## Why This Matters

Business stakeholders constantly ask: *"Can we just get a chat box where I can ask my dashboard questions?"* 

Microsoft has built exactly this with Copilot for Power BI and the Q&A visual. However, if your data analysts treat AI like a magic wand and point it at a flat, messy CSV, the AI will hallucinate revenue numbers. Understanding how the LLM interacts with the Microsoft tabular engine is the difference between a self-service analytics utopia and a compliance disaster.

**Real-world scenario:**  
> The VP of Sales asks Copilot: "What was our margin in Q3?" The AI looks at the database, sees a column named `Margin_Pct` and a column named `Gross_Margin_Amt`, gets confused, averages the two columns together, and outputs a completely fabricated number. 

---
## Core Concepts

### Concept 1: The Copilot Ecosystem Disambiguated
"Copilot" is a brand name, not a single product. In the data stack, there are three distinct tools:
1. **Copilot for Power BI:** Built into the report builder. It generates report pages, summarizes visuals (Smart Narrative), and writes DAX measures. *Requires Fabric F64 capacity or Premium P1.*
2. **Copilot for Fabric (Data Engineering):** Built into Fabric Notebooks and Dataflows. It helps data engineers write PySpark, SQL, and perform data cleansing/transformations during the ETL phase.
3. **Azure OpenAI (Custom Integration):** For when you need an LLM to do something Copilot can't (e.g., scoring customer sentiment on a million text reviews in your database). You call the API manually via Power Query or Databricks.

### Concept 2: LLMs Write Code, They Don't Read Rows
When you ask Copilot in Power BI a question, **the LLM does not read your millions of rows of data.** 
Instead, it reads your *Semantic Model Schema* (table names, column names, relationships). It then writes a DAX query, asks the Power BI engine to execute the DAX, and formats the result. If your schema is confusing, the DAX will be wrong.

### Concept 3: The Star Schema Requirement
AI models are trained heavily on standard database architectures. If you use a single, massive flat table with 100 columns, the AI will struggle. If you use a standard Star Schema (one central Fact table surrounded by Dimension tables), the AI will understand the relationships natively and generate highly accurate DAX.

---
## Hands-On Implementation

### Step 1: Prepping the Semantic Model for AI
To make Copilot and Q&A work reliably, you must do the "unsexy" metadata work.

1. **Explicit Naming Conventions:** Rename `CUST_ID` to `Customer ID`. Rename `AMT_USD_REV` to `Total Revenue USD`. 
2. **Synonyms:** In the Power BI Model View, open the properties pane and add synonyms. For `Total Revenue USD`, add synonyms: *Sales, Income, Topline, Dollars*.
3. **Column Descriptions:** This is the most critical step. In the field description, write a mini-prompt for the AI. 
   * *Example for a `Status` column:* "Indicates the order status. 1 = Pending, 2 = Shipped, 3 = Canceled. Use this when users ask about delivery states."
4. **Data Categories:** Explicitly tag geographical data as `City`, `State`, or `Country`, and URLs as `Web URL`.

### Step 2: Custom AI via Power Query (Azure OpenAI)
Sometimes you need AI to process row-level data (e.g., categorizing thousands of support tickets). You can invoke Azure OpenAI directly inside Power Query using a Custom Function.

```powerquery
// 1. Create a Blank Query in Power Query and name it "GetAISentiment"
// 2. Open Advanced Editor and paste this:

(textToScore as text) =>
let
    // Your secure Azure OpenAI endpoint and key
    Endpoint = "https://your-resource.openai.azure.com/openai/deployments/gpt-4o-mini/chat/completions?api-version=2024-02-15-preview",
    ApiKey = "YOUR_AZURE_API_KEY",
    
    // The JSON payload
    Body = Json.FromValue([
        messages = {[role = "system", content = "You are a sentiment analyzer. Reply with exactly one word: POSITIVE, NEGATIVE, or NEUTRAL."],
            [role = "user", content = textToScore]
        },
        temperature = 0,
        max_tokens = 10
    ]),
    
    // The API Call
    Response = Web.Contents(Endpoint, [
        Headers =[
            #"Content-Type" = "application/json",
            #"api-key" = ApiKey
        ],
        Content = Body
    ]),
    
    // Parse the JSON response
    ParsedJSON = Json.Document(Response),
    Result = ParsedJSON[choices]{0}[message][content]
in
    Result
```
*Usage:* You can now add a Custom Column to your table and invoke `= GetAISentiment([CustomerFeedback])`. 
*(Note: Be mindful of API limits and costs! Do not run this on 50 million rows on every refresh. Use it during the ETL pipeline incrementally).*

---
## Tips & Tricks

> [!tip] Quick Win - DAX Generation
> Don't use Copilot to write DAX blindly. Provide the exact table context in your prompt. 
> *Good Prompt:* "Write a DAX measure for Year-over-Year Revenue Growth. Use the 'Sales' table [Total Amount] column and the 'Calendar' table [Date] column. Handle divide-by-zero errors."

> [!tip] Pro Tip - Smart Narratives
> Use the "Smart Narrative" visual and feed it custom instructions. Instead of letting it guess what to summarize, tell it: "Summarize the top 3 underperforming regions by profit margin, and list them as bullet points." This dynamically updates as users filter the dashboard.

> [!warning] Watch Out - Row-Level Security (RLS)
> Copilot respects Power BI Row-Level Security. However, if a user asks a question about data they don't have access to, Copilot might give a confusing "I cannot answer that" response rather than explaining it's a permissions issue. Document this for your end-users.

---
## Lessons Learned

> [!example] War Story: The "Auto-Summarize" Disaster
> **What happened:** A developer left the default "Summarize" behavior on a numeric column representing `Year` (e.g., 2024, 2025). A user asked Copilot, "What is our data showing for the years?" Copilot summed the years together and confidently replied: "The total is 4,049."
> **What we learned:** AI relies entirely on Power BI's underlying data typing.
> **What to do instead:** We strictly set all ID columns and Year columns to "Don't Summarize" in the model. Copilot immediately stopped trying to do math on dates and ID numbers.

---
## Best Practices Checklist

- [ ] Practice 1: **Hide unnecessary columns.** If a column is just an ETL artifact (like `Load_Date_Timestamp_UTC`), hide it from the Report View. If the AI can't see it, it can't get confused by it.
- [ ] Practice 2: **Create explicit measures.** Don't rely on implicit measures (dragging a column to a visual to SUM it). Write an explicit DAX measure `Total Sales = SUM(Sales[Amount])`. Copilot prefers explicit measures.
- [ ] Practice 3: **Use Fabric Notebooks for heavy lifting.** If you need AI to categorize 10 million rows, do not use Power Query. Use Copilot in a Fabric PySpark Notebook to batch process the data efficiently before it hits the semantic model.

---
## Anti-Patterns (Don't Do This)

| ❌ Don't | ✅ Do Instead | Why |
|---------|--------------|-----|
| Ask Copilot to fix bad data | Fix data in Power Query/Fabric | Copilot builds visuals and DAX; it cannot alter the underlying dataset to fix typos or bad joins. |
| Use Copilot on massive flat tables | Build a Star Schema | AI models understand standard database normalizations best. Flat tables lead to hallucinated cross-filtering. |
| Hardcode API keys in Power Query | Use Azure Key Vault or Service Principals | Leaving an OpenAI API key in plain text in a PBIX file is a massive security risk. |

---
## Related Topics

- [[AI-for-Data-Analysts]] - Core concepts of Text-to-SQL.
- [[Evaluation-and-Testing]] - How to verify that the DAX generated by AI is actually correct.
- [[API-Integration-Guide]] - Handling the rate limits if you use the Power Query custom function above.

---
## Further Reading

- [Microsoft Docs: Copilot in Power BI](https://learn.microsoft.com/en-us/power-bi/create-reports/copilot-introduction) - Official capabilities and capacity requirements.
- [SQLBI: Naming Conventions](https://www.sqlbi.com/articles/naming-conventions-in-power-bi/) - The definitive guide to naming columns/measures (which makes Copilot 10x smarter).

---
## Changelog

- **2026-04-25**: Created deep dive for Power BI / Fabric ecosystem.

---
## Questions or Feedback?

If Copilot is generating bad DAX for your specific model, post a screenshot of your Model View (Relationships) in the `#powerbi-ai` channel so we can help you optimize the schema!