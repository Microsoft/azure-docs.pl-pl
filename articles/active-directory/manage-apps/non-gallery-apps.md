---
title: Korzystanie z usługi Azure AD dla aplikacji, które nie znajdują się na liście w galerii aplikacji
description: Dowiedz się, jak zintegrować aplikacje niewymienione w galerii usługi Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 262c3a7c8a668ef25d092b5d523743e0ece89cc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88641147"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Korzystanie z usługi Azure AD dla aplikacji, które nie znajdują się na liście w galerii aplikacji

W oknie [Dodawanie aplikacji](add-application-portal.md) — szybki start dowiesz się, jak dodać aplikację do dzierżawy usługi Azure AD.

Oprócz opcji dostępnych w [galerii aplikacji usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)można dodać **aplikację bez galerii**. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Możliwości dla aplikacji, które nie są wymienione w galerii usługi Azure AD

Możesz dodać dowolną aplikację, która już istnieje w organizacji, lub dowolną aplikację innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. W zależności od [umowy licencyjnej](https://azure.microsoft.com/pricing/details/active-directory/)dostępne są następujące funkcje:

- Samoobsługowa integracja dowolnej aplikacji, która obsługuje [SAML (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) dostawców tożsamości (zainicjowanych przez program SP lub dostawcy tożsamości)
- Samoobsługowa integracja dowolnej aplikacji sieci Web, która ma stronę logowania opartą na języku HTML przy użyciu [logowania jednokrotnego opartego na hasłach](sso-options.md#password-based-sso)
- Samoobsługowe połączenie aplikacji korzystających z [systemu do obsługi międzydomenowego protokołu zarządzania tożsamościami (standard scim) na potrzeby aprowizacji użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Możliwość dodawania linków do dowolnej aplikacji w programie [uruchamiania aplikacji pakietu Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) lub [moich aplikacjach](sso-options.md#linked-sign-on)

Jeśli szukasz wskazówek dla deweloperów, jak zintegrować aplikacje niestandardowe z usługą Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md). Podczas tworzenia aplikacji, która korzysta z nowoczesnego protokołu, takiego jak [OpenID Connect Connect/OAuth](../develop/active-directory-v2-protocols.md) do uwierzytelniania użytkowników, możesz zarejestrować ją na platformie tożsamości firmy Microsoft, korzystając z [Rejestracje aplikacji](../develop/quickstart-register-app.md) środowiska w Azure Portal.

## <a name="next-steps"></a>Następne kroki

- [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)

