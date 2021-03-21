---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 252f2e6275b01522b83e846201d190b39d2bbf21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434513"
---
:::row:::
    :::column span="3":::
        Zestaw Speech SDK dla języka JavaScript jest dostępny jako pakiet npm. zobacz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech-SDK </a> i jego pomocnika repozytorium GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-Services-Speech-SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Chociaż zestaw Speech SDK dla języka JavaScript jest dostępny jako pakiet npm, w ten sposób zarówno przeglądarki sieci Web klienta, jak i Node.js mogą korzystać z różnych zagadnień związanych z architekturą każdego środowiska. Na przykład, <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model obiektów dokumentu (dom) </a> nie jest dostępny dla aplikacji po stronie serwera, podobnie jak <a href="https://nodejs.org/api/fs.html" target="_blank">system plików </a> nie jest dostępny dla aplikacji po stronie klienta.

### <a name="nodejs-package-manager-npm"></a>Menedżer pakietów Node.js (NPM)

Aby zainstalować zestaw Speech SDK dla języka JavaScript, uruchom poniższe `npm install` polecenie poniżej.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Tag skryptu HTML

Alternatywnie możesz bezpośrednio dołączyć `<script>` tag do `<head>` elementu HTML, opierając się na <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">syndykatie **JSDelivr** npm</a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Aby uzyskać więcej informacji, zobacz <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Przewodnik Szybki Start dotyczący zestawu SDK dla przeglądarki sieci Web </a>.
