---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3b7f459e0e95e798e5430414d23f34a455719a40
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376656"
---
:::row:::
    :::column span="3":::
        Zestaw Speech SDK dla języka JavaScript jest dostępny jako pakiet npm. zobacz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i jego pomocnika repozytorium GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-Services-Speech-SDK-js <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Chociaż zestaw Speech SDK dla języka JavaScript jest dostępny jako pakiet npm, w ten sposób zarówno Node.js, jak i przeglądarki sieci Web klienta mogą korzystać z tej usługi, należy wziąć pod uwagę różne konsekwencje architektury dla każdego środowiska. Na przykład, <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model obiektów dokumentu (dom) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nie jest dostępny dla aplikacji po stronie serwera, podobnie jak <a href="https://nodejs.org/api/fs.html" target="_blank">system <span class="docon docon-navigate-external x-hidden-focus"></span> plików</a> nie jest dostępny dla aplikacji po stronie klienta.

### <a name="nodejs-package-manager-npm"></a>Menedżer pakietów Node.js (NPM)

Aby zainstalować zestaw Speech SDK dla języka JavaScript, uruchom poniższe `npm install` polecenie poniżej.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Aby uzyskać więcej informacji, zobacz <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">zestaw SDK szybkiego <span class="docon docon-navigate-external x-hidden-focus"></span> startuNode.js Speech </a>.
