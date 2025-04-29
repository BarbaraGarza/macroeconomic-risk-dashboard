# DAX Measures – U.S. Macroeconomic Risk Tracker

This document contains custom DAX logic used to calculate dynamic scores, tooltips, risk contributions, and indicator rankings within the Power BI dashboard.

---

IndicatorGlossary = 
DATATABLE(
    "Indicator", STRING,
    "Definition", STRING,
    {
        {"CPI", "Measures inflation by tracking average price changes in consumer goods."},
        {"Unemployment", "Represents the percentage of people who are actively seeking jobs but unemployed."},
        {"GDP", "Reflects the overall economic output and growth or contraction of the economy."},
        {"Yield Curve", "A financial signal, when inverted, it often precedes a recession."},
        {"Confidence", "Measures how optimistic consumers feel about the economy."},
        {"Retail Sales", "Indicates consumer spending behavior, often tied to economic activity."}
    }
)
IndicatorLabels = 
DATATABLE(
    "Indicator", STRING,
    {
        {"CPI"},
        {"Unemployment"},
        {"GDP"},
        {"Yield Curve"},
        {"Confidence"},
        {"Retail"}
    }
)
Primary Risk Source Explanation = 
VAR TopIndicator =
    CALCULATE(
        MAX('IndicatorTriggerCounts'[Indicator]),
        TOPN(1, 'IndicatorTriggerCounts', 'IndicatorTriggerCounts'[TriggerCount], DESC)
    )
VAR TriggerNumber = 
    CALCULATE(
        MAX('IndicatorTriggerCounts'[TriggerCount]),
        FILTER('IndicatorTriggerCounts', 'IndicatorTriggerCounts'[Indicator] = TopIndicator)
    )
RETURN
    "The Primary Risk Source is " & TopIndicator & 
    ", which triggered " & TriggerNumber & " times from January 2020 to January 2025. " &
    "This indicates the most persistent source of macroeconomic risk during the analysis period."
Primary Risk Trigger Count = 
VAR TopIndicator =
    CALCULATE(
        MAX('IndicatorTriggerCounts'[Indicator]),
        TOPN(1, 'IndicatorTriggerCounts', 'IndicatorTriggerCounts'[TriggerCount], DESC)
    )
VAR TriggerNumber = 
    CALCULATE(
        MAX('IndicatorTriggerCounts'[TriggerCount]),
        FILTER('IndicatorTriggerCounts', 'IndicatorTriggerCounts'[Indicator] = TopIndicator)
    )
RETURN
    TriggerNumber
Risk Contribution % = 
SWITCH(
    SELECTEDVALUE(IndicatorLabels[Indicator]),
    "CPI", [CPI Contribution %],
    "Unemployment", [Unemployment Contribution %],
    "GDP", [GDP Contribution %],
    "Yield Curve", [Yield Curve Contribution %],
    "Confidence", [Confidence Contribution %],
    "Retail", [Retail Contribution %],
    BLANK()
)
Risk Level Explanation = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR Score = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    SWITCH(
        TRUE(),
        Score <= 2, "Low Risk ✅ — The economy is stable with minimal red flags.",
        Score <= 4, "Moderate Risk ⚠️ — Caution advised. Some indicators are signaling concern.",
        "High Risk 🚨 — Significant macroeconomic instability detected."
    )
Total Risk Score Explanation = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR Score = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    "The sum of all active risk signals this month. " &
    "Maximum score is 6 — one point per indicator. " &
    "This month, " & Score & " risk indicators are active."
IndicatorTriggerCounts = 
UNION(
    ROW("Indicator", "CPI", "TriggerCount", SUM('Sheet1'[CPI Risk])),
    ROW("Indicator", "Unemployment", "TriggerCount", SUM('Sheet1'[Unemployment Risk])),
    ROW("Indicator", "GDP", "TriggerCount", SUM('Sheet1'[GDP Risk])),
    ROW("Indicator", "Yield Curve", "TriggerCount", SUM('Sheet1'[Yield Curve Risk])),
    ROW("Indicator", "Confidence", "TriggerCount", SUM('Sheet1'[Consumer Confidence Risk])),
    ROW("Indicator", "Retail", "TriggerCount", SUM('Sheet1'[Retail Sales Risk]))
)
Confidence Contribution % = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR Confidence = CALCULATE(MAX('Sheet1'[Consumer Confidence Risk]), 'Sheet1'[Date] = LatestDate)
VAR Total = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    DIVIDE(Confidence, Total) * 100
CPI Contribution % = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR CPI = CALCULATE(MAX('Sheet1'[CPI Risk]), 'Sheet1'[Date] = LatestDate)
VAR Total = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    DIVIDE(CPI, Total) * 100
GDP Contribution % = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR GDP = CALCULATE(MAX('Sheet1'[GDP Risk]), 'Sheet1'[Date] = LatestDate)
VAR Total = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    DIVIDE(GDP, Total) * 100
Most Triggered Indicator = 
VAR TopIndicator =
    TOPN(
        1,
        IndicatorTriggerCounts,
        IndicatorTriggerCounts[TriggerCount],
        DESC
    )
RETURN
    MAXX(TopIndicator, IndicatorTriggerCounts[Indicator])
Retail Contribution % = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR Retail = CALCULATE(MAX('Sheet1'[Retail Sales Risk]), 'Sheet1'[Date] = LatestDate)
VAR Total = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    DIVIDE(Retail, Total) * 100
Unemployment Contribution % = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR Unemployment = CALCULATE(MAX('Sheet1'[Unemployment Risk]), 'Sheet1'[Date] = LatestDate)
VAR Total = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    DIVIDE(Unemployment, Total) * 100
Yield Curve Contribution % = 
VAR LatestDate = MAX('Sheet1'[Date])
VAR YieldCurve = CALCULATE(MAX('Sheet1'[Yield Curve Risk]), 'Sheet1'[Date] = LatestDate)
VAR Total = CALCULATE(MAX('Sheet1'[Total Risk Score]), 'Sheet1'[Date] = LatestDate)
RETURN
    DIVIDE(YieldCurve, Total) * 100
Latest Total Risk Score = 
VAR LatestDate = MAX('Sheet1'[Date])
RETURN
    CALCULATE(
        MAX('Sheet1'[Total Risk Score]),
        'Sheet1'[Date] = LatestDate
    )
