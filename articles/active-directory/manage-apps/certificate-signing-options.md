---
title: Zaawansowane opcje podpisywania certyfikatu tokenu SAML dla aplikacji usługi Azure AD
description: Dowiedz się, jak używać zaawansowanych opcji podpisywania certyfikatów w tokenie SAML dla wstępnie zintegrowanych aplikacji w Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb58f59b3796311f52d78ef87c8918f9888c152
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377874"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Zaawansowane opcje podpisywania certyfikatów w tokenie SAML dla aplikacji z galerii w Azure Active Directory

Usługa Azure Active Directory (Azure AD) obsługuje tysiące wstępnie zintegrowanych aplikacji w galerii Azure Active Directory App Gallery. Ponad 500 aplikacji obsługuje logowanie pojedyncze przy użyciu [protokołu SAML](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0, takiego jak [aplikacja NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Gdy klient uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD przy użyciu protokołu SAML, usługa Azure AD wysyła token do aplikacji (za pośrednictwem żądania POST protokołu HTTP). Następnie aplikacja weryfikuje token i używa go do logowania klienta zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML są podpisane unikatowym certyfikatem wygenerowanym w usłudze Azure AD i przez określone standardowe algorytmy.

Usługa Azure AD używa niektórych ustawień domyślnych dla aplikacji z galerii. Wartości domyślne są ustawiane na podstawie wymagań aplikacji.

W usłudze Azure AD można skonfigurować opcje podpisywania certyfikatów i algorytm podpisywania certyfikatów.

## <a name="certificate-signing-options"></a>Opcje podpisywania certyfikatów

Usługa Azure AD obsługuje trzy opcje podpisywania certyfikatów:

* **Podpisz asercja SAML**. Ta opcja domyślna jest ustawiana dla większości aplikacji z galerii. Jeśli wybierzesz tę opcję, usługa Azure AD jako dostawca tożsamości podpisze asercja SAML i certyfikat przy użyciu certyfikatu [X.509](https://wikipedia.org/wiki/X.509) aplikacji.

* **Podpisz odpowiedź SAML**. Jeśli wybierzesz tę opcję, usługa Azure AD jako idP podpisze odpowiedź SAML przy użyciu certyfikatu X.509 aplikacji.

* **Podpisz odpowiedź SAML i potwierdzenie**. Jeśli wybierzesz tę opcję, usługa Azure AD jako idP podpisze cały token SAML certyfikatem X.509 aplikacji.

## <a name="certificate-signing-algorithms"></a>Algorytmy podpisywania certyfikatów

Usługa Azure AD obsługuje dwa algorytmy podpisywania (secure hash algorithms, SHA) do podpisywania odpowiedzi SAML:

* **SHA-256.** Usługa Azure AD używa tego domyślnego algorytmu do podpisywania odpowiedzi SAML. Jest to najnowszy algorytm i jest bezpieczniejszy niż SHA-1. Większość aplikacji obsługuje algorytm SHA-256. Jeśli aplikacja obsługuje tylko algorytm SHA-1 jako algorytm podpisywania, można go zmienić. W przeciwnym razie zalecamy użycie algorytmu SHA-256 do podpisywania odpowiedzi SAML.

* **SHA-1**. Ten algorytm jest starszy i jest traktowany jako mniej bezpieczny niż ALGORYTM SHA-256. Jeśli aplikacja obsługuje tylko ten algorytm podpisywania,  możesz wybrać tę opcję z listy rozwijanej Algorytm podpisywania. Następnie usługa Azure AD podpisuje odpowiedź SAML algorytmem SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Zmienianie opcji podpisywania certyfikatu i algorytmu podpisywania

Aby zmienić opcje podpisywania certyfikatów SAML aplikacji i algorytm podpisywania certyfikatów, wybierz aplikację, która ma zastosowanie:

1. W [Azure Active Directory zaloguj](https://aad.portal.azure.com/)się do swojego konta. Zostanie **Azure Active Directory centrum administracyjnego.**
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji dla przedsiębiorstw na Twoim koncie.
1. Wybierz aplikację. Zostanie wyświetlona strona przeglądu aplikacji.

   ![Przykład: strona Przeglądu aplikacji](./media/certificate-signing-options/application-overview-page.png)

Następnie zmień opcje podpisywania certyfikatów w tokenie SAML dla tej aplikacji:

1. W okienku po lewej stronie przeglądu aplikacji wybierz **pozycję Logowanie pojedyncze.**
1. Jeśli zostanie **wyświetlona strona Sign-On single Sign-On with SAML - Preview** (Konfigurowanie aplikacji jednostronicowej za pomocą saml — wersja zapoznawcza), przejdź do kroku 5.
1. Jeśli strona **Wybierz metodę logowania** pojedynczego nie jest wyświetlana, wybierz pozycję Zmień tryby logowania **pojedynczego,** aby wyświetlić tę stronę.
1. Na stronie **Wybierz metodę logowania pojedynczego** wybierz pozycję **SAML, jeśli** jest dostępna. (Jeśli **język SAML** jest niedostępny, aplikacja nie obsługuje saml i możesz zignorować pozostałą część tej procedury i artykułu).
1. Na stronie **Konfigurowanie aplikacji Sign-On saml —** wersja zapoznawcza znajdź nagłówek Certyfikat  podpisywania **SAML** i wybierz ikonę Edytuj (ołówek). Zostanie **wyświetlona strona Certyfikat podpisywania SAML.**

   ![Przykład: strona certyfikatu podpisywania SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Z listy **rozwijanej Signing Option** (Opcja podpisywania) wybierz pozycję **Sign SAML response (Podpisz odpowiedź SAML),** Sign SAML assertion (Podpisz asercja SAML) lub Sign SAML response and assertion (Podpisz odpowiedź i potwierdzenie **SAML).**  Opisy tych opcji są wyświetlane wcześniej w tym artykule w [artykule Opcje podpisywania certyfikatów.](#certificate-signing-options)
1. Z listy **rozwijanej** Algorytm podpisywania wybierz pozycję **SHA-1** lub **SHA-256.** Opisy tych opcji są wyświetlane wcześniej w tym artykule w [sekcji Algorytmy podpisywania](#certificate-signing-algorithms) certyfikatów.
1. Jeśli wybrane opcje są satysfakcjonują, wybierz **pozycję** Zapisz, aby zastosować nowe ustawienia certyfikatu podpisywania SAML. W przeciwnym razie wybierz **znak X,** aby odrzucić zmiany.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie logowania pojedynczego do aplikacji, które nie znajdują się w galerii Azure Active Directory App Gallery](./configure-saml-single-sign-on.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym opartym na języku SAML](./debug-saml-sso-issues.md)