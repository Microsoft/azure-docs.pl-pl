---
title: 'Szybki Start: Rozpoznawanie mowy z pliku audio — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400526"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku dźwiękowego. Po spełnieniu kilku wymagań wstępnych, rozpoznawanie mowy z pliku obejmuje tylko kilka kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `AudioConfig` obiekt, który określa. Nazwa pliku WAV.
> * Utwórz `SpeechRecognizer` Obiekt przy użyciu obiektów `SpeechConfig` i `AudioConfig` z powyższych.
> * Za pomocą `SpeechRecognizer` obiektu, Rozpocznij proces rozpoznawania dla pojedynczego wypowiedź.
> * Sprawdź zwracaną wartość `SpeechRecognitionResult` .
