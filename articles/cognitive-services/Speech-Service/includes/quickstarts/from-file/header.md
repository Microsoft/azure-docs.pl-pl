---
title: 'Szybki start: rozpoznawanie mowy z pliku audio — usługa mowy'
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400526"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do rozpoznawania mowy z pliku audio. Po spełnieniu kilku wymagań wstępnych rozpoznawanie mowy z pliku wymaga tylko kilku kroków:
> [!div class="checklist"]
> * Utwórz `SpeechConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `AudioConfig` obiekt określający obiekt . Nazwa pliku WAV.
> * Utwórz `SpeechRecognizer` obiekt `SpeechConfig` przy `AudioConfig` użyciu obiektów i obiektów z góry.
> * Za `SpeechRecognizer` pomocą obiektu, uruchom proces rozpoznawania dla pojedynczej wypowiedź.
> * Sprawdź `SpeechRecognitionResult` zwrócony.
