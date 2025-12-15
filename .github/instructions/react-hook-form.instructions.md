---
title: React Hook Form Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# React Hook Form Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate form handling 
code using React Hook Form following best practices.

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
`formErrors`, `isDirty`).

### Form Setup

- Always use the `useForm` hook to initialize forms:
  ```typescript
  import { useForm } from 'react-hook-form';
  
  type FormData = {
    email: string;
    password: string;
  };
  
  function LoginForm() {
    const {
      register,
      handleSubmit,
      formState: { errors, isSubmitting },
    } = useForm<FormData>();
    
    const onSubmit = async (data: FormData) => {
      // Handle form submission
    };
    
    return (
      <form onSubmit={handleSubmit(onSubmit)}>
        {/* Form fields */}
      </form>
    );
  }
  ```

- Provide default values when needed:
  ```typescript
  const { register, handleSubmit } = useForm<FormData>({
    defaultValues: {
      email: '',
      password: '',
      rememberMe: false,
    },
  });
  ```

- Use mode to control validation timing:
  ```typescript
  const { register, handleSubmit } = useForm<FormData>({
    mode: 'onBlur', // 'onChange' | 'onSubmit' | 'onTouched' | 'all'
  });
  ```

### Field Registration

- Always use the `register` function to register form fields:
  ```typescript
  <input
    {...register('email', {
      required: 'Email is required',
      pattern: {
        value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
        message: 'Invalid email address',
      },
    })}
    type="email"
    placeholder="Email"
  />
  ```

- Provide validation rules inline:
  ```typescript
  <input
    {...register('password', {
      required: 'Password is required',
      minLength: {
        value: 8,
        message: 'Password must be at least 8 characters',
      },
      validate: {
        hasUpperCase: (value) =>
          /[A-Z]/.test(value) || 'Must contain uppercase letter',
        hasLowerCase: (value) =>
          /[a-z]/.test(value) || 'Must contain lowercase letter',
        hasNumber: (value) =>
          /[0-9]/.test(value) || 'Must contain a number',
      },
    })}
    type="password"
  />
  ```

### Validation with Zod

- Use Zod for schema-based validation with `@hookform/resolvers`:
  ```typescript
  import { zodResolver } from '@hookform/resolvers/zod';
  import { z } from 'zod';
  
  const loginSchema = z.object({
    email: z.string().email('Invalid email address'),
    password: z.string().min(8, 'Password must be at least 8 characters'),
  });
  
  type LoginFormData = z.infer<typeof loginSchema>;
  
  function LoginForm() {
    const {
      register,
      handleSubmit,
      formState: { errors },
    } = useForm<LoginFormData>({
      resolver: zodResolver(loginSchema),
    });
    
    return (
      <form onSubmit={handleSubmit(onSubmit)}>
        <input {...register('email')} />
        {errors.email && <span>{errors.email.message}</span>}
        
        <input {...register('password')} type="password" />
        {errors.password && <span>{errors.password.message}</span>}
      </form>
    );
  }
  ```

- Create reusable schema refinements:
  ```typescript
  const passwordSchema = z
    .string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/[A-Z]/, 'Must contain uppercase letter')
    .regex(/[a-z]/, 'Must contain lowercase letter')
    .regex(/[0-9]/, 'Must contain a number');
  
  const signupSchema = z.object({
    email: z.string().email('Invalid email'),
    password: passwordSchema,
    confirmPassword: z.string(),
  }).refine((data) => data.password === data.confirmPassword, {
    message: "Passwords don't match",
    path: ['confirmPassword'],
  });
  ```

### Error Handling

- Display field-level errors:
  ```typescript
  <div>
    <input
      {...register('email')}
      aria-invalid={errors.email ? 'true' : 'false'}
    />
    {errors.email && (
      <span role="alert" className="error">
        {errors.email.message}
      </span>
    )}
  </div>
  ```

- Use `ErrorMessage` component for cleaner error display:
  ```typescript
  import { ErrorMessage } from '@hookform/error-message';
  
  <ErrorMessage
    errors={errors}
    name="email"
    render={({ message }) => <p className="error">{message}</p>}
  />
  ```

- Handle form-level errors:
  ```typescript
  const onSubmit = async (data: FormData) => {
    try {
      await submitForm(data);
    } catch (error) {
      setError('root', {
        type: 'manual',
        message: 'Failed to submit form',
      });
    }
  };
  
  {errors.root && <div className="error">{errors.root.message}</div>}
  ```

### Controlled Components

- Use `Controller` for controlled components (Material UI, custom inputs):
  ```typescript
  import { Controller } from 'react-hook-form';
  import { TextField } from '@mui/material';
  
  <Controller
    name="email"
    control={control}
    defaultValue=""
    rules={{ required: 'Email is required' }}
    render={({ field, fieldState: { error } }) => (
      <TextField
        {...field}
        label="Email"
        error={!!error}
        helperText={error?.message}
        fullWidth
      />
    )}
  />
  ```

- Use `Controller` with select components:
  ```typescript
  <Controller
    name="country"
    control={control}
    render={({ field }) => (
      <Select {...field}>
        <MenuItem value="us">United States</MenuItem>
        <MenuItem value="uk">United Kingdom</MenuItem>
      </Select>
    )}
  />
  ```

- Use `Controller` with date pickers:
  ```typescript
  <Controller
    name="birthDate"
    control={control}
    render={({ field }) => (
      <DatePicker
        {...field}
        onChange={(date) => field.onChange(date)}
        value={field.value}
      />
    )}
  />
  ```

### Form State Management

- Access form state through `formState`:
  ```typescript
  const {
    formState: {
      errors,
      isDirty,
      isValid,
      isSubmitting,
      isSubmitted,
      isSubmitSuccessful,
      touchedFields,
      dirtyFields,
    },
  } = useForm<FormData>();
  
  <button
    type="submit"
    disabled={!isDirty || isSubmitting}
  >
    {isSubmitting ? 'Submitting...' : 'Submit'}
  </button>
  ```

- Reset form after successful submission:
  ```typescript
  const onSubmit = async (data: FormData) => {
    await submitForm(data);
    reset(); // Reset to default values
  };
  ```

- Reset with new values:
  ```typescript
  reset({
    email: 'new@email.com',
    password: '',
  });
  ```

### Watch Fields

- Watch specific fields:
  ```typescript
  const watchEmail = watch('email');
  const watchPassword = watch('password');
  
  // Use watched values in the UI
  <p>Email: {watchEmail}</p>
  ```

- Watch all fields:
  ```typescript
  const watchAllFields = watch();
  
  useEffect(() => {
    console.log('Form data changed:', watchAllFields);
  }, [watchAllFields]);
  ```

- Watch with callback for performance:
  ```typescript
  useEffect(() => {
    const subscription = watch((value, { name, type }) => {
      console.log(`${name} changed:`, value);
    });
    return () => subscription.unsubscribe();
  }, [watch]);
  ```

### Set and Clear Values

- Set field values programmatically:
  ```typescript
  setValue('email', 'user@example.com', {
    shouldValidate: true,
    shouldDirty: true,
    shouldTouch: true,
  });
  ```

- Clear specific field errors:
  ```typescript
  clearErrors('email');
  ```

- Clear all errors:
  ```typescript
  clearErrors();
  ```

- Trigger validation manually:
  ```typescript
  const isValid = await trigger(); // Validate all fields
  const isEmailValid = await trigger('email'); // Validate specific field
  ```

### Field Arrays

- Use `useFieldArray` for dynamic form fields:
  ```typescript
  import { useFieldArray } from 'react-hook-form';
  
  type FormData = {
    users: { name: string; email: string }[];
  };
  
  function UserForm() {
    const { register, control, handleSubmit } = useForm<FormData>({
      defaultValues: {
        users: [{ name: '', email: '' }],
      },
    });
    
    const { fields, append, remove } = useFieldArray({
      control,
      name: 'users',
    });
    
    return (
      <form onSubmit={handleSubmit(onSubmit)}>
        {fields.map((field, index) => (
          <div key={field.id}>
            <input {...register(`users.${index}.name`)} />
            <input {...register(`users.${index}.email`)} />
            <button type="button" onClick={() => remove(index)}>
              Remove
            </button>
          </div>
        ))}
        
        <button
          type="button"
          onClick={() => append({ name: '', email: '' })}
        >
          Add User
        </button>
      </form>
    );
  }
  ```

- Validate field array items:
  ```typescript
  const schema = z.object({
    users: z.array(
      z.object({
        name: z.string().min(1, 'Name is required'),
        email: z.string().email('Invalid email'),
      })
    ).min(1, 'At least one user is required'),
  });
  ```

### Nested Objects

- Handle nested form data:
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
  
  <input {...register('user.personal.firstName')} />
  <input {...register('user.personal.lastName')} />
  <input {...register('user.contact.email')} />
  <input {...register('user.contact.phone')} />
  ```

### Conditional Fields

- Show/hide fields based on other field values:
  ```typescript
  const accountType = watch('accountType');
  
  <select {...register('accountType')}>
    <option value="personal">Personal</option>
    <option value="business">Business</option>
  </select>
  
  {accountType === 'business' && (
    <input
      {...register('companyName', {
        required: 'Company name is required',
      })}
      placeholder="Company Name"
    />
  )}
  ```

### Async Validation

- Implement async validation for unique checks:
  ```typescript
  <input
    {...register('username', {
      required: 'Username is required',
      validate: async (value) => {
        const isAvailable = await checkUsernameAvailability(value);
        return isAvailable || 'Username is already taken';
      },
    })}
  />
  ```

- Debounce async validation:
  ```typescript
  const debouncedValidate = useMemo(
    () =>
      debounce(async (value: string) => {
        const isAvailable = await checkUsernameAvailability(value);
        return isAvailable || 'Username is already taken';
      }, 500),
    []
  );
  
  <input
    {...register('username', {
      validate: debouncedValidate,
    })}
  />
  ```

### Form Submission

- Handle successful submission:
  ```typescript
  const onSubmit = async (data: FormData) => {
    try {
      await submitForm(data);
      toast.success('Form submitted successfully');
      navigate('/success');
    } catch (error) {
      setError('root', {
        type: 'manual',
        message: 'Submission failed',
      });
    }
  };
  ```

- Handle submission with loading state:
  ```typescript
  const {
    handleSubmit,
    formState: { isSubmitting },
  } = useForm<FormData>();
  
  const onSubmit = async (data: FormData) => {
    await new Promise((resolve) => setTimeout(resolve, 2000));
    console.log(data);
  };
  
  <button type="submit" disabled={isSubmitting}>
    {isSubmitting ? 'Submitting...' : 'Submit'}
  </button>
  ```

### Integration with UI Libraries

- Material UI TextField:
  ```typescript
  <Controller
    name="email"
    control={control}
    rules={{ required: 'Email is required' }}
    render={({ field, fieldState: { error } }) => (
      <TextField
        {...field}
        label="Email"
        error={!!error}
        helperText={error?.message}
        fullWidth
      />
    )}
  />
  ```

- Material UI Select:
  ```typescript
  <Controller
    name="role"
    control={control}
    render={({ field }) => (
      <FormControl fullWidth>
        <InputLabel>Role</InputLabel>
        <Select {...field} label="Role">
          <MenuItem value="admin">Admin</MenuItem>
          <MenuItem value="user">User</MenuItem>
        </Select>
      </FormControl>
    )}
  />
  ```

- Material UI Checkbox:
  ```typescript
  <Controller
    name="acceptTerms"
    control={control}
    render={({ field }) => (
      <FormControlLabel
        control={<Checkbox {...field} checked={field.value} />}
        label="Accept terms and conditions"
      />
    )}
  />
  ```

### File Upload

- Handle file inputs:
  ```typescript
  type FormData = {
    avatar: FileList;
  };
  
  const { register, handleSubmit, watch } = useForm<FormData>();
  
  const avatarFile = watch('avatar');
  const avatarPreview = avatarFile?.[0]
    ? URL.createObjectURL(avatarFile[0])
    : null;
  
  <input
    {...register('avatar', {
      required: 'Avatar is required',
      validate: {
        fileSize: (files) =>
          files[0]?.size < 5000000 || 'File size must be less than 5MB',
        fileType: (files) =>
          ['image/jpeg', 'image/png'].includes(files[0]?.type) ||
          'Only JPEG and PNG files are allowed',
      },
    })}
    type="file"
    accept="image/*"
  />
  
  {avatarPreview && <img src={avatarPreview} alt="Avatar preview" />}
  ```

### TypeScript Best Practices

- Always define form data types:
  ```typescript
  type LoginFormData = {
    email: string;
    password: string;
    rememberMe: boolean;
  };
  
  const { register, handleSubmit } = useForm<LoginFormData>();
  ```

- Infer types from Zod schemas:
  ```typescript
  const userSchema = z.object({
    name: z.string(),
    age: z.number(),
  });
  
  type UserFormData = z.infer<typeof userSchema>;
  ```

- Type custom validation functions:
  ```typescript
  const validateEmail = (value: string): boolean | string => {
    return /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(value) ||
      'Invalid email';
  };
  ```

### Testing

- Test form validation:
  ```typescript
  import { render, screen } from '@testing-library/react';
  import userEvent from '@testing-library/user-event';
  
  test('validates required fields', async () => {
    const user = userEvent.setup();
    render(<LoginForm />);
    
    await user.click(screen.getByRole('button', { name: /submit/i }));
    
    expect(await screen.findByText(/email is required/i)).toBeInTheDocument();
  });
  ```

- Test form submission:
  ```typescript
  test('submits form with valid data', async () => {
    const user = userEvent.setup();
    const mockOnSubmit = vi.fn();
    render(<LoginForm onSubmit={mockOnSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /submit/i }));
    
    await waitFor(() => {
      expect(mockOnSubmit).toHaveBeenCalledWith({
        email: 'test@example.com',
        password: 'password123',
      });
    });
  });
  ```

### Performance Optimization

- Use `useFormContext` to avoid prop drilling:
  ```typescript
  import { FormProvider, useFormContext } from 'react-hook-form';
  
  function ParentForm() {
    const methods = useForm<FormData>();
    
    return (
      <FormProvider {...methods}>
        <form onSubmit={methods.handleSubmit(onSubmit)}>
          <ChildComponent />
        </form>
      </FormProvider>
    );
  }
  
  function ChildComponent() {
    const { register } = useFormContext<FormData>();
    return <input {...register('email')} />;
  }
  ```

- Avoid unnecessary re-renders with `mode: 'onBlur'`:
  ```typescript
  const methods = useForm<FormData>({
    mode: 'onBlur', // Only validate on blur, not on every keystroke
  });
  ```

### Best Practices

- Always provide TypeScript types for form data.
- Use Zod for complex validation schemas.
- Use `Controller` for controlled components from UI libraries.
- Display clear, user-friendly error messages.
- Handle loading and submission states appropriately.
- Reset forms after successful submission when appropriate.
- Use `useFieldArray` for dynamic form fields.
- Validate asynchronously for unique checks (username, email).
- Debounce expensive validations to avoid performance issues.
- Use `mode` to control when validation occurs.
- Provide accessible error messages with ARIA attributes.
- Test form validation and submission thoroughly.
- Avoid prop drilling by using `useFormContext` for nested forms.
- Keep validation logic close to the form definition.
- Use semantic HTML for better accessibility.
