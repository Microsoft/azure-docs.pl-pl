---
title: Synchronizacja katalogów za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury w celu osiągnięcia synchronizacji katalogów za pomocą Azure Active Directory.
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
ms.openlocfilehash: 748f91b2fe77667969e9736f8084a9dd24018425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172473"
---
# <a name="directory-synchronization"></a>Synchronizacja katalogów

Wiele organizacji ma infrastrukturę hybrydową obejmującą zarówno składniki lokalne, jak i w chmurze. Synchronizowanie tożsamości użytkowników między katalogami lokalnymi i w chmurze umożliwia użytkownikom dostęp do zasobów przy użyciu jednego zestawu poświadczeń. 

Synchronizacja jest procesem 

* Tworzenie obiektu na podstawie określonych warunków
* Aktualizowanie obiektu
* Usuwanie obiektu, gdy warunki nie są już spełnione. 

Lokalna obsługa administracyjna obejmuje Inicjowanie obsługi administracyjnej ze źródeł lokalnych (takich jak Active Directory) Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Zastosowania

Należy synchronizować dane tożsamości z lokalnych środowisk Active Directory do usługi Azure AD.

![Diagram architektoniczny](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: uzyskuje dostęp do aplikacji za pomocą usługi Azure AD.

* **Przeglądarka sieci Web**: składnik, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji.

* **Aplikacja**: aplikacja sieci Web, która korzysta z usługi Azure AD na potrzeby uwierzytelniania i autoryzacji.

* **Azure AD**: synchronizuje informacje o tożsamościach z katalogu lokalnego organizacji za pośrednictwem Azure AD Connect. 

* **Azure AD Connect**: Narzędzie do łączenia się z lokalnymi infrastrukturami tożsamości w celu Microsoft Azure AD. Kreator i środowisko z przewodnikiem pomagają wdrożyć i skonfigurować wymagania wstępne oraz składniki wymagane do połączenia, w tym synchronizację i logowanie się z aktywnych katalogów do usługi Azure AD. 

* **Active Directory**: Active Directory to usługa katalogowa dostępna w większości systemów operacyjnych Windows Server. Serwery z systemem Active Directory Domain Services (AD DS) są nazywane kontrolerami domeny. Uwierzytelniają i autoryzują wszystkich użytkowników i komputery w domenie.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implementowanie synchronizacji katalogów za pomocą usługi Azure AD

* [Co to jest aprowizacja tożsamości?](../cloud-provisioning/what-is-provisioning.md) 

* [Narzędzia integracji katalogu tożsamości hybrydowej](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Plan instalacji Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md)