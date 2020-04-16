---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399912"
---
:::row:::
    :::column span="3":::
        JavaScript Speech SDK jest dostępny jako pakiet npm, zobacz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i jest towarzyszem repozytorium GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Chociaż javascript speech SDK jest dostępny jako pakiet npm, w związku z tym zarówno przeglądarki internetowe klienta, jak i Node.js mogą go zużywać — należy wziąć pod uwagę różne implikacje architektoniczne każdego środowiska. Na przykład <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model obiektu dokumentu <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> nie jest dostępny dla aplikacji po stronie serwera, podobnie jak <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> system plików</a> nie jest dostępny dla aplikacji po stronie klienta.

### <a name="nodejs-package-manager-npm"></a>Menedżer pakietów node.js (NPM)

Aby zainstalować pakiet JavaScript Speech SDK, uruchom poniższe polecenie. `npm install`

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Tag skryptu HTML

Alternatywnie można bezpośrednio dołączyć `<script>` znacznik w elemencie HTMLs, `<head>` opierając się na <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">syndykacie <span class="docon docon-navigate-external x-hidden-focus"> </span> **JSDelivr** NPM </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Aby uzyskać więcej informacji, zobacz szybki <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">start <span class="docon docon-navigate-external x-hidden-focus"> </span>sdk mowy przeglądarki sieci Web </a>.
