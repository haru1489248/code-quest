# Coding Guidelines

---

## General

### **Avoid `export default`, Use Named Exports with Arrow Functions**

**Rule**: Do not use `export default`. Instead, use named exports and arrow functions for defining components.

**Bad Example**
```tsx
// ❌ Bad: Using default export and function declaration
function ClientSidebar() {
   return <div>example</div>
}

export default ClientSidebar

// ❌ Bad: Importing default export
import ClientSidebar from '~/components/clients/ClientSidebar'
```

**Good Example**
```tsx
// ✅ Good: Using named export with arrow function
export const ClientSidebar = () => {
   return <div>example</div>
}

// ✅ Good: Importing named export
import { ClientSidebar } from '~/components/clients/ClientSidebar'
```

---

### **Use `React.useState`, `React.useEffect`, etc. Instead of Importing `useState`, `useEffect`, etc.**

Always use `React.useState`, `React.useEffect`, and other React hooks directly from the `React` object, instead of importing them individually.

**Example**
```tsx
// ❌ Bad: Using default export and function declaration
import { useState } from "react";

const [email, setEmail] = useState("");
const [password, setPassword] = useState("");

// ✅ Good: Using named export with React object
import React from "react";

const [email, setEmail] = React.useState("");
const [password, setPassword] = React.useState("");
```

---

## Typing

### **Do Not Ignore Type Safety**

- Do not bypass TypeScript's type system
- Avoid using `any`
- Never use `@ts-expect-error` or `@ts-ignore`

**Bad Examples**
```tsx
// ❌ Bad: Ignoring TypeScript with @ts-expect-error
<ClientBasicInfo
  // @ts-expect-error: Fix later
  licenses={client?.company?.licenses || []}
/>

// ❌ Bad: Using `any` in filters and maps
queryParams.client_type_ids
  ? clientTypes
      // eslint-disable-next-line @typescript-eslint/no-explicit-any
      .filter((type: any) =>
        queryParams.client_type_ids.includes(type.id),
      )
      // eslint-disable-next-line @typescript-eslint/no-explicit-any
      .map((type: any) => type.name)
  : [],
```

---

### **Use `type` Instead of `interface`**

**Bad Example**
```tsx
// ❌ Bad: Using `interface`
interface Client {
  id: number
  name: string
  licenses?: string[]
}
```

**Good Example**
```tsx
// ✅ Good: Using `type`
type Client = {
  id: number
  name: string
  licenses?: string[]
}
```

---

### **Prefer Inline Type Definitions**

For component props, use **inline type definitions** rather than defining types separately. This approach improves readability by keeping type definitions within the component's context, making them immediately understandable.

**Good Example: Using Inline Type Definitions**
```tsx
// ✅ Good: インラインで型定義
export function ClientContentsHeader({
  companyName,
  isMonitored,
  clientCreator,
  createdAt,
  currentUserId,
}: {
  companyName: string;
  isMonitored: boolean;
  clientCreator: string;
  createdAt: Date;
  currentUserId?: string;
}) {
  // Function body
}

// ❌ Bad: 型を別途定義
type ClientContentsHeaderProps = {
  companyName: string;
  isMonitored: boolean;
  clientCreator: string;
  createdAt: Date;
  currentUserId?: string;
};

export function ClientContentsHeader({
  companyName,
  isMonitored,
  clientCreator,
  createdAt,
  currentUserId,
}: ClientContentsHeaderProps) {
  // Function body
}
```

**🚨 Exception**

For highly complex props or types that are reused in multiple places, defining and reusing a separate type is allowed. In such cases, ensure that the type name is meaningful and descriptive.

---

## Entities

### Organizing Entity with `actions`, `hooks`, `types`, and `utils`

When working with an entity in your application, it's helpful to organize the related functionality into specific folders to keep the codebase clean and maintainable. Below is a typical folder structure for an entity (in this case, a `supplier` entity):

```
supplier
├── actions
│   └── supplier.actions.ts
├── hooks
│   └── use-supplier.ts
├── types
│   └── supplier.type.ts
└── utils
    └── supplier.util.ts
```

Each folder serves a distinct purpose:

- **`actions`**: Functions that perform database CRUD operations.
- **`hooks`**: Custom hooks that wrap the logic from actions, usually for use with React Query.
- **`types`**: Type definitions related to the entity.
- **`utils`**: Helper functions that support the entity but don't directly interact with the database.

---

### **`actions` Folder: Database CRUD Operations**

In the `actions` folder, we define functions that involve interacting with the database, such as Create, Read, Update, and Delete (CRUD) operations. These functions often use a pattern to handle business logic and database transactions.

**Create Example:**
```tsx
export const createMasterSupplier = async ({
  name, address, telephoneNumber, faxNumber, email, website, memo
}: {
  name: string;
  address: string;
  telephoneNumber: string;
  faxNumber: string;
  email: string;
  website: string;
  memo: string
}) => {
  return await modelAction({
    actionType: 'CREATE',
    entity: 'MASTER_SUPPLIER',
    successMessage: 'Master Supplier successfully added',
    action: async (session, tx) => {
      const createdSupplier = await tx.masterSupplier.create({
        data: {
          name,
          address,
          telephoneNumber,
          faxNumber,
          email,
          website,
          memo
        }
      });

      return {
        entityId: createdSupplier.id,
        oldValue: null,
        newValue: { ...createdSupplier }
      };
    },
  });
};
```

**Explanation**:
- `modelAction` is a utility function that ensures database transactions are handled properly and confirms that the user is authenticated.
- This action creates a new supplier in the database.

**Read Example:**
```tsx
export const fetchItemLatestId = async () => {
  return await modelView({
    action: async () => {
      const latestItem = await prisma.item.findFirst({
        orderBy: { id: 'desc' },
        select: { id: true }
      });
      return latestItem?.id || 0;
    },
  });
};
```

**Explanation**:
- `modelView` is a utility function that handles read operations and checks if the user is logged in.

---

### **`hooks` Folder: Wrapping Actions with React Query**

The `hooks` folder contains custom hooks that abstract away the logic from actions and make it easier to interact with the backend in a declarative way. Hooks use React Query's `useQuery` for fetching data and `useMutation` for sending data.

**Using `useQuery` for Fetching Data:**
```tsx
import { useQuery } from '@tanstack/react-query';
import { fetchMasterSupplieries } from '../actions/supplier.actions';

export const useMasterSuppliers = ({
  params,
  enabled
}: {
  params: { name?: string };
  enabled?: boolean
}) => {
  return useQuery({
    queryKey: ['master-suppliers', params],
    queryFn: async () => {
      const res = await fetchMasterSupplieries(params);
      return res;
    },
    enabled,
  });
};
```

- `useMasterSuppliers` is a custom hook that fetches the list of master suppliers using `useQuery`.
- The `enabled` flag can be used to control when the query is triggered.

**Using `useMutation` for Performing Actions:**
```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { toast } from 'react-toastify';
import { createMasterSupplier } from '../actions/supplier.actions';

export const useCreateMasterSupplier = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: async (params: {
      name: string;
      address: string;
      telephoneNumber: string;
      faxNumber: string;
      email: string;
      website: string;
      memo: string;
    }) => {
      const res = await createMasterSupplier(params);
      return res;
    },
    onSuccess: (res) => {
      queryClient.invalidateQueries({ queryKey: ['master-suppliers'] });
      if (res.status === 'success') {
        toast.success(res.message);
      } else {
        toast.error(res.message);
      }
    },
    onError: () => {
      toast.error('Failed to create Master Supplier.');
    },
  });
};
```

- `useCreateMasterSupplier` is a custom hook that wraps the `createMasterSupplier` action and provides status handling.
- On success, it invalidates the `master-suppliers` query to trigger a refetch of the suppliers list.
- It uses `react-toastify` to show success or error messages to the user.

---

### **`types` Folder: Defining Types**

The `types` folder contains TypeScript types related to the entity. These types define the shape of data that is passed around in your application and help TypeScript catch errors at compile time.

```tsx
import { MasterSupplier, Purchase } from '@prisma/client'

export type MasterSupplierWithRels = MasterSupplier & {
    purchase: Purchase[]
};
```

- `Supplier` is a TypeScript type that defines the shape of a supplier object.
- Using clear types like this makes it easier to work with the data and provides type safety.

---

### **`utils` Folder: Utility Functions**

The `utils` folder contains helper functions that don't directly interact with the database but assist with various other tasks, like formatting or validating data.

```tsx
export const formatSupplierName = (name: string) => {
  return name.toUpperCase();
};
```

- `formatSupplierName` is a simple utility function that formats the supplier's name to uppercase.
- Utility functions like this help keep your code DRY (Don't Repeat Yourself) and more modular.

---

## Style

### **Use Tailwind, Avoid CSS**

Tailwind is a powerful utility-first CSS framework that promotes consistency in spacing, colors, and more. Use Tailwind to build your application and avoid writing custom CSS whenever possible. Inline styles should also be avoided to maintain a consistent and scalable design.

**Example**
```tsx
// ❌ Bad: Using Inline Style
<div style={{ margin: '10px', background: 'white' }}>This is an Element</div>

// ❌ Bad: Using .css
// app.css
.element {
  margin: 10px;
  background: white;
}

// ✅ Good: Using Tailwind
<div className="m-4 bg-white">This is an Element</div>
```

---

## App File Structure

**Rule**

Next.js App Router has a defined convention for file placement: it expects files to follow the pattern `[path]/page.tsx`. In this application, we aim to keep components close to the pages that use them. Therefore, components used on a specific page should be placed in the `[path]/_components/` directory.

**Example**
```
app/private/_components/Sidebar.tsx
```

---

## Components File Structure

**Rule**

We use a component-based structure following the principles of **atoms**, and **molecules** to organize the components.

- **Atoms**: These are the smallest, most fundamental building blocks of the UI, such as buttons, inputs, labels, or icons. They are typically single-purpose components with minimal logic or styling.
- **Molecules**: These are combinations of atoms that form more complex UI elements. They represent small, reusable components that can be shared across the application, such as forms, modals, or cards.

**Example**
```
├── atom
│   ├── DatePicker.tsx
│   ├── Loading.tsx
└── molecules
    ├── BarcodeModal
    │   ├── BarcodeModal.tsx
    │   └── index.ts
    ├── BasicListPage
    │   ├── BasicListPage.tsx
    │   └── index.ts
    ├── ChangeStatusDialog
    │   └── ChangeStatusDialog.tsx
    ├── CsvImportModal
    │   ├── CsvImportModal.tsx
    │   └── index.ts
    ├── DeleteDialog
    │   ├── DeleteDialog.tsx
    │   └── index.ts
    ├── EntityWriteModal
    │   ├── EntityWriteModal.tsx
    │   ├── EntityWriteModalForm.tsx
    │   ├── EntityWriteModalMultiSelect.tsx
    │   └── index.ts
    ├── FilterGroupBox
    │   └── FilterGroupBox.tsx
    ├── FlowStatusNode
    │   └── FlowStatusNode.tsx
    ├── Header
    │   ├── Header.tsx
    │   └── index.ts
    ├── InputLabel
    │   └── InputLabel.tsx
```