# Keyboard: keydown and keyup

قبل از اینکه به صفحه کلید بپردازیم، این موضوع را درنظر داشته باشید که در دیوایس‌های جدید ورودی های دیگری مثل تشخیص صدا (به خصوص در موبایل‌ها) و یا کپی و پیست با موس نیز وجود دارد.

بنابراین اگر بخواهیم هرگونه ورودی به یک فیلد `<input>` را ر دیابی کنیم، این event کافی نیست. event دیگری به نام `input` وجود دارد که هر گونه تغییر در فیلد `<input>` از هر طریقی را ردیابی میکند. انجام اینکار انتخاب بهتری برای این مورد است. بعدا درباره این موضوع صحبت خواهیم کرد.<info:events-change-input>.

زمانی که ما میخواهیم حرکات صفحه کلید را رهگیری کنیم باید از event‌های صفحه کلید استفاده کنیم. برای مثال در مواقعی که بخواهیم فشردن یا رهاکردن کلیدی (ویا ترکیبی از آنها) را تشخیص دهیم

## Teststand [#keyboard-test-stand]

```offline
برای درک بهتر رویدادهای (event) صفحه کلید، شما میتوانید از [teststand](sandbox:keyboard-dump) استفاده کنید.
```

```online
برای درک بهتر رویدادهای (event) صفحه کلید شما میتوانید از روش زیر استفاده کنید.

ترکیب کلیدهای مختلف را در جعبه متن امتحان کنید.

[codetabs src="keyboard-dump" height=480]
```


## Keydown and keyup

رویداد `keydown` زمانی اتفاق می‌افتد که کلیدی فشرده شود و سپس رویداد `keyup` اتفاق می‌افتد در زمانیکه آن کلید رها شود.

### event.code and event.key

ویژگی `key`روی به ما اجازه میدهد که کاراکتر کلید را دریافت کنیم درحالیکه ویژگی `code` رویداد به ما اجازه دریافت کد فیزیکی کلید را میدهد.

برای مثال کلید z `key:Z` میتواند با کلید شیفت یا بدون آن `key:Shift` فشرده شود. این ترکیب دو نتیجه متفاوت به ما خواهد داد:  `z` و `Z`.

مقدار `event.key`  دقیقا کاراکتر تایپ شده خواهد بود که دو مقدار متفاوت است. اما مقدار `event.code`  یکسان است:

| Key          | `event.key` | `event.code` |
|--------------|-------------|--------------|
| `key:Z`      |`z` (lowercase)         |`KeyZ`        |
| `key:Shift+Z`|`Z` (uppercase)          |`KeyZ`        |


اگر یک کاربر با صفحه کلید به زبان دیگری غیر از انگلیسی کار کند آنگاه خروجی `event.key` با `"Z"` متفاوت خواهد بود. در صورتیکه مقدار `event.code` همواره یکسان است.

```smart header="\"KeyZ\" and other key codes"
هر کلیدی یک کد دارد که باتوجه به مکان آن روی صفحه کلید متفاوت است. Key code ها در لینک مقابل توضیح داده شده است.[UI Events code specification](https://www.w3.org/TR/uievents-code/).

برای مثال:
- key code حروف برابر است با: `"Key<letter>"` `"KeyA"`, `"KeyB"` etc.
- key code اعداد برابر است با `"Digit<number>"`: `"Digit0"`, `"Digit1"` etc.
- کلیدهای خاص با اسم خودشان کد شده‌اند: `"Enter"`, `"Backspace"`, `"Tab"` etc.

There are several widespread keyboard layouts, and the specification gives key codes for each of them.
چندین طرح و چیدمان برای صفحه‌کلید وجود دارد که هرکدام کدهای خاص خودشان را دارند.

مقاله [alphanumeric section of the spec](https://www.w3.org/TR/uievents-code/#key-alphanumeric-section) را مطالعه کنید, یا در قسمت بالا [teststand](#keyboard-test-stand) برای دریافت کد کلید آنرا فشار دهید.
```

```warn header="Case matters: `\"KeyZ\"`, not `\"keyZ\"`"
به نظر ساده و قابل درک است, اما بعضی افراد هنوز دچار مشکل می‌شوند..

در هنگام تایپ به املا درست آن بسیار دفت کنید: `KeyZ` درست است, نه `keyZ`. مثلا عبارت `event.code=="keyZ"` درست کار نخواهد کرد: عبارت `"Key"` باید با حرف بزرگ باشد.
```

اگر دکمه‌ای کاراکتر خروجی نداشته باشد چه? برای مثال, `key:Shift` یا `key:F1` یا کلیدهای دیگر. برای این دسته از کلیدها, `event.key` و `event.code` مقدار یکسانی خواهد بود:

| Key          | `event.key` | `event.code` |
|--------------|-------------|--------------|
| `key:F1`      |`F1`          |`F1`        |
| `key:Backspace`      |`Backspace`          |`Backspace`        |
| `key:Shift`|`Shift`          |`ShiftRight` or `ShiftLeft`        |

درنظر داشته باشد که `event.code` مشخص میکند که دقیقا کدام دکمه فشرده شده است. برای مثال اکثر صفحه‌کلیدها دو کلید shift یکی در سمت راست و یکی در سمت چپ صفحه کلید دارند. عبارت `event.code` به ما میگوید که دقیقا کدام کلید فشار داده شده و عبارت `event.key` وظیفه دارد که مقدار آن کلید را به ما برگرداند که در اینجا مقدار shift است.

فرض کنید که میخواهیم که فشرده شدن کلیدهای `Ctrl+Z` (یا `key:Cmd+Z` برای Mac) کنترل و شناسایی کنیم. بیشتر ویرایشگرهای متن این دو دکمه را برای "Undo" قرار داده‌اند. ما میتوانیم روی `keydown` یک listener قرار دهیم و ببینیم کدام دکمه‌ها فشرده شده است.

در اینجاب برای ما سوالی پیش می‌آید: یا listener باید مقدار `event.key` را بررسی کند و یا مقدار `event.code`?

از طرفی مقدار `event.key` یک کاراکتر است و باتوجه به زبان صفحه کلید مقدار آن تغییر میکند. اگر کاربر در سیستم عاملش بیشتر یک سیستم عامل داته باشد و زبان را تغییر دهد آنگاه یک کلید واحد خروجی متفاوتی خواهد داشت.. بنابراین منطقی است که ما مقدار `event.code` را بررسی کنیم چراکه همواره مقدار ثابتی دارد.

مثل این:

```js run
document.addEventListener('keydown', function(event) {
  if (event.code == 'KeyZ' && (event.ctrlKey || event.metaKey)) {
    alert('Undo!')
  }
});
```

On the other hand, there's a problem with `event.code`. For different keyboard layouts, the same key may have different characters.

For example, here are US layout ("QWERTY") and German layout ("QWERTZ") under it (from Wikipedia):

![](us-layout.svg)

![](german-layout.svg)

For the same key, US layout has "Z", while German layout has "Y" (letters are swapped).

Literally, `event.code` will equal `KeyZ` for people with German layout when they press `key:Y`.

If we check `event.code == 'KeyZ'` in our code, then for people with German layout such test will pass when they press `key:Y`.

That sounds really odd, but so it is. The [specification](https://www.w3.org/TR/uievents-code/#table-key-code-alphanumeric-writing-system) explicitly mentions such behavior.

So, `event.code` may match a wrong character for unexpected layout. Same letters in different layouts may map to different physical keys, leading to different codes. Luckily, that happens only with several codes, e.g. `keyA`, `keyQ`, `keyZ` (as we've seen), and doesn't happen with special keys such as `Shift`. You can find the list in the [specification](https://www.w3.org/TR/uievents-code/#table-key-code-alphanumeric-writing-system).

To reliably track layout-dependent characters, `event.key` may be a better way.

On the other hand, `event.code` has the benefit of staying always the same, bound to the physical key location. So hotkeys that rely on it work well even in case of a language switch.

Do we want to handle layout-dependant keys? Then `event.key` is the way to go.

Or we want a hotkey to work even after a language switch? Then `event.code` may be better.

## Auto-repeat

If a key is being pressed for a long enough time, it starts to "auto-repeat": the `keydown` triggers again and again, and then when it's released we finally get `keyup`. So it's kind of normal to have many `keydown` and a single `keyup`.

For events triggered by auto-repeat, the event object has `event.repeat` property set to `true`.


## Default actions

Default actions vary, as there are many possible things that may be initiated by the keyboard.

For instance:

- A character appears on the screen (the most obvious outcome).
- A character is deleted (`key:Delete` key).
- The page is scrolled (`key:PageDown` key).
- The browser opens the "Save Page" dialog (`key:Ctrl+S`)
-  ...and so on.

Preventing the default action on `keydown` can cancel most of them, with the exception of OS-based special keys. For instance, on Windows `key:Alt+F4` closes the current browser window. And there's no way to stop it by preventing the default action in JavaScript.

For instance, the `<input>` below expects a phone number, so it does not accept keys except digits, `+`, `()` or `-`:

```html autorun height=60 run
<script>
function checkPhoneKey(key) {
  return (key >= '0' && key <= '9') || ['+','(',')','-'].includes(key);
}
</script>
<input *!*onkeydown="return checkPhoneKey(event.key)"*/!* placeholder="Phone, please" type="tel">
```

The `onkeydown` handler here uses `checkPhoneKey` to check for the key pressed. If it's valid (from `0..9` or one of `+-()`), then it returns `true`, otherwise `false`.

As we know, the `false` value returned from the event handler, assigned using a DOM property or an attribute, such as above, prevents the default action, so nothing appears in the `<input>` for keys that don't pass the test. (The `true` value returned doesn't affect anything, only returning `false` matters)

Please note that special keys, such as `key:Backspace`, `key:Left`, `key:Right`, do not work in the input. That's a side effect of the strict filter `checkPhoneKey`. These keys make it return `false`.

Let's relax the filter a little bit by allowing arrow keys `key:Left`, `key:Right` and `key:Delete`, `key:Backspace`:

```html autorun height=60 run
<script>
function checkPhoneKey(key) {
  return (key >= '0' && key <= '9') ||
    ['+','(',')','-',*!*'ArrowLeft','ArrowRight','Delete','Backspace'*/!*].includes(key);
}
</script>
<input onkeydown="return checkPhoneKey(event.key)" placeholder="Phone, please" type="tel">
```

Now arrows and deletion works well.

Even though we have the key filter, one still can enter anything using a mouse and right-click + Paste. Mobile devices provide other means to enter values. So the filter is not 100% reliable.

The alternative approach would be to track the `oninput` event -- it triggers *after* any modification. There we can check the new `input.value` and modify it/highlight the `<input>` when it's invalid. Or we can use both event handlers together.

## Legacy

In the past, there was a `keypress` event, and also `keyCode`, `charCode`, `which` properties of the event object.

There were so many browser incompatibilities while working with them, that developers of the specification had no way, other than deprecating all of them and creating new, modern events (described above in this chapter). The old code still works, as browsers keep supporting them, but there's totally no need to use those any more.

## Mobile Keyboards

When using virtual/mobile keyboards, formally known as IME (Input-Method Editor), the W3C standard states that a KeyboardEvent's [`e.keyCode` should be `229`](https://www.w3.org/TR/uievents/#determine-keydown-keyup-keyCode) and [`e.key` should be `"Unidentified"`](https://www.w3.org/TR/uievents-key/#key-attr-values).

While some of these keyboards might still use the right values for `e.key`, `e.code`, `e.keyCode`... when pressing certain keys such as arrows or backspace, there's no guarantee, so your keyboard logic might not always work on mobile devices.

## Summary

Pressing a key always generates a keyboard event, be it symbol keys or special keys like `key:Shift` or `key:Ctrl` and so on. The only exception is `key:Fn` key that sometimes presents on a laptop keyboard. There's no keyboard event for it, because it's often implemented on lower level than OS.

Keyboard events:

- `keydown` -- on pressing the key (auto-repeats if the key is pressed for long),
- `keyup` -- on releasing the key.

Main keyboard event properties:

- `code` -- the "key code" (`"KeyA"`, `"ArrowLeft"` and so on), specific to the physical location of the key on keyboard.
- `key` -- the character (`"A"`, `"a"` and so on), for non-character keys, such as `key:Esc`, usually has the same value  as `code`.

In the past, keyboard events were sometimes used to track user input in form fields. That's not reliable, because the input can come from various sources. We have `input` and `change` events to handle any input (covered later in the chapter <info:events-change-input>). They trigger after any kind of input, including copy-pasting or speech recognition.

We should use keyboard events when we really want keyboard. For example, to react on hotkeys or special keys.
