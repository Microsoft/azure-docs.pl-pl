---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954967"
---
## <a name="enable-logging"></a>Włącz rejestrowanie

Aby ułatwić debugowanie i rozwiązywanie problemów z błędami uwierzytelniania, biblioteka uwierzytelniania firmy Microsoft zapewnia wbudowaną obsługę rejestrowania. Rejestrowanie to każda biblioteka została omówiona w następujących artykułach:

:::row:::
    :::column:::
        - [Rejestrowanie na platformie MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Rejestrowanie w bibliotece MSAL dla systemu Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Rejestrowanie w bibliotece MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Rejestrowanie w bibliotece MSAL dla systemu iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Rejestrowanie w bibliotece MSAL dla języka Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Rejestrowanie w bibliotece MSAL dla języka Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Oto kilka sugestii dotyczących zbierania danych:

- Użytkownicy mogą poproszeni o pomoc w przypadku wystąpienia problemów. Najlepszym rozwiązaniem jest przechwycenie i tymczasowe przechowywanie dzienników. Podaj lokalizację, w której użytkownicy mogą przekazywać dzienniki. MSAL udostępnia rozszerzenia rejestrowania do przechwytywania szczegółowych informacji o uwierzytelnianiu.

- Jeśli dane telemetryczne są dostępne, włącz je za poorednictwem MSAL w celu zebrania danych dotyczących sposobu logowania użytkowników do aplikacji.


## <a name="validate-your-integration"></a>Weryfikowanie integracji

Przetestuj integrację, postępując zgodnie z [listą kontrolną integracji z platformą Microsoft Identity](../articles/active-directory/develop/identity-platform-integration-checklist.md).
