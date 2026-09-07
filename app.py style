"""
Style for Marble and Granite — Export Intelligence Dashboard
Run with: streamlit run app.py
"""
import pandas as pd
import plotly.express as px
import plotly.graph_objects as go
import streamlit as st

st.set_page_config(page_title="Style Export Intelligence Dashboard", layout="wide", page_icon="🪨")

# ---------- Data loading ----------
@st.cache_data
def load_data():
    countries = pd.read_csv("data/countries.csv")
    stones = pd.read_csv("data/stones.csv")
    segments = pd.read_csv("data/segments.csv")
    scores = pd.read_csv("data/stone_country_scores.csv")
    swot = pd.read_csv("data/swot.csv")
    action_plan = pd.read_csv("data/action_plan.csv")
    return countries, stones, segments, scores, swot, action_plan

countries, stones, segments, scores, swot, action_plan = load_data()

DATA_DISCLAIMER = (
    "Country and stone×country scores marked **ESTIMATE** are analyst judgment calibrated against the "
    "cited external trade sources (see report), not raw UN Comtrade / ITC Trade Map pulls. Rows marked "
    "**VERIFIED** are grounded directly in a cited source. Treat ESTIMATE scores as a prioritization "
    "starting point, not final commercial fact — validate with real quotes/orders before pricing decisions."
)

# ---------- Sidebar: global filters ----------
st.sidebar.title("STYLE 🪨")
st.sidebar.caption("For Marble and Granite — Export Intelligence")
st.sidebar.info(DATA_DISCLAIMER)

country_filter = st.sidebar.multiselect("Country", sorted(countries["country"].unique()))
stone_filter = st.sidebar.multiselect("Stone", sorted(stones["stone"].unique()))
segment_filter = st.sidebar.multiselect("Buyer Segment", sorted(segments["segment"].unique()))
priority_filter = st.sidebar.multiselect("Country Priority", sorted(countries["priority"].unique()))

def apply_country_filters(df):
    out = df.copy()
    if country_filter:
        out = out[out["country"].isin(country_filter)]
    if priority_filter:
        out = out[out["priority"].isin(priority_filter)]
    return out

filtered_countries = apply_country_filters(countries)

# ---------- Tabs (dashboard "pages") ----------
tab_overview, tab_country, tab_stone, tab_heatmap, tab_segment, tab_swot, tab_action = st.tabs(
    ["📊 Executive Overview", "🌍 Country Intelligence", "🪨 Stone Intelligence",
     "🔥 Stone × Country Heatmap", "🎯 Target Segments", "⚖️ SWOT", "🗓️ Action Plan"]
)

# ================= PAGE 1: EXECUTIVE OVERVIEW =================
with tab_overview:
    st.header("Executive Overview")
    c1, c2, c3, c4 = st.columns(4)
    c1.metric("Countries Analyzed", len(countries))
    c2.metric("Top Priority Countries", (countries["priority"] == "Critical").sum())
    c3.metric("Priority Stones", len(stones))
    c4.metric("Buyer Segments Mapped", len(segments))

    c5, c6 = st.columns(2)
    top_country = countries.sort_values("overall_score", ascending=False).iloc[0]
    top_stone_country = scores.sort_values("fit_score", ascending=False).iloc[0]
    c5.metric("Highest Opportunity Market", top_country["country"], f"Score {top_country['overall_score']}")
    c6.metric("Best Single Stone×Country Fit",
              f"{top_stone_country['stone']} → {top_stone_country['country']}",
              f"Score {top_stone_country['fit_score']}")

    st.subheader("Top 10 Countries — Overall Export Opportunity Score")
    top10 = countries.sort_values("overall_score", ascending=False).head(10)
    fig = px.bar(top10, x="overall_score", y="country", orientation="h", color="priority",
                 labels={"overall_score": "Opportunity Score (0-100)", "country": ""})
    fig.update_layout(yaxis={"categoryorder": "total ascending"})
    st.plotly_chart(fig, use_container_width=True)

    st.subheader("Management Recommendation")
    st.success(
        "Focus the first 12 months on **Saudi Arabia, UAE, and Libya** — the markets where Egyptian stone "
        "already has either an established trade corridor or a large, accessible construction/hospitality "
        "pipeline. Lead with **Sunny Minya** and **Galala Light** for premium/hospitality buyers and "
        "**Ramad El Sherka** for volume/durability buyers. Treat the US and Western Europe as "
        "specification-led relationship-building, not a volume play, until stone-specific lab data is in hand."
    )

# ================= PAGE 2: COUNTRY INTELLIGENCE =================
with tab_country:
    st.header("Country Market Intelligence")
    st.caption(DATA_DISCLAIMER)

    display_cols = ["country", "region", "priority", "overall_score", "market_demand", "product_fit",
                     "logistics", "buyer_accessibility", "profit_potential", "risk_score", "main_risk", "data_status"]
    st.dataframe(
        filtered_countries[display_cols].sort_values("overall_score", ascending=False),
        use_container_width=True, hide_index=True
    )

    st.subheader("World Map — Opportunity Score")
    fig_map = px.scatter_geo(
        filtered_countries, lat="lat", lon="lon", size="overall_score", color="priority",
        hover_name="country", hover_data={"overall_score": True, "main_risk": True, "lat": False, "lon": False},
        projection="natural earth"
    )
    st.plotly_chart(fig_map, use_container_width=True)

    st.subheader("Compare Countries")
    compare_pick = st.multiselect("Pick up to 4 countries to compare", sorted(countries["country"].unique()),
                                   default=list(countries.sort_values("overall_score", ascending=False)["country"].head(3)))
    if compare_pick:
        radar_dims = ["market_demand", "product_fit", "logistics", "buyer_accessibility", "profit_potential", "risk_score"]
        fig_radar = go.Figure()
        for c in compare_pick[:4]:
            row = countries[countries["country"] == c].iloc[0]
            fig_radar.add_trace(go.Scatterpolar(r=[row[d] for d in radar_dims], theta=radar_dims,
                                                  fill="toself", name=c))
        fig_radar.update_layout(polar={"radialaxis": {"visible": True, "range": [0, 100]}}, showlegend=True)
        st.plotly_chart(fig_radar, use_container_width=True)

    st.subheader("Drill-down: select a country")
    pick = st.selectbox("Country", sorted(countries["country"].unique()))
    row = countries[countries["country"] == pick].iloc[0]
    colA, colB = st.columns(2)
    with colA:
        st.write(f"**Priority:** {row['priority']}   |   **Score:** {row['overall_score']}")
        st.write(f"**Best stones:** {row['best_stones']}")
        st.write(f"**Best buyer segments:** {row['best_segments']}")
    with colB:
        st.write(f"**Entry strategy:** {row['entry_strategy']}")
        st.write(f"**Main risk:** {row['main_risk']}")
        st.write(f"**Data status:** {row['data_status']}")

# ================= PAGE 3: STONE INTELLIGENCE =================
with tab_stone:
    st.header("Stone Intelligence")
    pick_stone = st.selectbox("Select a stone", stones["stone"].unique())
    srow = stones[stones["stone"] == pick_stone].iloc[0]

    st.subheader(f"{srow['stone']} ({srow['material']}, {srow['color_family']})")
    st.write(srow["description"])
    c1, c2 = st.columns(2)
    c1.write(f"**Best applications:** {srow['best_applications']}")
    c1.write(f"**Best buyer segments:** {srow['best_segments']}")
    c2.write(f"**Positioning:** {srow['positioning']}")
    c2.write(f"**Marketing angle:** {srow['marketing_angle']}")
    st.caption(f"Data status: {srow['data_status']}")

    st.subheader(f"Top countries for {pick_stone}")
    s_scores = scores[scores["stone"] == pick_stone].sort_values("fit_score", ascending=False)
    fig = px.bar(s_scores, x="fit_score", y="country", orientation="h", color="fit_score",
                 color_continuous_scale="Oranges", labels={"fit_score": "Product-Market Fit Score", "country": ""})
    fig.update_layout(yaxis={"categoryorder": "total ascending"})
    st.plotly_chart(fig, use_container_width=True)

# ================= PAGE 4: STONE x COUNTRY HEATMAP =================
with tab_heatmap:
    st.header("Stone × Country Heatmap")
    st.caption("Which stone should Style sell in which country? Values are Product-Market Fit Score (0-100).")
    pivot = scores.pivot(index="stone", columns="country", values="fit_score")
    fig = px.imshow(pivot, text_auto=True, color_continuous_scale="YlOrRd", aspect="auto")
    fig.update_layout(height=500)
    st.plotly_chart(fig, use_container_width=True)
    st.dataframe(scores.pivot(index="stone", columns="country", values="rating_stars"), use_container_width=True)

# ================= PAGE 5: TARGET SEGMENTS =================
with tab_segment:
    st.header("Target Audience Intelligence")
    seg_display = segments.copy()
    if segment_filter:
        seg_display = seg_display[seg_display["segment"].isin(segment_filter)]
    st.dataframe(seg_display.sort_values("score", ascending=False), use_container_width=True, hide_index=True)

    fig = px.bar(seg_display.sort_values("score", ascending=False), x="score", y="segment", orientation="h",
                 color="priority", labels={"score": "Target Segment Score (0-100)", "segment": ""})
    fig.update_layout(yaxis={"categoryorder": "total ascending"}, height=500)
    st.plotly_chart(fig, use_container_width=True)

# ================= PAGE 6: SWOT =================
with tab_swot:
    st.header("SWOT Dashboard")
    swot_filter = st.multiselect("Filter by category", swot["category"].unique(), default=list(swot["category"].unique()))
    swot_display = swot[swot["category"].isin(swot_filter)]

    for cat in ["Strength", "Weakness", "Opportunity", "Threat"]:
        subset = swot_display[swot_display["category"] == cat].sort_values("impact_score", ascending=False)
        if subset.empty:
            continue
        st.subheader(cat + "s")
        st.dataframe(subset[["item", "impact_score", "importance", "evidence_status"]], use_container_width=True, hide_index=True)

# ================= PAGE 7: ACTION PLAN =================
with tab_action:
    st.header("Export Action Plan")
    st.dataframe(action_plan, use_container_width=True, hide_index=True)

    fig = px.timeline(
        action_plan.assign(
            start=pd.Categorical(action_plan["phase"]).codes,
            finish=pd.Categorical(action_plan["phase"]).codes + 1,
        ),
        x_start="start", x_end="finish", y="action", color="phase"
    )
    fig.update_yaxes(autorange="reversed")
    fig.update_layout(height=500, xaxis_title="Phase (1 = Months 1-2 ... 5 = Months 10-12)")
    st.plotly_chart(fig, use_container_width=True)

st.sidebar.markdown("---")
st.sidebar.caption("Built for Style for Marble and Granite — Phase 2 deliverable. "
                    "Data current as of the Phase 1 report; refresh country scores as real orders/quotes come in.")
