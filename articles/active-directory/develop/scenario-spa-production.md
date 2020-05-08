---
title: Przenoszenie aplikacji jednostronicowej do środowiska produkcyjnego — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (Przenieś do środowiska produkcyjnego)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 777f3de8f2872e378fe30cc50ee0a5eb3823a625
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900311"
---
# <a name="single-page-application-move-to-production"></a>Aplikacja jednostronicowa: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, Dowiedz się, jak przejść do środowiska produkcyjnego.

## <a name="improve-your-app"></a>Ulepszanie aplikacji

[Włącz rejestrowanie](msal-logging.md) , aby przygotować aplikację do produkcji.

## <a name="test-your-integration"></a>Testowanie integracji

Przetestuj integrację, postępując zgodnie z [listą kontrolną integracji z platformą Microsoft Identity](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Następne kroki

Głębokie szczegółowe przykładu szybkiego startu, w którym wyjaśniono kod sposobu logowania użytkowników i uzyskiwania tokenu dostępu do wywoływania interfejsu API Microsoft Graph przy użyciu MSAL. js:

> [!div class="nextstepaction"]
> [Samouczek dotyczący SPA JavaScript](./tutorial-v2-javascript-spa.md)

Przykład demonstrujący, jak uzyskać tokeny dla własnego interfejsu API sieci Web zaplecza przy użyciu MSAL. js:

> [!div class="nextstepaction"]
> [SPA z ASP.NET zaplecza](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Przykład, który pokazuje, jak używać MSAL. js do logowania użytkowników w aplikacji, która jest zarejestrowana w Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA z Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
