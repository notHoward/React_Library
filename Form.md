# React Form Libraries - Complete Beginner's Guide

A comprehensive from-scratch guide to React Hook Form and Formik.

## Table of Contents

1. [React Hook Form - Complete Guide](#react-hook-form---complete-guide)
2. [Formik - Complete Guide](#formik---complete-guide)
3. [Comparison & When to Use](#comparison--when-to-use)

---

# React Hook Form - Complete Guide

## Installation

```bash
npm install react-hook-form
```

## The Most Basic Form

```jsx
import { useForm } from "react-hook-form";

function SignupForm() {
  // 1. Call useForm hook - this gives you all the form methods
  const { register, handleSubmit } = useForm();

  // 2. This function runs when form is successfully submitted
  const onSubmit = (formData) => {
    console.log("Form submitted with:", formData);
    // formData = { email: 'user@example.com', password: '123456' }
  };

  // 3. Return form with register and handleSubmit
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("email")} />
      <input {...register("password")} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## Understanding Each Method

### 1. register - Connects inputs to React Hook Form

```jsx
const { register } = useForm();

// Basic usage - connects input to form state
<input {...register('email')} />
// This is the same as:
<input
  name="email"
  onChange={(e) => {/* update internal state */}}
  onBlur={(e) => {/* mark as touched */}}
  ref={(ref) => {/* store reference */}}
/>

// With validation rules
<input {...register('email', {
  required: 'Email is required',  // Error message if empty
  minLength: { value: 5, message: 'Too short' },
  maxLength: { value: 50, message: 'Too long' },
  pattern: { value: /^\S+@\S+$/, message: 'Invalid email' }
})} />

// For numbers
<input type="number" {...register('age', {
  valueAsNumber: true,  // Converts input to number automatically
  min: { value: 18, message: 'Must be 18+' },
  max: { value: 99, message: 'Too old' }
})} />

// For checkboxes
<input type="checkbox" {...register('rememberMe')} />
// Returns: true or false

// For radio buttons
<input type="radio" value="male" {...register('gender')} />
<input type="radio" value="female" {...register('gender')} />
// Returns: 'male' or 'female'

// For select dropdown
<select {...register('country')}>
  <option value="us">USA</option>
  <option value="uk">UK</option>
</select>
// Returns: 'us' or 'uk'
```

### 2. handleSubmit - Validates and submits form

```jsx
const { handleSubmit } = useForm();

// handleSubmit does TWO things:
// 1. Validates all fields
// 2. Only calls your onSubmit if validation passes

// Basic usage
<form onSubmit={handleSubmit(onSubmit)}>

// What happens internally:
// Step 1: User clicks submit button
// Step 2: handleSubmit runs validation on all registered fields
// Step 3: If validation PASSES → calls your onSubmit with form data
// Step 4: If validation FAILS → doesn't call onSubmit, sets errors

// With two handlers (success and error)
const onSuccess = (data) => console.log('Success:', data);
const onError = (errors) => console.log('Errors:', errors);

<form onSubmit={handleSubmit(onSuccess, onError)}>
```

### 3. formState - Access form state

```jsx
const { formState } = useForm();
const {
  errors, // Object containing all validation errors
  isSubmitting, // True while submit handler is running
  isDirty, // True if any field changed from initial
  isValid, // True if all validation passed
  touchedFields, // Object of fields that have been blurred
  submitCount, // Number of times form was submitted
} = formState;

// Example displaying errors
<input {...register("email", { required: "Email required" })} />;
{
  errors.email && <p style={{ color: "red" }}>{errors.email.message}</p>;
}

// Example disabling button during submission
<button type="submit" disabled={isSubmitting}>
  {isSubmitting ? "Submitting..." : "Submit"}
</button>;

// Example showing unsaved changes warning
useEffect(() => {
  if (isDirty) {
    window.onbeforeunload = () => true;
  }
  return () => {
    window.onbeforeunload = null;
  };
}, [isDirty]);
```

### 4. watch - Monitor form values in real-time

```jsx
const { watch } = useForm();
const email = watch("email"); // Watch single field
const allValues = watch(); // Watch all fields
const [email, password] = watch(["email", "password"]); // Watch multiple

// Real-world example: Password confirmation
function PasswordForm() {
  const {
    register,
    watch,
    formState: { errors },
  } = useForm();
  const password = watch("password"); // Get current password value

  return (
    <form>
      <input {...register("password")} />
      <input
        {...register("confirmPassword", {
          validate: (value) => value === password || "Passwords do not match",
        })}
      />
      {errors.confirmPassword && <p>{errors.confirmPassword.message}</p>}
    </form>
  );
}
```

### 5. setValue - Manually set field values

```jsx
const { setValue } = useForm();

// Set a single field
setValue("email", "john@example.com");

// Set with options (no validation, no touch)
setValue("email", "john@example.com", {
  shouldValidate: false, // Don't run validation
  shouldDirty: false, // Don't mark as dirty
  shouldTouch: false, // Don't mark as touched
});

// Real-world example: Pre-populate form from API
useEffect(() => {
  fetchUserData().then((user) => {
    setValue("firstName", user.firstName);
    setValue("lastName", user.lastName);
    setValue("email", user.email);
  });
}, []);
```

### 6. getValues - Get current values without re-rendering

```jsx
const { getValues } = useForm();

// Get single value
const email = getValues("email");

// Get all values
const allValues = getValues();

// Get multiple values
const [email, password] = getValues(["email", "password"]);

// Difference from watch():
// - watch() triggers re-render when value changes
// - getValues() just reads current value without re-render

// When to use getValues(): in onSubmit, in callbacks
const onSubmit = (data) => {
  console.log(data); // Same as getValues()
  const currentValues = getValues(); // Also works
};
```

### 7. reset - Reset form to initial values

```jsx
const { reset } = useForm({
  defaultValues: {
    email: "",
    password: "",
    rememberMe: false,
  },
});

// Reset to default values
reset();

// Reset to custom values
reset({
  email: "custom@example.com",
  password: "",
  rememberMe: true,
});

// Real-world example: Clear form after submit
const onSubmit = async (data) => {
  await saveToAPI(data);
  reset(); // Clear form after successful save
  alert("Form saved!");
};
```

### 8. trigger - Manually trigger validation

```jsx
const { trigger } = useForm();

// Validate all fields
const isValid = await trigger();

// Validate specific fields
const isEmailValid = await trigger("email");
const [isEmailValid, isPasswordValid] = await trigger(["email", "password"]);

// Real-world example: Validate on blur
<input
  {...register("email", { required: true })}
  onBlur={() => trigger("email")} // Validate when user leaves field
/>;
```

### 9. clearErrors - Clear validation errors

```jsx
const { clearErrors } = useForm();

// Clear all errors
clearErrors();

// Clear specific field error
clearErrors("email");

// Clear multiple field errors
clearErrors(["email", "password"]);

// Real-world example: Clear error when user starts typing
<input
  {...register("email", { required: true })}
  onChange={(e) => {
    register("email").onChange(e); // Normal onChange
    clearErrors("email"); // Clear error
  }}
/>;
```

### 10. setError - Manually set validation error

```jsx
const { setError } = useForm();

// Set error on a field
setError("email", {
  type: "manual",
  message: "Email already exists",
});

// Set server-side error
const onSubmit = async (data) => {
  try {
    await api.submit(data);
  } catch (error) {
    setError("root", {
      // 'root' is for form-level errors
      type: "server",
      message: error.message,
    });
  }
};

// Display server error
{
  errors.root && <p>{errors.root.message}</p>;
}
```

## Complete Example with All Methods

```jsx
import { useForm } from "react-hook-form";

function CompleteFormExample() {
  // 1. Initialize form with all options
  const {
    register, // Connect inputs
    handleSubmit, // Handle submission
    formState: {
      // Form state
      errors,
      isSubmitting,
      isDirty,
      isValid,
      touchedFields,
      submitCount,
    },
    watch, // Monitor values
    setValue, // Set values manually
    getValues, // Get values without re-render
    reset, // Reset form
    trigger, // Manual validation
    clearErrors, // Clear errors
    setError, // Set manual errors
  } = useForm({
    defaultValues: {
      username: "",
      email: "",
      age: 0,
      newsletter: false,
    },
    mode: "onChange", // Validate on every change
  });

  // Watch specific fields
  const username = watch("username");
  const newsletter = watch("newsletter");

  // Auto-populate from API
  useEffect(() => {
    fetchUserData().then((data) => {
      setValue("username", data.username);
      setValue("email", data.email);
      setValue("age", data.age);
      setValue("newsletter", data.newsletter);
    });
  }, [setValue]);

  // Submit handler
  const onSubmit = async (data) => {
    try {
      await api.saveUser(data);
      reset(); // Clear form on success
      alert("User saved successfully!");
    } catch (error) {
      setError("root", {
        message: "Failed to save user",
      });
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* Username Field */}
      <div>
        <label>Username</label>
        <input
          {...register("username", {
            required: "Username is required",
            minLength: {
              value: 3,
              message: "Username must be at least 3 characters",
            },
            maxLength: {
              value: 20,
              message: "Username cannot exceed 20 characters",
            },
          })}
          placeholder="Enter username"
        />
        {errors.username && (
          <span style={{ color: "red" }}>{errors.username.message}</span>
        )}
        {touchedFields.username && !errors.username && (
          <span style={{ color: "green" }}>✓ Valid</span>
        )}
        <small>Currently typing: {username}</small>
      </div>

      {/* Email Field */}
      <div>
        <label>Email</label>
        <input
          type="email"
          {...register("email", {
            required: "Email is required",
            pattern: {
              value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
              message: "Invalid email address",
            },
          })}
          onBlur={() => trigger("email")} // Validate on blur
          placeholder="Enter email"
        />
        {errors.email && (
          <span style={{ color: "red" }}>{errors.email.message}</span>
        )}
      </div>

      {/* Age Field */}
      <div>
        <label>Age</label>
        <input
          type="number"
          {...register("age", {
            required: "Age is required",
            min: {
              value: 18,
              message: "You must be 18 or older",
            },
            max: {
              value: 99,
              message: "Age cannot exceed 99",
            },
            valueAsNumber: true,
          })}
          placeholder="Enter age"
        />
        {errors.age && (
          <span style={{ color: "red" }}>{errors.age.message}</span>
        )}
      </div>

      {/* Newsletter Checkbox */}
      <div>
        <label>
          <input type="checkbox" {...register("newsletter")} />
          Subscribe to newsletter
        </label>
        {newsletter && <p>You'll receive our weekly newsletter</p>}
      </div>

      {/* Form-level error */}
      {errors.root && <div style={{ color: "red" }}>{errors.root.message}</div>}

      {/* Form Status */}
      <div>
        <p>Form dirty: {isDirty ? "Yes" : "No"}</p>
        <p>Form valid: {isValid ? "Yes" : "No"}</p>
        <p>Submit attempts: {submitCount}</p>
      </div>

      {/* Buttons */}
      <button type="submit" disabled={isSubmitting || !isValid}>
        {isSubmitting ? "Saving..." : "Save User"}
      </button>

      <button
        type="button"
        onClick={() => {
          clearErrors(); // Clear all errors
          reset(); // Reset form
        }}
      >
        Reset Form
      </button>

      <button
        type="button"
        onClick={() => {
          console.log("Current values:", getValues());
        }}
      >
        Debug - Log Values
      </button>
    </form>
  );
}
```

---

# Formik - Complete Guide

## Installation

```bash
npm install formik
npm install yup  # For validation (recommended)
```

## The Most Basic Form

```jsx
import { useFormik } from "formik";

function SignupForm() {
  // 1. Call useFormik with configuration
  const formik = useFormik({
    initialValues: {
      // Starting values for all fields
      email: "",
      password: "",
    },
    onSubmit: (values) => {
      // Runs when form is submitted
      console.log("Form data:", values);
    },
  });

  // 2. Connect formik to your JSX
  return (
    <form onSubmit={formik.handleSubmit}>
      <input
        id="email"
        name="email"
        value={formik.values.email}
        onChange={formik.handleChange}
      />
      <input
        id="password"
        name="password"
        value={formik.values.password}
        onChange={formik.handleChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## Understanding Each Formik Method

### 1. initialValues - Starting state of form

```jsx
const formik = useFormik({
  initialValues: {
    firstName: "",
    lastName: "",
    email: "",
    age: 0,
    newsletter: false,
    gender: "",
    country: "",
  },
});

// Formik automatically tracks changes from these initial values
```

### 2. onSubmit - What happens when form is submitted

```jsx
const formik = useFormik({
  onSubmit: async (values, { setSubmitting, resetForm }) => {
    // values: contains all form data
    console.log("Submitting:", values);

    try {
      await api.save(values);
      resetForm(); // Clear form after success
      alert("Saved!");
    } catch (error) {
      console.error("Error:", error);
    } finally {
      setSubmitting(false); // Re-enable submit button
    }
  },
});
```

### 3. handleSubmit - Form submission handler

```jsx
// In your JSX
<form onSubmit={formik.handleSubmit}>

// What handleSubmit does:
// 1. Prevents default browser form submission
// 2. Runs validation (if configured)
// 3. If validation passes, calls your onSubmit
// 4. If validation fails, sets errors and doesn't submit

// You don't need to write:
// const handleSubmit = (e) => {
//   e.preventDefault();
//   if (validate()) onSubmit();
// }
// Formik does it for you!
```

### 4. handleChange - Handles input changes

```jsx
<input
  name="email"
  value={formik.values.email}
  onChange={formik.handleChange}
/>

// What handleChange does:
// 1. Gets the input name and value from event
// 2. Updates formik.values[name] to new value
// 3. Marks field as "touched" (optional)

// It automatically handles all input types:
// - Text inputs → string value
// - Checkboxes → boolean value
// - Radio buttons → string value
// - Select dropdown → selected value
```

### 5. values - Current form data

```jsx
const { values } = formik;

// Access individual values
const email = values.email;
const fullName = `${values.firstName} ${values.lastName}`;

// Display current form data
<pre>{JSON.stringify(values, null, 2)}</pre>;

// Use in conditional rendering
{
  values.newsletter && <div>You'll receive our newsletter</div>;
}
```

### 6. errors - Validation errors

```jsx
import * as Yup from "yup";

const formik = useFormik({
  validationSchema: Yup.object({
    email: Yup.string().email("Invalid email").required("Required"),
    password: Yup.string().min(6, "Too short").required("Required"),
  }),
  onSubmit: (values) => console.log(values),
});

// Display errors
<input name="email" {...formik.getFieldProps("email")} />;
{
  formik.errors.email && formik.touched.email && (
    <div style={{ color: "red" }}>{formik.errors.email}</div>
  );
}
```

### 7. touched - Track which fields have been visited

```jsx
// touched object tracks which fields have been blurred
// Example: { email: true, password: false }

// Show error only after user has visited field
{
  formik.touched.email && formik.errors.email && (
    <div>{formik.errors.email}</div>
  );
}

// Mark field as touched manually
const handleBlur = (e) => {
  formik.handleBlur(e);
  // formik.touched[e.target.name] becomes true
};
```

### 8. setFieldValue - Manually set a field's value

```jsx
const { setFieldValue } = formik;

// Set single field
setFieldValue("email", "john@example.com");

// With options (skip validation)
setFieldValue("email", "john@example.com", false);

// Real-world: Populate form from API
useEffect(() => {
  fetchUser().then((user) => {
    setFieldValue("firstName", user.firstName);
    setFieldValue("lastName", user.lastName);
    setFieldValue("email", user.email);
  });
}, []);
```

### 9. setFieldTouched - Manually mark field as touched

```jsx
const { setFieldTouched } = formik;

// Mark email as touched
setFieldTouched("email", true);

// Real-world: Validate on button click
const validateForm = () => {
  setFieldTouched("email", true);
  setFieldTouched("password", true);
};
```

### 10. setFieldError - Manually set field error

```jsx
const { setFieldError } = formik;

// Set error on field
setFieldError("email", "Email already exists");

// Real-world: Handle server validation errors
const onSubmit = async (values) => {
  try {
    await api.save(values);
  } catch (error) {
    if (error.field === "email") {
      setFieldError("email", error.message);
    }
  }
};
```

### 11. setSubmitting - Control submit button state

```jsx
const { isSubmitting, setSubmitting } = formik;

// In onSubmit
onSubmit: async (values, { setSubmitting }) => {
  setSubmitting(true); // Disable submit button

  try {
    await api.save(values);
  } finally {
    setSubmitting(false); // Re-enable submit button
  }
};

// In JSX
<button type="submit" disabled={isSubmitting}>
  {isSubmitting ? "Saving..." : "Save"}
</button>;
```

### 12. resetForm - Reset to initial values

```jsx
const { resetForm } = formik;

// Reset to initial values
resetForm();

// Reset with new values
resetForm({
  values: { email: "", password: "" },
  touched: {},
  errors: {},
});

// Real-world: Clear form after submit
onSubmit: async (values, { resetForm }) => {
  await api.save(values);
  resetForm();
  alert("Form cleared!");
};
```

### 13. getFieldProps - Get props for field (shorthand)

```jsx
// Instead of writing this:
<input
  name="email"
  value={formik.values.email}
  onChange={formik.handleChange}
  onBlur={formik.handleBlur}
/>

// You can write this:
<input {...formik.getFieldProps('email')} />

// getFieldProps returns:
{
  name: 'email',
  value: formik.values.email,
  onChange: formik.handleChange,
  onBlur: formik.handleBlur
}
```

### 14. validateForm - Manually validate entire form

```jsx
const { validateForm } = formik;

// Validate all fields
const errors = await validateForm();

// Real-world: Validate before navigation
const handleNextPage = async () => {
  const errors = await validateForm();
  if (Object.keys(errors).length === 0) {
    // No errors, proceed to next page
    nextPage();
  }
};
```

## Complete Formik Example

```jsx
import { useFormik } from "formik";
import * as Yup from "yup";

function CompleteFormikForm() {
  // 1. Configure formik with all options
  const formik = useFormik({
    // Starting values
    initialValues: {
      firstName: "",
      lastName: "",
      email: "",
      age: 0,
      gender: "",
      newsletter: false,
      comments: "",
    },

    // Validation schema
    validationSchema: Yup.object({
      firstName: Yup.string()
        .required("First name is required")
        .min(2, "Too short")
        .max(50, "Too long"),

      lastName: Yup.string().required("Last name is required"),

      email: Yup.string().email("Invalid email").required("Email is required"),

      age: Yup.number()
        .min(18, "Must be 18 or older")
        .max(99, "Must be 99 or younger")
        .required("Age is required"),

      gender: Yup.string().required("Please select gender"),

      newsletter: Yup.boolean(),

      comments: Yup.string().max(500, "Too long"),
    }),

    // Submit handler
    onSubmit: async (values, { setSubmitting, resetForm, setFieldError }) => {
      try {
        console.log("Submitting:", values);

        // Simulate API call
        await new Promise((resolve) => setTimeout(resolve, 2000));

        // Success
        alert("Form submitted successfully!");
        resetForm(); // Clear form
      } catch (error) {
        // Handle error
        setFieldError("root", error.message);
      } finally {
        setSubmitting(false);
      }
    },

    // Validate on change
    validateOnChange: true,

    // Validate on blur
    validateOnBlur: true,
  });

  // Helper to show error
  const showError = (field) => {
    return formik.touched[field] && formik.errors[field];
  };

  return (
    <form onSubmit={formik.handleSubmit}>
      {/* First Name */}
      <div>
        <label htmlFor="firstName">First Name *</label>
        <input
          id="firstName"
          name="firstName"
          type="text"
          {...formik.getFieldProps("firstName")}
          placeholder="Enter first name"
        />
        {showError("firstName") && (
          <div style={{ color: "red", fontSize: "14px" }}>
            {formik.errors.firstName}
          </div>
        )}
      </div>

      {/* Last Name */}
      <div>
        <label htmlFor="lastName">Last Name *</label>
        <input
          id="lastName"
          name="lastName"
          type="text"
          {...formik.getFieldProps("lastName")}
          placeholder="Enter last name"
        />
        {showError("lastName") && (
          <div style={{ color: "red", fontSize: "14px" }}>
            {formik.errors.lastName}
          </div>
        )}
      </div>

      {/* Email */}
      <div>
        <label htmlFor="email">Email *</label>
        <input
          id="email"
          name="email"
          type="email"
          {...formik.getFieldProps("email")}
          placeholder="Enter email"
        />
        {showError("email") && (
          <div style={{ color: "red", fontSize: "14px" }}>
            {formik.errors.email}
          </div>
        )}
      </div>

      {/* Age */}
      <div>
        <label htmlFor="age">Age *</label>
        <input
          id="age"
          name="age"
          type="number"
          {...formik.getFieldProps("age")}
          placeholder="Enter age"
        />
        {showError("age") && (
          <div style={{ color: "red", fontSize: "14px" }}>
            {formik.errors.age}
          </div>
        )}
      </div>

      {/* Gender - Radio Buttons */}
      <div>
        <label>Gender *</label>
        <div>
          <label>
            <input
              type="radio"
              name="gender"
              value="male"
              checked={formik.values.gender === "male"}
              onChange={formik.handleChange}
              onBlur={formik.handleBlur}
            />
            Male
          </label>
          <label>
            <input
              type="radio"
              name="gender"
              value="female"
              checked={formik.values.gender === "female"}
              onChange={formik.handleChange}
              onBlur={formik.handleBlur}
            />
            Female
          </label>
          <label>
            <input
              type="radio"
              name="gender"
              value="other"
              checked={formik.values.gender === "other"}
              onChange={formik.handleChange}
              onBlur={formik.handleBlur}
            />
            Other
          </label>
        </div>
        {showError("gender") && (
          <div style={{ color: "red", fontSize: "14px" }}>
            {formik.errors.gender}
          </div>
        )}
      </div>

      {/* Newsletter - Checkbox */}
      <div>
        <label>
          <input
            type="checkbox"
            name="newsletter"
            checked={formik.values.newsletter}
            onChange={formik.handleChange}
            onBlur={formik.handleBlur}
          />
          Subscribe to newsletter
        </label>
      </div>

      {/* Comments - Textarea */}
      <div>
        <label htmlFor="comments">Comments</label>
        <textarea
          id="comments"
          name="comments"
          {...formik.getFieldProps("comments")}
          rows="4"
          placeholder="Enter your comments"
        />
        {showError("comments") && (
          <div style={{ color: "red", fontSize: "14px" }}>
            {formik.errors.comments}
          </div>
        )}
        <div>{formik.values.comments.length}/500 characters</div>
      </div>

      {/* Form-level error */}
      {formik.errors.root && (
        <div style={{ color: "red", padding: "10px", background: "#ffeeee" }}>
          {formik.errors.root}
        </div>
      )}

      {/* Form Status */}
      <div style={{ fontSize: "12px", color: "#666" }}>
        <p>Form dirty: {formik.dirty ? "Yes" : "No"}</p>
        <p>Form valid: {formik.isValid ? "Yes" : "No"}</p>
        <p>Submit count: {formik.submitCount}</p>
      </div>

      {/* Buttons */}
      <div>
        <button type="submit" disabled={formik.isSubmitting || !formik.isValid}>
          {formik.isSubmitting ? "Submitting..." : "Submit"}
        </button>

        <button
          type="button"
          onClick={() => {
            formik.resetForm();
            alert("Form reset!");
          }}
        >
          Reset
        </button>

        <button
          type="button"
          onClick={() => {
            console.log("Current values:", formik.values);
            console.log("Current errors:", formik.errors);
          }}
        >
          Debug - Log Values
        </button>
      </div>
    </form>
  );
}
```

---

## Comparison & When to Use

### React Hook Form is better for:

- Large forms with many fields
- Performance-critical applications
- Forms with complex validation
- Dynamic forms with add/remove fields
- File uploads

### Formik is better for:

- Simple to medium forms
- Teams familiar with Formik
- Quick prototyping
- Integration with Yup validation
- Less boilerplate for simple forms

### Quick Decision Guide

```jsx
// Use React Hook Form if:
// - Form has 20+ fields
// - Need best performance
// - Complex validation rules
// - Dynamic field arrays

// Use Formik if:
// - Form has 5-10 fields
// - Quick development needed
// - Team already knows Formik
// - Simple validation needs
```

---

## Useful Resources

- React Hook Form Docs: https://react-hook-form.com/
- Formik Docs: https://formik.org/
- Yup Validation: https://github.com/jquense/yup

---

Created for beginners learning React form libraries
