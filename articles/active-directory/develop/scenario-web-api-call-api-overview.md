---
title: Tworzenie interfejsu API sieci Web, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (omówienie).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a66f0a2de1d8239baffbe53dfb5d6f2dd275d448
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756337"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenariusz: internetowy interfejs API, który wywołuje interfejsy API sieci Web

Dowiedz się, co musisz wiedzieć, aby utworzyć interfejs API sieci Web, który wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

W tym scenariuszu, w którym chroniony internetowy interfejs API wywołuje inne interfejsy API sieci Web, kompiluje w [scenariuszu: chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Omówienie

- Klient sieci Web, pulpitu, aplikacji mobilnej lub jednostronicowej (niereprezentowanej na towarzyszącym diagramie) wywołuje chroniony internetowy interfejs API i udostępnia token okaziciela sieci Web w formacie JSON (JWT) w nagłówku HTTP "Authorization".
- Chroniony internetowy interfejs API sprawdza token i używa metody Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` w celu zażądania innego tokenu z Azure Active Directory (Azure AD), aby chroniony internetowy interfejs API mógł wywołać drugi internetowy interfejs API lub podrzędny interfejs API sieci Web w imieniu użytkownika.
- Chroniony internetowy interfejs API może również wywołać `AcquireTokenSilent` później, aby zażądać tokenów innych podrzędnych interfejsów API w imieniu tego samego użytkownika. `AcquireTokenSilent` Odświeża token w razie konieczności.

![Diagram interfejsu API sieci Web wywołującego interfejs API sieci Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Szczegółowych informacji

Część rejestracji aplikacji, która jest powiązana z uprawnieniami interfejsu API, jest klasyczna. Konfiguracja aplikacji obejmuje używanie przepływu OAuth 2,0 w imieniu użytkownika do wymiany tokenu okaziciela JWT z tokenem dla podrzędnego interfejsu API. Token ten jest dodawany do pamięci podręcznej tokenów, gdzie jest dostępny w kontrolerach internetowego interfejsu API, a następnie może uzyskać token dyskretnie wywołujący podrzędne interfejsy API.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](scenario-web-api-call-api-app-registration.md).
