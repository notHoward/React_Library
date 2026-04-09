# Supabase - Complete Beginner's Guide

A comprehensive from-scratch guide to Supabase backend development.

## Table of Contents

1. [What is Supabase](#what-is-supabase)
2. [Core Concepts](#core-concepts)
3. [Project Setup](#project-setup)
4. [Database Basics](#database-basics)
5. [JavaScript Client Library](#javascript-client-library)
6. [CRUD Operations](#crud-operations)
7. [Filters and Queries](#filters-and-queries)
8. [Authentication](#authentication)
9. [Row Level Security (RLS)](#row-level-security-rls)
10. [Realtime Subscriptions](#realtime-subscriptions)
11. [Storage](#storage)
12. [Edge Functions](#edge-functions)
13. [Complete Examples](#complete-examples)
14. [API Reference](#api-reference)

---

## What is Supabase

Supabase is an open-source Firebase alternative that provides a complete backend solution for your applications.

**What Supabase provides:**

| Feature                | Description                                     |
| ---------------------- | ----------------------------------------------- |
| **Database**           | PostgreSQL database with real-time capabilities |
| **Authentication**     | User management with email, magic links, OAuth  |
| **Storage**            | File storage for images, videos, documents      |
| **Edge Functions**     | Serverless functions written in TypeScript      |
| **Realtime**           | WebSocket-based real-time subscriptions         |
| **Auto-generated API** | RESTful API from your database schema           |

**Why Supabase instead of Firebase:**

| Aspect      | Supabase                | Firebase          |
| ----------- | ----------------------- | ----------------- |
| Database    | PostgreSQL (relational) | Firestore (NoSQL) |
| Pricing     | More generous free tier | Pay-as-you-go     |
| Open Source | Yes (self-hostable)     | No                |
| SQL         | Full SQL support        | No                |
| Real-time   | Built-in                | Limited           |

**How Supabase works:**

```
Your App → Supabase Client → Supabase API → PostgreSQL Database
                                      ↓
                                 Realtime WebSocket
                                      ↓
                              Real-time Updates to App
```

---

## Core Concepts

### The 6 Main Services

```javascript
// 1. Database (PostgreSQL)
supabase.from('users').select('*')

// 2. Authentication
supabase.auth.signUp({ email, password })

// 3. Storage
supabase.storage.from('avatars').upload('file.jpg', file)

// 4. Realtime
supabase.channel('room1').on('broadcast', ...)

// 5. Edge Functions
supabase.functions.invoke('my-function')

// 6. Auto-generated API
// REST endpoints created automatically from your tables
```

### The Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│                      Your React App                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Supabase Client SDK                       │
│  • createClient() - Initialize connection                    │
│  • from() - Choose table                                     │
│  • select() - Query data                                     │
│  • insert() - Add data                                       │
│  • subscribe() - Real-time updates                           │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     Supabase Platform                        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐        │
│  │  Auth    │ │PostgREST │ │Realtime  │ │ Storage  │        │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘        │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    PostgreSQL Database                       │
│  • Tables with RLS policies                                  │
│  • Triggers and functions                                    │
│  • Real-time replication                                     │
└─────────────────────────────────────────────────────────────┘
```

---

## Project Setup

### Step 1: Create a Supabase Project

1. Go to [supabase.com/dashboard](https://supabase.com/dashboard)
2. Click "New project"
3. Enter project details:
   - Name: `my-app`
   - Database Password: Create a strong password
   - Region: Choose closest to your users
4. Click "Create new project"
5. Wait 2-3 minutes for database to spin up

### Step 2: Get API Keys

After project is created:

1. Go to Project Settings (gear icon)
2. Click "API" in the sidebar
3. Copy these values:
   - **Project URL**: `https://xxxxx.supabase.co`
   - **anon public key**: `eyJhbGciOiJIUzI1NiIs...` (safe for frontend)

### Step 3: Install the Client Library

```bash
npm install @supabase/supabase-js
```

### Step 4: Initialize the Client

```javascript
// lib/supabase.js
import { createClient } from "@supabase/supabase-js";

// Get these from your Supabase project settings
const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL;
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY;

export const supabase = createClient(supabaseUrl, supabaseAnonKey);
```

### Environment Variables

Create a `.env.local` file:

```env
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key-here
```

---

## Database Basics

### Creating Tables

You can create tables in the Supabase Dashboard or using SQL.

**Method 1: Dashboard UI**

1. Go to Table Editor
2. Click "Create table"
3. Add columns:
   - `id` (int8, primary key)
   - `created_at` (timestamptz, default now())
   - `title` (text)
   - `completed` (bool, default false)

**Method 2: SQL Editor**

```sql
-- Create a todos table
CREATE TABLE todos (
  id BIGSERIAL PRIMARY KEY,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  title TEXT NOT NULL,
  completed BOOLEAN DEFAULT FALSE,
  user_id UUID REFERENCES auth.users(id) NOT NULL
);

-- Enable Row Level Security
ALTER TABLE todos ENABLE ROW LEVEL SECURITY;

-- Create policy for users to see only their own todos
CREATE POLICY "Users can view their own todos"
  ON todos FOR SELECT
  USING (auth.uid() = user_id);

-- Create policy for users to insert their own todos
CREATE POLICY "Users can insert their own todos"
  ON todos FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Create policy for users to update their own todos
CREATE POLICY "Users can update their own todos"
  ON todos FOR UPDATE
  USING (auth.uid() = user_id);

-- Create policy for users to delete their own todos
CREATE POLICY "Users can delete their own todos"
  ON todos FOR DELETE
  USING (auth.uid() = user_id);
```

### Data Types

| SQL Type          | JavaScript   | Description             |
| ----------------- | ------------ | ----------------------- |
| `bigint` / `int8` | number       | Large integer           |
| `int` / `int4`    | number       | Standard integer        |
| `text`            | string       | Variable length text    |
| `varchar`         | string       | Limited length text     |
| `boolean`         | boolean      | True/false              |
| `timestamptz`     | Date object  | Timestamp with timezone |
| `jsonb`           | object/array | JSON data               |
| `uuid`            | string       | Unique identifier       |
| `float8`          | number       | Decimal number          |

### Relationships

```sql
-- One-to-many: users → posts
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT
);

CREATE TABLE posts (
  id BIGSERIAL PRIMARY KEY,
  title TEXT,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE
);

-- Many-to-many: users ↔ projects
CREATE TABLE users (
  id UUID PRIMARY KEY,
  name TEXT
);

CREATE TABLE projects (
  id BIGSERIAL PRIMARY KEY,
  name TEXT
);

CREATE TABLE user_projects (
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  project_id BIGINT REFERENCES projects(id) ON DELETE CASCADE,
  PRIMARY KEY (user_id, project_id)
);
```

---

## JavaScript Client Library

### Initializing the Client

```javascript
import { createClient } from "@supabase/supabase-js";

const supabaseUrl = "https://your-project.supabase.co";
const supabaseAnonKey = "your-anon-key";

// Basic initialization
const supabase = createClient(supabaseUrl, supabaseAnonKey);

// With custom options
const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: true,
    storage: localStorage, // or AsyncStorage for React Native
  },
  db: {
    schema: "public",
  },
  realtime: {
    params: {
      eventsPerSecond: 10,
    },
  },
});
```

### Understanding the Response Object

Every query returns a Promise with this structure:

```javascript
const { data, error, status, statusText } = await supabase
  .from("todos")
  .select("*");

// Success case
// data: [{ id: 1, title: 'Buy milk' }]
// error: null
// status: 200

// Error case
// data: null
// error: { message: 'Error message', code: 'PGRST...' }
// status: 400
```

### Error Handling Pattern

```javascript
async function fetchTodos() {
  const { data, error } = await supabase.from("todos").select("*");

  if (error) {
    console.error("Error fetching todos:", error.message);
    // Handle error appropriately
    return [];
  }

  return data;
}
```

---

## CRUD Operations

### SELECT (Read Data)

```javascript
import { supabase } from "./lib/supabase";

// Get all rows
const { data, error } = await supabase.from("todos").select("*");

// Select specific columns
const { data, error } = await supabase
  .from("todos")
  .select("id, title, completed");

// Select with relationship (join)
const { data, error } = await supabase.from("posts").select(`
    id,
    title,
    user:users (
      id,
      name
    )
  `);

// Get single row by ID
const { data, error } = await supabase
  .from("todos")
  .select("*")
  .eq("id", 123)
  .single();

// Get first matching row
const { data, error } = await supabase
  .from("todos")
  .select("*")
  .eq("completed", false)
  .maybeSingle(); // returns null if not found

// Count rows
const { count, error } = await supabase
  .from("todos")
  .select("*", { count: "exact", head: true });
```

### INSERT (Create Data)

```javascript
// Insert single row
const { data, error } = await supabase
  .from("todos")
  .insert({ title: "Buy groceries", completed: false })
  .select(); // Returns the inserted row

// Insert multiple rows
const { data, error } = await supabase
  .from("todos")
  .insert([{ title: "Task 1" }, { title: "Task 2" }, { title: "Task 3" }])
  .select();

// Insert with returning minimal (better performance)
const { error } = await supabase.from("todos").insert({ title: "Quick task" });
// Note: without .select(), no data is returned

// Upsert (insert or update if exists)
const { data, error } = await supabase
  .from("todos")
  .upsert({ id: 1, title: "Updated title" })
  .select();
```

### UPDATE (Modify Data)

```javascript
// Update with filter
const { data, error } = await supabase
  .from("todos")
  .update({ completed: true })
  .eq("id", 1)
  .select();

// Update multiple rows
const { data, error } = await supabase
  .from("todos")
  .update({ completed: true })
  .eq("user_id", currentUserId)
  .select();

// Update with multiple conditions
const { data, error } = await supabase
  .from("todos")
  .update({ priority: "high" })
  .eq("completed", false)
  .lt("due_date", "2024-12-31")
  .select();
```

### DELETE (Remove Data)

```javascript
// Delete single row
const { error } = await supabase.from("todos").delete().eq("id", 1);

// Delete with returning deleted data
const { data, error } = await supabase
  .from("todos")
  .delete()
  .eq("completed", true)
  .select();

// Delete all matching rows
const { error } = await supabase.from("todos").delete().eq("user_id", userId);
```

---

## Filters and Queries

### Basic Filters

```javascript
// Equal to
.eq('column', value)

// Not equal to
.neq('column', value)

// Greater than
.gt('column', value)

// Greater than or equal
.gte('column', value)

// Less than
.lt('column', value)

// Less than or equal
.lte('column', value)

// Like (pattern matching)
.like('name', '%John%')

// ILike (case-insensitive)
.ilike('name', '%john%')

// Is null
.is('column', null)

// In array
.in('column', [1, 2, 3])

// Contains (for arrays)
.contains('tags', ['urgent'])

// Filter examples
const { data } = await supabase
  .from('products')
  .select('*')
  .gte('price', 10)
  .lte('price', 50)
  .eq('in_stock', true)
```

### Logical Operators

```javascript
// OR condition
const { data } = await supabase
  .from("products")
  .select("*")
  .or("category.eq.electronics,price.gt.100");

// AND condition (just chain filters)
const { data } = await supabase
  .from("products")
  .select("*")
  .eq("category", "electronics")
  .gte("price", 50);

// NOT condition
const { data } = await supabase
  .from("products")
  .select("*")
  .not("category", "eq", "outlet");
```

### Ordering and Limits

```javascript
// Order by column (ascending default)
.order('created_at', { ascending: false })

// Order by multiple columns
.order('priority', { ascending: false })
.order('created_at', { ascending: true })

// Limit results
.limit(10)

// Pagination with range
.range(0, 9) // First 10 rows
.range(10, 19) // Next 10 rows
```

### Full Example: Product Search

```javascript
async function searchProducts(filters) {
  let query = supabase.from("products").select("*");

  if (filters.category) {
    query = query.eq("category", filters.category);
  }

  if (filters.minPrice) {
    query = query.gte("price", filters.minPrice);
  }

  if (filters.maxPrice) {
    query = query.lte("price", filters.maxPrice);
  }

  if (filters.search) {
    query = query.ilike("name", `%${filters.search}%`);
  }

  if (filters.sortBy) {
    query = query.order(filters.sortBy, {
      ascending: filters.sortOrder === "asc",
    });
  }

  if (filters.limit) {
    query = query.limit(filters.limit);
  }

  const { data, error } = await query;

  if (error) throw error;
  return data;
}
```

---

## Authentication

### User Management Overview

Supabase Auth provides:

- Email/Password signup and login
- Magic link (passwordless) authentication
- OAuth providers (Google, GitHub, Apple, etc.)
- Phone authentication (SMS)
- Session management
- User metadata

### Sign Up

```javascript
import { supabase } from "./lib/supabase";

// Email/Password signup
const { data, error } = await supabase.auth.signUp({
  email: "user@example.com",
  password: "password123",
  options: {
    data: {
      full_name: "John Doe",
      avatar_url: "https://example.com/avatar.jpg",
    },
  },
});

// After signup, user receives confirmation email
// The session is null until email is confirmed
```

### Sign In

```javascript
// Email/Password sign in
const { data, error } = await supabase.auth.signInWithPassword({
  email: "user@example.com",
  password: "password123",
});

// Magic link (passwordless)
const { error } = await supabase.auth.signInWithOtp({
  email: "user@example.com",
  options: {
    emailRedirectTo: "https://yourapp.com/welcome",
  },
});

// OAuth with Google
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: "google",
  options: {
    redirectTo: "https://yourapp.com/auth/callback",
  },
});

// OAuth with GitHub
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: "github",
});
```

### Sign Out

```javascript
const { error } = await supabase.auth.signOut();
```

### Session Management

```javascript
// Get current session
const {
  data: { session },
  error,
} = await supabase.auth.getSession();

// Get current user
const {
  data: { user },
  error,
} = await supabase.auth.getUser();

// Listen to auth state changes
supabase.auth.onAuthStateChange((event, session) => {
  console.log("Auth event:", event);
  // event: 'SIGNED_IN', 'SIGNED_OUT', 'USER_UPDATED', 'PASSWORD_RECOVERY'

  if (session) {
    console.log("User is logged in:", session.user.email);
  }
});

// Refresh session
const { data, error } = await supabase.auth.refreshSession();
```

### Update User

```javascript
// Update user email
const { data, error } = await supabase.auth.updateUser({
  email: "newemail@example.com",
});

// Update user password
const { data, error } = await supabase.auth.updateUser({
  password: "newpassword123",
});

// Update user metadata
const { data, error } = await supabase.auth.updateUser({
  data: { full_name: "New Name", avatar_url: "new-avatar.jpg" },
});
```

### Password Reset

```javascript
// Request password reset
const { error } = await supabase.auth.resetPasswordForEmail(
  "user@example.com",
  {
    redirectTo: "https://yourapp.com/reset-password",
  },
);

// Update password with recovery token
const { data, error } = await supabase.auth.updateUser({
  password: "newpassword123",
});
```

### Protected Route Example

```jsx
// components/ProtectedRoute.jsx
import { useEffect, useState } from "react";
import { supabase } from "../lib/supabase";

export function ProtectedRoute({ children }) {
  const [loading, setLoading] = useState(true);
  const [user, setUser] = useState(null);

  useEffect(() => {
    // Check current session
    supabase.auth.getSession().then(({ data: { session } }) => {
      setUser(session?.user ?? null);
      setLoading(false);
    });

    // Listen for auth changes
    const {
      data: { subscription },
    } = supabase.auth.onAuthStateChange((_event, session) => {
      setUser(session?.user ?? null);
    });

    return () => subscription.unsubscribe();
  }, []);

  if (loading) {
    return <div>Loading...</div>;
  }

  if (!user) {
    return <Navigate to="/login" />;
  }

  return children;
}
```

---

## Row Level Security (RLS)

### What is RLS?

Row Level Security is PostgreSQL's built-in feature that restricts which rows a user can access. It's the most important security feature in Supabase.

**How RLS works:**

1. You enable RLS on a table
2. You create policies that define who can access what
3. Supabase automatically applies these policies based on the authenticated user

### Enabling RLS

```sql
-- Enable RLS on a table
ALTER TABLE todos ENABLE ROW LEVEL SECURITY;

-- Disable RLS (not recommended for production)
ALTER TABLE todos DISABLE ROW LEVEL SECURITY;
```

### Basic Policies

```sql
-- Policy for SELECT (read)
CREATE POLICY "Users can view their own todos"
ON todos
FOR SELECT
USING (auth.uid() = user_id);

-- Policy for INSERT (create)
CREATE POLICY "Users can create todos"
ON todos
FOR INSERT
WITH CHECK (auth.uid() = user_id);

-- Policy for UPDATE (modify)
CREATE POLICY "Users can update their own todos"
ON todos
FOR UPDATE
USING (auth.uid() = user_id);

-- Policy for DELETE (remove)
CREATE POLICY "Users can delete their own todos"
ON todos
FOR DELETE
USING (auth.uid() = user_id);
```

### Policy for All Operations

```sql
-- Single policy for all operations
CREATE POLICY "Users can manage their own todos"
ON todos
USING (auth.uid() = user_id)
WITH CHECK (auth.uid() = user_id);
```

### Public Access Policies

```sql
-- Allow anyone to read (no authentication needed)
CREATE POLICY "Public profiles are viewable by everyone"
ON profiles
FOR SELECT
USING (true);

-- Allow authenticated users to insert
CREATE POLICY "Authenticated users can create profiles"
ON profiles
FOR INSERT
WITH CHECK (auth.role() = 'authenticated');
```

### Role-Based Policies

```sql
-- Admin policy (using custom claim)
CREATE POLICY "Admins can do anything"
ON todos
USING (auth.jwt() ->> 'role' = 'admin');

-- Moderator policy
CREATE POLICY "Moderators can delete any todo"
ON todos
FOR DELETE
USING (auth.jwt() ->> 'role' = 'moderator');
```

### Testing RLS Policies

```sql
-- Test as anonymous user
SET ROLE anon;
SELECT * FROM todos;  -- Should see limited results

-- Test as authenticated user
SET ROLE authenticated;
SET LOCAL request.jwt.claims = '{"sub": "user-123"}';
SELECT * FROM todos;  -- Should see only user-123's todos
```

### Complete RLS Example

```sql
-- Create profiles table
CREATE TABLE profiles (
  id UUID REFERENCES auth.users PRIMARY KEY,
  username TEXT UNIQUE,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Anyone can view profiles
CREATE POLICY "Public profiles are viewable by everyone"
ON profiles FOR SELECT USING (true);

-- Users can insert their own profile
CREATE POLICY "Users can insert their own profile"
ON profiles FOR INSERT
WITH CHECK (auth.uid() = id);

-- Users can update their own profile
CREATE POLICY "Users can update their own profile"
ON profiles FOR UPDATE
USING (auth.uid() = id);
```

---

## Realtime Subscriptions

### Overview

Supabase Realtime allows you to listen to database changes in real-time using WebSockets.

**Three ways to use Realtime:**

1. **Broadcast** - Send messages between clients (chat, cursors)
2. **Presence** - Track online users (who's online)
3. **Postgres Changes** - Listen to database changes (INSERT, UPDATE, DELETE)

### Basic Setup

```javascript
import { supabase } from "./lib/supabase";

// Create a channel
const channel = supabase.channel("room:lobby", {
  config: { private: true }, // Recommended for production
});
```

### 1. Postgres Changes (Database Changes)

Listen to INSERT, UPDATE, DELETE operations on your tables:

```javascript
// Listen to all changes on a table
const channel = supabase
  .channel("db-changes")
  .on(
    "postgres_changes",
    {
      event: "*", // 'INSERT', 'UPDATE', 'DELETE', or '*'
      schema: "public",
      table: "todos",
    },
    (payload) => {
      console.log("Change received!", payload);
      console.log("Event type:", payload.eventType);
      console.log("New data:", payload.new);
      console.log("Old data:", payload.old);
    },
  )
  .subscribe()

  // Listen only to INSERT events
  .on(
    "postgres_changes",
    { event: "INSERT", schema: "public", table: "todos" },
    (payload) => {
      console.log("New todo added:", payload.new);
    },
  )

  // Listen with filter (only rows matching condition)
  .on(
    "postgres_changes",
    {
      event: "UPDATE",
      schema: "public",
      table: "todos",
      filter: `user_id=eq.${userId}`,
    },
    (payload) => {
      console.log("Your todo was updated:", payload.new);
    },
  );
```

### 2. Broadcast (Custom Messages)

Send and receive custom messages between clients:

```javascript
// Listen for broadcast messages
const channel = supabase
  .channel("chat-room")
  .on("broadcast", { event: "message" }, (payload) => {
    console.log("New message:", payload.payload);
    // payload.payload = { text: 'Hello!', user: 'John', timestamp: ... }
  })
  .subscribe();

// Send a broadcast message
await channel.send({
  type: "broadcast",
  event: "message",
  payload: {
    text: "Hello, world!",
    user: currentUser.name,
    timestamp: new Date().toISOString(),
  },
});
```

### 3. Presence (Track Users)

Track which users are online and their state:

```javascript
// Listen for presence changes
const channel = supabase
  .channel("room-presence")
  .on("presence", { event: "sync" }, () => {
    const newState = channel.presenceState();
    console.log("Current presence state:", newState);
    // newState = {
    //   'user-123': [{ user_id: 'user-123', online_at: '...' }],
    //   'user-456': [{ user_id: 'user-456', online_at: '...' }]
    // }
  })
  .subscribe(async (status) => {
    if (status !== "SUBSCRIBED") return;

    // Track current user's presence
    const presenceTrackStatus = await channel.track({
      user_id: currentUser.id,
      online_at: new Date().toISOString(),
    });
  });

// Update presence (e.g., typing indicator)
await channel.track({
  user_id: currentUser.id,
  status: "typing",
});

// Stop tracking presence
await channel.untrack();
```

### Cleaning Up Subscriptions

Always unsubscribe when components unmount to prevent memory leaks:

```jsx
import { useEffect } from "react";
import { supabase } from "./lib/supabase";

function TodoList() {
  useEffect(() => {
    const channel = supabase
      .channel("todos-changes")
      .on(
        "postgres_changes",
        { event: "*", schema: "public", table: "todos" },
        (payload) => {
          console.log("Change:", payload);
        },
      )
      .subscribe();

    // Cleanup on unmount
    return () => {
      supabase.removeChannel(channel);
    };
  }, []);

  return <div>Todo List</div>;
}
```

### Real-time Chat Example

```jsx
import { useEffect, useState } from "react";
import { supabase } from "./lib/supabase";

function ChatRoom({ roomId, currentUser }) {
  const [messages, setMessages] = useState([]);
  const [onlineUsers, setOnlineUsers] = useState([]);

  useEffect(() => {
    // Load existing messages
    loadMessages();

    // Subscribe to new messages
    const messageChannel = supabase
      .channel(`room:${roomId}`)
      .on("broadcast", { event: "message" }, (payload) => {
        setMessages((prev) => [...prev, payload.payload]);
      })
      .subscribe();

    // Track presence
    const presenceChannel = supabase
      .channel(`presence:${roomId}`)
      .on("presence", { event: "sync" }, () => {
        const state = presenceChannel.presenceState();
        const users = Object.values(state)
          .flat()
          .map((u) => u.user);
        setOnlineUsers(users);
      })
      .subscribe(async (status) => {
        if (status === "SUBSCRIBED") {
          await presenceChannel.track({
            user: currentUser.name,
            id: currentUser.id,
          });
        }
      });

    return () => {
      supabase.removeChannel(messageChannel);
      supabase.removeChannel(presenceChannel);
    };
  }, [roomId]);

  const sendMessage = async (text) => {
    await supabase.channel(`room:${roomId}`).send({
      type: "broadcast",
      event: "message",
      payload: {
        text,
        user: currentUser.name,
        timestamp: new Date().toISOString(),
      },
    });
  };

  return (
    <div>
      <div>Online: {onlineUsers.map((u) => u.user).join(", ")}</div>
      <div className="messages">
        {messages.map((msg, i) => (
          <div key={i}>
            <strong>{msg.user}:</strong> {msg.text}
          </div>
        ))}
      </div>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          sendMessage(e.target.message.value);
          e.target.reset();
        }}
      >
        <input name="message" placeholder="Type a message..." />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

---

## Storage

### Overview

Supabase Storage allows you to upload, download, and manage files.

### Creating a Bucket

```javascript
// Create a public bucket (anyone can read)
const { data, error } = await supabase.storage.createBucket("avatars", {
  public: true,
});

// Create a private bucket (requires authentication)
const { data, error } = await supabase.storage.createBucket("documents", {
  public: false,
});
```

### Uploading Files

```javascript
// Upload a file
const file = event.target.files[0];
const { data, error } = await supabase.storage
  .from("avatars")
  .upload(`public/${file.name}`, file, {
    cacheControl: "3600",
    upsert: false,
  });

// Upload with custom content type
const { data, error } = await supabase.storage
  .from("avatars")
  .upload("user-avatar.jpg", file, {
    contentType: "image/jpeg",
  });

// Upload from URL
const { data, error } = await supabase.storage
  .from("avatars")
  .upload("profile-pic.jpg", imageUrl);
```

### Downloading Files

```javascript
// Get public URL (for public buckets)
const { data } = supabase.storage
  .from("avatars")
  .getPublicUrl("folder/avatar.jpg");

// Download file (for private buckets)
const { data, error } = await supabase.storage
  .from("documents")
  .download("secret-file.pdf");

// Create signed URL (temporary access)
const { data, error } = await supabase.storage
  .from("documents")
  .createSignedUrl("secret-file.pdf", 60); // expires in 60 seconds
```

### Listing Files

```javascript
// List files in a folder
const { data, error } = await supabase.storage.from("avatars").list("folder", {
  limit: 100,
  offset: 0,
  sortBy: { column: "name", order: "asc" },
});
```

### Deleting Files

```javascript
// Delete a single file
const { data, error } = await supabase.storage
  .from("avatars")
  .remove(["old-avatar.jpg"]);

// Delete multiple files
const { data, error } = await supabase.storage
  .from("avatars")
  .remove(["file1.jpg", "file2.jpg", "file3.jpg"]);
```

### Storage RLS Policies

```sql
-- Allow authenticated users to upload avatars
CREATE POLICY "Users can upload avatars"
ON storage.objects
FOR INSERT
WITH CHECK (
  bucket_id = 'avatars'
  AND auth.role() = 'authenticated'
);

-- Allow public to view avatars
CREATE POLICY "Public can view avatars"
ON storage.objects
FOR SELECT
USING (bucket_id = 'avatars');

-- Users can delete their own files
CREATE POLICY "Users can delete their own files"
ON storage.objects
FOR DELETE
USING (
  bucket_id = 'avatars'
  AND (storage.foldername(name))[1] = auth.uid()::text
);
```

---

## Edge Functions

### Overview

Supabase Edge Functions are TypeScript functions deployed globally and triggered via HTTP.

### Creating a Function

```bash
# Install Supabase CLI
npm install -g supabase

# Login to Supabase
supabase login

# Create a new function
supabase functions new my-function
```

### Basic Function Example

```typescript
// supabase/functions/my-function/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";

serve(async (req) => {
  const { name } = await req.json();

  return new Response(JSON.stringify({ message: `Hello ${name}!` }), {
    headers: { "Content-Type": "application/json" },
  });
});
```

### Function with Database Access

```typescript
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

serve(async (req) => {
  // Create Supabase client with service role (bypasses RLS)
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL") ?? "",
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY") ?? "",
  );

  // Get authorization header
  const authHeader = req.headers.get("Authorization");
  const token = authHeader?.replace("Bearer ", "");

  // Get user from token
  const {
    data: { user },
    error: userError,
  } = await supabase.auth.getUser(token);

  if (userError) {
    return new Response(JSON.stringify({ error: "Unauthorized" }), {
      status: 401,
    });
  }

  // Query database
  const { data: todos, error } = await supabase
    .from("todos")
    .select("*")
    .eq("user_id", user.id);

  return new Response(JSON.stringify({ todos }), {
    headers: { "Content-Type": "application/json" },
  });
});
```

### Deploying and Invoking Functions

```bash
# Deploy function
supabase functions deploy my-function

# Set secrets
supabase secrets set MY_SECRET_KEY=value

# Invoke locally
supabase functions serve my-function

# Invoke from client
const { data, error } = await supabase.functions.invoke('my-function', {
  body: { name: 'John' },
})
```

---

## Complete Examples

### Example 1: Todo App with Auth

```jsx
// App.jsx
import { useEffect, useState } from "react";
import { supabase } from "./lib/supabase";

function App() {
  const [session, setSession] = useState(null);
  const [todos, setTodos] = useState([]);
  const [newTodo, setNewTodo] = useState("");

  useEffect(() => {
    // Check current session
    supabase.auth.getSession().then(({ data: { session } }) => {
      setSession(session);
    });

    // Listen for auth changes
    const {
      data: { subscription },
    } = supabase.auth.onAuthStateChange((_event, session) => {
      setSession(session);
    });

    return () => subscription.unsubscribe();
  }, []);

  useEffect(() => {
    if (session?.user) {
      fetchTodos();

      // Subscribe to real-time changes
      const channel = supabase
        .channel("todos-changes")
        .on(
          "postgres_changes",
          {
            event: "*",
            schema: "public",
            table: "todos",
            filter: `user_id=eq.${session.user.id}`,
          },
          () => fetchTodos(),
        )
        .subscribe();

      return () => supabase.removeChannel(channel);
    }
  }, [session]);

  async function fetchTodos() {
    const { data } = await supabase
      .from("todos")
      .select("*")
      .eq("user_id", session.user.id)
      .order("created_at", { ascending: false });

    setTodos(data || []);
  }

  async function addTodo() {
    if (!newTodo.trim()) return;

    await supabase.from("todos").insert({
      title: newTodo,
      user_id: session.user.id,
    });

    setNewTodo("");
  }

  async function toggleTodo(id, completed) {
    await supabase.from("todos").update({ completed: !completed }).eq("id", id);
  }

  async function deleteTodo(id) {
    await supabase.from("todos").delete().eq("id", id);
  }

  if (!session) {
    return <AuthScreen onLogin={() => setSession(true)} />;
  }

  return (
    <div className="app">
      <div className="header">
        <h1>Todo App</h1>
        <button onClick={() => supabase.auth.signOut()}>Sign Out</button>
      </div>

      <div className="add-todo">
        <input
          value={newTodo}
          onChange={(e) => setNewTodo(e.target.value)}
          onKeyPress={(e) => e.key === "Enter" && addTodo()}
          placeholder="Add a new todo..."
        />
        <button onClick={addTodo}>Add</button>
      </div>

      <div className="todo-list">
        {todos.map((todo) => (
          <div key={todo.id} className="todo-item">
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id, todo.completed)}
            />
            <span
              style={{
                textDecoration: todo.completed ? "line-through" : "none",
              }}
            >
              {todo.title}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </div>
        ))}
      </div>
    </div>
  );
}

function AuthScreen({ onLogin }) {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [loading, setLoading] = useState(false);

  async function handleSignUp() {
    setLoading(true);
    const { error } = await supabase.auth.signUp({ email, password });
    if (error) alert(error.message);
    setLoading(false);
  }

  async function handleSignIn() {
    setLoading(true);
    const { error } = await supabase.auth.signInWithPassword({
      email,
      password,
    });
    if (error) alert(error.message);
    else onLogin();
    setLoading(false);
  }

  return (
    <div className="auth">
      <h2>Welcome to Todo App</h2>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      <button onClick={handleSignIn} disabled={loading}>
        Sign In
      </button>
      <button onClick={handleSignUp} disabled={loading}>
        Sign Up
      </button>
    </div>
  );
}
```

### Example 2: Real-time Chat

```jsx
// ChatApp.jsx
import { useEffect, useState } from "react";
import { supabase } from "./lib/supabase";

function ChatApp() {
  const [messages, setMessages] = useState([]);
  const [onlineUsers, setOnlineUsers] = useState([]);
  const [newMessage, setNewMessage] = useState("");
  const [user, setUser] = useState(null);

  useEffect(() => {
    // Get current user
    supabase.auth.getUser().then(({ data }) => {
      setUser(data.user);
    });

    // Load existing messages
    loadMessages();

    // Subscribe to new messages via Broadcast
    const messageChannel = supabase
      .channel("chat:lobby")
      .on("broadcast", { event: "message" }, (payload) => {
        setMessages((prev) => [...prev, payload.payload]);
      })
      .subscribe();

    // Track presence
    const presenceChannel = supabase
      .channel("chat:presence")
      .on("presence", { event: "sync" }, () => {
        const state = presenceChannel.presenceState();
        const users = Object.values(state).flat();
        setOnlineUsers(users);
      })
      .subscribe(async (status) => {
        if (status === "SUBSCRIBED" && user) {
          await presenceChannel.track({
            user_id: user.id,
            email: user.email,
            online_at: new Date().toISOString(),
          });
        }
      });

    return () => {
      supabase.removeChannel(messageChannel);
      supabase.removeChannel(presenceChannel);
    };
  }, [user]);

  async function loadMessages() {
    // In production, you'd store messages in a table
    // For demo, we'll just start with an empty array
    setMessages([]);
  }

  async function sendMessage() {
    if (!newMessage.trim()) return;

    await supabase.channel("chat:lobby").send({
      type: "broadcast",
      event: "message",
      payload: {
        id: Date.now(),
        text: newMessage,
        user_email: user?.email,
        timestamp: new Date().toISOString(),
      },
    });

    setNewMessage("");
  }

  return (
    <div className="chat-app">
      <div className="sidebar">
        <h3>Online Users ({onlineUsers.length})</h3>
        {onlineUsers.map((user, i) => (
          <div key={i} className="online-user">
            {user.email}
          </div>
        ))}
      </div>

      <div className="chat-area">
        <div className="messages">
          {messages.map((msg) => (
            <div key={msg.id} className="message">
              <strong>{msg.user_email}:</strong> {msg.text}
              <small>{new Date(msg.timestamp).toLocaleTimeString()}</small>
            </div>
          ))}
        </div>

        <div className="message-input">
          <input
            value={newMessage}
            onChange={(e) => setNewMessage(e.target.value)}
            onKeyPress={(e) => e.key === "Enter" && sendMessage()}
            placeholder="Type a message..."
          />
          <button onClick={sendMessage}>Send</button>
        </div>
      </div>
    </div>
  );
}
```

---

## API Reference

### Client Initialization

| Method                            | Description                      |
| --------------------------------- | -------------------------------- |
| `createClient(url, key, options)` | Creates Supabase client instance |

### Database (CRUD)

| Method                    | Description                    |
| ------------------------- | ------------------------------ |
| `.from(table)`            | Select table to query          |
| `.select(columns)`        | Select columns (default: '\*') |
| `.insert(values)`         | Insert rows                    |
| `.update(values)`         | Update rows                    |
| `.delete()`               | Delete rows                    |
| `.upsert(values)`         | Insert or update               |
| `.eq(column, value)`      | Equal to filter                |
| `.neq(column, value)`     | Not equal to filter            |
| `.gt(column, value)`      | Greater than filter            |
| `.gte(column, value)`     | Greater than or equal          |
| `.lt(column, value)`      | Less than filter               |
| `.lte(column, value)`     | Less than or equal             |
| `.like(column, pattern)`  | Pattern matching               |
| `.ilike(column, pattern)` | Case-insensitive pattern       |
| `.in(column, values)`     | In array filter                |
| `.order(column, options)` | Sort results                   |
| `.limit(count)`           | Limit results                  |
| `.range(start, end)`      | Pagination                     |
| `.single()`               | Expect single row              |
| `.maybeSingle()`          | Single row or null             |

### Authentication

| Method                          | Description                 |
| ------------------------------- | --------------------------- |
| `.auth.signUp()`                | Create new user             |
| `.auth.signInWithPassword()`    | Sign in with email/password |
| `.auth.signInWithOtp()`         | Send magic link             |
| `.auth.signInWithOAuth()`       | OAuth sign in               |
| `.auth.signOut()`               | Sign out user               |
| `.auth.getSession()`            | Get current session         |
| `.auth.getUser()`               | Get current user            |
| `.auth.updateUser()`            | Update user data            |
| `.auth.resetPasswordForEmail()` | Send password reset         |
| `.auth.onAuthStateChange()`     | Listen to auth changes      |

### Realtime

| Method                        | Description            |
| ----------------------------- | ---------------------- |
| `.channel(name)`              | Create or get channel  |
| `.on(type, filter, callback)` | Add event listener     |
| `.subscribe()`                | Start receiving events |
| `.send(message)`              | Send broadcast message |
| `.track(metadata)`            | Track presence         |
| `.untrack()`                  | Stop tracking presence |
| `.removeChannel(channel)`     | Clean up channel       |

### Storage

| Method                              | Description           |
| ----------------------------------- | --------------------- |
| `.storage.createBucket()`           | Create storage bucket |
| `.storage.from(bucket)`             | Select bucket         |
| `.upload(path, file)`               | Upload file           |
| `.download(path)`                   | Download file         |
| `.getPublicUrl(path)`               | Get public URL        |
| `.createSignedUrl(path, expiresIn)` | Create temporary URL  |
| `.list(path)`                       | List files            |
| `.remove(paths)`                    | Delete files          |

---

## Troubleshooting

**Problem 1:** RLS policy blocking access

**Solution:** Check your policies and ensure the user is authenticated

**Problem 2:** Realtime not working

**Solution:** Enable Realtime for your table in the Dashboard (Database → Replication)

**Problem 3:** TypeScript errors

**Solution:** Install `@supabase/supabase-js` types are included

**Problem 4:** CORS errors

**Solution:** Add your domain to CORS allowed origins in Dashboard settings

---

## Useful Resources

- Supabase Docs: https://supabase.com/docs
- GitHub Repository: https://github.com/supabase/supabase
- Supabase JS Client: https://github.com/supabase/supabase-js
- Discord Community: https://discord.supabase.com

---

Created for beginners learning Supabase
