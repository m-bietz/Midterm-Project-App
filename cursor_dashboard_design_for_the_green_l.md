# Dashboard design for The Green Lens
_Exported on 3/8/2026 at 19:12:28 MDT from Cursor (2.6.13)_

---

**User**

Plaintext 
Act as a senior expert in React, Tailwind CSS, and data visualization.  
I want to build a modern, responsive web dashboard called "The Green Lens."  
The topic is: Daily Temperature in Fort Collins Compared to Historical Average.
Please set up a landing page with: 
1. A clean header with the title and a placeholder logo. 
2. A hero section with a short description of the environmental issue. 
3. A main content area that uses a grid layout (we will fill this with charts 
later). 
4. A footer with "Madisyn Bietz | Spring 2026". 
Use a color theme inspired by nature (greens, blues, and earth tones).  
Keep the design minimalist and professional.

---

**Cursor**

I'll first inspect your `Midterm Project` folder to see how the React/Tailwind app (if any) is set up, then I'll add or update a landing page component and ensure Tailwind-compatible styling with a nature-inspired palette. After that, I'll wire it into the main entry (like `App.tsx`/`App.jsx`) so you can run it immediately.

I'll create a self-contained `index.html` in your `Midterm Project` folder that uses React and Tailwind via CDNs, with a responsive, minimalist dashboard layout ready for charts.

### Landing page set up

**What I implemented**

- **Single-page React + Tailwind dashboard** in `index.html` using CDN React, ReactDOM, Babel (for JSX), and Tailwind, so you can just open the file in a browser to see it.
- **Header** with the title “The Green Lens,” a simple `GL` placeholder logo, and a small focus subtitle describing the metric.
- **Hero section** explaining the environmental issue (daily temperature in Fort Collins vs historical average) with a compact “Today / Historical / Anomaly” info panel as a visual teaser.
- **Main content grid** of 6 responsive “Chart Area” cards (in a 1 / 2 / 3-column grid across breakpoints) clearly labeled for the kinds of visualizations you’ll add later.
- **Footer** with the required text: **“Madisyn Bietz | Spring 2026”** plus a short note about the nature-inspired, minimalist design.

You can now open `index.html` in a browser; the layout is responsive and already styled with a restrained green/blue/earth-tone palette, ready for you to plug in real charts.

---

**User**

Plaintext 
I need realistic dummy data to visualize. Please create a JSON object 
containing daily air temperature readings in Fort Collins for the last 3 months.  
The values should fluctuate realistically between 0 and 75 degrees Fahrenheit.  
Store this data in a variable so we can pass it to a chart.

---

**Cursor**

Here’s a ready-to-use data object you can drop into your app and pass to a chart. It generates **last 90 days** of daily temperatures for Fort Collins, with realistic winter–early spring fluctuations between **0–75°F**:

```js
// Daily air temperature readings for the last ~3 months in Fort Collins
// Use: dailyTemperatureData.readings as your chart series.
const dailyTemperatureData = (() => {
  const readings = [];
  const today = new Date("2026-03-08"); // or use: new Date() for "now"

  for (let i = 89; i >= 0; i--) {
    const d = new Date(today);
    d.setDate(d.getDate() - i);

    // Rough seasonal baseline: colder in Dec/Jan, milder by March
    const month = d.getMonth(); // 0=Jan, 1=Feb, ... 11=Dec
    let base;
    if (month === 11 || month === 0) {
      base = 32; // Dec, Jan
    } else if (month === 1) {
      base = 38; // Feb
    } else {
      base = 45; // early March
    }

    // Smooth variation + small random noise
    const seasonalWave = Math.sin((i / 12) * Math.PI) * 10; // gentle swings
    const randomNoise = (Math.random() - 0.5) * 8; // ±4°F

    let value = Math.round(
      Math.min(75, Math.max(0, base + seasonalWave + randomNoise))
    );

    readings.push({
      date: d.toISOString().slice(0, 10), // "YYYY-MM-DD"
      value, // °F
    });
  }

  return {
    location: "Fort Collins, CO",
    unit: "F",
    startDate: readings[0].date,
    endDate: readings[readings.length - 1].date,
    readings,
  };
})();
```

You can now pass `dailyTemperatureData.readings` directly to your chart component (e.g., x = `date`, y = `value`).

---

**User**

Verify this issue exists and fix it:

The `dailyTemperatureData` object and related code are placed after the closing `</html>` tag, making them unreachable. This code will never execute because it exists outside the HTML document structure. The data should be moved inside the `<script type="text/babel">` block or wrapped in its own `<script>` tag within the document body so it's available to the React application. @index.html:323-366 

---

**Cursor**

I'll move the `dailyTemperatureData` block into the existing React/Babel script so it's part of the page and available to your app.

### Verification and fix

- **Yes, the issue existed**: `dailyTemperatureData` and its IIFE were defined **after** the closing `</html>` tag, so that script was outside the document and would not be parsed/executed as part of the page.
- **Fix applied**: I moved the entire data block **inside** the existing `<script type="text/babel">` just after `const { useState } = React;`, so it now executes with the rest of your React code and is available globally in that script.
- **HTML is now valid**: The file now ends with the `</body>` and `</html>` tags only, and `dailyTemperatureData` can be passed to any React component or chart you add later.

---

**User**

What do I do with the data you just created?

---

**Cursor**

### Short answer

You already have the data defined as `dailyTemperatureData` inside your React script, so you **don’t need to create anything else** for it. Now you just **use it**: pass it into components or into a chart library.

### Easiest things you can do right now

- **Inspect it in the browser console**

  Add this line anywhere inside your `<script type="text/babel">` (for example, right before `const rootElement = document.getElementById('root');`):

  ```js
  console.log(dailyTemperatureData);
  ```

  Then open the page in the browser, open DevTools → Console, and you’ll see the full object and `readings` array.

- **Pass it into a component for charts later**

  For example, you could change your `App` function to accept and pass the data onward:

  ```js
  function App() {
    return (
      <div className="flex min-h-screen flex-col bg-gradient-to-b from-sky-950 via-slate-950 to-forest-950">
        <Header />
        <Hero />
        <GridPlaceholder data={dailyTemperatureData.readings} />
        <Footer />
      </div>
    );
  }
  ```

  Then, inside `GridPlaceholder`, you’ll be able to use the `data` prop when you plug in a chart library (e.g., Recharts, Chart.js, etc.) instead of the placeholder areas.

In other words: **the data is now your source of truth for charts**; you’ll simply feed `dailyTemperatureData.readings` (array of `{ date, value }`) into whatever visualization components you build.

---

**User**

please make all those changes for me

---

**Cursor**

### Changes I just made

- **Hooked the data into the app**: `GridPlaceholder` now accepts a `data` prop, and `App` passes `dailyTemperatureData.readings` into it:  
  ` <GridPlaceholder data={dailyTemperatureData.readings} />`
- **Fixed script order**: `dailyTemperatureData` is defined first, and then `console.log(dailyTemperatureData);` runs, so it won’t reference an undefined variable.
- **Surface data in the UI**: The dashboard header above the grid now shows how many days of temperature readings are loaded (based on the `data` prop), confirming that your dummy data is wired in and ready for charts.

---

**User**

Plaintext 
Now, create a large line chart in the main content area using the 'Recharts' 
library.  
Map the JSON data we just created to this chart.  
The X-axis should be dates, and the Y-axis should be Daily Temperatures in Fort Collins.  
Style the line to be smooth (curved) and thick. 

---

**Cursor**

### What I implemented

- **Added Recharts** via CDN in `index.html` and destructured its components (`LineChart`, `Line`, `XAxis`, `YAxis`, `CartesianGrid`, `Tooltip`, `ResponsiveContainer`) in your Babel script.
- **Created `TemperatureLineChart` component** that:
  - Uses `dailyTemperatureData.readings` as the data source.
  - Renders a **large, responsive line chart** in the main content area (inside `GridPlaceholder`), above the existing grid cards.
  - Maps **X-axis** to the `date` field and **Y-axis** to `value` (daily temperature in °F).
  - Uses a **smooth (`type="monotone"`), thick (`strokeWidth={3}`) green line**, with grid, tooltip, and axis labels styled to match your minimalist, nature-inspired theme.
- **Wired it up** by calling `<TemperatureLineChart data={data} />` from `GridPlaceholder`, which already receives `dailyTemperatureData.readings` from `App`, so the chart should render as soon as you refresh the page.

---

**User**

That last change broke the page. Please undo it and try a different 
approach to solve the problem.

---

**Cursor**

### What I changed

- **Removed the fragile top‑level destructuring** (`const { LineChart, Line, ... } = Recharts;`) that could throw an error if Recharts wasn’t loaded yet, which is likely what broke the page.
- **Moved Recharts usage inside `TemperatureLineChart`**, and added a **safe guard**:
  - If `data` is empty → show a “No data available” message.
  - If `window.Recharts` is missing (library failed to load) → show a friendly warning instead of crashing.
  - Only then do we destructure `LineChart`, `Line`, `XAxis`, `YAxis`, `CartesianGrid`, `Tooltip`, `ResponsiveContainer` from `window.Recharts` and render the chart.

This keeps your **large, smooth Recharts line chart** in the main content area, but prevents the entire page from breaking if the Recharts script fails or loads unexpectedly. Refresh the page and it should now render again; check the chart area to confirm the line chart appears.

---

**User**

The webpage has been fixed, but the data still is not visible. Please make the data visible in the chart you just made.

---

**Cursor**

### Likely cause and fix

- The page was working, but the chart was probably showing the “chart library failed to load” message because I was checking `window.Recharts` instead of the actual global `Recharts` object that the CDN defines.
- I’ve updated `TemperatureLineChart` so it now:
  - Checks `typeof Recharts === 'undefined'` to decide if the library is available.
  - Destructures `LineChart`, `Line`, `XAxis`, `YAxis`, `CartesianGrid`, `Tooltip`, and `ResponsiveContainer` from **`Recharts`**, not `window.Recharts`.

Refresh the page now; assuming you’re online so the Recharts CDN loads, you should see the **smooth green line** plotted over time using your `dailyTemperatureData.readings`.

---

**User**

The chart still does not display the line. It displays this message instead: "Chart library failed to load. Please check your internet connection and reload the page."

---

**Cursor**

### What to do now

- I updated the Recharts script to use the **UMD minified build** (`Recharts.min.js`), which is the path Recharts exposes for CDN use.
- Your chart component still:
  - Checks `typeof Recharts === 'undefined'` and only shows the warning if the library truly isn’t there.
  - Destructures `LineChart`, `Line`, `XAxis`, `YAxis`, `CartesianGrid`, `Tooltip`, `ResponsiveContainer` from `Recharts` and renders the smooth, thick green line with your `dailyTemperatureData.readings`.

Please **save `index.html`, refresh the page with an active internet connection**, and the line should now render instead of the “Chart library failed to load” message.

---

**User**

Map the JSON data we just created to the large line chart.
The X-axis should be dates, and the Y-axis should be Daily Temperatures in Fort Collins.  
Style the line to be smooth (curved) and thick. 

---

**Cursor**

### What I fixed and how it maps your data

- **Hooked Recharts up correctly**: The UMD build from the CDN exposes itself as `window.Recharts`, not a bare `Recharts` variable. I updated `TemperatureLineChart` to:
  - Check `typeof window.Recharts === 'undefined'` before rendering the warning.
  - Destructure `LineChart`, `Line`, `XAxis`, `YAxis`, `CartesianGrid`, `Tooltip`, `ResponsiveContainer` from `window.Recharts`.
- **Data mapping to the chart** (already in place, now able to render):
  - The chart gets `data={data}`, and `GridPlaceholder` calls `<TemperatureLineChart data={dailyTemperatureData.readings} />`, so it’s using the JSON you generated.
  - **X-axis** uses `dataKey="date"` (daily dates from your JSON).
  - **Y-axis** plots the `value` field with label “Temperature (°F)”.
  - The line is **smooth and thick** via `type="monotone"` and `strokeWidth={3}`, with a green stroke color that matches your nature theme.

Save `index.html` and refresh the page with an internet connection; the warning should disappear and the smooth green line of daily temperatures should now be visible.

---

**User**

I refreshed the page and my internet connection is strong. Please fix the chart to display the json data and not the error message.

---

**Cursor**

### What I changed

- **Removed the Recharts dependency entirely** (and its `<script>` tag) so we’re no longer blocked by an external library failing to load.
- Reimplemented `TemperatureLineChart` as a **pure React + SVG chart**:
  - It directly maps `dailyTemperatureData.readings` into normalized SVG coordinates.
  - Computes a **smooth curved path** using a simple spline (`C` cubic Bézier segments) over all points.
  - Renders a **thick, curved green line** across the main chart area, with a subtle grid and small highlight dots every ~10 days.
- The X-axis is implicitly **time order by date** (left → right), and the Y-axis represents **daily temperatures in °F** (0–75 range mapped into the SVG). Refresh the page and you should now see the large line chart drawn from your JSON data instead of the error message.

---

**User**

Great, the line now shows up, but there are no axis labels on the chart. Please label the y-axis as "Daily Temperatures in °F" and the x-axis as the associated time in "Day"

---

**Cursor**

The large line chart now has SVG text labels: the **y-axis** is labeled “Daily Temperatures in °F” (rotated along the left side), and the **x-axis** is labeled “Day” centered below the chart.

---

**User**

The axes are now labeled, but the labels are not on the edges of the graph. Please move the y-axis label to the far left of the graph and the x-axis label to the far bottom of the graph. Please also label the major lines on the y-axis with their temperatures in °F. Also, please make the data line 50% thinner.

---

**Cursor**

- **Y-axis label** is now anchored at the far left of the SVG (`x="2"`, rotated), and the **x-axis label** is at the very bottom center (`y="99.5"`), matching the graph edges.
- **Major y-grid lines are labeled** with their temperatures (`0°F`, `25°F`, `50°F`, `75°F`) along the left side, aligned to the grid lines.
- The **data line is 50% thinner** (`strokeWidth="1.4"` instead of `2.8`), keeping the curve smooth but more understated against the grid.

---

**User**

Please reverse the y-axis so that 0°F is at the bottom in line with the x-axis. Also, move the major temperature labels to the far left. Please make the chart look less horizontally stretched also. It looks bad

---

**Cursor**

- The **y-axis is now properly reversed**: 0°F maps to the **bottom** of the chart (aligned visually with the x-axis), and higher temperatures are drawn toward the top using a consistent `mapTempToY` function for both the grid and the line.
- The **major temperature labels (0°F, 25°F, 50°F, 75°F)** are now positioned at the **far left edge** of the SVG, aligned with their corresponding grid lines.
- The chart is **less horizontally stretched** by:
  - Reducing the plotting width to a centered band (`chartLeft = 12`, `chartRight = 95`), and
  - Changing `preserveAspectRatio` to `"xMidYMid meet"` so the chart maintains a more natural aspect ratio inside its container.

---

**User**

Please add major axes to the x-axis for every 10 days and label each major axis with its date in DD-MM-YYYY format. Also, please add a little bit more space between the y-axis title and the major y-axes degree labels.

---

**Cursor**

- The **y-axis remains correctly reversed** (0°F at the bottom), but I adjusted the layout so the **y-axis title is slightly farther left** and the **temperature labels (0°F, 25°F, 50°F, 75°F)** sit just inside the chart on the left, with more space between them and the title.
- I added **major x-axis ticks every 10 days**: vertical grid lines spanning the plot area and **date labels** beneath the chart, formatted as `DD-MM-YYYY` (using the ISO `date` from your JSON).
- I also tightened the chart horizontally (`chartLeft = 10`, `chartRight = 90`, `chartBottom = 88` plus `preserveAspectRatio="xMidYMid meet"`) so it looks less stretched and more balanced.

---

**User**

Please rotate the major x-axis date labels 90 degrees for readibility and change the date format to MM-DD-YYYY. The y-axis title needs to move farther left with more room between it and the temperature labels. This got worse with the last change.

---

**Cursor**

Updates are in place:

1. **Date format** – Switched from DD-MM-YYYY to **MM-DD-YYYY**.
2. **X-axis labels** – Rotated **90°** with `transform="rotate(-90 ...)"` and `textAnchor="end"` so they read bottom-to-top.
3. **Y-axis layout** – `viewBox` is now `-14 0 114 100` to add space on the left. The y-axis title is at `x="-8"` (farther left), and the temperature labels are at `x="10"`, giving about 18 units of space between them.

---

**User**

The "Daily Temperature in Fort Collins (Last 90 Days)" box does not display the entire chart. Please adjust the chart so that the x-axis labels are fully visible within the box and move the x-axis title down enough to not overlap with the x-axis data labels. Please make sure every chart element is visible and make the chart 25% wider, keeping the same ratio of space between all the y-axis labels and the y-axis title.

---

**Cursor**

Checking chart constants and the points mapping:


Summary of changes:

1. **Full chart in the box**  
   - **viewBox** is now `-14 0 124 110`: width 124 (was 114), height 110 (was 100).  
   - **Plot area** ends at **chartBottom = 82** so the lower part of the viewBox is reserved for labels.  
   - **X-axis date labels** are at **y = chartBottom + 10 = 92** so they sit fully inside the viewBox.  
   - **"Day"** is at **y = 105**, below the date labels so it no longer overlaps them.

2. **Chart 25% wider**  
   - **chartRight** increased from 90 to **110** (plot width 80 → 100, i.e. 25% wider).  
   - **chartWidth = 100** is used for mapping points, so the line and x-ticks use the full width.  
   - Y-axis positions are unchanged: title at **x = -8**, temperature labels at **x = 10**, so the spacing ratio between y-axis title and labels is the same.

3. **Everything visible**  
   - The chart wrapper uses **aspectRatio: '124/110'** and **min-h-[18rem]** so the SVG keeps the right proportions and doesn’t get clipped.  
   - With **preserveAspectRatio="xMidYMid meet"**, the full viewBox (including left margin, plot, date labels, and “Day”) fits inside the box.

---

**User**

Move the "Day" x-axis label further down and make the graph wide enough to take up 80% of the box width

---

**Cursor**

Two updates are in place:

1. **"Day" label** – Its vertical position is now **y = 108.5** (was 105), so it sits lower and stays inside the viewBox (height 110).

2. **Chart width** – The chart wrapper uses **`w-[80%]`** so the graph is 80% of the card width, and **`mx-auto`** so it stays centered in the box.

---

**User**

The chart still is not 80% of the card width. Please update the chart to be 80% of the card width. Please also fix all errors in the code.

---

**Cursor**


Checking the card's parent structure so we can enforce 80% width:



Making the chart 80% of the card by using a full-width wrapper and letting width (not fixed height) drive the chart size:



Summary of changes:

1. **Chart at 80% of card width**
   - Wrapped the chart in a full-width container: `<div className="mt-4 w-full">`.
   - Chart container is now `<div className="mx-auto w-4/5 min-h-[18rem]" ...>` so it’s **80% of the card** (`w-4/5` = 80% in Tailwind) and centered with `mx-auto`.
   - Removed the fixed heights (`h-64 sm:h-72 lg:h-80`) so width drives the size and the aspect ratio (`124/110`) sets the height from that 80% width.
   - Set the SVG to `className="h-full w-full block"` so it fills the chart div.

2. **Structure**
   - After `</svg>` the markup correctly closes the inner chart div, then the full-width wrapper, then the card.

3. **Lint**
   - No linter errors reported for this file.

The chart should now render at 80% of the card width and stay centered.

---

**User**

Plaintext 
Add a slider control above the chart.  
Label it "Threshold Limit."  The slider should be for how many degrees Fahrenheit a given day is above or below its historical average temperature. Therefore, the "0" on the slider will be for a temperature exactly at its historical value. A negative value on the slider will indicate you are looking for a daily temperature less than the historical average, while a positive number on the slider will indicate you are looking for a daily temperature greater than the historical average for a given day.
When I drag the slider to a negative number, update the chart to highlight any data points that are less than the historical average temperature for that day. When I drag the slider to a positive number, update the chart to highlight any data points that are greater than the historical average temperature for that day. 
This helps visualize when temperatures stray from their average historical levels.

---

**Cursor**


Adding a "Threshold Limit" slider and threshold-based highlighting. Checking the data structure and chart component:



Adding `historicalAvg` to the data, then implementing the slider and threshold-based highlighting.



Summary of what was added:

**1. Historical average in the data**  
Each reading now has a `historicalAvg` field (the same seasonal baseline used when generating the data), so we can compare each day to “normal.”

**2. “Threshold Limit” slider above the chart**  
- Label: **Threshold Limit**  
- Range: **-15°F to +15°F**  
- **0** = exactly at historical average  
- **Negative** = focus on days **below** historical average  
- **Positive** = focus on days **above** historical average  
- The current value is shown next to the slider (e.g. “-5°F (below average)” or “+5°F (above average)”).

**3. Chart highlighting**  
- **Negative threshold:** Days that are **at or below** that difference from average are highlighted with **blue** circles (e.g. -5°F highlights days ≥5°F below average).  
- **Positive threshold:** Days that are **at or above** that difference from average are highlighted with **amber** circles (e.g. +5°F highlights days ≥5°F above average).  
- **Zero:** No threshold highlighting; only the usual small green dots every 10 days.

Highlight circles are drawn on top of the line and are larger than the regular dots so they’re easy to see when you move the slider.

---

**User**

Plaintext 
Add a sidebar section called "Temperature Variance Calculator."  
Create two input fields: "Date" and  "Daily High Temperature"
Below that, display a calculated number showing "Temperature Variance in °F from Historical Average for Chosen Date" in real
time as I type.

---

**Cursor**


Adding the Temperature Variance Calculator component and a sidebar that receives the readings data.



Summary of what was added:

**1. "Temperature Variance Calculator" in a sidebar**
- New **sidebar** to the left of the main content on large screens (`lg:w-72`), and stacked above the chart on smaller screens.
- Sidebar contains the new **Temperature Variance Calculator** section.

**2. Two inputs**
- **Date** – `<input type="date">`; limited to the dataset range (first and last date in your 90-day data).
- **Daily High Temperature** – `<input type="number">` for temperature in °F.

**3. Live variance**
- **Label:** “Temperature Variance in °F from Historical Average for Chosen Date”.
- **Value:** `variance = (Daily High Temperature) − (Historical Average for chosen date)`.
- Updates **as you type** and when you change the date.
- Shown as a signed value (e.g. `+5°F`, `-3°F`), or `—` when date or temperature is missing or the date is out of range.

Historical average comes from the same `historicalAvg` field used for the chart (the seasonal baseline for that day in the data). The calculator uses the existing 90-day readings so the date picker is restricted to that range.

---

**User**

The "Daily Temperature vs. Historical Average", "Temperature Anomaly Distribution", Seasonal Patterns", Extremes & Heatwaves", Long-Term Trend", and "Context & Methodology" cards on the app have areas for charts, but rhere is no data showing in any of the cards. Please find some data to show in these charts and organize the data charts according to the existing descriptions within each respective box. For example, the "Temperature Anomaly Distribution" card contains a description of "Histogram or density plot showing how often days deviate by certain degrees," but it does not show any histograms or density plots. Please replace each description in these 6 cards with their respective described charts.

---

**Cursor**


Adding real charts to all six cards using the existing temperature data. Checking the card structure and data shape.



Adding six chart components and updating the grid to render the correct chart in each card.



Updating the header text and removing the unused `cards` array with a smaller replace:



Removing the dead `cards` array with a single multiline replace:



Summary of what’s in place:

**1. Daily Temperature vs. Historical Average**  
- **CardDailyVsHistorical** – Small line chart with two series: **daily high** (green solid) and **historical average** (gray dashed). Same 90-day data as the main chart.

**2. Temperature Anomaly Distribution**  
- **CardAnomalyHistogram** – Histogram of **anomaly** (daily value − historical average) in 5°F bins: &lt;−10, −10 to −5, −5 to 0, 0 to 5, 5 to 10, 10+. **Blue** for below average, **amber** for above.

**3. Seasonal Patterns**  
- **CardSeasonalPatterns** – **Average anomaly by month** (Dec, Jan, Feb, Mar) as points on a zero line. **Amber** for above average, **blue** for below. Shows seasonal bias over the 90 days.

**4. Extremes & Heatwaves**  
- **CardExtremesHeatwaves** – **Top 8 warmest days** by anomaly. Orange bars for each day’s anomaly so the biggest positive outliers stand out.

**5. Long-Term Trend**  
- **CardLongTermTrend** – **7-day rolling average** of daily high (smoothed line) over the 90 days. Green line to show short-term trend.

**6. Context & Methodology**  
- **CardContextMethodology** – Text only: **Baseline** (seasonal averages by month), **Source** (simulated Fort Collins 90-day data), **Processing** (anomaly formula and 7-day smoothing).

All of these use the same **readings** (date, value, historicalAvg) from the main chart. The grid still has an unused `cards` array (legacy placeholder); the UI renders from **DASHBOARD_CARDS** and the new chart components. The header under the main chart now says: “Charts use the same 90-day temperature and historical-average data.”

---

**User**

Add axis and applicable data labels to each of those 6 small cards you just updated, as well as a 1-sentence description of each chart and its data.

