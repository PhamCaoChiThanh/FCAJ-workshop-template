---
title: "5.7 Frontend & URL Masking"
date: 2026-07-10
weight: 7
chapter: false
---

## 5.7 Next.js Frontend Configuration, Vercel Deployment & URL Masking Security

This section covers connecting the Next.js frontend with our AWS backend, implementing client-side URL Masking security, and deploying the client application to Vercel.

---

### 1. Secure API Calls Using JWT Token

To safeguard administrative actions and tenant datasets, all core SmartDorm APIs enforce JSON Web Token (JWT) verification.
*   **Why use `sessionStorage` over `localStorage`?** 
    *   In shared computing environments (such as university library kiosks or computer labs), utilizing `localStorage` poses severe security risks, as the token resides permanently on the browser storage until manually cleared.
    *   By shifting authentication tokens into `sessionStorage`, tokens are automatically destroyed as soon as the student closes the browser tab, preventing session hijacking.

The snippet below demonstrates retrieving the JWT from `sessionStorage` and appending it to the Authorization header of outbound HTTP requests:

```typescript
// Fetch Room list from the Backend Web API
export async function getRooms(status?: string) {
  // Retrieve token stored within the tab session
  const token = sessionStorage.getItem("token");
  const query = status ? `?status=${status}` : "";
  
  const res = await fetch(`https://your-api-gateway.amazonaws.com/api/rooms${query}`, {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${token}` // Inject Authorization Header
    }
  });
  
  if (!res.ok) {
    throw new Error("Failed to load rooms info");
  }
  
  return res.json();
}
```

---

### 2. Implementing Client-side URL Masking Security

**URL Masking** intercepts navigation requests and cleanses tracking query parameters from the browser address bar, stopping unauthorized users from manually typing and scanning routes.

#### Workflow:
1.  Upon successful authentication, the administrator is redirected to `/owner?sig=9b81d8d4ec0b4d5a9d8c919d3f` containing a temporary digital signature.
2.  A security wrapper layout (`SecurityLayout`) intercepts the route and reads the `sig` parameter.
3.  If the signature is missing or fails verification, the client is redirected to a custom **403 Forbidden** page.
4.  If verified, the layout calls `window.history.replaceState` to clear the URL query parameters, rendering a clean `/owner` path.

Create this file at [frontend/app/owner/layout.tsx](file:///m:/SmartDorm/frontend/app/owner/layout.tsx):

```typescript
"use client";

import { useEffect } from "react";
import { useRouter, usePathname, useSearchParams } from "next/navigation";

export default function SecurityLayout({ children }: { children: React.ReactNode }) {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSearchParams();

  useEffect(() => {
    // Intercept administrative routes
    if (pathname.startsWith("/owner") || pathname.startsWith("/admin")) {
      const sig = searchParams.get("sig");
      const expectedSig = "9b81d8d4ec0b4d5a9d8c919d3f"; // Demo signature hash
      
      if (sig !== expectedSig) {
        // Halt navigation and redirect to 403 Forbidden
        router.push("/403");
      } else {
        // URL MASKING: Rewrite address bar back to clean path
        // Browser displays: https://smartdorm.vercel.app/owner
        window.history.replaceState(null, "", pathname);
      }
    }
  }, [pathname, searchParams, router]);

  return <>{children}</>;
}
```

---

### 3. Deploying Next.js Frontend to Vercel Hosting

Vercel is the recommended serverless hosting provider for Next.js applications:

*   **Step 1:** Push the SmartDorm codebase to a personal **GitHub Repository**.
*   **Step 2:** Log into the [Vercel Dashboard](https://vercel.com/) and click **Add New Project**, importing your GitHub repository.
*   **Step 3:** Set the root directory of the build to the `frontend` subfolder.
*   **Step 4:** Configure the project build options:
    *   *Framework Preset:* Next.js
    *   *Build Command:* `next build`
    *   *Output Directory:* `.next`
    *   *Environment Variables:* Add `NEXT_PUBLIC_API_URL` pointing to the public endpoint (Function URL or API Gateway URL) outputted by Terraform.
*   **Step 5:** Click **Deploy**. Vercel will build the frontend and provide a secure HTTPS public URL.

---

### 4. Sandbox Demo Credentials

Use the following credentials to access the live sandbox environment at [https://smartdorm-orpin.vercel.app/](https://smartdorm-orpin.vercel.app/):

*   **Owner / Administrator Account:**
    *   *Username:* `chithanh_admin`
    *   *Password:* `ThanhPlus2026_Secure@Admin_!#`
*   **Student / Tenant Account:**
    *   *Username:* `chithanh123`
    *   *Password:* `Chithanh123@`
