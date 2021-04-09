---
title: Przenoszenie jednostronicowej aplikacji do środowiska produkcyjnego
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954968"
---
# <a name="single-page-application-move-to-production"></a>Aplikacja jednostronicowa: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę podczas przesuwania aplikacji do środowiska produkcyjnego.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Wdrażanie aplikacji

Zapoznaj się z [przykładem wdrożenia](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) , aby dowiedzieć się, jak WDROŻYĆ projekty spa i interfejsów API sieci Web za pomocą usługi Azure Storage i platformy Azure App Services odpowiednio. 

## <a name="code-samples"></a>Przykłady kodu

Te przykłady kodu przedstawiają kilka operacji kluczowych dla aplikacji jednostronicowej.
- [Spa z zapleczem ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): jak uzyskać tokeny dla własnego internetowego interfejsu API zaplecza (ASP.NET Core) za pomocą **MSAL.js**.

- [Node.js Web API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): sprawdzanie poprawności tokenów dostępu dla internetowego interfejsu API sieci web (Node.js) za pomocą **usługi Passport-Azure-AD**.

- [Spa z Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): jak używać **MSAL.js** do logowania użytkowników w aplikacji, która jest zarejestrowana w **Azure Active Directory B2C** (Azure AD B2C).

- [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): jak używać **usługi Passport-Azure-AD** do weryfikowania tokenów dostępu dla aplikacji zarejestrowanych w **Azure Active Directory B2C** (Azure AD B2C).

## <a name="next-steps"></a>Następne kroki

- [Samouczek Spa JavaScript](./tutorial-v2-javascript-spa.md): głębokie szczegółowe do logowania użytkowników i uzyskiwania tokenu dostępu do wywoływania **interfejsu API Microsoft Graph** przy użyciu **MSAL.js**.