---
title: Material UI Copilot Instructions
applyTo: "**/*.js,**/*.jsx,**/*.ts,**/*.tsx"
---
# Material UI Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
uses Material UI (MUI) following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

**Note**: Material UI is a component-based styling solution. If your project uses 
Tailwind CSS for styling, refer to the Tailwind CSS instructions instead. These 
two approaches can coexist in a monorepo with separate projects, but mixing them 
within the same application is not recommended.

## Coding Standards

### Naming Conventions

- Use PascalCase for custom MUI component wrappers (e.g., `CustomButton`, 
`StyledTextField`, `AppBarComponent`).
- Prefix styled components with "Styled" (e.g., `StyledButton`, 
`StyledCard`, `StyledDialog`).
- Name theme customization files as `theme.ts` or `theme.js` in a dedicated 
theme directory.
- Use descriptive names for theme variants (e.g., `lightTheme`, `darkTheme`, 
`customPalette`).
- Name custom hook files with the "use" prefix (e.g., `useTheme`, 
`useMediaQuery`, `useDialog`).
- Use kebab-case for icon component files when creating custom icons.

### Component Usage

- Always use MUI components instead of native HTML elements when available 
for consistency and theming support.
- Prefer using MUI's layout components (`Box`, `Stack`, `Grid`, `Container`) 
over div elements:
  ```jsx
  // Good
  <Box sx={{ display: 'flex', gap: 2 }}>
    <Button>Click me</Button>
  </Box>
  
  // Avoid
  <div style={{ display: 'flex', gap: '16px' }}>
    <Button>Click me</Button>
  </div>
  ```
- Use `Stack` for simple one-dimensional layouts with consistent spacing:
  ```jsx
  <Stack spacing={2} direction="row" alignItems="center">
    <Button>First</Button>
    <Button>Second</Button>
  </Stack>
  ```
- Use `Grid` for complex two-dimensional responsive layouts:
  ```jsx
  <Grid container spacing={2}>
    <Grid item xs={12} md={6}>
      <Card>Content 1</Card>
    </Grid>
    <Grid item xs={12} md={6}>
      <Card>Content 2</Card>
    </Grid>
  </Grid>
  ```
- Use `Container` for consistent page-level max-width and padding:
  ```jsx
  <Container maxWidth="lg">
    <Typography variant="h1">Page Title</Typography>
  </Container>
  ```

### Styling with the sx Prop

- Prefer the `sx` prop over inline styles for all styling needs:
  ```jsx
  // Good
  <Box sx={{ p: 2, bgcolor: 'primary.main', color: 'primary.contrastText' }}>
  
  // Avoid
  <Box style={{ padding: '16px', backgroundColor: '#1976d2', color: '#fff' }}>
  ```
- Use theme tokens in `sx` prop for consistency:
  ```jsx
  <Box
    sx={{
      p: 3,                      // padding: theme.spacing(3)
      mt: 2,                     // marginTop: theme.spacing(2)
      bgcolor: 'primary.main',   // theme.palette.primary.main
      borderRadius: 1,           // theme.shape.borderRadius
      boxShadow: 2,              // theme.shadows[2]
    }}
  >
    {/* Content */}
  </Box>
  ```
- Use responsive values in `sx` prop for mobile-first design:
  ```jsx
  <Box
    sx={{
      width: { xs: '100%', sm: '50%', md: '33.33%' },
      p: { xs: 2, md: 3 },
      display: { xs: 'block', md: 'flex' },
    }}
  >
    {/* Content */}
  </Box>
  ```
- Use callback syntax when you need access to the theme:
  ```jsx
  <Box
    sx={(theme) => ({
      backgroundColor: theme.palette.mode === 'dark' 
        ? theme.palette.grey[800] 
        : theme.palette.grey[100],
      '&:hover': {
        backgroundColor: theme.palette.action.hover,
      },
    })}
  >
    {/* Content */}
  </Box>
  ```
- Group related styles logically: layout → spacing → sizing → colors → 
effects → pseudo-classes.
- Use shorthand properties when available (`p` for padding, `m` for margin, 
`bgcolor` for backgroundColor).

### Styled Components API

- Use the `styled` API for creating reusable component variants:
  ```typescript
  import { styled } from '@mui/material/styles';
  import Button from '@mui/material/Button';
  
  const CustomButton = styled(Button)(({ theme }) => ({
    borderRadius: theme.shape.borderRadius * 2,
    textTransform: 'none',
    fontWeight: 600,
    '&:hover': {
      transform: 'scale(1.05)',
    },
  }));
  ```
- Accept and use component props in styled components:
  ```typescript
  const StyledCard = styled(Card, {
    shouldForwardProp: (prop) => prop !== 'featured',
  })<{ featured?: boolean }>(({ theme, featured }) => ({
    border: featured ? `2px solid ${theme.palette.primary.main}` : 'none',
    boxShadow: featured ? theme.shadows[8] : theme.shadows[2],
  }));
  ```
- Use `shouldForwardProp` to prevent non-standard props from reaching the DOM.
- Define TypeScript types for custom props in styled components.
- Keep styled component definitions co-located with their usage or in a 
dedicated styles file.

### Theme Configuration

- Always create and use a custom theme with `createTheme`:
  ```typescript
  import { createTheme } from '@mui/material/styles';
  
  const theme = createTheme({
    palette: {
      primary: {
        main: '#1976d2',
      },
      secondary: {
        main: '#dc004e',
      },
    },
    typography: {
      fontFamily: '"Roboto", "Helvetica", "Arial", sans-serif',
      h1: {
        fontSize: '2.5rem',
        fontWeight: 600,
      },
    },
    shape: {
      borderRadius: 8,
    },
    spacing: 8,
  });
  ```
- Wrap your app with `ThemeProvider` at the root:
  ```jsx
  import { ThemeProvider } from '@mui/material/styles';
  
  function App() {
    return (
      <ThemeProvider theme={theme}>
        {/* Your app components */}
      </ThemeProvider>
    );
  }
  ```
- Use theme augmentation for custom properties:
  ```typescript
  declare module '@mui/material/styles' {
    interface Theme {
      status: {
        danger: string;
      };
    }
    interface ThemeOptions {
      status?: {
        danger?: string;
      };
    }
  }
  ```
- Access theme in components using `useTheme` hook:
  ```typescript
  import { useTheme } from '@mui/material/styles';
  
  function MyComponent() {
    const theme = useTheme();
    return <div style={{ color: theme.palette.primary.main }} />;
  }
  ```

### Typography

- Always use the `Typography` component for text content:
  ```jsx
  <Typography variant="h1">Main Heading</Typography>
  <Typography variant="body1">Body text</Typography>
  <Typography variant="caption" color="text.secondary">Caption text</Typography>
  ```
- Use semantic variants: `h1` through `h6` for headings, `body1` and `body2` 
for body text, `caption` for small text.
- Use the `component` prop to ensure proper semantic HTML:
  ```jsx
  <Typography variant="h2" component="h1">
    Visually h2, semantically h1
  </Typography>
  ```
- Leverage typography color tokens:
  ```jsx
  <Typography color="primary">Primary colored text</Typography>
  <Typography color="text.secondary">Secondary text</Typography>
  <Typography color="error">Error text</Typography>
  ```
- Customize typography in theme for consistent application-wide styling:
  ```typescript
  const theme = createTheme({
    typography: {
      h1: {
        fontSize: '3rem',
        fontWeight: 700,
        lineHeight: 1.2,
      },
      body1: {
        fontSize: '1rem',
        lineHeight: 1.5,
      },
    },
  });
  ```

### Color System

- Always use theme palette colors instead of hardcoded values:
  ```jsx
  // Good
  <Box sx={{ bgcolor: 'primary.main', color: 'primary.contrastText' }}>
  <Box sx={{ bgcolor: 'background.paper', color: 'text.primary' }}>
  
  // Avoid
  <Box sx={{ bgcolor: '#1976d2', color: '#ffffff' }}>
  ```
- Use palette variations: `main`, `light`, `dark`, `contrastText` for theme 
colors.
- Leverage semantic color tokens:
  - `primary.main`, `secondary.main`, `error.main`, `warning.main`, 
`info.main`, `success.main`
  - `text.primary`, `text.secondary`, `text.disabled`
  - `background.default`, `background.paper`
  - `divider`, `action.active`, `action.hover`, `action.selected`, 
`action.disabled`
- Use color with opacity when needed:
  ```jsx
  <Box sx={{ bgcolor: 'primary.main', opacity: 0.5 }}>
  ```
- Define custom colors in the theme palette:
  ```typescript
  const theme = createTheme({
    palette: {
      brand: {
        main: '#ff6b6b',
        light: '#ff9999',
        dark: '#cc5555',
        contrastText: '#fff',
      },
    },
  });
  
  // Augment the palette types
  declare module '@mui/material/styles' {
    interface Palette {
      brand: Palette['primary'];
    }
    interface PaletteOptions {
      brand?: PaletteOptions['primary'];
    }
  }
  ```

### Responsive Design

- Use MUI's breakpoint system with the `sx` prop:
  ```jsx
  <Box
    sx={{
      fontSize: { xs: '0.875rem', sm: '1rem', md: '1.125rem' },
      p: { xs: 1, sm: 2, md: 3 },
    }}
  >
  ```
- Breakpoints: `xs` (0px), `sm` (600px), `md` (900px), `lg` (1200px), `xl` 
(1536px).
- Use `useMediaQuery` hook for conditional rendering based on breakpoints:
  ```typescript
  import { useMediaQuery, useTheme } from '@mui/material';
  
  function MyComponent() {
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down('sm'));
    const isDesktop = useMediaQuery(theme.breakpoints.up('md'));
    
    return isMobile ? <MobileView /> : <DesktopView />;
  }
  ```
- Use responsive props on Grid components:
  ```jsx
  <Grid container spacing={{ xs: 2, md: 3 }}>
    <Grid item xs={12} sm={6} md={4}>
      <Card />
    </Grid>
  </Grid>
  ```
- Customize breakpoints in theme if needed:
  ```typescript
  const theme = createTheme({
    breakpoints: {
      values: {
        xs: 0,
        sm: 600,
        md: 960,
        lg: 1280,
        xl: 1920,
      },
    },
  });
  ```
- Use `Hidden` component (deprecated in v5) or conditional rendering with 
`useMediaQuery` for showing/hiding content at specific breakpoints.

### Button Variants and States

- Use appropriate button variants for different contexts:
  ```jsx
  <Button variant="contained">Primary Action</Button>
  <Button variant="outlined">Secondary Action</Button>
  <Button variant="text">Tertiary Action</Button>
  ```
- Set button colors using the `color` prop:
  ```jsx
  <Button variant="contained" color="primary">Primary</Button>
  <Button variant="contained" color="secondary">Secondary</Button>
  <Button variant="contained" color="error">Delete</Button>
  <Button variant="contained" color="success">Confirm</Button>
  ```
- Add icons to buttons for better UX:
  ```jsx
  <Button startIcon={<RiAddLine />}>Add Item</Button>
  <Button endIcon={<RiSendPlaneLine />}>Send</Button>
  <IconButton color="primary"><RiDeleteBinLine /></IconButton>
  ```
- Use `LoadingButton` from `@mui/lab` for async operations:
  ```jsx
  import LoadingButton from '@mui/lab/LoadingButton';
  
  <LoadingButton
    loading={isLoading}
    loadingIndicator="Loading..."
    variant="contained"
    onClick={handleSubmit}
  >
    Submit
  </LoadingButton>
  ```
- Set button sizes:
  ```jsx
  <Button size="small">Small</Button>
  <Button size="medium">Medium</Button>
  <Button size="large">Large</Button>
  ```
- Use `disabled` prop for non-interactive states:
  ```jsx
  <Button disabled>Disabled Button</Button>
  ```

### Form Components and Validation

- Always use MUI form components for consistent styling:
  ```jsx
  <TextField
    label="Email"
    variant="outlined"
    fullWidth
    required
    error={!!errors.email}
    helperText={errors.email?.message}
  />
  ```
- Use controlled components with React state or form libraries:
  ```jsx
  const [value, setValue] = useState('');
  
  <TextField
    value={value}
    onChange={(e) => setValue(e.target.value)}
    label="Name"
  />
  ```
- Integrate with React Hook Form for complex forms:
  ```typescript
  import { useForm, Controller } from 'react-hook-form';
  
  const { control, handleSubmit } = useForm();
  
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
      />
    )}
  />
  ```
- Use `FormControl`, `FormLabel`, and `FormHelperText` for grouped inputs:
  ```jsx
  <FormControl error={!!error} fullWidth>
    <FormLabel>Label</FormLabel>
    <TextField />
    <FormHelperText>{error || 'Helper text'}</FormHelperText>
  </FormControl>
  ```
- Use appropriate input variants: `outlined` (default), `filled`, `standard`.
- Implement proper form validation states:
  ```jsx
  <TextField
    error={hasError}
    helperText={hasError ? 'This field is required' : ''}
  />
  ```
- Use `Select`, `Checkbox`, `Radio`, `Switch` components consistently:
  ```jsx
  <FormControl fullWidth>
    <InputLabel>Age</InputLabel>
    <Select value={age} onChange={handleChange} label="Age">
      <MenuItem value={10}>Ten</MenuItem>
      <MenuItem value={20}>Twenty</MenuItem>
    </Select>
  </FormControl>
  
  <FormControlLabel
    control={<Checkbox checked={checked} onChange={handleChange} />}
    label="Accept terms"
  />
  ```

### Icons

- Import icons from `@remixicon/react` (Remix icons):
  ```jsx
  import { RiDeleteBinLine, RiEditLine, RiSaveLine } from '@remixicon/react';
  ```
- Use `IconButton` for icon-only interactive elements:
  ```jsx
  <IconButton color="primary" onClick={handleEdit}>
    <RiEditLine />
  </IconButton>
  ```
- Set icon sizes using `sx` prop or inline styles:
  ```jsx
  <RiDeleteBinLine style={{ fontSize: '1rem' }} />
  <RiDeleteBinLine style={{ fontSize: '1.25rem' }} />
  <RiDeleteBinLine style={{ fontSize: '1.5rem' }} />
  <RiDeleteBinLine style={{ fontSize: 40 }} />
  ```
- Use icons with consistent colors via `sx` prop or style:
  ```jsx
  <Box sx={{ color: 'primary.main' }}>
    <RiDeleteBinLine />
  </Box>
  <Box sx={{ color: 'error.main' }}>
    <RiDeleteBinLine />
  </Box>
  <Box sx={{ color: 'text.secondary' }}>
    <RiDeleteBinLine />
  </Box>
  ```
- Add ARIA labels to icon buttons for accessibility:
  ```jsx
  <IconButton aria-label="delete" onClick={handleDelete}>
    <RiDeleteBinLine />
  </IconButton>
  ```
- Use `SvgIcon` for custom SVG icons:
  ```jsx
  import SvgIcon from '@mui/material/SvgIcon';
  
  function CustomIcon(props) {
    return (
      <SvgIcon {...props}>
        <path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z" />
      </SvgIcon>
    );
  }
  ```

### Dialog and Modal Patterns

- Use `Dialog` component for modal interactions:
  ```jsx
  import Dialog from '@mui/material/Dialog';
  import DialogTitle from '@mui/material/DialogTitle';
  import DialogContent from '@mui/material/DialogContent';
  import DialogActions from '@mui/material/DialogActions';
  
  <Dialog open={open} onClose={handleClose} maxWidth="sm" fullWidth>
    <DialogTitle>Dialog Title</DialogTitle>
    <DialogContent>
      <Typography>Dialog content goes here</Typography>
    </DialogContent>
    <DialogActions>
      <Button onClick={handleClose}>Cancel</Button>
      <Button onClick={handleConfirm} variant="contained">
        Confirm
      </Button>
    </DialogActions>
  </Dialog>
  ```
- Use `maxWidth` prop to control dialog width: `xs`, `sm`, `md`, `lg`, `xl`.
- Implement responsive dialogs with `fullScreen`:
  ```jsx
  const isMobile = useMediaQuery(theme.breakpoints.down('sm'));
  
  <Dialog open={open} fullScreen={isMobile}>
  ```
- Use `Modal` for custom overlay content:
  ```jsx
  <Modal open={open} onClose={handleClose}>
    <Box sx={{
      position: 'absolute',
      top: '50%',
      left: '50%',
      transform: 'translate(-50%, -50%)',
      width: 400,
      bgcolor: 'background.paper',
      boxShadow: 24,
      p: 4,
    }}>
      <Typography>Modal Content</Typography>
    </Box>
  </Modal>
  ```
- Use `Drawer` for side panel navigation:
  ```jsx
  <Drawer anchor="left" open={open} onClose={handleClose}>
    <Box sx={{ width: 250 }} role="presentation">
      <List>
        <ListItemButton>
          <ListItemText primary="Item 1" />
        </ListItemButton>
      </List>
    </Box>
  </Drawer>
  ```
- Set `disableScrollLock` when needed to prevent body scroll lock.

### Card Component Patterns

- Use `Card` components for content grouping:
  ```jsx
  <Card>
    <CardMedia
      component="img"
      height="140"
      image="/image.jpg"
      alt="Description"
    />
    <CardContent>
      <Typography variant="h5" component="div">
        Card Title
      </Typography>
      <Typography variant="body2" color="text.secondary">
        Card description
      </Typography>
    </CardContent>
    <CardActions>
      <Button size="small">Learn More</Button>
    </CardActions>
  </Card>
  ```
- Use `CardHeader` for cards with avatars or actions:
  ```jsx
  <Card>
    <CardHeader
      avatar={<Avatar>R</Avatar>}
      action={
        <IconButton>
          <RiMoreFill />
        </IconButton>
      }
      title="Card Title"
      subheader="Subheader"
    />
    <CardContent>
      <Typography>Content</Typography>
    </CardContent>
  </Card>
  ```
- Add elevation to cards for visual hierarchy:
  ```jsx
  <Card elevation={3}>
  <Card elevation={0}>
  ```
- Use `variant="outlined"` for bordered cards without shadows:
  ```jsx
  <Card variant="outlined">
  ```

### List and Menu Components

- Use `List` components for displaying collections:
  ```jsx
  <List>
    <ListItem>
      <ListItemIcon>
        <RiInboxLine />
      </ListItemIcon>
      <ListItemText primary="Inbox" secondary="5 new messages" />
    </ListItem>
    <ListItemButton onClick={handleClick}>
      <ListItemText primary="Starred" />
    </ListItemButton>
  </List>
  ```
- Add dividers between list items:
  ```jsx
  <List>
    <ListItem>First Item</ListItem>
    <Divider />
    <ListItem>Second Item</ListItem>
  </List>
  ```
- Use `Menu` for dropdown menus:
  ```jsx
  const [anchorEl, setAnchorEl] = useState(null);
  const open = Boolean(anchorEl);
  
  <Button onClick={(e) => setAnchorEl(e.currentTarget)}>
    Open Menu
  </Button>
  <Menu anchorEl={anchorEl} open={open} onClose={() => setAnchorEl(null)}>
    <MenuItem onClick={handleClose}>Profile</MenuItem>
    <MenuItem onClick={handleClose}>Settings</MenuItem>
    <MenuItem onClick={handleClose}>Logout</MenuItem>
  </Menu>
  ```
- Use `Select` with `MenuItem` for form dropdowns.
- Implement nested lists with `Collapse`:
  ```jsx
  <ListItemButton onClick={handleClick}>
    <ListItemText primary="Inbox" />
    {open ? <RiArrowUpSLine /> : <RiArrowDownSLine />}
  </ListItemButton>
  <Collapse in={open} timeout="auto" unmountOnExit>
    <List component="div" disablePadding>
      <ListItemButton sx={{ pl: 4 }}>
        <ListItemText primary="Nested Item" />
      </ListItemButton>
    </List>
  </Collapse>
  ```

### Navigation Components

- Use `AppBar` with `Toolbar` for application headers:
  ```jsx
  <AppBar position="static">
    <Toolbar>
      <IconButton edge="start" color="inherit" sx={{ mr: 2 }}>
        <RiMenuLine />
      </IconButton>
      <Typography variant="h6" component="div" sx={{ flexGrow: 1 }}>
        App Title
      </Typography>
      <Button color="inherit">Login</Button>
    </Toolbar>
  </AppBar>
  ```
- Set appropriate `position` values: `static`, `fixed`, `absolute`, `sticky`, 
`relative`.
- Use `Tabs` for navigation within sections:
  ```jsx
  const [value, setValue] = useState(0);
  
  <Tabs value={value} onChange={(e, newValue) => setValue(newValue)}>
    <Tab label="Tab 1" />
    <Tab label="Tab 2" />
    <Tab label="Tab 3" />
  </Tabs>
  <TabPanel value={value} index={0}>
    Content 1
  </TabPanel>
  ```
- Use `BottomNavigation` for mobile navigation:
  ```jsx
  <BottomNavigation value={value} onChange={(e, newValue) => setValue(newValue)}>
    <BottomNavigationAction label="Home" icon={<RiHomeLine />} />
    <BottomNavigationAction label="Search" icon={<RiSearchLine />} />
    <BottomNavigationAction label="Profile" icon={<RiUserLine />} />
  </BottomNavigation>
  ```
- Implement breadcrumbs for hierarchical navigation:
  ```jsx
  <Breadcrumbs>
    <Link href="/">Home</Link>
    <Link href="/products">Products</Link>
    <Typography color="text.primary">Current Page</Typography>
  </Breadcrumbs>
  ```

### Feedback Components

- Use `Snackbar` for temporary notifications:
  ```jsx
  import Snackbar from '@mui/material/Snackbar';
  import Alert from '@mui/material/Alert';
  
  <Snackbar open={open} autoHideDuration={6000} onClose={handleClose}>
    <Alert onClose={handleClose} severity="success" sx={{ width: '100%' }}>
      Operation successful!
    </Alert>
  </Snackbar>
  ```
- Set Alert severity: `error`, `warning`, `info`, `success`.
- Use `CircularProgress` or `LinearProgress` for loading states:
  ```jsx
  <CircularProgress />
  <CircularProgress color="secondary" size={24} />
  
  <LinearProgress />
  <LinearProgress variant="determinate" value={progress} />
  ```
- Use `Backdrop` for loading overlays:
  ```jsx
  <Backdrop open={loading} sx={{ color: '#fff', zIndex: (theme) => theme.zIndex.drawer + 1 }}>
    <CircularProgress color="inherit" />
  </Backdrop>
  ```
- Use `Skeleton` for content placeholders:
  ```jsx
  <Skeleton variant="text" width={210} />
  <Skeleton variant="circular" width={40} height={40} />
  <Skeleton variant="rectangular" width={210} height={118} />
  ```
- Use `Tooltip` for providing additional context:
  ```jsx
  <Tooltip title="Delete">
    <IconButton>
      <RiDeleteBinLine />
    </IconButton>
  </Tooltip>
  ```

### Data Display Components

- Use `Table` components for tabular data:
  ```jsx
  <TableContainer component={Paper}>
    <Table>
      <TableHead>
        <TableRow>
          <TableCell>Name</TableCell>
          <TableCell>Age</TableCell>
        </TableRow>
      </TableHead>
      <TableBody>
        {rows.map((row) => (
          <TableRow key={row.id}>
            <TableCell>{row.name}</TableCell>
            <TableCell>{row.age}</TableCell>
          </TableRow>
        ))}
      </TableBody>
    </Table>
  </TableContainer>
  ```
- Use `Chip` for tags, labels, or compact information:
  ```jsx
  <Chip label="Tag" color="primary" />
  <Chip label="Clickable" onClick={handleClick} />
  <Chip label="Deletable" onDelete={handleDelete} />
  <Chip label="Icon" icon={<RiEmotionLine />} />
  ```
- Use `Badge` for notification indicators:
  ```jsx
  <Badge badgeContent={4} color="primary">
    <RiMailLine />
  </Badge>
  <Badge variant="dot" color="error">
    <RiNotificationLine />
  </Badge>
  ```
- Use `Avatar` for user representations:
  ```jsx
  <Avatar alt="User Name" src="/avatar.jpg" />
  <Avatar>UN</Avatar>
  <Avatar sx={{ bgcolor: 'primary.main' }}>
    <RiUserLine />
  </Avatar>
  ```
- Use `AvatarGroup` for multiple avatars:
  ```jsx
  <AvatarGroup max={4}>
    <Avatar alt="User 1" src="/avatar1.jpg" />
    <Avatar alt="User 2" src="/avatar2.jpg" />
    <Avatar alt="User 3" src="/avatar3.jpg" />
  </AvatarGroup>
  ```

### Accessibility

- Always provide meaningful ARIA labels for icon buttons and interactive 
elements:
  ```jsx
  <IconButton aria-label="delete" onClick={handleDelete}>
    <RiDeleteBinLine />
  </IconButton>
  ```
- Use proper semantic HTML with the `component` prop:
  ```jsx
  <Typography variant="h1" component="h1">Main Heading</Typography>
  ```
- Ensure sufficient color contrast ratios (WCAG AA minimum: 4.5:1 contrast ratio 
for normal text, 3:1 for large text).
- Provide alternative text for images in `CardMedia` and `Avatar`:
  ```jsx
  <CardMedia component="img" alt="Descriptive alt text" />
  <Avatar alt="User Name" src="/avatar.jpg" />
  ```
- Use `FormHelperText` to provide additional context for form inputs.
- Ensure keyboard navigation works properly with focusable elements.
- Use `role` attributes when creating custom interactive components.
- Test with screen readers to verify accessibility.
- Use focus indicators; avoid removing outlines without providing alternatives.
- Implement proper tab order for complex layouts.
- Use `aria-describedby` to associate helper text with inputs:
  ```jsx
  <TextField
    aria-describedby="helper-text"
    helperText={<span id="helper-text">Helper text</span>}
  />
  ```

### Performance Optimization

- Import components individually for better tree-shaking:
  ```typescript
  import Button from '@mui/material/Button';
  // Instead of: import { Button } from '@mui/material';
  ```
- Use dynamic imports for heavy components:
  ```typescript
  const HeavyComponent = lazy(() => import('./HeavyComponent'));
  
  <Suspense fallback={<CircularProgress />}>
    <HeavyComponent />
  </Suspense>
  ```
- Use `React.memo` for expensive MUI component wrappers:
  ```typescript
  const MemoizedCard = React.memo(CustomCard);
  ```
- Use `disableRipple` when ripple effects cause performance issues:
  ```jsx
  <Button disableRipple>No Ripple</Button>
  ```
- Leverage virtualization for long lists with libraries like `react-window` 
or `react-virtualized`.
- Minimize theme provider nesting; use a single ThemeProvider at the root.

### Component Composition

- Compose components for reusability rather than creating monolithic 
components:
  ```jsx
  // Good
  function UserCard({ user }) {
    return (
      <Card>
        <CardHeader title={user.name} subheader={user.role} />
        <CardContent>
          <UserDetails user={user} />
        </CardContent>
        <CardActions>
          <UserActions user={user} />
        </CardActions>
      </Card>
    );
  }
  ```
- Extract common patterns into reusable wrapper components:
  ```typescript
  function FormTextField({ name, label, control, rules }: Props) {
    return (
      <Controller
        name={name}
        control={control}
        rules={rules}
        render={({ field, fieldState: { error } }) => (
          <TextField
            {...field}
            label={label}
            error={!!error}
            helperText={error?.message}
            fullWidth
          />
        )}
      />
    );
  }
  ```
- Use slots and component prop for customization:
  ```jsx
  <Select
    MenuProps={{
      PaperProps: {
        sx: { maxHeight: 200 },
      },
    }}
  >
  ```

### Testing Best Practices

- Use `@testing-library/react` for testing MUI components:
  ```typescript
  import { render, screen } from '@testing-library/react';
  import { ThemeProvider, createTheme } from '@mui/material/styles';
  
  const theme = createTheme();
  
  test('renders button', () => {
    render(
      <ThemeProvider theme={theme}>
        <Button>Click me</Button>
      </ThemeProvider>
    );
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });
  ```
- Create a custom render function with providers:
  ```typescript
  function renderWithTheme(ui: React.ReactElement) {
    return render(<ThemeProvider theme={theme}>{ui}</ThemeProvider>);
  }
  ```
- Test user interactions with `userEvent`:
  ```typescript
  import userEvent from '@testing-library/user-event';
  
  test('handles button click', async () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    
    await userEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  ```
- Test form submissions and validations.
- Verify accessibility with `jest-axe`.
- Test responsive behavior by mocking `useMediaQuery`.

### Migration from MUI v4 to v5+

- Replace `makeStyles` with `styled` or `sx` prop:
  ```typescript
  // v4
  const useStyles = makeStyles((theme) => ({
    root: { padding: theme.spacing(2) },
  }));
  const classes = useStyles();
  
  // v5
  <Box sx={{ p: 2 }}>
  // or
  const StyledBox = styled(Box)(({ theme }) => ({
    padding: theme.spacing(2),
  }));
  ```
- Replace `@material-ui/core` imports with `@mui/material`.
- Update `createMuiTheme` to `createTheme`.
- Replace `fade` utility with `alpha`:
  ```typescript
  import { alpha } from '@mui/material/styles';
  
  backgroundColor: alpha(theme.palette.primary.main, 0.5);
  ```

### Code Organization

- Organize theme configuration in a dedicated `theme/` directory.
- Keep styled components in separate files or co-located with their usage.
- Create a component library directory for reusable custom MUI components.
- Group related components together (e.g., all form components).
- Export commonly used styled components from a centralized location.

### Best Practices Summary

- Always use MUI components instead of native HTML elements when available.
- Prefer the `sx` prop over inline styles for consistency with theme.
- Use theme tokens (colors, spacing, breakpoints) instead of hardcoded values.
- Implement proper TypeScript typing for custom components and theme 
extensions.
- Ensure accessibility with proper ARIA labels, semantic HTML, and keyboard 
navigation.
- Optimize performance by importing components individually and using 
memoization.
- Test components thoroughly with theme providers and user interactions.
- Follow responsive design principles using MUI's breakpoint system.
- Leverage MUI's extensive component library before creating custom components.
- Keep styling consistent by extending the theme rather than using arbitrary 
values.
- Document complex customizations and theme configurations.
- Use MUI's built-in features (elevation, variants, sizes) for consistency 
across the application.
