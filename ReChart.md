# Recharts Complete Control Guide

A comprehensive guide to controlling every aspect of Recharts components.

## Table of Contents

1. [The Basic Recipe](#the-basic-recipe)
2. [Control 1: ResponsiveContainer](#control-1-responsivecontainer)
3. [Control 2: ChartType](#control-2-charttype)
4. [Control 3: ChartComponent](#control-3-chartcomponent)
5. [Control 4: Tooltip](#control-4-tooltip)
6. [Control 5: Legend](#control-5-legend)
7. [Quick Reference](#quick-reference)
8. [Layout Templates](#layout-templates)
9. [Troubleshooting](#troubleshooting)

---

## The Basic Recipe

```jsx
<ResponsiveContainer>
  {" "}
  // 1. Container - makes it responsive
  <ChartType>
    {" "}
    // 2. Chart component (PieChart, LineChart, etc.)
    <ChartComponent /> // 3. Main visual (Pie, Line, Bar)
    <Tooltip /> // 4. Optional: Tooltip for interactivity
    <Legend /> // 5. Optional: Legend for labels
  </ChartType>
</ResponsiveContainer>
```

---

## Control 1: ResponsiveContainer

Handles responsive sizing of the chart container.

Props

| Prop      | Type          | Default | Description                       |
| --------- | ------------- | ------- | --------------------------------- |
| width     | string/number | "100%"  | Width: "100%", "90%", or pixels   |
| height    | string/number | "100%"  | Height: number (pixels) or "100%" |
| minWidth  | number        | -       | Minimum width in pixels           |
| minHeight | number        | -       | Minimum height in pixels          |
| debounce  | number        | 0       | Delay resize updates (ms)         |

Examples

```jsx
// Fixed height, responsive width
<ResponsiveContainer width="100%" height={400}>

// Square chart
<ResponsiveContainer width="100%" height="100%" minHeight={300}>

// Wide chart for dashboard
<ResponsiveContainer width="100%" height={250} debounce={50}>
```

---

## Control 2: ChartType

Defines chart structure, layout, and margins.

Available Chart Types

- PieChart - For pie/donut charts
- LineChart - For line/trend charts
- BarChart - For bar charts
- AreaChart - For area charts
- ComposedChart - For mixed chart types

Common Props

| Prop   | Type   | Default                                  | Description                        |
| ------ | ------ | ---------------------------------------- | ---------------------------------- |
| width  | number | -                                        | Fixed width (overrides container)  |
| height | number | -                                        | Fixed height (overrides container) |
| margin | object | { top: 5, right: 5, bottom: 5, left: 5 } | Space around chart                 |

Examples

```jsx
// With custom margins
<PieChart margin={{ top: 20, right: 30, left: 20, bottom: 20 }}>

// Compact chart
<LineChart margin={{ top: 5, right: 5, left: 5, bottom: 5 }}>

// Large top margin for title
<BarChart margin={{ top: 50, right: 20, left: 20, bottom: 20 }}>
```

---

## Control 3: ChartComponent

Controls data visualization (size, position, colors, shapes).

Pie Component

```jsx
<Pie
  // Positioning
  cx="50%" // Center X: "0%" to "100%" or pixels
  cy="50%" // Center Y: "0%" to "100%" or pixels
  // Sizing
  innerRadius={0} // 0 = pie, >0 = donut hole
  outerRadius={80} // Overall pie size
  paddingAngle={0} // Gap between slices (degrees)
  // Data
  data={data} // Your data array
  nameKey="name" // Field for labels
  dataKey="value" // Field for values
  fill="#8884d8" // Default color
  // Labels
  label={{
    fontSize: 12,
    fontWeight: "bold",
    fill: "#333",
    position: "outside", // 'inside', 'outside', 'center'
  }}
  labelLine={true} // Line from slice to label
  // Animation
  isAnimationActive={true}
  animationDuration={500}
  animationBegin={0}
  animationEasing="ease"
  // Interactivity
  onMouseEnter={(data, index) => console.log(data)}
  onClick={(data, index) => console.log(data)}
>
  {data.map((entry, index) => (
    <Cell key={`cell-${index}`} fill={entry.color} />
  ))}
</Pie>
```

Line Component

```jsx
<Line
  type="monotone" // 'monotone', 'linear', 'step', 'stepBefore', 'stepAfter'
  dataKey="value" // Which data field to plot
  stroke="#8884d8" // Line color
  strokeWidth={2} // Line thickness
  dot={{ r: 4 }} // Show dots on data points
  activeDot={{ r: 8 }} // Size of dot on hover
  connectNulls={false} // Connect gaps in data
/>
```

Bar Component

```jsx
<Bar
  dataKey="value" // Which data field to plot
  fill="#8884d8" // Bar color
  barSize={20} // Width of bars
  maxBarSize={50} // Maximum bar width
  radius={[4, 4, 0, 0]} // Rounded corners (top-left, top-right, bottom-right, bottom-left)
  label={{ position: "top" }} // Show values on bars
/>
```

Area Component

```jsx
<Area
  type="monotone" // Line smoothing type
  dataKey="value" // Which data field to plot
  fill="#8884d8" // Area fill color
  fillOpacity={0.3} // Transparency of fill
  stroke="#8884d8" // Line color
  strokeWidth={2} // Line thickness
/>
```

---

## Control 4: Tooltip

Displays data details on hover interaction.

Props

| Prop           | Type     | Description                  |
| -------------- | -------- | ---------------------------- |
| contentStyle   | object   | Style for tooltip container  |
| labelStyle     | object   | Style for label text         |
| itemStyle      | object   | Style for value items        |
| formatter      | function | Format values before display |
| labelFormatter | function | Format label before display  |
| offset         | number   | Offset from cursor (pixels)  |
| cursor         | object   | Style of cursor line         |
| trigger        | string   | 'hover', 'click', or 'focus' |

Examples

```jsx
// Basic tooltip
<Tooltip />

// Styled tooltip
<Tooltip
  contentStyle={{
    backgroundColor: '#fff',
    border: '1px solid #ccc',
    borderRadius: '4px',
    padding: '8px',
    fontSize: '12px'
  }}
  labelStyle={{
    fontWeight: 'bold',
    marginBottom: '4px',
    color: '#333'
  }}
  itemStyle={{
    padding: '2px 0',
    color: '#666'
  }}
/>

// With custom formatting
<Tooltip
  formatter={(value, name, props) => [`$${value}`, name]}
  labelFormatter={(label) => `Date: ${label}`}
  offset={10}
  cursor={{ stroke: '#666', strokeWidth: 1 }}
/>
```

---

## Control 5: Legend

Manages labels, keys, and their positioning.

Props

| Prop          | Type          | Default      | Description                                     |
| ------------- | ------------- | ------------ | ----------------------------------------------- |
| verticalAlign | string        | 'bottom'     | 'top', 'middle', 'bottom'                       |
| align         | string        | 'center'     | 'left', 'center', 'right'                       |
| layout        | string        | 'horizontal' | 'horizontal', 'vertical'                        |
| width         | string/number | 'auto'       | Width of legend area                            |
| height        | number        | 'auto'       | Height of legend area                           |
| iconSize      | number        | 14           | Size of color icon                              |
| iconType      | string        | 'circle'     | 'circle', 'square', 'line', 'rect', 'plainline' |
| wrapperStyle  | object        | {}           | CSS styles for legend wrapper                   |
| formatter     | function      | -            | Custom legend text formatting                   |
| onClick       | function      | -            | Click handler for legend items                  |

Examples

```jsx
// Basic legend
<Legend />

// Right-aligned vertical legend
<Legend
  verticalAlign="middle"
  align="right"
  width="30%"
  layout="vertical"
  iconType="circle"
  iconSize={10}
/>

// Bottom horizontal legend
<Legend
  verticalAlign="bottom"
  align="center"
  width="100%"
  layout="horizontal"
  iconType="square"
/>

// Custom formatted legend
<Legend
  verticalAlign="top"
  align="left"
  wrapperStyle={{
    fontSize: '12px',
    paddingTop: '10px'
  }}
  formatter={(value, entry, index) => (
    <span style={{ color: entry.color }}>{value}</span>
  )}
  onClick={(data) => console.log('Legend clicked:', data)}
/>
```

---

## Quick Reference

Positioning Values

| Position     | cx    | cy    |
| ------------ | ----- | ----- |
| Center       | "50%" | "50%" |
| Top-Left     | "25%" | "25%" |
| Top-Right    | "75%" | "25%" |
| Bottom-Left  | "25%" | "75%" |
| Bottom-Right | "75%" | "75%" |

Common Layout Patterns

| Layout                 | cx    | Legend align | Legend verticalAlign |
| ---------------------- | ----- | ------------ | -------------------- |
| Pie Left, Legend Right | "35%" | "right"      | "middle"             |
| Pie Right, Legend Left | "65%" | "left"       | "middle"             |
| Legend Below           | "50%" | "center"     | "bottom"             |
| Legend Above           | "50%" | "center"     | "top"                |

Chart Types Quick Selection

| Use Case              | Chart Type    |
| --------------------- | ------------- |
| Show proportions      | PieChart      |
| Show trends over time | LineChart     |
| Compare categories    | BarChart      |
| Show volume over time | AreaChart     |
| Mixed data types      | ComposedChart |

---

## Layout Templates

Template 1: Pie with Right Legend

```jsx
<ResponsiveContainer width="100%" height={300}>
  <PieChart>
    <Pie
      data={data}
      cx="35%" // Shift left for legend space
      cy="50%" // Vertically centered
      innerRadius={60}
      outerRadius={90}
      paddingAngle={3}
      dataKey="value"
      nameKey="name"
    >
      {data.map((entry, index) => (
        <Cell key={`cell-${index}`} fill={entry.color} />
      ))}
    </Pie>
    <Tooltip />
    <Legend
      verticalAlign="middle"
      align="right"
      width="30%"
      layout="vertical"
      iconType="circle"
      iconSize={10}
    />
  </PieChart>
</ResponsiveContainer>
```

Template 2: Donut Chart with Center Text

```jsx
<ResponsiveContainer width="100%" height={300}>
  <PieChart>
    <Pie
      data={data}
      cx="50%"
      cy="50%"
      innerRadius={60} // Creates donut hole
      outerRadius={100}
      paddingAngle={3}
      dataKey="value"
      nameKey="name"
    >
      {data.map((entry, index) => (
        <Cell key={`cell-${index}`} fill={entry.color} />
      ))}
    </Pie>
    <Tooltip />
    <text
      x="50%"
      y="50%"
      textAnchor="middle"
      dominantBaseline="middle"
      style={{ fontSize: "24px", fontWeight: "bold" }}
    >
      Total
    </text>
  </PieChart>
</ResponsiveContainer>
```

Template 3: Line Chart with Dual Axis

```jsx
<ResponsiveContainer width="100%" height={400}>
  <LineChart data={data} margin={{ top: 20, right: 30, left: 20, bottom: 20 }}>
    <CartesianGrid strokeDasharray="3 3" />
    <XAxis dataKey="name" />
    <YAxis yAxisId="left" />
    <YAxis yAxisId="right" orientation="right" />
    <Tooltip />
    <Legend />
    <Line
      yAxisId="left"
      type="monotone"
      dataKey="revenue"
      stroke="#8884d8"
      strokeWidth={2}
    />
    <Line
      yAxisId="right"
      type="monotone"
      dataKey="units"
      stroke="#82ca9d"
      strokeWidth={2}
    />
  </LineChart>
</ResponsiveContainer>
```

Template 4: Stacked Bar Chart

```jsx
<ResponsiveContainer width="100%" height={400}>
  <BarChart data={data} margin={{ top: 20, right: 30, left: 20, bottom: 20 }}>
    <CartesianGrid strokeDasharray="3 3" />
    <XAxis dataKey="name" />
    <YAxis />
    <Tooltip />
    <Legend />
    <Bar dataKey="productA" stackId="a" fill="#8884d8" />
    <Bar dataKey="productB" stackId="a" fill="#82ca9d" />
    <Bar dataKey="productC" stackId="a" fill="#ffc658" />
  </BarChart>
</ResponsiveContainer>
```

Template 5: Half Pie (Gauge Chart)

```jsx
<ResponsiveContainer width="100%" height={250}>
  <PieChart>
    <Pie
      data={data}
      cx="50%"
      cy="50%"
      startAngle={180}
      endAngle={0}
      innerRadius={70}
      outerRadius={90}
      paddingAngle={3}
      dataKey="value"
      nameKey="name"
    >
      {data.map((entry, index) => (
        <Cell key={`cell-${index}`} fill={entry.color} />
      ))}
    </Pie>
    <Tooltip />
  </PieChart>
</ResponsiveContainer>
```

---

## Troubleshooting

Problem 1: Chart not showing

Solutions:

- Add ResponsiveContainer wrapper
- Check height is set (not 0 or undefined)
- Verify data array isn't empty
- Ensure component is imported correctly

Correct example:

```jsx
<ResponsiveContainer width="100%" height={300}>
  <LineChart data={data}>// chart content</LineChart>
</ResponsiveContainer>
```

Incorrect example (missing height):

```jsx
<ResponsiveContainer width="100%">
  <LineChart data={data}>// chart content</LineChart>
</ResponsiveContainer>
```

Problem 2: Legend overlapping chart

Solutions:

- Adjust cx and cy positions
- Set Legend width percentage
- Add margin to ChartType
- Use verticalAlign and align properly

Correct example:

```jsx
<Pie cx="35%" cy="50%" />
<Legend verticalAlign="middle" align="right" width="30%" />
<PieChart margin={{ top: 20, right: 30, left: 20, bottom: 20 }}>
```

Problem 3: Labels too big or small

Solutions:

- Customize label with fontSize
- Adjust wrapperStyle on Legend
- Modify contentStyle on Tooltip

Examples:

```jsx
// Custom label size
<label={{ fontSize: 12, fontWeight: 'bold' }} />

// Custom legend text size
<Legend wrapperStyle={{ fontSize: '12px' }} />
```

Problem 4: Chart not responsive

Solutions:

- Always use ResponsiveContainer
- Use percentages for cx and cy
- Avoid fixed widths on ChartType
- Set width="100%" on ResponsiveContainer

Problem 5: Colors not matching dark mode

Solutions:

- Create light and dark color arrays
- Use context to switch themes
- Apply colors to Cell components

Example:

```jsx
const { isDarkMode } = useDarkModeContext();
const colors = isDarkMode ? darkColors : lightColors;

{
  data.map((entry, index) => <Cell key={index} fill={colors[index]} />);
}
```

---

## Performance Tips

Tip 1: Memoize data preparation

```jsx
const preparedData = useMemo(() => prepareData(rawData), [rawData]);
```

Tip 2: Avoid inline functions in render

Incorrect:

```jsx
<Line onClick={() => console.log("clicked")} />
```

Correct:

```jsx
const handleClick = useCallback(() => console.log("clicked"), []);
<Line onClick={handleClick} />;
```

Tip 3: Use appropriate animation durations

```jsx
// Shorter animations for frequent updates
<Bar animationDuration={300} />

// Longer animations for initial load
<Bar animationDuration={1000} animationBegin={0} />
```

---

## Quick Copy-Paste Templates

Minimal Pie Chart

```jsx
<ResponsiveContainer width="100%" height={300}>
  <PieChart>
    <Pie data={data} dataKey="value" nameKey="name" fill="#8884d8" />
    <Tooltip />
  </PieChart>
</ResponsiveContainer>
```

Minimal Line Chart

```jsx
<ResponsiveContainer width="100%" height={300}>
  <LineChart data={data}>
    <XAxis dataKey="name" />
    <YAxis />
    <Tooltip />
    <Line type="monotone" dataKey="value" stroke="#8884d8" />
  </LineChart>
</ResponsiveContainer>
```

Minimal Bar Chart

```jsx
<ResponsiveContainer width="100%" height={300}>
  <BarChart data={data}>
    <XAxis dataKey="name" />
    <YAxis />
    <Tooltip />
    <Bar dataKey="value" fill="#8884d8" />
  </BarChart>
</ResponsiveContainer>
```

---

## Useful Resources

- Recharts Official Documentation: https://recharts.org/
- Recharts GitHub Repository: https://github.com/recharts/recharts
- Examples Gallery: https://recharts.org/en-US/examples

---

Created for React developers using Recharts
