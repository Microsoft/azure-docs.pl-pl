---
title: Używanie Video Indexer do automatycznego identyfikowania języków mówionych — Azure
titleSuffix: Azure Media Services
description: W tym artykule opisano sposób Video Indexer identyfikacji języka do automatycznego identyfikowania języka mówionego w wideo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 40f2e146956919e154f59d90b56a1b03379abbb2
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600641"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Automatyczne identyfikowanie języka mówionego przy użyciu modelu identyfikacji języka

Video Indexer obsługuje automatyczną identyfikację języka (LID, automatic language identification), czyli proces automatycznego identyfikowania zawartości języka mówionego z dźwięku i wysyłania pliku multimedialnego do transkrypcji w dominującym zidentyfikowanym języku. 

Obecnie program LID obsługuje: angielski, hiszpański, francuski, niemiecki, włoski, chiński mandaryński, japoński, rosyjski i portugalski (Brazylia). 

Zapoznaj się z [sekcją Wytyczne i ograniczenia](#guidelines-and-limitations) poniżej.

## <a name="choosing-auto-language-identification-on-indexing"></a>Wybieranie automatycznej identyfikacji języka podczas indeksowania

Podczas indeksowania [lub ponownego indeksowania wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) przy użyciu interfejsu API wybierz opcję w `auto detect` `sourceLanguage` parametrze .

W przypadku korzystania z  portalu przejdź [](https://www.videoindexer.ai/) do filmów wideo konta na Video Indexer głównej aplikacji i umieść kursor nad nazwą wideo, które chcesz ponownie indeksować. W prawym dolnym rogu kliknij przycisk ponownego indeksowania. W **oknie dialogowym Re-index video** (Ponowne indeksowanie wideo) wybierz pozycję *Auto detect (Wykryj* automatycznie) z listy rozwijanej **Video source language (Język** źródłowy wideo).

![Autowykrywanie](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Dane wyjściowe modelu

Video Indexer transkrybuje wideo zgodnie z najbardziej prawdopodobnym językiem, jeśli pewność tego języka to `> 0.6` . Jeśli nie można zidentyfikować języka z pewnością, zakłada się, że język mówiony jest angielski. 

Dominujący język modelu jest dostępny w pliku JSON szczegółowych informacji jako atrybut (w katalogu `sourceLanguage` głównym/wideo/szczegółowych informacjach). Odpowiedni wynik ufności jest również dostępny w ramach `sourceLanguageConfidence` atrybutu .

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

    angielski, hiszpański, francuski, niemiecki, włoski, mandaryński, japoński, rosyjski i portugalski (Brazylia).
* Mimo że Video Indexer język arabski (Modern Standard i Format), hebrajski i koreański, te języki nie są obsługiwane w wersji POKRYWY.
* Jeśli dźwięk zawiera języki inne niż obsługiwana lista powyżej, wynik jest nieoczekiwany.
* Jeśli Video Indexer nie może zidentyfikować języka z wystarczająco dużym poziomem pewności (), językiem `>0.6` rezerwowym jest angielski.
* Nie ma bieżącej obsługi plików z dźwiękami w różnych językach. Jeśli dźwięk zawiera języki mieszane, wynik jest nieoczekiwany. 
* Dźwięk niskiej jakości może mieć wpływ na wyniki modelu.
* Model wymaga co najmniej jednej minuty mowy w dźwięku.
* Model zaprojektowano tak, aby rozpoznawał mowę konwersacyjną (a nie polecenia głosowe, mowę itp.).

## <a name="next-steps"></a>Następne kroki

* [Omówienie](video-indexer-overview.md)
* [Automatyczne identyfikowanie i transkrybowanie zawartości w wielu językach](multi-language-identification-transcription.md)
