# Zod - Complete Beginner's Guide

A comprehensive from-scratch guide to Zod schema validation.

## Table of Contents

1. [What is Zod](#what-is-zod)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [Primitive Types](#primitive-types)
5. [Objects](#objects)
6. [Arrays](#arrays)
7. [Unions and Enums](#unions-and-enums)
8. [Optional and Nullable](#optional-and-nullable)
9. [Strings](#strings)
10. [Numbers](#numbers)
11. [Dates](#dates)
12. [Custom Validation](#custom-validation)
13. [Transforms](#transforms)
14. [Error Handling](#error-handling)
15. [Inferring Types](#inferring-types)
16. [Complete Examples](#complete-examples)
17. [API Reference](#api-reference)

---

## What is Zod

Zod is a TypeScript-first schema validation library that helps you validate data shapes and types.

**What Zod does:**

- Validates that data matches your expected shape
- Provides clear error messages
- Infers TypeScript types automatically
- Works at runtime (unlike TypeScript alone)

**Why Zod:**

```typescript
// Without Zod (runtime validation is manual and messy)
function validateUser(data: any) {
  if (typeof data.name !== "string") throw new Error("Name must be string");
  if (typeof data.age !== "number") throw new Error("Age must be number");
  if (data.age < 0) throw new Error("Age must be positive");
  // ... more manual checks
}

// With Zod - clean and declarative!
const userSchema = z.object({
  name: z.string(),
  age: z.number().min(0),
});

const validated = userSchema.parse(data);
```

## Installation

```bash
npm install zod
```

## Basic Setup

### Creating and Using Schemas

```javascript
import { z } from "zod";

// 1. Define a schema
const userSchema = z.object({
  name: z.string(),
  age: z.number(),
  email: z.string().email(),
});

// 2. Validate data
const validData = {
  name: "John",
  age: 25,
  email: "john@example.com",
};

// parse() - throws error if invalid
const result1 = userSchema.parse(validData);
console.log(result1); // { name: 'John', age: 25, email: 'john@example.com' }

// safeParse() - returns object with success flag
const result2 = userSchema.safeParse(validData);
console.log(result2);
// { success: true, data: { name: 'John', age: 25, email: 'john@example.com' } }

// Invalid data
const invalidData = {
  name: "John",
  age: "twenty five", // wrong type
  email: "not-an-email",
};

const result3 = userSchema.safeParse(invalidData);
console.log(result3);
// {
//   success: false,
//   error: ZodError {
//     issues: [
//       { code: 'invalid_type', path: ['age'], message: 'Expected number, received string' },
//       { code: 'invalid_string', path: ['email'], message: 'Invalid email' }
//     ]
//   }
// }
```

### parse vs safeParse

```javascript
// parse() - throws error (use when you're sure data is valid)
try {
  const data = userSchema.parse(unknownData);
  // Use data safely
} catch (error) {
  // Handle validation error
}

// safeParse() - returns result object (use for user input)
const result = userSchema.safeParse(userInput);
if (result.success) {
  // Data is valid
  console.log(result.data);
} else {
  // Data is invalid
  console.log(result.error.errors);
}
```

---

## Primitive Types

### Basic Types

```javascript
import { z } from "zod";

// String
z.string(); // Any string
z.string().min(1); // Non-empty string
z.string().max(100); // Max 100 chars

// Number
z.number(); // Any number
z.number().int(); // Integer only
z.number().positive(); // Positive number
z.number().negative(); // Negative number
z.number().nonnegative(); // >= 0
z.number().safe(); // Finite number (not Infinity)

// Boolean
z.boolean(); // true or false

// BigInt
z.bigint(); // BigInt type

// Date
z.date(); // Date object

// Undefined/Null/Void
z.undefined(); // undefined only
z.null(); // null only
z.void(); // undefined or null
z.any(); // Any value (no validation)
z.unknown(); // Unknown value (must be refined)

// Literals
z.literal("hello"); // Must be exactly 'hello'
z.literal(42); // Must be exactly 42
z.literal(true); // Must be exactly true

// Examples
const stringSchema = z.string();
stringSchema.parse("hello"); // OK
stringSchema.parse(123); // Error

const literalSchema = z.literal("red");
literalSchema.parse("red"); // OK
literalSchema.parse("blue"); // Error
```

---

## Objects

### Basic Objects

```javascript
import { z } from "zod";

// Simple object
const userSchema = z.object({
  name: z.string(),
  age: z.number(),
});

// Nested object
const addressSchema = z.object({
  street: z.string(),
  city: z.string(),
  zipCode: z.string().length(5),
});

const personSchema = z.object({
  name: z.string(),
  address: addressSchema,
});

// Usage
const person = {
  name: "John",
  address: {
    street: "123 Main St",
    city: "Boston",
    zipCode: "02101",
  },
};

const validated = personSchema.parse(person);
```

### Object Methods

```javascript
const schema = z.object({
  name: z.string(),
  age: z.number(),
  email: z.string().email(),
});

// .shape - access the shape
console.log(schema.shape); // { name: ..., age: ..., email: ... }

// .partial() - make all fields optional
const partialSchema = schema.partial();
// Now name, age, email are all optional

// .required() - make all fields required
const requiredSchema = schema.required();

// .pick() - select specific fields
const pickedSchema = schema.pick({ name: true, email: true });
// Only name and email

// .omit() - exclude specific fields
const omittedSchema = schema.omit({ age: true });
// All fields except age

// .extend() - add fields
const extendedSchema = schema.extend({
  phone: z.string(),
});

// .merge() - merge two objects
const baseSchema = z.object({ id: z.number() });
const mergedSchema = baseSchema.merge(schema);
// { id, name, age, email }

// Examples
const user = {
  name: "John",
  age: 25,
  email: "john@example.com",
};

// Partial allows missing fields
partialSchema.parse({ name: "John" }); // OK

// Pick only specific fields
pickedSchema.parse({ name: "John", email: "john@example.com" }); // OK
```

### Strict vs Passthrough

```javascript
// strict() - rejects unknown fields
const strictSchema = z
  .object({
    name: z.string(),
  })
  .strict();

strictSchema.parse({ name: "John", extra: "field" }); // Error!

// passthrough() - allows unknown fields
const passthroughSchema = z
  .object({
    name: z.string(),
  })
  .passthrough();

passthroughSchema.parse({ name: "John", extra: "field" }); // OK, returns all fields

// strip() - removes unknown fields (default)
const stripSchema = z.object({
  name: z.string(),
});

stripSchema.parse({ name: "John", extra: "field" }); // OK, returns { name: 'John' }
```

---

## Arrays

### Basic Arrays

```javascript
import { z } from "zod";

// Array of strings
const stringArraySchema = z.array(z.string());

// Array of numbers
const numberArraySchema = z.array(z.number());

// Array of objects
const userArraySchema = z.array(
  z.object({
    name: z.string(),
    age: z.number(),
  }),
);

// Usage
stringArraySchema.parse(["a", "b", "c"]); // OK
stringArraySchema.parse(["a", 1, "c"]); // Error

// Non-empty array
const nonEmptySchema = z.array(z.string()).min(1);

// Array with min/max length
const boundedSchema = z.array(z.number()).min(2).max(5);
```

### Array Methods

```javascript
// .nonempty() - array must have at least one element
const nonEmptySchema = z.array(z.string()).nonempty();

// .min() - minimum length
const minSchema = z.array(z.number()).min(3);

// .max() - maximum length
const maxSchema = z.array(z.number()).max(10);

// .length() - exact length
const exactSchema = z.array(z.string()).length(3);

// Examples
const colors = z.array(z.string());

colors.parse(["red", "blue"]); // OK
colors.parse([]); // OK (empty array allowed)

const nonEmptyColors = colors.nonempty();
nonEmptyColors.parse(["red"]); // OK
nonEmptyColors.parse([]); // Error
```

### Tuples

```javascript
// Tuple - fixed length array with specific types at each position
const tupleSchema = z.tuple([
  z.string(), // position 0 must be string
  z.number(), // position 1 must be number
  z.boolean(), // position 2 must be boolean
]);

// Usage
tupleSchema.parse(["hello", 42, true]); // OK
tupleSchema.parse(["hello", 42]); // Error (too short)
tupleSchema.parse(["hello", 42, "true"]); // Error (wrong type)

// Real-world example: coordinates
const coordinateSchema = z.tuple([
  z.number().min(-90).max(90), // latitude
  z.number().min(-180).max(180), // longitude
]);

const location = coordinateSchema.parse([51.505, -0.09]);
```

---

## Unions and Enums

### Unions

```javascript
import { z } from "zod";

// Union of types
const stringOrNumber = z.union([z.string(), z.number()]);

// Usage
stringOrNumber.parse("hello"); // OK
stringOrNumber.parse(42); // OK
stringOrNumber.parse(true); // Error

// Shorthand with .or()
const stringOrNumber2 = z.string().or(z.number());

// Union of objects
const successResponse = z.object({
  status: z.literal("success"),
  data: z.object({ id: z.number() }),
});

const errorResponse = z.object({
  status: z.literal("error"),
  message: z.string(),
});

const apiResponse = z.union([successResponse, errorResponse]);

// Usage
apiResponse.parse({
  status: "success",
  data: { id: 123 },
}); // OK

apiResponse.parse({
  status: "error",
  message: "Something went wrong",
}); // OK
```

### Discriminated Unions

```javascript
// When objects share a common field
const dogSchema = z.object({
  type: z.literal("dog"),
  breed: z.string(),
  barkVolume: z.number(),
});

const catSchema = z.object({
  type: z.literal("cat"),
  breed: z.string(),
  independentLevel: z.number(),
});

const petSchema = z.discriminatedUnion("type", [dogSchema, catSchema]);

// Usage
petSchema.parse({
  type: "dog",
  breed: "Labrador",
  barkVolume: 80,
}); // OK

petSchema.parse({
  type: "cat",
  breed: "Siamese",
  independentLevel: 9,
}); // OK

petSchema.parse({
  type: "dog",
  breed: "Poodle",
  independentLevel: 5, // Should be barkVolume!
}); // Error
```

### Enums

```javascript
// Using z.enum()
const ColorEnum = z.enum(['red', 'green', 'blue']);

// Usage
ColorEnum.parse('red'); // OK
ColorEnum.parse('yellow'); // Error

// Get enum values as array
const colors = ColorEnum.options; // ['red', 'green', 'blue']

// Native enum
enum Color {
  Red = 'red',
  Green = 'green',
  Blue = 'blue'
}

const colorSchema = z.nativeEnum(Color);
colorSchema.parse(Color.Red); // OK
```

---

## Optional and Nullable

### Optional Fields

```javascript
// Optional field (can be undefined)
const schema = z.object({
  name: z.string(),
  age: z.optional(z.number()),
});

// Or using .optional()
const schema2 = z.object({
  name: z.string(),
  age: z.number().optional(),
});

// Usage
schema.parse({ name: "John" }); // OK (age omitted)
schema.parse({ name: "John", age: 25 }); // OK
schema.parse({ name: "John", age: undefined }); // OK

// Optional with default value
const withDefault = z.object({
  name: z.string(),
  age: z.number().optional().default(18),
});

withDefault.parse({ name: "John" }); // { name: 'John', age: 18 }
```

### Nullable

```javascript
// Nullable (can be null)
const schema = z.object({
  name: z.string(),
  middleName: z.nullable(z.string()),
});

// Usage
schema.parse({ name: "John", middleName: null }); // OK
schema.parse({ name: "John", middleName: "William" }); // OK
schema.parse({ name: "John" }); // Error (middleName required)

// Optional and nullable combined
const flexible = z.object({
  name: z.string(),
  nickname: z.optional(z.nullable(z.string())),
});

// Can be: omitted, null, or string
flexible.parse({ name: "John" }); // OK
flexible.parse({ name: "John", nickname: null }); // OK
flexible.parse({ name: "John", nickname: "Johnny" }); // OK
```

---

## Strings

### String Validations

```javascript
import { z } from "zod";

// Basic string
z.string();

// Length constraints
z.string().min(3); // At least 3 characters
z.string().max(100); // At most 100 characters
z.string().length(10); // Exactly 10 characters
z.string().nonempty(); // At least 1 character (not just whitespace)

// Content validation
z.string().email(); // Valid email format
z.string().url(); // Valid URL
z.string().uuid(); // Valid UUID
z.string().cuid(); // Valid CUID
z.string().regex(/^[A-Z]+$/); // Custom regex
z.string().includes("hello"); // Contains substring
z.string().startsWith("Hi"); // Starts with
z.string().endsWith("!"); // Ends with

// Transformations
z.string().trim(); // Remove whitespace
z.string().toLowerCase(); // Convert to lowercase
z.string().toUpperCase(); // Convert to uppercase

// Examples
const emailSchema = z.string().email();
emailSchema.parse("john@example.com"); // OK
emailSchema.parse("not-an-email"); // Error

const usernameSchema = z
  .string()
  .min(3)
  .max(20)
  .regex(/^[a-zA-Z0-9_]+$/);

usernameSchema.parse("john_doe"); // OK
usernameSchema.parse("jo"); // Error (too short)
usernameSchema.parse("john@doe"); // Error (invalid character)

const passwordSchema = z
  .string()
  .min(8)
  .regex(/[A-Z]/, "Must contain uppercase")
  .regex(/[0-9]/, "Must contain number")
  .regex(/[^a-zA-Z0-9]/, "Must contain special character");

passwordSchema.parse("Password123!"); // OK
passwordSchema.parse("weak"); // Error
```

---

## Numbers

### Number Validations

```javascript
import { z } from "zod";

// Basic number
z.number();

// Type checks
z.number().int(); // Integer only
z.number().finite(); // Finite (not Infinity)
z.number().safe(); // Safe integer (within Number.MAX_SAFE_INTEGER)

// Range constraints
z.number().min(0); // >= 0
z.number().max(100); // <= 100
z.number().positive(); // > 0
z.number().nonnegative(); // >= 0
z.number().negative(); // < 0
z.number().nonpositive(); // <= 0

// Multiple constraints
z.number().min(0).max(100).int();

// Examples
const ageSchema = z.number().int().min(0).max(150);

ageSchema.parse(25); // OK
ageSchema.parse(-5); // Error
ageSchema.parse(200); // Error
ageSchema.parse(25.5); // Error (not integer)

const percentageSchema = z.number().min(0).max(100);

percentageSchema.parse(75); // OK
percentageSchema.parse(101); // Error

const priceSchema = z.number().positive().multipleOf(0.01); // Must be valid currency

priceSchema.parse(19.99); // OK
priceSchema.parse(19.999); // Error (not multiple of 0.01)
```

---

## Dates

### Date Validations

```javascript
import { z } from "zod";

// Basic date (must be Date object)
z.date();

// Date from string (coerce)
z.coerce.date();

// Date with constraints
z.date().min(new Date("2024-01-01")); // After or equal to
z.date().max(new Date("2024-12-31")); // Before or equal to

// Examples
const birthDateSchema = z
  .date()
  .max(new Date(), "Cannot be in future")
  .min(new Date("1900-01-01"), "Too old");

// Coerce string to date
const stringDateSchema = z.coerce.date();

stringDateSchema.parse("2024-01-01"); // Date object
stringDateSchema.parse("invalid"); // Error

// Real-world: Event scheduling
const eventSchema = z
  .object({
    startDate: z.date(),
    endDate: z.date(),
    title: z.string(),
  })
  .refine((data) => data.endDate > data.startDate, {
    message: "End date must be after start date",
    path: ["endDate"],
  });

const event = {
  startDate: new Date("2024-01-01"),
  endDate: new Date("2024-01-02"),
  title: "Conference",
};
```

---

## Custom Validation

### refine() - Custom Validation

```javascript
import { z } from "zod";

// Basic refine
const positiveNumber = z.number().refine((val) => val > 0, {
  message: "Must be positive",
});

// Refine with access to entire object
const passwordSchema = z
  .object({
    password: z.string(),
    confirmPassword: z.string(),
  })
  .refine((data) => data.password === data.confirmPassword, {
    message: "Passwords don't match",
    path: ["confirmPassword"], // Error appears on this field
  });

// Complex validation
const userSchema = z
  .object({
    username: z.string(),
    age: z.number(),
    role: z.enum(["admin", "user"]),
  })
  .refine(
    (data) => {
      // Admins must be at least 18
      if (data.role === "admin" && data.age < 18) {
        return false;
      }
      return true;
    },
    {
      message: "Admins must be 18 or older",
      path: ["age"],
    },
  );

// Async validation
const uniqueUsername = z.string().refine(
  async (username) => {
    const exists = await checkUsernameExists(username);
    return !exists;
  },
  {
    message: "Username already taken",
  },
);
```

### superRefine() - Advanced Validation

```javascript
// superRefine allows multiple errors and complex logic
const formSchema = z
  .object({
    email: z.string(),
    age: z.number(),
  })
  .superRefine((data, ctx) => {
    // Check email domain
    if (!data.email.endsWith("@company.com")) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Must use company email",
        path: ["email"],
      });
    }

    // Check age range
    if (data.age < 18) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Must be 18 or older",
        path: ["age"],
      });
    }

    // Check both together
    if (data.age > 65 && !data.email.includes("senior")) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Senior users must have senior email",
        path: ["email"],
      });
    }
  });
```

---

## Transforms

### transform() - Data Transformation

```javascript
import { z } from "zod";

// Basic transform
const stringToNumber = z.string().transform((val) => parseInt(val, 10));

stringToNumber.parse("123"); // 123 (number)

// Chain transforms
const toUpperCase = z.string().transform((val) => val.toUpperCase());
const trimAndUpper = z
  .string()
  .trim()
  .transform((val) => val.toUpperCase());

// Complex transform
const userSchema = z
  .object({
    firstName: z.string(),
    lastName: z.string(),
  })
  .transform((data) => ({
    fullName: `${data.firstName} ${data.lastName}`,
    ...data,
  }));

// Parse with transform
const result = userSchema.parse({
  firstName: "John",
  lastName: "Doe",
});
// { firstName: 'John', lastName: 'Doe', fullName: 'John Doe' }

// Real-world: API response transformation
const apiResponseSchema = z
  .object({
    data: z.unknown(),
    status: z.number(),
  })
  .transform((response) => ({
    success: response.status === 200,
    data: response.data,
    timestamp: new Date(),
  }));

// Using preprocess for raw values
const dateFromString = z.preprocess((arg) => {
  if (typeof arg === "string") {
    return new Date(arg);
  }
  return arg;
}, z.date());

dateFromString.parse("2024-01-01"); // Date object
```

---

## Error Handling

### Accessing Validation Errors

```javascript
import { z } from "zod";

const schema = z.object({
  name: z.string().min(3),
  age: z.number().min(0).max(150),
  email: z.string().email(),
});

const result = schema.safeParse({
  name: "Jo",
  age: -5,
  email: "not-email",
});

if (!result.success) {
  // Get all errors
  console.log(result.error.errors);

  // Format errors nicely
  result.error.errors.forEach((err) => {
    console.log(`Field: ${err.path.join(".")}`);
    console.log(`Error: ${err.message}`);
    console.log(`Code: ${err.code}`);
  });

  // Get errors for specific field
  const nameErrors = result.error.errors.filter((e) => e.path[0] === "name");

  // Flatten errors (simple format)
  const flattened = result.error.flatten();
  console.log(flattened.fieldErrors);
  // {
  //   name: ['String must contain at least 3 character(s)'],
  //   age: ['Number must be greater than or equal to 0'],
  //   email: ['Invalid email']
  // }
}
```

### Custom Error Messages

```javascript
const schema = z.object({
  email: z
    .string({
      required_error: "Email is required",
      invalid_type_error: "Email must be a string",
    })
    .email("Please enter a valid email address"),

  age: z
    .number({
      required_error: "Age is required",
    })
    .min(18, "You must be at least 18 years old")
    .max(120, "Age cannot exceed 120"),

  password: z
    .string()
    .min(8, "Password must be at least 8 characters")
    .regex(/[A-Z]/, "Password must contain an uppercase letter")
    .regex(/[0-9]/, "Password must contain a number"),
});

// Usage
const result = schema.safeParse(userInput);
if (!result.success) {
  const errors = result.error.errors;
  errors.forEach((err) => {
    console.log(`${err.path.join(".")}: ${err.message}`);
  });
}
```

---

## Inferring Types

### Type Inference

```javascript
import { z } from 'zod';

// Define schema
const userSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
  age: z.number().optional(),
  role: z.enum(['admin', 'user'])
});

// Infer TypeScript type from schema
type User = z.infer<typeof userSchema>;
// Equivalent to:
// type User = {
//   id: number;
//   name: string;
//   email: string;
//   age?: number;
//   role: 'admin' | 'user';
// }

// Use the type
const user: User = {
  id: 1,
  name: 'John',
  email: 'john@example.com',
  role: 'admin'
};

// With nested schemas
const addressSchema = z.object({
  street: z.string(),
  city: z.string(),
  zipCode: z.string()
});

const personSchema = z.object({
  name: z.string(),
  address: addressSchema
});

type Person = z.infer<typeof personSchema>;
// type Person = {
//   name: string;
//   address: {
//     street: string;
//     city: string;
//     zipCode: string;
//   };
// }
```

### Advanced Type Inference

```javascript
// Unions
const statusSchema = z.union([
  z.literal('active'),
  z.literal('inactive'),
  z.literal('pending')
]);
type Status = z.infer<typeof statusSchema>; // 'active' | 'inactive' | 'pending'

// Arrays
const tagsSchema = z.array(z.string());
type Tags = z.infer<typeof tagsSchema>; // string[]

// Tuples
const pointSchema = z.tuple([z.number(), z.number()]);
type Point = z.infer<typeof pointSchema>; // [number, number]

// Records
const dictSchema = z.record(z.string(), z.number());
type Dict = z.infer<typeof dictSchema>; // { [key: string]: number }

// Optional fields
const optionalSchema = z.object({
  name: z.string(),
  age: z.optional(z.number())
});
type OptionalUser = z.infer<typeof optionalSchema>;
// { name: string; age?: number }

// Nullable
const nullableSchema = z.object({
  middleName: z.nullable(z.string())
});
type WithNullable = z.infer<typeof nullableSchema>;
// { middleName: string | null }
```

---

## Complete Examples

### Example 1: User Registration Form

```javascript
import { z } from 'zod';

// Define schema
const registrationSchema = z.object({
  username: z.string()
    .min(3, 'Username must be at least 3 characters')
    .max(20, 'Username cannot exceed 20 characters')
    .regex(/^[a-zA-Z0-9_]+$/, 'Username can only contain letters, numbers, and underscores'),

  email: z.string()
    .email('Please enter a valid email address'),

  password: z.string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
    .regex(/[a-z]/, 'Password must contain at least one lowercase letter')
    .regex(/[0-9]/, 'Password must contain at least one number')
    .regex(/[^a-zA-Z0-9]/, 'Password must contain at least one special character'),

  confirmPassword: z.string(),

  age: z.number()
    .int('Age must be a whole number')
    .min(13, 'You must be at least 13 years old')
    .max(120, 'Please enter a valid age'),

  termsAccepted: z.boolean()
    .refine(val => val === true, 'You must accept the terms and conditions')
}).refine(data => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ['confirmPassword']
});

// Form validation function
function validateRegistration(formData: unknown) {
  const result = registrationSchema.safeParse(formData);

  if (!result.success) {
    // Format errors for form display
    const errors: Record<string, string> = {};
    result.error.errors.forEach(err => {
      const field = err.path[0] as string;
      errors[field] = err.message;
    });
    return { success: false, errors };
  }

  return { success: true, data: result.data };
}

// Usage
const formData = {
  username: 'john_doe',
  email: 'john@example.com',
  password: 'Password123!',
  confirmPassword: 'Password123!',
  age: 25,
  termsAccepted: true
};

const validation = validateRegistration(formData);
if (validation.success) {
  console.log('Valid data:', validation.data);
} else {
  console.log('Errors:', validation.errors);
}
```

### Example 2: API Response Validation

```javascript
import { z } from 'zod';

// Define API response schemas
const userSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
  createdAt: z.string().datetime(),
  isActive: z.boolean()
});

const paginationSchema = z.object({
  page: z.number().int().min(1),
  limit: z.number().int().min(1).max(100),
  total: z.number().int().min(0),
  totalPages: z.number().int().min(0)
});

const successResponseSchema = z.object({
  status: z.literal('success'),
  data: z.unknown(),
  pagination: paginationSchema.optional(),
  message: z.string().optional()
});

const errorResponseSchema = z.object({
  status: z.literal('error'),
  error: z.object({
    code: z.string(),
    message: z.string(),
    details: z.unknown().optional()
  })
});

const apiResponseSchema = z.discriminatedUnion('status', [
  successResponseSchema,
  errorResponseSchema
]);

// Specific response types
const usersResponseSchema = successResponseSchema.extend({
  data: z.array(userSchema)
});

type UsersResponse = z.infer<typeof usersResponseSchema>;

// API client with validation
async function fetchUsers(page: number = 1): Promise<UsersResponse> {
  const response = await fetch(`/api/users?page=${page}`);
  const data = await response.json();

  const validated = usersResponseSchema.parse(data);
  return validated;
}

// Usage
try {
  const users = await fetchUsers(1);
  console.log(`Loaded ${users.data.length} users`);
  users.data.forEach(user => {
    console.log(`${user.name} (${user.email})`);
  });
} catch (error) {
  if (error instanceof z.ZodError) {
    console.error('API response validation failed:', error.errors);
  } else {
    console.error('API request failed:', error);
  }
}
```

### Example 3: Environment Variables Validation

```javascript
import { z } from "zod";

// Define env schema
const envSchema = z.object({
  // Required
  NODE_ENV: z.enum(["development", "production", "test"]),
  PORT: z.string().transform(Number).pipe(z.number().int().positive()),
  DATABASE_URL: z.string().url(),
  API_KEY: z.string().min(32),

  // Optional with defaults
  REDIS_URL: z.string().url().optional(),
  CORS_ORIGIN: z.string().url().default("http://localhost:3000"),
  LOG_LEVEL: z.enum(["debug", "info", "warn", "error"]).default("info"),

  // Complex validation
  FEATURE_FLAGS: z
    .string()
    .transform((str) => str.split(","))
    .pipe(z.array(z.string())),
});

// Validate environment variables
function validateEnv() {
  const result = envSchema.safeParse(process.env);

  if (!result.success) {
    console.error("❌ Invalid environment variables:");
    result.error.errors.forEach((err) => {
      console.error(`  - ${err.path.join(".")}: ${err.message}`);
    });
    throw new Error("Invalid environment configuration");
  }

  return result.data;
}

// Usage
const env = validateEnv();
console.log(`Running in ${env.NODE_ENV} mode on port ${env.PORT}`);
console.log(`Feature flags: ${env.FEATURE_FLAGS.join(", ")}`);
```

### Example 4: Form Validation with React Hook Form

```jsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Define schema
const loginSchema = z.object({
  email: z.string()
    .min(1, 'Email is required')
    .email('Please enter a valid email'),

  password: z.string()
    .min(1, 'Password is required')
    .min(6, 'Password must be at least 6 characters'),

  rememberMe: z.boolean().default(false)
});

type LoginFormData = z.infer<typeof loginSchema>;

function LoginForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema)
  });

  const onSubmit = async (data: LoginFormData) => {
    console.log('Form data:', data);
    // Submit to API
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>Email</label>
        <input {...register('email')} type="email" />
        {errors.email && <span style={{ color: 'red' }}>{errors.email.message}</span>}
      </div>

      <div>
        <label>Password</label>
        <input {...register('password')} type="password" />
        {errors.password && <span style={{ color: 'red' }}>{errors.password.message}</span>}
      </div>

      <div>
        <label>
          <input {...register('rememberMe')} type="checkbox" />
          Remember me
        </label>
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}
```

---

## API Reference

### Core Methods

| Method                 | Description                | Returns                   |
| ---------------------- | -------------------------- | ------------------------- |
| `parse(data)`          | Validate and return data   | data (throws on error)    |
| `safeParse(data)`      | Validate and return result | `{ success, data/error }` |
| `parseAsync(data)`     | Async validation           | Promise\<data\>           |
| `safeParseAsync(data)` | Async safe validation      | Promise\<result\>         |

### Schema Methods

| Method             | Description              |
| ------------------ | ------------------------ |
| `.optional()`      | Make field optional      |
| `.nullable()`      | Allow null               |
| `.nullish()`       | Allow null and undefined |
| `.default(value)`  | Set default value        |
| `.transform(fn)`   | Transform value          |
| `.refine(fn)`      | Custom validation        |
| `.superRefine(fn)` | Advanced validation      |
| `.pipe(schema)`    | Chain schemas            |

### String Methods

| Method           | Description       |
| ---------------- | ----------------- |
| `.min(n)`        | Minimum length    |
| `.max(n)`        | Maximum length    |
| `.length(n)`     | Exact length      |
| `.email()`       | Email format      |
| `.url()`         | URL format        |
| `.uuid()`        | UUID format       |
| `.regex(regex)`  | Pattern matching  |
| `.trim()`        | Remove whitespace |
| `.toLowerCase()` | Lowercase         |
| `.toUpperCase()` | Uppercase         |

### Number Methods

| Method           | Description        |
| ---------------- | ------------------ |
| `.min(n)`        | Minimum value      |
| `.max(n)`        | Maximum value      |
| `.int()`         | Integer only       |
| `.positive()`    | Positive (>0)      |
| `.nonnegative()` | Non-negative (>=0) |
| `.negative()`    | Negative (<0)      |
| `.multipleOf(n)` | Multiple of n      |

### Array Methods

| Method        | Description          |
| ------------- | -------------------- |
| `.min(n)`     | Minimum length       |
| `.max(n)`     | Maximum length       |
| `.length(n)`  | Exact length         |
| `.nonempty()` | At least one element |

### Object Methods

| Method            | Description           |
| ----------------- | --------------------- |
| `.partial()`      | All fields optional   |
| `.required()`     | All fields required   |
| `.pick(keys)`     | Select fields         |
| `.omit(keys)`     | Exclude fields        |
| `.extend(fields)` | Add fields            |
| `.merge(schema)`  | Merge objects         |
| `.strict()`       | Reject unknown fields |
| `.passthrough()`  | Allow unknown fields  |

## Troubleshooting

Problem 1: TypeScript errors

Solutions:

- Ensure Zod types are imported
- Use `z.infer` for type inference
- Check schema definition matches data

Problem 2: Validation not working

Solutions:

- Verify data structure matches schema
- Check for correct method (parse vs safeParse)
- Review custom validation logic

Problem 3: Error messages not showing

Solutions:

- Check error path is correct
- Use `error.flatten()` for simpler format
- Access `error.errors` array

## Useful Resources

- Zod Documentation: https://zod.dev/
- GitHub Repository: https://github.com/colinhacks/zod
- Zod with React Hook Form: https://react-hook-form.com/get-started#SchemaValidation

---

Created for beginners learning Zod validation
