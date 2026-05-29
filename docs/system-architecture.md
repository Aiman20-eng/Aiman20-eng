# وثيقة معمارية النظام — AI Context Builder

## 1. النظرة العامة

AI Context Builder إضافة متصفح مبنية باستخدام React وTypeScript وTailwind CSS وتعمل وفق Manifest V3. تعتمد الإضافة على التخزين المحلي IndexedDB ولا تستخدم أي Backend أو AI APIs في الإصدار الأول.

## 2. مكونات الإضافة

```text
Browser Extension
│
├── Popup App
│   ├── إدارة المشاريع
│   ├── البحث
│   ├── القوالب
│   ├── الاستيراد والتصدير
│   └── معاينة السياق
│
├── Content Script
│   ├── اكتشاف منصات الذكاء الاصطناعي
│   ├── حقن زر الإدراج
│   ├── العثور على مربع الكتابة
│   └── إدراج النص
│
├── Background Service Worker
│   ├── وسيط رسائل خفيف عند الحاجة
│   └── إدارة أحداث Manifest V3
│
└── IndexedDB Storage
    ├── المشاريع
    ├── الإعدادات
    └── معلومات النسخة
```

## 3. المبادئ المعمارية

- **Feature Based Architecture:** كل ميزة تملك مكوناتها وخدماتها ومنطقها.
- **Separation of Concerns:** فصل التخزين، بناء السياق، الواجهة، وحقن الصفحة.
- **Local First:** البيانات تبدأ وتنتهي على جهاز المستخدم.
- **Minimal Permissions:** أقل صلاحيات ممكنة لتشغيل المنتج.
- **Progressive Enhancement:** دعم منصات إضافية لاحقاً دون تغيير جذري في النواة.
- **Typed Domain Models:** تعريف أنواع واضحة للكيانات والرسائل.

## 4. طبقات النظام

### 4.1 طبقة العرض

المسؤولة عن واجهة المستخدم داخل Popup وصفحات الإضافة.

- React Components.
- Tailwind CSS.
- دعم RTL عبر `dir="rtl"` وخصائص CSS مناسبة.
- مكونات قابلة لإعادة الاستخدام مثل الأزرار والحقول والبطاقات.

### 4.2 طبقة الميزات

تضم منطق الميزات الأساسية:

- Workspaces.
- Context Builder.
- Templates.
- Import / Export.
- Search.
- One Click Insert.

### 4.3 طبقة الخدمات

تضم خدمات لا ترتبط مباشرة بالواجهة:

- `contextBuilderService`: توليد Prompt من بيانات المشروع.
- `templateService`: إدارة القوالب الافتراضية.
- `importExportService`: تحويل البيانات والتحقق منها.
- `platformDetectorService`: تحديد المنصة الحالية داخل Content Script.

### 4.4 طبقة التخزين

- `indexedDbClient`: فتح قاعدة البيانات وتحديث نسختها.
- `workspaceRepository`: CRUD للمشاريع.
- `settingsRepository`: حفظ إعدادات بسيطة مثل المشروع النشط.

### 4.5 طبقة الاتصال الداخلي

تستخدم رسائل Extension Runtime عند الحاجة بين:

- Popup.
- Background Service Worker.
- Content Script.

في الإصدار الأول يجب إبقاء الرسائل محدودة وواضحة.

## 5. تدفق البيانات

### 5.1 إنشاء مشروع

```text
User Form
  → Validation
  → Workspace Feature
  → Workspace Repository
  → IndexedDB
  → UI State Refresh
```

### 5.2 توليد السياق

```text
Selected Workspace
  → Context Builder Service
  → Structured Arabic Prompt
  → Preview or Insert
```

### 5.3 الإدراج داخل منصة ذكاء اصطناعي

```text
Content Script Button
  → Read Active Workspace from IndexedDB
  → Build Context Prompt
  → Locate Text Input
  → Insert Text
  → Dispatch Input Events
```

## 6. دعم المنصات

### 6.1 ChatGPT

- اكتشاف الصفحة عبر النطاق.
- البحث عن محرر النصوص النشط.
- إدراج النص مع إطلاق أحداث `input` و`change` إن لزم.

### 6.2 Claude

- دعم محرر نصوص غني أو textarea حسب نسخة الواجهة.
- إدراج النص دون الضغط التلقائي على زر الإرسال.

### 6.3 Gemini

- البحث عن محرر `contenteditable` أو textarea.
- المحافظة على تركيز المستخدم داخل مربع الكتابة.

## 7. الصلاحيات المقترحة في Manifest V3

```json
{
  "permissions": ["storage"],
  "host_permissions": [
    "https://chatgpt.com/*",
    "https://chat.openai.com/*",
    "https://claude.ai/*",
    "https://gemini.google.com/*"
  ]
}
```

> ملاحظة: رغم استخدام IndexedDB مباشرة، يمكن استخدام صلاحية `storage` لاحقاً لإعدادات صغيرة إذا احتجنا. يجب مراجعة الحاجة النهائية أثناء التنفيذ.

## 8. إدارة الحالة

في الإصدار الأول يفضل استخدام React Hooks وحالة محلية دون مكتبات إدارة حالة خارجية.

- `useWorkspaces` لتحميل وإدارة المشاريع.
- `useWorkspaceSearch` لتصفية النتائج.
- `useActiveWorkspace` لإدارة المشروع النشط.
- `useImportExport` لإجراءات JSON.

## 9. استراتيجية الاختبار

- اختبارات وحدات لخدمة بناء السياق.
- اختبارات وحدات للتحقق من الاستيراد والتصدير.
- اختبارات وحدات لخدمة القوالب.
- اختبارات بسيطة لمستودع التخزين باستخدام mock أو بيئة اختبار مناسبة.
- اختبار يدوي للإدراج على المنصات المدعومة.

## 10. المخاطر التقنية

| الخطر | التأثير | التخفيف |
|---|---|---|
| تغيّر بنية صفحات ChatGPT أو Claude أو Gemini | فشل الإدراج | استخدام محددات مرنة ومراقبة DOM |
| قيود Manifest V3 على Service Worker | سلوك غير متوقع للأحداث | إبقاء المنطق الأساسي في Popup وContent Script |
| اختلاف محررات النصوص بين المنصات | إدراج غير مستقر | بناء Adapter لكل منصة |
| ملفات JSON غير صحيحة عند الاستيراد | تلف البيانات | التحقق قبل الكتابة ودمج آمن |

## 11. قابلية التوسع

يمكن إضافة الميزات التالية لاحقاً دون كسر البنية:

- تصنيفات للمشاريع.
- نسخ متعددة للسياق.
- اختصارات لوحة مفاتيح.
- مزامنة اختيارية مشفرة.
- دعم منصات AI إضافية.
- Dark Mode.
- قوالب مخصصة من المستخدم.
