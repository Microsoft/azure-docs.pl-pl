---
title: Uwierzytelnianie oparte na hasłach z Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania uwierzytelniania opartego na hasłach z Azure Active Directory.
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
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172826"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Uwierzytelnianie oparte na hasłach z Azure Active Directory

Pojedyncze Sign-On oparte na haśle (SSO) korzysta z istniejącego procesu uwierzytelniania dla aplikacji. Po włączeniu logowania jednokrotnego opartego na haśle Azure Active Directory (Azure AD) zbiera, szyfruje i bezpiecznie przechowuje poświadczenia użytkownika w katalogu. Usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji, gdy użytkownik próbuje się zalogować.

Wybierz pozycję Logowanie jednokrotne oparte na hasłach, gdy aplikacja uwierzytelnia się przy użyciu nazwy użytkownika i hasła zamiast tokenów dostępu i nagłówków. Logowanie jednokrotne oparte na hasłach obsługuje dowolną aplikację opartą na chmurze, która ma stronę logowania opartą na języku HTML. 

## <a name="use-when"></a>Zastosowania

Należy chronić się przed uwierzytelnianiem wstępnym i zapewnić Logowanie jednokrotne za pomocą magazynu haseł do aplikacji sieci Web.

![Diagram architektoniczny](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: uzyskuje dostęp do utworzonej aplikacji opartej na aplikacjach lub bezpośrednio odwiedzając witrynę. 

* **Przeglądarka sieci Web**: składnik, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji. Użytkownik uzyskuje dostęp do aplikacji opartej na formularzach za pośrednictwem rozszerzenia moje aplikacje. 

* **Rozszerzenie moje aplikacje**: identyfikuje skonfigurowaną aplikację rejestracji jednokrotnej opartą na hasłach i dostarcza poświadczenia do formularza logowania. Rozszerzenie moje aplikacje jest zainstalowane w przeglądarce sieci Web. 

* **Azure AD**: uwierzytelnia użytkownika.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementowanie logowania jednokrotnego opartego na haśle za pomocą usługi Azure AD

* [Co to jest logowanie jednokrotne na podstawie hasła](../manage-apps/what-is-single-sign-on.md) 

* [Konfigurowanie logowania jednokrotnego na podstawie hasła dla aplikacji w chmurze ](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [Konfigurowanie logowania jednokrotnego opartego na hasłach dla aplikacji lokalnych przy użyciu serwera proxy aplikacji](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

