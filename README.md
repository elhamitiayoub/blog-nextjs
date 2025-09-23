# 🚀 **BlogHamiti - Next.js 15 Blog Application**

## 📝 **1. Project Overview**

The **BlogHamiti** project is a full-stack blog application built with **Next.js 15** and **Prisma ORM**, featuring:

- 🖥 **Frontend:** Next.js 15 with the **App Router** and **Tailwind CSS**.
- 🗄 **Backend:** Prisma ORM connected to a **PostgreSQL** database.
- 🎨 **UI Components:** ShadCN UI for modern, reusable elements.
- 🎯 **Goal:** Build a dynamic blog platform where users can **view posts**, and later **add authentication** and **post creation** features.

---

## ⚙️ **2. Initial Project Setup**

### 📦 **Dependencies Installed**

```bash
npm install next@latest react react-dom
npm install tailwindcss prisma @prisma/client
npm install @shadcn/ui

```

---

## 🗄 **3. Prisma Configuration**

### 📝 **schema.prisma**

The `schema.prisma` file defines the database configuration and structure.

```
generator client {
  provider = "prisma-client-js"
  output   = "../lib/generated/prisma"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model BlogPost {
  id          String   @id @default(uuid())
  title       String
  content     String
  imageURL    String
  authorId    String
  authorName  String
  authorImage String

  createAt    DateTime @default(now())
  upddateAt   DateTime @updatedAt
}

```

---

### 📊 **Field Explanation**

| 🏷 Field | 🔗 Type | 📝 Description |
| --- | --- | --- |
| `id` | String | Unique ID generated automatically (`UUID`). |
| `title` | String | Blog post title. |
| `content` | String | Main content of the post. |
| `imageURL` | String | URL for the post's main image. |
| `authorId` | String | ID of the author. |
| `authorName` | String | Name of the author. |
| `authorImage` | String | Profile picture of the author. |
| `createAt` | DateTime | Timestamp when the post is created. |
| `upddateAt` | DateTime | Automatically updates when post is edited. |

---

### 🛠 **Prisma Commands**

1. **Generate Prisma Client:**
    
    ```bash
    npx prisma generate
    ```
    
2. **Run Database Migration:**
    
    ```bash
    npx prisma migrate dev --name init
    ```
    
3. **Open Prisma Studio:**
    
    ```bash
    npx prisma studio
    ```
    
    - Access Prisma Studio at 🌍 [**http://localhost:5555**](http://localhost:5555/)

---

## 🔌 **4. Backend Integration**

To make Prisma accessible across the project, a utility file was added:

**📁 File:** `app/utils/db.ts`

```tsx
import { PrismaClient } from "@prisma/client";

export const prisma = new PrismaClient();
```

---

## ⚡ **5. Fetching Blog Posts in Next.js**

The **Home page** dynamically fetches blog posts from Prisma and displays them.

**📁 File:** `app/page.tsx`

```tsx
import { prisma } from "./utils/db";

async function getData() {
  const data = await prisma.blogPost.findMany({
    select: {
      title: true,
      content: true,
      imageURL: true,
      authorImage: true,
      authorName: true,
      id: true,
      createAt: true,
    }
  });
  return data;
}

export default async function Home() {
  const data = await getData();
  return (
    <div className="py-6">
      <h1 className="text-3xl font-bold tracking-tight mb-8">Latest Posts</h1>

      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
        {data.map((item) => (
          <h1 key={item.id}>{item.title}</h1>
        ))}
      </div>
    </div>
  );
}
```

---

### 🔍 **How It Works**

- `getData()` retrieves blog posts from the database.
- `select` is used to **fetch only required fields** for performance.
- The `Home` component displays each post's **title** in a responsive grid layout.

---

## 🎨 **6. Frontend Progress**

### 🌐 Navbar

- Built with **Tailwind CSS** + **ShadCN** buttons.
- Contains:
    - **Logo:** BlogHamiti 📰
    - **Navigation Links:** `Home` and `Dashboard`
    - **Buttons:** `Login` & `Sign Up`

**Preview:**

---

### 🏠 Home Page Output

Two sample posts are displayed dynamically from Prisma Studio:

| 📝 Title | 📖 Content |
| --- | --- |
| xfb | dfb |
| dvf | fnfg |

**Preview:**

---

## 🗺 **7. Current Folder Structure**

```
blog-hamiti/
│
├── app/
│   ├── page.tsx
│   ├── utils/
│   │   └── db.ts
│   └── components/
│
├── prisma/
│   └── schema.prisma
│
├── lib/
│   └── generated/
│       └── prisma/
│
├── public/
│
└── tailwind.config.js

```

---

## 🔮 **8. Next Steps**

Here’s what you’ll work on next:

1. 🖼 **Create Blog Card Component**
    - Display:
        - Post **image**
        - **Title**
        - **Author details**
        - **Creation date**
2. 🌍 **Dynamic Blog Routing**
    - Add `[id]/page.tsx` to display **individual blog posts**.
3. 👤 **Improve Database Structure**
    - Create a separate `User` model for managing authors.
4. 🔐 **Authentication**
    - Integrate **NextAuth.js** for user login and sign-up.

---

## 🏁 **Summary**

- ✅ **Prisma** configured and database connected.
- ✅ **Tailwind CSS** set up with ShadCN components.
- ✅ **Navbar** and basic UI implemented.
- ✅ Blog posts successfully fetched and displayed dynamically.
- 🚧 **Next milestone:** Build **blog card component** and dynamic routes.

# 🧑‍💻 **Authentication System Documentation**

## **Overview**

We implemented a secure **authentication system** in our Next.js blog application using **Kinde**.

The system allows users to **register**, **log in**, and **log out**, while dynamically updating the navigation bar based on the user's authentication state.

---

## **Features Implemented**

- **🔐 User Authentication** with Kinde:
    - `Sign Up` to create a new account.
    - `Login` to access the application.
    - `Logout` to securely end the session.
- **Dynamic Navbar**:
    - Shows **Login** and **Sign Up** buttons when the user is **not authenticated**.
    - Displays the **user's name** and a **Logout** button when the user is **logged in**.

---

## **How It Works**

1. **Session Management**
    
    ```tsx
    const { getUser } = getKindeServerSession();
    const user = await getUser();
    ```
    
    - `getKindeServerSession()` securely retrieves the current **user session** on the **server side**.
    - `getUser()` returns:
        - The logged-in user's data (`id`, `given_name`, `email`, etc.).
        - `null` if no user is logged in.

---

1. **Navbar Behavior**
    - When `user` exists → Display **user's first name** and `Logout` button.
    - When `user` is `null` → Display `Login` and `Sign Up` buttons.
    
    ```tsx
    {user ? (
      <div className="flex items-center gap-4">
        <p>{user.given_name}</p>
        <LogoutLink>Logout</LogoutLink>
      </div>
    ) : (
      <div className="flex items-center gap-4">
        <LoginLink>Login</LoginLink>
        <RegisterLink>Sign Up</RegisterLink>
      </div>
    )}
    ```
    

---

## **User Flow**

```
[User visits site]
       ↓
If user is NOT logged in → Show Login & Sign Up buttons
       ↓
User logs in or registers via Kinde
       ↓
Session is created on the server
       ↓
Navbar updates → Shows User's Name + Logout button
       ↓
User clicks Logout → Session ends → Back to Login & Sign Up buttons

```

---

## **Result in the UI**

- **Before Login:**
    
    ```
    [Login] [Sign Up]
    ```
    
- **After Login (e.g., Ayoub):**
    
    ```
    Ayoub 👋 [Logout]
    ```
    

---

## **Why This is Secure**

- All session handling occurs **server-side**, preventing exposure of sensitive tokens.
- The UI automatically adapts based on the authenticated session.
- Users cannot access protected routes without logging in.
