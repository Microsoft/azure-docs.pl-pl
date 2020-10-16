---
title: Ograniczone delegowanie protokołu Kerberos za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące osiągania tego wzorca uwierzytelniania
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
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114461"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Uwierzytelnianie systemu Windows — ograniczone delegowanie protokołu Kerberos za pomocą Azure Active Directory

Ograniczone delegowanie protokołu Kerberos (KCD) zapewnia ograniczone delegowanie między zasobami i opiera się na nazwach zasad usługi. Wymaga to od administratorów domeny tworzenia delegowania i jest ograniczona do jednej domeny. KCD oparte na zasobach jest często używany jako sposób udostępniania uwierzytelniania Kerberos dla aplikacji sieci Web, która ma użytkowników w wielu domenach w lesie Active Directory.

Serwer proxy aplikacji usługi Azure Active Directory może zapewniać Logowanie jednokrotne (SSO) i dostęp zdalny do aplikacji opartych na KCD, które wymagają biletu Kerberos na potrzeby dostępu i ograniczonego delegowania protokołu Kerberos (KCD).

Możesz włączyć logowanie jednokrotne do lokalnych aplikacji KCD, które korzystają ze zintegrowanego uwierzytelniania systemu Windows (IWA), dając łączników serwera proxy aplikacji uprawnienia do personifikacji użytkowników w Active Directory. Łącznik serwera proxy aplikacji używa tego uprawnienia do wysyłania i odbierania tokenów w imieniu użytkownika.

## <a name="use-when"></a>Zastosowania

Istnieje konieczność zapewnienia dostępu zdalnego, ochrony przed uwierzytelnianiem wstępnym i zapewnienia logowania jednokrotnego do lokalnych aplikacji IWA.

![Diagram architektury](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: uzyskuje dostęp do starszej aplikacji obsługiwanej przez serwer proxy aplikacji.

* **Przeglądarka sieci Web**: składnik, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji.

* **Azure AD**: uwierzytelnia użytkownika. 

* **Usługa serwera proxy aplikacji**: działa jako zwrotny serwer proxy w celu wysłania żądania od użytkownika do aplikacji lokalnej. Znajduje się w usłudze Azure AD. Serwer proxy aplikacji może również wymusić wszelkie zasady dostępu warunkowego.

* **Łącznik serwera proxy aplikacji**: zainstalowane lokalnie na serwerach z systemem Windows w celu zapewnienia łączności z aplikacją. Zwraca odpowiedź do usługi Azure AD. Wykonuje negocjację KCD z Active Directory, personifikując użytkownika w celu uzyskania tokenu Kerberos do aplikacji.

* **Active Directory**: wysyła token Kerberos dla aplikacji do łącznika serwera proxy aplikacji.

* **Starsze aplikacje**: aplikacje odbierające żądania użytkowników z serwera proxy aplikacji. Starsze aplikacje zwracają odpowiedź do łącznika serwera proxy aplikacji.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementowanie uwierzytelniania systemu Windows (KCD) przy użyciu usługi Azure AD

* [Logowanie jednokrotne w aplikacjach z użyciem ograniczonego delegowania Kerberos dzięki funkcji serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
