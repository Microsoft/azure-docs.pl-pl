---
title: Przygotowanie aplikacji mobilnej — wywoływanie interfejsów API sieci Web dla środowiska produkcyjnego | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web. (Przygotuj aplikacje dla środowiska produkcyjnego).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882543"
---
# <a name="prepare-mobile-apps-for-production"></a>Przygotuj aplikacje mobilne dla środowiska produkcyjnego

Ten artykuł zawiera szczegółowe informacje na temat ulepszania jakości i niezawodności aplikacji mobilnej przed przeniesieniem ich do środowiska produkcyjnego.

## <a name="handle-errors"></a>Obsługa błędów

Podczas przygotowywania aplikacji mobilnej do produkcji może wystąpić kilka warunków błędu. Główne przypadki, które będą obsługiwane, to ciche awarie i powrotu do interakcji. Inne warunki, które należy wziąć pod uwagę, obejmują między innymi sytuacje braku sieci, awarię usługi, wymagania dotyczące zgody administratora oraz inne przypadki specyficzne dla scenariusza.

Dla każdego typu biblioteki uwierzytelniania firmy Microsoft (MSAL) można znaleźć przykładowy kod i zawartość typu wiki opisującą sposób obsługi warunków błędów:

- [MSAL wiki systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL wiki systemu iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Eliminowanie i badanie problemów

Aby lepiej zdiagnozować problemy w aplikacji, Zbierz dane. Aby uzyskać informacje na temat rodzajów danych, które można zbierać, zobacz [Rejestrowanie w aplikacjach MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Oto kilka sugestii dotyczących zbierania danych:

- Użytkownicy mogą poproszeni o pomoc w przypadku wystąpienia problemów. Najlepszym rozwiązaniem jest przechwycenie i tymczasowe przechowywanie dzienników. Podaj lokalizację, w której użytkownicy mogą przekazywać dzienniki. MSAL udostępnia rozszerzenia rejestrowania do przechwytywania szczegółowych informacji o uwierzytelnianiu.

- Jeśli dane telemetryczne są dostępne, włącz je za poorednictwem MSAL w celu zebrania danych dotyczących sposobu logowania użytkowników do aplikacji.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Aby wypróbować dodatkowe przykłady, zobacz [publiczne aplikacje dla komputerów stacjonarnych i mobilnych](sample-v2-code.md#desktop-and-mobile-public-client-apps).
