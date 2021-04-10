---
title: Dodawanie aplikacji wielodostępnej do galerii aplikacji usługi Azure AD
description: Wyjaśnia, w jaki sposób można wyświetlić listę niestandardowej aplikacji wielodostępnej w galerii aplikacji usługi Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ROBOTS: NOINDEX
ms.openlocfilehash: de0231a49c4f806660ea0cb305fdc1a70e2b36d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063131"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Dodawanie aplikacji wielodostępnej do galerii aplikacji w usłudze Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest Galeria aplikacji usługi Azure AD?

Azure Active Directory (Azure AD) to oparta na chmurze usługa tożsamości. [Galeria aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) znajduje się w sklepie z aplikacjami w portalu Azure Marketplace, w którym wszystkie łączniki aplikacji są publikowane pod kątem logowania jednokrotnego i aprowizacji użytkowników. Klienci korzystający z usługi Azure AD jako dostawcy tożsamości mogą znaleźć różne łączniki aplikacji SaaS opublikowane w tym miejscu. Administratorzy IT mogą dodawać łączniki z galerii aplikacji, a następnie konfigurować i używać łączników do logowania jednokrotnego i inicjowania obsługi. Usługa Azure AD obsługuje wszystkie główne protokoły federacyjne, w tym SAML 2,0, OpenID Connect Connect, OAuth i WS-Fed do logowania jednokrotnego. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Jeśli aplikacja obsługuje protokół SAML lub OpenIDConnect
Jeśli masz aplikację wielodostępną, którą chcesz znaleźć w galerii aplikacji usługi Azure AD, musisz najpierw upewnić się, że aplikacja obsługuje jedną z następujących technologii logowania jednokrotnego:

- **OpenID Connect Connect**: aby można było wyświetlić swoją aplikację, Utwórz aplikację wielodostępną w usłudze Azure AD i zaimplementuj [strukturę zgody na usługę Azure AD](./consent-framework.md) dla swojej aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego, aby klient mógł wyrazić zgodę na aplikację. Można kontrolować dostęp użytkownika na podstawie identyfikatora dzierżawy oraz nazwy UPN użytkownika otrzymanego w tokenie. Prześlij aplikację, wykonując czynności opisane w temacie Tworzenie [listy aplikacji w galerii aplikacji Azure Active Directory](./v2-howto-app-gallery-listing.md).

- **SAML**: Jeśli aplikacja obsługuje protokół SAML 2,0, aplikacja może być wymieniona w galerii. Postępuj zgodnie z instrukcjami wyświetlanymi w temacie Tworzenie [aplikacji w galerii aplikacji Azure Active Directory](./v2-howto-app-gallery-listing.md).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Jeśli aplikacja nie obsługuje języka SAML lub OpenIDConnect
Aplikacje, które nie obsługują protokołu SAML ani OpenIDConnect, mogą być nadal zintegrowane z galerią aplikacji za pomocą technologii logowania jednokrotnego przy użyciu hasła.

Logowanie jednokrotne haseł, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji. 

Jeśli chcesz wystawić swoją aplikację za pomocą tej technologii:
1. Utwórz aplikację sieci Web, która zawiera stronę logowania w formacie HTML, aby skonfigurować Logowanie jednokrotne przy użyciu [hasła](../manage-apps/what-is-single-sign-on.md). 
2. Prześlij żądanie zgodnie z opisem w temacie [wyświetlanie aplikacji w galerii aplikacji Azure Active Directory](./v2-howto-app-gallery-listing.md).

## <a name="escalations"></a>Ich eskalacji

W przypadku wszelkich eskalacji Wyślij wiadomość e-mail do [zespołu integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , a my powrócimy do Ciebie najszybciej, jak to możliwe.

## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak wystawić swoją aplikację w galerii aplikacji Azure Active Directory](./v2-howto-app-gallery-listing.md).