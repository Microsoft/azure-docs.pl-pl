---
title: Azure Active Directory logowanie bezhasło
description: Informacje o opcjach logowania bezhasło w celu Azure Active Directory przy użyciu kluczy zabezpieczeń FIDO2 lub aplikacji Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e36acb5a6a0984b003e4e8deb597bddfc43b064a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647561"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opcje uwierzytelniania bezhasło dla Azure Active Directory

Funkcje, takie jak uwierzytelnianie wieloskładnikowe (MFA), to doskonały sposób na zabezpieczenie organizacji, ale użytkownicy często uzyskują sfrustrowani z dodatkową warstwą zabezpieczeń na potrzeby zapamiętywania haseł. Metody uwierzytelniania bez hasła są wygodniejsze, ponieważ hasło jest usuwane i zastępowane przez użytkownika

| Authentication  | Coś, co masz | Coś lub wiesz |
| --- | --- | --- |
| Logowanie bez hasła | Urządzenia, numery telefonów lub klucze zabezpieczeń systemu Windows 10 | Biometryczna lub PIN |

Każda organizacja ma inne potrzeby związane z uwierzytelnianiem. Firma Microsoft oferuje następujące trzy opcje uwierzytelniania bez hasła, które integrują się z usługą Azure Active Directory (Azure AD):

- Windows Hello for Business
- Aplikacja Microsoft Authenticator
- FIDO2 klucze zabezpieczeń

![Uwierzytelnianie: zabezpieczenia i wygoda](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Funkcja Windows Hello dla firm jest idealna w przypadku pracowników przetwarzających informacje, którzy mają własne Wyznaczeni komputery z systemem Windows. Poświadczenia biometryczne i numery PIN są bezpośrednio powiązane z komputerem użytkownika, co uniemożliwia dostęp od nikogo innego niż właściciel. Dzięki integracji infrastruktury kluczy publicznych (PKI) i wbudowanej obsłudze logowania jednokrotnego (SSO) usługa Windows Hello dla firm zapewnia wygodną metodę bezproblemowego uzyskiwania dostępu do zasobów firmy lokalnie i w chmurze.

![Przykład logowania użytkownika przy użyciu usługi Windows Hello dla firm](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Poniższe kroki pokazują, jak proces logowania współdziała z usługą Azure AD:

![Diagram przedstawiający kroki związane z logowaniem użytkownika przy użyciu usługi Windows Hello dla firm](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Użytkownik loguje się do systemu Windows za pomocą gestu biometrycznego lub numeru PIN. Gest odblokowuje klucz prywatny usługi Windows Hello dla firm i jest wysyłany do dostawcy obsługi zabezpieczeń uwierzytelniania w chmurze, zwanego *dostawcą AP usługi Cloud*.
1. Dostawca AP czeka w chmurze żąda identyfikatora jednorazowego (losowo dowolnej liczby, która może być używana tylko raz) z usługi Azure AD.
1. Usługa Azure AD zwraca identyfikator jednorazowy ważny przez 5 minut.
1. Dostawca AP w chmurze podpisuje identyfikator jednorazowy przy użyciu klucza prywatnego użytkownika i zwraca podpisany identyfikator jednorazowy do usługi Azure AD.
1. Usługa Azure AD weryfikuje podpisany identyfikator jednorazowy przy użyciu bezpiecznie zarejestrowanego klucza publicznego użytkownika w odniesieniu do sygnatury jednorazowej. Po sprawdzeniu podpisu usługa Azure AD następnie weryfikuje zwrócony podpisany identyfikator jednorazowy. Po sprawdzeniu poprawności identyfikatora jednorazowego usługa Azure AD tworzy podstawowy token odświeżania (PRT) z kluczem sesji zaszyfrowanym do klucza transportu urządzenia i zwraca go do dostawcy usługi AP.
1. Dostawca AP usług w chmurze odbiera zaszyfrowany PRT za pomocą klucza sesji. Przy użyciu prywatnego klucza transportowego urządzenia dostawca AP w chmurze odszyfrowuje klucz sesji i chroni klucz sesji przy użyciu Trusted Platform Module urządzenia (TPM).
1. Dostawca AP w chmurze zwraca pomyślną odpowiedź uwierzytelniania do systemu Windows. Następnie użytkownik może uzyskać dostęp do systemu Windows, a także aplikacji lokalnych i w chmurze bez konieczności ponownego uwierzytelniania (SSO).

[Przewodnik planowania](/windows/security/identity-protection/hello-for-business/hello-planning-guide) usługi Windows Hello dla firm może służyć do podejmowania decyzji dotyczących typu wdrożenia usługi Windows Hello dla firm i opcji, które należy wziąć pod uwagę.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Możesz również pozwolić, aby telefon pracownika stał się metodą uwierzytelniania bezhasłem. Aplikacja Microsoft Authenticator może już być używana jako wygodna opcja uwierzytelniania wieloskładnikowego oprócz hasła. Możesz również użyć aplikacji Authenticator jako opcji bezhaseł.

![Zaloguj się do przeglądarki Microsoft Edge przy użyciu aplikacji Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Aplikacja Authenticator włącza każdy telefon z systemem iOS lub Android do silnego poświadczenia bez hasła. Użytkownicy mogą logować się do dowolnej platformy lub przeglądarki, uzyskując powiadomienie na telefonie, dopasowując liczbę wyświetlaną na ekranie na telefonie, a następnie używając ich biometrycznych (dotykowych lub ubocznych) lub numerów PIN, aby potwierdzić. Zapoznaj się z artykułem [pobieranie i instalowanie aplikacji Microsoft Authenticator,](../user-help/user-help-auth-app-download-install.md) Aby uzyskać szczegółowe informacje dotyczące instalacji.

Uwierzytelnianie bezhasła przy użyciu aplikacji Authenticator jest zgodne z tym samym wzorcem, co w przypadku usługi Windows Hello dla firm. Jest to nieco bardziej skomplikowany sposób, w jaki użytkownik musi zostać zidentyfikowany, aby usługa Azure AD mogła znaleźć używaną wersję aplikacji Microsoft Authenticator:

![Diagram przedstawiający kroki związane z logowaniem użytkownika przy użyciu aplikacji Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Użytkownik wprowadza nazwę użytkownika.
1. Usługa Azure AD wykryje, że użytkownik ma silne poświadczenie i uruchamia strumień silnego poświadczenia.
1. Powiadomienie jest wysyłane do aplikacji za pośrednictwem Apple Push Notification Service (APNS) na urządzeniach z systemem iOS lub za pośrednictwem usługi Firebase Cloud Messaging (FCM) na urządzeniach z systemem Android.
1. Użytkownik odbiera Powiadomienie wypychane i otwiera aplikację.
1. Aplikacja wywołuje usługę Azure AD i odbiera żądanie potwierdzenia obecności oraz identyfikator jednorazowy.
1. Użytkownik kończy wyzwanie przez wprowadzenie ich biometrycznych lub numerów PIN w celu odblokowania klucza prywatnego.
1. Identyfikator jednorazowy jest podpisywany z kluczem prywatnym i wysyłany z powrotem do usługi Azure AD.
1. Usługa Azure AD wykonuje walidację klucza publicznego/prywatnego i zwraca token.

Aby rozpocząć logowanie bez hasła, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Włączanie podpisywania bezhasła przy użyciu aplikacji uwierzytelniania](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 klucze zabezpieczeń

FIDO (Fast IDentity online) Alliance pomaga wspierać otwarte standardy uwierzytelniania i zmniejszać użycie haseł jako formy uwierzytelniania. FIDO2 to najnowszy standard, który obejmuje Standard Uwierzytelnianie sieci Web (WebAuthn).

Klucze zabezpieczeń FIDO2 to metoda uwierzytelniania bez hasła oparta na standardach, która może mieć dowolny współczynnik form. Szybka tożsamość w trybie online (FIDO) to otwarty standard uwierzytelniania bezhaseł. FIDO umożliwia użytkownikom i organizacjom korzystanie ze standardu do logowania się do swoich zasobów bez użycia nazwy użytkownika ani hasła przy użyciu zewnętrznego klucza zabezpieczeń lub klucza platformy wbudowanego w urządzenie.

Użytkownicy mogą rejestrować i wybierać klucz zabezpieczeń FIDO2 w interfejsie logowania jako główny sposób uwierzytelniania. Te klucze zabezpieczeń FIDO2 są zazwyczaj urządzeniami USB, ale mogą również korzystać z połączenia Bluetooth lub NFC. W przypadku urządzenia sprzętowego, które obsługuje uwierzytelnianie, zabezpieczenia konta są zwiększane, ponieważ nie ma hasła, które może być ujawnione lub odgadnąć.

Klucze zabezpieczeń FIDO2 mogą służyć do logowania się do usługi Azure AD lub hybrydowych urządzeń z systemem Windows 10 przyłączonych do usługi Azure AD i uzyskiwania rejestracji jednokrotnej w swoich zasobach w chmurze i lokalnych. Użytkownicy mogą również zalogować się w obsługiwanych przeglądarkach. Klucze zabezpieczeń FIDO2 są świetną opcją dla przedsiębiorstw, które mają bardzo duże znaczenie dla bezpieczeństwa lub mają scenariusze lub pracowników, którzy nie chcą korzystać z telefonu jako drugiego czynnika.

![Zaloguj się do przeglądarki Microsoft Edge przy użyciu klucza zabezpieczeń](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Następujący proces jest używany, gdy użytkownik loguje się przy użyciu klucza zabezpieczeń FIDO2:

![Diagram przedstawiający kroki związane z logowaniem użytkownika przy użyciu klucza zabezpieczeń FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Użytkownik podłącza klucz zabezpieczeń FIDO2 na swoim komputerze.
2. System Windows wykrywa klucz zabezpieczeń FIDO2.
3. System Windows wysyła żądanie uwierzytelnienia.
4. Usługa Azure AD wysyła kopię zapasową.
5. Użytkownik wykonuje swój gest, aby odblokować klucz prywatny zapisany w bezpiecznym enklawy klucz zabezpieczeń FIDO2.
6. Klucz zabezpieczeń FIDO2 podpisuje identyfikator jednorazowy kluczem prywatnym.
7. Żądanie tokenu podstawowego odświeżania tokenu (PRT) z podpisanym identyfikatorem jednorazowym jest wysyłane do usługi Azure AD.
8. Usługa Azure AD weryfikuje podpisany identyfikator jednorazowy przy użyciu klucza publicznego FIDO2.
9. Usługa Azure AD zwraca PRT, aby umożliwić dostęp do zasobów lokalnych.

Chociaż istnieje wiele kluczy, które są FIDO2 certyfikowane przez usługi FIDO Alliance, firma Microsoft wymaga, aby niektóre opcjonalne rozszerzenia specyfikacji protokołu Klient-wystawca FIDO2 (CTAP) zostały zaimplementowane przez dostawcę w celu zapewnienia maksymalnego poziomu zabezpieczeń i najlepszego środowiska.

Klucz zabezpieczeń **musi** implementować następujące funkcje i rozszerzenia z protokołu CTAP FIDO2, aby były zgodne z firmą Microsoft:

| # | Zaufanie funkcji/rozszerzenia | Dlaczego ta funkcja lub rozszerzenie jest wymagane? |
| --- | --- | --- |
| 1 | Klucz rezydentny | Ta funkcja umożliwia przenośny Klucz zabezpieczeń, w którym Twoje poświadczenia są przechowywane w kluczu zabezpieczeń. |
| 2 | Numer PIN klienta | Ta funkcja umożliwia ochronę poświadczeń przy użyciu drugiego czynnika i ma zastosowanie do kluczy zabezpieczeń, które nie mają interfejsu użytkownika. |
| 3 | HMAC-Secret | To rozszerzenie zapewnia możliwość zalogowania się na urządzeniu, gdy jest ono wyłączone lub w trybie samolotowym. |
| 4 | Wiele kont na jednostkę UZALEŻNIONą | Ta funkcja zapewnia, że można użyć tego samego klucza zabezpieczeń dla wielu usług, takich jak konto Microsoft i Azure Active Directory. |

### <a name="fido2-security-key-providers"></a>FIDO2 dostawcy kluczy zabezpieczeń

Następujący dostawcy oferują klucze zabezpieczeń FIDO2 różnego rodzaju, które są znane jako zgodne z funkcją bezhaseł. Zachęcamy do ocenienia właściwości zabezpieczeń tych kluczy, kontaktując się z dostawcą oraz FIDO Alliance.

| Dostawca | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| INTERFEJSU | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Rozwiązania TrustKey | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Firmy Gemalto (Grupa firmy Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Token2 Szwajcaria | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |

> [!NOTE]
> Jeśli kupisz i planujesz korzystanie z kluczy zabezpieczeń opartych na NFC, musisz mieć obsługiwany czytnik NFC dla klucza zabezpieczeń. Czytnik NFC nie jest wymaganiem ani ograniczeniem platformy Azure. Aby uzyskać listę obsługiwanych czytników NFC, należy skontaktować się z dostawcą dla klucza zabezpieczeń opartego na NFC.

Jeśli jesteś dostawcą i chcesz uzyskać urządzenie na tej liście obsługiwanych urządzeń, skontaktuj się z firmą [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

Aby rozpocząć pracę z kluczami zabezpieczeń FIDO2, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Włączanie podpisywania bezhasła przy użyciu kluczy zabezpieczeń FIDO2](howto-authentication-passwordless-security-key.md)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Obowiązują następujące zastrzeżenia:

- Administratorzy mogą włączać metody uwierzytelniania bezhasło dla swojej dzierżawy
- Administratorzy mogą kierować wszystkich użytkowników lub wybierać użytkowników/grupy w ramach ich dzierżawy dla każdej metody
- Użytkownicy końcowi mogą rejestrować te metody uwierzytelniania bezhasło i zarządzać nimi w portalu konta
- Użytkownicy końcowi mogą logować się przy użyciu następujących metod uwierzytelniania bez hasła:
   - Aplikacja Microsoft Authenticator: działa w scenariuszach, w których jest używane uwierzytelnianie usługi Azure AD, w tym wszystkie przeglądarki, podczas instalacji systemu Windows 10 oraz zintegrowane aplikacje mobilne w dowolnym systemie operacyjnym.
   - Klucze zabezpieczeń: Pracuj na ekranie blokady dla systemu Windows 10 i sieci Web w obsługiwanych przeglądarkach, takich jak Microsoft Edge (starsza i Nowa krawędź).

## <a name="choose-a-passwordless-method"></a>Wybierz metodę bezhasło

Wybór między tymi trzema opcjami bezhaseł zależy od zabezpieczeń, platformy i wymagań aplikacji firmy.

Oto kilka czynników, które należy wziąć pod uwagę podczas wybierania technologii bezhasło Microsoft:

||**Windows Hello for Business**|**Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator**|**FIDO2 klucze zabezpieczeń**|
|:-|:-|:-|:-|
|**Wymagania wstępne**| Windows 10, wersja 1809 lub nowsza<br>Azure Active Directory| Aplikacja Microsoft Authenticator<br>Telefon (urządzenia z systemem iOS i Android z systemem Android 6,0 lub nowszym)|Windows 10, wersja 1903 lub nowsza<br>Azure Active Directory|
|**Tryb**|Platforma|Oprogramowanie|Sprzęt|
|**Systemy i urządzenia**|KOMPUTER z wbudowaną Trusted Platform Module (TPM)<br>Rozpoznawanie kodu PIN i biometrii |Rozpoznawanie kodu PIN i biometrii na telefonie|FIDO2 urządzenia zabezpieczające zgodne z firmą Microsoft|
|**Środowisko użytkownika**|Zaloguj się przy użyciu kodu PIN lub rozpoznawania biometrycznego (twarzy, Iris lub odcisku palca) z urządzeniami z systemem Windows.<br>Uwierzytelnianie przy użyciu systemu Windows Hello jest powiązane z urządzeniem; Aby uzyskać dostęp do zasobów firmy, użytkownik potrzebuje zarówno urządzenia, jak i składnika logowania, takiego jak kod PIN lub czynnik biometryczny.|Zaloguj się przy użyciu telefonu komórkowego z funkcją skanowania odcisków palców, rozpoznawania twarzy lub tęczówki lub numeru PIN.<br>Użytkownicy logują się do konta służbowego lub osobistego z komputera lub telefonu komórkowego.|Zaloguj się przy użyciu urządzenia zabezpieczeń FIDO2 (biometrii, kod PIN i NFC)<br>Użytkownik może uzyskać dostęp do urządzenia w oparciu o kontrolki organizacji i uwierzytelniać się na podstawie kodu PIN, biometrii przy użyciu urządzeń, takich jak klucze zabezpieczeń USB i karty inteligentne obsługujące NFC, klucze lub noszenia.|
|**Włączone scenariusze**| Korzystanie z urządzenia z systemem Windows bez hasła.<br>Dotyczy dedykowanego komputera służbowego z możliwością logowania jednokrotnego do urządzenia i aplikacji.|Rozwiązanie z obsługą hasła bez miejsca przy użyciu telefonu komórkowego.<br>Dotyczy uzyskiwania dostępu do służbowych aplikacji w sieci Web z dowolnego urządzenia.|Środowisko pracy bez hasła dla pracowników korzystających z biometrii, numeru PIN i NFC.<br>Dotyczy komputerów udostępnionych i lokalizacji, w których telefon komórkowy nie jest żywotną opcją (na przykład dla personelu pomocy technicznej, kiosku publicznego lub zespołu szpitalowego)|

Skorzystaj z poniższej tabeli, aby wybrać metodę, która będzie obsługiwać Twoje wymagania i użytkowników.

|Osoba|Scenariusz|Środowisko|Technologia bezhasło|
|:-|:-|:-|:-|
|**Administracja**|Zabezpieczanie dostępu do urządzenia na potrzeby zadań zarządzania|Przypisane urządzenie z systemem Windows 10|Klucz zabezpieczeń usługi Windows Hello dla firm i/lub FIDO2|
|**Administracja**|Zadania zarządzania na urządzeniach z systemem innym niż Windows| Urządzenie przenośne lub inne niż systemu Windows|Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator|
|**Pracownik przetwarzający informacje**|Praca w produktywności|Przypisane urządzenie z systemem Windows 10|Klucz zabezpieczeń usługi Windows Hello dla firm i/lub FIDO2|
|**Pracownik przetwarzający informacje**|Praca w produktywności| Urządzenie przenośne lub inne niż systemu Windows|Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator|
|**Teraźniejszości proces roboczy**|Kioski w fabryce, zakładzie, sprzedaży detalicznej lub danych|Udostępnione urządzenia z systemem Windows 10|FIDO2 klucze zabezpieczeń|

## <a name="next-steps"></a>Następne kroki

Aby zacząć korzystać z hasła w usłudze Azure AD, wykonaj jedną z następujących czynności:

* [Włącz logowanie bezhasłem klucza zabezpieczeń FIDO2](howto-authentication-passwordless-security-key.md)
* [Włącz logowanie za pomocą telefonu bez hasła przy użyciu aplikacji uwierzytelniania](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Linki zewnętrzne

* [FIDO Alliance](https://fidoalliance.org/)
* [Specyfikacja usługi CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
