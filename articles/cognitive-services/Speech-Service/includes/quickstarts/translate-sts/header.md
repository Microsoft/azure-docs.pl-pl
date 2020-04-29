---
title: 'Szybki Start: Tłumaczenie mowy na mowę — usługa mowy'
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
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980687"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby interaktywnie przetłumaczyć mowę z jednego języka na mowę w innym języku. Po spełnieniu kilku wymagań wstępnych, Tłumaczenie mowy na mowę obejmuje sześć kroków:
> [!div class="checklist"]
> * Utwórz ````SpeechTranslationConfig```` obiekt z klucza subskrypcji i regionu.
> * Zaktualizuj ````SpeechTranslationConfig```` obiekt, aby określić języki źródłowe i docelowe.
> * Zaktualizuj ````SpeechTranslationConfig```` obiekt, aby określić nazwę głosu wyjściowego mowy.
> * Utwórz ````TranslationRecognizer```` Obiekt przy użyciu ````SpeechTranslationConfig```` obiektu powyższego.
> * Za pomocą ````TranslationRecognizer```` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź zwracaną wartość ````TranslationRecognitionResult```` .
