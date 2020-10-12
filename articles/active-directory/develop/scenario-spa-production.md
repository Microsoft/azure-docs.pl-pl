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
ms.openlocfilehash: 21ba0193c3f1e19ffc74452aaceee34759c7e606
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949018"
---
# <a name="single-page-application-move-to-production"></a>Aplikacja jednostronicowa: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, Dowiedz się, jak przejść do środowiska produkcyjnego.

## <a name="improve-your-app"></a>Ulepszanie aplikacji

[Włącz rejestrowanie](msal-logging.md) , aby przygotować aplikację do produkcji.

## <a name="test-your-integration"></a>Testowanie integracji

Przetestuj integrację, postępując zgodnie z [listą kontrolną integracji z platformą Microsoft Identity](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Wdrażanie aplikacji

Zapoznaj się z [przykładem wdrożenia](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) , aby dowiedzieć się, jak WDROŻYĆ projekty spa i interfejsów API sieci Web za pomocą usługi Azure Storage i platformy Azure App Services odpowiednio. 

## <a name="next-steps"></a>Następne kroki

Głębokie szczegółowe przykładu szybkiego startu, w którym wyjaśniono kod sposobu logowania użytkowników i uzyskiwania tokenu dostępu do wywoływania **interfejsu API Microsoft Graph** przy użyciu **MSAL.js**:

> [!div class="nextstepaction"]
> [Samouczek dotyczący SPA JavaScript](./tutorial-v2-javascript-spa.md)

Przykład demonstrujący, jak uzyskać tokeny dla własnego internetowego interfejsu API zaplecza (ASP.NET Core) za pomocą **MSAL.js**:

> [!div class="nextstepaction"]
> [SPA z ASP.NET zaplecza](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Przykład, który pokazuje, jak sprawdzać poprawność tokenów dostępu dla internetowego interfejsu API sieci Web (Node.js) za pomocą **usługi Passport-Azure-AD**.

> [!div class="nextstepaction"]
> [ Interfejs API sieci WebNode.js (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)

Przykład, który pokazuje, jak używać **MSAL.js** do logowania użytkowników w aplikacji zarejestrowanej za pomocą **Azure Active Directory B2C** (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA z Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

Przykład, który pokazuje, jak używać **usługi Passport-Azure-AD** do weryfikowania tokenów dostępu dla aplikacji zarejestrowanych w **Azure Active Directory B2C** (Azure AD B2C)

> [!div class="nextstepaction"]
> [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
