---
title: TanStack Form Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# TanStack Form Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate form handling 
code using TanStack Form (formerly React Form) following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Naming Conventions

- Name form components with "Form" suffix (e.g., `LoginForm`, `UserProfileForm`, 
`ContactForm`).
- Name form validation schemas with "Schema" suffix (e.g., `loginSchema`, 
`userSchema`).
- Prefix form submit handlers with "onSubmit" or "handleSubmit".
- Name field names in lowercase with camelCase (e.g., `email`, `firstName`, 
`phoneNumber`).
- Use descriptive names for form state variables (e.g., `isSubmitting`, 
`canSubmit`, `isFieldsValid`).
- Name custom validators with "validate" prefix (e.g., `validateEmail`, 
`validatePassword`).

### Form Setup

- Always use the `useForm` hook to initialize forms:
  ```typescript
  import { useForm } from '@tanstack/react-form';
  
  type FormData = {
    email: string;
    password: string;
  };
  
  function LoginForm() {
    const form = useForm<FormData>({
      defaultValues: {
        email: '',
        password: '',
      },
      onSubmit: async ({ value }) => {
        // Handle form submission
        await submitLogin(value);
      },
    });
    
    return (
      <form
        onSubmit={(e) => {
          e.preventDefault();
          e.stopPropagation();
          form.handleSubmit();
        }}
      >
        {/* Form fields */}
      </form>
    );
  }
  ```

- Provide default values in the form configuration:
  ```typescript
  const form = useForm({
    defaultValues: {
      email: '',
      password: '',
      rememberMe: false,
    },
    onSubmit: async ({ value }) => {
      console.log(value);
    },
  });
  ```

- Use typed forms for better TypeScript support:
  ```typescript
  type UserFormData = {
    name: string;
    email: string;
    age: number;
  };
  
  const form = useForm<UserFormData>({
    defaultValues: {
      name: '',
      email: '',
      age: 0,
    },
    onSubmit: async ({ value }) => {
      // value is properly typed as UserFormData
      await createUser(value);
    },
  });
  ```

### Field Registration

- Use `form.Field` component for field registration and validation:
  ```typescript
  <form.Field
    name="email"
    validators={{
      onChange: ({ value }) => {
        if (!value) return 'Email is required';
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          return 'Invalid email format';
        }
        return undefined;
      },
    }}
  >
    {(field) => (
      <div>
        <label htmlFor={field.name}>Email:</label>
        <input
          id={field.name}
          name={field.name}
          value={field.state.value}
          onBlur={field.handleBlur}
          onChange={(e) => field.handleChange(e.target.value)}
        />
        {field.state.meta.errors && (
          <span className="error">{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

- Use inline validators for simple validation:
  ```typescript
  <form.Field
    name="password"
    validators={{
      onChange: ({ value }) => {
        if (!value) return 'Password is required';
        if (value.length < 8) return 'Password must be at least 8 characters';
        return undefined;
      },
      onChangeAsyncDebounceMs: 500,
      onChangeAsync: async ({ value }) => {
        // Async validation (e.g., check password strength with API)
        const isStrong = await checkPasswordStrength(value);
        return isStrong ? undefined : 'Password is too weak';
      },
    }}
  >
    {(field) => (
      <div>
        <input
          type="password"
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          onBlur={field.handleBlur}
        />
        {field.state.meta.errors && (
          <span>{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

### Validation with Schema Libraries

- Integrate with Zod for schema-based validation:
  ```typescript
  import { zodValidator } from '@tanstack/zod-form-adapter';
  import { z } from 'zod';
  
  const userSchema = z.object({
    email: z.string().email('Invalid email address'),
    password: z.string().min(8, 'Password must be at least 8 characters'),
    age: z.number().min(18, 'Must be at least 18 years old'),
  });
  
  type UserFormData = z.infer<typeof userSchema>;
  
  function UserForm() {
    const form = useForm<UserFormData>({
      defaultValues: {
        email: '',
        password: '',
        age: 0,
      },
      onSubmit: async ({ value }) => {
        await createUser(value);
      },
      validatorAdapter: zodValidator,
    });
    
    return (
      <form
        onSubmit={(e) => {
          e.preventDefault();
          form.handleSubmit();
        }}
      >
        <form.Field
          name="email"
          validators={{
            onChange: userSchema.shape.email,
          }}
        >
          {(field) => (
            <div>
              <input
                value={field.state.value}
                onChange={(e) => field.handleChange(e.target.value)}
                onBlur={field.handleBlur}
              />
              {field.state.meta.errors && (
                <span>{field.state.meta.errors[0]}</span>
              )}
            </div>
          )}
        </form.Field>
      </form>
    );
  }
  ```

- Integrate with Yup for schema-based validation:
  ```typescript
  import { yupValidator } from '@tanstack/yup-form-adapter';
  import * as yup from 'yup';
  
  const loginSchema = yup.object({
    email: yup.string().email('Invalid email').required('Email is required'),
    password: yup.string().min(8, 'Too short').required('Password is required'),
  });
  
  type LoginFormData = yup.InferType<typeof loginSchema>;
  
  const form = useForm<LoginFormData>({
    defaultValues: {
      email: '',
      password: '',
    },
    onSubmit: async ({ value }) => {
      await login(value);
    },
    validatorAdapter: yupValidator,
  });
  ```

- Integrate with Valibot for schema-based validation:
  ```typescript
  import { valibotValidator } from '@tanstack/valibot-form-adapter';
  import * as v from 'valibot';
  
  const userSchema = v.object({
    email: v.pipe(v.string(), v.email('Invalid email')),
    password: v.pipe(v.string(), v.minLength(8, 'Too short')),
  });
  
  type UserFormData = v.InferOutput<typeof userSchema>;
  
  const form = useForm<UserFormData>({
    defaultValues: {
      email: '',
      password: '',
    },
    onSubmit: async ({ value }) => {
      await createUser(value);
    },
    validatorAdapter: valibotValidator,
  });
  ```

### TypeScript Best Practices

- Always define TypeScript types for form data:
  ```typescript
  type ContactFormData = {
    name: string;
    email: string;
    message: string;
    newsletter: boolean;
  };
  
  const form = useForm<ContactFormData>({
    defaultValues: {
      name: '',
      email: '',
      message: '',
      newsletter: false,
    },
    onSubmit: async ({ value }) => {
      // value is typed as ContactFormData
      await sendContact(value);
    },
  });
  ```

- Infer types from validation schemas:
  ```typescript
  import { z } from 'zod';
  
  const profileSchema = z.object({
    firstName: z.string().min(1, 'Required'),
    lastName: z.string().min(1, 'Required'),
    bio: z.string().optional(),
  });
  
  type ProfileFormData = z.infer<typeof profileSchema>;
  
  const form = useForm<ProfileFormData>({
    defaultValues: {
      firstName: '',
      lastName: '',
      bio: '',
    },
    onSubmit: async ({ value }) => {
      await updateProfile(value);
    },
    validatorAdapter: zodValidator,
  });
  ```

- Type custom validators properly:
  ```typescript
  const validateEmail = (value: string): string | undefined => {
    if (!value) return 'Email is required';
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
      return 'Invalid email format';
    }
    return undefined;
  };
  
  const validatePasswordMatch = (
    password: string,
    confirmPassword: string
  ): string | undefined => {
    if (password !== confirmPassword) {
      return 'Passwords do not match';
    }
    return undefined;
  };
  ```

### Error Handling

- Display field-level errors using field state:
  ```typescript
  <form.Field name="email">
    {(field) => (
      <div>
        <input
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          onBlur={field.handleBlur}
          aria-invalid={field.state.meta.errors.length > 0}
        />
        {field.state.meta.errors.length > 0 && (
          <span role="alert" className="error">
            {field.state.meta.errors[0]}
          </span>
        )}
      </div>
    )}
  </form.Field>
  ```

- Show errors only after field is touched or form submission:
  ```typescript
  <form.Field name="email">
    {(field) => (
      <div>
        <input
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          onBlur={field.handleBlur}
        />
        {field.state.meta.isTouched && field.state.meta.errors.length > 0 && (
          <span className="error">{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

- Handle form-level errors:
  ```typescript
  const form = useForm({
    defaultValues: { email: '', password: '' },
    onSubmit: async ({ value }) => {
      try {
        await login(value);
      } catch (error) {
        // Set form-level error
        throw new Error('Login failed. Please check your credentials.');
      }
    },
  });
  
  // Display form-level error
  {form.state.errors.length > 0 && (
    <div className="form-error">
      {form.state.errors[0]}
    </div>
  )}
  ```

### Form State Management

- Access form state for UI feedback:
  ```typescript
  const form = useForm({
    defaultValues: { email: '', password: '' },
    onSubmit: async ({ value }) => {
      await login(value);
    },
  });
  
  return (
    <div>
      <form.Subscribe
        selector={(state) => ({
          canSubmit: state.canSubmit,
          isSubmitting: state.isSubmitting,
          isFieldsValid: state.isFieldsValid,
        })}
      >
        {({ canSubmit, isSubmitting, isFieldsValid }) => (
          <>
            <button type="submit" disabled={!canSubmit || isSubmitting}>
              {isSubmitting ? 'Submitting...' : 'Submit'}
            </button>
            {!isFieldsValid && <p>Please fix the errors above</p>}
          </>
        )}
      </form.Subscribe>
    </div>
  );
  ```

- Reset form after submission:
  ```typescript
  const form = useForm({
    defaultValues: { email: '', password: '' },
    onSubmit: async ({ value }) => {
      await login(value);
      // Reset form after successful submission
      form.reset();
    },
  });
  ```

- Listen to form state changes:
  ```typescript
  <form.Subscribe
    selector={(state) => ({
      isDirty: state.isDirty,
      isTouched: state.isTouched,
      isValidating: state.isValidating,
    })}
  >
    {({ isDirty, isTouched, isValidating }) => (
      <div>
        {isDirty && <p>You have unsaved changes</p>}
        {isValidating && <p>Validating...</p>}
      </div>
    )}
  </form.Subscribe>
  ```

### Array Fields

- Handle array fields with `form.Field`:
  ```typescript
  type FormData = {
    users: Array<{ name: string; email: string }>;
  };
  
  const form = useForm<FormData>({
    defaultValues: {
      users: [{ name: '', email: '' }],
    },
    onSubmit: async ({ value }) => {
      await saveUsers(value.users);
    },
  });
  
  <form.Field name="users">
    {(field) => (
      <div>
        {field.state.value.map((_, index) => (
          <div key={index}>
            <form.Field name={`users[${index}].name`}>
              {(subField) => (
                <input
                  value={subField.state.value}
                  onChange={(e) => subField.handleChange(e.target.value)}
                />
              )}
            </form.Field>
            <form.Field name={`users[${index}].email`}>
              {(subField) => (
                <input
                  value={subField.state.value}
                  onChange={(e) => subField.handleChange(e.target.value)}
                />
              )}
            </form.Field>
            <button
              type="button"
              onClick={() => {
                field.handleChange(
                  field.state.value.filter((_, i) => i !== index)
                );
              }}
            >
              Remove
            </button>
          </div>
        ))}
        <button
          type="button"
          onClick={() => {
            field.handleChange([
              ...field.state.value,
              { name: '', email: '' },
            ]);
          }}
        >
          Add User
        </button>
      </div>
    )}
  </form.Field>
  ```

- Validate array fields:
  ```typescript
  <form.Field
    name="users"
    validators={{
      onChange: ({ value }) => {
        if (value.length === 0) {
          return 'At least one user is required';
        }
        return undefined;
      },
    }}
  >
    {(field) => (
      <div>
        {/* Array field UI */}
        {field.state.meta.errors.length > 0 && (
          <span className="error">{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

### Nested Objects

- Handle nested form data structures:
  ```typescript
  type FormData = {
    user: {
      personal: {
        firstName: string;
        lastName: string;
      };
      contact: {
        email: string;
        phone: string;
      };
    };
  };
  
  const form = useForm<FormData>({
    defaultValues: {
      user: {
        personal: {
          firstName: '',
          lastName: '',
        },
        contact: {
          email: '',
          phone: '',
        },
      },
    },
    onSubmit: async ({ value }) => {
      await saveUser(value.user);
    },
  });
  
  <form.Field name="user.personal.firstName">
    {(field) => (
      <input
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
      />
    )}
  </form.Field>
  
  <form.Field name="user.contact.email">
    {(field) => (
      <input
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
      />
    )}
  </form.Field>
  ```

### Async Validation

- Implement async validation with debouncing:
  ```typescript
  <form.Field
    name="username"
    validators={{
      onChangeAsyncDebounceMs: 500,
      onChangeAsync: async ({ value }) => {
        if (!value) return 'Username is required';
        
        const isAvailable = await checkUsernameAvailability(value);
        return isAvailable ? undefined : 'Username is already taken';
      },
    }}
  >
    {(field) => (
      <div>
        <input
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          onBlur={field.handleBlur}
        />
        {field.state.meta.isValidating && <span>Checking...</span>}
        {field.state.meta.errors.length > 0 && (
          <span className="error">{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

- Implement server-side validation on blur:
  ```typescript
  <form.Field
    name="email"
    validators={{
      onBlurAsync: async ({ value }) => {
        const isValid = await validateEmailOnServer(value);
        return isValid ? undefined : 'Email validation failed';
      },
    }}
  >
    {(field) => (
      <input
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
        onBlur={field.handleBlur}
      />
    )}
  </form.Field>
  ```

### File Upload

- Handle file inputs:
  ```typescript
  type FormData = {
    avatar: File | null;
    documents: File[];
  };
  
  const form = useForm<FormData>({
    defaultValues: {
      avatar: null,
      documents: [],
    },
    onSubmit: async ({ value }) => {
      const formData = new FormData();
      if (value.avatar) {
        formData.append('avatar', value.avatar);
      }
      value.documents.forEach((doc) => {
        formData.append('documents', doc);
      });
      await uploadFiles(formData);
    },
  });
  
  <form.Field
    name="avatar"
    validators={{
      onChange: ({ value }) => {
        if (!value) return 'Avatar is required';
        if (value.size > 5000000) {
          return 'File size must be less than 5MB';
        }
        if (!['image/jpeg', 'image/png'].includes(value.type)) {
          return 'Only JPEG and PNG files are allowed';
        }
        return undefined;
      },
    }}
  >
    {(field) => (
      <div>
        <input
          type="file"
          accept="image/*"
          onChange={(e) => {
            const file = e.target.files?.[0] || null;
            field.handleChange(file);
          }}
        />
        {field.state.meta.errors.length > 0 && (
          <span className="error">{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

- Preview uploaded images:
  ```typescript
  <form.Field name="avatar">
    {(field) => {
      const preview = field.state.value
        ? URL.createObjectURL(field.state.value)
        : null;
      
      return (
        <div>
          <input
            type="file"
            accept="image/*"
            onChange={(e) => {
              const file = e.target.files?.[0] || null;
              field.handleChange(file);
            }}
          />
          {preview && <img src={preview} alt="Preview" />}
        </div>
      );
    }}
  </form.Field>
  ```

### Conditional Fields

- Show/hide fields based on other field values:
  ```typescript
  const form = useForm({
    defaultValues: {
      accountType: 'personal',
      companyName: '',
    },
    onSubmit: async ({ value }) => {
      await saveAccount(value);
    },
  });
  
  <form.Field name="accountType">
    {(field) => (
      <select
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
      >
        <option value="personal">Personal</option>
        <option value="business">Business</option>
      </select>
    )}
  </form.Field>
  
  <form.Subscribe
    selector={(state) => state.values.accountType}
  >
    {(accountType) => (
      <>
        {accountType === 'business' && (
          <form.Field
            name="companyName"
            validators={{
              onChange: ({ value }) =>
                !value ? 'Company name is required' : undefined,
            }}
          >
            {(field) => (
              <input
                value={field.state.value}
                onChange={(e) => field.handleChange(e.target.value)}
                placeholder="Company Name"
              />
            )}
          </form.Field>
        )}
      </>
    )}
  </form.Subscribe>
  ```

### Cross-field Validation

- Validate fields based on other field values:
  ```typescript
  type FormData = {
    password: string;
    confirmPassword: string;
  };
  
  const form = useForm<FormData>({
    defaultValues: {
      password: '',
      confirmPassword: '',
    },
    onSubmit: async ({ value }) => {
      await updatePassword(value);
    },
  });
  
  <form.Field name="password">
    {(field) => (
      <input
        type="password"
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
      />
    )}
  </form.Field>
  
  <form.Field
    name="confirmPassword"
    validators={{
      onChangeListenTo: ['password'],
      onChange: ({ value, fieldApi }) => {
        const password = fieldApi.form.getFieldValue('password');
        if (value !== password) {
          return 'Passwords do not match';
        }
        return undefined;
      },
    }}
  >
    {(field) => (
      <div>
        <input
          type="password"
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
        />
        {field.state.meta.errors.length > 0 && (
          <span className="error">{field.state.meta.errors[0]}</span>
        )}
      </div>
    )}
  </form.Field>
  ```

### Integration with UI Libraries

- Material UI TextField:
  ```typescript
  import { TextField } from '@mui/material';
  
  <form.Field name="email">
    {(field) => (
      <TextField
        label="Email"
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
        onBlur={field.handleBlur}
        error={field.state.meta.errors.length > 0}
        helperText={field.state.meta.errors[0]}
        fullWidth
      />
    )}
  </form.Field>
  ```

- Material UI Select:
  ```typescript
  import { FormControl, InputLabel, Select, MenuItem } from '@mui/material';
  
  <form.Field name="role">
    {(field) => (
      <FormControl fullWidth>
        <InputLabel>Role</InputLabel>
        <Select
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          onBlur={field.handleBlur}
          label="Role"
        >
          <MenuItem value="admin">Admin</MenuItem>
          <MenuItem value="user">User</MenuItem>
        </Select>
      </FormControl>
    )}
  </form.Field>
  ```

- Material UI Checkbox:
  ```typescript
  import { FormControlLabel, Checkbox } from '@mui/material';
  
  <form.Field name="acceptTerms">
    {(field) => (
      <FormControlLabel
        control={
          <Checkbox
            checked={field.state.value}
            onChange={(e) => field.handleChange(e.target.checked)}
            onBlur={field.handleBlur}
          />
        }
        label="Accept terms and conditions"
      />
    )}
  </form.Field>
  ```

### Performance Optimization

- Use `form.Subscribe` with selectors to prevent unnecessary re-renders:
  ```typescript
  // Good - Only re-renders when canSubmit changes
  <form.Subscribe
    selector={(state) => state.canSubmit}
  >
    {(canSubmit) => (
      <button type="submit" disabled={!canSubmit}>
        Submit
      </button>
    )}
  </form.Subscribe>
  
  // Avoid - Re-renders on any form state change
  const canSubmit = form.state.canSubmit;
  <button type="submit" disabled={!canSubmit}>Submit</button>
  ```

- Debounce expensive validations:
  ```typescript
  <form.Field
    name="searchQuery"
    validators={{
      onChangeAsyncDebounceMs: 500,
      onChangeAsync: async ({ value }) => {
        const results = await expensiveSearchValidation(value);
        return results.isValid ? undefined : 'Invalid search query';
      },
    }}
  >
    {(field) => (
      <input
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
      />
    )}
  </form.Field>
  ```

- Use React.memo for expensive field components:
  ```typescript
  const EmailField = React.memo(function EmailField() {
    return (
      <form.Field name="email">
        {(field) => (
          <input
            value={field.state.value}
            onChange={(e) => field.handleChange(e.target.value)}
          />
        )}
      </form.Field>
    );
  });
  ```

### Form Submission

- Handle successful submission with feedback:
  ```typescript
  const form = useForm({
    defaultValues: { email: '', password: '' },
    onSubmit: async ({ value }) => {
      try {
        await login(value);
        toast.success('Login successful!');
        navigate('/dashboard');
      } catch (error) {
        toast.error('Login failed. Please try again.');
        throw error;
      }
    },
  });
  ```

- Handle submission with loading state:
  ```typescript
  <form
    onSubmit={(e) => {
      e.preventDefault();
      e.stopPropagation();
      form.handleSubmit();
    }}
  >
    {/* Form fields */}
    
    <form.Subscribe
      selector={(state) => ({
        isSubmitting: state.isSubmitting,
        canSubmit: state.canSubmit,
      })}
    >
      {({ isSubmitting, canSubmit }) => (
        <button type="submit" disabled={!canSubmit || isSubmitting}>
          {isSubmitting ? 'Submitting...' : 'Submit'}
        </button>
      )}
    </form.Subscribe>
  </form>
  ```

- Prevent default form submission:
  ```typescript
  <form
    onSubmit={(e) => {
      e.preventDefault();
      e.stopPropagation();
      form.handleSubmit();
    }}
  >
    {/* Form content */}
  </form>
  ```

### Testing

- Test form validation:
  ```typescript
  import { render, screen, waitFor } from '@testing-library/react';
  import userEvent from '@testing-library/user-event';
  
  test('validates email format', async () => {
    const user = userEvent.setup();
    render(<LoginForm />);
    
    const emailInput = screen.getByLabelText(/email/i);
    await user.type(emailInput, 'invalid-email');
    await user.tab();
    
    expect(await screen.findByText(/invalid email/i)).toBeInTheDocument();
  });
  ```

- Test form submission:
  ```typescript
  test('submits form with valid data', async () => {
    const user = userEvent.setup();
    const mockOnSubmit = vi.fn();
    render(<ContactForm onSubmit={mockOnSubmit} />);
    
    await user.type(screen.getByLabelText(/name/i), 'John Doe');
    await user.type(screen.getByLabelText(/email/i), 'john@example.com');
    await user.click(screen.getByRole('button', { name: /submit/i }));
    
    await waitFor(() => {
      expect(mockOnSubmit).toHaveBeenCalledWith({
        name: 'John Doe',
        email: 'john@example.com',
      });
    });
  });
  ```

- Test async validation:
  ```typescript
  test('validates username availability', async () => {
    const user = userEvent.setup();
    const mockCheckUsername = vi.fn().mockResolvedValue(false);
    render(<SignupForm checkUsername={mockCheckUsername} />);
    
    await user.type(screen.getByLabelText(/username/i), 'existing-user');
    
    await waitFor(() => {
      expect(screen.getByText(/username is already taken/i)).toBeInTheDocument();
    });
    
    expect(mockCheckUsername).toHaveBeenCalledWith('existing-user');
  });
  ```

### Accessibility

- Always associate labels with form inputs:
  ```typescript
  <form.Field name="email">
    {(field) => (
      <div>
        <label htmlFor={field.name}>Email Address:</label>
        <input
          id={field.name}
          name={field.name}
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          onBlur={field.handleBlur}
          aria-invalid={field.state.meta.errors.length > 0}
          aria-describedby={
            field.state.meta.errors.length > 0
              ? `${field.name}-error`
              : undefined
          }
        />
        {field.state.meta.errors.length > 0 && (
          <span id={`${field.name}-error`} role="alert" className="error">
            {field.state.meta.errors[0]}
          </span>
        )}
      </div>
    )}
  </form.Field>
  ```

- Mark required fields:
  ```typescript
  <form.Field
    name="email"
    validators={{
      onChange: ({ value }) => (!value ? 'Email is required' : undefined),
    }}
  >
    {(field) => (
      <div>
        <label htmlFor={field.name}>
          Email Address
          <abbr title="required" aria-label="required">*</abbr>
        </label>
        <input
          id={field.name}
          value={field.state.value}
          onChange={(e) => field.handleChange(e.target.value)}
          required
          aria-required="true"
        />
      </div>
    )}
  </form.Field>
  ```

- Use ARIA attributes for validation states:
  ```typescript
  <form.Field name="password">
    {(field) => (
      <input
        value={field.state.value}
        onChange={(e) => field.handleChange(e.target.value)}
        aria-invalid={field.state.meta.errors.length > 0}
        aria-describedby={
          field.state.meta.errors.length > 0 ? 'password-error' : undefined
        }
      />
    )}
  </form.Field>
  ```

### Code Organization

- Extract reusable field components:
  ```typescript
  function FormTextField({
    name,
    label,
    type = 'text',
    validators,
  }: {
    name: string;
    label: string;
    type?: string;
    validators?: any;
  }) {
    return (
      <form.Field name={name} validators={validators}>
        {(field) => (
          <div>
            <label htmlFor={field.name}>{label}:</label>
            <input
              id={field.name}
              type={type}
              value={field.state.value}
              onChange={(e) => field.handleChange(e.target.value)}
              onBlur={field.handleBlur}
              aria-invalid={field.state.meta.errors.length > 0}
            />
            {field.state.meta.errors.length > 0 && (
              <span role="alert" className="error">
                {field.state.meta.errors[0]}
              </span>
            )}
          </div>
        )}
      </form.Field>
    );
  }
  
  // Usage
  function LoginForm() {
    const form = useForm({ /* ... */ });
    
    return (
      <form onSubmit={(e) => { e.preventDefault(); form.handleSubmit(); }}>
        <FormTextField
          name="email"
          label="Email"
          validators={{
            onChange: validateEmail,
          }}
        />
        <FormTextField
          name="password"
          label="Password"
          type="password"
          validators={{
            onChange: validatePassword,
          }}
        />
      </form>
    );
  }
  ```

- Group validation logic in separate files:
  ```typescript
  // validators/userValidators.ts
  export const validateEmail = ({ value }: { value: string }) => {
    if (!value) return 'Email is required';
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
      return 'Invalid email format';
    }
    return undefined;
  };
  
  export const validatePassword = ({ value }: { value: string }) => {
    if (!value) return 'Password is required';
    if (value.length < 8) return 'Password must be at least 8 characters';
    return undefined;
  };
  ```

### Best Practices

- Always use TypeScript for form definitions and field typing.
- Leverage validation adapters (Zod, Yup, Valibot) for complex schemas.
- Use `form.Subscribe` with selectors to optimize re-renders.
- Debounce async validations to reduce API calls.
- Provide clear, user-friendly error messages.
- Handle loading states during submission and async validation.
- Use proper ARIA attributes for accessibility.
- Associate labels with inputs using htmlFor and id.
- Extract reusable field components for consistency.
- Keep validation logic separate and testable.
- Use cross-field validation with `onChangeListenTo` when needed.
- Reset forms after successful submission when appropriate.
- Test form validation, submission, and error handling.
- Prevent default form submission behavior.
- Handle file uploads with proper validation.
- Use array fields for dynamic form structures.
- Implement conditional fields based on form state.
- Provide visual feedback for form state (submitting, validating).
- Use the field name as the input id for accessibility.
- Display errors only after field is touched or form is submitted.
