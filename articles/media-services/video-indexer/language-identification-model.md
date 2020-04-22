---
title: Automatyczne identyfikowanie języków mówionych za pomocą indeksatora wideo — Azure
titleSuffix: Azure Media Services
description: W tym artykule opisano, jak model identyfikacji języka indeksatora wideo jest używany do automatycznego identyfikowania języka mówionego w filmie wideo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687122"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatyczne identyfikowanie języka mówionego za pomocą modelu identyfikacji języka

Video Indexer obsługuje automatyczną identyfikację języka (LID), która jest procesem automatycznej identyfikacji treści języka mówionego z audio i wysyłania pliku multimedialnego do transkrypcji w dominującym zidentyfikowanym języku. 

Obecnie LID obsługuje: angielski, hiszpański, francuski, niemiecki, włoski, mandaryński chiński, japoński, rosyjski i portugalski (brazylijski). 

Zapoznaj się z poniższą sekcją [Wytłachnia i ograniczeniami.](#guidelines-and-limitations)

## <a name="choosing-auto-language-identification-on-indexing"></a>Wybieranie automatycznej identyfikacji języka podczas indeksowania

Podczas indeksowania lub [ponownego indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) wideo za `auto detect` pomocą interfejsu `sourceLanguage` API, wybierz opcję w parametrze.

Korzystając z portalu, przejdź do **filmów z konta** na stronie głównej [indeksatora wideo](https://www.videoindexer.ai/) i umieść wskaźnik myszy na nazwie filmu, który chcesz ponownie indeksować. W prawym dolnym rogu kliknij przycisk ponownego indeksowania. W oknie dialogowym **Ponowne indeksowanie wideo** wybierz pozycję *Automatyczne wykrywanie* z listy rozwijanej **Język źródłowy wideo.**

![Autowykrywanie](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Dane wyjściowe modelu

Indeksator wideo transkrybuje film według najbardziej prawdopodobnego języka, `> 0.6`jeśli zaufanie do tego języka jest . Jeśli język nie może być identyfikowany z ufnością, zakłada, że językiem mówionym jest angielski. 

Model dominujący język jest dostępny w `sourceLanguage` insights JSON jako atrybut (w katalogu głównym / filmy / insights). Odpowiedni wynik zaufania jest również `sourceLanguageConfidence` dostępny w ramach atrybutu.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia

* Automatyczna identyfikacja języka (LID) obsługuje następujące języki: 

    Angielski, hiszpański, francuski, niemiecki, włoski, mandaryński Chines, japoński, rosyjski i portugalski (brazylijski).
* Mimo że indeksator wideo obsługuje arabski (Modern Standard i Levantine), hindi i koreański, te języki nie są obsługiwane w LID.
* Jeśli dźwięk zawiera języki inne niż obsługiwana lista powyżej, wynik jest nieoczekiwany.
* Jeśli indeksator wideo nie może zidentyfikować języka`>0.6`z wystarczająco wysokim zaufaniem ( ), językiem rezerwowym jest angielski.
* Nie ma bieżącej obsługi plików z mieszanymi językami audio. Jeśli dźwięk zawiera języki mieszane, wynik jest nieoczekiwany. 
* Dźwięk o niskiej jakości może mieć wpływ na wyniki modelu.
* Model wymaga co najmniej jednej minuty mowy w audio.
* Model jest przeznaczony do rozpoznawania spontanicznej mowy konwersacyjnej (nie poleceń głosowych, śpiewu itp.).

## <a name="next-steps"></a>Następne kroki

* [Omówienie](video-indexer-overview.md)
* [Automatyczne identyfikowanie i transkrybowanie treści wielojęzycznych](multi-language-identification-transcription.md)
