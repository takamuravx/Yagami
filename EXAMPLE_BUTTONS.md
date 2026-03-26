# مثال على استخدام الأزرار التفاعلية في Yagami

## نظرة عامة

تم إضافة دعم الأزرار التفاعلية إلى مكتبة Yagami. يمكنك الآن إرسال رسائل تحتوي على أزرار قابلة للنقر.

## طريقة الاستخدام

### 1. إرسال رسالة نصية مع أزرار

```typescript
import { createButtonMessage } from '@takamuravx/yagami'

// إنشاء رسالة مع أزرار
const buttonMessage = createButtonMessage(
  'مرحباً! اختر أحد الخيارات التالية:',
  [
    { buttonId: 'option1', buttonText: 'الخيار الأول' },
    { buttonId: 'option2', buttonText: 'الخيار الثاني' },
    { buttonId: 'option3', buttonText: 'الخيار الثالث' }
  ],
  'هذا نص تذييل الرسالة'
)

// إرسال الرسالة
await sock.sendMessage(jid, { buttons: buttonMessage })
```

### 2. إرسال صورة مع أزرار

```typescript
import { createButtonMessage } from '@takamuravx/yagami'

// إنشاء رسالة صورة مع أزرار
const buttonMessage = createButtonMessage(
  'اختر أحد الخيارات:',
  [
    { buttonId: 'yes', buttonText: 'نعم' },
    { buttonId: 'no', buttonText: 'لا' }
  ],
  'تذييل الرسالة',
  {
    image: { url: 'https://example.com/image.jpg' },
    caption: 'وصف الصورة'
  }
)

// إرسال الرسالة
await sock.sendMessage(jid, { buttons: buttonMessage })
```

### 3. إرسال فيديو مع أزرار

```typescript
import { createButtonMessage } from '@takamuravx/yagami'

const buttonMessage = createButtonMessage(
  'شاهد الفيديو واختر:',
  [
    { buttonId: 'like', buttonText: 'أعجبني' },
    { buttonId: 'dislike', buttonText: 'لم يعجبني' }
  ],
  'تذييل',
  {
    video: { url: 'https://example.com/video.mp4' },
    caption: 'وصف الفيديو'
  }
)

await sock.sendMessage(jid, { buttons: buttonMessage })
```

### 4. إرسال مستند مع أزرار

```typescript
import { createButtonMessage } from '@takamuravx/yagami'

const buttonMessage = createButtonMessage(
  'راجع المستند واختر:',
  [
    { buttonId: 'approve', buttonText: 'موافق' },
    { buttonId: 'reject', buttonText: 'رفض' }
  ],
  'تذييل',
  {
    document: { url: 'https://example.com/document.pdf' },
    title: 'اسم الملف.pdf'
  }
)

await sock.sendMessage(jid, { buttons: buttonMessage })
```

### 5. الاستخدام المباشر بدون دالة المساعدة

```typescript
await sock.sendMessage(jid, {
  buttons: {
    text: 'اختر خياراً:',
    footer: 'تذييل',
    buttons: [
      { buttonId: 'btn1', buttonText: 'زر 1', type: 1 },
      { buttonId: 'btn2', buttonText: 'زر 2', type: 1 }
    ]
  }
})
```

## استقبال ردود الأزرار

عندما ينقر المستخدم على زر، ستتلقى رداً يمكنك معالجته:

```typescript
sock.ev.on('messages.upsert', async ({ messages }) => {
  const msg = messages[0]
  
  // التحقق من رد الزر
  if (msg.message?.buttonsResponseMessage) {
    const buttonId = msg.message.buttonsResponseMessage.selectedButtonId
    const displayText = msg.message.buttonsResponseMessage.selectedDisplayText
    
    console.log(`تم النقر على الزر: ${buttonId} - ${displayText}`)
    
    // معالجة الرد حسب معرف الزر
    switch (buttonId) {
      case 'option1':
        await sock.sendMessage(msg.key.remoteJid!, { text: 'اخترت الخيار الأول!' })
        break
      case 'option2':
        await sock.sendMessage(msg.key.remoteJid!, { text: 'اخترت الخيار الثاني!' })
        break
    }
  }
})
```

## أنواع الأزرار

- `1` (RESPONSE): زر عادي يرسل رداً
- `2` (URL): زر يفتح رابط (يتطلب إعدادات إضافية)

## ملاحظات

- الحد الأقصى للأزرار في الرسالة الواحدة هو 3 أزرار
- يمكن استخدام الصور أو الفيديو أو المستندات كرأس للرسالة
- نص الرسالة الرئيسي (text) مطلوب
- معرف الزر (buttonId) يجب أن يكون فريداً في الرسالة الواحدة
