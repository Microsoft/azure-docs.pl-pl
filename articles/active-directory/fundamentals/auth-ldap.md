---
title: Uwierzytelnianie LDAP z Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania uwierzytelniania LDAP przy użyciu Azure Active Directory.
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
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168716"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Uwierzytelnianie LDAP z Azure Active Directory

Lightweight Directory Access Protocol (LDAP) to protokół aplikacji służący do pracy z różnymi usługami katalogowymi. Usługi katalogowe, takie jak Active Directory, [przechowują informacje o użytkowniku i koncie](https://www.dnsstuff.com/active-directory-service-accounts)oraz informacje o zabezpieczeniach, takie jak hasła. Następnie usługa umożliwia udostępnianie informacji innym urządzeniom w sieci. Aplikacje dla przedsiębiorstw, takie jak poczta e-mail, menedżerowie relacji klienta (CRMs) i oprogramowanie Kadr (HR), mogą używać protokołu LDAP do uwierzytelniania, uzyskiwania dostępu i znajdowania informacji. 

Azure Active Directory (Azure AD) obsługuje ten wzorzec za pośrednictwem Azure AD Domain Services (AD DS). Umożliwia ona organizacjom, które wdrażają strategię w chmurze w celu modernizacji środowiska przez przeniesienie lokalnych zasobów LDAP do chmury. Bezpośrednimi korzyściami będą: 

* Integracja z usługą Azure AD. Dodatki użytkowników i grup lub zmiany atrybutów do ich obiektów są automatycznie synchronizowane z dzierżawy usługi Azure AD w celu AD DS. Zmiany w obiektach w Active Directory lokalnym są synchronizowane z usługą Azure AD, a następnie do AD DS.

* Uproszczenie operacji. Zmniejsza konieczność ręcznego utrzymywania i poprawek infrastruktury lokalnej. 

* Miarodaj. Masz dostęp do zarządzanych usług o wysokiej dostępności 

## <a name="use-when"></a>Zastosowania

Istnieje potrzeba, aby aplikacja lub usługa korzystała z uwierzytelniania LDAP.

![Diagram architektury](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: uzyskuje dostęp do aplikacji zależnych od protokołu LDAP za pośrednictwem przeglądarki.

* **Przeglądarka sieci Web**: interfejs, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji.

* **Virtual Network**: sieć prywatna na platformie Azure, za pomocą której Starsza aplikacja może korzystać z usług LDAP. 

* **Starsze aplikacje**: aplikacje lub obciążenia serwera wymagające protokołu LDAP wdrożone w sieci wirtualnej na platformie Azure lub mające wgląd w AD DS wystąpienia adresów IP za pośrednictwem tras sieciowych. 

* **Azure AD**: synchronizuje informacje o tożsamościach z katalogu lokalnego organizacji za pośrednictwem Azure AD Connect.

* **Azure AD Domain Services (AD DS)**: wykonuje jednokierunkową synchronizację z usługi Azure AD w celu zapewnienia dostępu do centralnego zestawu użytkowników, grup i poświadczeń. Wystąpienie AD DS jest przypisane do sieci wirtualnej. Aplikacje, usługi i maszyny wirtualne na platformie Azure, które łączą się z siecią wirtualną przypisaną do AD DS mogą używać typowych funkcji AD DS, takich jak LDAP, przyłączanie do domeny, zasady grupy, Kerberos i uwierzytelnianie NTLM.
   > [!NOTE]
   >  W środowiskach, w których organizacja nie może synchronizować skrótów haseł lub logowania użytkowników przy użyciu kart inteligentnych, zalecamy użycie lasu zasobów w AD DS. 

* **Azure AD Connect**: Narzędzie do synchronizowania informacji o tożsamości lokalnej do Microsoft Azure AD. Kreator wdrażania i środowisko z przewodnikiem ułatwiają Konfigurowanie wymagań wstępnych i składników wymaganych do połączenia, w tym synchronizacji i logowania się z Active Directory do usługi Azure AD. 

* **Active Directory**: usługa katalogowa przechowująca [lokalne informacje o tożsamości, takie jak informacje o użytkowniku i koncie](https://www.dnsstuff.com/active-directory-service-accounts), a także informacje o zabezpieczeniach, takie jak hasła.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementowanie uwierzytelniania LDAP za pomocą usługi Azure AD

* [Tworzenie i Konfigurowanie wystąpienia usługi Azure AD DS](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Konfigurowanie sieci wirtualnej dla wystąpienia usługi Azure AD DS](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Konfigurowanie Secure LDAP dla domeny zarządzanej AD DS platformy Azure](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Utwórz zaufanie lasu wychodzącego do domeny lokalnej na platformie Azure AD DS](../../active-directory-domain-services/tutorial-create-forest-trust.md)

