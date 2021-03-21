---
title: Informacje na temat logowania jednokrotnego za pomocą aplikacji lokalnej przy użyciu serwera proxy aplikacji
description: Zapoznaj się z logowaniem jednokrotnym za pomocą aplikacji lokalnej przy użyciu serwera proxy aplikacji.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kenwith
ms.reviewer: japere, asteen
ms.openlocfilehash: 10b722edbe8d70c92e617c78db3d2fb1d46da3a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254108"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak skonfigurować logowanie jednokrotne do aplikacji opartej na serwerze proxy aplikacji

Logowanie jednokrotne (SSO) umożliwia użytkownikom dostęp do aplikacji bez wielokrotnego uwierzytelniania. Umożliwia ona pojedyncze uwierzytelnianie w chmurze, w odniesieniu do Azure Active Directory i umożliwia usłudze lub łącznikowi personifikację użytkownika w celu wykonania wszelkich dodatkowych wyzwań związanych z uwierzytelnianiem aplikacji.

## <a name="how-to-configure-single-sign-on"></a>Jak skonfigurować Logowanie jednokrotne
Aby skonfigurować Logowanie jednokrotne, najpierw upewnij się, że aplikacja została skonfigurowana pod kątem wstępnego uwierzytelniania za pomocą Azure Active Directory. Aby przeprowadzić tę konfigurację, przejdź do pozycji **Azure Active Directory**  - &gt; **aplikacje dla przedsiębiorstw**  - &gt; **wszystkie aplikacje**  - &gt; **- &gt; serwer proxy aplikacji** aplikacji. Na tej stronie zostanie wyświetlone pole "wstępne uwierzytelnianie" i upewnij się, że jest ono ustawione na wartość "Azure Active Directory. 

Aby uzyskać więcej informacji na temat metod wstępnego uwierzytelniania, zobacz krok 4 [dokumentu publikowanie aplikacji](application-proxy-add-on-premises-application.md).

   ![Metoda wstępnego uwierzytelniania w Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurowanie trybów logowania jednokrotnego dla aplikacji serwera proxy aplikacji
Skonfiguruj konkretny typ logowania jednokrotnego. Metody logowania są klasyfikowane na podstawie typu uwierzytelniania używanego przez aplikację zaplecza. Aplikacje serwera proxy aplikacji obsługują trzy typy logowania:

-   **Logowanie oparte na haśle**: logowanie oparte na hasłach może być używane dla każdej aplikacji, która używa pól username i Password do logowania. Kroki konfiguracji znajdują się w sekcji Konfigurowanie logowania jednokrotnego [dla aplikacji w galerii usługi Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Zintegrowane uwierzytelnianie systemu Windows**: w przypadku aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA) Logowanie jednokrotne jest włączane przy użyciu ograniczonego delegowania protokołu Kerberos (KCD). Ta metoda zapewnia uprawnienia łączników serwera proxy aplikacji w Active Directory, aby personifikować użytkowników i wysyłać i odbierać tokeny w ich imieniu. Szczegółowe informacje na temat konfigurowania KCD można znaleźć w [jednym Sign-On z dokumentacją KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Logowanie oparte na nagłówkach**: logowanie oparte na nagłówkach służy do udostępniania funkcji logowania jednokrotnego przy użyciu nagłówków HTTP. Aby dowiedzieć się więcej, zobacz Logowanie jednokrotne [oparte na nagłówkach](application-proxy-configure-single-sign-on-with-headers.md).

-   **Logowanie jednokrotne SAML**: z logowaniem jednokrotnym SAML usługa Azure AD jest uwierzytelniana w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD komunikuje informacje logowania do aplikacji za pomocą protokołu połączenia. Korzystając z logowania jednokrotnego opartego na protokole SAML, można mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML. Aby uzyskać informacje na temat konfigurowania logowania jednokrotnego w protokole SAML, zobacz [SAML dla logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).

Każdą z tych opcji można znaleźć, przechodząc do aplikacji w "aplikacje dla przedsiębiorstw" i otwierając stronę **logowania** jednokrotnego w menu po lewej stronie. Należy pamiętać, że jeśli aplikacja została utworzona w starym portalu, mogą nie być wyświetlane wszystkie te opcje.

Na tej stronie zostanie również wyświetlona jedna dodatkowa opcja Sign-On: Logowanie połączone. Ta opcja jest również obsługiwana przez serwer proxy aplikacji. Jednak ta opcja nie powoduje dodania logowania jednokrotnego do aplikacji. Wspomniana aplikacja może już mieć zaimplementowane Logowanie jednokrotne przy użyciu innej usługi, takiej jak Active Directory Federation Services. 

Ta opcja umożliwia administratorowi utworzenie linku do aplikacji, która jest po raz pierwszy włączona podczas uzyskiwania dostępu do aplikacji. Na przykład jeśli istnieje aplikacja, która jest skonfigurowana do uwierzytelniania użytkowników za pomocą Active Directory Federation Services 2,0, administrator może użyć opcji "połączone logowanie", aby utworzyć link do niego w aplikacjach.

## <a name="next-steps"></a>Następne kroki
- [Przechowywanie haseł dla logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
- [Uwierzytelnianie oparte na nagłówkach logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-headers.md) 
- Protokół [SAML do logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).
