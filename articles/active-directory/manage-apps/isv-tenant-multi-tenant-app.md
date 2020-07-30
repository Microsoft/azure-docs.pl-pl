---
title: Tworzenie dzierżawy platformy Azure dla aplikacji z wieloma dzierżawcami
description: Wskazówki dla niezależnych dostawców oprogramowania w ramach integracji z usługą Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 264d4afb50904be17624f8801b7b79fe4b232030
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386813"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Tworzenie dzierżawy platformy Azure dla aplikacji z wieloma dzierżawcami  

Aby zapewnić dostęp do aplikacji z wieloma dzierżawcami, musisz utworzyć dzierżawę Azure Active Directory, aby zarejestrować aplikację i włączyć federacji tożsamości klienta. Zobacz [Wybieranie odpowiedniego protokołu federacyjnego dla aplikacji](isv-choose-multi-tenant-federation.md)wielodostępnej. Ta dzierżawa umożliwi przetestowanie aplikacji i Federacji w środowisku podobnym do Twoich klientów w środowiskach usługi Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Koszty hostingu aplikacji wielodostępnej

Azure Active Directory jest dostępny w wielu wersjach. [Zobacz szczegółowe porównanie funkcji](https://azure.microsoft.com/pricing/details/active-directory/).

Możesz bezpłatnie utworzyć subskrypcję platformy Azure i usługę Azure Active Directory oraz korzystać z funkcji podstawowych.

## <a name="create-your-tenant"></a>Tworzenie dzierżawy

1. Utwórz dzierżawę. Zobacz [Konfigurowanie środowiska deweloperskiego](../develop/quickstart-create-new-tenant.md).

2. Włącz i przetestuj dostęp jednokrotny do swojej aplikacji,

   a. **W przypadku aplikacji OIDC lub Oath** [zarejestruj aplikację](../develop/quickstart-register-app.md) jako aplikację wielodostępną. Wybierz konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft w obszarze obsługiwane typy kont

   b. W **przypadku aplikacji opartych na protokole SAML i WS-** based aplikacje można [konfigurować](configure-saml-single-sign-on.md) za pomocą ogólnego szablonu SAML w usłudze Azure AD.

W razie potrzeby można również [przekonwertować aplikację z jedną dzierżawą na wiele dzierżawców](../develop/howto-convert-app-to-be-multi-tenant.md) .

## <a name="next-steps"></a>Następne kroki

[Integruj Logowanie jednokrotne w aplikacji](isv-sso-content.md)
