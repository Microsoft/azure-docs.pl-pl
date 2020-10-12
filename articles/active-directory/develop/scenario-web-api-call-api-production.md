---
title: Przenoszenie internetowego interfejsu API wywoływanie interfejsów API sieci Web do środowiska produkcyjnego Microsoft Identity platform | Azure
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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518202"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Przenieś do środowiska produkcyjnego

Po uzyskaniu tokenu do wywoływania interfejsów API sieci Web można przenieść aplikację do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Dowiedz się więcej

Teraz, gdy znasz podstawowe informacje o sposobie wywoływania interfejsów API sieci Web z własnego interfejsu API sieci Web, być może zainteresuje Cię Poniższy samouczek, w którym opisano kod używany do tworzenia chronionego internetowego interfejsu API, który wywołuje interfejsy API sieci Web.

| Przykład | Platforma | Opis |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-samouczek](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) — rozdział 1 | ASP.NET Core Web API, Desktop (WPF) | ASP.NET Core wywołań interfejsu Web API Microsoft Graph, które są wywoływane z aplikacji WPF przy użyciu platformy tożsamości firmy Microsoft (v 2.0). |
