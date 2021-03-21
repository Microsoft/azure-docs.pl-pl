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
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675961"
---
# <a name="prepare-mobile-apps-for-production"></a>Przygotuj aplikacje mobilne dla środowiska produkcyjnego

Ten artykuł zawiera szczegółowe informacje na temat ulepszania jakości i niezawodności aplikacji mobilnej przed przeniesieniem ich do środowiska produkcyjnego.

## <a name="handle-errors"></a>Obsługa błędów

Podczas przygotowywania aplikacji mobilnej do produkcji może wystąpić kilka warunków błędu. Główne przypadki, które będą obsługiwane, to ciche awarie i powrotu do interakcji. Inne warunki, które należy wziąć pod uwagę, obejmują między innymi sytuacje braku sieci, awarię usługi, wymagania dotyczące zgody administratora oraz inne przypadki specyficzne dla scenariusza.

Dla każdego typu biblioteki uwierzytelniania firmy Microsoft (MSAL) można znaleźć przykładowy kod i zawartość typu wiki opisującą sposób obsługi warunków błędów:

- [MSAL wiki systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL wiki systemu iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

Aby wypróbować dodatkowe przykłady, zobacz [publiczne aplikacje dla komputerów stacjonarnych i mobilnych](sample-v2-code.md#desktop-and-mobile-public-client-apps).