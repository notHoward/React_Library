# Shadcn UI - Complete Beginner's Guide

A comprehensive from-scratch guide to Shadcn UI component library.

## Table of Contents

1. [What is Shadcn UI](#what-is-shadcn-ui)
2. [Installation](#installation)
3. [Project Setup](#project-setup)
4. [Adding Components](#adding-components)
5. [Using Components](#using-components)
6. [All Components](#all-components)
7. [Forms with React Hook Form & Zod](#forms-with-react-hook-form--zod)
8. [Theming](#theming)
9. [Customization](#customization)
10. [Complete Examples](#complete-examples)
11. [API Reference](#api-reference)

---

## What is Shadcn UI

Shadcn UI is **not a traditional component library** - it's a collection of beautifully designed, accessible components that you copy-paste directly into your project.

**What makes Shadcn UI different:**

| Traditional Libraries (MUI, Chakra) | Shadcn UI                       |
| ----------------------------------- | ------------------------------- |
| Installed as npm package            | Copy-paste source code          |
| Hard to customize                   | You own the code - full control |
| Large bundle size                   | Only components you use         |
| Fighting CSS overrides              | Edit directly                   |

**Built on amazing tools:**

- **Tailwind CSS** - Utility-first styling
- **Radix UI** - Accessible, unstyled primitives
- **Class Variance Authority** - Component variants

**The Philosophy:** "Open Source. Open Code." - You're not installing a library, you're adding components to your codebase that you can modify however you want.

---

## Installation

### Prerequisites

- Node.js (latest LTS)
- React 18+ or Next.js 14+
- Tailwind CSS configured

### Create a New Project

**For Next.js (Recommended):**

```bash
npx create-next-app@latest my-app --typescript --tailwind --eslint
```

**For Vite:**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install tailwindcss @tailwindcss/vite
```

### Initialize Shadcn UI

Run the initialization command:

```bash
npx shadcn@latest init
```

You'll be prompted to configure:

```bash
✔ Which style would you like to use? › New York
✔ Which color would you like to use as base color? › Slate
✔ Do you want to use CSS variables for colors? … yes
✔ Where is your global CSS file? … src/app/globals.css
✔ Where is your tailwind.config.js located? … tailwind.config.js
✔ Configure the import alias for components: … @/components
✔ Configure the import alias for utils: … @/lib/utils
✔ Are you using React Server Components? … yes
✔ Write configuration to components.json? … yes
```

**What happens during init:**

1. Installs dependencies (`tailwindcss-animate`, `class-variance-authority`, `clsx`, `tailwind-merge`, `lucide-react`)
2. Updates `tailwind.config.js` with required animations
3. Adds CSS variables to `globals.css`
4. Creates `lib/utils.js` with the `cn()` helper function

### For JavaScript Projects (No TypeScript)

If you're not using TypeScript, create a `jsconfig.json` file:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

Update `vite.config.js`:

```javascript
import path from "path";

export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});
```

---

## Adding Components

### The `npx shadcn@latest add` Command

To add a component, run:

```bash
npx shadcn@latest add [component-name]
```

**What happens when you add a component:**

1. Creates a new file in `src/components/ui/`
2. Installs any required Radix UI dependencies
3. Adds the component code to your project

### Adding a Button

```bash
npx shadcn@latest add button
```

This creates `src/components/ui/button.tsx` with the full source code.

### Adding a Card

```bash
npx shadcn@latest add card
```

### Adding Multiple Components

```bash
npx shadcn@latest add button card input label select
```

### Most Commonly Used Components

```bash
# Basic components
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add input
npx shadcn@latest add label
npx shadcn@latest add select

# Feedback components
npx shadcn@latest add alert
npx shadcn@latest add toast
npx shadcn@latest add sonner
npx shadcn@latest add progress

# Layout components
npx shadcn@latest add separator
npx shadcn@latest add sheet
npx shadcn@latest add dialog
npx shadcn@latest add dropdown-menu

# Form components
npx shadcn@latest add form
npx shadcn@latest add checkbox
npx shadcn@latest add radio-group
npx shadcn@latest add textarea

# Data display
npx shadcn@latest add table
npx shadcn@latest add avatar
npx shadcn@latest add badge
npx shadcn@latest add tabs
```

### Complete Component List

According to the documentation, Shadcn UI offers 56 components:

| Category     | Components                                                            |
| ------------ | --------------------------------------------------------------------- |
| Layout       | Accordion, Card, Sheet, Separator, Tabs, Collapsible                  |
| Form         | Button, Input, Label, Select, Checkbox, Radio Group, Textarea, Switch |
| Overlay      | Dialog, Alert Dialog, Popover, Tooltip, Dropdown Menu, Context Menu   |
| Navigation   | Breadcrumb, Navigation Menu, Pagination, Menubar                      |
| Data Display | Table, Avatar, Badge, Calendar, Carousel, Chart                       |
| Feedback     | Alert, Progress, Skeleton, Sonner, Toast                              |

---

## Using Components

### Basic Button Usage

After adding the button component, use it in your page:

```tsx
import { Button } from "@/components/ui/button";

export default function Home() {
  return (
    <div className="p-8">
      <Button>Click me</Button>
    </div>
  );
}
```

### Button Variants

```tsx
import { Button } from "@/components/ui/button";

function ButtonDemo() {
  return (
    <div className="flex gap-4">
      <Button variant="default">Default</Button>
      <Button variant="destructive">Destructive</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="link">Link</Button>
    </div>
  );
}
```

### Button Sizes

```tsx
<div className="flex gap-4 items-center">
  <Button size="default">Default</Button>
  <Button size="sm">Small</Button>
  <Button size="lg">Large</Button>
  <Button size="icon">🔍</Button>
</div>
```

### Card Component

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import { Button } from "@/components/ui/button";

function CardDemo() {
  return (
    <Card className="w-[350px]">
      <CardHeader>
        <CardTitle>Card Title</CardTitle>
        <CardDescription>Card description goes here</CardDescription>
      </CardHeader>
      <CardContent>
        <p>Card content - main information goes here</p>
      </CardContent>
      <CardFooter>
        <Button>Action</Button>
      </CardFooter>
    </Card>
  );
}
```

### Input with Label

```tsx
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";

function InputDemo() {
  return (
    <div className="grid w-full max-w-sm gap-4">
      <div className="grid gap-2">
        <Label htmlFor="email">Email</Label>
        <Input id="email" type="email" placeholder="Enter your email" />
      </div>
      <div className="grid gap-2">
        <Label htmlFor="password">Password</Label>
        <Input id="password" type="password" placeholder="Enter password" />
      </div>
    </div>
  );
}
```

### Dialog (Modal)

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";

function DialogDemo() {
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button>Open Dialog</Button>
      </DialogTrigger>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Are you sure?</DialogTitle>
          <DialogDescription>This action cannot be undone.</DialogDescription>
        </DialogHeader>
        <DialogFooter>
          <Button variant="outline">Cancel</Button>
          <Button variant="destructive">Confirm</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  );
}
```

### Dropdown Menu

```tsx
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { Button } from "@/components/ui/button";

function DropdownDemo() {
  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button>Open Menu</Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent>
        <DropdownMenuLabel>My Account</DropdownMenuLabel>
        <DropdownMenuSeparator />
        <DropdownMenuItem>Profile</DropdownMenuItem>
        <DropdownMenuItem>Settings</DropdownMenuItem>
        <DropdownMenuItem>Logout</DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  );
}
```

### Select Dropdown

```tsx
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";

function SelectDemo() {
  return (
    <Select>
      <SelectTrigger className="w-[180px]">
        <SelectValue placeholder="Select a fruit" />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="apple">Apple</SelectItem>
        <SelectItem value="banana">Banana</SelectItem>
        <SelectItem value="orange">Orange</SelectItem>
      </SelectContent>
    </Select>
  );
}
```

### Toast Notifications

First, add the toast component:

```bash
npx shadcn@latest add toast
```

Add the Toaster to your layout:

```tsx
// app/layout.tsx
import { Toaster } from "@/components/ui/toaster";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        {children}
        <Toaster />
      </body>
    </html>
  );
}
```

Use the toast hook in client components:

```tsx
"use client";
import { Button } from "@/components/ui/button";
import { useToast } from "@/components/ui/use-toast";

function ToastDemo() {
  const { toast } = useToast();

  return (
    <Button
      onClick={() => {
        toast({
          title: "Success!",
          description: "Your action was completed successfully.",
          duration: 3000,
        });
      }}
    >
      Show Toast
    </Button>
  );
}
```

### Tabs

```tsx
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";

function TabsDemo() {
  return (
    <Tabs defaultValue="tab1" className="w-[400px]">
      <TabsList>
        <TabsTrigger value="tab1">Tab 1</TabsTrigger>
        <TabsTrigger value="tab2">Tab 2</TabsTrigger>
        <TabsTrigger value="tab3">Tab 3</TabsTrigger>
      </TabsList>
      <TabsContent value="tab1">Content for tab 1</TabsContent>
      <TabsContent value="tab2">Content for tab 2</TabsContent>
      <TabsContent value="tab3">Content for tab 3</TabsContent>
    </Tabs>
  );
}
```

---

## Forms with React Hook Form & Zod

### Install Dependencies

```bash
npm install react-hook-form zod @hookform/resolvers
npx shadcn@latest add form
```

### Create a Form Schema

```tsx
"use client";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";

// Define validation schema
const formSchema = z.object({
  username: z.string().min(2, {
    message: "Username must be at least 2 characters.",
  }),
  email: z.string().email({
    message: "Please enter a valid email address.",
  }),
  password: z.string().min(6, {
    message: "Password must be at least 6 characters.",
  }),
});

function ProfileForm() {
  const form = useForm({
    resolver: zodResolver(formSchema),
    defaultValues: {
      username: "",
      email: "",
      password: "",
    },
  });

  function onSubmit(values) {
    console.log(values);
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
        <FormField
          control={form.control}
          name="username"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Username</FormLabel>
              <FormControl>
                <Input placeholder="shadcn" {...field} />
              </FormControl>
              <FormDescription>
                This is your public display name.
              </FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input placeholder="you@example.com" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        <FormField
          control={form.control}
          name="password"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Password</FormLabel>
              <FormControl>
                <Input type="password" placeholder="••••••" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        <Button type="submit">Submit</Button>
      </form>
    </Form>
  );
}
```

### Complete Example with Select Field

```tsx
"use client";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { useToast } from "@/components/ui/use-toast";

const projectSchema = z.object({
  projectName: z.string().min(2, "Project name must be at least 2 characters"),
  framework: z.string({ required_error: "Please select a framework" }),
});

export default function ProjectForm() {
  const { toast } = useToast();
  const form = useForm({
    resolver: zodResolver(projectSchema),
    defaultValues: {
      projectName: "",
      framework: "",
    },
  });

  function onSubmit(values) {
    toast({
      title: "Project Created!",
      description: (
        <pre className="mt-2 rounded-md bg-slate-950 p-4">
          <code className="text-white">{JSON.stringify(values, null, 2)}</code>
        </pre>
      ),
    });
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-6">
        <FormField
          control={form.control}
          name="projectName"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Project Name</FormLabel>
              <FormControl>
                <Input placeholder="my-awesome-project" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="framework"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Framework</FormLabel>
              <Select onValueChange={field.onChange} defaultValue={field.value}>
                <FormControl>
                  <SelectTrigger>
                    <SelectValue placeholder="Select a framework" />
                  </SelectTrigger>
                </FormControl>
                <SelectContent>
                  <SelectItem value="nextjs">Next.js</SelectItem>
                  <SelectItem value="vite">Vite</SelectItem>
                  <SelectItem value="astro">Astro</SelectItem>
                  <SelectItem value="remix">Remix</SelectItem>
                </SelectContent>
              </Select>
              <FormMessage />
            </FormItem>
          )}
        />

        <Button type="submit">Create Project</Button>
      </form>
    </Form>
  );
}
```

---

## Theming

### CSS Variables

Shadcn UI uses CSS variables for theming. These are defined in your `globals.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;
    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;
    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;
    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;
    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 212.7 26.8% 83.9%;
  }
}
```

### Changing Colors

To change the base color, modify the CSS variables:

```css
:root {
  --primary: 24 94% 53%; /* Orange primary */
  --primary-foreground: 60 9% 98%;
}
```

### Dark Mode

Add the `dark` class to the HTML element to enable dark mode:

```tsx
// Toggle dark mode
const toggleDarkMode = () => {
  document.documentElement.classList.toggle("dark");
};
```

### Using Theme Variables in Components

```tsx
<div className="bg-background text-foreground">
  <div className="bg-primary text-primary-foreground">Primary Button</div>
  <div className="bg-secondary text-secondary-foreground">Secondary</div>
  <div className="border-border">Bordered content</div>
</div>
```

---

## Customization

### The cn() Utility Function

Shadcn UI provides a `cn()` function that combines Tailwind classes:

```tsx
import { cn } from "@/lib/utils";

// Basic usage
<div className={cn("p-4", "bg-red-500")}> // "p-4 bg-red-500"

// Conditional classes
<div className={cn("p-4", {
  "bg-red-500": isError,
  "bg-green-500": isSuccess,
})} />

// Overriding classes (last class wins)
<div className={cn("p-4 bg-blue-500", "bg-red-500")}> // "p-4 bg-red-500"
```

### Customizing Button Component

Since the component code is in your project, you can modify it directly:

```tsx
// components/ui/button.tsx - Add custom variant
const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors",
  {
    variants: {
      variant: {
        // ... existing variants
        custom: "bg-purple-500 text-white hover:bg-purple-600",
      },
    },
  },
);
```

### Adding Custom Components

Create your own components using Shadcn UI primitives:

```tsx
// components/UserCard.tsx
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";
import { Button } from "@/components/ui/button";

export function UserCard({ user }) {
  return (
    <Card className="w-80">
      <CardHeader className="flex flex-row items-center gap-4">
        <Avatar>
          <AvatarImage src={user.avatar} />
          <AvatarFallback>{user.name[0]}</AvatarFallback>
        </Avatar>
        <CardTitle>{user.name}</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-sm text-muted-foreground">{user.email}</p>
        <Button className="mt-4 w-full">View Profile</Button>
      </CardContent>
    </Card>
  );
}
```

---

## Complete Examples

### Example 1: Sign Up Form

```tsx
"use client";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { Button } from "@/components/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { useToast } from "@/components/ui/use-toast";

const signUpSchema = z
  .object({
    name: z.string().min(2, "Name must be at least 2 characters"),
    email: z.string().email("Please enter a valid email"),
    password: z.string().min(6, "Password must be at least 6 characters"),
    confirmPassword: z.string(),
  })
  .refine((data) => data.password === data.confirmPassword, {
    message: "Passwords don't match",
    path: ["confirmPassword"],
  });

export function SignUpForm() {
  const { toast } = useToast();
  const form = useForm({
    resolver: zodResolver(signUpSchema),
    defaultValues: {
      name: "",
      email: "",
      password: "",
      confirmPassword: "",
    },
  });

  function onSubmit(values) {
    toast({
      title: "Account Created!",
      description: `Welcome, ${values.name}!`,
    });
  }

  return (
    <Card className="w-full max-w-md mx-auto">
      <CardHeader>
        <CardTitle>Sign Up</CardTitle>
        <CardDescription>Create an account to get started</CardDescription>
      </CardHeader>
      <CardContent>
        <Form {...form}>
          <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
            <FormField
              control={form.control}
              name="name"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Name</FormLabel>
                  <FormControl>
                    <Input placeholder="John Doe" {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <FormField
              control={form.control}
              name="email"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Email</FormLabel>
                  <FormControl>
                    <Input
                      type="email"
                      placeholder="john@example.com"
                      {...field}
                    />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <FormField
              control={form.control}
              name="password"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Password</FormLabel>
                  <FormControl>
                    <Input type="password" placeholder="••••••" {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <FormField
              control={form.control}
              name="confirmPassword"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Confirm Password</FormLabel>
                  <FormControl>
                    <Input type="password" placeholder="••••••" {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <Button type="submit" className="w-full">
              Sign Up
            </Button>
          </form>
        </Form>
      </CardContent>
    </Card>
  );
}
```

### Example 2: Dashboard Layout

```tsx
// app/dashboard/page.tsx
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";
import { Badge } from "@/components/ui/badge";

const recentSales = [
  {
    id: 1,
    name: "John Doe",
    email: "john@example.com",
    amount: "$250",
    status: "completed",
  },
  {
    id: 2,
    name: "Jane Smith",
    email: "jane@example.com",
    amount: "$150",
    status: "pending",
  },
  {
    id: 3,
    name: "Bob Johnson",
    email: "bob@example.com",
    amount: "$350",
    status: "completed",
  },
];

export default function DashboardPage() {
  return (
    <div className="p-8 space-y-8">
      <div className="flex justify-between items-center">
        <h1 className="text-3xl font-bold">Dashboard</h1>
        <Button>Export Data</Button>
      </div>

      {/* Stats Cards */}
      <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4">
        <Card>
          <CardHeader className="flex flex-row items-center justify-between pb-2">
            <CardTitle className="text-sm font-medium">Total Revenue</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">$45,231.89</div>
            <p className="text-xs text-muted-foreground">
              +20.1% from last month
            </p>
          </CardContent>
        </Card>
        <Card>
          <CardHeader className="flex flex-row items-center justify-between pb-2">
            <CardTitle className="text-sm font-medium">Subscriptions</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="text-2xl font-bold">+2350</div>
            <p className="text-xs text-muted-foreground">
              +180.1% from last month
            </p>
          </CardContent>
        </Card>
      </div>

      {/* Recent Sales Table */}
      <Card>
        <CardHeader>
          <CardTitle>Recent Sales</CardTitle>
        </CardHeader>
        <CardContent>
          <Table>
            <TableHeader>
              <TableRow>
                <TableHead>Customer</TableHead>
                <TableHead>Email</TableHead>
                <TableHead>Amount</TableHead>
                <TableHead>Status</TableHead>
              </TableRow>
            </TableHeader>
            <TableBody>
              {recentSales.map((sale) => (
                <TableRow key={sale.id}>
                  <TableCell className="flex items-center gap-3">
                    <Avatar className="h-8 w-8">
                      <AvatarFallback>{sale.name[0]}</AvatarFallback>
                    </Avatar>
                    {sale.name}
                  </TableCell>
                  <TableCell>{sale.email}</TableCell>
                  <TableCell>{sale.amount}</TableCell>
                  <TableCell>
                    <Badge
                      variant={
                        sale.status === "completed" ? "default" : "secondary"
                      }
                    >
                      {sale.status}
                    </Badge>
                  </TableCell>
                </TableRow>
              ))}
            </TableBody>
          </Table>
        </CardContent>
      </Card>
    </div>
  );
}
```

---

## API Reference

### CLI Commands

| Command                                           | Description                          |
| ------------------------------------------------- | ------------------------------------ |
| `npx shadcn@latest init`                          | Initialize Shadcn UI in your project |
| `npx shadcn@latest add [component]`               | Add a component to your project      |
| `npx shadcn@latest add [component1] [component2]` | Add multiple components              |
| `npx shadcn@latest diff`                          | See changes since last update        |

### Component Variants

**Button Variants:**

- `default` - Primary action button
- `destructive` - Delete/remove action
- `outline` - Bordered button
- `secondary` - Secondary action
- `ghost` - Minimal button
- `link` - Looks like a link

**Button Sizes:**

- `default` - Default size
- `sm` - Small button
- `lg` - Large button
- `icon` - Square button for icons

### Utility Functions

| Function       | Purpose                     |
| -------------- | --------------------------- |
| `cn()`         | Merge Tailwind classes      |
| `formatDate()` | Format dates (if installed) |

### CSS Variables

| Variable        | Purpose               |
| --------------- | --------------------- |
| `--background`  | Main background color |
| `--foreground`  | Main text color       |
| `--primary`     | Primary brand color   |
| `--secondary`   | Secondary brand color |
| `--destructive` | Error/danger color    |
| `--border`      | Border color          |
| `--radius`      | Border radius         |

---

## Troubleshooting

**Problem 1:** Components not styled correctly

**Solutions:**

- Ensure Tailwind CSS is configured
- Check `globals.css` has the CSS variables
- Verify `tailwind.config.js` includes the content paths

**Problem 2:** TypeScript errors

**Solutions:**

- Make sure `tsconfig.json` has the path aliases configured
- Run `npx shadcn@latest init` to regenerate config

**Problem 3:** Build errors in production

**Solutions:**

- Check all components are properly imported
- Ensure `"use client"` directive for client components in Next.js

---

## Useful Resources

- Shadcn UI Docs: https://ui.shadcn.com
- GitHub Repository: https://github.com/shadcn/ui
- Components Gallery: https://ui.shadcn.com/docs/components

---

Created for beginners learning Shadcn UI
