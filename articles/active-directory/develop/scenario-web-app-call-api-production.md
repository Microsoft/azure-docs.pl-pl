---
title: Przenieś do środowiska produkcyjnego aplikacji sieci Web, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przejść do środowiska produkcyjnego aplikacji sieci Web, która wywołuje interfejsy API sieci Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675944"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę podczas przesuwania aplikacji do środowiska produkcyjnego.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej, pobierając pełny, progresywny samouczek dotyczący ASP.NET Core aplikacji sieci Web. Samouczek:

- Pokazuje, jak podpisać użytkowników w wielu odbiorcach lub w chmurach narodowych lub przy użyciu tożsamości społecznościowych.
- Wywołania Microsoft Graph.
- Wywołuje kilka interfejsów API firmy Microsoft.
- Obsługuje przyrostową zgodę.
- Wywołuje własny internetowy interfejs API.

[Samouczek aplikacji sieci Web ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
