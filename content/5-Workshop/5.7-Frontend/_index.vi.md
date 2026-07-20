---
title: "5.7 Frontend & URL Masking"
date: 2026-07-10
weight: 7
chapter: false
---

# 5.7 Cấu hình Next.js Frontend, Deploy Vercel & Bảo mật URL Masking

Phần này hướng dẫn các bước kết nối giao diện Next.js với Backend API, giải pháp an ninh bảo mật che dấu địa chỉ URL nhạy cảm phía Client (URL Masking) và quy trình deploy tự động lên nền tảng Vercel Hosting.

---

### 1. Kết nối Frontend gọi APIs & Giải pháp cách ly phiên (Session Isolation)

Khi người dùng đăng nhập thành công, máy chủ Backend sẽ cấp một mã xác thực **JWT (JSON Web Token)** để sử dụng cho các yêu cầu lấy hoặc ghi dữ liệu tiếp theo.

> [!IMPORTANT]
> **Tại sao dùng `sessionStorage` thay vì `localStorage`?**
> *   *Nguy cơ từ máy tính công cộng:* Trong môi trường ký túc xá học đường, phần lớn sinh viên đăng nhập tài khoản trên các máy tính dùng chung (tại thư viện, phòng máy trường học, quán internet).
> *   *Lỗ hổng của `localStorage`:* Dữ liệu lưu trong `localStorage` sẽ ghi vĩnh viễn trên ổ đĩa cứng của trình duyệt. Nếu sinh viên quên không nhấn Đăng xuất (Logout) và tắt trình duyệt đi, người sử dụng máy tiếp theo vẫn có thể khôi phục lại JWT Token và truy cập bất hợp pháp vào hồ sơ cá nhân.
> *   *Giải pháp với `sessionStorage`:* Dữ liệu chỉ tồn tại tạm thời trong phiên làm việc của Tab trình duyệt đó. Ngay khi sinh viên đóng Tab hoặc tắt trình duyệt, phiên làm việc sẽ lập tức bị hủy bỏ hoàn toàn (Session Isolation), bảo vệ an toàn định danh tối đa.

Mã nguồn hàm gọi fetch API tích hợp JWT gửi kèm theo Header Authorization:

```typescript
// Giao tiếp với API Backend lấy thông tin danh sách phòng
export async function getRooms(status?: string) {
  // Lấy token xác thực từ sessionStorage để đảm bảo an toàn cách ly phiên
  const token = sessionStorage.getItem("token");
  const query = status ? `?status=${status}` : "";
  
  const res = await fetch(`https://your-api-gateway.amazonaws.com/api/rooms${query}`, {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${token}` // Gửi kèm token trong Header
    }
  });
  
  if (!res.ok) {
    throw new Error("Không thể truy xuất dữ liệu phòng từ máy chủ API");
  }
  
  return res.json();
}
```

---

### 2. Triển khai kỹ thuật bảo mật URL Masking phía Client-side

Kỹ thuật **URL Masking (Che giấu URL)** là một chốt chặn an ninh quan trọng ở tầng giao diện người dùng. Nó giúp ẩn các thông số định danh hoặc các đường dẫn trang quản trị nhạy cảm khỏi việc quét URL thủ công (URL Manipulation) từ những kẻ tấn công dò tìm lỗ hổng.

#### Cơ chế hoạt động của Giải pháp bảo mật:
1.  **Đăng nhập & Cấp chữ ký số:** Khi quản trị viên đăng nhập thành công, họ được dẫn hướng đến trang quản trị kèm theo một tham số chữ ký số ngắn hạn được mã hóa cứng trên URL: `/owner?sig=9b81d8d4ec0b4d5a9d8c919d3f`.
2.  **Kiểm tra chữ ký (Signature Check):** Một Component Layout bảo mật của Next.js (`SecurityLayout`) được cấu hình để bao bọc toàn bộ các trang con nhạy cảm. Component này sẽ bắt lấy tham số `sig` trên URL để đối soát. Nếu chữ ký không trùng khớp hoặc bị thiếu, hệ thống lập tức từ chối hiển thị và đẩy người dùng về trang lỗi **403 Forbidden**.
3.  **Che giấu dấu vết (Masking URL):** Nếu chữ ký số trùng khớp hoàn toàn, hệ thống thực thi hàm `window.history.replaceState` để dọn dẹp sạch sẽ các tham số truy vấn trên thanh địa chỉ, đưa URL trở về định dạng sạch `/owner`. Tin tặc đứng cạnh màn hình hoặc xem lịch sử trình duyệt hoàn toàn không thể biết được chữ ký số hợp lệ là gì.

Tạo tệp cấu hình bảo mật layout tại [frontend/app/owner/layout.tsx](file:///m:/SmartDorm/frontend/app/owner/layout.tsx):

```typescript
"use client";

import { useEffect } from "react";
import { useRouter, usePathname, useSearchParams } from "next/navigation";

export default function SecurityLayout({ children }: { children: React.ReactNode }) {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSearchParams();

  useEffect(() => {
    // Chỉ kích hoạt bộ lọc bảo mật trên các trang thuộc quyền quản trị của chủ nhà/admin
    if (pathname.startsWith("/owner") || pathname.startsWith("/admin")) {
      const sig = searchParams.get("sig");
      const expectedSig = "9b81d8d4ec0b4d5a9d8c919d3f"; // Chữ ký bảo mật được đối soát mẫu
      
      if (sig !== expectedSig) {
        // Chặn đứng truy cập bất hợp pháp và điều hướng về trang lỗi bảo mật
        router.push("/403");
      } else {
        // URL MASKING: Dọn sạch thanh địa chỉ trình duyệt, ẩn signature đi
        // Địa chỉ trình duyệt sẽ hiển thị sạch sẽ dạng: https://smartdorm.vercel.app/owner
        window.history.replaceState(null, "", pathname);
      }
    }
  }, [pathname, searchParams, router]);

  return <>{children}</>;
}
```

---

### 3. Quy trình triển khai Next.js Frontend lên Vercel Hosting

Vercel là nền tảng tối ưu nhất hiện nay để chạy các ứng dụng Next.js nhờ cơ chế build thông minh và cấp phát chứng chỉ bảo mật SSL/HTTPS tự động miễn phí.

*   **Bước 1: Lưu trữ mã nguồn:** Đẩy toàn bộ dự án chứa mã nguồn SmartDorm lên tài khoản **GitHub Repository** cá nhân của bạn.
*   **Bước 2: Tạo dự án trên Vercel:** Truy cập [Vercel](https://vercel.com/), đăng nhập và chọn **Add New Project**. Tiến hành cấp quyền liên kết tới GitHub của bạn và chọn Import repository chứa mã nguồn SmartDorm.
*   **Bước 3: Lựa chọn thư mục con:** Lựa chọn thư mục chạy Frontend là thư mục con `frontend/` trong cấu trúc dự án.
*   **Bước 4: Cấu hình biến môi trường:** Tại giao diện cấu hình build, thiết lập các trường sau:
    *   *Build Command:* `next build`
    *   *Output Directory:* `.next`
    *   *Environment Variables:* Thêm biến môi trường `NEXT_PUBLIC_API_URL` và gán giá trị là URL Endpoint của AWS Lambda (được xuất ra sau khi bạn chạy xong lệnh `terraform apply` ở bước 5.3 hoặc 5.5).
*   **Bước 5: Kích hoạt Deploy:** Nhấp chọn nút **Deploy**. Hệ thống Vercel sẽ tự động tải các package thư viện, tiến hành tối ưu hóa mã nguồn tĩnh Next.js và cấp phát một URL HTTPS công khai miễn phí để bạn truy cập ứng dụng trực tiếp trên internet.

---

### 4. Thông tin tài khoản thử nghiệm hệ thống (Demo Credentials)

Sau khi hệ thống được triển khai trực tuyến thành công, bạn có thể thực hiện kiểm thử chức năng và luồng nghiệp vụ tại địa chỉ web demo của dự án [https://smartdorm-orpin.vercel.app/](https://smartdorm-orpin.vercel.app/) thông qua hai dải quyền tài khoản mặc định dưới đây:

*   **Tài khoản Chủ nhà/Quản trị viên (Admin/Owner):**
    *   **Tên đăng nhập (TK):** `chithanh_admin`
    *   **Mật khẩu (MK):** `ThanhPlus2026_Secure@Admin_!#`
*   **Tài khoản Người thuê/Sinh viên (Tenant/User):**
    *   **Tên đăng nhập (TK):** `chithanh123`
    *   **Mật khẩu (MK):** `Chithanh123@`
