# Vitest - Complete Beginner's Guide

A comprehensive from-scratch guide to Vitest testing framework.

## Table of Contents
1. [What is Vitest](#what-is-vitest)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [Writing Your First Test](#writing-your-first-test)
5. [Test Structure](#test-structure)
6. [Assertions (Expect)](#assertions-expect)
7. [Testing Async Code](#testing-async-code)
8. [Mocking](#mocking)
9. [Testing React Components](#testing-react-components)
10. [Testing Hooks](#testing-hooks)
11. [Testing API Calls](#testing-api-calls)
12. [Snapshots](#snapshots)
13. [Coverage](#coverage)
14. [Configuration](#configuration)
15. [Complete Examples](#complete-examples)
16. [API Reference](#api-reference)

---

## What is Vitest

Vitest is a blazing fast unit test framework powered by Vite, designed to be a drop-in replacement for Jest.

**Why Vitest:**
- Incredibly fast (uses Vite's fast hot reload)
- Jest-compatible API (easy migration)
- Native ESM and TypeScript support
- Built-in mocking, snapshots, and coverage

**Vitest vs Jest:**
```javascript
// Jest (slower, requires config)
npm test -- --watch

// Vitest (faster, zero config for Vite projects)
npm vitest
```

## Installation

### For Vite Projects

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### For React Projects

```bash
# Create Vite React app
npm create vite@latest my-app -- --template react
cd my-app

# Install Vitest and testing libraries
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom @vitest/ui
```

## Basic Setup

### Configure Vitest

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,        // Use global APIs (describe, it, expect)
    environment: 'jsdom', // Browser-like environment
    setupFiles: './src/test/setup.js',
    css: true,            // Include CSS
    coverage: {
      provider: 'v8',     // Coverage provider
      reporter: ['text', 'json', 'html'],
    },
  },
});
```

### Setup File

```javascript
// src/test/setup.js
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import matchers from '@testing-library/jest-dom/matchers';

// Extend expect with jest-dom matchers
expect.extend(matchers);

// Cleanup after each test
afterEach(() => {
  cleanup();
});
```

### Package.json Scripts

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

---

## Writing Your First Test

### Basic Test Structure

```javascript
// sum.js
export function sum(a, b) {
  return a + b;
}

// sum.test.js
import { describe, it, expect } from 'vitest';
import { sum } from './sum';

describe('sum function', () => {
  it('adds two positive numbers correctly', () => {
    expect(sum(2, 3)).toBe(5);
  });

  it('adds negative numbers correctly', () => {
    expect(sum(-2, -3)).toBe(-5);
  });

  it('adds zero correctly', () => {
    expect(sum(0, 5)).toBe(5);
  });
});
```

### Running Tests

```bash
# Run tests once
npm run test:run

# Run in watch mode (auto-rerun on changes)
npm run test

# Run with UI
npm run test:ui

# Run with coverage
npm run test:coverage
```

---

## Test Structure

### describe Blocks

```javascript
import { describe, it, expect } from 'vitest';

// Group related tests
describe('User Authentication', () => {
  describe('Login', () => {
    it('should succeed with valid credentials', () => {
      expect(login('user@example.com', 'password')).toBe(true);
    });

    it('should fail with invalid credentials', () => {
      expect(login('user@example.com', 'wrong')).toBe(false);
    });
  });

  describe('Logout', () => {
    it('should clear user session', () => {
      expect(logout()).toBe(true);
    });
  });
});
```

### Nested describe

```javascript
describe('Calculator', () => {
  describe('Basic Operations', () => {
    describe('Addition', () => {
      it('adds positive numbers', () => {
        expect(add(2, 3)).toBe(5);
      });

      it('adds negative numbers', () => {
        expect(add(-2, -3)).toBe(-5);
      });
    });

    describe('Subtraction', () => {
      it('subtracts positive numbers', () => {
        expect(subtract(5, 3)).toBe(2);
      });
    });
  });
});
```

### Test Lifecycle Hooks

```javascript
import { beforeAll, afterAll, beforeEach, afterEach, describe, it, expect } from 'vitest';

describe('Database tests', () => {
  // Runs once before all tests
  beforeAll(() => {
    console.log('Connect to database');
  });

  // Runs once after all tests
  afterAll(() => {
    console.log('Disconnect from database');
  });

  // Runs before each test
  beforeEach(() => {
    console.log('Clear database');
  });

  // Runs after each test
  afterEach(() => {
    console.log('Clean up');
  });

  it('test 1', () => {
    expect(true).toBe(true);
  });

  it('test 2', () => {
    expect(true).toBe(true);
  });
});
```

---

## Assertions (Expect)

### Basic Matchers

```javascript
import { expect, it } from 'vitest';

// Equality
it('checks equality', () => {
  expect(2 + 2).toBe(4);
  expect({ name: 'John' }).toEqual({ name: 'John' });
  expect({ name: 'John' }).not.toEqual({ name: 'Jane' });
});

// Truthiness
it('checks truthiness', () => {
  expect(true).toBeTruthy();
  expect(false).toBeFalsy();
  expect(null).toBeNull();
  expect(undefined).toBeUndefined();
  expect(0).toBeDefined();
});

// Numbers
it('checks numbers', () => {
  expect(10).toBeGreaterThan(5);
  expect(5).toBeLessThan(10);
  expect(5).toBeGreaterThanOrEqual(5);
  expect(5).toBeLessThanOrEqual(5);
  expect(0.1 + 0.2).toBeCloseTo(0.3);
});

// Strings
it('checks strings', () => {
  expect('hello world').toContain('world');
  expect('hello').toMatch(/ell/);
  expect('hello').toHaveLength(5);
});

// Arrays
it('checks arrays', () => {
  expect([1, 2, 3]).toContain(2);
  expect([1, 2, 3]).toHaveLength(3);
  expect([]).toBeEmpty();
});

// Objects
it('checks objects', () => {
  expect({ a: 1, b: 2 }).toHaveProperty('a');
  expect({ a: 1, b: 2 }).toHaveProperty('a', 1);
  expect({ a: 1, b: 2 }).toMatchObject({ a: 1 });
});
```

### Advanced Matchers

```javascript
// Exceptions
it('checks exceptions', () => {
  expect(() => {
    throw new Error('Something went wrong');
  }).toThrow();

  expect(() => {
    throw new Error('Something went wrong');
  }).toThrow('Something went wrong');

  expect(() => {
    throw new Error('Something went wrong');
  }).toThrow(/wrong/);
});

// Custom matcher
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling;
    if (pass) {
      return {
        message: () => `expected ${received} not to be within range ${floor} - ${ceiling}`,
        pass: true,
      };
    } else {
      return {
        message: () => `expected ${received} to be within range ${floor} - ${ceiling}`,
        pass: false,
      };
    }
  },
});

it('custom matcher', () => {
  expect(10).toBeWithinRange(1, 20);
});
```

---

## Testing Async Code

### Promises

```javascript
import { describe, it, expect } from 'vitest';

// Async function
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Using async/await
it('fetches user successfully', async () => {
  const user = await fetchUser(1);
  expect(user).toHaveProperty('id', 1);
});

// Using resolves/rejects
it('resolves successfully', () => {
  expect(fetchUser(1)).resolves.toHaveProperty('id', 1);
});

it('rejects on error', () => {
  expect(fetchUser(999)).rejects.toThrow();
});
```

### Callbacks

```javascript
function fetchData(callback) {
  setTimeout(() => {
    callback({ data: 'success' });
  }, 100);
}

it('handles callbacks', (done) => {
  fetchData((result) => {
    expect(result).toEqual({ data: 'success' });
    done();
  });
});
```

### Timers

```javascript
import { vi, describe, it, expect, beforeEach, afterEach } from 'vitest';

function debounce(fn, delay) {
  let timeout;
  return (...args) => {
    clearTimeout(timeout);
    timeout = setTimeout(() => fn(...args), delay);
  };
}

describe('timer tests', () => {
  beforeEach(() => {
    vi.useFakeTimers();
  });

  afterEach(() => {
    vi.useRealTimers();
  });

  it('debounces function calls', () => {
    const fn = vi.fn();
    const debounced = debounce(fn, 100);

    debounced();
    debounced();
    debounced();

    expect(fn).not.toHaveBeenCalled();

    vi.advanceTimersByTime(100);

    expect(fn).toHaveBeenCalledTimes(1);
  });
});
```

---

## Mocking

### Function Mocks

```javascript
import { vi, describe, it, expect } from 'vitest';

// Create mock function
const mockFn = vi.fn();

it('creates mock function', () => {
  mockFn('hello', 123);

  expect(mockFn).toHaveBeenCalled();
  expect(mockFn).toHaveBeenCalledTimes(1);
  expect(mockFn).toHaveBeenCalledWith('hello', 123);
});

// Mock return values
const mockReturn = vi.fn()
  .mockReturnValue('default')
  .mockReturnValueOnce('first')
  .mockReturnValueOnce('second');

it('returns mocked values', () => {
  expect(mockReturn()).toBe('first');
  expect(mockReturn()).toBe('second');
  expect(mockReturn()).toBe('default');
});

// Mock implementations
const mockImpl = vi.fn().mockImplementation((x) => x * 2);

it('implements custom logic', () => {
  expect(mockImpl(5)).toBe(10);
});
```

### Module Mocking

```javascript
// api.js
export async function fetchUsers() {
  const response = await fetch('/api/users');
  return response.json();
}

// userService.js
import { fetchUsers } from './api';

export async function getActiveUsers() {
  const users = await fetchUsers();
  return users.filter(user => user.active);
}

// userService.test.js
import { vi, describe, it, expect } from 'vitest';
import { getActiveUsers } from './userService';

vi.mock('./api', () => ({
  fetchUsers: vi.fn()
}));

import { fetchUsers } from './api';

describe('getActiveUsers', () => {
  it('returns only active users', async () => {
    fetchUsers.mockResolvedValue([
      { id: 1, name: 'John', active: true },
      { id: 2, name: 'Jane', active: false },
      { id: 3, name: 'Bob', active: true },
    ]);

    const activeUsers = await getActiveUsers();

    expect(activeUsers).toHaveLength(2);
    expect(activeUsers[0]).toHaveProperty('active', true);
  });
});
```

### Spying on Methods

```javascript
class UserService {
  async getUser(id) {
    return { id, name: 'John' };
  }

  async saveUser(user) {
    return { ...user, saved: true };
  }
}

it('spies on class methods', async () => {
  const service = new UserService();

  const getUserSpy = vi.spyOn(service, 'getUser');
  const saveUserSpy = vi.spyOn(service, 'saveUser');

  await service.getUser(1);
  await service.saveUser({ name: 'Jane' });

  expect(getUserSpy).toHaveBeenCalledWith(1);
  expect(saveUserSpy).toHaveBeenCalledWith({ name: 'Jane' });

  getUserSpy.mockRestore();
  saveUserSpy.mockRestore();
});
```

---

## Testing React Components

### Basic Component Test

```jsx
// Button.jsx
export function Button({ onClick, children, disabled = false }) {
  return (
    <button onClick={onClick} disabled={disabled} className="btn">
      {children}
    </button>
  );
}

// Button.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { Button } from './Button';

describe('Button', () => {
  it('renders with children', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('handles click events', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);

    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    expect(screen.getByText('Click me')).toBeDisabled();
  });
});
```

### Testing Forms

```jsx
// LoginForm.jsx
import { useState } from 'react';

export function LoginForm({ onSubmit }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
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
      <button type="submit">Login</button>
    </form>
  );
}

// LoginForm.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect, vi } from 'vitest';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('submits form with user data', async () => {
    const handleSubmit = vi.fn();
    const user = userEvent.setup();

    render(<LoginForm onSubmit={handleSubmit} />);

    await user.type(screen.getByPlaceholderText('Email'), 'test@example.com');
    await user.type(screen.getByPlaceholderText('Password'), 'password123');
    await user.click(screen.getByText('Login'));

    expect(handleSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123'
    });
  });
});
```

### Testing Async Components

```jsx
// UserProfile.jsx
import { useState, useEffect } from 'react';

export function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  return <div>{user.name}</div>;
}

// UserProfile.test.jsx
import { render, screen, waitFor } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { UserProfile } from './UserProfile';

// Mock fetch
global.fetch = vi.fn();

describe('UserProfile', () => {
  it('displays user name after loading', async () => {
    const mockUser = { id: 1, name: 'John Doe' };

    fetch.mockResolvedValueOnce({
      ok: true,
      json: async () => mockUser
    });

    render(<UserProfile userId={1} />);

    expect(screen.getByText('Loading...')).toBeInTheDocument();

    await waitFor(() => {
      expect(screen.getByText('John Doe')).toBeInTheDocument();
    });
  });

  it('displays error message on failure', async () => {
    fetch.mockRejectedValueOnce(new Error('Failed to fetch'));

    render(<UserProfile userId={1} />);

    await waitFor(() => {
      expect(screen.getByText(/Error/)).toBeInTheDocument();
    });
  });
});
```

---

## Testing Hooks

### Custom Hook Test

```jsx
// useCounter.js
import { useState } from 'react';

export function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}

// useCounter.test.js
import { renderHook, act } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('initializes with default value', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  it('initializes with custom value', () => {
    const { result } = renderHook(() => useCounter(10));
    expect(result.current.count).toBe(10);
  });

  it('increments counter', () => {
    const { result } = renderHook(() => useCounter());

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });

  it('decrements counter', () => {
    const { result } = renderHook(() => useCounter(5));

    act(() => {
      result.current.decrement();
    });

    expect(result.current.count).toBe(4);
  });

  it('resets counter', () => {
    const { result } = renderHook(() => useCounter(5));

    act(() => {
      result.current.increment();
      result.current.increment();
      result.current.reset();
    });

    expect(result.current.count).toBe(5);
  });
});
```

### Testing Hooks with Dependencies

```jsx
// useLocalStorage.js
import { useState, useEffect } from 'react';

export function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(storedValue));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);

  return [storedValue, setStoredValue];
}

// useLocalStorage.test.js
import { renderHook, act } from '@testing-library/react';
import { describe, it, expect, beforeEach } from 'vitest';
import { useLocalStorage } from './useLocalStorage';

describe('useLocalStorage', () => {
  beforeEach(() => {
    window.localStorage.clear();
  });

  it('initializes with initial value', () => {
    const { result } = renderHook(() => useLocalStorage('key', 'default'));
    expect(result.current[0]).toBe('default');
  });

  it('reads existing value from localStorage', () => {
    window.localStorage.setItem('key', JSON.stringify('stored'));

    const { result } = renderHook(() => useLocalStorage('key', 'default'));
    expect(result.current[0]).toBe('stored');
  });

  it('updates localStorage when value changes', () => {
    const { result } = renderHook(() => useLocalStorage('key', 'default'));

    act(() => {
      result.current[1]('new value');
    });

    expect(window.localStorage.getItem('key')).toBe(JSON.stringify('new value'));
    expect(result.current[0]).toBe('new value');
  });
});
```

---

## Testing API Calls

### Testing Fetch

```javascript
// api.js
export async function getUsers() {
  const response = await fetch('/api/users');
  if (!response.ok) throw new Error('Failed to fetch');
  return response.json();
}

// api.test.js
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { getUsers } from './api';

describe('getUsers', () => {
  beforeEach(() => {
    global.fetch = vi.fn();
  });

  it('returns users on success', async () => {
    const mockUsers = [{ id: 1, name: 'John' }];

    fetch.mockResolvedValueOnce({
      ok: true,
      json: async () => mockUsers
    });

    const users = await getUsers();
    expect(users).toEqual(mockUsers);
    expect(fetch).toHaveBeenCalledWith('/api/users');
  });

  it('throws error on failure', async () => {
    fetch.mockResolvedValueOnce({
      ok: false,
      status: 500
    });

    await expect(getUsers()).rejects.toThrow('Failed to fetch');
  });
});
```

### Testing Axios

```javascript
// api.js
import axios from 'axios';

export async function createUser(userData) {
  const response = await axios.post('/api/users', userData);
  return response.data;
}

// api.test.js
import { describe, it, expect, vi } from 'vitest';
import axios from 'axios';
import { createUser } from './api';

vi.mock('axios');

describe('createUser', () => {
  it('creates user successfully', async () => {
    const mockUser = { id: 1, name: 'John' };
    axios.post.mockResolvedValueOnce({ data: mockUser });

    const result = await createUser({ name: 'John' });

    expect(result).toEqual(mockUser);
    expect(axios.post).toHaveBeenCalledWith('/api/users', { name: 'John' });
  });

  it('handles error', async () => {
    axios.post.mockRejectedValueOnce(new Error('Network error'));

    await expect(createUser({ name: 'John' })).rejects.toThrow('Network error');
  });
});
```

---

## Snapshots

### Basic Snapshot Testing

```jsx
// UserCard.jsx
export function UserCard({ user }) {
  return (
    <div className="user-card">
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Age: {user.age}</p>
    </div>
  );
}

// UserCard.test.jsx
import { render } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { UserCard } from './UserCard';

describe('UserCard', () => {
  it('matches snapshot', () => {
    const user = {
      name: 'John Doe',
      email: 'john@example.com',
      age: 30
    };

    const { container } = render(<UserCard user={user} />);
    expect(container).toMatchSnapshot();
  });
});
```

### Inline Snapshots

```javascript
it('matches inline snapshot', () => {
  const data = { name: 'John', age: 30 };
  expect(data).toMatchInlineSnapshot(`
    {
      "age": 30,
      "name": "John",
    }
  `);
});
```

---

## Coverage

### Running Coverage

```bash
npm run test:coverage
```

### Coverage Configuration

```javascript
// vite.config.js
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html', 'lcov'],
      exclude: [
        'node_modules/',
        'src/test/',
        '**/*.test.js',
        '**/*.spec.js',
      ],
      thresholds: {
        global: {
          statements: 80,
          branches: 80,
          functions: 80,
          lines: 80,
        },
      },
    },
  },
});
```

### Coverage Reports

```bash
# Text report in console
npm run test:coverage

# HTML report in browser
open coverage/index.html

# JSON report for CI
cat coverage/coverage-final.json
```

---

## Configuration

### Full Configuration Example

```javascript
// vitest.config.js
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    // Global APIs
    globals: true,

    // Environment
    environment: 'jsdom',
    environmentOptions: {
      jsdom: {
        resources: 'usable',
      },
    },

    // Setup files
    setupFiles: ['./src/test/setup.js'],

    // CSS handling
    css: {
      modules: {
        classNameStrategy: 'non-scoped',
      },
    },

    // Coverage
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: ['node_modules/', 'src/test/'],
    },

    // Mocking
    mockReset: true,
    restoreMocks: true,
    clearMocks: true,

    // Timeout
    testTimeout: 10000,

    // Retry
    retry: 0,

    // Watch mode
    watch: false,

    // Output
    silent: false,
    verbose: true,

    // Test matching
    include: ['src/**/*.{test,spec}.{js,jsx,ts,tsx}'],
    exclude: ['node_modules', 'dist', '.idea', '.git', '.cache'],
  },
});
```

---

## Complete Examples

### Example 1: Todo App Tests

```jsx
// TodoApp.jsx
import { useState } from 'react';

export function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  const addTodo = () => {
    if (input.trim()) {
      setTodos([...todos, { id: Date.now(), text: input, completed: false }]);
      setInput('');
    }
  };

  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const deleteTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  return (
    <div>
      <h1>Todo App</h1>
      <div>
        <input
          value={input}
          onChange={(e) => setInput(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && addTodo()}
          placeholder="Add todo..."
        />
        <button onClick={addTodo}>Add</button>
      </div>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>
      <div>Total: {todos.length}</div>
    </div>
  );
}

// TodoApp.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect } from 'vitest';
import { TodoApp } from './TodoApp';

describe('TodoApp', () => {
  it('adds a new todo', async () => {
    const user = userEvent.setup();
    render(<TodoApp />);

    const input = screen.getByPlaceholderText('Add todo...');
    await user.type(input, 'Buy groceries');
    await user.click(screen.getByText('Add'));

    expect(screen.getByText('Buy groceries')).toBeInTheDocument();
    expect(screen.getByText('Total: 1')).toBeInTheDocument();
  });

  it('toggles todo completion', async () => {
    const user = userEvent.setup();
    render(<TodoApp />);

    const input = screen.getByPlaceholderText('Add todo...');
    await user.type(input, 'Learn testing');
    await user.click(screen.getByText('Add'));

    const checkbox = screen.getByRole('checkbox');
    expect(checkbox).not.toBeChecked();

    await user.click(checkbox);
    expect(checkbox).toBeChecked();
  });

  it('deletes a todo', async () => {
    const user = userEvent.setup();
    render(<TodoApp />);

    const input = screen.getByPlaceholderText('Add todo...');
    await user.type(input, 'Delete me');
    await user.click(screen.getByText('Add'));

    expect(screen.getByText('Delete me')).toBeInTheDocument();

    await user.click(screen.getByText('Delete'));
    expect(screen.queryByText('Delete me')).not.toBeInTheDocument();
    expect(screen.getByText('Total: 0')).toBeInTheDocument();
  });
});
```

### Example 2: API Integration Tests

```javascript
// userService.js
export class UserService {
  async getUsers() {
    const response = await fetch('/api/users');
    if (!response.ok) throw new Error('Failed to fetch users');
    return response.json();
  }

  async createUser(userData) {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(userData),
    });
    if (!response.ok) throw new Error('Failed to create user');
    return response.json();
  }

  async updateUser(id, userData) {
    const response = await fetch(`/api/users/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(userData),
    });
    if (!response.ok) throw new Error('Failed to update user');
    return response.json();
  }

  async deleteUser(id) {
    const response = await fetch(`/api/users/${id}`, {
      method: 'DELETE',
    });
    if (!response.ok) throw new Error('Failed to delete user');
    return true;
  }
}

// userService.test.js
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { UserService } from './userService';

describe('UserService', () => {
  let userService;

  beforeEach(() => {
    userService = new UserService();
    global.fetch = vi.fn();
  });

  describe('getUsers', () => {
    it('returns users on success', async () => {
      const mockUsers = [{ id: 1, name: 'John' }];
      fetch.mockResolvedValueOnce({
        ok: true,
        json: async () => mockUsers,
      });

      const users = await userService.getUsers();
      expect(users).toEqual(mockUsers);
      expect(fetch).toHaveBeenCalledWith('/api/users');
    });

    it('throws error on failure', async () => {
      fetch.mockResolvedValueOnce({
        ok: false,
        status: 500,
      });

      await expect(userService.getUsers()).rejects.toThrow('Failed to fetch users');
    });
  });

  describe('createUser', () => {
    it('creates user successfully', async () => {
      const newUser = { name: 'Jane', email: 'jane@example.com' };
      const createdUser = { id: 2, ...newUser };

      fetch.mockResolvedValueOnce({
        ok: true,
        json: async () => createdUser,
      });

      const result = await userService.createUser(newUser);
      expect(result).toEqual(createdUser);
      expect(fetch).toHaveBeenCalledWith('/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newUser),
      });
    });
  });
});
```

---

## API Reference

### Global Functions

| Function | Description |
|----------|-------------|
| `describe(name, fn)` | Groups related tests |
| `it(name, fn)` / `test(name, fn)` | Defines a test case |
| `expect(value)` | Creates an assertion |
| `vi.fn()` | Creates a mock function |
| `vi.spyOn(object, method)` | Creates a spy on a method |

### Lifecycle Hooks

| Hook | Timing |
|------|--------|
| `beforeAll(fn)` | Once before all tests |
| `afterAll(fn)` | Once after all tests |
| `beforeEach(fn)` | Before each test |
| `afterEach(fn)` | After each test |

### Common Matchers

| Matcher | Purpose |
|---------|---------|
| `toBe(value)` | Exact equality (primitive) |
| `toEqual(value)` | Deep equality (object) |
| `toBeTruthy()` | Truthy value |
| `toBeFalsy()` | Falsy value |
| `toBeNull()` | Null value |
| `toBeUndefined()` | Undefined value |
| `toBeDefined()` | Defined value |
| `toContain(item)` | Contains item |
| `toHaveLength(number)` | Has length |
| `toThrow()` | Throws error |
| `resolves` | Promise resolves |
| `rejects` | Promise rejects |

## Troubleshooting

Problem 1: Tests not running

Solutions:
- Check test script in package.json
- Verify test files match pattern
- Run `vitest --run` to run once

Problem 2: React components not rendering

Solutions:
- Set environment to jsdom
- Import render from @testing-library/react
- Check component is exported correctly

Problem 3: Mocks not working

Solutions:
- Use vi.mock at top level
- Reset mocks with vi.resetAllMocks()
- Check mock implementation

## Useful Resources

- Vitest Docs: https://vitest.dev/
- Testing Library Docs: https://testing-library.com/
- Vitest GitHub: https://github.com/vitest-dev/vitest

---

Created for beginners learning Vitest testing
