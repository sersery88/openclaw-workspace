---
name: shadcn-baseui
version: 1.0.0
description: Use when building UI with shadcn/ui components using Base UI primitives (NOT Radix UI). Tailwind CSS layouts, form patterns, theming, dark mode, and component implementation with @base-ui-components/react.
triggers:
  - shadcn
  - shadcn/ui
  - base ui
  - base-ui
  - component library
  - UI components
  - form pattern
  - dialog
  - select
  - menu
  - popover
  - tooltip
role: specialist
scope: implementation
output-format: code
---

# shadcn/ui + Base UI Expert

⚠️ **CRITICAL: We use Base UI (@base-ui-components/react), NOT Radix UI!**

## Core Rule

- **NEVER** import from `@radix-ui/*`
- **ALWAYS** use `@base-ui-components/react` for primitives
- shadcn/ui components should be built on Base UI primitives

## Base UI Installation

```bash
npm install @base-ui-components/react
# Import pattern: import { Component } from '@base-ui-components/react/component'
# OR (newer): import { Component } from '@base-ui/react/component'
```

## Styling Approach
- Use `clsx` for dynamic class merging
- Use Tailwind CSS utility classes
- Use `data-[state]` attributes for state-based styling (e.g. `data-[checked]`, `data-[highlighted]`, `data-[popup-open]`)
- Components accept `className` as string or function: `className={(state) => ...}`
- Components accept `render` prop for composition with other components

## Base UI vs Radix UI Mapping

| Feature | ❌ Radix UI | ✅ Base UI |
|---------|-----------|----------|
| Package | `@radix-ui/react-*` | `@base-ui-components/react` |
| Dialog | `@radix-ui/react-dialog` | `import { Dialog } from '@base-ui-components/react/dialog'` |
| Select | `@radix-ui/react-select` | `import { Select } from '@base-ui-components/react/select'` |
| Popover | `@radix-ui/react-popover` | `import { Popover } from '@base-ui-components/react/popover'` |
| Tooltip | `@radix-ui/react-tooltip` | `import { Tooltip } from '@base-ui-components/react/tooltip'` |
| Menu | `@radix-ui/react-dropdown-menu` | `import { Menu } from '@base-ui-components/react/menu'` |
| Checkbox | `@radix-ui/react-checkbox` | `import { Checkbox } from '@base-ui-components/react/checkbox'` |
| Switch | `@radix-ui/react-switch` | `import { Switch } from '@base-ui-components/react/switch'` |
| Tabs | `@radix-ui/react-tabs` | `import { Tabs } from '@base-ui-components/react/tabs'` |
| Accordion | `@radix-ui/react-accordion` | `import { Accordion } from '@base-ui-components/react/accordion'` |
| Slider | `@radix-ui/react-slider` | `import { Slider } from '@base-ui-components/react/slider'` |
| Progress | `@radix-ui/react-progress` | `import { Progress } from '@base-ui-components/react/progress'` |
| Toggle | `@radix-ui/react-toggle` | `import { Toggle } from '@base-ui-components/react/toggle'` |
| Collapsible | `@radix-ui/react-collapsible` | `import { Collapsible } from '@base-ui-components/react/collapsible'` |
| AlertDialog | `@radix-ui/react-alert-dialog` | `import { AlertDialog } from '@base-ui-components/react/alert-dialog'` |
| RadioGroup | `@radix-ui/react-radio-group` | `import { RadioGroup } from '@base-ui-components/react/radio-group'` |
| ScrollArea | `@radix-ui/react-scroll-area` | `import { ScrollArea } from '@base-ui-components/react/scroll-area'` |
| Separator | `@radix-ui/react-separator` | `import { Separator } from '@base-ui-components/react/separator'` |
| NumberField | N/A | `import { NumberField } from '@base-ui-components/react/number-field'` |
| PreviewCard | N/A | `import { PreviewCard } from '@base-ui-components/react/preview-card'` |
| Field | N/A | `import { Field } from '@base-ui-components/react/field'` |

## Component Patterns with Base UI

### Dialog

```tsx
import { Dialog } from '@base-ui-components/react/dialog'

export function ConfirmDialog({ open, onOpenChange, onConfirm, title, description }) {
  return (
    <Dialog.Root open={open} onOpenChange={onOpenChange}>
      <Dialog.Portal>
        <Dialog.Backdrop className="fixed inset-0 bg-black/50 backdrop-blur-sm" />
        <Dialog.Popup className="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-white rounded-xl p-6 shadow-xl max-w-md w-full">
          <Dialog.Title className="text-lg font-semibold">{title}</Dialog.Title>
          <Dialog.Description className="mt-2 text-sm text-gray-600">
            {description}
          </Dialog.Description>
          <div className="mt-6 flex justify-end gap-3">
            <Dialog.Close className="px-4 py-2 text-sm rounded-lg border hover:bg-gray-50">
              Cancel
            </Dialog.Close>
            <button
              onClick={onConfirm}
              className="px-4 py-2 text-sm rounded-lg bg-primary text-white hover:bg-primary/90"
            >
              Confirm
            </button>
          </div>
        </Dialog.Popup>
      </Dialog.Portal>
    </Dialog.Root>
  )
}
```

### Select

```tsx
import { Select } from '@base-ui-components/react/select'

export function CustomSelect({ options, value, onChange, placeholder }) {
  return (
    <Select.Root value={value} onValueChange={onChange}>
      <Select.Trigger className="flex h-10 w-full items-center justify-between rounded-lg border px-3 py-2 text-sm">
        <Select.Value placeholder={placeholder} />
        <Select.Icon>
          <ChevronDown className="h-4 w-4 opacity-50" />
        </Select.Icon>
      </Select.Trigger>
      <Select.Portal>
        <Select.Positioner>
          <Select.Popup className="bg-white rounded-lg border shadow-lg p-1">
            {options.map((option) => (
              <Select.Item
                key={option.value}
                value={option.value}
                className="flex items-center px-3 py-2 text-sm rounded-md cursor-pointer hover:bg-gray-100"
              >
                <Select.ItemText>{option.label}</Select.ItemText>
                <Select.ItemIndicator className="ml-auto">
                  <Check className="h-4 w-4" />
                </Select.ItemIndicator>
              </Select.Item>
            ))}
          </Select.Popup>
        </Select.Positioner>
      </Select.Portal>
    </Select.Root>
  )
}
```

### Menu (Dropdown)

```tsx
import { Menu } from '@base-ui-components/react/menu'

export function ActionMenu({ items }) {
  return (
    <Menu.Root>
      <Menu.Trigger className="inline-flex items-center justify-center rounded-lg border p-2 hover:bg-gray-50">
        <MoreHorizontal className="h-4 w-4" />
      </Menu.Trigger>
      <Menu.Portal>
        <Menu.Positioner>
          <Menu.Popup className="bg-white rounded-lg border shadow-lg p-1 min-w-[160px]">
            {items.map((item) => (
              <Menu.Item
                key={item.label}
                onClick={item.onClick}
                className="flex items-center px-3 py-2 text-sm rounded-md cursor-pointer hover:bg-gray-100"
              >
                {item.icon && <item.icon className="mr-2 h-4 w-4" />}
                {item.label}
              </Menu.Item>
            ))}
          </Menu.Popup>
        </Menu.Positioner>
      </Menu.Portal>
    </Menu.Root>
  )
}
```

### Tooltip

```tsx
import { Tooltip } from '@base-ui-components/react/tooltip'

export function IconWithTooltip({ icon: Icon, label }) {
  return (
    <Tooltip.Provider>
      <Tooltip.Root>
        <Tooltip.Trigger className="p-2 rounded-lg hover:bg-gray-100">
          <Icon className="h-4 w-4" />
        </Tooltip.Trigger>
        <Tooltip.Portal>
          <Tooltip.Positioner>
            <Tooltip.Popup className="bg-gray-900 text-white text-xs px-2 py-1 rounded-md">
              {label}
              <Tooltip.Arrow className="fill-gray-900" />
            </Tooltip.Popup>
          </Tooltip.Positioner>
        </Tooltip.Portal>
      </Tooltip.Root>
    </Tooltip.Provider>
  )
}
```

### Tabs

```tsx
import { Tabs } from '@base-ui-components/react/tabs'

export function ViewTabs({ tabs }) {
  return (
    <Tabs.Root defaultValue={tabs[0].value}>
      <Tabs.List className="flex border-b">
        {tabs.map((tab) => (
          <Tabs.Tab
            key={tab.value}
            value={tab.value}
            className="px-4 py-2 text-sm font-medium border-b-2 border-transparent data-[selected]:border-primary data-[selected]:text-primary hover:text-gray-700"
          >
            {tab.label}
          </Tabs.Tab>
        ))}
      </Tabs.List>
      {tabs.map((tab) => (
        <Tabs.Panel key={tab.value} value={tab.value} className="py-4">
          {tab.content}
        </Tabs.Panel>
      ))}
    </Tabs.Root>
  )
}
```

### Checkbox

```tsx
import { Checkbox } from '@base-ui-components/react/checkbox'

export function LabeledCheckbox({ checked, onCheckedChange, label }) {
  return (
    <label className="flex items-center gap-2 cursor-pointer">
      <Checkbox.Root
        checked={checked}
        onCheckedChange={onCheckedChange}
        className="h-4 w-4 rounded border border-gray-300 data-[checked]:bg-primary data-[checked]:border-primary"
      >
        <Checkbox.Indicator>
          <Check className="h-3 w-3 text-white" />
        </Checkbox.Indicator>
      </Checkbox.Root>
      <span className="text-sm">{label}</span>
    </label>
  )
}
```

### Switch

```tsx
import { Switch } from '@base-ui-components/react/switch'

export function ToggleSwitch({ checked, onCheckedChange, label }) {
  return (
    <label className="flex items-center gap-3">
      <Switch.Root
        checked={checked}
        onCheckedChange={onCheckedChange}
        className="relative h-6 w-11 rounded-full bg-gray-200 data-[checked]:bg-primary transition-colors"
      >
        <Switch.Thumb className="block h-5 w-5 rounded-full bg-white shadow translate-x-0.5 data-[checked]:translate-x-[22px] transition-transform" />
      </Switch.Root>
      <span className="text-sm font-medium">{label}</span>
    </label>
  )
}
```

### Field (Form Validation)

```tsx
import { Field } from '@base-ui-components/react/field'

export function ValidatedInput({ label, name, error }) {
  return (
    <Field.Root>
      <Field.Label className="text-sm font-medium">{label}</Field.Label>
      <Field.Control
        name={name}
        className="mt-1 h-10 w-full rounded-lg border px-3 text-sm focus:outline-none focus:ring-2 focus:ring-primary/20"
      />
      {error && (
        <Field.Error className="mt-1 text-xs text-red-500">{error}</Field.Error>
      )}
    </Field.Root>
  )
}
```

### Accordion

```tsx
import { Accordion } from '@base-ui-components/react/accordion'

export function FAQ({ items }) {
  return (
    <Accordion.Root className="space-y-2">
      {items.map((item, index) => (
        <Accordion.Item key={index} value={`item-${index}`} className="border rounded-lg">
          <Accordion.Header>
            <Accordion.Trigger className="flex w-full items-center justify-between px-4 py-3 text-sm font-medium hover:bg-gray-50">
              {item.question}
              <ChevronDown className="h-4 w-4 transition-transform data-[open]:rotate-180" />
            </Accordion.Trigger>
          </Accordion.Header>
          <Accordion.Panel className="px-4 pb-3 text-sm text-gray-600">
            {item.answer}
          </Accordion.Panel>
        </Accordion.Item>
      ))}
    </Accordion.Root>
  )
}
```

## Tailwind CSS Patterns

### Common Utility Patterns

```tsx
// Centering
<div className="flex items-center justify-center min-h-screen">

// Container with max-width
<div className="container mx-auto px-4">

// Responsive grid
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">

// Sticky header
<header className="sticky top-0 z-50 border-b bg-background/95 backdrop-blur">

// Truncated text
<p className="truncate">Very long text...</p>

// Animations
<div className="transition-all duration-200 hover:scale-105">
```

### Button Variants (Tailwind)

```tsx
// Primary
<button className="h-10 px-4 rounded-lg bg-primary text-white font-medium hover:bg-primary/90 transition-colors">

// Outline
<button className="h-10 px-4 rounded-lg border border-gray-200 bg-white font-medium hover:bg-gray-50 transition-colors">

// Ghost
<button className="h-10 px-4 rounded-lg font-medium hover:bg-gray-100 transition-colors">

// Destructive
<button className="h-10 px-4 rounded-lg bg-red-500 text-white font-medium hover:bg-red-600 transition-colors">

// Icon button
<button className="h-10 w-10 inline-flex items-center justify-center rounded-lg border hover:bg-gray-50">

// Loading
<button disabled className="h-10 px-4 rounded-lg bg-primary/70 text-white cursor-not-allowed">
  <Loader2 className="mr-2 h-4 w-4 animate-spin" /> Loading...
</button>
```

## Form Pattern with react-hook-form + zod + Base UI

```tsx
'use client'
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import { Field } from '@base-ui-components/react/field'
import { Checkbox } from '@base-ui-components/react/checkbox'
import { Select } from '@base-ui-components/react/select'

const schema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  role: z.enum(['admin', 'user']),
  agree: z.boolean().refine(v => v, 'Must agree'),
})

type FormData = z.infer<typeof schema>

export function UserForm() {
  const { register, handleSubmit, formState: { errors }, setValue, watch } = useForm<FormData>({
    resolver: zodResolver(schema),
  })

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <Field.Root>
        <Field.Label className="text-sm font-medium">Name</Field.Label>
        <input {...register('name')} className="mt-1 h-10 w-full rounded-lg border px-3 text-sm" />
        {errors.name && <p className="mt-1 text-xs text-red-500">{errors.name.message}</p>}
      </Field.Root>

      <Field.Root>
        <Field.Label className="text-sm font-medium">Email</Field.Label>
        <input {...register('email')} type="email" className="mt-1 h-10 w-full rounded-lg border px-3 text-sm" />
        {errors.email && <p className="mt-1 text-xs text-red-500">{errors.email.message}</p>}
      </Field.Root>

      <button type="submit" className="h-10 px-6 rounded-lg bg-primary text-white font-medium hover:bg-primary/90">
        Submit
      </button>
    </form>
  )
}
```

## Key Differences from Radix-based shadcn

1. **Imports**: Always `@base-ui-components/react/<component>` (or `@base-ui/react/<component>`) instead of `@radix-ui/react-<component>`
2. **API**: Base UI uses `Popup` instead of `Content`, `Positioner` for positioning
3. **Styling**: Base UI uses `data-[state]` attributes (`data-[checked]`, `data-[highlighted]`, `data-[popup-open]`), styled with Tailwind + `clsx`
4. **No Provider wrappers** needed for most components (simpler API)
5. **Extra components**: Base UI has `NumberField`, `PreviewCard`, `Field`, `Fieldset`, `Autocomplete` that Radix doesn't
6. **Composition**: Use `render` prop to compose multiple Base UI components together
7. **Type-safe Props**: All sub-components export `.Props` types (e.g. `Select.Trigger.Props`)
8. **Context7 Docs**: For latest Base UI docs, query `https://context7.com/api/v1/websites/base-ui_react?tokens=10000&topic=<component-name>`

## Official Component List (base-ui.com)
Accordion, AlertDialog, Autocomplete, Checkbox, Collapsible, Dialog, Field, Fieldset, Menu, NumberField, Popover, PreviewCard, Progress, Radio, RadioGroup, ScrollArea, Select, Separator, Slider, Switch, Tabs, Toggle, ToggleGroup, Toolbar, Tooltip, Toast
