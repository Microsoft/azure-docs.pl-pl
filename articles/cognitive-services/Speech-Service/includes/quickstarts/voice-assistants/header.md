---
title: 'Szybki Start: Tworzenie niestandardowego asystenta głosowego — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241143"
---
W tym przewodniku szybki start użyjesz [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) , aby utworzyć niestandardową aplikację asystenta głosowego, która łączy się z bot, który został już utworzony i skonfigurowany. Jeśli musisz utworzyć bot, zapoznaj się z [samouczkiem](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) dotyczącym bardziej kompleksowego przewodnika.

Po spełnieniu kilku wymagań wstępnych połączenie niestandardowego asystenta głosowego zajmuje zaledwie kilka kroków:
> [!div class="checklist"]
> * Utwórz `BotFrameworkConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `DialogServiceConnector` Obiekt przy użyciu `BotFrameworkConfig` obiektu powyższego.
> * Za pomocą `DialogServiceConnector` obiektu, Rozpocznij proces nasłuchiwania dla jednego wypowiedź.
> * Sprawdź zwracaną wartość `ActivityReceivedEventArgs` .
