---
title: Zarządzanie certyfikatami federacyjnymi w usłudze Azure AD | Microsoft Docs
description: Dowiedz się, jak dostosować datę wygaśnięcia certyfikatów federacyjnych oraz jak odnowić certyfikaty, które wkrótce wygasną.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87190e9a7aa3c3a26b77220dd0332d65c640bc2e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658999"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w Azure Active Directory

W tym artykule omówiono typowe pytania i informacje związane z certyfikatami, które Azure Active Directory (usługa Azure AD) tworzy do ustanowienia federacyjnego logowania jednokrotnego (SSO) dla aplikacji oprogramowania jako usługi (SaaS). Dodaj aplikacje z galerii aplikacji usługi Azure AD lub przy użyciu szablonu aplikacji bez galerii. Skonfiguruj aplikację przy użyciu opcji federacyjnego logowania jednokrotnego.

Ten artykuł dotyczy tylko aplikacji skonfigurowanych do korzystania z logowania jednokrotnego usługi Azure AD za pomocą Federacji [SAML](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatycznie wygenerowany certyfikat dla galerii i aplikacji innych niż Galeria

Gdy dodajesz nową aplikację z galerii i skonfigurujesz logowanie oparte na protokole SAML ( **przez wybranie protokołu** SAML logowania jednokrotnego  >  **SAML** ze strony przegląd aplikacji), usługa Azure AD generuje certyfikat dla aplikacji, która jest ważna przez trzy lata. Aby pobrać aktywny certyfikat jako plik certyfikatu zabezpieczeń (**. cer**), Wróć do tej strony (**Logowanie oparte na języku SAML**) i wybierz łącze pobierania w nagłówku **certyfikatu podpisywania SAML** . Można wybrać jeden certyfikat pierwotny (binarny) lub certyfikat Base64 (Base 64-Encoded Text). W przypadku aplikacji galerii w tej sekcji można także wyświetlić link umożliwiający pobranie certyfikatu jako pliku XML metadanych Federacji (plik **XML** ), w zależności od wymagań aplikacji.

![Opcje pobierania aktywnego certyfikatu podpisywania SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Możesz również pobrać aktywny lub nieaktywny certyfikat, wybierając ikonę **edycji** nagłówka **certyfikatu podpisywania SAML** (ołówek), która wyświetla stronę **certyfikat podpisywania SAML** . Wybierz wielokropek (**...**) obok certyfikatu, który chcesz pobrać, a następnie wybierz odpowiedni format certyfikatu. Dostępna jest dodatkowa opcja pobrania certyfikatu w formacie (PEM) z ulepszoną ochroną prywatności. Ten format jest identyczny z algorytmem Base64, ale z rozszerzeniem nazwy pliku **PEM** , który nie jest rozpoznawany w systemie Windows jako format certyfikatu.

![Opcje pobierania certyfikatu podpisywania SAML (aktywne i nieaktywne)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Dostosowywanie daty wygaśnięcia certyfikatu federacyjnego i przekazanie go do nowego certyfikatu

Domyślnie platforma Azure konfiguruje certyfikat do wygaśnięcia po trzech latach, gdy jest tworzony automatycznie podczas konfigurowania logowania jednokrotnego za pomocą protokołu SAML. Ponieważ nie można zmienić daty certyfikatu po jego zapisaniu, należy wykonać następujące działania:

1. Utwórz nowy certyfikat z żądanym terminem.
1. Zapisz nowy certyfikat.
1. Pobierz nowy certyfikat w poprawnym formacie.
1. Przekaż nowy certyfikat do aplikacji.
1. Ustaw nowy certyfikat jako aktywny w portalu Azure Active Directory.

Poniższe dwie sekcje pomagają wykonać te kroki.

### <a name="create-a-new-certificate"></a>Utwórz nowy certyfikat

Najpierw utwórz i Zapisz nowy certyfikat z inną datą wygaśnięcia:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlona strona **Centrum administracyjne Azure Active Directory** .
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji przedsiębiorstwa znajdujących się na Twoim koncie.
1. Wybierz aplikację, której dotyczy dana aplikacja. Zostanie wyświetlona strona przeglądu aplikacji.
1. W lewym okienku strony przegląd aplikacji wybierz pozycję **Logowanie jednokrotne**.
1. Jeśli zostanie wyświetlona strona **Wybierz metodę logowania** jednokrotnego, wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu protokołu SAML-Preview** Znajdź nagłówek **certyfikatu podpisywania SAML** i wybierz ikonę **edycji** (ołówek). Zostanie wyświetlona strona **certyfikat podpisywania SAML** , która wyświetla stan (**aktywny** lub **nieaktywny**), datę wygaśnięcia i odcisk palca (ciąg skrótu) każdego certyfikatu.
1. Wybierz pozycję **nowy certyfikat**. Zostanie wyświetlony nowy wiersz poniżej listy certyfikatów, gdzie Data wygaśnięcia jest domyślnie równa dokładnie trzech lat od bieżącej daty. (Zmiany nie zostały jeszcze zapisane, więc nadal możesz zmodyfikować datę wygaśnięcia).
1. W wierszu nowy certyfikat Umieść kursor nad kolumną Data wygaśnięcia i wybierz ikonę **Wybierz datę** (kalendarz). Zostanie wyświetlona kontrolka kalendarz, w której są wyświetlane dni miesiąca z bieżącą datą wygaśnięcia nowego wiersza.
1. Użyj kontrolki Calendar, aby ustawić nową datę. Możesz ustawić dowolną datę między bieżącą datą a trzy lata od daty bieżącej.
1. Wybierz pozycję **Zapisz**. Nowy certyfikat jest teraz wyświetlany ze stanem **nieaktywny**, wybraną datą wygaśnięcia i odciskiem palca.
1. Wybierz **znak X** , aby powrócić do strony **Konfigurowanie pojedynczego Sign-On przy użyciu protokołu SAML-Preview** .

### <a name="upload-and-activate-a-certificate"></a>Przekazywanie i aktywowanie certyfikatu

Następnie Pobierz nowy certyfikat w prawidłowym formacie, przekaż go do aplikacji i ustaw go jako aktywny w Azure Active Directory:

1. Wyświetl dodatkowe instrukcje dotyczące konfiguracji logowania SAML w aplikacji, wykonując jedną z poniższych opcji:

   - Wybieranie linku **przewodnika konfiguracji** do wyświetlania w osobnym oknie przeglądarki lub karcie lub
   - Przejdź do nagłówka **Set** i wybierz **instrukcje krok po kroku** , aby wyświetlić na pasku bocznym.

1. W instrukcjach Zanotuj format kodowania wymagany do przekazania certyfikatu.
1. Postępuj zgodnie z instrukcjami w sekcji [Certyfikaty generowane automatycznie dla galerii i aplikacji innych niż Galeria](#auto-generated-certificate-for-gallery-and-non-gallery-applications) . Ten krok umożliwia pobranie certyfikatu w formacie kodowania, który jest wymagany do przekazywania przez aplikację.
1. Jeśli chcesz przetworzyć nowy certyfikat, Wróć do strony **certyfikat podpisywania SAML** , a następnie w nowo zapisanym wierszu certyfikatu wybierz wielokropek (**...**) i wybierz pozycję **Ustaw certyfikat jako aktywny**. Stan nowego certyfikatu zmieni się na **aktywny**, a poprzednio aktywny certyfikat zmieni się na stan **nieaktywny**.
1. Kontynuuj zgodnie z instrukcjami konfiguracji logowania SAML aplikacji, które zostały wyświetlone wcześniej, aby można było przekazać certyfikat podpisywania SAML w prawidłowym formacie kodowania.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Dodaj adresy e-mail powiadomień dla wygaśnięcia certyfikatu

Usługa Azure AD wyśle wiadomość e-mail z powiadomieniem 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Możesz dodać więcej niż jeden adres e-mail, aby otrzymywać powiadomienia. Aby określić adresy e-mail, na które mają być wysyłane powiadomienia:

1. Na stronie **certyfikat podpisywania SAML** przejdź do nagłówka **adres e-mail powiadomienia** . Domyślnie w tym nagłówku jest stosowany tylko adres e-mail administratora, który dodał aplikację.
1. Poniżej końcowego adresu e-mail wpisz adres e-mail, na który powinien zostać wyświetlony komunikat o wygaśnięciu certyfikatu, a następnie naciśnij klawisz ENTER.
1. Powtórz poprzedni krok dla każdego adresu e-mail, który chcesz dodać.
1. Dla każdego adresu e-mail, który chcesz usunąć, wybierz ikonę **Usuń** (można utworzyć śmieci) obok adresu e-mail.
1. Wybierz pozycję **Zapisz**.

Otrzymasz wiadomość e-mail z powiadomieniem aadnotification@microsoft.com . Aby uniknąć wiadomości e-mail w lokalizacji spamu, Dodaj tę wiadomość e-mail do kontaktów.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Odnów certyfikat, który wkrótce wygaśnie

Jeśli certyfikat wkrótce wygaśnie, możesz go odnowić, korzystając z procedury, która powoduje brak poważnych przestojów dla użytkowników. Aby odnowić certyfikat wygasający:

1. Postępuj zgodnie z instrukcjami w sekcji [Tworzenie nowego certyfikatu](#create-a-new-certificate) wcześniej, używając daty pokrywającej się z istniejącym certyfikatem. Ta data ogranicza czas przestoju spowodowany wygaśnięciem certyfikatu.
1. Jeśli aplikacja może automatycznie przetworzyć certyfikat, Ustaw nowy certyfikat na aktywny, wykonując następujące czynności:
   1. Wróć do strony **certyfikatu podpisywania SAML** .
   1. W nowo zapisanym wierszu certyfikatu wybierz wielokropek (**...**), a następnie wybierz pozycję **Ustaw certyfikat jako aktywny**.
   1. Pomiń dwa następne kroki.

1. Jeśli aplikacja może obsługiwać tylko jeden certyfikat naraz, należy wybrać interwał przestoju, aby wykonać następny krok. (W przeciwnym razie, jeśli aplikacja nie pobiera automatycznie nowego certyfikatu, ale może obsługiwać więcej niż jeden certyfikat podpisywania, można wykonać następny krok w dowolnym momencie).
1. Przed wygaśnięciem starego certyfikatu postępuj zgodnie z instrukcjami podanymi w sekcji [przekazywanie i aktywowanie certyfikatu](#upload-and-activate-a-certificate) wcześniej.
1. Zaloguj się do aplikacji, aby upewnić się, że certyfikat działa poprawnie.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
- [Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Debugowanie logowania jednokrotnego opartego na protokole SAML w aplikacjach w usłudze Azure Active Directory](./debug-saml-sso-issues.md)