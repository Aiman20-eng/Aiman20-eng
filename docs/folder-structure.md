# هيكل المجلدات — AI Context Builder

## 1. الهيكل المقترح

```text
src/
├── assets/
│   ├── icons/
│   └── styles/
│
├── components/
│   ├── ui/
│   │   ├── Button.tsx
│   │   ├── Card.tsx
│   │   ├── Input.tsx
│   │   ├── Textarea.tsx
│   │   ├── Modal.tsx
│   │   └── EmptyState.tsx
│   └── layout/
│       ├── AppHeader.tsx
│       └── SectionTitle.tsx
│
├── features/
│   ├── workspaces/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types.ts
│   │
│   ├── context-builder/
│   │   ├── components/
│   │   ├── services/
│   │   └── types.ts
│   │
│   ├── templates/
│   │   ├── data/
│   │   ├── services/
│   │   └── types.ts
│   │
│   ├── import-export/
│   │   ├── components/
│   │   ├── services/
│   │   └── types.ts
│   │
│   └── insert-context/
│       ├── adapters/
│       ├── components/
│       ├── services/
│       └── types.ts
│
├── hooks/
│   ├── useDebouncedValue.ts
│   └── useToast.ts
│
├── services/
│   ├── messageService.ts
│   └── platformDetectorService.ts
│
├── storage/
│   ├── indexedDbClient.ts
│   ├── workspaceRepository.ts
│   ├── settingsRepository.ts
│   └── metadataRepository.ts
│
├── types/
│   ├── workspace.ts
│   ├── settings.ts
│   ├── templates.ts
│   └── extensionMessages.ts
│
├── utils/
│   ├── date.ts
│   ├── id.ts
│   ├── json.ts
│   ├── text.ts
│   └── validation.ts
│
├── popup/
│   ├── App.tsx
│   ├── main.tsx
│   └── routes.tsx
│
├── content/
│   ├── contentScript.ts
│   ├── injectButton.ts
│   └── inputInserter.ts
│
├── background/
│   └── serviceWorker.ts
│
└── tests/
    ├── contextBuilder.test.ts
    ├── importExport.test.ts
    └── templates.test.ts
```

## 2. مسؤوليات المجلدات

### `components/`

مكونات عامة لا تعرف شيئاً عن منطق الميزات، مثل الأزرار والحقول والنوافذ.

### `features/`

كل ميزة مستقلة نسبياً، وتحتوي على مكوناتها وخدماتها وأنواعها الخاصة.

### `hooks/`

Hooks عامة قابلة للاستخدام في أكثر من ميزة.

### `services/`

خدمات عامة لا ترتبط بميزة واحدة فقط.

### `storage/`

كل ما يخص IndexedDB والمستودعات المحلية.

### `utils/`

دوال صغيرة نقية مثل توليد المعرفات، تنسيق التاريخ، والتحقق من JSON.

### `types/`

أنواع مشتركة بين أكثر من طبقة.

### `popup/`

نقطة دخول واجهة Popup الخاصة بالإضافة.

### `content/`

منطق حقن الزر والإدراج داخل صفحات منصات الذكاء الاصطناعي.

### `background/`

Service Worker الخاص بـ Manifest V3.

## 3. قواعد التنظيم

- لا يتم وضع منطق التخزين داخل مكونات React.
- لا يتم استدعاء DOM الخاص بالمنصات داخل Popup.
- لا يتم وضع نصوص القوالب داخل المكونات مباشرة.
- لا يتم تكرار أنواع المشروع بين أكثر من ملف.
- كل ملف يجب أن يملك مسؤولية واحدة واضحة.
- التعليقات تكون لتوضيح القرارات أو السلوك غير البديهي، لا لشرح كل سطر.

## 4. اصطلاحات التسمية

| العنصر | النمط | مثال |
|---|---|---|
| مكونات React | PascalCase | `WorkspaceCard.tsx` |
| Hooks | camelCase يبدأ بـ use | `useWorkspaces.ts` |
| خدمات | camelCase مع Service | `contextBuilderService.ts` |
| مستودعات | camelCase مع Repository | `workspaceRepository.ts` |
| أنواع | PascalCase | `Workspace` |
| اختبارات | اسم الملف + test | `contextBuilder.test.ts` |

## 5. مسارات البناء المتوقعة

- Popup entry: `src/popup/main.tsx`.
- Content script entry: `src/content/contentScript.ts`.
- Background entry: `src/background/serviceWorker.ts`.
- Manifest: `public/manifest.json` أو يتم توليده حسب أداة البناء.
