# React Hot Toast - Complete Beginner's Guide

A comprehensive from-scratch guide to React Hot Toast notifications.

## Table of Contents
1. [What is React Hot Toast](#what-is-react-hot-toast)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [All Toast Methods](#all-toast-methods)
5. [Toast Options](#toast-options)
6. [Custom Styling](#custom-styling)
7. [Promise Toasts](#promise-toasts)
8. [Loading Toasts](#loading-toasts)
9. [Custom Components](#custom-components)
10. [Common Patterns](#common-patterns)
11. [Complete API Reference](#complete-api-reference)

---

## What is React Hot Toast

React Hot Toast is a library that shows beautiful notification messages (toasts) in your React app.

**What toasts look like:**
```
┌─────────────────────────────────┐
│  ✓  Success! Form submitted     │  <- Success toast
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  ⚠  Warning! Low inventory      │  <- Warning toast
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  ❌  Error! Something went wrong│  <- Error toast
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  ℹ  Info: New update available  │  <- Info toast
└─────────────────────────────────┘

┌─────────────────────────────────┐
│  ◌  Loading... Please wait      │  <- Loading toast
└─────────────────────────────────┘
```

## Installation

```bash
npm install react-hot-toast
```

## Basic Setup

### Step 1: Add Toaster Component

```jsx
// App.jsx or main.jsx
import { Toaster } from 'react-hot-toast';
import toast from 'react-hot-toast';

function App() {
  return (
    <div>
      {/* 1. Add Toaster component - this renders all toasts */}
      <Toaster />

      {/* Your app content */}
      <MyComponent />
    </div>
  );
}
```

### Step 2: Show Toasts Anywhere

```jsx
import toast from 'react-hot-toast';

function MyComponent() {
  const showSuccess = () => {
    // 2. Call toast methods to show notifications
    toast.success('Successfully saved!');
  };

  return (
    <button onClick={showSuccess}>
      Show Success
    </button>
  );
}
```

## All Toast Methods

### 1. toast.success() - Show success message

```jsx
import toast from 'react-hot-toast';

// Basic success
toast.success('Form submitted successfully!');

// With options
toast.success('Item added to cart', {
  duration: 3000,        // Show for 3 seconds
  position: 'top-center', // Position on screen
  icon: '🎉',            // Custom icon
});

// Real-world example
function SaveButton() {
  const handleSave = async () => {
    try {
      await saveData();
      toast.success('Data saved successfully!');
    } catch (error) {
      toast.error('Failed to save');
    }
  };

  return <button onClick={handleSave}>Save</button>;
}
```

### 2. toast.error() - Show error message

```jsx
// Basic error
toast.error('Something went wrong!');

// With error object
try {
  throw new Error('Network failed');
} catch (error) {
  toast.error(error.message);
}

// With custom styling
toast.error('Invalid email address', {
  duration: 5000,
  style: {
    background: '#ff0000',
    color: '#ffffff',
  },
});

// Real-world: Form validation
function LoginForm() {
  const handleSubmit = (e) => {
    e.preventDefault();
    const email = e.target.email.value;

    if (!email.includes('@')) {
      toast.error('Please enter a valid email');
      return;
    }

    // Proceed with login
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

### 3. toast.loading() - Show loading state

```jsx
// Basic loading
const toastId = toast.loading('Loading...');

// Update after operation
const handleAsyncOperation = async () => {
  const toastId = toast.loading('Saving data...');

  try {
    await saveData();
    toast.success('Saved!', { id: toastId }); // Replaces loading
  } catch (error) {
    toast.error('Failed!', { id: toastId }); // Replaces loading
  }
};

// Real-world: API call
function SubmitButton() {
  const handleSubmit = async () => {
    const toastId = toast.loading('Submitting form...');

    try {
      const response = await fetch('/api/submit', {
        method: 'POST',
        body: formData
      });

      if (response.ok) {
        toast.success('Form submitted!', { id: toastId });
      } else {
        toast.error('Submission failed', { id: toastId });
      }
    } catch (error) {
      toast.error('Network error', { id: toastId });
    }
  };

  return <button onClick={handleSubmit}>Submit</button>;
}
```

### 4. toast.promise() - Handle promises elegantly

```jsx
// Basic promise
const promise = fetch('/api/data');

toast.promise(promise, {
  loading: 'Loading data...',
  success: 'Data loaded!',
  error: 'Failed to load data',
});

// With custom messages
toast.promise(
  saveUserData(userData),
  {
    loading: 'Saving user...',
    success: (data) => `User ${data.name} saved!`,
    error: (err) => `Error: ${err.message}`,
  }
);

// Real-world: Delete operation
function DeleteButton({ id }) {
  const handleDelete = () => {
    toast.promise(
      deleteUser(id),
      {
        loading: 'Deleting user...',
        success: 'User deleted successfully',
        error: 'Could not delete user',
      }
    );
  };

  return <button onClick={handleDelete}>Delete</button>;
}

// Real-world: File upload
function UploadButton() {
  const handleUpload = (file) => {
    const uploadPromise = uploadFile(file);

    toast.promise(uploadPromise, {
      loading: `Uploading ${file.name}...`,
      success: `${file.name} uploaded!`,
      error: `Failed to upload ${file.name}`,
    });
  };

  return <button onClick={() => handleUpload(file)}>Upload</button>;
}
```

### 5. toast.custom() - Create custom toast

```jsx
// Basic custom toast
toast.custom((t) => (
  <div style={{
    background: '#333',
    color: '#fff',
    padding: '10px 20px',
    borderRadius: '8px',
  }}>
    Custom Toast Message
  </div>
));

// Interactive custom toast
toast.custom((t) => (
  <div style={{
    background: 'white',
    border: '1px solid #ccc',
    borderRadius: '8px',
    padding: '16px',
    boxShadow: '0 2px 8px rgba(0,0,0,0.15)',
  }}>
    <h4>Confirm Action</h4>
    <p>Are you sure you want to delete?</p>
    <button onClick={() => toast.dismiss(t.id)}>Cancel</button>
    <button onClick={() => {
      // Perform action
      toast.dismiss(t.id);
      toast.success('Deleted!');
    }}>Confirm</button>
  </div>
));

// Real-world: Undo toast
function DeleteItem({ id, onDelete }) {
  const handleDelete = () => {
    // Temporarily remove
    onDelete(id);

    // Show undo option
    toast.custom((t) => (
      <div style={{
        background: '#333',
        color: 'white',
        padding: '12px 20px',
        borderRadius: '8px',
        display: 'flex',
        gap: '10px',
        alignItems: 'center'
      }}>
        <span>Item deleted</span>
        <button
          onClick={() => {
            onDelete(id, true); // Undo
            toast.dismiss(t.id);
          }}
          style={{
            background: 'white',
            border: 'none',
            padding: '4px 8px',
            borderRadius: '4px',
            cursor: 'pointer'
          }}
        >
          Undo
        </button>
      </div>
    ), { duration: 5000 });
  };

  return <button onClick={handleDelete}>Delete</button>;
}
```

### 6. toast.dismiss() - Remove toasts

```jsx
// Dismiss all toasts
toast.dismiss();

// Dismiss specific toast by ID
const toastId = toast.loading('Loading...');
toast.dismiss(toastId);

// Dismiss after timeout
const toastId = toast.success('Message sent!');
setTimeout(() => {
  toast.dismiss(toastId);
}, 2000);

// Real-world: Clear all toasts on page change
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';

function App() {
  const location = useLocation();

  useEffect(() => {
    // Clear all toasts when route changes
    toast.dismiss();
  }, [location]);

  return <div>...</div>;
}
```

### 7. toast.remove() - Remove without animation

```jsx
// Remove (no exit animation)
toast.remove();

// Remove specific toast
toast.remove(toastId);

// Difference between dismiss() and remove():
// - dismiss(): Triggers exit animation, then removes
// - remove(): Immediately removes without animation
```

## Toast Options

### Position Options

```jsx
// All available positions
toast.success('Top Left', { position: 'top-left' });
toast.success('Top Center', { position: 'top-center' });
toast.success('Top Right', { position: 'top-right' });
toast.success('Bottom Left', { position: 'bottom-left' });
toast.success('Bottom Center', { position: 'bottom-center' });
toast.success('Bottom Right', { position: 'bottom-right' });

// Default is 'top-center'
```

### Duration Options

```jsx
// Duration in milliseconds
toast.success('Short message', { duration: 1000 });    // 1 second
toast.success('Normal message', { duration: 3000 });   // 3 seconds (default)
toast.success('Long message', { duration: 5000 });     // 5 seconds
toast.loading('Persistent', { duration: Infinity });   // Never auto-dismiss

// Real-world: Different durations for different messages
toast.error('Critical error!', { duration: 10000 });    // Show longer
toast.success('Quick update', { duration: 2000 });      // Show briefly
```

### Icon Options

```jsx
// Custom icon
toast.success('Custom icon', { icon: '🎉' });
toast.error('Custom error icon', { icon: '💀' });
toast('Custom message', { icon: '👋' });

// Remove icon
toast.success('No icon', { icon: null });

// Real-world: Branded notifications
toast('New order received!', {
  icon: '🛒',
  duration: 5000
});
```

### Style Options

```jsx
// Custom styling
toast.success('Styled toast', {
  style: {
    background: '#333',
    color: '#fff',
    fontSize: '14px',
    padding: '16px 24px',
    borderRadius: '8px',
    border: '1px solid #00ff00',
  },
  iconTheme: {
    primary: '#00ff00',
    secondary: '#fff',
  },
});

// Real-world: Dark mode support
const isDarkMode = useDarkMode();
toast('Message', {
  style: {
    background: isDarkMode ? '#333' : '#fff',
    color: isDarkMode ? '#fff' : '#333',
  },
});
```

## Custom Styling

### Global Toaster Configuration

```jsx
import { Toaster } from 'react-hot-toast';

function App() {
  return (
    <>
      <Toaster
        position="top-right"           // Default position
        reverseOrder={false}           // New toasts on top
        gutter={8}                     // Space between toasts
        containerClassName=""          // Custom container class
        containerStyle={{}}            // Custom container styles
        toastOptions={{
          // Default options for all toasts
          duration: 3000,
          style: {
            background: '#363636',
            color: '#fff',
          },
          success: {
            duration: 3000,
            iconTheme: {
              primary: '#00ff00',
              secondary: '#fff',
            },
          },
          error: {
            duration: 4000,
            iconTheme: {
              primary: '#ff0000',
              secondary: '#fff',
            },
          },
        }}
      />
      <AppContent />
    </>
  );
}
```

### Custom CSS Classes

```jsx
// CSS file
.custom-toast {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  font-weight: bold;
  border-radius: 50px;
  padding: 12px 24px;
}

.custom-toast-success {
  background: linear-gradient(135deg, #00b09b, #96c93d);
}

.custom-toast-error {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

// Usage
toast.success('Custom success!', {
  className: 'custom-toast custom-toast-success',
});

toast.error('Custom error!', {
  className: 'custom-toast custom-toast-error',
});
```

## Promise Toasts - Detailed Examples

### Basic Promise Toast

```jsx
// Simple API call
const fetchUsers = () => {
  return fetch('/api/users').then(res => res.json());
};

toast.promise(fetchUsers(), {
  loading: 'Loading users...',
  success: 'Users loaded!',
  error: 'Failed to load users',
});
```

### Promise Toast with Dynamic Messages

```jsx
// Dynamic success message
toast.promise(
  saveUser(userData),
  {
    loading: 'Saving user...',
    success: (data) => `User ${data.name} saved successfully!`,
    error: (err) => `Error: ${err.message}`,
  }
);
```

### Promise Toast with Multiple Steps

```jsx
async function processOrder(orderId) {
  // Step 1: Validate
  await validateOrder(orderId);

  // Step 2: Process payment
  await processPayment(orderId);

  // Step 3: Send confirmation
  await sendConfirmation(orderId);

  return { orderId, status: 'completed' };
}

toast.promise(processOrder(123), {
  loading: 'Processing your order...',
  success: 'Order confirmed! Check your email.',
  error: 'Order failed. Please try again.',
});
```

## Loading Toasts - Detailed Examples

### Manual Loading State

```jsx
function UploadFile() {
  const handleUpload = async (file) => {
    const toastId = toast.loading(`Uploading ${file.name}...`);

    try {
      const progress = 0;

      // Simulate progress updates
      const interval = setInterval(() => {
        progress += 10;
        toast.loading(`Uploading ${file.name}: ${progress}%`, {
          id: toastId
        });
      }, 500);

      await uploadToServer(file);
      clearInterval(interval);

      toast.success(`${file.name} uploaded!`, {
        id: toastId,
        duration: 3000
      });
    } catch (error) {
      toast.error(`Failed to upload ${file.name}`, {
        id: toastId,
        duration: 4000
      });
    }
  };

  return <button onClick={() => handleUpload(file)}>Upload</button>;
}
```

### Multiple Loading States

```jsx
function DataLoader() {
  const loadAllData = async () => {
    const usersToast = toast.loading('Loading users...');
    const postsToast = toast.loading('Loading posts...');

    try {
      const [users, posts] = await Promise.all([
        fetchUsers(),
        fetchPosts()
      ]);

      toast.success(`Loaded ${users.length} users`, { id: usersToast });
      toast.success(`Loaded ${posts.length} posts`, { id: postsToast });
    } catch (error) {
      toast.error('Failed to load data', { id: usersToast });
      toast.error('Failed to load data', { id: postsToast });
    }
  };

  return <button onClick={loadAllData}>Load All Data</button>;
}
```

## Custom Components - Detailed Examples

### Confirmation Toast

```jsx
function ConfirmationToast({ message, onConfirm, onCancel }) {
  return (
    <div style={{
      background: 'white',
      borderRadius: '8px',
      padding: '16px',
      boxShadow: '0 4px 12px rgba(0,0,0,0.15)',
      minWidth: '300px',
    }}>
      <p style={{ margin: '0 0 12px 0' }}>{message}</p>
      <div style={{ display: 'flex', gap: '8px', justifyContent: 'flex-end' }}>
        <button
          onClick={onCancel}
          style={{
            padding: '6px 12px',
            border: '1px solid #ccc',
            background: 'white',
            borderRadius: '4px',
            cursor: 'pointer'
          }}
        >
          Cancel
        </button>
        <button
          onClick={onConfirm}
          style={{
            padding: '6px 12px',
            background: '#dc2626',
            color: 'white',
            border: 'none',
            borderRadius: '4px',
            cursor: 'pointer'
          }}
        >
          Confirm
        </button>
      </div>
    </div>
  );
}

// Usage
function DeleteButton({ id, onDelete }) {
  const handleDelete = () => {
    toast.custom((t) => (
      <ConfirmationToast
        message="Are you sure you want to delete this item?"
        onConfirm={() => {
          onDelete(id);
          toast.dismiss(t.id);
          toast.success('Item deleted');
        }}
        onCancel={() => toast.dismiss(t.id)}
      />
    ), {
      duration: Infinity, // Don't auto-dismiss
      position: 'top-center',
    });
  };

  return <button onClick={handleDelete}>Delete</button>;
}
```

### Form Toast

```jsx
function FormToast({ errors }) {
  return (
    <div style={{
      background: '#fee2e2',
      borderLeft: '4px solid #dc2626',
      padding: '12px',
      borderRadius: '4px',
      maxWidth: '400px',
    }}>
      <strong style={{ color: '#dc2626' }}>Form Errors:</strong>
      <ul style={{ margin: '8px 0 0 0', paddingLeft: '20px' }}>
        {errors.map(error => (
          <li key={error.field} style={{ color: '#dc2626' }}>
            {error.message}
          </li>
        ))}
      </ul>
    </div>
  );
}

// Usage
function validateForm(formData) {
  const errors = [];

  if (!formData.email) {
    errors.push({ field: 'email', message: 'Email is required' });
  }
  if (!formData.password) {
    errors.push({ field: 'password', message: 'Password is required' });
  }

  if (errors.length > 0) {
    toast.custom((t) => <FormToast errors={errors} />, {
      duration: 5000,
      position: 'top-center',
    });
    return false;
  }

  return true;
}
```

### Progress Toast

```jsx
function ProgressToast({ progress, total }) {
  const percentage = (progress / total) * 100;

  return (
    <div style={{
      background: 'white',
      borderRadius: '8px',
      padding: '16px',
      width: '300px',
      boxShadow: '0 2px 8px rgba(0,0,0,0.15)',
    }}>
      <div style={{ marginBottom: '8px' }}>
        Uploading: {progress} / {total} files
      </div>
      <div style={{
        background: '#e5e7eb',
        borderRadius: '4px',
        height: '8px',
        overflow: 'hidden',
      }}>
        <div style={{
          width: `${percentage}%`,
          background: '#3b82f6',
          height: '100%',
          transition: 'width 0.3s ease',
        }} />
      </div>
    </div>
  );
}

// Usage
async function uploadFiles(files) {
  const toastId = toast.custom((t) => (
    <ProgressToast progress={0} total={files.length} />
  ), { duration: Infinity });

  for (let i = 0; i < files.length; i++) {
    await uploadFile(files[i]);

    // Update progress
    toast.custom((t) => (
      <ProgressToast progress={i + 1} total={files.length} />
    ), { id: toastId });
  }

  toast.success('All files uploaded!', { id: toastId });
}
```

## Common Patterns

### Pattern 1: Toast Utility Functions

```jsx
// utils/toast.js
import toast from 'react-hot-toast';

export const showSuccess = (message) => {
  toast.success(message, {
    duration: 3000,
    icon: '✓',
    style: {
      background: '#10b981',
      color: '#fff',
    },
  });
};

export const showError = (message) => {
  toast.error(message, {
    duration: 4000,
    icon: '✗',
    style: {
      background: '#ef4444',
      color: '#fff',
    },
  });
};

export const showWarning = (message) => {
  toast(message, {
    icon: '⚠',
    style: {
      background: '#f59e0b',
      color: '#fff',
    },
  });
};

export const showInfo = (message) => {
  toast(message, {
    icon: 'ℹ',
    style: {
      background: '#3b82f6',
      color: '#fff',
    },
  });
};

// Usage
import { showSuccess, showError } from './utils/toast';

showSuccess('Operation completed!');
showError('Something went wrong!');
```

### Pattern 2: API Error Handler

```jsx
// utils/api.js
import toast from 'react-hot-toast';

export async function apiCall(promise, successMessage, errorMessage) {
  const toastId = toast.loading('Processing...');

  try {
    const result = await promise;
    toast.success(successMessage || 'Success!', { id: toastId });
    return result;
  } catch (error) {
    toast.error(errorMessage || error.message, { id: toastId });
    throw error;
  }
}

// Usage
const handleSave = async () => {
  try {
    await apiCall(
      saveUserData(userData),
      'User saved successfully!',
      'Failed to save user'
    );
  } catch (error) {
    // Handle error
  }
};
```

### Pattern 3: Form Submission with Toast

```jsx
import { useForm } from 'react-hook-form';
import toast from 'react-hot-toast';

function ContactForm() {
  const { register, handleSubmit, reset } = useForm();

  const onSubmit = async (data) => {
    const toastId = toast.loading('Sending message...');

    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 2000));

      toast.success('Message sent successfully!', { id: toastId });
      reset(); // Clear form
    } catch (error) {
      toast.error('Failed to send message', { id: toastId });
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} placeholder="Name" />
      <input {...register('email')} placeholder="Email" />
      <textarea {...register('message')} placeholder="Message" />
      <button type="submit">Send Message</button>
    </form>
  );
}
```

### Pattern 4: Copy to Clipboard Toast

```jsx
function CopyButton({ text }) {
  const handleCopy = async () => {
    try {
      await navigator.clipboard.writeText(text);
      toast.success('Copied to clipboard!', {
        icon: '📋',
        duration: 2000,
      });
    } catch (error) {
      toast.error('Failed to copy', {
        duration: 2000,
      });
    }
  };

  return <button onClick={handleCopy}>Copy Text</button>;
}
```

### Pattern 5: Offline/Online Toast

```jsx
import { useEffect } from 'react';
import toast from 'react-hot-toast';

function NetworkStatus() {
  useEffect(() => {
    const handleOnline = () => {
      toast.success('Back online!', {
        icon: '🌐',
        duration: 3000,
      });
    };

    const handleOffline = () => {
      toast.error('You are offline', {
        icon: '📡',
        duration: Infinity, // Stay until back online
      });
    };

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return null;
}

// Add to App
function App() {
  return (
    <>
      <NetworkStatus />
      <Toaster />
      <YourApp />
    </>
  );
}
```

## Complete API Reference

### Toast Methods

| Method | Description | Example |
|--------|-------------|---------|
| `toast.success()` | Show success message | `toast.success('Done!')` |
| `toast.error()` | Show error message | `toast.error('Failed!')` |
| `toast.loading()` | Show loading state | `toast.loading('Loading...')` |
| `toast.promise()` | Handle promise | `toast.promise(promise, options)` |
| `toast.custom()` | Custom toast | `toast.custom(component)` |
| `toast.dismiss()` | Remove toast | `toast.dismiss(id)` |
| `toast.remove()` | Remove instantly | `toast.remove(id)` |

### Toast Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `duration` | number | 3000 | How long to show (ms) |
| `position` | string | 'top-center' | Where to show |
| `icon` | string/element | null | Custom icon |
| `style` | object | {} | Custom styles |
| `className` | string | '' | CSS class |
| `iconTheme` | object | {} | Icon colors |
| `ariaProps` | object | {} | Accessibility props |

### Position Options

```
'top-left'       'top-center'       'top-right'
    ↓                  ↓                  ↓
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│                                         │
│                                         │
│                                         │
└─────────────────────────────────────────┘
    ↑                  ↑                  ↑
'bottom-left'    'bottom-center'    'bottom-right'
```

### Toaster Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `position` | string | 'top-center' | Default position |
| `reverseOrder` | boolean | false | New toasts on top |
| `gutter` | number | 8 | Space between toasts |
| `containerStyle` | object | {} | Container styles |
| `toastOptions` | object | {} | Default toast options |

## Troubleshooting

Problem 1: Toasts not showing

Solutions:
- Ensure Toaster component is added
- Check Toaster is rendered in component tree
- Verify toast methods are being called

Problem 2: Toasts disappearing too fast

Solutions:
- Increase duration option
- Use Infinity for permanent toasts

```jsx
toast.success('Important message', { duration: 10000 });
toast.loading('Stay here', { duration: Infinity });
```

Problem 3: Toasts stacking weirdly

Solutions:
- Adjust gutter option
- Change reverseOrder

```jsx
<Toaster
  gutter={12}
  reverseOrder={false}
/>
```

Problem 4: Custom styling not applying

Solutions:
- Check className is spelled correctly
- Ensure styles have higher specificity
- Use inline styles for guaranteed application

## Useful Resources

- React Hot Toast Docs: https://react-hot-toast.com/
- GitHub Repository: https://github.com/timolins/react-hot-toast
- Examples: https://react-hot-toast.com/docs

---

Created for beginners learning React Hot Toast notifications
