---
title: Uruchamianie czytnika immersyjnego z zawartością HTML
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak uruchomić czytnik immersyjny z zawartością HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript
ms.openlocfilehash: fbe54b3bd6e0fc8c42b4dfc7401be74ae65ce6cd
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406932"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Jak uruchomić czytnik immersyjny z zawartością HTML

W tym artykule pokazano, jak uruchomić czytnik immersyjny z zawartością HTML.

## <a name="prepare-the-html-content"></a>Przygotowywanie zawartości HTML

Umieść zawartość, która ma być renderowana w czytniku immersyjny wewnątrz elementu kontenera. Upewnij się, że element Container ma unikatową wartość `id` . Czytnik immersyjny zapewnia obsługę podstawowych elementów HTML. więcej informacji można znaleźć w [dokumentacji](./reference.md#html-support) .

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

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)