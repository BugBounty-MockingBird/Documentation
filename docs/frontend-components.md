# Frontend Component Library

Documentation for the BugBounty KSP frontend component library, built with React and TypeScript.

## 🎨 Design System

### Color Palette

```css
/* Primary Colors */
--primary-50: #e3f2fd;
--primary-100: #bbdefb;
--primary-500: #2196f3;
--primary-700: #1976d2;
--primary-900: #0d47a1;

/* Secondary Colors */
--secondary-500: #ff4081;
--secondary-700: #c51162;

/* Severity Colors */
--critical: #d32f2f;
--high: #f57c00;
--medium: #ffa726;
--low: #66bb6a;

/* Neutrals */
--gray-50: #fafafa;
--gray-100: #f5f5f5;
--gray-500: #9e9e9e;
--gray-900: #212121;

/* Semantic Colors */
--success: #4caf50;
--warning: #ff9800;
--error: #f44336;
--info: #2196f3;
```

### Typography

```css
/* Font Family */
font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;

/* Font Sizes */
--text-xs: 0.75rem;    /* 12px */
--text-sm: 0.875rem;   /* 14px */
--text-base: 1rem;     /* 16px */
--text-lg: 1.125rem;   /* 18px */
--text-xl: 1.25rem;    /* 20px */
--text-2xl: 1.5rem;    /* 24px */
--text-3xl: 1.875rem;  /* 30px */
--text-4xl: 2.25rem;   /* 36px */

/* Font Weights */
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

### Spacing Scale

```css
--space-1: 0.25rem;  /* 4px */
--space-2: 0.5rem;   /* 8px */
--space-3: 0.75rem;  /* 12px */
--space-4: 1rem;     /* 16px */
--space-6: 1.5rem;   /* 24px */
--space-8: 2rem;     /* 32px */
--space-12: 3rem;    /* 48px */
--space-16: 4rem;    /* 64px */
```

## 🧱 Core Components

### Button

Versatile button component with multiple variants.

```tsx
import { Button } from '@/components/Button';

// Primary Button
<Button variant="primary" onClick={handleClick}>
  Submit Report
</Button>

// Secondary Button
<Button variant="secondary" size="large">
  Cancel
</Button>

// Danger Button
<Button variant="danger" disabled>
  Delete Program
</Button>

// Loading State
<Button loading>
  Processing...
</Button>
```

**Props:**
- `variant`: 'primary' | 'secondary' | 'danger' | 'ghost' | 'link'
- `size`: 'small' | 'medium' | 'large'
- `loading`: boolean
- `disabled`: boolean
- `fullWidth`: boolean

### Card

Container component for grouping related content.

```tsx
import { Card } from '@/components/Card';

<Card>
  <Card.Header>
    <Card.Title>Vulnerability Report</Card.Title>
    <Card.Subtitle>SQL Injection</Card.Subtitle>
  </Card.Header>
  <Card.Body>
    <p>Detailed vulnerability description...</p>
  </Card.Body>
  <Card.Footer>
    <Button>View Details</Button>
  </Card.Footer>
</Card>
```

**Props:**
- `variant`: 'default' | 'outlined' | 'elevated'
- `padding`: 'none' | 'small' | 'medium' | 'large'
- `hoverable`: boolean

### Input

Text input component with validation support.

```tsx
import { Input } from '@/components/Input';

<Input
  label="Email Address"
  type="email"
  placeholder="user@example.com"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
  error={emailError}
  required
/>
```

**Props:**
- `label`: string
- `type`: 'text' | 'email' | 'password' | 'number' | 'url'
- `placeholder`: string
- `error`: string
- `helperText`: string
- `required`: boolean
- `disabled`: boolean

### Select

Dropdown selection component.

```tsx
import { Select } from '@/components/Select';

<Select
  label="Severity"
  value={severity}
  onChange={(value) => setSeverity(value)}
  options={[
    { value: 'critical', label: 'Critical' },
    { value: 'high', label: 'High' },
    { value: 'medium', label: 'Medium' },
    { value: 'low', label: 'Low' }
  ]}
/>
```

### TextArea

Multi-line text input for longer content.

```tsx
import { TextArea } from '@/components/TextArea';

<TextArea
  label="Description"
  rows={6}
  value={description}
  onChange={(e) => setDescription(e.target.value)}
  placeholder="Provide a detailed description..."
  maxLength={5000}
/>
```

### Badge

Small label component for status indicators.

```tsx
import { Badge } from '@/components/Badge';

<Badge variant="critical">Critical</Badge>
<Badge variant="success">Resolved</Badge>
<Badge variant="warning">Pending</Badge>
<Badge variant="info">New</Badge>
```

**Props:**
- `variant`: 'critical' | 'high' | 'medium' | 'low' | 'success' | 'warning' | 'info'
- `size`: 'small' | 'medium' | 'large'

### Modal

Dialog component for focused interactions.

```tsx
import { Modal } from '@/components/Modal';

<Modal
  isOpen={isOpen}
  onClose={() => setIsOpen(false)}
  title="Submit Report"
  size="large"
>
  <Modal.Body>
    <ReportForm />
  </Modal.Body>
  <Modal.Footer>
    <Button variant="secondary" onClick={() => setIsOpen(false)}>
      Cancel
    </Button>
    <Button variant="primary" onClick={handleSubmit}>
      Submit
    </Button>
  </Modal.Footer>
</Modal>
```

**Props:**
- `isOpen`: boolean
- `onClose`: () => void
- `title`: string
- `size`: 'small' | 'medium' | 'large' | 'fullscreen'
- `closeOnOverlayClick`: boolean

### Table

Data table component with sorting and pagination.

```tsx
import { Table } from '@/components/Table';

<Table
  columns={[
    { key: 'title', label: 'Title', sortable: true },
    { key: 'severity', label: 'Severity', sortable: true },
    { key: 'status', label: 'Status' },
    { key: 'submitted_at', label: 'Date', sortable: true }
  ]}
  data={reports}
  onSort={handleSort}
  loading={isLoading}
/>
```

### Tabs

Tabbed navigation component.

```tsx
import { Tabs } from '@/components/Tabs';

<Tabs defaultValue="overview">
  <Tabs.List>
    <Tabs.Tab value="overview">Overview</Tabs.Tab>
    <Tabs.Tab value="details">Details</Tabs.Tab>
    <Tabs.Tab value="timeline">Timeline</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel value="overview">
    <ProgramOverview />
  </Tabs.Panel>
  <Tabs.Panel value="details">
    <ProgramDetails />
  </Tabs.Panel>
  <Tabs.Panel value="timeline">
    <ProgramTimeline />
  </Tabs.Panel>
</Tabs>
```

### Alert

Notification and message component.

```tsx
import { Alert } from '@/components/Alert';

<Alert variant="success" title="Success!">
  Your report has been submitted successfully.
</Alert>

<Alert variant="error" onClose={handleClose}>
  An error occurred. Please try again.
</Alert>
```

**Props:**
- `variant`: 'success' | 'error' | 'warning' | 'info'
- `title`: string
- `onClose`: () => void
- `dismissible`: boolean

## 🎯 Specialized Components

### ReportCard

Displays vulnerability report summary.

```tsx
import { ReportCard } from '@/components/ReportCard';

<ReportCard
  report={{
    id: 'uuid',
    title: 'SQL Injection in Login',
    severity: 'critical',
    status: 'new',
    submitted_at: '2026-02-04'
  }}
  onClick={() => navigate(`/reports/${report.id}`)}
/>
```

### ProgramCard

Displays bounty program information.

{% raw %}
```tsx
import { ProgramCard } from '@/components/ProgramCard';

<ProgramCard
  program={{
    name: 'Web Application Security',
    reward_range: { min: 100, max: 10000 },
    status: 'active',
    total_reports: 125
  }}
/>
```
{% endraw %}

### SeverityBadge

Displays severity level with appropriate styling.

```tsx
import { SeverityBadge } from '@/components/SeverityBadge';

<SeverityBadge severity="critical" />
<SeverityBadge severity="high" />
<SeverityBadge severity="medium" />
<SeverityBadge severity="low" />
```

### StatusIndicator

Visual indicator for status states.

```tsx
import { StatusIndicator } from '@/components/StatusIndicator';

<StatusIndicator status="new" label="New" />
<StatusIndicator status="triaged" label="Triaged" />
<StatusIndicator status="resolved" label="Resolved" />
```

### Timeline

Visual timeline for events.

```tsx
import { Timeline } from '@/components/Timeline';

<Timeline
  events={[
    {
      type: 'submitted',
      timestamp: '2026-02-01',
      user: 'John Doe',
      description: 'Report submitted'
    },
    {
      type: 'triaged',
      timestamp: '2026-02-02',
      user: 'Admin',
      description: 'Report triaged'
    }
  ]}
/>
```

## 📐 Layout Components

### Container

Responsive container with max-width.

```tsx
import { Container } from '@/components/Container';

<Container size="large">
  <YourContent />
</Container>
```

### Grid

Flexible grid layout system.

```tsx
import { Grid } from '@/components/Grid';

<Grid cols={3} gap={4}>
  <Grid.Item>
    <Card>Content 1</Card>
  </Grid.Item>
  <Grid.Item>
    <Card>Content 2</Card>
  </Grid.Item>
  <Grid.Item>
    <Card>Content 3</Card>
  </Grid.Item>
</Grid>
```

### Stack

Vertical or horizontal stack layout.

```tsx
import { Stack } from '@/components/Stack';

<Stack direction="vertical" spacing={4}>
  <Button>Action 1</Button>
  <Button>Action 2</Button>
  <Button>Action 3</Button>
</Stack>
```

## 🎨 Styling

### Using CSS Modules

```tsx
import styles from './MyComponent.module.css';

export const MyComponent = () => (
  <div className={styles.container}>
    <h1 className={styles.title}>Hello</h1>
  </div>
);
```

### Using Styled Components

```tsx
import styled from 'styled-components';

const Container = styled.div`
  padding: var(--space-4);
  background: var(--gray-50);
  border-radius: 8px;
`;

const Title = styled.h1`
  font-size: var(--text-2xl);
  font-weight: var(--font-bold);
  color: var(--gray-900);
`;
```

## 🔧 Utility Hooks

### useToast

Display toast notifications.

```tsx
import { useToast } from '@/hooks/useToast';

const { toast } = useToast();

toast.success('Report submitted successfully!');
toast.error('An error occurred');
toast.warning('Please review your input');
```

### useModal

Manage modal state.

```tsx
import { useModal } from '@/hooks/useModal';

const { isOpen, open, close } = useModal();

<Button onClick={open}>Open Modal</Button>
<Modal isOpen={isOpen} onClose={close}>
  Content
</Modal>
```

### useForm

Form state management with validation.

```tsx
import { useForm } from '@/hooks/useForm';

const { values, errors, handleChange, handleSubmit } = useForm({
  initialValues: { email: '', password: '' },
  onSubmit: (values) => console.log(values),
  validate: (values) => {
    const errors = {};
    if (!values.email) errors.email = 'Required';
    return errors;
  }
});
```

## 📚 Best Practices

### Component Structure

```tsx
// imports
import React from 'react';
import { Button } from '@/components/Button';

// types
interface MyComponentProps {
  title: string;
  onSubmit: () => void;
}

// component
export const MyComponent: React.FC<MyComponentProps> = ({ 
  title, 
  onSubmit 
}) => {
  return (
    <div>
      <h1>{title}</h1>
      <Button onClick={onSubmit}>Submit</Button>
    </div>
  );
};
```

### Accessibility

- Always provide `aria-label` for icon buttons
- Use semantic HTML elements
- Ensure keyboard navigation works
- Maintain proper heading hierarchy
- Provide alternative text for images

### Performance

- Use React.memo for expensive components
- Implement lazy loading for large lists
- Debounce search inputs
- Use code splitting for routes

---

[← API Reference](api-reference.md) | [Home](index.md) | [Next: n8n Workflows →](n8n-workflows.md)
