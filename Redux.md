# Redux - Complete Beginner's Guide

A comprehensive from-scratch guide to Redux Toolkit.

## Table of Contents
1. [What is Redux](#what-is-redux)
2. [Core Concepts](#core-concepts)
3. [Installation](#installation)
4. [Basic Setup](#basic-setup)
5. [Creating Slices](#creating-slices)
6. [Using Redux in Components](#using-redux-in-components)
7. [Actions Explained](#actions-explained)
8. [Reducers Explained](#reducers-explained)
9. [Selectors Explained](#selectors-explained)
10. [Async Actions with createAsyncThunk](#async-actions-with-createasyncthunk)
11. [Middleware](#middleware)
12. [DevTools](#devtools)
13. [Complete Examples](#complete-examples)
14. [API Reference](#api-reference)

---

## What is Redux

Redux is a library for **managing global state** in your React app.

**What Redux does:**
- Stores all app state in one central location (store)
- Makes state available to any component
- Updates state in a predictable way (actions + reducers)
- Provides devtools for debugging

**The Redux Flow:**
```
Component → dispatches Action → Reducer updates State → Component re-renders
```

**Without Redux (prop drilling):**
```jsx
// Need to pass data through many components
<App>
  <Dashboard user={user}>
    <Profile user={user}>
      <UserInfo user={user}>  // Finally reaches here
```

**With Redux:**
```jsx
// Any component can access state directly
function UserInfo() {
  const user = useSelector(state => state.user);
  // No prop drilling needed!
}
```

## Core Concepts

### The 3 Principles of Redux

1. **Single Source of Truth** - One store holds all state
2. **State is Read-Only** - Can only change by dispatching actions
3. **Changes are Made with Pure Functions** - Reducers specify how state changes

### The 4 Main Parts

```jsx
Store        // 1. Container that holds all state
Actions      // 2. Plain objects that describe what happened
Reducers     // 3. Functions that specify how state changes
Dispatch     // 4. Function that sends actions to the store
```

## Installation

```bash
npm install @reduxjs/toolkit react-redux
```

## Basic Setup

### Step 1: Create the Store

```jsx
// store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './features/counter/counterSlice';
import userReducer from './features/user/userSlice';

// 1. Create store with reducers
export const store = configureStore({
  reducer: {
    counter: counterReducer,  // state.counter
    user: userReducer,        // state.user
  },
});
```

### Step 2: Provide Store to App

```jsx
// main.jsx or index.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

// 2. Wrap app with Provider
ReactDOM.createRoot(document.getElementById('root')).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

### Step 3: Create a Slice

```jsx
// features/counter/counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

// 3. Create slice (state + reducers)
const counterSlice = createSlice({
  name: 'counter',           // Name of this slice
  initialState: {            // Initial state
    value: 0
  },
  reducers: {                // Functions that update state
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    }
  }
});

// Export actions
export const { increment, decrement, incrementByAmount } = counterSlice.actions;

// Export reducer
export default counterSlice.reducer;
```

### Step 4: Use in Component

```jsx
// components/Counter.jsx
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from '../features/counter/counterSlice';

function Counter() {
  // 4a. Read state from store
  const count = useSelector((state) => state.counter.value);

  // 4b. Get dispatch function
  const dispatch = useDispatch();

  // 4c. Dispatch actions to update state
  return (
    <div>
      <div>Count: {count}</div>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}
```

---

## Creating Slices

### Basic Slice

```jsx
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  value: 0,
  status: 'idle'
};

const counterSlice = createSlice({
  name: 'counter',           // Used in action types: 'counter/increment'
  initialState,              // Initial state
  reducers: {                // Each reducer becomes an action
    increment: (state) => {
      // Immer allows "mutating" syntax
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    reset: (state) => {
      state.value = 0;
    },
    setValue: (state, action) => {
      state.value = action.payload;
    }
  }
});

// Action creators are automatically generated
export const { increment, decrement, reset, setValue } = counterSlice.actions;

// Reducer is automatically generated
export default counterSlice.reducer;
```

### Slice with Complex State

```jsx
const todoSlice = createSlice({
  name: 'todos',
  initialState: {
    items: [],
    filter: 'all',
    searchTerm: '',
    loading: false,
    error: null
  },
  reducers: {
    // Add todo
    addTodo: (state, action) => {
      state.items.push({
        id: Date.now(),
        text: action.payload,
        completed: false,
        createdAt: new Date().toISOString()
      });
    },

    // Toggle todo completion
    toggleTodo: (state, action) => {
      const todo = state.items.find(todo => todo.id === action.payload);
      if (todo) {
        todo.completed = !todo.completed;
      }
    },

    // Delete todo
    deleteTodo: (state, action) => {
      state.items = state.items.filter(todo => todo.id !== action.payload);
    },

    // Update todo text
    updateTodo: (state, action) => {
      const { id, text } = action.payload;
      const todo = state.items.find(todo => todo.id === id);
      if (todo) {
        todo.text = text;
      }
    },

    // Set filter
    setFilter: (state, action) => {
      state.filter = action.payload;
    },

    // Set search term
    setSearchTerm: (state, action) => {
      state.searchTerm = action.payload;
    },

    // Clear all completed todos
    clearCompleted: (state) => {
      state.items = state.items.filter(todo => !todo.completed);
    },

    // Toggle all todos
    toggleAll: (state, action) => {
      state.items.forEach(todo => {
        todo.completed = action.payload;
      });
    }
  }
});

export const {
  addTodo,
  toggleTodo,
  deleteTodo,
  updateTodo,
  setFilter,
  setSearchTerm,
  clearCompleted,
  toggleAll
} = todoSlice.actions;

export default todoSlice.reducer;
```

---

## Using Redux in Components

### useSelector - Reading State

```jsx
import { useSelector } from 'react-redux';

function UserProfile() {
  // Basic selector - get entire slice
  const counter = useSelector(state => state.counter);

  // Select specific value
  const count = useSelector(state => state.counter.value);

  // Select multiple values
  const { value, status } = useSelector(state => state.counter);

  // Select nested data
  const userName = useSelector(state => state.user.profile.name);

  // Select with calculation
  const doubledCount = useSelector(state => state.counter.value * 2);

  // Select filtered data
  const activeTodos = useSelector(state =>
    state.todos.items.filter(todo => !todo.completed)
  );

  // Select with memoization
  const todoStats = useSelector(state => {
    const todos = state.todos.items;
    return {
      total: todos.length,
      completed: todos.filter(t => t.completed).length,
      active: todos.filter(t => !t.completed).length
    };
  });

  return <div>Count: {count}</div>;
}
```

### useDispatch - Updating State

```jsx
import { useDispatch } from 'react-redux';
import { increment, decrement, setValue } from './counterSlice';

function CounterControls() {
  const dispatch = useDispatch();

  // Dispatch without payload
  const handleIncrement = () => {
    dispatch(increment());
  };

  // Dispatch with payload
  const handleSetValue = (value) => {
    dispatch(setValue(value));
  };

  // Dispatch multiple actions
  const handleReset = () => {
    dispatch(setValue(0));
    dispatch(increment()); // Won't work as expected, just example
  };

  return (
    <div>
      <button onClick={handleIncrement}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
      <button onClick={() => dispatch(setValue(100))}>Set to 100</button>
    </div>
  );
}
```

### Complete Component Example

```jsx
import { useSelector, useDispatch } from 'react-redux';
import { useState } from 'react';
import { addTodo, toggleTodo, deleteTodo, setFilter } from './todoSlice';

function TodoApp() {
  const [inputText, setInputText] = useState('');
  const dispatch = useDispatch();

  // Select data from store
  const todos = useSelector(state => state.todos.items);
  const filter = useSelector(state => state.todos.filter);

  // Filtered todos based on filter
  const filteredTodos = useSelector(state => {
    const todos = state.todos.items;
    const filter = state.todos.filter;

    if (filter === 'active') {
      return todos.filter(todo => !todo.completed);
    }
    if (filter === 'completed') {
      return todos.filter(todo => todo.completed);
    }
    return todos;
  });

  // Statistics
  const stats = useSelector(state => {
    const todos = state.todos.items;
    return {
      total: todos.length,
      completed: todos.filter(t => t.completed).length,
      active: todos.filter(t => !t.completed).length
    };
  });

  const handleAddTodo = () => {
    if (inputText.trim()) {
      dispatch(addTodo(inputText));
      setInputText('');
    }
  };

  const handleToggleTodo = (id) => {
    dispatch(toggleTodo(id));
  };

  const handleDeleteTodo = (id) => {
    dispatch(deleteTodo(id));
  };

  return (
    <div>
      <h1>Todo App</h1>

      {/* Stats */}
      <div>
        <span>Total: {stats.total}</span>
        <span>Active: {stats.active}</span>
        <span>Completed: {stats.completed}</span>
      </div>

      {/* Add Todo */}
      <div>
        <input
          value={inputText}
          onChange={(e) => setInputText(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && handleAddTodo()}
        />
        <button onClick={handleAddTodo}>Add Todo</button>
      </div>

      {/* Filters */}
      <div>
        <button onClick={() => dispatch(setFilter('all'))}>All</button>
        <button onClick={() => dispatch(setFilter('active'))}>Active</button>
        <button onClick={() => dispatch(setFilter('completed'))}>Completed</button>
      </div>

      {/* Todo List */}
      <ul>
        {filteredTodos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => handleToggleTodo(todo.id)}
            />
            <span style={{
              textDecoration: todo.completed ? 'line-through' : 'none'
            }}>
              {todo.text}
            </span>
            <button onClick={() => handleDeleteTodo(todo.id)}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## Actions Explained

### What is an Action?

An action is a plain JavaScript object that describes what happened.

```jsx
// Action structure
{
  type: 'counter/increment',  // Required: describes the action
  payload: 5,                  // Optional: data needed for the update
  meta: {},                    // Optional: metadata
  error: false                 // Optional: error flag
}
```

### Action Creators

```jsx
// Action creators are functions that create action objects

// Without payload
const incrementAction = () => ({
  type: 'counter/increment'
});

// With payload
const incrementByAmountAction = (amount) => ({
  type: 'counter/incrementByAmount',
  payload: amount
});

// Using slice (automatic action creators)
const { increment, incrementByAmount } = counterSlice.actions;

// Dispatch actions
dispatch(increment());                    // { type: 'counter/increment' }
dispatch(incrementByAmount(5));          // { type: 'counter/incrementByAmount', payload: 5 }
```

### Understanding Action Flow

```jsx
// 1. Component dispatches action
dispatch(increment())

// 2. Action object is created
// { type: 'counter/increment' }

// 3. Action is sent to reducer
// Reducer sees type 'counter/increment'

// 4. Reducer updates state
// state.value += 1

// 5. Components re-render with new state
```

---

## Reducers Explained

### What is a Reducer?

A reducer is a pure function that takes the current state and an action, and returns the new state.

```jsx
// Reducer signature
(state, action) => newState

// Example reducer
function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case 'counter/increment':
      return { ...state, value: state.value + 1 };
    case 'counter/decrement':
      return { ...state, value: state.value - 1 };
    default:
      return state;
  }
}
```

### Reducer with createSlice (Recommended)

```jsx
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    // Immer allows "mutating" syntax
    increment: (state) => {
      state.value += 1;        // Actually immutable under the hood
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    }
  }
});

// What Immer does:
// state.value += 1  ->  returns { ...state, value: state.value + 1 }
```

### Manual Reducer (Without createSlice)

```jsx
// Action types
const INCREMENT = 'counter/increment';
const DECREMENT = 'counter/decrement';

// Action creators
export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });

// Reducer
const initialState = { value: 0 };

export default function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, value: state.value + 1 };
    case DECREMENT:
      return { ...state, value: state.value - 1 };
    default:
      return state;
  }
}
```

### Reducer Rules (Important!)

```jsx
// REDUCERS MUST BE PURE FUNCTIONS:

// ✅ DO - Calculate new state based on old state
(state) => ({ ...state, value: state.value + 1 })

// ✅ DO - Return new objects/arrays
(state) => ({ ...state, items: [...state.items, newItem] })

// ❌ DON'T - Mutate state directly
(state) => {
  state.value = 5;  // Wrong in manual reducer (OK with Immer)
  return state;
}

// ❌ DON'T - Do side effects
(state) => {
  console.log(state);     // No side effects!
  localStorage.setItem(); // No side effects!
  fetch('/api');          // No side effects!
  return state;
}

// ❌ DON'T - Call non-pure functions
(state) => {
  const random = Math.random();  // Not pure!
  return { ...state, value: random };
}
```

---

## Selectors Explained

### Basic Selectors

```jsx
// Selectors are functions that extract specific pieces of state

// Basic selector
const selectCount = (state) => state.counter.value;

// Using in component
const count = useSelector(selectCount);

// Inline selector
const count = useSelector(state => state.counter.value);
```

### Memoized Selectors with createSelector

```jsx
import { createSelector } from '@reduxjs/toolkit';

// Base selectors (reusable)
const selectTodos = (state) => state.todos.items;
const selectFilter = (state) => state.todos.filter;
const selectSearchTerm = (state) => state.todos.searchTerm;

// Memoized derived selector
// Only recalculates when inputs change
const selectFilteredTodos = createSelector(
  [selectTodos, selectFilter, selectSearchTerm],
  (todos, filter, searchTerm) => {
    let filtered = todos;

    // Apply filter
    if (filter === 'active') {
      filtered = filtered.filter(todo => !todo.completed);
    } else if (filter === 'completed') {
      filtered = filtered.filter(todo => todo.completed);
    }

    // Apply search
    if (searchTerm) {
      filtered = filtered.filter(todo =>
        todo.text.toLowerCase().includes(searchTerm.toLowerCase())
      );
    }

    return filtered;
  }
);

// Another derived selector
const selectTodoStats = createSelector(
  [selectTodos],
  (todos) => ({
    total: todos.length,
    completed: todos.filter(t => t.completed).length,
    active: todos.filter(t => !t.completed).length,
    percentComplete: todos.length
      ? (todos.filter(t => t.completed).length / todos.length) * 100
      : 0
  })
);

// Using in component
function TodoList() {
  const filteredTodos = useSelector(selectFilteredTodos);
  const stats = useSelector(selectTodoStats);

  return (
    <div>
      <div>Progress: {stats.percentComplete}%</div>
      {filteredTodos.map(todo => ...)}
    </div>
  );
}
```

### Selector with Props

```jsx
// Selector factory (returns a selector function)
const selectTodoById = (todoId) => (state) =>
  state.todos.items.find(todo => todo.id === todoId);

// Using in component
function TodoItem({ id }) {
  const todo = useSelector(selectTodoById(id));

  if (!todo) return null;
  return <div>{todo.text}</div>;
}

// Memoized version
const makeSelectTodoById = () => {
  return createSelector(
    [(state, id) => state.todos.items, (state, id) => id],
    (todos, id) => todos.find(todo => todo.id === id)
  );
};

// Usage with useMemo
function TodoItem({ id }) {
  const selectTodoById = useMemo(makeSelectTodoById, []);
  const todo = useSelector(state => selectTodoById(state, id));

  return <div>{todo.text}</div>;
}
```

---

## Async Actions with createAsyncThunk

### Basic Async Thunk

```jsx
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// 1. Create async thunk
export const fetchUsers = createAsyncThunk(
  'users/fetchUsers',  // Action type prefix
  async () => {
    const response = await fetch('/api/users');
    const data = await response.json();
    return data; // This becomes action.payload on success
  }
);

// 2. Handle in slice
const userSlice = createSlice({
  name: 'users',
  initialState: {
    items: [],
    loading: false,
    error: null
  },
  reducers: {},
  extraReducers: (builder) => {
    builder
      // Loading state
      .addCase(fetchUsers.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      // Success state
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.loading = false;
        state.items = action.payload;
      })
      // Error state
      .addCase(fetchUsers.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  }
});

export default userSlice.reducer;
```

### Async Thunk with Parameters

```jsx
// Thunk with parameters
export const fetchUserById = createAsyncThunk(
  'users/fetchUserById',
  async (userId, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      if (!response.ok) throw new Error('User not found');
      return await response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

// Thunk with POST request
export const createUser = createAsyncThunk(
  'users/createUser',
  async (userData, { rejectWithValue }) => {
    try {
      const response = await fetch('/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(userData)
      });

      if (!response.ok) throw new Error('Failed to create user');
      return await response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

// Slice handling
const userSlice = createSlice({
  name: 'users',
  initialState: {
    items: [],
    currentUser: null,
    loading: false,
    error: null
  },
  extraReducers: (builder) => {
    builder
      // fetchUserById
      .addCase(fetchUserById.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchUserById.fulfilled, (state, action) => {
        state.loading = false;
        state.currentUser = action.payload;
      })
      .addCase(fetchUserById.rejected, (state, action) => {
        state.loading = false;
        state.error = action.payload;
      })
      // createUser
      .addCase(createUser.fulfilled, (state, action) => {
        state.items.push(action.payload);
      });
  }
});
```

### Async Thunk with Access to State

```jsx
export const updateUser = createAsyncThunk(
  'users/updateUser',
  async (userData, { getState, rejectWithValue }) => {
    // Access current state
    const state = getState();
    const currentUser = state.users.currentUser;

    try {
      const response = await fetch(`/api/users/${currentUser.id}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ ...currentUser, ...userData })
      });

      return await response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);
```

### Using Async Thunks in Components

```jsx
import { useDispatch, useSelector } from 'react-redux';
import { fetchUsers, createUser } from './userSlice';

function UserManager() {
  const dispatch = useDispatch();
  const { items, loading, error } = useSelector(state => state.users);

  // Fetch users on mount
  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  const handleCreateUser = async (userData) => {
    try {
      const result = await dispatch(createUser(userData)).unwrap();
      console.log('User created:', result);
    } catch (error) {
      console.error('Failed to create user:', error);
    }
  };

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      {items.map(user => <div key={user.id}>{user.name}</div>)}
      <button onClick={() => handleCreateUser({ name: 'New User' })}>
        Create User
      </button>
    </div>
  );
}
```

---

## Complete Examples

### Example 1: Todo App with Async

```jsx
// features/todos/todoSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Async thunks
export const fetchTodos = createAsyncThunk('todos/fetchTodos', async () => {
  const response = await fetch('/api/todos');
  return response.json();
});

export const addTodoAsync = createAsyncThunk('todos/addTodo', async (text) => {
  const response = await fetch('/api/todos', {
    method: 'POST',
    body: JSON.stringify({ text, completed: false }),
    headers: { 'Content-Type': 'application/json' }
  });
  return response.json();
});

export const toggleTodoAsync = createAsyncThunk('todos/toggleTodo', async (id, { getState }) => {
  const todo = getState().todos.items.find(t => t.id === id);
  const response = await fetch(`/api/todos/${id}`, {
    method: 'PATCH',
    body: JSON.stringify({ completed: !todo.completed }),
    headers: { 'Content-Type': 'application/json' }
  });
  return response.json();
});

const todoSlice = createSlice({
  name: 'todos',
  initialState: {
    items: [],
    status: 'idle', // 'idle', 'loading', 'succeeded', 'failed'
    error: null,
    filter: 'all'
  },
  reducers: {
    setFilter: (state, action) => {
      state.filter = action.payload;
    }
  },
  extraReducers: (builder) => {
    builder
      // fetchTodos
      .addCase(fetchTodos.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchTodos.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.items = action.payload;
      })
      .addCase(fetchTodos.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message;
      })
      // addTodoAsync
      .addCase(addTodoAsync.fulfilled, (state, action) => {
        state.items.push(action.payload);
      })
      // toggleTodoAsync
      .addCase(toggleTodoAsync.fulfilled, (state, action) => {
        const index = state.items.findIndex(todo => todo.id === action.payload.id);
        if (index !== -1) {
          state.items[index] = action.payload;
        }
      });
  }
});

export const { setFilter } = todoSlice.actions;
export default todoSlice.reducer;

// components/TodoApp.jsx
import { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchTodos, addTodoAsync, toggleTodoAsync, setFilter } from './todoSlice';

function TodoApp() {
  const dispatch = useDispatch();
  const { items, status, error, filter } = useSelector(state => state.todos);

  useEffect(() => {
    if (status === 'idle') {
      dispatch(fetchTodos());
    }
  }, [status, dispatch]);

  const filteredTodos = items.filter(todo => {
    if (filter === 'active') return !todo.completed;
    if (filter === 'completed') return todo.completed;
    return true;
  });

  if (status === 'loading') return <div>Loading todos...</div>;
  if (status === 'failed') return <div>Error: {error}</div>;

  return (
    <div>
      <input
        onKeyPress={(e) => {
          if (e.key === 'Enter') {
            dispatch(addTodoAsync(e.target.value));
            e.target.value = '';
          }
        }}
      />

      <div>
        <button onClick={() => dispatch(setFilter('all'))}>All</button>
        <button onClick={() => dispatch(setFilter('active'))}>Active</button>
        <button onClick={() => dispatch(setFilter('completed'))}>Completed</button>
      </div>

      {filteredTodos.map(todo => (
        <div key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => dispatch(toggleTodoAsync(todo.id))}
          />
          <span>{todo.text}</span>
        </div>
      ))}
    </div>
  );
}
```

### Example 2: Shopping Cart

```jsx
// features/cart/cartSlice.js
import { createSlice } from '@reduxjs/toolkit';

const cartSlice = createSlice({
  name: 'cart',
  initialState: {
    items: [],
    totalQuantity: 0,
    totalAmount: 0
  },
  reducers: {
    addItem: (state, action) => {
      const existingItem = state.items.find(item => item.id === action.payload.id);

      if (existingItem) {
        existingItem.quantity += 1;
        existingItem.totalPrice += action.payload.price;
      } else {
        state.items.push({
          ...action.payload,
          quantity: 1,
          totalPrice: action.payload.price
        });
      }

      state.totalQuantity += 1;
      state.totalAmount += action.payload.price;
    },

    removeItem: (state, action) => {
      const item = state.items.find(item => item.id === action.payload);

      if (item.quantity === 1) {
        state.items = state.items.filter(item => item.id !== action.payload);
      } else {
        item.quantity -= 1;
        item.totalPrice -= item.price;
      }

      state.totalQuantity -= 1;
      state.totalAmount -= item.price;
    },

    clearCart: (state) => {
      state.items = [];
      state.totalQuantity = 0;
      state.totalAmount = 0;
    }
  }
});

export const { addItem, removeItem, clearCart } = cartSlice.actions;
export default cartSlice.reducer;

// Selectors
export const selectCartItems = (state) => state.cart.items;
export const selectCartTotal = (state) => state.cart.totalAmount;
export const selectCartQuantity = (state) => state.cart.totalQuantity;

// components/Cart.jsx
import { useSelector, useDispatch } from 'react-redux';
import { removeItem, clearCart } from './cartSlice';

function Cart() {
  const dispatch = useDispatch();
  const items = useSelector(selectCartItems);
  const total = useSelector(selectCartTotal);
  const quantity = useSelector(selectCartQuantity);

  if (items.length === 0) {
    return <div>Cart is empty</div>;
  }

  return (
    <div>
      <h2>Shopping Cart ({quantity} items)</h2>

      {items.map(item => (
        <div key={item.id}>
          <span>{item.name}</span>
          <span>Quantity: {item.quantity}</span>
          <span>${item.totalPrice}</span>
          <button onClick={() => dispatch(removeItem(item.id))}>
            Remove
          </button>
        </div>
      ))}

      <div>Total: ${total}</div>
      <button onClick={() => dispatch(clearCart())}>Clear Cart</button>
      <button>Checkout</button>
    </div>
  );
}
```

---

## API Reference

### Redux Toolkit Functions

| Function | Purpose | Example |
|----------|---------|---------|
| configureStore | Create store | `configureStore({ reducer })` |
| createSlice | Create slice | `createSlice({ name, initialState, reducers })` |
| createAsyncThunk | Create async action | `createAsyncThunk('type', async () => {})` |
| createSelector | Memoized selector | `createSelector([input], output)` |

### React-Redux Hooks

| Hook | Purpose | Returns |
|------|---------|---------|
| useSelector | Read state | Selected state value |
| useDispatch | Get dispatch function | dispatch function |
| useStore | Get store instance | store object |

### Slice Properties

```jsx
const slice = createSlice({ ... });

slice.name        // 'counter'
slice.reducer     // reducer function
slice.actions     // { increment, decrement, ... }
slice.caseReducers // Original reducer functions
```

### Action States (createAsyncThunk)

```jsx
fetchUsers.pending   // Action when request starts
fetchUsers.fulfilled // Action when request succeeds
fetchUsers.rejected  // Action when request fails
```

## Troubleshooting

Problem 1: State not updating

Solutions:
- Check reducer is pure
- Verify action is dispatched
- Ensure reducer is in store

Problem 2: Component not re-rendering

Solutions:
- Check useSelector is used
- Verify selector returns new reference
- Use shallowEqual for objects

Problem 3: Async thunk not working

Solutions:
- Check thunk is dispatched
- Verify API call returns promise
- Handle errors with rejectWithValue

Problem 4: State mutation

Solutions:
- Use createSlice (includes Immer)
- Never mutate in manual reducers
- Return new objects/arrays

## Useful Resources

- Redux Toolkit Docs: https://redux-toolkit.js.org/
- React Redux Docs: https://react-redux.js.org/
- Redux Essentials Tutorial: https://redux.js.org/tutorials/essentials/part-1-overview-concepts

---

Created for beginners learning Redux Toolkit
