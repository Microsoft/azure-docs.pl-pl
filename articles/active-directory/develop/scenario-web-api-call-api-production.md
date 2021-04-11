---
title: Przenoszenie internetowego interfejsu API wywoływanie interfejsów API sieci Web do środowiska produkcyjnego | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przenieść internetowy interfejs API, który wywołuje interfejsy API sieci Web w środowisku produkcyjnym.
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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675876"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Przenieś do środowiska produkcyjnego

Po uzyskaniu tokenu do wywoływania interfejsów API sieci Web należy wziąć pod uwagę następujące kwestie, które należy rozważyć podczas przesuwania aplikacji do środowiska produkcyjnego.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawowe informacje o sposobie wywoływania interfejsów API sieci Web z własnego interfejsu API sieci Web, być może zainteresuje Cię Poniższy samouczek, w którym opisano kod używany do tworzenia chronionego internetowego interfejsu API, który wywołuje interfejsy API sieci Web.

| Przykład | Platforma | Opis |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-samouczek](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) — rozdział 1 | ASP.NET Core Web API, Desktop (WPF) | ASP.NET Core wywołań interfejsu Web API Microsoft Graph, które są wywoływane z aplikacji WPF przy użyciu platformy tożsamości firmy Microsoft. |
