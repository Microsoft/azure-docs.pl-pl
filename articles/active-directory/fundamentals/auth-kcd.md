---
title: Ograniczone delegowanie protokołu Kerberos za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania ograniczonego delegowania protokołu Kerberos za pomocą Azure Active Directory.
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
ms.openlocfilehash: a1c7aa4d2300a6dee44da067b122fc7af97f7aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172860"
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

* [Logowanie jednokrotne w aplikacjach z użyciem ograniczonego delegowania Kerberos dzięki funkcji serwera proxy aplikacji](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md)

