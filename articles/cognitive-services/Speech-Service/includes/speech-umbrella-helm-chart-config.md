---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły dotyczące opcji konfiguracji grafu Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80874357"
---
### <a name="speech-umbrella-chart"></a>Speech (wykres parasol)

Wartości na wykresie "parasol" najwyższego poziomu przesłaniają odpowiednie wartości wykresów podrzędnych. W związku z tym w tym miejscu należy dodać wszystkie lokalne, dostosowane wartości.

|Parametr|Opis|Domyślne|
| -- | -- | -- | -- |
| `speechToText.enabled` | Określa, czy jest włączona usługa **zamiany mowy na tekst** . | `true` |
| `speechToText.verification.enabled` | Określa, czy `helm test` Funkcja **zamiany mowy na tekst** jest włączona. | `true` |
| `speechToText.verification.image.registry` | Repozytorium obrazów platformy Docker `helm test` używane do testowania usługi **zamiany mowy na tekst** . Helm tworzy oddzielny pod względem klastra w celu *przetestowania i ściąga obraz z* tego rejestru. | `docker.io` |
| `speechToText.verification.image.repository` | Repozytorium obrazów platformy Docker `helm test` używane do testowania usługi **zamiany mowy na tekst** . Test Helm korzysta z tego repozytorium do ściągania obrazu z *użyciem testu* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Tag obrazu platformy Docker używany z `helm test` usługą **zamiany mowy na tekst** . Test Helm wykorzystuje ten tag do ściągania obrazu z *użyciem testu* . | `latest` |
| `speechToText.verification.image.pullByHash` | Czy obraz platformy Docker *test-use* jest ściągany przez skrót. If `true` `speechToText.verification.image.hash` należy dodać, używając prawidłowej wartości skrótu obrazu. | `false` |
| `speechToText.verification.image.arguments` | Argumenty używane do wykonywania *testu — Użyj* obrazu platformy Docker. Helm test pod przekazuje te argumenty do kontenera podczas uruchamiania `helm test` . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Czy usługa **zamiany tekstu na mowę** jest włączona. | `true` |
| `textToSpeech.verification.enabled` | Określa, czy `helm test` Funkcja **zamiany mowy na tekst** jest włączona. | `true` |
| `textToSpeech.verification.image.registry` | Repozytorium obrazów platformy Docker `helm test` używane do testowania usługi **zamiany mowy na tekst** . Helm tworzy oddzielny pod względem klastra w celu *przetestowania i ściąga obraz z* tego rejestru. | `docker.io` |
| `textToSpeech.verification.image.repository` | Repozytorium obrazów platformy Docker `helm test` używane do testowania usługi **zamiany mowy na tekst** . Test Helm korzysta z tego repozytorium do ściągania obrazu z *użyciem testu* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Tag obrazu platformy Docker używany z `helm test` usługą **zamiany mowy na tekst** . Test Helm wykorzystuje ten tag do ściągania obrazu z *użyciem testu* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Czy obraz platformy Docker *test-use* jest ściągany przez skrót. If `true` `textToSpeech.verification.image.hash` należy dodać, używając prawidłowej wartości skrótu obrazu. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty, które mają zostać wykonane przy użyciu obrazu platformy Docker *test-use* . Test Helm pod przekazaniem tych argumentów do kontenera podczas uruchamiania `helm test` . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |