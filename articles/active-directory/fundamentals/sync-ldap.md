---
title: Synchronizacja LDAP z Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące osiągania synchronizacji LDAP z Azure Active Directory.
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
ms.openlocfilehash: e617d7ccc14e65c18eb86877b1c7fb1aeef74cd0
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578896"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Synchronizacja LDAP z Azure Active Directory

Lightweight Directory Access Protocol (LDAP) to protokół usługi katalogowej, który działa na stosie TCP/IP. Zapewnia mechanizm służący do nawiązywania połączeń z katalogami internetowymi, wyszukiwania i modyfikowania katalogów internetowych. Usługa katalogu LDAP jest oparta na modelu klient-serwer i jego funkcja ma umożliwić dostęp do istniejącego katalogu. Wiele firm zależą od lokalnych serwerów LDAP do przechowywania użytkowników i grup dla ich krytycznych aplikacji firmowych. 

Azure Active Directory (Azure AD) może zastąpić synchronizację LDAP z Azure AD Connect. Usługa synchronizacji Azure AD Connect wykonuje wszystkie operacje związane z synchronizacją danych tożsamości między środowiskami lokalnymi i usługą Azure AD. 

## <a name="use-when"></a>Zastosowania

Należy synchronizować dane tożsamości między lokalnymi katalogami LDAP v3 a usługą Azure AD. 

![Diagram architektoniczny](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik** : uzyskuje dostęp do aplikacji, która korzysta z katalogu LDAP v3 do sortowania użytkowników i haseł.

* **Przeglądarka sieci Web** : składnik, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji

* **Aplikacja internetowa** : aplikacja z zależnościami w katalogach LDAP v3.

* **Azure AD** : usługa Azure AD synchronizuje informacje o tożsamościach (użytkowników, grupy, hasła) z lokalnych katalogów LDAP organizacji za pośrednictwem Azure AD Connect. 

* **Azure AD Connect** : to narzędzie służące do nawiązywania połączenia z lokalnymi infrastrukturami tożsamości w celu Microsoft Azure AD. Kreator i środowisko z przewodnikiem ułatwiają wdrażanie i Konfigurowanie wymagań wstępnych i składników wymaganych do nawiązania połączenia. 

* **Łącznik niestandardowy** : ogólny łącznik LDAP umożliwia integrację usługi synchronizacji Azure AD Connect z serwerem LDAP v3. Znajduje się on na Azure AD Connect.

* **Active Directory** : Active Directory to usługa katalogowa dostępna w większości systemów operacyjnych Windows Server. Serwery z usługami katalogowymi Active Directory są nazywane kontrolerami domeny i uwierzytelniają i autoryzują wszystkich użytkowników i komputery w domenie systemu Windows.

* **Serwer LDAP v3** : katalog zgodny z protokołem LDAP przechowujący użytkowników i hasła firmowe używane do uwierzytelniania usług katalogowych.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementowanie synchronizacji LDAP z usługą Azure AD

* [Narzędzia integracji katalogu tożsamości hybrydowej](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Plan instalacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap) 

* [Omówienie i Tworzenie łącznika LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Łączniki LDAP to zaawansowana konfiguracja wymagająca pewnej znajomości programu Forefront Identity Manager i/lub Microsoft Identity Manager. Jeśli są używane w środowisku produkcyjnym, firma Microsoft zaleca pytania dotyczące tej konfiguracji, korzystając z [Pomoc techniczna Premier](https://support.microsoft.com/premier) lub Microsoft Partner Network.

 
