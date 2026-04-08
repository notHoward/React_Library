# Framer Motion - Complete Beginner's Guide

A comprehensive from-scratch guide to Framer Motion animations.

## Table of Contents

1. [What is Framer Motion](#what-is-framer-motion)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [Core Concepts](#core-concepts)
5. [Basic Animations](#basic-animations)
6. [Variants](#variants)
7. [Gestures](#gestures)
8. [Transitions](#transitions)
9. [Keyframes](#keyframes)
10. [Exit Animations](#exit-animations)
11. [Scroll Animations](#scroll-animations)
12. [Drag and Drop](#drag-and-drop)
13. [SVG Animation](#svg-animation)
14. [AnimatePresence](#animatepresence)
15. [Layout Animations](#layout-animations)
16. [Complete Examples](#complete-examples)
17. [API Reference](#api-reference)

---

## What is Framer Motion

Framer Motion is a production-ready animation library for React that makes creating smooth, powerful animations simple.

**What Framer Motion does:**

- Animates components entering/exiting the DOM
- Handles gestures (hover, tap, drag)
- Creates scroll-triggered animations
- Animates layout changes
- Provides spring and physics-based animations

**Framer Motion vs CSS Animations:**

```jsx
// CSS animations - need CSS files, class names
<div className="fade-in">Hello</div>

// Framer Motion - declarative, in component
<motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
  Hello
</motion.div>
```

## Installation

```bash
npm install framer-motion
```

## Basic Setup

### Import and First Animation

```jsx
import { motion } from "framer-motion";

function BasicAnimation() {
  return (
    <motion.div
      initial={{ opacity: 0, y: -50 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.5 }}
    >
      Hello World!
    </motion.div>
  );
}
```

### Understanding motion Components

```jsx
// motion can wrap any HTML element
<motion.div />
<motion.span />
<motion.h1 />
<motion.button />
<motion.img />
<motion.ul />
<motion.li />
<motion.section />

// motion can also wrap custom components (with forwardRef)
const MyComponent = forwardRef((props, ref) => (
  <div ref={ref} {...props} />
));
<motion.custom component={MyComponent} />
```

---

## Core Concepts

### The Animation Lifecycle

```jsx
<motion.div
  initial={{ opacity: 0 }} // Starting state (mount)
  animate={{ opacity: 1 }} // Ending state (mount)
  exit={{ opacity: 0 }} // State when unmounting
  transition={{ duration: 1 }} // Animation settings
>
  Content
</motion.div>
```

### Animation Properties

```jsx
// Transform properties
<motion.div
  animate={{
    x: 100,           // Move right 100px
    y: 50,            // Move down 50px
    scale: 1.5,       // Scale to 1.5x
    rotate: 45,       // Rotate 45 degrees
    skewX: 10,        // Skew horizontally
    skewY: 5,         // Skew vertically
  }}
/>

// Opacity and visibility
<motion.div
  animate={{
    opacity: 0.5,     // Fade to 50%
    visibility: 'hidden',
  }}
/>

// Dimensions
<motion.div
  animate={{
    width: '50%',
    height: 200,
    borderRadius: '50%',
  }}
/>

// Colors
<motion.div
  animate={{
    backgroundColor: '#ff0000',
    color: '#ffffff',
    borderColor: '#00ff00',
  }}
/>

// Multiple properties together
<motion.div
  animate={{
    x: 100,
    scale: 1.2,
    opacity: 0.8,
    rotate: 180,
    backgroundColor: '#ff0000',
  }}
/>
```

---

## Basic Animations

### Simple Fade In

```jsx
function FadeIn() {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 1 }}
    >
      This fades in over 1 second
    </motion.div>
  );
}
```

### Slide In

```jsx
function SlideIn() {
  return (
    <motion.div
      initial={{ x: -100, opacity: 0 }}
      animate={{ x: 0, opacity: 1 }}
      transition={{ duration: 0.5 }}
    >
      Slides in from left
    </motion.div>
  );
}
```

### Scale Animation

```jsx
function ScaleAnimation() {
  return (
    <motion.div
      initial={{ scale: 0 }}
      animate={{ scale: 1 }}
      transition={{ duration: 0.5, type: "spring" }}
    >
      Grows from nothing
    </motion.div>
  );
}
```

### Rotate Animation

```jsx
function RotateAnimation() {
  return (
    <motion.div
      animate={{ rotate: 360 }}
      transition={{ duration: 2, repeat: Infinity }}
    >
      Spins continuously
    </motion.div>
  );
}
```

### Sequence Animation (stagger)

```jsx
function SequenceAnimation() {
  const container = {
    hidden: { opacity: 0 },
    show: {
      opacity: 1,
      transition: {
        staggerChildren: 0.2, // Each child animates 0.2s apart
      },
    },
  };

  const item = {
    hidden: { opacity: 0, x: -20 },
    show: { opacity: 1, x: 0 },
  };

  return (
    <motion.ul variants={container} initial="hidden" animate="show">
      <motion.li variants={item}>Item 1</motion.li>
      <motion.li variants={item}>Item 2</motion.li>
      <motion.li variants={item}>Item 3</motion.li>
    </motion.ul>
  );
}
```

---

## Variants

Variants allow you to define animation states and reuse them.

### Basic Variants

```jsx
const variants = {
  hidden: { opacity: 0, y: 50 },
  visible: { opacity: 1, y: 0 },
};

function VariantExample() {
  return (
    <motion.div
      variants={variants}
      initial="hidden"
      animate="visible"
      transition={{ duration: 0.5 }}
    >
      Animated with variants
    </motion.div>
  );
}
```

### Parent-Child Variants

```jsx
const parentVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.3,
      delayChildren: 0.2,
    },
  },
};

const childVariants = {
  hidden: { y: 20, opacity: 0 },
  visible: { y: 0, opacity: 1 },
};

function StaggeredList() {
  return (
    <motion.ul variants={parentVariants} initial="hidden" animate="visible">
      <motion.li variants={childVariants}>Item 1</motion.li>
      <motion.li variants={childVariants}>Item 2</motion.li>
      <motion.li variants={childVariants}>Item 3</motion.li>
    </motion.ul>
  );
}
```

### Dynamic Variants

```jsx
const variants = {
  hidden: (custom) => ({
    opacity: 0,
    x: custom ? 100 : -100,
  }),
  visible: (custom) => ({
    opacity: 1,
    x: 0,
    transition: { delay: custom ? 0.5 : 0 },
  }),
};

function DynamicVariants() {
  return (
    <>
      <motion.div
        variants={variants}
        initial="hidden"
        animate="visible"
        custom={false}
      >
        Slides from left
      </motion.div>
      <motion.div
        variants={variants}
        initial="hidden"
        animate="visible"
        custom={true}
      >
        Slides from right with delay
      </motion.div>
    </>
  );
}
```

---

## Gestures

### Hover Animation

```jsx
function HoverAnimation() {
  return (
    <motion.button
      whileHover={{
        scale: 1.05,
        backgroundColor: "#ff0000",
        transition: { duration: 0.2 },
      }}
      className="px-4 py-2 bg-blue-500 text-white rounded"
    >
      Hover me
    </motion.button>
  );
}
```

### Tap Animation

```jsx
function TapAnimation() {
  return (
    <motion.button
      whileTap={{
        scale: 0.95,
        backgroundColor: "#cc0000",
      }}
      className="px-4 py-2 bg-red-500 text-white rounded"
    >
      Click me
    </motion.button>
  );
}
```

### Focus Animation

```jsx
function FocusAnimation() {
  return (
    <motion.input
      whileFocus={{
        scale: 1.02,
        boxShadow: "0 0 0 3px rgba(59,130,246,0.5)",
      }}
      className="px-4 py-2 border rounded"
      placeholder="Focus me"
    />
  );
}
```

### Combined Gestures

```jsx
function CombinedGestures() {
  return (
    <motion.div
      whileHover={{ scale: 1.1 }}
      whileTap={{ scale: 0.9 }}
      whileFocus={{ scale: 1.05 }}
      whileDrag={{ scale: 1.2 }}
      className="w-32 h-32 bg-purple-500 rounded-lg flex items-center justify-center text-white cursor-pointer"
    >
      Interact
    </motion.div>
  );
}
```

---

## Transitions

### Duration and Delay

```jsx
<motion.div
  animate={{ x: 100 }}
  transition={{
    duration: 2, // Animation takes 2 seconds
    delay: 1, // Starts after 1 second
  }}
/>
```

### Easing Functions

```jsx
// Linear (constant speed)
<motion.div
  animate={{ x: 100 }}
  transition={{ ease: "linear", duration: 1 }}
/>

// Ease in (slow to fast)
<motion.div
  animate={{ x: 100 }}
  transition={{ ease: "easeIn", duration: 1 }}
/>

// Ease out (fast to slow)
<motion.div
  animate={{ x: 100 }}
  transition={{ ease: "easeOut", duration: 1 }}
/>

// Ease in-out (slow, fast, slow)
<motion.div
  animate={{ x: 100 }}
  transition={{ ease: "easeInOut", duration: 1 }}
/>

// Custom cubic bezier
<motion.div
  animate={{ x: 100 }}
  transition={{ ease: [0.17, 0.67, 0.83, 0.67], duration: 1 }}
/>
```

### Spring Animation

```jsx
// Basic spring
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", stiffness: 100, damping: 10 }}
/>

// Bouncy spring
<motion.div
  animate={{ scale: 1.5 }}
  transition={{ type: "spring", stiffness: 300, damping: 5 }}
/>

// Slow spring
<motion.div
  animate={{ rotate: 360 }}
  transition={{ type: "spring", stiffness: 50, damping: 20 }}
/>

// Spring with mass
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", mass: 2, stiffness: 100, damping: 10 }}
/>
```

### Different Transitions per Property

```jsx
<motion.div
  animate={{
    x: 100,
    opacity: 0.5,
    scale: 1.5,
  }}
  transition={{
    duration: 1,
    x: { type: "spring", stiffness: 100 },
    opacity: { duration: 0.5, delay: 0.5 },
    scale: { duration: 0.8, ease: "easeOut" },
  }}
/>
```

### Transition Order

```jsx
// Sequential animations
<motion.div
  animate={{ x: 100, y: 100 }}
  transition={{
    duration: 1,
    times: [0, 0.5, 1],
    ease: "easeInOut"
  }}
/>

// Stagger children
<motion.ul
  initial="hidden"
  animate="visible"
  variants={{
    hidden: { opacity: 0 },
    visible: {
      opacity: 1,
      transition: { staggerChildren: 0.2 }
    }
  }}
>
  <motion.li variants={{ hidden: { x: -20 }, visible: { x: 0 } }} />
  <motion.li variants={{ hidden: { x: -20 }, visible: { x: 0 } }} />
  <motion.li variants={{ hidden: { x: -20 }, visible: { x: 0 } }} />
</motion.ul>
```

---

## Keyframes

### Basic Keyframes

```jsx
function KeyframeExample() {
  return (
    <motion.div
      animate={{
        x: [0, 100, 0, -100, 0], // Move right, back, left, back
        opacity: [1, 0.5, 1, 0.5, 1],
        scale: [1, 1.2, 1, 0.8, 1],
      }}
      transition={{
        duration: 3,
        repeat: Infinity,
        repeatType: "loop", // "loop", "reverse", or "mirror"
      }}
      className="w-32 h-32 bg-blue-500 rounded"
    />
  );
}
```

### Keyframes with Times

```jsx
<motion.div
  animate={{ x: [0, 100, 50, 200] }}
  transition={{
    duration: 2,
    times: [0, 0.3, 0.6, 1], // When each keyframe occurs
    ease: "easeInOut",
  }}
/>
```

### Color Keyframes

```jsx
<motion.div
  animate={{
    backgroundColor: ["#ff0000", "#00ff00", "#0000ff", "#ff0000"],
  }}
  transition={{
    duration: 4,
    repeat: Infinity,
    repeatType: "loop",
  }}
  className="w-32 h-32 rounded"
/>
```

---

## Exit Animations

### Basic Exit Animation

```jsx
import { motion, AnimatePresence } from "framer-motion";

function ExitAnimation() {
  const [isVisible, setIsVisible] = useState(true);

  return (
    <div>
      <button onClick={() => setIsVisible(!isVisible)}>Toggle</button>

      <AnimatePresence>
        {isVisible && (
          <motion.div
            initial={{ opacity: 0, scale: 0 }}
            animate={{ opacity: 1, scale: 1 }}
            exit={{ opacity: 0, scale: 0 }}
            transition={{ duration: 0.5 }}
            className="w-32 h-32 bg-blue-500 mt-4 rounded"
          />
        )}
      </AnimatePresence>
    </div>
  );
}
```

### Stagger Exit Animations

```jsx
function StaggerExit() {
  const [items, setItems] = useState([1, 2, 3, 4]);

  const removeItem = (id) => {
    setItems(items.filter((item) => item !== id));
  };

  return (
    <AnimatePresence>
      {items.map((item) => (
        <motion.div
          key={item}
          initial={{ opacity: 0, x: -50 }}
          animate={{ opacity: 1, x: 0 }}
          exit={{ opacity: 0, x: 50 }}
          transition={{ duration: 0.3 }}
          onClick={() => removeItem(item)}
          className="p-4 mb-2 bg-gray-200 rounded cursor-pointer"
        >
          Item {item} - Click to remove
        </motion.div>
      ))}
    </AnimatePresence>
  );
}
```

### Mode Options

```jsx
// wait - waits for exit to finish before mounting new
<AnimatePresence mode="wait">
  {currentPage === 'home' && <Home />}
  {currentPage === 'about' && <About />}
</AnimatePresence>

// popLayout - removes element immediately but animates surrounding elements
<AnimatePresence mode="popLayout">
  {items.map(item => (
    <motion.div
      key={item.id}
      layout
      exit={{ opacity: 0, scale: 0 }}
    />
  ))}
</AnimatePresence>

// sync - starts exit and enter simultaneously
<AnimatePresence mode="sync">
  {isOpen && <Modal />}
</AnimatePresence>
```

---

## Scroll Animations

### WhileInView

```jsx
function ScrollAnimation() {
  return (
    <motion.div
      initial={{ opacity: 0, y: 50 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, amount: 0.5 }}
      transition={{ duration: 0.5 }}
      className="h-64 bg-blue-500 m-4 rounded"
    >
      Appears when scrolled into view
    </motion.div>
  );
}
```

### Scroll Progress

```jsx
import { useScroll, useTransform } from "framer-motion";

function ScrollProgress() {
  const { scrollYProgress } = useScroll();
  const scale = useTransform(scrollYProgress, [0, 1], [0.2, 1]);
  const opacity = useTransform(scrollYProgress, [0, 0.5, 1], [0, 1, 0]);

  return (
    <motion.div
      style={{ scale, opacity }}
      className="fixed top-0 left-0 right-0 h-2 bg-blue-500 origin-left"
    />
  );
}
```

### Parallax Effect

```jsx
function Parallax() {
  const { scrollYProgress } = useScroll();
  const y = useTransform(scrollYProgress, [0, 1], [0, 200]);

  return (
    <motion.div
      style={{ y }}
      className="h-64 bg-gradient-to-r from-blue-500 to-purple-500"
    >
      Parallax content
    </motion.div>
  );
}
```

---

## Drag and Drop

### Basic Draggable

```jsx
function Draggable() {
  return (
    <motion.div
      drag
      dragConstraints={{
        top: -50,
        left: -50,
        right: 50,
        bottom: 50,
      }}
      className="w-32 h-32 bg-blue-500 rounded cursor-grab active:cursor-grabbing"
    />
  );
}
```

### Drag with Snap

```jsx
function DragSnap() {
  return (
    <motion.div
      drag
      dragSnapToOrigin
      dragConstraints={{ left: 0, right: 0, top: 0, bottom: 0 }}
      className="w-32 h-32 bg-green-500 rounded cursor-grab"
    />
  );
}
```

### Drag with Elasticity

```jsx
function DragElastic() {
  return (
    <motion.div
      drag
      dragElastic={0.5}
      dragConstraints={{ left: -200, right: 200, top: -200, bottom: 200 }}
      className="w-32 h-32 bg-purple-500 rounded cursor-grab"
    />
  );
}
```

### Drag Events

```jsx
function DragEvents() {
  return (
    <motion.div
      drag
      dragConstraints={{ left: -200, right: 200, top: -200, bottom: 200 }}
      onDragStart={() => console.log("Drag started")}
      onDrag={(event, info) => console.log("Dragging:", info.point)}
      onDragEnd={(event, info) => console.log("Drag ended at:", info.point)}
      className="w-32 h-32 bg-red-500 rounded cursor-grab"
    />
  );
}
```

### Drag List (Reorder)

```jsx
import { Reorder } from "framer-motion";

function DragList() {
  const [items, setItems] = useState(["Item 1", "Item 2", "Item 3", "Item 4"]);

  return (
    <Reorder.Group axis="y" values={items} onReorder={setItems}>
      {items.map((item) => (
        <Reorder.Item key={item} value={item}>
          <motion.div
            whileDrag={{ scale: 1.02, boxShadow: "0 5px 10px rgba(0,0,0,0.1)" }}
            className="p-4 mb-2 bg-gray-200 rounded cursor-grab"
          >
            {item}
          </motion.div>
        </Reorder.Item>
      ))}
    </Reorder.Group>
  );
}
```

---

## SVG Animation

### Animated SVG Path

```jsx
function AnimatedPath() {
  return (
    <svg width="400" height="200" viewBox="0 0 400 200">
      <motion.path
        d="M 0 100 Q 100 0 200 100 T 400 100"
        fill="none"
        stroke="#3b82f6"
        strokeWidth="4"
        initial={{ pathLength: 0 }}
        animate={{ pathLength: 1 }}
        transition={{ duration: 2, ease: "easeInOut" }}
      />
    </svg>
  );
}
```

### Animated Circle

```jsx
function AnimatedCircle() {
  return (
    <svg width="200" height="200">
      <motion.circle
        cx="100"
        cy="100"
        r="50"
        fill="#3b82f6"
        initial={{ scale: 0 }}
        animate={{ scale: 1 }}
        transition={{ type: "spring", stiffness: 200, damping: 10 }}
      />
    </svg>
  );
}
```

### Morphing SVG

```jsx
function MorphingSVG() {
  const [isCircle, setIsCircle] = useState(true);

  return (
    <div>
      <button
        onClick={() => setIsCircle(!isCircle)}
        className="mb-4 p-2 bg-blue-500 text-white rounded"
      >
        Morph
      </button>

      <svg width="200" height="200" viewBox="0 0 200 200">
        <motion.path
          d={
            isCircle
              ? "M 100 100 m -50 0 a 50 50 0 1 0 100 0 a 50 50 0 1 0 -100 0" // Circle
              : "M 100 50 L 150 150 L 50 150 Z" // Triangle
          }
          fill="#3b82f6"
          animate={{
            d: isCircle
              ? "M 100 100 m -50 0 a 50 50 0 1 0 100 0 a 50 50 0 1 0 -100 0"
              : "M 100 50 L 150 150 L 50 150 Z",
          }}
          transition={{ duration: 0.5 }}
        />
      </svg>
    </div>
  );
}
```

---

## AnimatePresence

### Modal Example

```jsx
function Modal() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>

      <AnimatePresence>
        {isOpen && (
          <>
            {/* Backdrop */}
            <motion.div
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              onClick={() => setIsOpen(false)}
              className="fixed inset-0 bg-black/50 z-40"
            />

            {/* Modal */}
            <motion.div
              initial={{ scale: 0.8, opacity: 0 }}
              animate={{ scale: 1, opacity: 1 }}
              exit={{ scale: 0.8, opacity: 0 }}
              transition={{ type: "spring", damping: 20 }}
              className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-6 rounded-lg z-50"
            >
              <h2 className="text-xl font-bold mb-4">Modal Title</h2>
              <p className="mb-4">Modal content goes here</p>
              <button onClick={() => setIsOpen(false)}>Close</button>
            </motion.div>
          </>
        )}
      </AnimatePresence>
    </div>
  );
}
```

### Notification Toast Example

```jsx
function ToastNotification() {
  const [notifications, setNotifications] = useState([]);

  const addNotification = () => {
    const id = Date.now();
    setNotifications([...notifications, { id, message: `Notification ${id}` }]);

    setTimeout(() => {
      setNotifications((prev) => prev.filter((n) => n.id !== id));
    }, 3000);
  };

  return (
    <div>
      <button onClick={addNotification}>Show Toast</button>

      <div className="fixed bottom-4 right-4 space-y-2">
        <AnimatePresence>
          {notifications.map((notification) => (
            <motion.div
              key={notification.id}
              initial={{ opacity: 0, x: 100 }}
              animate={{ opacity: 1, x: 0 }}
              exit={{ opacity: 0, x: 100 }}
              transition={{ duration: 0.3 }}
              className="bg-gray-800 text-white px-4 py-2 rounded shadow-lg"
            >
              {notification.message}
            </motion.div>
          ))}
        </AnimatePresence>
      </div>
    </div>
  );
}
```

---

## Layout Animations

### Automatic Layout Animation

```jsx
function LayoutAnimation() {
  const [isExpanded, setIsExpanded] = useState(false);

  return (
    <div>
      <button onClick={() => setIsExpanded(!isExpanded)}>Toggle</button>

      <motion.div
        layout
        className="bg-blue-500 text-white p-4 rounded"
        style={{
          width: isExpanded ? 300 : 150,
          height: isExpanded ? 200 : 100,
        }}
      >
        <motion.h2 layout>Title</motion.h2>
        <motion.p layout>
          {isExpanded && "This content appears when expanded"}
        </motion.p>
      </motion.div>
    </div>
  );
}
```

### Shared Layout Animations

```jsx
function SharedLayout() {
  const [selectedId, setSelectedId] = useState(null);

  const items = [
    { id: 1, color: "bg-red-500", title: "Item 1" },
    { id: 2, color: "bg-blue-500", title: "Item 2" },
    { id: 3, color: "bg-green-500", title: "Item 3" },
  ];

  return (
    <div>
      <div className="grid grid-cols-3 gap-4">
        {items.map((item) => (
          <motion.div
            key={item.id}
            layoutId={item.id.toString()}
            className={`${item.color} h-32 rounded cursor-pointer flex items-center justify-center text-white`}
            onClick={() => setSelectedId(item.id)}
          >
            {item.title}
          </motion.div>
        ))}
      </div>

      <AnimatePresence>
        {selectedId && (
          <motion.div
            layoutId={selectedId.toString()}
            className="fixed inset-4 bg-gray-800 rounded-lg z-50 flex items-center justify-center text-white text-2xl"
            onClick={() => setSelectedId(null)}
          >
            <motion.button
              className="absolute top-4 right-4 bg-red-500 px-4 py-2 rounded"
              onClick={() => setSelectedId(null)}
            >
              Close
            </motion.button>
            Expanded view of item {selectedId}
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
}
```

---

## Complete Examples

### Example 1: Animated Button Component

```jsx
import { motion } from "framer-motion";

function AnimatedButton({ children, onClick, variant = "primary" }) {
  const variants = {
    primary: { background: "#3b82f6", hoverBackground: "#2563eb" },
    secondary: { background: "#6b7280", hoverBackground: "#4b5563" },
    danger: { background: "#ef4444", hoverBackground: "#dc2626" },
  };

  return (
    <motion.button
      whileHover={{
        scale: 1.05,
        backgroundColor: variants[variant].hoverBackground,
        transition: { duration: 0.2 },
      }}
      whileTap={{ scale: 0.95 }}
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ type: "spring", stiffness: 300 }}
      onClick={onClick}
      className="px-6 py-3 rounded-lg text-white font-semibold shadow-lg"
      style={{ backgroundColor: variants[variant].background }}
    >
      {children}
    </motion.button>
  );
}
```

### Example 2: Animated Card Grid

```jsx
function AnimatedCardGrid() {
  const cards = [
    {
      id: 1,
      title: "Card 1",
      description: "Description 1",
      color: "from-red-500 to-red-600",
    },
    {
      id: 2,
      title: "Card 2",
      description: "Description 2",
      color: "from-blue-500 to-blue-600",
    },
    {
      id: 3,
      title: "Card 3",
      description: "Description 3",
      color: "from-green-500 to-green-600",
    },
    {
      id: 4,
      title: "Card 4",
      description: "Description 4",
      color: "from-purple-500 to-purple-600",
    },
  ];

  const containerVariants = {
    hidden: { opacity: 0 },
    visible: {
      opacity: 1,
      transition: {
        staggerChildren: 0.1,
        delayChildren: 0.2,
      },
    },
  };

  const cardVariants = {
    hidden: {
      opacity: 0,
      y: 50,
      scale: 0.9,
    },
    visible: {
      opacity: 1,
      y: 0,
      scale: 1,
      transition: {
        type: "spring",
        damping: 15,
        stiffness: 200,
      },
    },
    hover: {
      scale: 1.05,
      y: -5,
      transition: { type: "spring", stiffness: 400 },
    },
  };

  return (
    <motion.div
      variants={containerVariants}
      initial="hidden"
      animate="visible"
      className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 p-6"
    >
      {cards.map((card) => (
        <motion.div
          key={card.id}
          variants={cardVariants}
          whileHover="hover"
          className="bg-white rounded-lg shadow-lg overflow-hidden cursor-pointer"
        >
          <div className={`h-32 bg-gradient-to-r ${card.color}`} />
          <div className="p-4">
            <h3 className="font-bold text-lg mb-2">{card.title}</h3>
            <p className="text-gray-600">{card.description}</p>
            <motion.button
              whileHover={{ scale: 1.02 }}
              whileTap={{ scale: 0.98 }}
              className="mt-4 px-4 py-2 bg-blue-500 text-white rounded w-full"
            >
              Learn More
            </motion.button>
          </div>
        </motion.div>
      ))}
    </motion.div>
  );
}
```

### Example 3: Page Transition

```jsx
import { motion, AnimatePresence } from "framer-motion";
import { useLocation, Routes, Route } from "react-router-dom";

const pageVariants = {
  initial: { opacity: 0, x: -100 },
  in: { opacity: 1, x: 0 },
  out: { opacity: 0, x: 100 },
};

const pageTransition = {
  type: "tween",
  ease: "anticipate",
  duration: 0.5,
};

function PageTransition() {
  const location = useLocation();

  return (
    <AnimatePresence mode="wait">
      <Routes location={location} key={location.pathname}>
        <Route
          path="/"
          element={
            <motion.div
              initial="initial"
              animate="in"
              exit="out"
              variants={pageVariants}
              transition={pageTransition}
              className="min-h-screen bg-blue-500 flex items-center justify-center text-white text-4xl"
            >
              Home Page
            </motion.div>
          }
        />
        <Route
          path="/about"
          element={
            <motion.div
              initial="initial"
              animate="in"
              exit="out"
              variants={pageVariants}
              transition={pageTransition}
              className="min-h-screen bg-green-500 flex items-center justify-center text-white text-4xl"
            >
              About Page
            </motion.div>
          }
        />
      </Routes>
    </AnimatePresence>
  );
}
```

### Example 4: Loading Spinner

```jsx
function LoadingSpinner() {
  return (
    <div className="flex items-center justify-center h-64">
      <motion.div
        animate={{
          rotate: 360,
          scale: [1, 1.2, 1],
        }}
        transition={{
          rotate: { duration: 1, repeat: Infinity, ease: "linear" },
          scale: { duration: 0.5, repeat: Infinity, repeatType: "reverse" },
        }}
        className="w-16 h-16 border-4 border-blue-500 border-t-transparent rounded-full"
      />
    </div>
  );
}
```

### Example 5: Accordion Component

```jsx
function Accordion({ title, children }) {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div className="border rounded-lg mb-2">
      <motion.button
        onClick={() => setIsOpen(!isOpen)}
        className="w-full px-4 py-3 text-left font-semibold bg-gray-100 hover:bg-gray-200 transition-colors"
        whileTap={{ scale: 0.99 }}
      >
        <div className="flex justify-between items-center">
          {title}
          <motion.span
            animate={{ rotate: isOpen ? 180 : 0 }}
            transition={{ duration: 0.3 }}
          >
            ▼
          </motion.span>
        </div>
      </motion.button>

      <AnimatePresence>
        {isOpen && (
          <motion.div
            initial={{ height: 0, opacity: 0 }}
            animate={{ height: "auto", opacity: 1 }}
            exit={{ height: 0, opacity: 0 }}
            transition={{ duration: 0.3 }}
            className="overflow-hidden"
          >
            <div className="p-4">{children}</div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
}
```

---

## API Reference

### motion Component Props

| Prop         | Type           | Description               |
| ------------ | -------------- | ------------------------- |
| `initial`    | object/string  | Starting animation state  |
| `animate`    | object/string  | Ending animation state    |
| `exit`       | object/string  | Animation when unmounting |
| `transition` | object         | Animation settings        |
| `whileHover` | object         | Animation on hover        |
| `whileTap`   | object         | Animation on tap/click    |
| `whileFocus` | object         | Animation on focus        |
| `whileDrag`  | object         | Animation while dragging  |
| `drag`       | boolean/string | Enable dragging           |
| `layout`     | boolean        | Animate layout changes    |
| `layoutId`   | string         | Shared layout animations  |

### Transition Properties

| Property     | Options                                                                   | Default     |
| ------------ | ------------------------------------------------------------------------- | ----------- |
| `duration`   | number                                                                    | 0.3         |
| `delay`      | number                                                                    | 0           |
| `ease`       | "linear", "easeIn", "easeOut", "easeInOut", [number,number,number,number] | "easeInOut" |
| `type`       | "spring", "tween", "inertia"                                              | "tween"     |
| `stiffness`  | number (spring)                                                           | 100         |
| `damping`    | number (spring)                                                           | 10          |
| `mass`       | number (spring)                                                           | 1           |
| `repeat`     | number                                                                    | 0           |
| `repeatType` | "loop", "reverse", "mirror"                                               | "loop"      |

### Gesture Properties

| Gesture      | Properties                           |
| ------------ | ------------------------------------ |
| `whileHover` | scale, backgroundColor, rotate, etc. |
| `whileTap`   | scale, backgroundColor, rotate, etc. |
| `whileDrag`  | scale, cursor, opacity, etc.         |
| `whileFocus` | scale, boxShadow, borderColor, etc.  |

### Animation Values

| Value Type | Properties                          |
| ---------- | ----------------------------------- |
| Transform  | x, y, scale, rotate, skewX, skewY   |
| Opacity    | opacity                             |
| Color      | backgroundColor, color, borderColor |
| Dimension  | width, height, borderRadius         |
| Position   | top, right, bottom, left            |

## Troubleshooting

Problem 1: Animations not working

Solutions:

- Check motion component is used (not regular div)
- Verify animation properties are correct
- Ensure transition is configured

Problem 2: Exit animations not working

Solutions:

- Wrap component with AnimatePresence
- Add exit prop to motion component
- Check AnimatePresence is at parent level

Problem 3: Layout animations flickering

Solutions:

- Add layout prop to parent and children
- Use layoutId for shared elements
- Set position: relative on parent

## Useful Resources

- Framer Motion Docs: https://www.framer.com/motion/
- Framer Motion Examples: https://www.framer.com/motion/examples/
- GitHub Repository: https://github.com/framer/motion

---

Created for beginners learning Framer Motion animations
