---
title: Jak przygotować zawartość HTML dla czytnika immersyjny
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak uruchomić czytnik immersyjny przy użyciu języków HTML, JavaScript, Python, Android lub iOS. Czytnik immersyjny używa sprawdzonych technik, aby zwiększyć czytelność czytanych informacji dotyczących języka, nowych czytelników i studentów z różnicami w nauce.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620167"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Jak przygotować zawartość HTML dla czytnika immersyjny

W tym artykule pokazano, jak określić strukturę kodu HTML i pobrać zawartość, aby mogła być używana przez czytnik immersyjny.

## <a name="prepare-the-html-content"></a>Przygotowywanie zawartości HTML

Umieść zawartość, która ma być renderowana w czytniku immersyjny wewnątrz elementu kontenera. Upewnij się, że element Container ma unikatową wartość `id` . Czytnik immersyjny zapewnia obsługę podstawowych elementów HTML. więcej informacji można znaleźć w [dokumentacji](reference.md#html-support) .

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Pobieranie zawartości HTML w języku JavaScript

Użyj `id` elementu Container, aby pobrać zawartość HTML w kodzie JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Uruchom czytnik immersyjny z zawartością HTML

Podczas wywoływania `ImmersiveReader.launchAsync` Ustaw właściwość fragmentu `mimeType` na, `text/html` Aby włączyć renderowanie kodu HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](reference.md)