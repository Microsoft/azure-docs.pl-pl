---
title: Uwierzytelnianie oparte na nagłówkach z Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania uwierzytelniania opartego na nagłówkach za pomocą Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69e87fc919893a544f5d0b1b615a110f25486e57
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168750"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Uwierzytelnianie oparte na nagłówkach z Azure Active Directory

Starsze aplikacje zwykle korzystają z uwierzytelniania opartego na nagłówkach. W tym scenariuszu użytkownik (lub nadawca wiadomości) uwierzytelnia się w rozwiązaniu tożsamości pośredniczącej. Rozwiązanie pośredniczące uwierzytelnia użytkownika i propaguje wymagane nagłówki protokołu HTTP (Hypertext Transfer Protocol) do docelowej usługi sieci Web. Azure Active Directory (AD) obsługuje ten wzorzec za pośrednictwem usługi serwera proxy aplikacji i integracji z innymi rozwiązaniami kontrolera sieci. 

W naszym rozwiązaniu serwer proxy aplikacji zapewnia zdalny dostęp do aplikacji, uwierzytelnia użytkownika i przekazuje nagłówki wymagane przez aplikację. 

## <a name="use-when"></a>Zastosowania

Użytkownicy zdalni muszą bezpiecznie zalogować się przy użyciu logowania jednokrotnego do aplikacji lokalnych, które wymagają uwierzytelniania opartego na nagłówkach.

![Uwierzytelnianie oparte na nagłówku obrazu architektonicznego](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: uzyskuje dostęp do starszych aplikacji obsłużonych przez serwer proxy aplikacji.

* **Przeglądarka sieci Web**: składnik, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji.

* **Azure AD**: uwierzytelnia użytkownika. 

* **Usługa serwera proxy aplikacji**: działa jako zwrotny serwer proxy w celu wysłania żądania od użytkownika do aplikacji lokalnej. Znajduje się w usłudze Azure AD i może również wymuszać wszelkie zasady dostępu warunkowego.

* **Łącznik serwera proxy aplikacji**: zainstalowane lokalnie na serwerach z systemem Windows w celu zapewnienia łączności z aplikacjami. Używa tylko połączeń wychodzących. Zwraca odpowiedź do usługi Azure AD.

* **Starsze aplikacje**: aplikacje odbierające żądania użytkowników z serwera proxy aplikacji. Starsza aplikacja odbiera wymagane nagłówki HTTP w celu skonfigurowania sesji i zwrócenia odpowiedzi. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementowanie uwierzytelniania opartego na nagłówkach za pomocą usługi Azure AD

* [Dodawanie aplikacji lokalnej dla dostępu zdalnego za pomocą serwera proxy aplikacji w usłudze Azure AD](../manage-apps/application-proxy-add-on-premises-application.md)  

* [Logowanie jednokrotne z uwierzytelnianiem na podstawie nagłówka dzięki funkcji serwera proxy aplikacji i narzędziu PingAccess](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) 

* [Bezpieczne starsze aplikacje dzięki kontrolerom dostarczania aplikacji i sieciom](../manage-apps/secure-hybrid-access.md)