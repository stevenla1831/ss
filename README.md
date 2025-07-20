# ss/**
 * ScanAddFriend.jsx (v2) – Auto‑scan on load
 *
 * A minimal LIFF v2 React component that immediately triggers QR‑code scanning
 * without requiring the user to press a button.  After a successful scan, the
 * page navigates to the scanned URL (use your LINE Add‑Friend link in the QR).
 *
 * ✨ How to use
 * 1. Keep the <script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
 *    in index.html (or equivalent).
 * 2. Replace BOTH placeholders below:
 *      ‑ YOUR_LIFF_ID  → your actual LIFF ID
 *      ‑ @YourID        → your LINE OA ID (for fallback redirect)
 * 3. Deploy to a HTTPS endpoint and register that URL in your LIFF settings.
 */

import { useEffect, useState } from "react";

export default function ScanAddFriend() {
  const [msg, setMsg] = useState("啟動掃碼中…");

  useEffect(() => {
    const run = async () => {
      try {
        // 1️⃣ 初始化 LIFF
        await liff.init({ liffId: "YOUR_LIFF_ID" });

        // 2️⃣ 如果不是在 LINE 內開啟，直接導到加好友頁
        if (!liff.isInClient()) {
          window.location.href = "https://line.me/R/ti/p/@YourID";
          return;
        }

        // 3️⃣ 立即呼叫掃描器 (scanCodeV2 會開啟相機)
        const result = await liff.scanCodeV2();

        // 4️⃣ 掃碼成功 → 導向掃描到的網址
        if (result?.value) {
          window.location.href = result.value;
        } else {
          setMsg("未讀取到 QR 內容，請重新掃描。\n請關閉頁面並再嘗試。");
        }
      } catch (err) {
        console.error(err);
        setMsg(`發生錯誤：${err.message}`);
      }
    };
    run();
  }, []);

  return (
    <div className="flex items-center justify-center h-screen bg-gray-100 p-4 text-center whitespace-pre-line">
      <span className="text-base text-gray-600 animate-pulse">{msg}</span>
    </div>
  );
}
