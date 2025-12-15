---
title: Axios Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# Axios Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate API client 
code using Axios following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Naming Conventions

- Name API client files with "api" or "client" suffix (e.g., `userApi.ts`, 
`apiClient.ts`).
- Name axios instances with "client" or "api" (e.g., `apiClient`, `authClient`).
- Prefix request functions with HTTP method or action verb (e.g., `getUsers`, 
`createPost`, `deleteComment`).
- Name interceptor functions descriptively (e.g., `authInterceptor`, 
`errorInterceptor`, `loggingInterceptor`).
- Name error classes with "Error" suffix (e.g., `ApiError`, `NetworkError`).

### Axios Instance Creation

- Always create axios instances with baseURL configuration:
  ```typescript
  import axios from 'axios';
  
  export const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_URL || 'http://localhost:3000/api',
    timeout: 10000,
    headers: {
      'Content-Type': 'application/json',
    },
  });
  ```

- Create separate instances for different APIs:
  ```typescript
  // Main API
  export const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_URL,
  });
  
  // External service
  export const externalClient = axios.create({
    baseURL: 'https://external-service.com/api',
  });
  
  // Authentication service
  export const authClient = axios.create({
    baseURL: import.meta.env.VITE_AUTH_URL,
  });
  ```

### TypeScript Typing

- Always define TypeScript types for request and response data:
  ```typescript
  type User = {
    id: string;
    name: string;
    email: string;
  };
  
  type CreateUserRequest = {
    name: string;
    email: string;
    password: string;
  };
  
  type ApiResponse<T> = {
    data: T;
    message: string;
    status: number;
  };
  
  export async function getUser(id: string): Promise<User> {
    const response = await apiClient.get<ApiResponse<User>>(`/users/${id}`);
    return response.data.data;
  }
  
  export async function createUser(data: CreateUserRequest): Promise<User> {
    const response = await apiClient.post<ApiResponse<User>>('/users', data);
    return response.data.data;
  }
  ```

- Define error types:
  ```typescript
  type ApiErrorResponse = {
    message: string;
    errors?: Record<string, string[]>;
    code?: string;
  };
  
  class ApiError extends Error {
    constructor(
      message: string,
      public status: number,
      public code?: string,
      public errors?: Record<string, string[]>
    ) {
      super(message);
      this.name = 'ApiError';
    }
  }
  ```

### Request Interceptors

- Add authentication token to requests:
  ```typescript
  apiClient.interceptors.request.use(
    (config) => {
      const token = localStorage.getItem('token');
      if (token) {
        config.headers.Authorization = `Bearer ${token}`;
      }
      return config;
    },
    (error) => {
      return Promise.reject(error);
    }
  );
  ```

- Add request logging for debugging:
  ```typescript
  apiClient.interceptors.request.use(
    (config) => {
      console.log(`[API Request] ${config.method?.toUpperCase()} ${config.url}`, {
        params: config.params,
        data: config.data,
      });
      return config;
    },
    (error) => {
      console.error('[API Request Error]', error);
      return Promise.reject(error);
    }
  );
  ```

- Add custom headers conditionally:
  ```typescript
  apiClient.interceptors.request.use((config) => {
    // Add timezone
    config.headers['X-Timezone'] = Intl.DateTimeFormat().resolvedOptions().timeZone;
    
    // Add locale
    config.headers['Accept-Language'] = navigator.language;
    
    // Add custom tracking ID
    config.headers['X-Request-ID'] = generateRequestId();
    
    return config;
  });
  ```

### Response Interceptors

- Handle successful responses uniformly:
  ```typescript
  apiClient.interceptors.response.use(
    (response) => {
      console.log(`[API Response] ${response.config.url}`, response.data);
      return response;
    },
    (error) => {
      return Promise.reject(error);
    }
  );
  ```

- Transform response data:
  ```typescript
  apiClient.interceptors.response.use((response) => {
    // Extract nested data
    if (response.data?.data) {
      return { ...response, data: response.data.data };
    }
    return response;
  });
  ```

### Error Handling

- Create a centralized error interceptor:
  ```typescript
  apiClient.interceptors.response.use(
    (response) => response,
    (error) => {
      if (axios.isAxiosError(error)) {
        const status = error.response?.status;
        const data = error.response?.data as ApiErrorResponse;
        
        switch (status) {
          case 401:
            // Handle unauthorized
            handleUnauthorized();
            break;
          case 403:
            // Handle forbidden
            toast.error('You do not have permission to perform this action');
            break;
          case 404:
            // Handle not found
            toast.error('Resource not found');
            break;
          case 422:
            // Handle validation errors
            handleValidationErrors(data.errors);
            break;
          case 500:
            // Handle server errors
            toast.error('Server error. Please try again later.');
            break;
          default:
            toast.error(data?.message || 'An error occurred');
        }
        
        throw new ApiError(
          data?.message || error.message,
          status || 0,
          data?.code,
          data?.errors
        );
      }
      
      // Network error or request setup error
      if (error.request && !error.response) {
        toast.error('Network error. Please check your connection.');
      }
      
      return Promise.reject(error);
    }
  );
  ```

- Handle token refresh:
  ```typescript
  let isRefreshing = false;
  let failedQueue: Array<{
    resolve: (token: string) => void;
    reject: (error: unknown) => void;
  }> = [];
  
  const processQueue = (error: unknown = null, token: string | null = null) => {
    failedQueue.forEach((prom) => {
      if (error) {
        prom.reject(error);
      } else if (token) {
        prom.resolve(token);
      }
    });
    failedQueue = [];
  };
  
  apiClient.interceptors.response.use(
    (response) => response,
    async (error) => {
      const originalRequest = error.config;
      
      if (error.response?.status === 401 && !originalRequest._retry) {
        if (isRefreshing) {
          return new Promise((resolve, reject) => {
            failedQueue.push({ resolve, reject });
          })
            .then((token) => {
              originalRequest.headers.Authorization = `Bearer ${token}`;
              return apiClient(originalRequest);
            })
            .catch((err) => Promise.reject(err));
        }
        
        originalRequest._retry = true;
        isRefreshing = true;
        
        try {
          const refreshToken = localStorage.getItem('refreshToken');
          const response = await authClient.post('/auth/refresh', {
            refreshToken,
          });
          const { token } = response.data;
          
          localStorage.setItem('token', token);
          apiClient.defaults.headers.common.Authorization = `Bearer ${token}`;
          originalRequest.headers.Authorization = `Bearer ${token}`;
          
          processQueue(null, token);
          return apiClient(originalRequest);
        } catch (refreshError) {
          processQueue(refreshError, null);
          // Redirect to login
          window.location.href = '/login';
          return Promise.reject(refreshError);
        } finally {
          isRefreshing = false;
        }
      }
      
      return Promise.reject(error);
    }
  );
  ```

### Request Cancellation

- Cancel requests with AbortController:
  ```typescript
  import { useEffect, useRef } from 'react';
  
  function useAbortController() {
    const abortControllerRef = useRef<AbortController>();
    
    useEffect(() => {
      abortControllerRef.current = new AbortController();
      
      return () => {
        abortControllerRef.current?.abort();
      };
    }, []);
    
    return abortControllerRef.current;
  }
  
  // Usage in component
  function UserList() {
    const abortController = useAbortController();
    
    useEffect(() => {
      fetchUsers({ signal: abortController?.signal });
    }, [abortController]);
  }
  
  // In API function
  export async function fetchUsers(config?: AxiosRequestConfig): Promise<User[]> {
    const response = await apiClient.get<User[]>('/users', config);
    return response.data;
  }
  ```

- Cancel requests using Axios CancelToken (legacy approach):
  ```typescript
  const source = axios.CancelToken.source();
  
  export async function fetchUsers(): Promise<User[]> {
    const response = await apiClient.get<User[]>('/users', {
      cancelToken: source.token,
    });
    return response.data;
  }
  
  // Cancel the request
  source.cancel('Request canceled by user');
  ```

### Retry Logic

- Implement retry logic for failed requests:
  ```typescript
  import axiosRetry from 'axios-retry';
  
  axiosRetry(apiClient, {
    retries: 3,
    retryDelay: axiosRetry.exponentialDelay,
    retryCondition: (error) => {
      // Retry on network errors or 5xx errors
      return (
        axiosRetry.isNetworkOrIdempotentRequestError(error) ||
        (error.response?.status ?? 0) >= 500
      );
    },
    onRetry: (retryCount, error, requestConfig) => {
      console.log(`Retrying request ${requestConfig.url} (attempt ${retryCount})`);
    },
  });
  ```

- Manual retry implementation:
  ```typescript
  async function fetchWithRetry<T>(
    fn: () => Promise<T>,
    retries = 3,
    delay = 1000
  ): Promise<T> {
    try {
      return await fn();
    } catch (error) {
      if (retries > 0) {
        await new Promise((resolve) => setTimeout(resolve, delay));
        return fetchWithRetry(fn, retries - 1, delay * 2);
      }
      throw error;
    }
  }
  
  // Usage
  const users = await fetchWithRetry(() => getUsers(), 3, 1000);
  ```

### File Upload

- Upload files with progress tracking:
  ```typescript
  type UploadProgress = {
    loaded: number;
    total: number;
    percentage: number;
  };
  
  export async function uploadFile(
    file: File,
    onProgress?: (progress: UploadProgress) => void
  ): Promise<{ url: string }> {
    const formData = new FormData();
    formData.append('file', file);
    
    const response = await apiClient.post<{ url: string }>(
      '/upload',
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
        onUploadProgress: (progressEvent) => {
          if (onProgress && progressEvent.total) {
            const percentage = Math.round(
              (progressEvent.loaded * 100) / progressEvent.total
            );
            onProgress({
              loaded: progressEvent.loaded,
              total: progressEvent.total,
              percentage,
            });
          }
        },
      }
    );
    
    return response.data;
  }
  ```

- Upload multiple files:
  ```typescript
  export async function uploadMultipleFiles(
    files: File[]
  ): Promise<{ urls: string[] }> {
    const formData = new FormData();
    files.forEach((file) => {
      formData.append('files', file);
    });
    
    const response = await apiClient.post<{ urls: string[] }>(
      '/upload/multiple',
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      }
    );
    
    return response.data;
  }
  ```

### Download Files

- Download files with proper headers:
  ```typescript
  export async function downloadFile(fileId: string): Promise<void> {
    const response = await apiClient.get(`/files/${fileId}/download`, {
      responseType: 'blob',
    });
    
    // Create download link
    const url = window.URL.createObjectURL(new Blob([response.data]));
    const link = document.createElement('a');
    link.href = url;
    
    // Extract filename from Content-Disposition header
    const contentDisposition = response.headers['content-disposition'];
    const filename = contentDisposition
      ? contentDisposition.split('filename=')[1].replace(/"/g, '')
      : 'download';
    
    link.setAttribute('download', filename);
    document.body.appendChild(link);
    link.click();
    link.remove();
    window.URL.revokeObjectURL(url);
  }
  ```

### Pagination

- Handle paginated responses:
  ```typescript
  type PaginatedResponse<T> = {
    data: T[];
    page: number;
    limit: number;
    total: number;
    hasMore: boolean;
  };
  
  export async function getUsers(
    page = 1,
    limit = 10
  ): Promise<PaginatedResponse<User>> {
    const response = await apiClient.get<PaginatedResponse<User>>('/users', {
      params: { page, limit },
    });
    return response.data;
  }
  ```

- Infinite scroll / load more:
  ```typescript
  export async function getUsersInfinite(
    cursor?: string,
    limit = 20
  ): Promise<{ users: User[]; nextCursor?: string }> {
    const response = await apiClient.get<{
      users: User[];
      nextCursor?: string;
    }>('/users/infinite', {
      params: { cursor, limit },
    });
    return response.data;
  }
  ```

### Query Parameters

- Handle complex query parameters:
  ```typescript
  type UserFilters = {
    search?: string;
    role?: string[];
    isActive?: boolean;
    createdAfter?: Date;
  };
  
  export async function searchUsers(filters: UserFilters): Promise<User[]> {
    const params = new URLSearchParams();
    
    if (filters.search) {
      params.append('search', filters.search);
    }
    
    if (filters.role) {
      filters.role.forEach((role) => params.append('role[]', role));
    }
    
    if (filters.isActive !== undefined) {
      params.append('isActive', String(filters.isActive));
    }
    
    if (filters.createdAfter) {
      params.append('createdAfter', filters.createdAfter.toISOString());
    }
    
    const response = await apiClient.get<User[]>('/users/search', { params });
    return response.data;
  }
  ```

- Use URLSearchParams for cleaner parameter handling:
  ```typescript
  export async function getUsers(filters: Record<string, unknown>): Promise<User[]> {
    const response = await apiClient.get<User[]>('/users', {
      params: filters,
      paramsSerializer: (params) => {
        return new URLSearchParams(params).toString();
      },
    });
    return response.data;
  }
  ```

### Integration with TanStack Query

- Create API functions for React Query:
  ```typescript
  import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
  
  // Query functions
  export const userKeys = {
    all: ['users'] as const,
    lists: () => [...userKeys.all, 'list'] as const,
    list: (filters: string) => [...userKeys.lists(), { filters }] as const,
    details: () => [...userKeys.all, 'detail'] as const,
    detail: (id: string) => [...userKeys.details(), id] as const,
  };
  
  // Hooks
  export function useUser(id: string) {
    return useQuery({
      queryKey: userKeys.detail(id),
      queryFn: () => getUser(id),
    });
  }
  
  export function useCreateUser() {
    const queryClient = useQueryClient();
    
    return useMutation({
      mutationFn: createUser,
      onSuccess: () => {
        queryClient.invalidateQueries({ queryKey: userKeys.lists() });
      },
    });
  }
  ```

### Environment Configuration

- Configure baseURL based on environment:
  ```typescript
  const getBaseURL = () => {
    if (import.meta.env.MODE === 'production') {
      return 'https://api.production.com';
    }
    if (import.meta.env.MODE === 'staging') {
      return 'https://api.staging.com';
    }
    return 'http://localhost:3000/api';
  };
  
  export const apiClient = axios.create({
    baseURL: getBaseURL(),
  });
  ```

- Use environment variables:
  ```typescript
  export const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_URL,
    timeout: Number(import.meta.env.VITE_API_TIMEOUT) || 10000,
  });
  ```

### Testing

- Mock axios in tests:
  ```typescript
  import { vi } from 'vitest';
  import axios from 'axios';
  
  vi.mock('axios');
  const mockedAxios = axios as jest.Mocked<typeof axios>;
  
  test('fetches users', async () => {
    const users = [{ id: '1', name: 'John' }];
    mockedAxios.get.mockResolvedValue({ data: users });
    
    const result = await getUsers();
    
    expect(result).toEqual(users);
    expect(mockedAxios.get).toHaveBeenCalledWith('/users');
  });
  ```

- Use axios-mock-adapter:
  ```typescript
  import MockAdapter from 'axios-mock-adapter';
  
  const mock = new MockAdapter(apiClient);
  
  beforeEach(() => {
    mock.reset();
  });
  
  test('handles error response', async () => {
    mock.onGet('/users').reply(500, { message: 'Server error' });
    
    await expect(getUsers()).rejects.toThrow('Server error');
  });
  
  test('returns users', async () => {
    const users = [{ id: '1', name: 'John' }];
    mock.onGet('/users').reply(200, users);
    
    const result = await getUsers();
    expect(result).toEqual(users);
  });
  ```

### Best Practices

- Always create axios instances instead of using the default instance.
- Define TypeScript types for all request and response data.
- Use interceptors for cross-cutting concerns (auth, logging, errors).
- Implement centralized error handling.
- Handle token refresh in interceptors.
- Use request cancellation for component unmounting.
- Implement retry logic for transient failures.
- Set appropriate timeout values.
- Use environment variables for API URLs and configuration.
- Handle file uploads with progress tracking.
- Provide proper Content-Type headers.
- Use URLSearchParams for complex query parameters.
- Test API functions with mocked responses.
- Integrate with React Query for better data fetching.
- Log requests and responses in development.
- Handle network errors gracefully.
- Implement proper TypeScript error types.
- Use AbortController for request cancellation.
- Validate response data before returning.
- Document API functions with JSDoc comments.
