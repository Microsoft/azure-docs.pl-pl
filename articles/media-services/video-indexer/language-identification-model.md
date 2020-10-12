---
title: Użyj Video Indexer do autoidentyfikacji języków mówionych — Azure
titleSuffix: Azure Media Services
description: W tym artykule opisano, jak Video Indexer model identyfikacji języka jest używany do automatycznego identyfikowania języka wymawianego w filmie wideo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687122"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatyczne identyfikowanie języka mówionego przy użyciu modelu identyfikacji języka

Video Indexer obsługuje automatyczne identyfikowanie języka (POKRYWy), czyli proces automatycznego identyfikowania zawartości języka wymawianego z dźwiękiem i wysyłania pliku multimedialnego do uzyskanego w określonym języku. 

Obecnie POKRYWka obsługuje: angielski, hiszpański, francuski, niemiecki, włoski, mandarynki chiński, japoński, rosyjski i portugalski (Brazylia). 

Zapoznaj się z sekcją [wskazówki i ograniczenia](#guidelines-and-limitations) poniżej.

## <a name="choosing-auto-language-identification-on-indexing"></a>Wybieranie opcji autoidentyfikacja języka podczas indeksowania

Podczas indeksowania lub [ponownego indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) wideo przy użyciu interfejsu API, wybierz `auto detect` opcję w `sourceLanguage` parametrze.

Gdy korzystasz z portalu, przejdź do swoich **filmów wideo** na stronie głównej [Video Indexer](https://www.videoindexer.ai/) i umieść wskaźnik myszy na nazwie filmu wideo, który chcesz ponownie zindeksować. W prawym dolnym rogu kliknij przycisk ponownie Indeksuj. W oknie dialogowym **ponowne indeksowanie wideo** wybierz pozycję *Autowykrywanie* z pola listy rozwijanej **Język źródłowy wideo** .

![Wykryj automatycznie](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Dane wyjściowe modelu

Video Indexer przekształca wideo zgodnie z najbardziej prawdopodobnym językiem, jeśli jest to wiarygodność danego języka `> 0.6` . Jeśli nie można zidentyfikować języka z pewnością, założono, że język mówiony jest angielski. 

Język dominujący modelu jest dostępny w formacie JSON usługi Insights jako `sourceLanguage` atrybut (w obszarze głównym/wideo/Insights). Odpowiedni wynik zaufania jest również dostępny w `sourceLanguageConfidence` atrybucie.

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

* Automatyczna identyfikacja języka (pokrywa) obsługuje następujące języki: 

    Angielski, hiszpański, francuski, niemiecki, włoski, mandarynki chines, japoński, rosyjski i portugalski (Brazylia).
* Mimo że Video Indexer obsługuje arabski (nowoczesny Standard i Levantine), hindi i koreański, te języki nie są obsługiwane w POKRYWie.
* Jeśli dźwięk zawiera języki inne niż lista obsługiwanych powyżej, wynik jest nieoczekiwany.
* Jeśli Video Indexer nie może zidentyfikować języka o wysokim stopniu pewności ( `>0.6` ), język rezerwowy jest w języku angielskim.
* Nie istnieje bieżąca obsługa plików z dźwiękiem w językach mieszanych. Jeśli dźwięk zawiera różne języki, wynik jest nieoczekiwany. 
* Jakość audio o niskiej jakości może mieć wpływ na wyniki modelu.
* Model wymaga co najmniej jednej minuty mowy w dźwięku.
* Model jest przeznaczony do rozpoznawania samodzielnej obsługi mowy (nie polecenia głosowe, zarejestrowaniem itp.).

## <a name="next-steps"></a>Następne kroki

* [Omówienie](video-indexer-overview.md)
* [Automatyczne identyfikowanie i transkrybowanie zawartości w wielu językach](multi-language-identification-transcription.md)
