---
title: Często zadawane pytania dotyczące usługi Azure AD Multi-Factor Authentication — Azure Active Directory
description: Często zadawane pytania i odpowiedzi dotyczące usługi Azure AD Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58fbf874339bf287ce1af62e1edb39ec6227d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257106"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Często zadawane pytania dotyczące usługi Azure AD Multi-Factor Authentication

Często zadawane pytania dotyczą często zadawanych pytań dotyczących usługi Azure AD Multi-Factor Authentication i korzystania z usługi Multi-Factor Authentication. Jest on podzielony na pytania dotyczące usługi, ogólnie rzecz biorąc, modele rozliczeń, środowisko użytkownika i rozwiązywanie problemów.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie będzie już oferować serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego od użytkowników, powinni korzystać z usługi Azure AD Multi-Factor Authentication opartej na chmurze. Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i generować poświadczenia aktywacji w zwykły sposób.
>
> Informacje udostępnione poniżej dotyczące Serwer Multi-Factor Authentication platformy Azure mają zastosowanie tylko do użytkowników, którzy mają już uruchomiony serwer usługi MFA.
>
> Licencjonowanie oparte na zużyciu nie jest już dostępne dla nowych klientów obowiązujące od 1 września 2018.
> Od 1 września 2018 nowych dostawców uwierzytelniania nie można już tworzyć. Istniejący dostawcy uwierzytelniania mogą nadal być używane i aktualizowane. Uwierzytelnianie wieloskładnikowe będzie nadal mieć dostępną funkcję w Azure AD — wersja Premium licencji.

## <a name="general"></a>Ogólne

* [Jak usługa Azure Serwer Multi-Factor Authentication obsługuje dane użytkowników?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Jakie krótkie kody programu SMS są używane do wysyłania wiadomości SMS do użytkowników?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Jak usługa Azure Serwer Multi-Factor Authentication obsługuje dane użytkowników?

W przypadku Serwer Multi-Factor Authentication dane użytkownika są przechowywane tylko na serwerach lokalnych. Żadne trwałe dane użytkowników nie są przechowywane w chmurze. Gdy użytkownik przeprowadza weryfikację dwuetapową, Serwer Multi-Factor Authentication wysyła dane do usługi Azure AD Multi-Factor Authentication w chmurze w celu uwierzytelnienia. Komunikacja między Serwer Multi-Factor Authentication i Multi-Factor Authentication usługą w chmurze używa Secure Sockets Layer (SSL) lub Transport Layer Security (TLS) przez port 443 wychodzące.

Gdy żądania uwierzytelniania są wysyłane do usługi w chmurze, dane są zbierane do raportów dotyczących uwierzytelniania i użycia. Następujące pola danych są zawarte w dziennikach weryfikacji dwuetapowej:

* **Unikatowy identyfikator** (nazwa użytkownika lub identyfikator serwer Multi-Factor Authentication lokalnego)
* **Imię i** nazwisko (opcjonalnie)
* **Adres e-mail** (opcjonalnie)
* **Numer telefonu** (w przypadku korzystania z połączenia głosowego lub uwierzytelniania SMS)
* **Token urządzenia** (w przypadku korzystania z uwierzytelniania aplikacji mobilnych)
* **Tryb uwierzytelniania**
* **Wynik uwierzytelniania**
* **Nazwa Serwer Multi-Factor Authentication**
* **Adres IP Serwer Multi-Factor Authentication**
* **Adres IP klienta** (jeśli jest dostępny)

Opcjonalne pola można skonfigurować w Serwer Multi-Factor Authentication.

Wynik weryfikacji (sukces lub odmowa) i powód, jeśli został odrzucony, jest przechowywany w danych uwierzytelniania. Te dane są dostępne w raportach dotyczących uwierzytelniania i użycia.

Aby uzyskać więcej informacji, zobacz [dane dotyczące miejsca zamieszkania i klienta w usłudze Azure AD Multi-Factor Authentication](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Jakie krótkie kody programu SMS są używane do wysyłania wiadomości SMS do użytkowników?

W Stany Zjednoczone używamy następujących krótkich kodów programu SMS:

* *97671*
* *69829*
* *51789*
* *99399*

W Kanadzie używamy następujących krótkich kodów SMS:

* *759731*
* *673801*

Nie ma gwarancji spójnego dostarczania wiadomości SMS lub głosowego Multi-Factor Authentication na podstawie tej samej liczby. W interesie naszych użytkowników możemy dodawać lub usuwać krótkie kody w dowolnym momencie, gdy wprowadzimy zmiany trasy w celu poprawy możliwości dostarczania wiadomości SMS.

Nie obsługujemy krótkich kodów dla krajów lub regionów poza Stany Zjednoczone i Kanadę.

## <a name="billing"></a>Rozliczenia

W przypadku większości pytań dotyczących rozliczeń można odpowiedzieć na [stronie z cennikiem Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) lub z dokumentacją dotyczącą [wersji usługi Azure AD Multi-Factor Authentication i planów użycia](concept-mfa-licensing.md).

* [Czy Moja organizacja nalicza opłaty za wysyłanie połączeń telefonicznych i wiadomości SMS, które są używane do uwierzytelniania?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Czy model rozliczeń dla poszczególnych użytkowników jest odpowiedzialny za wszystkich włączonych użytkowników, czy tylko te, które przeprowadzono weryfikację dwuetapową?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Jak działa rozliczanie Multi-Factor Authentication?](#how-does-multi-factor-authentication-billing-work)
* [Czy istnieje bezpłatna wersja usługi Azure AD Multi-Factor Authentication?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [Czy Moja organizacja może przełączać się między różnymi modelami rozliczeniowymi dla poszczególnych użytkowników i uwierzytelniania?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Czy Moja organizacja może przełączać się między rozliczeniami i subskrypcjami na podstawie użycia (modelem opartym na licencji) w dowolnym momencie?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Czy Moja organizacja musi używać tożsamości i synchronizować je do korzystania z usługi Azure AD Multi-Factor Authentication?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Czy Moja organizacja nalicza opłaty za wysyłanie połączeń telefonicznych i wiadomości SMS, które są używane do uwierzytelniania?

Nie, nie są naliczone opłaty za poszczególne połączenia telefoniczne lub wiadomości SMS wysyłane do użytkowników za pomocą usługi Azure AD Multi-Factor Authentication. Jeśli używasz dostawcy usługi MFA uwierzytelniania, opłaty są naliczane za każde uwierzytelnianie, ale nie dla używanej metody.

Użytkownicy mogą być obciążani opłatami za rozmowy telefoniczne lub wiadomości SMS, które odbierają, zgodnie z ich osobistą usługą telefoniczną.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Czy model rozliczeń dla poszczególnych użytkowników jest odpowiedzialny za wszystkich włączonych użytkowników, czy tylko te, które przeprowadzono weryfikację dwuetapową?

Opłaty są naliczane na podstawie liczby użytkowników skonfigurowanych do korzystania z Multi-Factor Authentication, bez względu na to, czy przeprowadzono weryfikację dwuetapową tego miesiąca.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Jak działa rozliczanie Multi-Factor Authentication?

Podczas tworzenia dostawcy usługi MFA dla użytkownika lub uwierzytelniania w ramach subskrypcji platformy Azure w organizacji jest naliczana miesięczna stawka na podstawie użycia. Ten model rozliczeń jest podobny do tego, jak na platformie Azure są naliczane opłaty za użycie maszyn wirtualnych i Web Apps.

W przypadku zakupu subskrypcji usługi Azure AD Multi-Factor Authentication organizacja płaci tylko roczną opłatę za licencję dla każdego użytkownika. W ten sposób rozliczane są opłaty za licencje usługi MFA oraz Microsoft 365, Azure AD — wersja Premium lub Enterprise Mobility + Security.

Aby uzyskać więcej informacji, zobacz [jak uzyskać Multi-Factor Authentication usługi Azure AD](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Czy istnieje bezpłatna wersja usługi Azure AD Multi-Factor Authentication?

Domyślne ustawienia zabezpieczeń można włączyć w warstwie Azure AD — wersja Bezpłatna. W przypadku domyślnych ustawień zabezpieczeń wszyscy użytkownicy są włączeni do uwierzytelniania wieloskładnikowego przy użyciu aplikacji Microsoft Authenticator. Nie ma możliwości użycia wiadomości tekstowych ani weryfikacji telefonu z domyślnymi ustawieniami zabezpieczeń. tylko Microsoft Authenticator aplikacji.

Aby uzyskać więcej informacji, zobacz [co to są ustawienia domyślne zabezpieczeń?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Czy Moja organizacja może przełączać się między różnymi modelami rozliczeniowymi dla poszczególnych użytkowników i uwierzytelniania?

Jeśli organizacja kupuje uwierzytelnianie wieloskładnikowe jako usługę autonomiczną z rozliczeniami opartymi na zużyciu, wybiera się model rozliczeń podczas tworzenia dostawcy usługi MFA. Nie można zmienić modelu rozliczeń po utworzeniu dostawcy usługi MFA. 

Jeśli dostawca MFA *nie* jest połączony z dzierżawą usługi Azure AD lub zostanie połączony Dostawca usługi MFA z inną dzierżawcą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są transferowane. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

Dowiedz się więcej o dostawcach MFA w temacie [Rozpoczynanie pracy z dostawcą usługi Azure wieloskładnikowe Authentication](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Czy Moja organizacja może przełączać się między rozliczeniami i subskrypcjami na podstawie użycia (modelem opartym na licencji) w dowolnym momencie?

W niektórych przypadkach tak.

Jeśli katalog zawiera dostawcę Multi-Factor Authentication platformy Azure *dla poszczególnych użytkowników* , możesz dodać licencje usługi MFA. Użytkownicy z licencjami nie są wliczane do rozliczania opartego na użytkownikach. Użytkownicy bez licencji nadal mogą być włączeni do uwierzytelniania wieloskładnikowego za pomocą dostawcy usługi MFA. Jeśli kupisz i przypiszesz licencje wszystkim użytkownikom skonfigurowanym do korzystania z Multi-Factor Authentication, możesz usunąć dostawcę Multi-Factor Authentication platformy Azure. Możesz zawsze utworzyć innego dostawcę usługi MFA dla poszczególnych użytkowników, jeśli w przyszłości masz więcej użytkowników niż licencje.

Jeśli katalog zawiera dostawcę usługi Azure Multi-Factor Authentication *na potrzeby uwierzytelniania* , opłaty są naliczane za każde uwierzytelnianie, o ile dostawca usług MFA jest połączony z subskrypcją. Licencje usługi MFA można przypisywać użytkownikom, ale nadal będą naliczane opłaty za każde żądanie weryfikacji dwuetapowej, niezależnie od tego, czy pochodzi ona od kogoś, kto ma przypisaną licencję usługi MFA.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>Czy Moja organizacja musi używać tożsamości i synchronizować je do korzystania z usługi Azure AD Multi-Factor Authentication?

Jeśli Twoja organizacja korzysta z modelu rozliczania opartego na użyciu, Azure Active Directory jest opcjonalne, ale nie jest wymagane. Jeśli dostawca MFA nie jest połączony z dzierżawą usługi Azure AD, możesz wdrożyć tylko usługę Azure Serwer Multi-Factor Authentication lokalnie.

Azure Active Directory jest wymagany dla modelu licencji, ponieważ licencje są dodawane do dzierżawy usługi Azure AD podczas zakupu i przypisywania do użytkowników w katalogu.

## <a name="manage-and-support-user-accounts"></a>Manage and support user accounts (Zarządzanie kontami użytkowników i związana z nimi pomoc techniczna)

* [Co mam zrobić, jeśli nie otrzymasz odpowiedzi na telefonie?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Co należy zrobić, jeśli nikt z użytkowników nie może uzyskać do swojego konta?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Co należy zrobić, jeśli jeden z użytkowników utraci telefon korzystający z haseł aplikacji?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Co zrobić, jeśli użytkownik nie może się zalogować do aplikacji niekorzystających z przeglądarki?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Moi użytkownicy mówią, że czasami nie otrzymają wiadomości tekstowej lub przechodzą limit czasu weryfikacji.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Czy mogę zmienić czas, jaki wszyscy użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości tekstowej przed upływem limitu czasu systemu?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Czy mogę używać tokenów sprzętowych z usługą Azure Serwer Multi-Factor Authentication?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Czy mogę użyć usługi Azure Serwer Multi-Factor Authentication do zabezpieczenia usług terminalowych?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Identyfikator rozmówcy został skonfigurowany na serwerze usługi MFA, ale użytkownicy nadal otrzymują Multi-Factor Authentication wywołania z anonimowego obiektu wywołującego.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Dlaczego mój użytkownik jest monitowany o zarejestrowanie informacji o zabezpieczeniach?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Co mam zrobić, jeśli nie otrzymasz odpowiedzi na telefonie?

Użytkownicy mogą próbować do pięciu razy w ciągu 5 minut, aby uzyskać połączenie telefoniczne lub wiadomość SMS w celu uwierzytelnienia. Firma Microsoft używa wielu dostawców do dostarczania wywołań i wiadomości SMS. Jeśli takie podejście nie działa, otwórz sprawę pomocy technicznej, aby rozwiązać problem.

Aplikacje zabezpieczeń innych firm mogą również blokować kod weryfikacyjny wiadomości tekstowej lub połączenia telefonicznego. Jeśli używasz aplikacji zabezpieczeń innej firmy, spróbuj wyłączyć ochronę, a następnie poprosić o wysłanie innego kodu weryfikacyjnego uwierzytelniania MFA.

Jeśli powyższe kroki nie działają, należy sprawdzić, czy użytkownicy są skonfigurowani dla więcej niż jednej metody weryfikacji. Spróbuj zalogować się ponownie, ale wybierz inną metodę weryfikacji na stronie logowania.

Aby uzyskać więcej informacji, zobacz [Przewodnik rozwiązywania problemów użytkownika końcowego](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Co należy zrobić, jeśli nikt z użytkowników nie może uzyskać do swojego konta?

Możesz zresetować konto użytkownika, przechodząc przez proces rejestracji. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą usługi Azure AD Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Co należy zrobić, jeśli jeden z użytkowników utraci telefon korzystający z haseł aplikacji?

Aby zapobiec nieautoryzowanemu dostępowi, Usuń wszystkie hasła aplikacji użytkownika. Gdy użytkownik ma urządzenie zastępcze, może ponownie utworzyć hasła. Dowiedz się więcej o [zarządzaniu ustawieniami użytkowników i urządzeń za pomocą usługi Azure AD Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Co zrobić, jeśli użytkownik nie może się zalogować do aplikacji niekorzystających z przeglądarki?

Jeśli Twoja organizacja nadal używa starszych klientów i [zezwolono na korzystanie z haseł aplikacji](howto-mfa-app-passwords.md), użytkownicy nie będą mogli zalogować się do tych starszych klientów przy użyciu ich nazwy użytkownika i hasła. Zamiast tego należy [skonfigurować hasła aplikacji](../user-help/multi-factor-authentication-end-user-app-passwords.md). Użytkownicy muszą wyczyścić (usunąć) informacje logowania, ponownie uruchomić aplikację, a następnie zalogować się przy użyciu nazwy użytkownika i *hasła aplikacji* zamiast zwykłego hasła.

Jeśli Twoja organizacja nie ma starszych klientów, nie należy zezwalać użytkownikom na tworzenie haseł aplikacji.

> [!NOTE]
> **Nowoczesne uwierzytelnianie dla klientów pakietu Office 2013**
>
> Hasła aplikacji są wymagane tylko w przypadku aplikacji, które nie obsługują nowoczesnego uwierzytelniania. Klienci pakietu Office 2013 obsługują nowoczesne protokoły uwierzytelniania, ale muszą być skonfigurowane. Nowoczesne uwierzytelnianie jest dostępne dla każdego klienta z marca 2015 lub nowszą aktualizacją pakietu Office 2013. Aby uzyskać więcej informacji, zobacz wpis w blogu [zaktualizowany nowoczesny pakiet Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Moi użytkownicy mówią, że czasami nie otrzymają wiadomości tekstowej lub przechodzą limit czasu weryfikacji.

Dostarczanie komunikatów SMS nie jest gwarantowane, ponieważ istnieją niekontrolowane czynniki, które mogą mieć wpływ na niezawodność usługi. Te czynniki obejmują docelowy kraj lub region, przewoźnika komórkowego i siłę sygnału.

Aplikacje zabezpieczeń innych firm mogą również blokować kod weryfikacyjny wiadomości tekstowej lub połączenia telefonicznego. Jeśli używasz aplikacji zabezpieczeń innej firmy, spróbuj wyłączyć ochronę, a następnie poprosić o wysłanie innego kodu weryfikacyjnego uwierzytelniania MFA.

Jeśli użytkownicy często mają problemy z niezawodnymi komunikatami SMS, poinformuj ich o tym, aby zamiast tego używać metody aplikacji Microsoft Authenticator lub rozmowy telefonicznej. Microsoft Authenticator może odbierać powiadomienia zarówno przez połączenia komórkowe, jak i Wi-Fi. Ponadto aplikacja mobilna może generować kody weryfikacyjne nawet wtedy, gdy urządzenie nie ma żadnego sygnału. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) i [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Czy mogę zmienić czas, jaki wszyscy użytkownicy muszą wprowadzić kod weryfikacyjny z wiadomości tekstowej przed upływem limitu czasu systemu?

W niektórych przypadkach tak.

W przypadku jednokierunkowego SMS z usługą Azure MFA Server w wersji 7.0 lub nowszej można skonfigurować ustawienie limitu czasu, ustawiając klucz rejestru. Po wysłaniu wiadomości tekstowej przez usługę MFA w chmurze kod weryfikacyjny (lub jednorazowy kod dostępu) jest zwracany do serwera usługi MFA. Serwer MFA domyślnie przechowuje kod w pamięci przez 300 sekund. Jeśli użytkownik nie wprowadzi kodu przed upływem 300 sekund, jego uwierzytelnienie zostanie odrzucone. Wykonaj następujące kroki, aby zmienić domyślne ustawienie limitu czasu:

1. Przejdź do witryny `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Utwórz klucz rejestru **typu DWORD** o nazwie *pfsvc_pendingSmsTimeoutSeconds* i Ustaw czas w sekundach, w ciągu którego serwer usługi Azure MFA ma przechowywać kody dostępu jednorazowego.

>[!TIP]
>
> Jeśli masz wiele serwerów MFA, tylko te, które przetworzyły oryginalne żądanie uwierzytelnienia, znają kod weryfikacyjny, który został wysłany do użytkownika. Gdy użytkownik wprowadzi kod, żądanie uwierzytelnienia do weryfikacji musi być wysłane do tego samego serwera. Jeśli Walidacja kodu jest wysyłana na inny serwer, uwierzytelnianie zostanie odrzucone.

Jeśli użytkownicy nie odpowiadają na wiadomość SMS w określonym limicie czasu, uwierzytelnienie zostanie odrzucone.

W przypadku jednokierunkowej wiadomości SMS z usługą Azure AD MFA w chmurze (w tym karty AD FS lub rozszerzenia serwera zasad sieciowych) nie można skonfigurować ustawienia limitu czasu. Usługa Azure AD przechowuje kod weryfikacyjny przez 180 sekund.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Czy mogę używać tokenów sprzętowych z usługą Azure Serwer Multi-Factor Authentication?

Jeśli używasz usługi Azure Serwer Multi-Factor Authentication, możesz zaimportować tokeny hasła jednorazowego (TOTP) innej firmy, a następnie użyć ich do weryfikacji dwuetapowej.

Po umieszczeniu klucza tajnego w pliku CSV i zaimportowaniu do usługi Azure Serwer Multi-Factor Authentication można użyć tokenów ActiveIdentity, które są tokenami TOTP. Tokeny OATH można używać z Active Directory Federation Services (AD FS), uwierzytelnianie oparte na formularzach programu Internet Information Server (IIS) i Usługa telefonujących użytkowników zdalnego uwierzytelniania (RADIUS), o ile system klienta może akceptować dane wejściowe użytkownika.

Można importować tokeny TOTP OATH innych firm o następujących formatach:  

- Przenośny kontener kluczy symetrycznych (PSKC)
- CSV, jeśli plik zawiera numer seryjny, klucz tajny w podstawowym formacie 32 i przedział czasu

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Czy mogę użyć usługi Azure Serwer Multi-Factor Authentication do zabezpieczenia usług terminalowych?

Tak, ale jeśli korzystasz z systemu Windows Server 2012 R2 lub nowszego, możesz zabezpieczyć tylko usługi terminalowe przy użyciu bramy Pulpit zdalny (Brama usług pulpitu zdalnego).

Zmiany zabezpieczeń w systemie Windows Server 2012 R2 zmieniły sposób, w jaki usługa Azure Serwer Multi-Factor Authentication nawiązuje połączenie z pakietem zabezpieczeń urzędu zabezpieczeń lokalnych (LSA) w systemie Windows Server 2012 i wcześniejszych wersjach. W przypadku wersji usług terminalowych w systemie Windows Server 2012 lub starszym można [zabezpieczyć aplikację z uwierzytelnianiem systemu Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). W przypadku korzystania z systemu Windows Server 2012 R2 potrzebna jest Brama usług pulpitu zdalnego.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Identyfikator rozmówcy został skonfigurowany na serwerze usługi MFA, ale użytkownicy nadal otrzymują Multi-Factor Authentication wywołania z anonimowego obiektu wywołującego.

Gdy Multi-Factor Authentication wywołania są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są kierowane przez operatora, który nie obsługuje identyfikatora rozmówcy. Ze względu na to zachowanie operatora, identyfikator wywołującego nie jest gwarantowany, mimo że system Multi-Factor Authentication zawsze go wysyła.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Dlaczego mój użytkownik jest monitowany o zarejestrowanie informacji o zabezpieczeniach?

Istnieje kilka powodów, dla których użytkownicy mogą uzyskać monit o zarejestrowanie informacji o zabezpieczeniach:

- Użytkownik włączył uwierzytelnianie MFA przez administratora w usłudze Azure AD, ale nie ma jeszcze zarejestrowanych informacji o zabezpieczeniach dla konta.
- Użytkownik włączył funkcję samoobsługowego resetowania hasła w usłudze Azure AD. Informacje o zabezpieczeniach ułatwią im zresetowanie hasła w przyszłości, jeśli kiedykolwiek zapomnisz.
- Użytkownik uzyskał dostęp do aplikacji, która ma zasady dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego i nie została wcześniej zarejestrowana dla usługi MFA.
- Użytkownik rejestruje urządzenie w usłudze Azure AD (w tym za pomocą usługi Azure AD join) i organizacja wymaga uwierzytelniania wieloskładnikowego na potrzeby rejestracji urządzeń, ale użytkownik nie był wcześniej zarejestrowany dla usługi MFA.
- Użytkownik generuje usługę Windows Hello dla firm w systemie Windows 10 (która wymaga uwierzytelniania MFA) i nie została wcześniej zarejestrowana dla usługi MFA.
- Organizacja utworzyła i włączyła zasady rejestracji usługi MFA, które zostały zastosowane do użytkownika.
- Użytkownik zarejestrował się wcześniej do uwierzytelniania wieloskładnikowego, ale wybiera metodę weryfikacji, która została wyłączona przez administratora. W związku z tym użytkownik musi ponownie wykonać rejestrację usługi MFA, aby wybrać nową domyślną metodę weryfikacji.

## <a name="errors"></a>błędy

* [Co powinni zrobić użytkownicy, jeśli podczas korzystania z powiadomień aplikacji mobilnej zobaczysz komunikat o błędzie "żądanie uwierzytelnienia nie dotyczy aktywowanego konta"?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Co powinni zrobić użytkownicy, Jeśli zobaczysz komunikat o błędzie 0x800434D4L podczas logowania do aplikacji nie korzystającej z przeglądarki?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Co powinni zrobić użytkownicy, jeśli podczas korzystania z powiadomień aplikacji mobilnej zobaczysz komunikat o błędzie "żądanie uwierzytelnienia nie dotyczy aktywowanego konta"?

Poproszenie użytkownika o wykonanie poniższej procedury w celu usunięcia konta z Microsoft Authenticator, a następnie dodania go ponownie:

1. Przejdź do [swojego profilu Azure Portal](https://account.activedirectory.windowsazure.com/profile/) i zaloguj się przy użyciu konta organizacyjnego.
2. Wybierz opcję **dodatkowej weryfikacji zabezpieczeń**.
3. Usuń istniejące konto z aplikacji Microsoft Authenticator.
4. Kliknij przycisk **Konfiguruj**, a następnie postępuj zgodnie z instrukcjami, aby ponownie skonfigurować Microsoft Authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Co powinni zrobić użytkownicy, Jeśli zobaczysz komunikat o błędzie 0x800434D4L podczas logowania do aplikacji nie korzystającej z przeglądarki?

*0x800434D4L* błąd występuje podczas próby zalogowania się do aplikacji nie korzystającej z przeglądarki zainstalowanej na komputerze lokalnym, która nie działa z kontami wymagającymi weryfikacji dwuetapowej.

Obejście tego błędu ma na celu oddzielenie kont użytkowników dla operacji związanych z administratorami i nieadministratorami. Później można połączyć skrzynki pocztowe między kontem administratora i kontem nieadministracyjnym, aby można było zalogować się do programu Outlook przy użyciu konta nienależącego do administratora. Aby uzyskać więcej informacji na temat tego rozwiązania, Dowiedz się, jak [dać administratorowi możliwość otwierania i wyświetlania zawartości skrzynki pocztowej użytkownika](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Następne kroki

Jeśli na pytanie nie ma odpowiedzi, dostępne są następujące opcje pomocy technicznej:

* Wyszukaj rozwiązania typowych problemów technicznych w [Pomoc techniczna firmy Microsoft bazie wiedzy](https://support.microsoft.com) .
* Wyszukaj i Przeglądaj pytania techniczne oraz odpowiedzi ze społeczności lub zadawaj swoje pytanie w [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html).
* Skontaktuj się z pomocą techniczną firmy Microsoft za pomocą [usługi Azure serwer Multi-Factor Authentication](https://support.microsoft.com/oas/default.aspx?prid=14947). Gdy kontaktuje się z nami, jest to przydatne, jeśli możesz dołączyć tyle informacji o problemie, ile to możliwe. Informacje, które można podać, obejmują stronę, na której wydano błąd, konkretny kod błędu, identyfikator konkretnej sesji oraz identyfikator użytkownika, który wykorzystał błąd.
* Jeśli jesteś starszym klientem PhoneFactor i masz pytania lub potrzebujesz pomocy przy resetowaniu hasła, użyj [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) adresu e-mail, aby otworzyć zgłoszenie do pomocy technicznej.
