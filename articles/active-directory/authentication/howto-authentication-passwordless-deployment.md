---
title: Planowanie wdrożenia uwierzytelniania bez hasła przy użyciu usługi Azure AD
description: Informacje o planowaniu i wdrażaniu Azure Active Directory implementacji uwierzytelniania bezhaseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc5916cf4277935f6d7d7f453fd4dd76d7909ef2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651131"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planowanie wdrożenia uwierzytelniania bezhaseł w Azure Active Directory

> [!NOTE]
> Aby utworzyć wersję offline tego planu wdrożenia, użyj funkcji drukowania do formatu PDF w przeglądarce.

Większość ataków cybernetycznymi rozpoczyna się od złamanej nazwy użytkownika i hasła. Organizacje próbują wyeliminować zagrożenie, wymagając od użytkowników zastosowania jednego z następujących metod:

- Długie hasła
- Złożone hasła
- Częste zmiany hasła
- Multi-Factor Authentication (MFA)

Badania firmy Microsoft [pokazują](https://aka.ms/passwordguidance) , że te wysiłki annoy użytkowników i zwiększą koszty pomocy technicznej. Aby uzyskać więcej informacji, zobacz artykuł [PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Zalety uwierzytelniania bezhasła

- **Zwiększone zabezpieczenia**. Zmniejszenie ryzyka związanego z phishingiem i atakami polegającymi na wyłudzaniu hasła przez usunięcie haseł jako obszaru ataku.
-  **Lepsze środowisko użytkownika**. Zapewnianie użytkownikom wygodnego sposobu uzyskiwania dostępu do danych z dowolnego miejsca. Zapewnianie łatwego dostępu do aplikacji i usług, takich jak Outlook, OneDrive lub Office, podczas pracy w sieci mobilnej.
-  **Niezawodne wgląd w szczegółowe** dane. Uzyskaj wgląd w działanie bez hasła użytkowników z niezawodnym rejestrowaniem i inspekcją.

Hasło bez hasła jest zamieniane na coś, co Ci się podoba. Na przykład funkcja Windows Hello dla firm może korzystać z gestu biometrycznego, takiego jak znak lub odcisk palca, lub numeru PIN określonego dla urządzenia, który nie jest przesyłany przez sieć.

## <a name="passwordless-authentication-methods"></a>Metody uwierzytelniania bezhasło
Firma Microsoft oferuje trzy opcje uwierzytelniania bezhasło, które obejmują wiele scenariuszy. Metody te mogą być używane wspólnie:

- Funkcja [Windows Hello dla firm](./concept-authentication-passwordless.md) jest Najlepsza dla użytkowników na ich dedykowanych komputerach z systemem Windows.
- Klucz zabezpieczeń Logowanie przy użyciu [kluczy zabezpieczeń FIDO2](./concept-authentication-passwordless.md) jest szczególnie przydatny w przypadku użytkowników logujących się na maszynach udostępnionych, takich jak kioski, w sytuacjach, gdy korzystanie z telefonów jest ograniczone, a dla tożsamości o wysokim poziomie uprawnień.
- Logowanie za pomocą telefonu przy użyciu [aplikacji Microsoft Authenticator](./concept-authentication-passwordless.md) jest przydatne w przypadku udostępniania użytkownikom urządzeń przenośnych opcji bez hasła. Aplikacja Authenticator włącza wszystkie urządzenia z systemem iOS lub Android do silnego poświadczenia bez hasła, umożliwiając użytkownikom zalogowanie się do dowolnej platformy lub przeglądarki. Użytkownicy logują się, uzyskując powiadomienie na telefonie, dopasowując liczbę wyświetlaną na ekranie na telefonie, a następnie za pomocą danych biometrycznych lub numerów PIN do potwierdzenia.

### <a name="passwordless-authentication-scenarios"></a>Scenariusze uwierzytelniania bezhasło

Metody uwierzytelniania bezhaseł firmy Microsoft umożliwiają korzystanie z różnych scenariuszy. Aby wybrać strategię uwierzytelniania bezhasło, należy wziąć pod uwagę potrzeby organizacyjne, wymagania wstępne i możliwości poszczególnych metod uwierzytelniania. Zalecamy, aby każda organizacja korzystająca z urządzeń z systemem Windows 10 korzystała z usługi Windows Hello dla firm. Następnie Dodaj do dodatkowych scenariuszy opcję logowanie za pomocą telefonu (z aplikacją Microsoft Authenticator) lub kluczami zabezpieczeń.

| Scenariusz | Uwierzytelnianie na telefonie | Klucze zabezpieczeń | Windows Hello for Business |
| --- | --- | --- | --- |
| **Logowanie do komputera**: <br> Z przypisanego urządzenia z systemem Windows 10 | **Nie** | **Tak** <br> Przy użyciu biometrycznych, PIN | **Tak**<br>z rozpoznawaniem biometrycznym i lub numerem PIN |
| **Logowanie do komputera**: <br> Z udostępnionego urządzenia z systemem Windows 10 | **Nie** | **Tak** <br> Przy użyciu biometrycznych, PIN  | **Nie** |
| **Logowanie do aplikacji sieci Web**: <br>z komputera dedykowanego przez użytkownika | **Tak** | **Tak** <br> Udostępniane Logowanie jednokrotne do aplikacji jest włączane po zalogowaniu się komputera | **Tak**<br> Udostępniane Logowanie jednokrotne do aplikacji jest włączane po zalogowaniu się komputera |
| **Logowanie do aplikacji sieci Web**: <br> z urządzenia przenośnego lub z systemem innym niż Windows | **Tak** | **Nie** | **Nie** |
| **Logowanie do komputera**: <br> Komputer z systemem innym niż Windows | **Nie** | **Nie** | **Nie** |

Aby uzyskać informacje na temat wybierania najlepszej metody dla organizacji, zobacz [Decydowanie o metodzie bezhaseł](./concept-authentication-passwordless.md#choose-a-passwordless-method).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrażania bezhaseł organizacje muszą spełniać następujące wymagania wstępne:

| Wymaganie wstępne | Aplikacja Authenticator | FIDO2 klucze zabezpieczeń |
| --- | --- | --- |
| [Połączona Rejestracja w usłudze Azure AD Multi-Factor Authentication i funkcja samoobsługowego resetowania hasła (SSPR)](howto-registration-mfa-sspr-combined.md) jest włączona | √ | √ |
| [Użytkownicy mogą wykonywać Multi-Factor Authentication usługi Azure AD](howto-mfa-getstarted.md) | √ | √ |
| [Użytkownicy zarejestrowani w usłudze Azure AD Multi-Factor Authentication i SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Użytkownicy zarejestrowali swoje urządzenia przenośne w usłudze Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 w wersji 1809 lub nowszej przy użyciu obsługiwanej przeglądarki, takiej jak Microsoft Edge lub Mozilla Firefox <br> (wersja 67 lub nowsza). <br> *Firma Microsoft zaleca korzystanie z wersji 1903 lub nowszej na potrzeby obsługi natywnej*. |   | √ |
| Zgodne FIDO2 klucze zabezpieczeń. Upewnij się, że korzystasz z urządzenia zabezpieczeń [przetestowanego przez firmę Microsoft i zweryfikowane](./concept-authentication-passwordless.md) urządzenie z systemem FIDO2. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Wymagania wstępne dotyczące usługi Windows Hello dla firm

Wymagania wstępne dotyczące usługi Windows Hello są wysoce zależne od tego, czy są wdrażane w konfiguracji lokalnej, hybrydowej czy w chmurze. Aby uzyskać więcej informacji, zobacz [pełną listę wymagań wstępnych dotyczących usługi Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-ad-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe w usłudze Azure AD

Użytkownicy rejestrują metodę bezhaseł w ramach przepływu rejestracji Multi-Factor Authentication usługi Azure AD. Uwierzytelnianie wieloskładnikowe przy użyciu nazwy użytkownika i hasła wraz z inną zarejestrowanej metody może być używane jako rezerwa w przypadku, gdy nie mogą korzystać z swojego telefonu ani klucza zabezpieczeń w niektórych scenariuszach.

### <a name="licensing"></a>Licencjonowanie 
Nie ma dodatkowych kosztów uwierzytelniania bez hasła, chociaż niektóre wymagania wstępne mogą wymagać subskrypcji Premium. Aby uzyskać szczegółowe informacje dotyczące funkcji i licencjonowania na [stronie licencjonowania Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Opracowywanie planu

Należy wziąć pod uwagę potrzeby biznesowe i przypadki użycia dla każdej metody uwierzytelniania. Następnie wybierz metodę, która najlepiej odpowiada Twoim potrzebom.

### <a name="use-cases"></a>Przypadki zastosowań

W poniższej tabeli przedstawiono przypadki użycia do wdrożenia w ramach tego projektu.

| Obszar | Opis |
| --- | --- |
| **Dostęp** | Logowanie bez hasła jest dostępne z urządzenia firmowego lub osobistego w sieci firmowej lub poza nią. |
| **Kontroli** | Dane użycia są dostępne dla administratorów w celu przeprowadzania inspekcji niemal w czasie rzeczywistym. <br> Dane użycia są pobierane do systemów firmowych co najmniej co 29 dni lub używane jest narzędzie SIEM. |
| **Nadzór** | Cykl życia przypisań użytkowników do odpowiedniej metody uwierzytelniania i skojarzonych grup jest zdefiniowany i monitorowany. |
| **Bezpieczeństwo** | Dostęp do odpowiedniej metody uwierzytelniania jest kontrolowany za pośrednictwem przypisań użytkowników i grup. <br> Tylko autoryzowani użytkownicy mogą korzystać z logowania bezhasłem. |
| **Wydajność** | Osie czasu propagacji przydziału dostępu są udokumentowane i monitorowane. <br> Czasy logowania są mierzone w celu ułatwienia użycia. |
| **Środowisko użytkownika** | Użytkownicy są świadomi zgodności urządzeń przenośnych. <br> Użytkownicy mogą konfigurować logowanie za pomocą hasła aplikacji uwierzytelniania. |
| **Pomoc techniczna** | Użytkownicy wiedzą, jak znaleźć pomoc techniczną dla problemów związanych z logowaniem. |

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

W przypadku niepowodzenia projektów technologicznych zazwyczaj wynika to z niezgodności zaoczekiwań, wyników i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że interesują](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) Cię odpowiednie osoby zainteresowane i że role udziałowców w projekcie są dobrze zrozumiałe.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Aktywnie Komunikuj się, w jaki sposób środowisko użytkownika zostanie zmienione, gdy zostanie zmienione, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

Komunikacja z użytkownikami końcowymi powinna obejmować następujące informacje:

- [Włączanie korzystania ze połączonej rejestracji zabezpieczeń](howto-authentication-passwordless-phone.md)
- [Pobieranie aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Rejestrowanie w aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Logowanie za pomocą telefonu](../user-help/user-help-auth-app-sign-in.md)

Firma Microsoft udostępnia [Szablony komunikacji](https://aka.ms/mfatemplates)usługi uwierzytelniania wieloskładnikowego, [Szablony komunikacji](https://www.microsoft.com/download/details.aspx?id=56768)Self-Serviceego resetowania haseł (SSPR), a także [dokumentację użytkowników końcowych](../user-help/security-info-setup-signin.md) , która ułatwia przygotowanie komunikacji. Możesz wysyłać użytkowników do [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) rejestracji bezpośrednio, wybierając linki do **informacji zabezpieczających** na tej stronie.

### <a name="plan-to-pilot"></a>Planowanie pilotażu

W przypadku wdrażania uwierzytelniania bezhaseł należy najpierw włączyć co najmniej jedną grupę pilotażową. Do tego celu można [utworzyć grupy](../fundamentals/active-directory-groups-create-azure-portal.md) . Dodaj użytkowników, którzy będą uczestniczyć w pilotażu do grup. Następnie Włącz nowe metody uwierzytelniania bezhasło dla wybranych grup.

Grupy można synchronizować z katalogu lokalnego lub z usługi Azure AD. Gdy będziesz zadowolony z wyników pilotażu, możesz przełączyć się na uwierzytelnianie bez hasła dla wszystkich użytkowników.

Zapoznaj [się z najlepszymi rozwiązaniami dla pilotażu](../fundamentals/active-directory-deployment-plans.md) na stronie plany wdrożenia.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planowanie uwierzytelniania bezhasła przy użyciu aplikacji Microsoft Authenticator

Aplikacja Microsoft Authenticator jest bezpłatnym pobieraniem z Google Play lub sklepu Apple App Store. [Dowiedz się więcej o pobieraniu aplikacji Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Użytkownicy pobierają aplikację Microsoft Authenticator. i postępuj zgodnie z instrukcjami, aby włączyć logowanie za pomocą telefonu. 

Powoduje to włączenie dowolnego telefonu z systemem iOS lub Android do silnego poświadczenia bez hasła. Użytkownicy logują się do dowolnej platformy lub przeglądarki, uzyskując powiadomienie na telefonie, dopasowując liczbę wyświetlaną na ekranie na telefonie, a następnie używając biometrii lub numeru PIN do potwierdzenia. [Zobacz szczegóły dotyczące działania aplikacji Microsoft Authenticator](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![Zaloguj się przy użyciu aplikacji Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Zagadnienia techniczne dotyczące aplikacji Microsoft Authenticator

**Integracja AD FS** — gdy użytkownik włączy Microsoft Authenticator poświadczenia bezhasła, uwierzytelnianie dla tego użytkownika jest domyślnie wysyłane powiadomienie o zatwierdzeniu. Użytkownicy w dzierżawie hybrydowej nie mogą być kierowani do usług AD FS w celu zalogowania się, chyba że wybierze opcję "Użyj hasła zamiast". Ten proces pomija również wszystkie lokalne zasady dostępu warunkowego i przepływy uwierzytelniania przekazywane. Jeśli jednak określono *login_hint* , użytkownik jest przesyłany dalej do usług AD FS i pomija opcję, aby użyć poświadczeń bezhasłem.

**Azure AD Multi-Factor Authentication Server** — użytkownicy końcowi z obsługą uwierzytelniania wieloskładnikowego za pomocą lokalnego serwera usługi Azure MFA mogą tworzyć i używać jednego poświadczenia logowania jednokrotnego bez hasła. Jeśli użytkownik próbuje uaktualnić wiele instalacji (5 lub więcej) Microsoft Authenticator przy użyciu poświadczenia, ta zmiana może spowodować wystąpienie błędu.

**Rejestracja urządzenia** — aby użyć aplikacji Authenticator do uwierzytelniania bezserwerowego, urządzenie musi być zarejestrowane w dzierżawie usługi Azure AD i nie może być urządzeniem udostępnionym. Urządzenie można zarejestrować tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe jest obsługiwane w przypadku logowania za pomocą telefonu przy użyciu aplikacji uwierzytelniania.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Zaplanuj uwierzytelnianie bezhasła przy użyciu kluczy zabezpieczeń FIDO2
Istnieją trzy typy wdrożeń logowania bez hasła dostępnych w kluczach zabezpieczeń:

-    Azure Active Directory aplikacje sieci Web w obsługiwanej przeglądarce
-    Azure Active Directory dołączonych urządzeń z systemem Windows 10
-    Hybrydowe Azure Active Directory dołączone do urządzeń z systemem Windows 10
     -    Zapewnia dostęp do zasobów lokalnych i w chmurze. Aby uzyskać więcej informacji o dostępie do zasobów lokalnych, zobacz [Logowanie jednokrotne do zasobów lokalnych przy użyciu kluczy FIDO2](./howto-authentication-passwordless-security-key-on-premises.md)

Należy włączyć **zgodne klucze zabezpieczeń FIDO2**. Firma Microsoft ogłosiła [kluczowe partnerstwo z dostawcami kluczy FIDO2](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**W przypadku usługi Azure AD Web Apps i urządzeń przyłączonych do usługi Azure AD systemu Windows**:

-    System Windows 10 w wersji 1809 lub nowszej przy użyciu obsługiwanej przeglądarki, takiej jak Microsoft Edge lub Mozilla Firefox (wersja 67 lub nowsza). 
-    System Windows 10 w wersji 1809 obsługuje logowanie za pomocą usługi FIDO2 i może wymagać wdrożenia oprogramowania od producenta klucza FIDO2. Zalecamy użycie wersji 1903 lub nowszej. 

**Dla urządzeń przyłączonych do domeny hybrydowej Azure Active Directory**: 
-    Windows 10 w wersji 2004 lub nowszej
-    W pełni zainstalowane serwery domeny z systemem Windows Server 2016 lub 2019.
-    Najnowsza wersja Azure AD Connect

Aby uzyskać pełną listę wymagań, zobacz [Włączanie logowania za pomocą klucza zabezpieczeń bez hasła na urządzeniach z systemem Windows 10 z Azure Active Directory](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Cykl życia klucza zabezpieczeń

Klucze zabezpieczeń umożliwiają dostęp do zasobów i należy zaplanować zarządzanie tymi urządzeniami fizycznymi.

1. **Dystrybucja kluczy**: Zaplanuj sposób aprowizacji kluczy w organizacji. Może istnieć scentralizowany proces aprowizacji lub umożliwienie użytkownikom końcowym zakupu kluczy zgodnych z systemem FIDO 2,0.
1. **Aktywacja klucza**: użytkownicy końcowi muszą samoczynnie aktywować klucz zabezpieczeń. Użytkownicy końcowi rejestrują swoje klucze zabezpieczeń w [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) programie i włączają drugi współczynnik (PIN lub biometryczne) przy pierwszym użyciu.
1. **Wyłączanie klucza**: podczas gdy funkcje klucza zabezpieczeń są dostępne na etapie wersji zapoznawczej, nie ma możliwości, aby administrator usunął klucz z konta użytkownika. Użytkownik musi je usunąć. Jeśli klucz zostanie zgubiony lub skradziony:
   1. Usuń użytkownika z dowolnej grupy z włączoną obsługą uwierzytelniania bez hasła.
   1. Sprawdź, czy klucz został usunięty jako metoda uwierzytelniania.
   1. Wygeneruj nowy klucz. **Wymiana klucza**: użytkownicy mogą jednocześnie włączyć dwa klucze zabezpieczeń. Podczas wymiany klucza zabezpieczeń upewnij się, że użytkownik usunął również klucz, który został zastąpiony.

### <a name="enable-windows-10-support"></a>Włącz obsługę systemu Windows 10

Włączenie logowania systemu Windows 10 przy użyciu kluczy zabezpieczeń FIDO2 wymaga włączenia funkcji dostawcy poświadczeń w systemie Windows 10. Wybierz jedną z następujących opcji:

- [Włączanie dostawcy poświadczeń przy użyciu usługi Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Zalecanym rozwiązaniem jest wdrożenie usługi Intune.
- [Włączanie dostawcy poświadczeń z pakietem aprowizacji](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Jeśli wdrożenie usługi Intune nie jest możliwe, Administratorzy muszą wdrożyć pakiet na każdej maszynie, aby włączyć funkcję dostawcy poświadczeń. Instalację pakietu można przeprowadzić za pomocą jednej z następujących opcji:
      - zasady grupy lub Configuration Manager
      - Instalacja lokalna na komputerze z systemem Windows 10
- [Włącz dostawcę poświadczeń przy użyciu zasady grupy](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Obsługiwane tylko w przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD.

#### <a name="enable-on-premises-integration"></a>Włącz integrację lokalną

Aby włączyć dostęp do zasobów lokalnych, wykonaj kroki w celu [włączenia klucza zabezpieczeń bezhasło do zasobów lokalnych](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Te kroki należy również wykonać dla wszystkich urządzeń z dołączoną hybrydą usługą Azure AD, aby użyć kluczy zabezpieczeń FIDO2 dla logowania do systemu Windows 10.

### <a name="register-security-keys"></a>Rejestrowanie kluczy zabezpieczeń

Użytkownicy muszą zarejestrować swój klucz zabezpieczeń na każdej Azure Active Directory przyłączonych do komputerów z systemem Windows 10.

Aby uzyskać więcej informacji, zobacz [Rejestrowanie użytkowników i zarządzanie kluczami zabezpieczeń FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planowanie inspekcji, zabezpieczeń i testowania
Planowanie inspekcji, która spełnia wymagania organizacji i struktur zgodności, jest istotną częścią wdrożenia.

### <a name="auditing-passwordless"></a>Inspekcja bezhasła

Usługa Azure AD zawiera raporty, które udostępniają szczegółowe informacje techniczne i biznesowe. Czy właściciele aplikacji firmowych i technicznych zakładają własność i wykorzystują te raporty zgodnie z wymaganiami organizacji.

Sekcja metody **uwierzytelniania** w portalu Azure Active Directory jest miejscem, w którym Administratorzy mogą włączać i zarządzać ustawieniami poświadczeń bezterminowych.

Usługa Azure AD dodaje wpisy do dzienników inspekcji, gdy:

- Administrator wprowadza zmiany w sekcji metody uwierzytelniania.
- Użytkownik dokonuje dowolnego rodzaju zmiany poświadczeń w Azure Active Directory.

W poniższej tabeli przedstawiono kilka przykładów typowych scenariuszy raportowania:

|   | Zarządzanie ryzykiem | Zwiększ produktywność | Zarządzanie i zgodność |
| --- | --- | --- | --- |
| **Typy raportów** | Metody uwierzytelniania — Użytkownicy zarejestrowani do rejestracji zabezpieczeń połączonych | Metody uwierzytelniania — Użytkownicy zarejestrowani do powiadomień aplikacji | Logowania: Sprawdzanie, kto uzyskuje dostęp do dzierżawy i jak |
| **Potencjalni akcje** | Użytkownicy docelowi nie są jeszcze zarejestrowani | Wdrażanie stacji Microsoft Authenticator aplikacji lub kluczy zabezpieczeń | Odwoływanie dostępu lub wymuszanie dodatkowych zasad zabezpieczeń dla administratorów |

**Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni** i udostępnia dane za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API do pobierania w swoich systemach analitycznych. Jeśli potrzebujesz już przechowywania, Eksportuj i Zużywaj dzienniki w narzędziu SIEM, takim jak [Azure](../../sentinel/connect-azure-active-directory.md), Splunk lub Sumo Logic. [Dowiedz się więcej o wyświetlaniu raportów dotyczących dostępu i użycia](../reports-monitoring/overview-reports.md).

Użytkownicy mogą rejestrować swoje poświadczenia i zarządzać nimi, przechodząc do programu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Ten link kieruje użytkowników do środowiska zarządzania poświadczeniami użytkowników końcowych, które zostały włączone za pośrednictwem połączenia usługi SSPR/uwierzytelniania wieloskładnikowego. Rejestracja w usłudze Azure AD rejestruje urządzenia zabezpieczeń FIDO2 oraz zmiany metod uwierzytelniania użytkowników.

### <a name="plan-security"></a>Planowanie zabezpieczeń
W ramach tego planu wdrożenia firma Microsoft zaleca włączenie uwierzytelniania bezhaseł dla wszystkich uprzywilejowanych kont administratorów.

Gdy użytkownicy włączają lub wyłączają konto w kluczu zabezpieczeń lub zresetują drugi współczynnik dla klucza zabezpieczeń na komputerach z systemem Windows 10, do dziennika zabezpieczeń zostaje dodany wpis o następujących identyfikatorach zdarzeń: *4670* i *5382*.

### <a name="plan-testing"></a>Testowanie planu

Na każdym etapie wdrożenia podczas testowania scenariuszy i wdrażania upewnij się, że wyniki są zgodnie z oczekiwaniami.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testowanie aplikacji Microsoft Authenticator

Poniżej przedstawiono przykładowe przypadki testowe dotyczące uwierzytelniania bezhasła przy użyciu aplikacji Microsoft Authenticator:

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować aplikację Microsoft Authenticator | Użytkownik może zarejestrować aplikację z aka.ms/mysecurityinfo |
| Użytkownik może włączyć logowanie przy użyciu telefonu | Logowanie do konta służbowego zostało skonfigurowane |
| Użytkownik może uzyskać dostęp do aplikacji przy użyciu logowania za pomocą telefonu | Użytkownik przechodzi przez przepływ logowania przez telefon i dociera do aplikacji. |
| Przetestuj rejestrację logowania za pomocą telefonu, wyłączając Microsoft Authenticator logowanie bezhasło na ekranie metody uwierzytelniania w portalu Azure Active Directory | Wcześniej włączeni użytkownicy nie mogą korzystać z logowania bezMicrosoft Authenticatorowego przy użyciu hasła. |
| Usuwanie logowania za pomocą telefonu z aplikacji Microsoft Authenticator | Konto służbowe nie jest już dostępne na Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testowanie kluczy zabezpieczeń

Poniżej przedstawiono przykładowe przypadki testowe dotyczące uwierzytelniania bez hasła z kluczami zabezpieczeń.

**Logowanie za FIDO bezhasło do urządzeń z systemem Windows 10 dołączonych do Azure Active Directory**

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować urządzenie FIDO2 (1809) | Użytkownik może zarejestrować urządzenie FIDO2 przy użyciu usługi at Settings > konta > opcje logowania > Klucz zabezpieczeń |
| Użytkownik może zresetować urządzenie FIDO2 (1809) | Użytkownik może zresetować urządzenie FIDO2 za pomocą oprogramowania producenta |
| Użytkownik może zalogować się przy użyciu urządzenia FIDO2 (1809) | Użytkownik może wybrać klucz zabezpieczeń z okna logowania i pomyślnie się zalogować. |
| Użytkownik może zarejestrować urządzenie FIDO2 (1903) | Użytkownik może zarejestrować urządzenie FIDO2 w ustawieniach > kont > opcje logowania > Klucz zabezpieczeń |
| Użytkownik może zresetować urządzenie FIDO2 (1903) | Użytkownik może zresetować urządzenie FIDO2 w ustawieniach > kont > opcje logowania > Klucz zabezpieczeń |
| Użytkownik może zalogować się przy użyciu urządzenia FIDO2 (1903) | Użytkownik może wybrać klucz zabezpieczeń z okna logowania i pomyślnie się zalogować. |

**Logowanie w usłudze Azure AD Web Apps bezhasłem FIDO**

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować urządzenie FIDO2 w usłudze aka.ms/mysecurityinfo przy użyciu przeglądarki Microsoft Edge | Rejestracja powinna zakończyć się powodzeniem |
| Użytkownik może zarejestrować urządzenie FIDO2 w usłudze aka.ms/mysecurityinfo przy użyciu przeglądarki Firefox | Rejestracja powinna zakończyć się powodzeniem |
| Użytkownik może zalogować się do usługi OneDrive online przy użyciu urządzenia FIDO2 przy użyciu przeglądarki Microsoft Edge | Logowanie powinno zakończyć się pomyślnie |
| Użytkownik może zalogować się do usługi OneDrive online przy użyciu urządzenia FIDO2 przy użyciu przeglądarki Firefox | Logowanie powinno zakończyć się pomyślnie |
| Testowanie wycofywania rejestracji urządzeń FIDO2 przez wyłączenie kluczy zabezpieczeń FIDO2 w oknie Metoda uwierzytelniania w portalu Azure Active Directory | Użytkownicy będą monitowani o zalogowanie się przy użyciu klucza zabezpieczeń. Użytkownicy będą pomyślnie logować się i zostanie wyświetlony komunikat o błędzie: "zasady firmy wymagają użycia innej metody do zalogowania się". Użytkownicy powinni wtedy móc wybrać inną metodę i pomyślnie zalogować się. Zamknij okno i zaloguj się ponownie, aby sprawdzić, czy nie widzi tego samego komunikatu o błędzie. |

### <a name="plan-for-rollback"></a>Planowanie wycofywania

Chociaż uwierzytelnianie bezhasła jest lekkim elementem z minimalnym wpływem na użytkowników końcowych, może być konieczne wycofanie.

Wycofanie wymaga, aby administrator zalogować się do portalu Azure Active Directory, wybrać odpowiednie metody silnego uwierzytelniania i zmienić opcję Włącz na wartość **nie**. Ten proces powoduje wyłączenie funkcji bezhasło dla wszystkich użytkowników.

Użytkownicy, którzy już zarejestrowali urządzenia zabezpieczeń FIDO2, otrzymają monit o użycie urządzenia zabezpieczeń podczas kolejnego logowania, a następnie zobacz następujący błąd:

![Wybierz inny sposób logowania](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Wdrażanie i rozwiązywanie problemów z uwierzytelnianiem bezhaseł

Wykonaj kroki wyrównane do wybranej metody poniżej.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

| Rola usługi Azure AD | Opis |
| --- | --- |
| Administrator globalny|Najmniejsza rola uprzywilejowana może zaimplementować środowisko rejestracji połączonej. |
| Administrator uwierzytelniania | Najmniejsza rola uprzywilejowana może implementować metody uwierzytelniania i zarządzać nimi. |
| Użytkownik | Najmniej uprzywilejowana rola w celu skonfigurowania aplikacji uwierzytelniania na urządzeniu lub zarejestrowania urządzenia z kluczem zabezpieczeń na potrzeby logowania do sieci Web lub systemu Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Wdróż logowanie za pomocą telefonu przy użyciu aplikacji Microsoft Authenticator

Wykonaj kroki opisane w artykule, [Włącz logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md) , aby włączyć aplikację Microsoft Authenticator jako metodę uwierzytelniania bez hasła w organizacji.

### <a name="deploy-fido2-security-key-sign-in"></a>Wdróż Logowanie przy użyciu klucza zabezpieczeń FIDO2

Postępuj zgodnie z instrukcjami w artykule, [włączając klucz zabezpieczeń bezhasło dla usługi Azure AD](howto-authentication-passwordless-security-key.md) , aby włączyć klucze zabezpieczeń FIDO2 jako metody uwierzytelniania bezhaseł.

### <a name="troubleshoot-phone-sign-in"></a>Rozwiązywanie problemów z logowaniem do telefonu

| Scenariusz | Rozwiązanie |
| --- | --- |
| Użytkownik nie może wykonać rejestracji połączonej. | Upewnij się, że [rejestracja łączona](concept-registration-mfa-sspr-combined.md) jest włączona. |
| Użytkownik nie może włączyć aplikacji uwierzytelniania przy użyciu telefonu. | Upewnij się, że użytkownik znajduje się w zakresie wdrożenia. |
| Użytkownik nie należy do zakresu uwierzytelniania bez hasła, ale jest przedstawiany przy użyciu opcji logowania bez hasła, która nie może zostać ukończona. | Ten scenariusz występuje, gdy użytkownik włączył logowanie za pomocą telefonu w aplikacji przed utworzeniem zasad. <br> *Aby włączyć logowanie*: należy dodać użytkownika do zakresu użytkowników z włączonym logowaniem bezhasłem. <br> *Aby zablokować logowanie*: użytkownik powinien usunąć swoje poświadczenia z tej aplikacji. |

### <a name="troubleshoot-security-key-sign-in"></a>Rozwiązywanie problemów z logowaniem przy użyciu klucza zabezpieczeń

| Scenariusz | Rozwiązanie |
| --- | --- |
| Użytkownik nie może wykonać rejestracji połączonej. | Upewnij się, że [rejestracja łączona](concept-registration-mfa-sspr-combined.md) jest włączona. |
| Użytkownik nie może dodać klucza zabezpieczeń w [ustawieniach zabezpieczeń](https://aka.ms/mysecurityinfo). | Upewnij się, że [klucze zabezpieczeń](howto-authentication-passwordless-security-key.md) są włączone. |
| Użytkownik nie może dodać klucza zabezpieczeń w opcjach logowania systemu Windows 10. | [Upewnij się, że klucze zabezpieczeń logowania systemu Windows](./concept-authentication-passwordless.md) |
| **Komunikat o błędzie**: wykryliśmy, że ta przeglądarka lub system operacyjny nie obsługują kluczy zabezpieczeń FIDO2. | Urządzenia zabezpieczeń FIDO2 bezhasło mogą być rejestrowane tylko w obsługiwanych przeglądarkach (Microsoft Edge, Firefox w wersji 67) w systemie Windows 10 w wersji 1809 lub nowszej. |
| **Komunikat o błędzie**: zasady firmy wymagają, aby można było zalogować się przy użyciu innej metody. | Brak pewności, że klucze zabezpieczeń są włączone w dzierżawie. |
| Użytkownik nie może zarządzać moim kluczem zabezpieczeń w systemie Windows 10 w wersji 1809 | Wersja 1809 wymaga użycia oprogramowania do zarządzania kluczami zabezpieczeń dostarczonym przez dostawcę klucza FIDO2. Skontaktuj się z dostawcą, aby uzyskać pomoc techniczną. |
| Myślę, że klucz zabezpieczeń FIDO2 może być wadliwy — jak można go przetestować. | Przejdź do lokalizacji [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) , wprowadź poświadczenia dla konta testowego, podłącz podejrzany klucz zabezpieczeń, wybierz **+** przycisk w prawym górnym rogu ekranu, kliknij polecenie Utwórz i przejdź przez proces tworzenia. Jeśli ten scenariusz nie powiedzie się, urządzenie może być uszkodzone. |

## <a name="next-steps"></a>Następne kroki

- [Włącz klucze zabezpieczeń bezhasło do logowania się w usłudze Azure AD](howto-authentication-passwordless-security-key.md)
- [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Dowiedz się więcej na temat metod uwierzytelniania użycie & szczegółowych informacji](howto-authentication-methods-usage-insights.md)
