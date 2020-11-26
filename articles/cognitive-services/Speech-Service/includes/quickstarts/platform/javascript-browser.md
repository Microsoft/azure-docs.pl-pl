---
title: 'Szybki Start: zestaw Speech SDK dla języka JavaScript (przeglądarki) Konfiguracja platformy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę do korzystania z języka JavaScript (przeglądarki) z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188432"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla języka JavaScript do użycia ze stroną sieci Web.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Tworzenie nowego folderu witryny internetowej

Utwórz nowy, pusty folder. W przypadku, gdy chcesz hostować przykład na serwerze internetowym, upewnij się, że serwer internetowy ma dostęp do tego folderu.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozpakowywanie zestawu SDK usługi Mowa dla języka JavaScript do tego folderu

Pobierz zestaw SDK usługi Mowa jako [pakiet zip](https://aka.ms/csspeech/jsbrowserpackage) i rozpakuj go do nowo utworzonego folderu. Spowoduje to odpakowanie pięciu plików:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` Czytelna dla człowieka wersja zestawu Speech SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` Plik mapy używany do debugowania kodu zestawu SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` Definicje obiektów do użycia z TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Zminimalizowanego wersja zestawu Speech SDK.
* `speech-processor.js` Kod umożliwiający poprawienie wydajności w niektórych przeglądarkach.

## <a name="create-an-indexhtml-page"></a>Tworzenie strony index.html

Utwórz w folderze nowy plik o nazwie `index.html`, a następnie otwórz ten plik za pomocą edytora tekstów.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]