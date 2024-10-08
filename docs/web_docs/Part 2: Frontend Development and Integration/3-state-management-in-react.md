---
sidebar_position: 3
slug: /activities/part-2-frontend-development-and-integration/3-state-management-in-react
---

# State Management in React

In this section, we'll dive deep into state management for our PhotoSky application using React hooks. We'll cover how to manage local component state, share state between components, handle side effects, implement error handling, and manage loading states in our application.

## Understanding State in React

State in React represents the data that can change over time in your application. When state changes, React re-renders the components that depend on that state, updating the UI to reflect the new data.

## React Hooks for State Management

React Hooks are functions that let you "hook into" React state and lifecycle features from function components. We'll be using several hooks in our PhotoSky application:

1. [useState](https://react.dev/reference/react/useState): For managing local component state
2. [useEffect](https://react.dev/reference/react/useEffect): For performing side effects in function components
3. [useCallback](https://react.dev/reference/react/useCallback): For memoizing functions to optimize performance
4. [useRef](https://react.dev/reference/react/useRef): For creating mutable references that persist across re-renders

Let's see how we're using these hooks in our PhotoSky application.

## Managing Application State

In our `Album` component (inside `App.js`), we're using several `useState` hooks to manage our application state:

```jsx
// the parameter being passed to useState is used as the default value
const [images, setImages] = useState([]);
const [selectedImage, setSelectedImage] = useState(null);
const [dialogOpen, setDialogOpen] = useState(false);
const [navValue, setNavValue] = useState(0);
const [themeMode, setThemeMode] = useState('system');
const [isDarkMode, setIsDarkMode] = useState(false);
const [loading, setLoading] = useState(false);
const [imageDialogOpen, setImageDialogOpen] = useState(false);
const [menuAnchorEl, setMenuAnchorEl] = useState(null);
```

These state variables manage different aspects of our application:

- `images`: Stores the list of images fetched from the backend
- `selectedImage`: Keeps track of the image selected for viewing or deletion
- `dialogOpen`: Controls the visibility of the image viewer dialog
- `navValue`: Manages the selected value in the bottom navigation
- `themeMode`: Stores the current theme mode ('light', 'dark', or 'system')
- `isDarkMode`: A boolean indicating whether dark mode is active
- `loading`: Indicates whether a loading operation is in progress
- `imageDialogOpen`: Controls the visibility of the image upload/capture dialog
- `menuAnchorEl`: Stores the anchor element for the options menu

## Fetching Images and Error Handling

We use the [useCallback](https://react.dev/reference/react/useCallback) hook to memoize our `fetchImages` function, and the [useEffect](https://react.dev/reference/react/useEffect) hook to call it when the component mounts. We also implement error handling and loading state management:

```jsx
const fetchImages = useCallback(async () => {
  setLoading(true);
  try {
    // query api
    const response = await axios.get(`${API_URL}/list-images`);
    // set state to be the response
    setImages(response.data.images);
    enqueueSnackbar('Images loaded successfully', { variant: 'success' });
  } catch (error) {
    console.error('Error fetching images:', error);
    enqueueSnackbar('Error fetching images', { variant: 'error' });
  } finally {
    setLoading(false);
  }
}, [API_URL, enqueueSnackbar]);

useEffect(() => {
  fetchImages();
}, [fetchImages]);
```

Note the use of `setLoading(true)` at the start of the operation and `setLoading(false)` in the `finally` block. This ensures that the loading state is properly managed regardless of whether the operation succeeds or fails.

## Managing Theme

We use state to manage the theme and provide a function to toggle it:

```jsx
const handleToggleThemeMode = () => {
  if (themeMode === 'system') {
    setThemeMode('light');
    setIsDarkMode(false);
  } else if (themeMode === 'light') {
    setThemeMode('dark');
    setIsDarkMode(true);
  } else {
    setThemeMode('system');
    checkDarkMode();
  }
};

const checkDarkMode = useCallback(() => {
  const systemDarkMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
  if (themeMode === 'system') {
    setIsDarkMode(systemDarkMode);
  }
}, [themeMode]);

useEffect(() => {
  checkDarkMode();
}, [themeMode, checkDarkMode]);
```


## Error Handling and Notifications

We use the `notistack` library for displaying notifications to the user. The [enqueueSnackbar](https://notistack.com/api-reference#enqueuesnackbar-options) function is used throughout the application to show success and error messages:

```jsx
import { useSnackbar } from 'notistack';

function Album() {
  const { enqueueSnackbar } = useSnackbar();

  // ... other code ...

  enqueueSnackbar('Success message', { variant: 'success' });
  enqueueSnackbar('Error message', { variant: 'error' });

  // ... other code ...
}
```

## Loading State

We use a `loading` state variable to track when API calls are in progress. This is used to display a loading indicator:

```jsx
{loading && <LinearProgress />}
```

## Conclusion

We've implemented comprehensive state management in our PhotoSky application using React hooks. We're using:

- [useState](https://react.dev/reference/react/useState) for managing local state (images, theme, dialogs, etc.)
- [useEffect](https://react.dev/reference/react/useEffect) for side effects (fetching images, checking dark mode)
- [useCallback](https://react.dev/reference/react/useCallback) for memoizing functions (fetch images, upload, take picture)
- [useRef](https://react.dev/reference/react/useEffect) for creating a reference to the file input element

We've also implemented error handling, loading states, and a notification system to provide a smooth user experience. By using environment variables, we've made our application configurable and easier to deploy to different environments.

This approach allows us to manage our application's state efficiently, ensuring that our UI stays in sync with our data and that we're not unnecessarily re-rendering components or recreating functions. It also provides a robust way to handle asynchronous operations and keep the user informed about the application's status.

In the next section, we'll focus on integrating our frontend with the backend API, implementing the full functionality of our PhotoSky application.