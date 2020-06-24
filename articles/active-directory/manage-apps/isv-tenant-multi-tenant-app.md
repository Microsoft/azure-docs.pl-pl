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
ms.openlocfilehash: 1a0b63c130d7d1e72bd3320e40213ae3cb1069a6
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763248"
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

   b. W **przypadku aplikacji opartych na protokole SAML i WS-** based aplikacje można [konfigurować](configure-single-sign-on-non-gallery-applications.md) za pomocą ogólnego szablonu SAML w usłudze Azure AD.

W razie potrzeby można również [przekonwertować aplikację z jedną dzierżawą na wiele dzierżawców](../develop/howto-convert-app-to-be-multi-tenant.md) .

## <a name="next-steps"></a>Następne kroki

[Integruj Logowanie jednokrotne w aplikacji](isv-sso-content.md)
