---
title: Zarządzanie certyfikatami federacyjną w usłudze Azure AD | Microsoft Docs
description: Dowiedz się, jak dostosować datę wygaśnięcia certyfikatów federacji oraz jak odnowić certyfikaty, które wkrótce wygasną.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594af54221dd242bd481fe3d2fc823f628c1f955
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379455"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Manage certificates for federated single sign-on in Azure Active Directory

W tym artykule opisać typowe pytania i informacje związane z certyfikatami, które usługa Azure Active Directory (Azure AD) tworzy w celu ustanowienia federowego logowania jednokrotnego (SSO) z aplikacjami typu oprogramowanie jako usługa (SaaS). Dodaj aplikacje z galerii aplikacji usługi Azure AD lub przy użyciu szablonu aplikacji spoza galerii. Skonfiguruj aplikację przy użyciu opcji federowania logowania jednokrotnego.

Ten artykuł dotyczy tylko aplikacji, które są skonfigurowane do używania logowania jednokrotnego usługi Azure AD za [pośrednictwem SAML](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatycznie generowany certyfikat dla aplikacji z galerii i spoza galerii

Po dodaniu nowej aplikacji z galerii i skonfigurowaniu logowania opartego na forsłudze SAML (przez wybranie opcji SamL logowania pojedynczego na stronie przeglądu aplikacji) usługa Azure AD wygeneruje certyfikat dla aplikacji, która jest ważna przez  >   trzy lata. Aby pobrać aktywny certyfikat jako plik certyfikatu zabezpieczeń **(cer),** wróć do tej strony (logowanie oparte na **saml)** i wybierz link pobierania w nagłówku Certyfikat podpisywania **SAML.** Możesz wybrać certyfikat nieprzetworzony (binarny) lub certyfikat base64 (tekst zakodowany w formacie Base 64). W przypadku aplikacji z galerii w tej sekcji może być również pokazywany link do pobrania certyfikatu w formacie XML metadanych federacji **(plik XML),** w zależności od wymagań aplikacji.

![Opcje pobierania aktywnego certyfikatu podpisywania SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Możesz również pobrać aktywny lub nieaktywny certyfikat, wybierając  ikonę edytuj certyfikatu podpisywania SAML (ołówek), która wyświetla stronę Certyfikat podpisywania **SAML.**  Wybierz wielokropek (**...**) obok certyfikatu, który chcesz pobrać, a następnie wybierz odpowiedni format certyfikatu. Dostępna jest dodatkowa opcja pobrania certyfikatu w formacie PEM (privacy-enhanced mail). Ten format jest identyczny z Base64, ale z rozszerzeniem nazwy pliku **PEM,** który nie jest rozpoznawany w systemie Windows jako format certyfikatu.

![Opcje pobierania certyfikatu podpisywania SAML (aktywny i nieaktywny)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Dostosowywanie daty wygaśnięcia certyfikatu federacji i przekierowywał ją do nowego certyfikatu

Domyślnie platforma Azure konfiguruje certyfikat tak, aby wygasał po trzech latach od jego automatycznego utworzenia podczas konfigurowania logowania pojedynczego SAML. Ponieważ nie można zmienić daty certyfikatu po jego zapisaniu, należy:

1. Utwórz nowy certyfikat z żądaną datą.
1. Zapisz nowy certyfikat.
1. Pobierz nowy certyfikat w poprawnym formacie.
1. Przekaż nowy certyfikat do aplikacji.
1. Upewnij się, że nowy certyfikat jest aktywny w Azure Active Directory portal.

Poniższe dwie sekcje ułatwiają wykonanie tych kroków.

### <a name="create-a-new-certificate"></a>Tworzenie nowego certyfikatu

Najpierw utwórz i zapisz nowy certyfikat z inną datą wygaśnięcia:

1. Zaloguj się do [portalu Azure Active Directory portal.](https://aad.portal.azure.com/) Zostanie **Azure Active Directory centrum administracyjnego.**
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji dla przedsiębiorstw na Twoim koncie.
1. Wybierz aplikację, którego dotyczy problem. Zostanie wyświetlona strona przeglądu aplikacji.
1. W okienku po lewej stronie przeglądu aplikacji wybierz **pozycję Logowanie pojedyncze.**
1. Jeśli zostanie **wyświetlona strona Wybierz metodę logowania pojedynczego,** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie aplikacji Sign-On saml —** wersja zapoznawcza znajdź nagłówek Certyfikat  podpisywania **SAML** i wybierz ikonę Edytuj (ołówek). Zostanie **wyświetlona strona Certyfikat** podpisywania SAML z wyświetlonym stanem (Aktywny lub Nieaktywny), datą wygaśnięcia i odciskiem palca (ciągiem skrótu) każdego certyfikatu. 
1. Wybierz **pozycję Nowy certyfikat.** Pod listą certyfikatów zostanie wyświetlony nowy wiersz, w którym data wygaśnięcia jest domyślnie domyślną datą dokładnie trzech lat po bieżącej dacie. (Zmiany nie zostały jeszcze zapisane, więc nadal można zmodyfikować datę wygaśnięcia).
1. W wierszu nowego certyfikatu umieść wskaźnik myszy na kolumnie data wygaśnięcia i wybierz **ikonę Wybierz** datę (kalendarz). Zostanie wyświetlona kontrolka kalendarza z wyświetlonymi dniami miesiąca bieżącej daty wygaśnięcia nowego wiersza.
1. Użyj kontrolki kalendarza, aby ustawić nową datę. Można ustawić dowolną datę między bieżącą datą a trzema latami po bieżącej dacie.
1. Wybierz pozycję **Zapisz**. Nowy certyfikat będzie teraz wyświetlany ze stanem Nieaktywny, wybraną datą wygaśnięcia i odciskiem palca.
1. Wybierz przycisk **X,** aby powrócić do strony Set up Single Sign-On with SAML - Preview (Konfigurowanie aplikacji Sign-On **saml — wersja zapoznawcza).**

### <a name="upload-and-activate-a-certificate"></a>Przekazywanie i aktywowanie certyfikatu

Następnie pobierz nowy certyfikat w poprawnym formacie, przekaż go do aplikacji i uaplikuj go w Azure Active Directory:

1. Aby wyświetlić dodatkowe instrukcje konfiguracji logowania SAML aplikacji, zobacz:

   - Wybranie **linku przewodnika konfiguracji** do wyświetlenia w osobnym oknie przeglądarki lub na osobnej karcie lub
   - Przechodząc do **nagłówka konfigurowanie** i wybierając **pozycję Wyświetl instrukcje** krok po kroku, aby wyświetlić na pasku bocznym.

1. W instrukcjach zanotuj format kodowania wymagany do przekazania certyfikatu.
1. Postępuj zgodnie z instrukcjami podanymi wcześniej w sekcji Automatycznie [wygenerowany certyfikat](#auto-generated-certificate-for-gallery-and-non-gallery-applications) dla aplikacji spoza galerii. Ten krok umożliwia pobranie certyfikatu w formacie kodowania wymaganym do przekazania przez aplikację.
1. Jeśli chcesz przerzucać do nowego certyfikatu, wróć do strony Certyfikat podpisywania **SAML,** a następnie w nowo zapisanym wierszu certyfikatu wybierz wielokropek (**...**) i wybierz pozycję Udostępnij certyfikat **jako aktywny.** Stan nowego certyfikatu zmieni się na **Aktywny,** a wcześniej aktywny certyfikat zmieni się na **Nieaktywny.**
1. Kontynuuj zgodnie z wyświetlonymi wcześniej instrukcjami konfiguracji logowania SAML aplikacji, aby przekazać certyfikat podpisywania SAML w poprawnym formacie kodowania.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Dodawanie adresów powiadomień e-mail w celu wygaśnięcia certyfikatu

Usługa Azure AD wyśle powiadomienie e-mail na 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Możesz dodać więcej niż jeden adres e-mail, aby otrzymywać powiadomienia. Aby określić adresy e-mail, na które mają być wysyłane powiadomienia:

1. Na stronie **Certyfikat podpisywania SAML** przejdź do nagłówka **adresy e-mail powiadomień.** Domyślnie ten nagłówek używa tylko adresu e-mail administratora, który dodał aplikację.
1. Poniżej końcowego adresu e-mail wpisz adres e-mail, na który ma zostać wyświetlony komunikat o wygaśnięciu certyfikatu, a następnie naciśnij klawisz Enter.
1. Powtórz poprzedni krok dla każdego adresu e-mail, który chcesz dodać.
1. Dla każdego adresu e-mail,  który chcesz usunąć, wybierz ikonę Usuń (kosz na śmieci) obok adresu e-mail.
1. Wybierz pozycję **Zapisz**.

Do listy powiadomień można dodać maksymalnie 5 adresów e-mail (w tym adres e-mail administratora, który dodał aplikację). Jeśli chcesz, aby więcej osób było powiadamianych, użyj wiadomości e-mail z listą dystrybucyjną.

Otrzymasz wiadomość e-mail z powiadomieniem z adresu aadnotification@microsoft.com . Aby uniknąć wiadomości e-mail przechodzącej do twojej lokalizacji spamu, dodaj tę wiadomość e-mail do kontaktów.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Odnawianie certyfikatu, który wkrótce wygaśnie

Jeśli certyfikat wygaśnie, możesz go odnowić przy użyciu procedury, która nie powoduje żadnych znaczących przestojów dla użytkowników. Aby odnowić wygasający certyfikat:

1. Postępuj zgodnie z instrukcjami w sekcji Tworzenie [nowego certyfikatu](#create-a-new-certificate) wcześniej, używając daty, która nakłada się na istniejący certyfikat. Ta data ogranicza czas przestoju spowodowany wygaśnięciem certyfikatu.
1. Jeśli aplikacja może automatycznie przechować certyfikat, ustaw nowy certyfikat na aktywny, wykonać następujące kroki:
   1. Wstecz do strony **Certyfikat podpisywania SAML.**
   1. W nowo zapisanym wierszu certyfikatu wybierz wielokropek (**...**), a następnie wybierz pozycję **Aktywna certyfikat.**
   1. Pomiń następne dwa kroki.

1. Jeśli aplikacja może obsługiwać tylko jeden certyfikat na raz, wybierz interwał przestoju, aby wykonać następny krok. (W przeciwnym razie, jeśli aplikacja nie odbierze automatycznie nowego certyfikatu, ale może obsłużyć więcej niż jeden certyfikat podpisywania, możesz wykonać następny krok w dowolnym momencie).
1. Przed wygaśnięciem starego certyfikatu postępuj zgodnie z instrukcjami podanymi wcześniej w sekcji Przekazywanie i [aktywowanie](#upload-and-activate-a-certificate) certyfikatu.
1. Zaloguj się do aplikacji, aby upewnić się, że certyfikat działa prawidłowo.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
- [Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Debugowanie logowania jednokrotnego opartego na protokole SAML w aplikacjach w usłudze Azure Active Directory](./debug-saml-sso-issues.md)
