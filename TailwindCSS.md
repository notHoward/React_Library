# Tailwind CSS - Complete Beginner's Guide

A comprehensive from-scratch guide to Tailwind CSS framework.

## Table of Contents

1. [What is Tailwind CSS](#what-is-tailwind-css)
2. [Installation](#installation)
3. [Core Concepts](#core-concepts)
4. [Layout](#layout)
5. [Spacing](#spacing)
6. [Typography](#typography)
7. [Backgrounds](#backgrounds)
8. [Borders](#borders)
9. [Effects](#effects)
10. [Flexbox](#flexbox)
11. [Grid](#grid)
12. [Responsive Design](#responsive-design)
13. [Dark Mode](#dark-mode)
14. [Hover, Focus, Active States](#hover-focus-active-states)
15. [Customization](#customization)
16. [Complete Examples](#complete-examples)
17. [API Reference](#api-reference)

---

## What is Tailwind CSS

Tailwind CSS is a **utility-first** CSS framework that provides low-level utility classes for building custom designs.

**Tailwind vs Traditional CSS:**

```css
/* Traditional CSS - write custom styles */
.button {
  background-color: blue;
  color: white;
  padding: 8px 16px;
  border-radius: 4px;
}
```

```html
<!-- Traditional CSS - use custom class -->
<button class="button">Click me</button>
```

```html
<!-- Tailwind CSS - use utility classes directly -->
<button class="bg-blue-500 text-white px-4 py-2 rounded">Click me</button>
```

**Why Tailwind:**

- No context switching between HTML and CSS files
- Smaller CSS bundle size (purge unused styles)
- Consistent design system
- Highly customizable

## Installation

### Method 1: Via CDN (Quick Start)

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://cdn.tailwindcss.com"></script>
  </head>
  <body>
    <h1 class="text-3xl font-bold underline">Hello Tailwind!</h1>
  </body>
</html>
```

### Method 2: Via npm (Recommended for React)

```bash
# Create React app
npx create-react-app my-app
cd my-app

# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer

# Initialize Tailwind
npx tailwindcss init -p
```

**Configure Tailwind:**

```javascript
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

**Add Tailwind to CSS:**

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Method 3: Via Vite

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

---

## Core Concepts

### Utility Classes

```html
<!-- Each class does ONE thing -->
<div class="bg-red-500 text-white p-4 m-2 rounded-lg shadow-lg">
  This div has multiple utilities
</div>

<!-- bg-red-500 → background color red -->
<!-- text-white → text color white -->
<!-- p-4 → padding: 1rem -->
<!-- m-2 → margin: 0.5rem -->
<!-- rounded-lg → border-radius: 0.5rem -->
<!-- shadow-lg → large box shadow -->
```

### The Scale System

Tailwind uses a default scale from 0 to 96 (and beyond):

```html
<!-- Spacing scale -->
<p class="p-0">padding: 0</p>
<!-- 0px -->
<p class="p-1">padding: 0.25rem</p>
<!-- 4px -->
<p class="p-2">padding: 0.5rem</p>
<!-- 8px -->
<p class="p-4">padding: 1rem</p>
<!-- 16px -->
<p class="p-8">padding: 2rem</p>
<!-- 32px -->
<p class="p-16">padding: 4rem</p>
<!-- 64px -->

<!-- Color scale -->
<div class="bg-red-50">Red 50 (lightest)</div>
<div class="bg-red-100">Red 100</div>
<div class="bg-red-200">Red 200</div>
<div class="bg-red-300">Red 300</div>
<div class="bg-red-400">Red 400</div>
<div class="bg-red-500">Red 500 (default)</div>
<div class="bg-red-600">Red 600</div>
<div class="bg-red-700">Red 700</div>
<div class="bg-red-800">Red 800</div>
<div class="bg-red-900">Red 900 (darkest)</div>
```

---

## Layout

### Display

```html
<!-- Block vs Inline -->
<div class="block">This is a block element</div>
<span class="inline-block">Inline-block element</span>
<span class="inline">Inline element</span>

<!-- Flex -->
<div class="flex">Flex container</div>

<!-- Grid -->
<div class="grid">Grid container</div>

<!-- Hide/Show -->
<div class="hidden">Hidden element</div>
<div class="visible">Visible element</div>

<!-- Inline flex/grid -->
<div class="inline-flex">Inline flex container</div>
<div class="inline-grid">Inline grid container</div>
```

### Position

```html
<!-- Position types -->
<div class="static">Static position (default)</div>
<div class="relative">Relative position</div>
<div class="absolute">Absolute position</div>
<div class="fixed">Fixed position</div>
<div class="sticky">Sticky position</div>

<!-- Positioning coordinates -->
<div class="absolute top-0 left-0">Top left corner</div>
<div
  class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2"
>
  Centered
</div>
<div class="fixed bottom-4 right-4">Bottom right corner</div>

<!-- Z-index -->
<div class="z-0">Z-index 0</div>
<div class="z-10">Z-index 10</div>
<div class="z-20">Z-index 20</div>
<div class="z-50">Z-index 50</div>
<div class="z-auto">Auto z-index</div>
```

### Overflow

```html
<!-- Overflow control -->
<div class="overflow-auto">Auto scroll if needed</div>
<div class="overflow-hidden">Hidden overflow</div>
<div class="overflow-scroll">Always show scrollbar</div>
<div class="overflow-visible">Visible overflow</div>

<!-- Specific axes -->
<div class="overflow-x-auto">Horizontal scroll</div>
<div class="overflow-y-auto">Vertical scroll</div>
```

---

## Spacing

### Padding

```html
<!-- All sides -->
<div class="p-0">Padding 0</div>
<div class="p-1">Padding 0.25rem</div>
<div class="p-2">Padding 0.5rem</div>
<div class="p-4">Padding 1rem</div>
<div class="p-8">Padding 2rem</div>

<!-- Specific sides -->
<div class="pt-4">Padding top</div>
<div class="pr-4">Padding right</div>
<div class="pb-4">Padding bottom</div>
<div class="pl-4">Padding left</div>

<!-- Horizontal and vertical -->
<div class="px-4">Padding left and right</div>
<div class="py-4">Padding top and bottom</div>
```

### Margin

```html
<!-- All sides -->
<div class="m-0">Margin 0</div>
<div class="m-1">Margin 0.25rem</div>
<div class="m-2">Margin 0.5rem</div>
<div class="m-4">Margin 1rem</div>
<div class="m-8">Margin 2rem</div>

<!-- Specific sides -->
<div class="mt-4">Margin top</div>
<div class="mr-4">Margin right</div>
<div class="mb-4">Margin bottom</div>
<div class="ml-4">Margin left</div>

<!-- Auto margin -->
<div class="mx-auto">Center horizontally</div>
<div class="my-auto">Center vertically (in flex)</div>
<div class="ml-auto">Push to right (in flex)</div>
<div class="mr-auto">Push to left (in flex)</div>
```

### Space Between

```html
<!-- For flex/grid children -->
<div class="flex space-x-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>

<div class="flex flex-col space-y-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

---

## Typography

### Font Size

```html
<!-- Text sizes -->
<p class="text-xs">Extra small text (0.75rem)</p>
<p class="text-sm">Small text (0.875rem)</p>
<p class="text-base">Base text (1rem)</p>
<p class="text-lg">Large text (1.125rem)</p>
<p class="text-xl">Extra large (1.25rem)</p>
<p class="text-2xl">2XL (1.5rem)</p>
<p class="text-3xl">3XL (1.875rem)</p>
<p class="text-4xl">4XL (2.25rem)</p>
<p class="text-5xl">5XL (3rem)</p>
<p class="text-6xl">6XL (3.75rem)</p>
<p class="text-7xl">7XL (4.5rem)</p>
<p class="text-8xl">8XL (6rem)</p>
<p class="text-9xl">9XL (8rem)</p>
```

### Font Weight

```html
<p class="font-thin">Thin (100)</p>
<p class="font-extralight">Extra light (200)</p>
<p class="font-light">Light (300)</p>
<p class="font-normal">Normal (400)</p>
<p class="font-medium">Medium (500)</p>
<p class="font-semibold">Semibold (600)</p>
<p class="font-bold">Bold (700)</p>
<p class="font-extrabold">Extra bold (800)</p>
<p class="font-black">Black (900)</p>
```

### Text Color

```html
<!-- Basic colors -->
<p class="text-gray-500">Gray text</p>
<p class="text-red-500">Red text</p>
<p class="text-blue-500">Blue text</p>
<p class="text-green-500">Green text</p>
<p class="text-yellow-500">Yellow text</p>
<p class="text-purple-500">Purple text</p>
<p class="text-pink-500">Pink text</p>
<p class="text-indigo-500">Indigo text</p>

<!-- White and black -->
<p class="text-white bg-black">White text on black</p>
<p class="text-black bg-white">Black text on white</p>

<!-- Opacity -->
<p class="text-black/50">50% opacity black</p>
<p class="text-red-500/75">75% opacity red</p>
```

### Text Alignment

```html
<p class="text-left">Left aligned text</p>
<p class="text-center">Center aligned text</p>
<p class="text-right">Right aligned text</p>
<p class="text-justify">Justified text</p>
```

### Text Decoration

```html
<p class="underline">Underlined text</p>
<p class="line-through">Strikethrough text</p>
<p class="overline">Overline text</p>
<p class="no-underline">No decoration</p>

<!-- Decoration color -->
<p class="underline decoration-red-500">Red underline</p>
<p class="line-through decoration-blue-500">Blue strikethrough</p>

<!-- Decoration style -->
<p class="underline decoration-dotted">Dotted underline</p>
<p class="underline decoration-dashed">Dashed underline</p>
<p class="underline decoration-wavy">Wavy underline</p>
```

### Text Transform

```html
<p class="uppercase">Uppercase text</p>
<p class="lowercase">Lowercase text</p>
<p class="capitalize">capitalized text</p>
<p class="normal-case">Normal case</p>
```

### Line Height

```html
<p class="leading-none">Line height 1</p>
<p class="leading-tight">Line height 1.25</p>
<p class="leading-snug">Line height 1.375</p>
<p class="leading-normal">Line height 1.5</p>
<p class="leading-relaxed">Line height 1.625</p>
<p class="leading-loose">Line height 2</p>

<!-- Fixed line heights -->
<p class="leading-3">Line height 0.75rem</p>
<p class="leading-5">Line height 1.25rem</p>
<p class="leading-7">Line height 1.75rem</p>
```

### Letter Spacing

```html
<p class="tracking-tighter">Tighter letter spacing</p>
<p class="tracking-tight">Tight letter spacing</p>
<p class="tracking-normal">Normal letter spacing</p>
<p class="tracking-wide">Wide letter spacing</p>
<p class="tracking-wider">Wider letter spacing</p>
<p class="tracking-widest">Widest letter spacing</p>
```

---

## Backgrounds

### Background Color

```html
<div class="bg-red-500">Red background</div>
<div class="bg-blue-500">Blue background</div>
<div class="bg-green-500">Green background</div>
<div class="bg-gray-100">Light gray background</div>
<div class="bg-gray-900">Dark gray background</div>

<!-- Opacity -->
<div class="bg-black/50">50% opacity black</div>
<div class="bg-red-500/25">25% opacity red</div>
```

### Gradient Backgrounds

```html
<!-- Linear gradient -->
<div class="bg-gradient-to-r from-blue-500 to-purple-500">
  Left to right gradient
</div>

<div class="bg-gradient-to-t from-yellow-400 to-red-500">
  Bottom to top gradient
</div>

<div class="bg-gradient-to-tr from-green-400 to-blue-500">
  Top-right diagonal gradient
</div>

<!-- Multiple stops -->
<div class="bg-gradient-to-r from-red-500 via-yellow-500 to-green-500">
  Rainbow gradient
</div>
```

### Background Image

```html
<!-- Set background image -->
<div class="bg-cover bg-center" style="background-image: url('image.jpg')">
  Content
</div>

<!-- Background size -->
<div class="bg-auto">Auto size</div>
<div class="bg-cover">Cover entire area</div>
<div class="bg-contain">Contain within area</div>

<!-- Background position -->
<div class="bg-top">Top</div>
<div class="bg-center">Center</div>
<div class="bg-bottom">Bottom</div>
<div class="bg-left">Left</div>
<div class="bg-right">Right</div>

<!-- Background repeat -->
<div class="bg-repeat">Repeat</div>
<div class="bg-no-repeat">No repeat</div>
<div class="bg-repeat-x">Repeat horizontally</div>
<div class="bg-repeat-y">Repeat vertically</div>
```

---

## Borders

### Border Width

```html
<!-- All sides -->
<div class="border">Border on all sides</div>
<div class="border-0">No border</div>
<div class="border-2">2px border</div>
<div class="border-4">4px border</div>
<div class="border-8">8px border</div>

<!-- Specific sides -->
<div class="border-t">Top border only</div>
<div class="border-r">Right border only</div>
<div class="border-b">Bottom border only</div>
<div class="border-l">Left border only</div>
```

### Border Color

```html
<div class="border border-red-500">Red border</div>
<div class="border border-blue-500">Blue border</div>
<div class="border-t-2 border-green-500">Green top border</div>

<!-- Border with opacity -->
<div class="border border-black/50">50% opacity border</div>
```

### Border Radius

```html
<!-- All corners -->
<div class="rounded-none">No radius</div>
<div class="rounded-sm">Small radius</div>
<div class="rounded">Default radius</div>
<div class="rounded-md">Medium radius</div>
<div class="rounded-lg">Large radius</div>
<div class="rounded-xl">Extra large radius</div>
<div class="rounded-2xl">2XL radius</div>
<div class="rounded-3xl">3XL radius</div>
<div class="rounded-full">Full circle/oval</div>

<!-- Specific corners -->
<div class="rounded-t-lg">Top corners only</div>
<div class="rounded-b-lg">Bottom corners only</div>
<div class="rounded-l-lg">Left corners only</div>
<div class="rounded-r-lg">Right corners only</div>
<div class="rounded-tl-lg">Top-left only</div>
<div class="rounded-tr-lg">Top-right only</div>
<div class="rounded-bl-lg">Bottom-left only</div>
<div class="rounded-br-lg">Bottom-right only</div>
```

---

## Effects

### Box Shadow

```html
<div class="shadow-sm">Small shadow</div>
<div class="shadow">Default shadow</div>
<div class="shadow-md">Medium shadow</div>
<div class="shadow-lg">Large shadow</div>
<div class="shadow-xl">Extra large shadow</div>
<div class="shadow-2xl">2XL shadow</div>
<div class="shadow-inner">Inner shadow</div>
<div class="shadow-none">No shadow</div>

<!-- Colored shadow -->
<div class="shadow-lg shadow-red-500/50">Red tinted shadow</div>
```

### Opacity

```html
<div class="opacity-0">0% opacity (invisible)</div>
<div class="opacity-25">25% opacity</div>
<div class="opacity-50">50% opacity</div>
<div class="opacity-75">75% opacity</div>
<div class="opacity-100">100% opacity</div>
```

### Blur

```html
<img class="blur-none" src="image.jpg" />
<img class="blur-sm" src="image.jpg" />
<img class="blur" src="image.jpg" />
<img class="blur-md" src="image.jpg" />
<img class="blur-lg" src="image.jpg" />
<img class="blur-xl" src="image.jpg" />
<img class="blur-2xl" src="image.jpg" />
<img class="blur-3xl" src="image.jpg" />
```

### Transition

```html
<button class="bg-blue-500 hover:bg-blue-700 transition duration-300">
  Smooth transition on hover
</button>

<!-- Transition properties -->
<div class="transition-all">Transition all properties</div>
<div class="transition-colors">Transition colors only</div>
<div class="transition-opacity">Transition opacity only</div>
<div class="transition-transform">Transition transform only</div>

<!-- Transition duration -->
<div class="duration-75">75ms</div>
<div class="duration-100">100ms</div>
<div class="duration-150">150ms</div>
<div class="duration-200">200ms</div>
<div class="duration-300">300ms</div>
<div class="duration-500">500ms</div>
<div class="duration-700">700ms</div>
<div class="duration-1000">1000ms</div>

<!-- Transition timing -->
<div class="ease-linear">Linear</div>
<div class="ease-in">Ease in</div>
<div class="ease-out">Ease out</div>
<div class="ease-in-out">Ease in-out</div>

<!-- Transition delay -->
<div class="delay-75">75ms delay</div>
<div class="delay-100">100ms delay</div>
<div class="delay-150">150ms delay</div>
<div class="delay-200">200ms delay</div>
<div class="delay-300">300ms delay</div>
```

### Transform

```html
<!-- Scale -->
<div class="scale-50">Scale to 50%</div>
<div class="scale-75">Scale to 75%</div>
<div class="scale-90">Scale to 90%</div>
<div class="scale-100">Scale to 100%</div>
<div class="scale-110">Scale to 110%</div>
<div class="scale-125">Scale to 125%</div>
<div class="scale-150">Scale to 150%</div>

<!-- Rotate -->
<div class="rotate-0">No rotation</div>
<div class="rotate-45">45 degrees</div>
<div class="rotate-90">90 degrees</div>
<div class="rotate-180">180 degrees</div>
<div class="-rotate-45">Negative 45 degrees</div>

<!-- Translate -->
<div class="translate-x-0">No X movement</div>
<div class="translate-x-4">Move right 1rem</div>
<div class="translate-x-8">Move right 2rem</div>
<div class="-translate-x-4">Move left 1rem</div>
<div class="translate-y-4">Move down 1rem</div>
<div class="-translate-y-4">Move up 1rem</div>

<!-- Combine transforms -->
<div class="transform scale-110 rotate-45">Scaled and rotated</div>
```

---

## Flexbox

### Flex Container

```html
<!-- Display flex -->
<div class="flex">Flex container (row)</div>
<div class="inline-flex">Inline flex container</div>

<!-- Direction -->
<div class="flex flex-row">Row direction (default)</div>
<div class="flex flex-row-reverse">Reverse row direction</div>
<div class="flex flex-col">Column direction</div>
<div class="flex flex-col-reverse">Reverse column direction</div>
```

### Justify Content

```html
<div class="flex justify-start">Items at start</div>
<div class="flex justify-end">Items at end</div>
<div class="flex justify-center">Items centered</div>
<div class="flex justify-between">Space between items</div>
<div class="flex justify-around">Space around items</div>
<div class="flex justify-evenly">Evenly spaced items</div>
```

### Align Items

```html
<div class="flex items-start">Items at top</div>
<div class="flex items-end">Items at bottom</div>
<div class="flex items-center">Items centered vertically</div>
<div class="flex items-baseline">Items at baseline</div>
<div class="flex items-stretch">Items stretch (default)</div>
```

### Flex Wrap

```html
<div class="flex flex-wrap">Wrap items</div>
<div class="flex flex-wrap-reverse">Reverse wrap</div>
<div class="flex flex-nowrap">No wrap (default)</div>
```

### Flex Child Properties

```html
<!-- Grow -->
<div class="flex">
  <div class="flex-grow">Grows to fill space</div>
  <div class="flex-grow-0">Does not grow</div>
</div>

<!-- Shrink -->
<div class="flex">
  <div class="flex-shrink">Can shrink</div>
  <div class="flex-shrink-0">Does not shrink</div>
</div>

<!-- Basis -->
<div class="flex">
  <div class="flex-basis-1/2">50% width</div>
  <div class="flex-basis-1/3">33.33% width</div>
</div>

<!-- Order -->
<div class="flex">
  <div class="order-1">Second (order 1)</div>
  <div class="order-0">First (order 0)</div>
  <div class="order-2">Third (order 2)</div>
</div>
```

---

## Grid

### Grid Container

```html
<!-- Display grid -->
<div class="grid">Grid container</div>
<div class="inline-grid">Inline grid container</div>

<!-- Columns -->
<div class="grid grid-cols-1">1 column</div>
<div class="grid grid-cols-2">2 columns</div>
<div class="grid grid-cols-3">3 columns</div>
<div class="grid grid-cols-4">4 columns</div>
<div class="grid grid-cols-6">6 columns</div>
<div class="grid grid-cols-12">12 columns</div>

<!-- Rows -->
<div class="grid grid-rows-1">1 row</div>
<div class="grid grid-rows-2">2 rows</div>
<div class="grid grid-rows-3">3 rows</div>
```

### Column Span

```html
<div class="grid grid-cols-3">
  <div class="col-span-1">Spans 1 column</div>
  <div class="col-span-2">Spans 2 columns</div>
  <div class="col-span-3">Spans 3 columns</div>
</div>

<!-- Start and end -->
<div class="grid grid-cols-6">
  <div class="col-start-2 col-span-4">Starts at column 2, spans 4</div>
</div>
```

### Gap

```html
<!-- Gap between items -->
<div class="grid gap-4">Gap 1rem</div>
<div class="grid gap-x-4">Horizontal gap only</div>
<div class="grid gap-y-4">Vertical gap only</div>

<!-- Specific gaps -->
<div class="grid gap-2">Small gap</div>
<div class="grid gap-4">Medium gap</div>
<div class="grid gap-8">Large gap</div>
```

---

## Responsive Design

### Breakpoints

Tailwind uses mobile-first breakpoints:

```html
<!-- Mobile (default) -->
<div class="text-sm">Mobile text size</div>

<!-- sm: 640px and up -->
<div class="sm:text-base">Tablet text size</div>

<!-- md: 768px and up -->
<div class="md:text-lg">Small laptop text size</div>

<!-- lg: 1024px and up -->
<div class="lg:text-xl">Desktop text size</div>

<!-- xl: 1280px and up -->
<div class="xl:text-2xl">Large desktop text size</div>

<!-- 2xl: 1536px and up -->
<div class="2xl:text-3xl">Extra large text size</div>
```

### Responsive Examples

```html
<!-- Different layouts at different screen sizes -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>

<!-- Responsive padding -->
<div class="p-4 md:p-6 lg:p-8">Padding increases on larger screens</div>

<!-- Responsive text size -->
<h1 class="text-2xl md:text-4xl lg:text-6xl">Responsive Heading</h1>

<!-- Hide on mobile, show on desktop -->
<div class="hidden md:block">Hidden on mobile, visible on desktop</div>

<!-- Show on mobile, hide on desktop -->
<div class="block md:hidden">Visible on mobile, hidden on desktop</div>

<!-- Responsive flex direction -->
<div class="flex flex-col md:flex-row">
  <div>Column on mobile, row on desktop</div>
</div>
```

---

## Dark Mode

### Setup

```javascript
// tailwind.config.js
module.exports = {
  darkMode: "class", // or 'media' for system preference
  // ...
};
```

### Usage

```html
<!-- Light mode background -->
<div class="bg-white dark:bg-gray-800">
  <!-- White in light mode, dark gray in dark mode -->
  <h1 class="text-black dark:text-white">
    Black in light mode, white in dark mode
  </h1>
</div>

<!-- Toggle dark mode -->
<button onclick="document.documentElement.classList.toggle('dark')">
  Toggle Dark Mode
</button>

<!-- Example components -->
<div class="bg-gray-50 dark:bg-gray-900 p-6 rounded-lg shadow">
  <h2 class="text-gray-900 dark:text-white text-xl font-bold">Card Title</h2>
  <p class="text-gray-600 dark:text-gray-300 mt-2">
    Card content that adapts to dark mode
  </p>
  <button
    class="mt-4 bg-blue-500 dark:bg-blue-600 text-white px-4 py-2 rounded"
  >
    Button
  </button>
</div>
```

---

## Hover, Focus, Active States

### Hover State

```html
<!-- Background color change on hover -->
<button class="bg-blue-500 hover:bg-blue-700">Hover me</button>

<!-- Scale on hover -->
<div class="hover:scale-110 transition-transform">Grows on hover</div>

<!-- Shadow on hover -->
<div class="shadow hover:shadow-lg transition-shadow">
  Shadow grows on hover
</div>

<!-- Multiple hover effects -->
<button class="bg-blue-500 hover:bg-blue-700 hover:scale-105 transition-all">
  Multiple hover effects
</button>
```

### Focus State

```html
<!-- Focus ring -->
<input class="focus:ring-2 focus:ring-blue-500 focus:outline-none" />

<!-- Focus border -->
<input class="border focus:border-blue-500" />

<!-- Focus background -->
<input class="bg-gray-100 focus:bg-white" />
```

### Active State

```html
<button class="bg-blue-500 active:bg-blue-800">
  Click me (changes while clicking)
</button>
```

### Group Hover

```html
<!-- Parent hover affects children -->
<div class="group hover:bg-gray-100 p-4">
  <h3 class="group-hover:text-blue-500">Title changes on parent hover</h3>
  <p class="group-hover:text-gray-600">Description also changes</p>
  <button class="group-hover:bg-blue-500">Button changes too</button>
</div>
```

### First/Last/Even/Odd

```html
<!-- List items -->
<ul>
  <li class="first:mt-0">First item</li>
  <li class="last:mb-0">Last item</li>
  <li class="even:bg-gray-100">Even items have background</li>
  <li class="odd:bg-white">Odd items are white</li>
</ul>
```

---

## Customization

### tailwind.config.js

```javascript
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    // Extend default theme
    extend: {
      // Custom colors
      colors: {
        brand: {
          50: "#eff6ff",
          100: "#dbeafe",
          200: "#bfdbfe",
          300: "#93c5fd",
          400: "#60a5fa",
          500: "#3b82f6",
          600: "#2563eb",
          700: "#1d4ed8",
          800: "#1e40af",
          900: "#1e3a8a",
        },
        primary: "#ff6347",
        secondary: "#4a90e2",
      },

      // Custom spacing
      spacing: {
        128: "32rem",
        144: "36rem",
      },

      // Custom font sizes
      fontSize: {
        xxs: ["0.625rem", { lineHeight: "0.75rem" }],
      },

      // Custom animations
      animation: {
        "spin-slow": "spin 3s linear infinite",
        "bounce-slow": "bounce 2s infinite",
      },

      // Custom keyframes
      keyframes: {
        wiggle: {
          "0%, 100%": { transform: "rotate(-3deg)" },
          "50%": { transform: "rotate(3deg)" },
        },
      },

      // Custom screens
      screens: {
        xs: "475px",
        "3xl": "1792px",
      },

      // Custom container
      container: {
        center: true,
        padding: "2rem",
      },
    },
  },
  plugins: [],
};
```

### Using Custom Classes

```html
<!-- Using custom colors -->
<div class="bg-primary text-white">Custom primary color</div>

<!-- Using custom spacing -->
<div class="p-128">Custom 32rem padding</div>

<!-- Using custom font size -->
<p class="text-xxs">Extra extra small text</p>

<!-- Using custom animation -->
<div class="animate-wiggle">Wiggles on hover</div>

<!-- Using custom breakpoints -->
<div class="xs:text-center 3xl:text-right">
  Responsive with custom breakpoints
</div>
```

---

## Complete Examples

### Example 1: Modern Card Component

```html
<div
  class="max-w-sm rounded overflow-hidden shadow-lg bg-white dark:bg-gray-800 transition-all hover:shadow-2xl"
>
  <img class="w-full h-48 object-cover" src="/image.jpg" alt="Card image" />
  <div class="px-6 py-4">
    <div class="font-bold text-xl mb-2 text-gray-900 dark:text-white">
      Card Title
    </div>
    <p class="text-gray-700 dark:text-gray-300 text-base">
      This is a description of the card content. It provides details about what
      the card represents.
    </p>
  </div>
  <div class="px-6 pt-4 pb-6">
    <span
      class="inline-block bg-gray-200 dark:bg-gray-700 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 dark:text-gray-300 mr-2 mb-2"
    >
      #tag1
    </span>
    <span
      class="inline-block bg-gray-200 dark:bg-gray-700 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 dark:text-gray-300 mr-2 mb-2"
    >
      #tag2
    </span>
    <button
      class="mt-4 w-full bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded transition-colors"
    >
      Learn More
    </button>
  </div>
</div>
```

### Example 2: Navigation Bar

```html
<nav class="bg-white dark:bg-gray-900 shadow-lg fixed w-full top-0 z-50">
  <div class="container mx-auto px-4">
    <div class="flex justify-between items-center h-16">
      <!-- Logo -->
      <div class="text-xl font-bold text-gray-800 dark:text-white">MyApp</div>

      <!-- Desktop Menu -->
      <div class="hidden md:flex space-x-8">
        <a
          href="#"
          class="text-gray-600 dark:text-gray-300 hover:text-blue-500 dark:hover:text-blue-400 transition"
        >
          Home
        </a>
        <a
          href="#"
          class="text-gray-600 dark:text-gray-300 hover:text-blue-500 dark:hover:text-blue-400 transition"
        >
          About
        </a>
        <a
          href="#"
          class="text-gray-600 dark:text-gray-300 hover:text-blue-500 dark:hover:text-blue-400 transition"
        >
          Services
        </a>
        <a
          href="#"
          class="text-gray-600 dark:text-gray-300 hover:text-blue-500 dark:hover:text-blue-400 transition"
        >
          Contact
        </a>
      </div>

      <!-- Mobile Menu Button -->
      <button
        class="md:hidden text-gray-600 dark:text-gray-300 focus:outline-none"
      >
        <svg
          class="w-6 h-6"
          fill="none"
          stroke="currentColor"
          viewBox="0 0 24 24"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M4 6h16M4 12h16M4 18h16"
          ></path>
        </svg>
      </button>
    </div>
  </div>
</nav>

<!-- Spacer to push content below fixed navbar -->
<div class="h-16"></div>
```

### Example 3: Hero Section

```html
<section class="bg-gradient-to-r from-blue-600 to-purple-600 text-white">
  <div class="container mx-auto px-4 py-24 text-center">
    <h1 class="text-4xl md:text-6xl font-bold mb-4 animate-fade-in">
      Welcome to Our Platform
    </h1>
    <p class="text-xl md:text-2xl mb-8 opacity-90">
      The best solution for your business needs
    </p>
    <div class="space-x-4">
      <button
        class="bg-white text-blue-600 px-8 py-3 rounded-lg font-semibold hover:bg-gray-100 transition"
      >
        Get Started
      </button>
      <button
        class="border-2 border-white px-8 py-3 rounded-lg font-semibold hover:bg-white hover:text-blue-600 transition"
      >
        Learn More
      </button>
    </div>
  </div>
</section>
```

### Example 4: Responsive Grid Layout

```html
<div class="container mx-auto px-4 py-8">
  <div
    class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6"
  >
    <!-- Card 1 -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow-md overflow-hidden">
      <div class="h-48 bg-gradient-to-r from-pink-500 to-red-500"></div>
      <div class="p-4">
        <h3 class="font-bold text-lg mb-2">Card Title 1</h3>
        <p class="text-gray-600 dark:text-gray-400 text-sm">Description here</p>
      </div>
    </div>

    <!-- Card 2 -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow-md overflow-hidden">
      <div class="h-48 bg-gradient-to-r from-blue-500 to-indigo-500"></div>
      <div class="p-4">
        <h3 class="font-bold text-lg mb-2">Card Title 2</h3>
        <p class="text-gray-600 dark:text-gray-400 text-sm">Description here</p>
      </div>
    </div>

    <!-- Add more cards as needed -->
  </div>
</div>
```

### Example 5: Form with Validation

```html
<div
  class="max-w-md mx-auto bg-white dark:bg-gray-800 rounded-lg shadow-md p-6"
>
  <h2 class="text-2xl font-bold text-gray-900 dark:text-white mb-6">
    Contact Us
  </h2>

  <form class="space-y-4">
    <div>
      <label class="block text-gray-700 dark:text-gray-300 mb-2"> Name </label>
      <input
        type="text"
        class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600 dark:text-white"
        placeholder="Your name"
      />
    </div>

    <div>
      <label class="block text-gray-700 dark:text-gray-300 mb-2"> Email </label>
      <input
        type="email"
        class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600 dark:text-white"
        placeholder="your@email.com"
      />
    </div>

    <div>
      <label class="block text-gray-700 dark:text-gray-300 mb-2">
        Message
      </label>
      <textarea
        rows="4"
        class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none dark:bg-gray-700 dark:border-gray-600 dark:text-white"
        placeholder="Your message"
      ></textarea>
    </div>

    <button
      class="w-full bg-blue-500 hover:bg-blue-700 text-white font-bold py-3 px-4 rounded-lg transition-colors"
    >
      Send Message
    </button>
  </form>
</div>
```

---

## API Reference

### Common Utility Classes

| Category   | Prefix                                              | Example                       |
| ---------- | --------------------------------------------------- | ----------------------------- |
| Padding    | `p-`, `pt-`, `pr-`, `pb-`, `pl-`, `px-`, `py-`      | `p-4`, `px-2`                 |
| Margin     | `m-`, `mt-`, `mr-`, `mb-`, `ml-`, `mx-`, `my-`      | `m-4`, `mx-auto`              |
| Width      | `w-`                                                | `w-full`, `w-1/2`             |
| Height     | `h-`                                                | `h-screen`, `h-64`            |
| Text       | `text-`                                             | `text-center`, `text-red-500` |
| Background | `bg-`                                               | `bg-blue-500`, `bg-white`     |
| Border     | `border-`, `rounded-`                               | `border`, `rounded-lg`        |
| Flexbox    | `flex`, `justify-`, `items-`                        | `flex`, `justify-center`      |
| Grid       | `grid`, `grid-cols-`                                | `grid`, `grid-cols-3`         |
| Position   | `static`, `relative`, `absolute`, `fixed`, `sticky` | `absolute`, `top-0`           |
| Display    | `block`, `inline`, `hidden`                         | `block`, `hidden`             |

### Breakpoints

| Breakpoint | Min Width |
| ---------- | --------- |
| `sm`       | 640px     |
| `md`       | 768px     |
| `lg`       | 1024px    |
| `xl`       | 1280px    |
| `2xl`      | 1536px    |

### State Variants

| Variant        | Description                  |
| -------------- | ---------------------------- |
| `hover:`       | On hover                     |
| `focus:`       | On focus                     |
| `active:`      | On active (click)            |
| `group-hover:` | When parent group is hovered |
| `dark:`        | In dark mode                 |
| `first:`       | First child                  |
| `last:`        | Last child                   |
| `even:`        | Even children                |
| `odd:`         | Odd children                 |

## Troubleshooting

Problem 1: Tailwind classes not working

Solutions:

- Check content path in config
- Restart dev server
- Clear browser cache

Problem 2: Styles not updating

Solutions:

- Run `npm run build` to rebuild
- Check if classes are purged
- Verify Tailwind is imported

Problem 3: Dark mode not working

Solutions:

- Set `darkMode: 'class'` in config
- Add `dark` class to html element
- Use `dark:` variant on classes

## Useful Resources

- Tailwind CSS Docs: https://tailwindcss.com/docs
- Tailwind Playground: https://play.tailwindcss.com/
- Tailwind Cheatsheet: https://nerdcave.com/tailwind-cheat-sheet

---

Created for beginners learning Tailwind CSS
