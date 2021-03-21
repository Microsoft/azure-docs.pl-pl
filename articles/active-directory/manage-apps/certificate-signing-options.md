---
title: Zaawansowane opcje podpisywania certyfikatu tokenu SAML dla aplikacji usługi Azure AD
description: Dowiedz się, jak korzystać z zaawansowanych opcji podpisywania certyfikatów w tokenie SAML dla wstępnie zintegrowanych aplikacji w Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dd6a7a34ac231f8af9d08aab99e3d552f33ea1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259625"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Zaawansowane opcje podpisywania certyfikatu w tokenie SAML dla aplikacji galerii w Azure Active Directory

Dzisiaj Azure Active Directory (Azure AD) obsługuje tysiące wstępnie zintegrowanych aplikacji w galerii aplikacji Azure Active Directory. Ponad 500 aplikacji obsługuje logowanie jednokrotne za pomocą protokołu [SAML](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0, takiego jak aplikacja dla [pakietu](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) . Gdy klient uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD przy użyciu protokołu SAML, usługa Azure AD wysyła token do aplikacji (za pośrednictwem wpisu HTTP). Następnie aplikacja sprawdza poprawność i używa tokenu do logowania klienta zamiast monitowania o podanie nazwy użytkownika i hasła. Te tokeny SAML są podpisane przy użyciu unikatowego certyfikatu, który jest generowany w usłudze Azure AD i według określonych algorytmów standardowych.

Usługa Azure AD używa niektórych ustawień domyślnych dla aplikacji galerii. Wartości domyślne są konfigurowane na podstawie wymagań aplikacji.

W usłudze Azure AD można skonfigurować opcje podpisywania certyfikatu i algorytm podpisywania certyfikatu.

## <a name="certificate-signing-options"></a>Opcje podpisywania certyfikatów

Usługa Azure AD obsługuje trzy opcje podpisywania certyfikatu:

* **Podpisz potwierdzenie SAML**. Ta opcja domyślna jest ustawiana dla większości aplikacji galerii. W przypadku wybrania tej opcji usługa Azure AD jako dostawca tożsamości (dostawcy tożsamości) podpisuje potwierdzenie SAML i certyfikat z certyfikatem [X. 509](https://wikipedia.org/wiki/X.509) aplikacji.

* **Podpisz odpowiedź SAML**. W przypadku wybrania tej opcji usługa Azure AD dostawcy tożsamości podpisanie odpowiedzi SAML z certyfikatem X. 509 aplikacji.

* **Podpisz odpowiedź i potwierdzenie protokołu SAML**. W przypadku wybrania tej opcji usługa Azure AD jako dostawcy tożsamości podpisuje cały token SAML certyfikatem X. 509 aplikacji.

## <a name="certificate-signing-algorithms"></a>Algorytmy podpisywania certyfikatów

Usługa Azure AD obsługuje dwa algorytmy podpisywania lub bezpieczne algorytmy wyznaczania wartości skrótu (SHA) w celu podpisania odpowiedzi SAML:

* **SHA-256**. Usługa Azure AD używa tego domyślnego algorytmu do podpisywania odpowiedzi SAML. Jest to najnowszy algorytm i jest bezpieczniejszy niż algorytm SHA-1. Większość aplikacji obsługuje Algorytm SHA-256. Jeśli aplikacja obsługuje tylko algorytm SHA-1, można ją zmienić. W przeciwnym razie zalecamy użycie algorytmu SHA-256 do podpisywania odpowiedzi SAML.

* **SHA-1**. Ten algorytm jest starszy i jest traktowany jako mniej bezpieczny niż algorytm SHA-256. Jeśli aplikacja obsługuje tylko ten algorytm podpisywania, można wybrać tę opcję z listy rozwijanej **algorytm podpisywania** . Usługa Azure AD następnie podpisuje odpowiedź SAML przy użyciu algorytmu SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Zmień opcje podpisywania certyfikatu i algorytm podpisywania

Aby zmienić opcje podpisywania certyfikatu SAML aplikacji i algorytm podpisywania certyfikatu, wybierz daną aplikację:

1. W [portalu Azure Active Directory](https://aad.portal.azure.com/)Zaloguj się do swojego konta. Zostanie wyświetlona strona **Centrum administracyjne Azure Active Directory** .
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji przedsiębiorstwa znajdujących się na Twoim koncie.
1. Wybierz aplikację. Zostanie wyświetlona strona przeglądu aplikacji.

   ![Przykład: Strona przeglądu aplikacji](./media/certificate-signing-options/application-overview-page.png)

Następnie zmień opcje podpisywania certyfikatu w tokenie SAML dla tej aplikacji:

1. W lewym okienku strony przegląd aplikacji wybierz pozycję **Logowanie jednokrotne**.
1. Jeśli zostanie wyświetlona strona **Konfigurowanie pojedynczego Sign-On przy użyciu elementu SAML-Preview** , przejdź do kroku 5.
1. Jeśli strona **Wybierz metodę logowania** jednokrotnego nie zostanie wyświetlona, wybierz pozycję **Zmień tryby rejestracji** jednokrotnej, aby wyświetlić tę stronę.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** , jeśli jest dostępna. (Jeśli protokół **SAML** nie jest dostępny, aplikacja nie obsługuje protokołu SAML i można zignorować resztę tej procedury i artykułu).
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu protokołu SAML-Preview** Znajdź nagłówek **certyfikatu podpisywania SAML** i wybierz ikonę **edycji** (ołówek). Zostanie wyświetlona strona **certyfikat podpisywania SAML** .

   ![Przykład: Strona certyfikatu podpisywania SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Z listy rozwijanej **Opcja podpisywania** wybierz pozycję **Podpisz odpowiedź SAML**, **Podpisz potwierdzenie SAML** lub **Podpisz odpowiedź i potwierdzenie protokołu SAML**. Opisy tych opcji pojawiają się wcześniej w tym artykule w [opcjach podpisywania certyfikatu](#certificate-signing-options).
1. Z listy rozwijanej **algorytm podpisywania** wybierz pozycję **SHA-1** lub **SHA-256**. Opisy tych opcji pojawiają się wcześniej w tym artykule w sekcji [algorytmy podpisywania certyfikatu](#certificate-signing-algorithms) .
1. Jeśli jesteś zadowolony z wybranych opcji, wybierz pozycję **Zapisz** , aby zastosować nowe ustawienia certyfikatu podpisywania SAML. W przeciwnym razie wybierz **znak X** , aby odrzucić zmiany.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](./configure-saml-single-sign-on.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym opartym na języku SAML](./debug-saml-sso-issues.md)