---
title: 'Szybki Start: Tłumaczenie mowy na wiele języków — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980808"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych przetłumaczenie mowy na tekst w wielu językach obejmuje tylko sześć kroków:
> [!div class="checklist"]
> * Utwórz ````SpeechTranslationConfig```` obiekt z klucza subskrypcji i regionu.
> * Zaktualizuj ````SpeechTranslationConfig```` obiekt, aby określić język źródłowy rozpoznawania mowy.
> * Zaktualizuj ````SpeechTranslationConfig```` obiekt, aby określić wiele języków docelowych tłumaczenia.
> * Utwórz ````TranslationRecognizer```` Obiekt przy użyciu ````SpeechTranslationConfig```` obiektu powyższego.
> * Za pomocą ````TranslationRecognizer```` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź zwracaną wartość ````TranslationRecognitionResult```` .
