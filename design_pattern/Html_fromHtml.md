# Html#fromHtml()

# Problem

```kotlin
text = items?.joinToString {
        val name = "<font color=${if (it.colorCode.isBlank()) "black" else it.colorCode}>"+
                "(if (it.origin.isBlank()) it.name else it.name + it.origin) + "</font>"

        return@joinToString if (Build.VERSION.SDK_INT < Build.VERSION_CODES.N) Html.fromHtml(name)
	else Html.fromHtml(name, Html.FROM_HTML_MODE_LEGACY)        
    }
```

> html 태그를 붙여 font color를 주려고 했으나 plain text로만 적용됨

# Cause

- 위 코드는 Html#fromHtml()를 통해 Return받은 Spanned를 joinToString으로 이어 붙였더니, HTML태그가 적용이 안됨

# Resolve

```kotlin
val html = items?.joinToString {
        "<font color=${if (it.colorCode.isBlank()) "black" else it.colorCode}>" +
                "${if (it.origin.isBlank()) it.name else it.name + it.origin}</font>"
    }
    text = if (Build.VERSION.SDK_INT < Build.VERSION_CODES.N) Html.fromHtml(html)
    else Html.fromHtml(html, Html.FROM_HTML_MODE_LEGACY)
```

- HTML 태그들을 먼저 joinToString 
- Html#fromHtml() 인자로 넣어주고 Spanned를 TextView에 ㄱ