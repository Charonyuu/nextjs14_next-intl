# Next.js 14 使用 `next-intl` 進行國際化配置

### 項目結構

```bash
project-root/
│
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   └── ... (其他页面和组件)
│
├── intl/
│   ├── en.json
│   ├── zh.json
│   └── ... (其他语言文件)
│
├── i18n.ts
└── next.config.mjs
```

## 配置 next-intl 插件

### 在 next.config.mjs 中添加 createNextIntlPlugin：

```

import createNextIntlPlugin from "next-intl/plugin";

const withNextIntl = createNextIntlPlugin();

/** @type {import('next').NextConfig} */
const nextConfig = {};

export default withNextIntl(nextConfig);

```

## 建立 i18n.ts 檔案

### 在 app 目錄的同級目錄下添加 i18n.ts：

```tsx
// i18n.ts
import { getRequestConfig } from "next-intl/server";

export default getRequestConfig(async () => {
  // Provide a static locale, fetch a user setting,
  // read from `cookies()`, `headers()`, etc.
  const locale = "en";

  return {
    locale,
    messages: (await import(`./intl/${locale}.json`)).default,
  };
});
```

## 在 layout.tsx 中包裝 NextIntlClientProvider

### 在 app/layout.tsx 中使用 NextIntlClientProvider 包裝子元件：

```tsx
import { NextIntlClientProvider } from "next-intl";
import { getLocale, getMessages } from "next-intl/server";

export default async function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const locale = await getLocale();

  // Providing all messages to the client
  // side is the easiest way to get started
  const messages = await getMessages();

  return (
    <html lang={locale}>
      <body>
        <NextIntlClientProvider messages={messages}>
          {children}
        </NextIntlClientProvider>
      </body>
    </html>
  );
}
```

## 翻譯取用方法 (./intl/en.json)

### 扁平式翻譯

```json
{
  "title": "Hello world!"
}
```

#### 客戶端使用 useTranslations 獲取翻譯

```jsx
// Client Side Get intl
import { useTranslations } from "next-intl";

export default function HomePage() {
  const t = useTranslations("HomePage");
  return <h1>{t("title")}</h1>;
}
```

#### 服務器端使用 getTranslations 獲取翻譯

```tsx
// Server Side Get intl
import { getTranslations } from "next-intl/server";
export default async function HomePage() {
  const t = await getTranslations();
  return <h1>{t("title")}</h1>;
}
```

### 分層式翻譯(預設推薦)

```json
{
  "HomePage": {
    "title": "Hello world!"
  }
}
```

#### 客戶端使用 useTranslations 獲取翻譯

```tsx
// Client Side Get intl
import { useTranslations } from "next-intl";

export default function HomePage() {
  const t = useTranslations("HomePage");
  return <h1>{t("title")}</h1>;
}
```

#### 服務器端使用 getTranslations 獲取翻譯

```tsx
// Server Side Get intl
import { getTranslations } from "next-intl/server";
export default async function HomePage() {
  const t = await getTranslations("HomePage");
  return <h1>{t("title")}</h1>;
}
```
