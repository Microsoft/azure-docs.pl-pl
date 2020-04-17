---
title: Planowanie wdrożenia uwierzytelniania bez hasła za pomocą usługi Azure AD
description: Dowiedz się, jak zaplanować i wdrożyć implementację uwierzytelniania bez hasła usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ed549e51b911452bca7d4d4a16c7ef45594a8f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451435"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planowanie wdrożenia uwierzytelniania bez hasła w usłudze Azure Active Directory

> [!NOTE]
> Aby utworzyć wersję offline tego planu wdrożenia, użyj funkcji Drukuj w formacie PDF przeglądarki.

Większość cyberataków zaczyna się od zagrożonej nazwy użytkownika i hasła. Organizacje próbują przeciwdziałać zagrożeniu, wymagając od użytkowników użycia jednego z następujących podejść:

- Długie hasła
- Złożone hasła
- Częste zmiany hasła
- Multi-Factor Authentication (MFA)

Badania Microsoftu [pokazują,](https://aka.ms/passwordguidance) że te wysiłki denerwują użytkowników i podwyższają koszty pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Pa $ $word nie ma znaczenia](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Korzyści z uwierzytelniania bez hasła

- **Zwiększone bezpieczeństwo**. Zmniejsz ryzyko wyłudzania informacji i ataków na hasło, usuwając hasła jako obszar ataku.
-  **Lepsze wrażenia użytkownika**. Zapewnij użytkownikom wygodny sposób dostępu do danych z dowolnego miejsca. Zapewnij łatwy dostęp do aplikacji i usług, takich jak Outlook, OneDrive lub Office na urządzeniach mobilnych.
-  **Solidne spostrzeżenia**. Uzyskaj wgląd w działania użytkowników bez hasła dzięki niezawodnemu rejestrowaniu i inspekcji.

Dzięki bez hasła hasło jest zastępowane czymś, co masz, a także czymś, czym jesteś lub czymś, co znasz. Na przykład funkcja Windows Hello dla firm może używać gestu biometrycznego, takiego jak twarz lub odcisk palca, lub numeru PIN specyficznego dla urządzenia, który nie jest przesyłany przez sieć.

## <a name="passwordless-authentication-methods"></a>Metody uwierzytelniania bez hasła
Firma Microsoft oferuje trzy opcje uwierzytelniania bez hasła, które obejmują wiele scenariuszy. Metody te mogą być stosowane w tandemie:

- [Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) jest najlepszy dla użytkowników na dedykowanych komputerach z systemem Windows.
- Logowanie klucza zabezpieczeń za pomocą [kluczy zabezpieczeń FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) jest szczególnie przydatne dla użytkowników, którzy logują się do udostępnionych komputerów, takich jak kioski, w sytuacjach, gdy korzystanie z telefonów jest ograniczone, oraz dla wysoce uprzywilejowanych tożsamości.
- Logowanie telefoniczne za pomocą [aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) jest przydatne do zapewnienia użytkownikom urządzeń przenośnych opcji bez hasła. Aplikacja Authenticator zamienia dowolny telefon z systemem iOS lub Android w silne, bez hasła poświadczenia, umożliwiając użytkownikom zalogowanie się do dowolnej platformy lub przeglądarki. Użytkownicy logują się, uzyskując powiadomienie na swój telefon, dopasowując numer wyświetlany na ekranie do numeru w telefonie, a następnie używając swoich danych biometrycznych lub kodu PIN do potwierdzenia.

### <a name="passwordless-authentication-scenarios"></a>Scenariusze uwierzytelniania bez hasła

Metody uwierzytelniania bez hasła firmy Microsoft umożliwiają różne scenariusze. Należy wziąć pod uwagę potrzeby organizacyjne, wymagania wstępne i możliwości każdej metody uwierzytelniania, aby wybrać strategię uwierzytelniania bez hasła. Zaleca się, aby każda organizacja korzystająca z urządzeń z systemem Windows 10 używała funkcji Windows Hello dla firm. Następnie dodaj logowanie telefoniczne (za pomocą aplikacji Microsoft Authenticator) lub klucze zabezpieczeń dla dodatkowych scenariuszy.

| Scenariusz | Uwierzytelnianie telefoniczne | Klucze zabezpieczeń | Windows Hello dla firm |
| --- | --- | --- | --- |
| **Logowanie komputera:** <br> Z przypisanego urządzenia z systemem Windows 10 | **Nie** | **Tak** <br> Z biometrycznym, PIN | **Tak**<br>z rozpoznawaniem biometrycznym i kodem PIN |
| **Logowanie komputera:** <br> Z udostępnionego urządzenia z systemem Windows 10 | **Nie** | **Tak** <br> Z biometrycznym, PIN  | **Nie** |
| **Logowanie do aplikacji sieci Web:** <br>z komputera dedykowanego użytkownikowi | **Tak** | **Tak** <br> Pod warunkiem, że aplikacje logowania jednokrotnego jest włączona przez logowanie komputera | **Tak**<br> Pod warunkiem, że aplikacje logowania jednokrotnego jest włączona przez logowanie komputera |
| **Logowanie do aplikacji sieci Web:** <br> z urządzenia mobilnego lub innego niż windows | **Tak** | **Nie** | **Nie** |
| **Logowanie komputera:** <br> Komputer z systemem windows | **Nie** | **Nie** | **Nie** |

Aby uzyskać informacje na temat wybierania najlepszej metody dla organizacji, zobacz [Podejmowanie decyzji o metodzie bez hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrożenia bez hasła organizacje muszą spełniać następujące wymagania wstępne:

| Wymagania wstępne | Aplikacja Authenticator | Klucze bezpieczeństwa FIDO2 |
| --- | --- | --- |
| [Łączona rejestracja dla uwierzytelniania wieloskładnikowego platformy Azure i samoobsługowego resetowania hasła (SSPR)](howto-registration-mfa-sspr-combined.md) jest włączona | √ | √ |
| [Użytkownicy mogą wykonywać uwierzytelnianie wieloskładnikowe platformy Azure](howto-mfa-getstarted.md) | √ | √ |
| [Użytkownicy zarejestrowali się w usłudze Azure Uwierzytelnianie wieloskładnikowe i wiele SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Użytkownicy zarejestrowali swoje urządzenia przenośne w usłudze Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 w wersji 1809 lub nowszej przy użyciu obsługiwanej przeglądarki, takiej jak Microsoft Edge lub Mozilla Firefox <br> (wersja 67 lub nowsza). <br> *Firma Microsoft zaleca obsługę natywną w wersji 1903 lub nowszej.* |   | √ |
| Kompatybilne klucze bezpieczeństwa FIDO2. Upewnij się, że używasz [przetestowanego i zweryfikowanego](howto-authentication-passwordless-enable.md) urządzenia zabezpieczającego FIDO2 firmy Microsoft lub innego zgodnego urządzenia zabezpieczającego FIDO2. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Wymagania wstępne dla funkcji Windows Hello dla firm

Wymagania wstępne dla windows hello są w dużym stopniu zależne od tego, czy wdrażasz w konfiguracji lokalnej, hybrydowej lub tylko w chmurze. Aby uzyskać więcej informacji, zobacz [pełną listę wymagań wstępnych dla systemu Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Użytkownicy rejestrują swoją metodę bez hasła jako część przepływu rejestracji uwierzytelniania wieloskładnikowego platformy Azure. Uwierzytelnianie wieloskładnikowe z nazwą użytkownika i hasłem wraz z inną zarejestrowaną metodą może być używane jako rezerwowe w przypadku, gdy nie mogą używać telefonu lub klucza zabezpieczeń w niektórych scenariuszach.

### <a name="licensing"></a>Licencjonowanie 
Nie ma żadnych dodatkowych kosztów uwierzytelniania bez hasła, chociaż niektóre wymagania wstępne mogą wymagać subskrypcji premium. Szczegółowe informacje dotyczące funkcji i licencjonowania można znaleźć na [stronie licencjonowania usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Opracowanie planu

Należy wziąć pod uwagę potrzeby biznesowe i przypadki użycia dla każdej metody uwierzytelniania. Następnie wybierz metodę, która najlepiej odpowiada Twoim potrzebom.

### <a name="use-cases"></a>Przypadki zastosowań

W poniższej tabeli przedstawiono przypadki użycia, które mają być zaimplementowane podczas tego projektu.

| Obszar | Opis |
| --- | --- |
| **Dostęp** | Logowanie bez hasła jest dostępne na urządzeniu firmowym lub osobistym w sieci firmowej lub poza nią. |
| **Inspekcja** | Dane użycia są dostępne dla administratorów do inspekcji w czasie zbliżonym do rzeczywistego. <br> Dane użycia są pobierane do systemów firmowych co najmniej co 29 dni lub używane jest narzędzie SIEM. |
| **Nadzór** | Cykl życia przypisania użytkowników do odpowiedniej metody uwierzytelniania i skojarzonych grup jest definiowany i monitorowany. |
| **Zabezpieczenia** | Dostęp do odpowiedniej metody uwierzytelniania jest kontrolowany za pomocą przypisań użytkowników i grup. <br> Tylko autoryzowani użytkownicy mogą używać logowania bez hasła. |
| **Wydajność** | Terminy propagacji przydziałów dostępu są dokumentowane i monitorowane. <br> Czas logowania jest mierzony w celu ułatwienia użytkowania. |
| **Doświadczenie użytkownika** | Użytkownicy są świadomi zgodności urządzeń przenośnych. <br> Użytkownicy mogą skonfigurować logowanie bez hasła aplikacji Authenticator. |
| **Pomoc techniczna** | Użytkownicy są świadomi, jak znaleźć obsługę problemów z logowaniem bez hasła. |

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich interesariuszy

Gdy projekty technologiczne nie powiodą się, zazwyczaj z powodu niedopasowanych oczekiwań dotyczących wpływu, wyników i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że angażujesz odpowiednie zainteresowane strony](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) i że role interesariuszy w projekcie są dobrze zrozumiałe.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Proaktywnie komunikuj, jak zmieni się środowisko użytkowników, kiedy to się zmieni i jak uzyskać wsparcie, jeśli wystąpią problemy.

Komunikacja z użytkownikami końcowymi powinna zawierać następujące informacje:

- [Włączanie połączonego środowiska rejestracji zabezpieczeń](howto-authentication-passwordless-phone.md)
- [Pobieranie aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Rejestracja w aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Logowanie się za pomocą telefonu](../user-help/user-help-auth-app-sign-in.md)

Firma Microsoft udostępnia [szablony komunikacji](https://aka.ms/mfatemplates)uwierzytelniania wieloskładnikowego, [szablony komunikacji](https://www.microsoft.com/download/details.aspx?id=56768)samoobsługowe resetowanie haseł (SSPR) oraz [dokumentację użytkownika końcowego,](../user-help/security-info-setup-signin.md) aby ułatwić opracowywanie komunikatów. Do tej strony [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) można wysyłać użytkowników do rejestracji bezpośrednio, wybierając łącza **Informacje zabezpieczające** na tej stronie.

### <a name="plan-to-pilot"></a>Planowanie pilotażu

Podczas wdrażania uwierzytelniania bez hasła należy najpierw włączyć jedną lub więcej grup pilotażowych. W tym celu można [tworzyć grupy](../fundamentals/active-directory-groups-create-azure-portal.md) specjalnie. Dodaj użytkowników, którzy będą uczestniczyć w programie pilotażowym do grup. Następnie włącz nowe metody uwierzytelniania bez hasła dla wybranych grup.

Grupy mogą być synchronizowane z katalogu lokalnego lub z usługi Azure AD. Gdy będziesz zadowolony z wyników pilotażu, możesz włączyć uwierzytelnianie bez hasła dla wszystkich użytkowników.

Zobacz [najważniejsze wskazówki dotyczące programu pilotażowego](https://aka.ms/deploymentplans) na stronie plany wdrożenia.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planowanie uwierzytelniania bez hasła za pomocą aplikacji Microsoft Authenticator

Aplikacja Microsoft Authenticator jest bezpłatna do pobrania z Google Play lub Apple App Store. [Dowiedz się więcej o pobieraniu aplikacji Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Niech użytkownicy pobierzą aplikację Microsoft Authenticator. i postępuj zgodnie ze wskazówkami, aby włączyć logowanie się przez telefon. 

Zamienia dowolny telefon z systemem iOS lub Android w silne, bez hasła poświadczenia. Użytkownicy logują się do dowolnej platformy lub przeglądarki, uzyskując powiadomienie na swój telefon, dopasowując numer wyświetlany na ekranie do numeru w telefonie, a następnie używając danych biometrycznych lub kodu PIN do potwierdzenia. [Zobacz szczegółowe informacje o tym, jak działa aplikacja Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app).

![logowanie się za pomocą aplikacji Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Zagadnienia techniczne dotyczące aplikacji Microsoft Authenticator

**Integracja usług AD FS** — gdy użytkownik włącza poświadczenia bezoczyszkowe microsoft authenticator, uwierzytelnianie dla tego użytkownika domyślnie wysyła powiadomienie do zatwierdzenia. Użytkownicy w dzierżawie hybrydowej nie mogą być kierowani do usługi ADFS w celu zalogowania się, chyba że wybiorą opcję "Użyj hasła". Ten proces pomija również wszystkie lokalne zasady dostępu warunkowego i przepływy uwierzytelniania przekazywanego. Jeśli jednak określono *login_hint,* użytkownik jest przekazywał dalej do usługi ADFS i pomijał opcję używania poświadczeń bez hasła.

**Serwer uwierzytelniania wieloskładnikowego platformy Azure** — użytkownicy końcowi włączeni do uwierzytelniania wieloskładnikowego za pośrednictwem lokalnego serwera usługi Azure MFA w organizacji mogą tworzyć i używać pojedynczych poświadczeń logowania telefonu bez hasła. Jeśli użytkownik podejmie próbę uaktualnienia wielu instalacji (5 lub więcej) programu Microsoft Authenticator przy użyciu poświadczeń, ta zmiana może spowodować błąd.

**Rejestracja urządzenia** — aby użyć aplikacji Authenticator do uwierzytelniania bez hasła, urządzenie musi być zarejestrowane w dzierżawie usługi Azure AD i nie może być urządzeniem udostępnionym. Urządzenie może być zarejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe lub szkolne jest obsługiwane w przypadku logowania telefonu za pomocą aplikacji Authenticator.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planowanie uwierzytelniania bez hasła za pomocą kluczy zabezpieczeń FIDO2
Dostępne są trzy typy wdrożeń logowania bez hasła z kluczami zabezpieczeń:

-    Aplikacje sieci Web usługi Azure Active Directory w obsługiwanej przeglądarce
-    Urządzenia z systemem Windows 10 przyłączone do usługi Azure Active Directory
-    Urządzenia z systemem Windows 10 połączone z usługą Azure Active Directory (wersja zapoznawcza)
     -    Zapewnia dostęp zarówno do zasobów opartych na chmurze, jak i lokalnych. Aby uzyskać więcej informacji na temat dostępu do zasobów lokalnych, zobacz [samoso do zasobów lokalnych przy użyciu kluczy FIDOP2](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

Należy włączyć **zgodne klucze zabezpieczeń FIDO2**. Microsoft ogłosił [kluczowe partnerstwa z kluczowymi dostawcami FIDO2.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)

**W przypadku aplikacji sieci Web usługi Azure AD i urządzeń połączonych z systemem Azure AD Windows:**

-    Windows 10 w wersji 1809 lub nowszej przy użyciu obsługiwanej przeglądarki, takiej jak Microsoft Edge lub Mozilla Firefox (wersja 67 lub nowsza). 
-    System Windows 10 w wersji 1809 obsługuje logowanie FIDO2 i może wymagać wdrożenia oprogramowania producenta klucza FIDO2. Zalecamy użycie wersji 1903 lub nowszej. 

**W przypadku urządzeń połączonych domeną usługi Azure Active Directory hybrydowych:** 
-    Windows 10 Insider budować 18945 lub nowsze
-    W pełni poprawione serwery domeny z systemem Windows Server 2016 lub 2019.
-    Najnowsza wersja usługi Azure AD Connect

Aby uzyskać pełną listę wymagań, zobacz [Włączanie logowania bez hasła do urządzeń z systemem Windows 10 za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements).


### <a name="security-key-life-cycle"></a>Cykl życia klucza zabezpieczeń

Klucze zabezpieczeń umożliwiają dostęp do zasobów i należy zaplanować zarządzanie tymi urządzeniami fizycznymi.

1. **Dystrybucja kluczy:** Planowanie sposobu inicjowania obsługi administracyjnej kluczy w organizacji. Możesz mieć scentralizowany proces inicjowania obsługi administracyjnej lub zezwolić użytkownikom końcowym na zakup kluczy zgodnych z FIDO 2.0.
1. **Aktywacja klucza:** Użytkownicy końcowi muszą samodzielnie aktywować klucz zabezpieczeń. Użytkownicy końcowi rejestrują swoje klucze zabezpieczeń [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) i włączają drugi współczynnik (PIN lub biometryczny) przy pierwszym użyciu.
1. **Wyłączenie klucza:** Podczas gdy funkcja klucza zabezpieczeń znajduje się na etapie podglądu, administrator nie może usunąć klucza z konta użytkownika. Użytkownik musi go usunąć. W przypadku zgubionych lub skradzionych kluczy:
   1. Usuń użytkownika z dowolnej grupy włączonej do uwierzytelniania bez hasła.
   1. Sprawdź, czy klucz został usunięty jako metoda uwierzytelniania.
   1. Wyemiuj nowy klucz. **Wymiana kluczy:** Użytkownicy mogą włączyć dwa klucze zabezpieczeń w tym samym czasie. Podczas wymiany klucza zabezpieczeń upewnij się, że użytkownik usunął również wymieniany klucz.

### <a name="enable-windows-10-support"></a>Włącz obsługę systemu Windows 10

Włączenie logowania systemu Windows 10 przy użyciu kluczy zabezpieczeń FIDO2 wymaga włączenia funkcji dostawcy poświadczeń w systemie Windows 10. Wybierz jedną z następujących opcji:

- [Włączanie dostawcy poświadczeń za pomocą usługi Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Wdrożenie usługi Intune jest zalecaną opcją.
- [Włączanie dostawcy poświadczeń przy użyciu pakietu inicjowania obsługi administracyjnej](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Jeśli wdrożenie usługi Intune nie jest możliwe, administratorzy muszą wdrożyć pakiet na każdym komputerze, aby włączyć funkcję dostawcy poświadczeń. Instalacja pakietu może być przeprowadzona za pomocą jednej z następujących opcji:
      - Zasady grupy lub menedżer konfiguracji
      - Instalacja lokalna na komputerze z systemem Windows 10
- [Włącz dostawcę poświadczeń za pomocą zasad grupy](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Obsługiwane tylko dla hybrydowych urządzeń przyłączonych do usługi Azure AD.

#### <a name="enable-on-premises-integration"></a>Włączanie integracji lokalnej

Aby włączyć dostęp do zasobów lokalnych, wykonaj czynności, aby [włączyć logowanie się bez hasła do zasobów lokalnych (wersja zapoznawcza).](howto-authentication-passwordless-security-key-on-premises.md)

> [!IMPORTANT]
> Te kroki należy również wykonać dla wszystkich hybrydowych urządzeń przyłączonych do usługi Azure AD, aby korzystać z kluczy zabezpieczeń FIDO2 dla systemu Windows 10 logowania.

### <a name="register-security-keys"></a>Rejestrowanie kluczy zabezpieczeń

Użytkownicy muszą zarejestrować swój klucz zabezpieczeń na każdym z komputerów z systemem Windows 10 przyłączonych do usługi Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [Rejestracja użytkowników i zarządzanie kluczami bezpieczeństwa FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planowanie inspekcji, zabezpieczeń i testowania
Planowanie inspekcji, które spełnia struktury organizacyjne i zgodności jest istotną częścią wdrożenia.

### <a name="auditing-passwordless"></a>Inspekcja bez hasła

Usługa Azure AD ma raporty, które zawierają szczegółowe informacje techniczne i biznesowe. Niech właściciele aplikacji biznesowych i technicznych przejmą na własność te raporty i będą je wykorzystywać na podstawie wymagań organizacji.

Sekcja **Metody uwierzytelniania** w portalu usługi Azure Active Directory to miejsce, w którym administratorzy mogą włączać i zarządzać ustawieniami poświadczeń bez hasła.

Usługa Azure AD dodaje wpisy do dzienników inspekcji, gdy:

- Administrator wprowadza zmiany w sekcji Metody uwierzytelniania.
- Użytkownik wprowadza wszelkiego rodzaju zmiany swoich poświadczeń w usłudze Azure Active Directory.

Poniższa tabela zawiera kilka przykładów typowych scenariuszy raportowania:

|   | Zarządzanie ryzykiem | Zwiększ produktywność | Zarządzanie i zgodność |
| --- | --- | --- | --- |
| **Typy raportów** | Metody uwierzytelniania - użytkownicy zarejestrowani do połączonej rejestracji zabezpieczeń | Metody uwierzytelniania – użytkownicy zarejestrowani do powiadamiania aplikacji | Logowania: sprawdź, kto i jak uzyskuje dostęp do dzierżawy |
| **Potencjalne działania** | Użytkownicy docelowi, którzy nie są jeszcze zarejestrowani | Wdrażanie aplikacji lub kluczy zabezpieczeń Microsoft Authenticator | Odwoływanie dostępu lub wymuszanie dodatkowych zasad zabezpieczeń dla administratorów |

**Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni** i udostępnia dane za pośrednictwem portalu administratora platformy Azure lub interfejsu API do pobrania do systemów analizy. Jeśli potrzebujesz dłuższego przechowywania, eksportuj i zużywają dzienniki w narzędziu SIEM, takim jak [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk lub Sumo Logic. [Dowiedz się więcej o wyświetlaniu raportów o dostępie i użytkowaniu](../reports-monitoring/overview-reports.md).

Użytkownicy mogą rejestrować swoje poświadczenia [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)i zarządzać nimi, przechodząc do pliku . To łącze kieruje użytkowników do środowiska zarządzania poświadczeniami użytkownika końcowego, które zostało włączone za pośrednictwem połączonego środowiska rejestracji uwierzytelniania sskładowego/uwierzytelniania wieloskładnikowego. Usługa Azure AD rejestruje rejestrację urządzeń zabezpieczających FIDO2 i zmiany metod uwierzytelniania przez użytkowników.

### <a name="plan-security"></a>Zaplanuj bezpieczeństwo
W ramach tego planu wdrażania firma Microsoft zaleca włączenie uwierzytelniania bez hasła dla wszystkich uprzywilejowanych kont administratora.

Gdy użytkownicy włączyć lub wyłączyć konto na klucz zabezpieczeń lub zresetować drugi współczynnik klucza zabezpieczeń na swoich komputerach z systemem Windows 10, wpis jest dodawany do dziennika zabezpieczeń i są pod następującymi identyfikatorami zdarzeń: *4670* i *5382*.

### <a name="plan-testing"></a>Planowanie testowania

Na każdym etapie wdrażania podczas testowania scenariuszy i wdrażania upewnij się, że wyniki są zgodnie z oczekiwaniami.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testowanie aplikacji Microsoft Authenticator

Poniżej przedstawiono przykładowe przypadki testowe uwierzytelniania bez hasła za pomocą aplikacji Microsoft Authenticator:

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować aplikację Microsoft Authenticator | Użytkownik może zarejestrować aplikację z aka.ms/mysecurityinfo |
| Użytkownik może włączyć logowanie telefoniczne | Logowanie telefonu skonfigurowane dla konta służbowego |
| Użytkownik może uzyskać dostęp do aplikacji za pomocą logowania telefonu | Użytkownik przechodzi przez przepływ logowania telefonu i dociera do aplikacji. |
| Testowanie wycofywania rejestracji logowania telefonu przez wyłączenie logowania bez hasła firmy Microsoft Authenticator na ekranie Metody uwierzytelniania w portalu usługi Azure Active Directory | Wcześniej włączono użytkowników nie można używać logowania bez hasła z Microsoft Authenticator. |
| Usuwanie logowania telefonu z aplikacji Microsoft Authenticator | Konto służbowe nie jest już dostępne w usłudze Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testowanie kluczy zabezpieczeń

Poniżej przedstawiono przykładowe przypadki testowe uwierzytelniania bez hasła za pomocą kluczy zabezpieczeń.

**Bez hasła logowanie FIDO do urządzeń usługi Azure Active Directory przyłączonych do systemu Windows 10**

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować urządzenie FIDO2 (1809) | Użytkownik może zarejestrować urządzenie FIDO2 za pomocą opcji Ustawienia > > logowania > kluczem bezpieczeństwa |
| Użytkownik może zresetować urządzenie FIDO2 (1809) | Użytkownik może zresetować urządzenie FIDO2 za pomocą oprogramowania producenta |
| Użytkownik może zalogować się za pomocą urządzenia FIDO2 (1809) | Użytkownik może wybrać klucz zabezpieczeń z okna logowania i pomyślnie się zalogować. |
| Użytkownik może zarejestrować urządzenie FIDO2 (1903) | Użytkownik może zarejestrować urządzenie FIDO2 w ustawieniach > kontach > opcji logowania > kluczem zabezpieczeń |
| Użytkownik może zresetować urządzenie FIDO2 (1903) | Użytkownik może zresetować urządzenie FIDO2 w ustawieniach > kontach > opcji logowania > kluczem zabezpieczeń |
| Użytkownik może zalogować się za pomocą urządzenia FIDO2 (1903) | Użytkownik może wybrać klucz zabezpieczeń z okna logowania i pomyślnie się zalogować. |

**Logowanie FIDO bez hasła do aplikacji sieci Web usługi Azure AD**

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować urządzenie FIDO2 w aka.ms/mysecurityinfo za pomocą Microsoft Edge | Rejestracja powinna zakończyć się sukcesem |
| Użytkownik może zarejestrować urządzenie FIDO2 w aka.ms/mysecurityinfo za pomocą Firefoksa | Rejestracja powinna zakończyć się sukcesem |
| Użytkownik może zalogować się do usługi OneDrive online przy użyciu urządzenia FIDO2 za pomocą przeglądarki Microsoft Edge | Logowanie powinno zakończyć się pomyślnie |
| Użytkownik może zalogować się do usługi OneDrive online za pomocą urządzenia FIDO2 za pomocą Firefoksa | Logowanie powinno zakończyć się pomyślnie |
| Testowanie wycofywania rejestracji urządzeń FIDO2 przez wyłączenie kluczy zabezpieczeń FIDO2 w oknie metody uwierzytelniania w portalu usługi Azure Active Directory | Użytkownicy zostaną poproszeni o zalogowanie się przy użyciu klucza zabezpieczeń. Użytkownicy z powodzeniem się zalogują i zostanie wyświetlony błąd: "Zasady firmy wymagają użycia innej metody do logowania". Użytkownicy powinni mieć możliwość wybrania innej metody i pomyślnego zalogowania się. Zamknij okno i zaloguj się ponownie, aby sprawdzić, czy nie widzą tego samego komunikatu o błędzie. |

### <a name="plan-for-rollback"></a>Planowanie wycofywania

Chociaż uwierzytelnianie bez hasła jest lekką funkcją o minimalnym wpływie na użytkowników końcowych, może być konieczne wycofanie.

Wycofywanie wymaga od administratora zalogowania się do portalu usługi Azure Active Directory, wybrania żądanych metod silnego uwierzytelniania i zmiany opcji włącz na **Nie**. Ten proces wyłącza funkcję bez hasła dla wszystkich użytkowników.

Użytkownicy, którzy zarejestrowali już urządzenia zabezpieczające FIDO2, są monitowani o użycie urządzenia zabezpieczającego przy następnym logowaniu, a następnie widzą następujący błąd:

![wybierz inny sposób logowania](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Wdrażanie uwierzytelniania bez hasła i rozwiązywanie problemów z ich uwierzytelnianiamięstym

Wykonaj kroki dostosowane do wybranej metody poniżej.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

| Rola usługi Azure AD | Opis |
| --- | --- |
| Administrator globalny|Najmniej uprzywilejowana rola w stanie zaimplementować połączone doświadczenie rejestracji. |
| Administrator uwierzytelniania | Najmniej uprzywilejowana rola w stanie zaimplementować i zarządzać metodami uwierzytelniania. |
| Użytkownik | Najmniej uprzywilejowana rola konfigurowania aplikacji Authenticator na urządzeniu lub rejestrowania urządzenia klucza zabezpieczeń do logowania się w sieci Web lub windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Wdrażanie logowania telefonu za pomocą aplikacji Microsoft Authenticator

Postępuj zgodnie z instrukcjami w artykule [Włącz logowanie bez hasła za pomocą aplikacji Microsoft Authenticator,](howto-authentication-passwordless-phone.md) aby włączyć aplikację Microsoft Authenticator jako metodę uwierzytelniania bez hasła w organizacji.

### <a name="deploy-fido2-security-key-sign-in"></a>Wdrażanie logowania klucza zabezpieczeń FIDO2

Wykonaj kroki opisane w artykule [Włącz logowanie się bez hasła do usługi Azure AD,](howto-authentication-passwordless-security-key.md) aby włączyć klucze zabezpieczeń FIDO2 jako metody uwierzytelniania bez hasła.

### <a name="troubleshoot-phone-sign-in"></a>Rozwiązywanie problemów z logowaniem telefonu

| Scenariusz | Rozwiązanie |
| --- | --- |
| Użytkownik nie może przeprowadzić rejestracji połączonej. | Upewnij się, że [rejestracja połączona](concept-registration-mfa-sspr-combined.md) jest włączona. |
| Użytkownik nie może włączyć aplikacji uwierzytelniającego logowania telefonu. | Upewnij się, że użytkownik jest w zakresie wdrożenia. |
| Użytkownik NIE jest w zakresie uwierzytelniania bez hasła, ale jest przedstawiony z opcją logowania bez hasła, których nie można ukończyć. | W tym scenariuszu występuje, gdy użytkownik włączył logowanie telefonu w aplikacji przed tworzeniem zasad. <br> *Aby włączyć logowanie:* Dodaj użytkownika do zakresu użytkowników włączonych do logowania bez hasła. <br> *Aby zablokować logowanie:* niech użytkownik usunie swoje poświadczenia z tej aplikacji. |

### <a name="troubleshoot-security-key-sign-in"></a>Rozwiązywanie problemów z logowaniem do klucza zabezpieczeń

| Scenariusz | Rozwiązanie |
| --- | --- |
| Użytkownik nie może wykonać rejestracji łączonej. | Upewnij się, że [rejestracja połączona](concept-registration-mfa-sspr-combined.md) jest włączona. |
| Użytkownik nie może dodać klucza zabezpieczeń w [ustawieniach zabezpieczeń](https://aka.ms/mysecurityinfo). | Upewnij się, że [klucze zabezpieczeń](howto-authentication-passwordless-security-key.md) są włączone. |
| Użytkownik nie może dodać klucza zabezpieczeń w opcjach logowania do systemu Windows 10. | [Upewnij się, że klucze zabezpieczeń dla systemu Windows się logują](howto-authentication-passwordless-enable.md) |
| **Komunikat o błędzie**: Wykryliśmy, że ta przeglądarka lub system operacyjny nie obsługuje kluczy bezpieczeństwa FIDO2. | Urządzenia zabezpieczające FIDO2 bez hasła mogą być rejestrowane tylko w obsługiwanych przeglądarkach (Microsoft Edge, Firefox w wersji 67) w systemie Windows 10 w wersji 1809 lub nowszej. |
| **Komunikat o błędzie:** Zasady firmy wymagają użycia innej metody do logowania. | Nie pewny klucze zabezpieczeń są włączone w dzierżawie. |
| Użytkownik nie może zarządzać kluczem zabezpieczeń w systemie Windows 10 w wersji 1809 | Wersja 1809 wymaga użycia oprogramowania do zarządzania kluczami zabezpieczeń dostarczonego przez dostawcę klucza FIDO2. Skontaktuj się z dostawcą, aby uzyskać pomoc. |
| Myślę, że mój klucz bezpieczeństwa FIDO2 może być uszkodzony, jak mogę go przetestować. | Przejdź [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)do , wprowadź poświadczenia dla konta testowego, podłącz **+** podejrzany klucz zabezpieczeń, wybierz przycisk w prawym górnym rogu ekranu, kliknij przycisk utwórz i przejdź przez proces tworzenia. Jeśli ten scenariusz nie powiedzie się, urządzenie może być uszkodzony. |

## <a name="next-steps"></a>Następne kroki

- [Włączanie bez hasłem kluczy zabezpieczeń do logowania się do usługi Azure AD](howto-authentication-passwordless-security-key.md)
- [Włączanie logowania bez hasła za pomocą aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Dowiedz się więcej o użyciu metod uwierzytelniania & szczegółowych informacji](howto-authentication-methods-usage-insights.md)

