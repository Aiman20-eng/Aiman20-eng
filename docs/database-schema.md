# مخطط قاعدة البيانات — IndexedDB

## 1. نظرة عامة

تعتمد إضافة AI Context Builder على IndexedDB لتخزين جميع البيانات محلياً. لا يتم إرسال البيانات إلى أي خادم في الإصدار الأول.

## 2. معلومات قاعدة البيانات

| العنصر | القيمة |
|---|---|
| اسم قاعدة البيانات | `ai-context-builder-db` |
| النسخة الأولية | `1` |
| نوع التخزين | IndexedDB |
| أسلوب المفاتيح | UUID string |

## 3. Object Stores

### 3.1 `workspaces`

يخزن مشاريع المستخدم وسياقاتها.

#### الحقول

| الحقل | النوع | مطلوب | الوصف |
|---|---|---|---|
| `id` | `string` | نعم | معرف فريد للمشروع |
| `name` | `string` | نعم | اسم المشروع |
| `description` | `string` | لا | وصف مختصر أو تفصيلي للمشروع |
| `technologies` | `string[]` | لا | التقنيات المستخدمة |
| `goals` | `string` | لا | أهداف المشروع |
| `requirements` | `string` | لا | المتطلبات والقيود |
| `aiInstructions` | `string` | لا | تعليمات خاصة للذكاء الاصطناعي |
| `additionalNotes` | `string` | لا | ملاحظات إضافية |
| `templateId` | `string \| null` | لا | معرف القالب المستخدم إن وجد |
| `createdAt` | `string` | نعم | تاريخ الإنشاء بصيغة ISO |
| `updatedAt` | `string` | نعم | تاريخ آخر تحديث بصيغة ISO |

#### الفهارس

| الفهرس | الحقل | فريد |
|---|---|---|
| `by_name` | `name` | لا |
| `by_updated_at` | `updatedAt` | لا |
| `by_template_id` | `templateId` | لا |

#### نموذج JSON

```json
{
  "id": "workspace_01HZXExample",
  "name": "منصة حجوزات",
  "description": "منصة لإدارة حجوزات الخدمات والمواعيد.",
  "technologies": ["React", "TypeScript", "Laravel"],
  "goals": "بناء تجربة حجز سهلة وسريعة للمستخدم النهائي.",
  "requirements": "دعم تعدد الأدوار وإشعارات الحجز.",
  "aiInstructions": "أجب باللغة العربية، واقترح حلولاً عملية قابلة للتنفيذ.",
  "additionalNotes": "الأولوية للأداء وتجربة المستخدم.",
  "templateId": "laravel",
  "createdAt": "2026-05-29T00:00:00.000Z",
  "updatedAt": "2026-05-29T00:00:00.000Z"
}
```

### 3.2 `settings`

يخزن إعدادات بسيطة للإضافة.

#### الحقول

| الحقل | النوع | مطلوب | الوصف |
|---|---|---|---|
| `key` | `string` | نعم | مفتاح الإعداد |
| `value` | `unknown` | نعم | قيمة الإعداد |
| `updatedAt` | `string` | نعم | تاريخ آخر تحديث |

#### المفاتيح المقترحة

| المفتاح | النوع | الوصف |
|---|---|---|
| `activeWorkspaceId` | `string \| null` | المشروع النشط للإدراج السريع |
| `hasCompletedOnboarding` | `boolean` | هل أنهى المستخدم شاشة البداية |
| `preferredPlatform` | `string \| null` | منصة مفضلة مستقبلاً |

### 3.3 `metadata`

يخزن معلومات تقنية عن قاعدة البيانات والنسخ.

| الحقل | النوع | مطلوب | الوصف |
|---|---|---|---|
| `key` | `string` | نعم | مفتاح المعلومة |
| `value` | `unknown` | نعم | القيمة |

#### المفاتيح المقترحة

- `schemaVersion`.
- `lastExportAt`.
- `lastImportAt`.

## 4. أنواع TypeScript المقترحة

```ts
export interface Workspace {
  id: string;
  name: string;
  description: string;
  technologies: string[];
  goals: string;
  requirements: string;
  aiInstructions: string;
  additionalNotes: string;
  templateId: string | null;
  createdAt: string;
  updatedAt: string;
}

export interface AppSetting<TValue = unknown> {
  key: string;
  value: TValue;
  updatedAt: string;
}

export interface DatabaseMetadata<TValue = unknown> {
  key: string;
  value: TValue;
}
```

## 5. مخطط التصدير والاستيراد

### 5.1 صيغة ملف التصدير

```json
{
  "app": "AI Context Builder",
  "version": 1,
  "exportedAt": "2026-05-29T00:00:00.000Z",
  "workspaces": []
}
```

### 5.2 قواعد الاستيراد

- يجب أن يحتوي الملف على `app` و`version` و`workspaces`.
- يتم تجاهل الحقول غير المعروفة.
- يتم رفض العناصر التي لا تحتوي على `name` صالح.
- عند وجود نفس `id`، يتم تحديث المشروع إذا كان المستورد أحدث أو يتم توليد معرف جديد حسب قرار التنفيذ.
- لا يتم حذف البيانات الحالية أثناء الاستيراد.

## 6. الترحيلات المستقبلية

أي تغيير في البنية يجب أن يتم عبر زيادة رقم النسخة وتنفيذ Migration واضح داخل `indexedDbClient`.

أمثلة محتملة:

- إضافة `tags` للمشاريع.
- إضافة `favorite` للمشاريع المفضلة.
- إضافة `contextVariants` لنسخ متعددة من السياق.
