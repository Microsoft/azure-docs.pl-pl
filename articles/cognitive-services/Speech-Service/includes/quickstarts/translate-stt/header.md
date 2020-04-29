---
title: 'Szybki Start: translacja zamiany mowy na tekst — usługa mowy'
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980907"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na tekst w innym języku. Po spełnieniu kilku wymagań wstępnych, przetłumaczenie zamiany mowy na tekst obejmuje tylko pięć kroków:
> [!div class="checklist"]
> * Utwórz ````SpeechConfig```` obiekt z klucza subskrypcji i regionu.
> * Zaktualizuj ````SpeechConfig```` obiekt, aby określić języki źródłowe i docelowe.
> * Utwórz ````TranslationRecognizer```` Obiekt przy użyciu ````SpeechConfig```` obiektu powyższego.
> * Za pomocą ````TranslationRecognizer```` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź zwracaną wartość ````TranslationRecognitionResult```` .
