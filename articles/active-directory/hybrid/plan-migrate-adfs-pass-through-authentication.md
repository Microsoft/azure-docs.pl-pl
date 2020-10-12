---
title: 'Azure AD Connect: Migrowanie z Federacji do PTA dla usługi Azure AD'
description: Ten artykuł zawiera informacje o przenoszeniu hybrydowego środowiska tożsamości z Federacji do uwierzytelniania przekazywanego.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0ee8661ca985e1882cff54d2fc2cdc5e9ad0a22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335973"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrowanie z Federacji do uwierzytelniania przekazywanego dla Azure Active Directory

W tym artykule opisano sposób przenoszenia domen organizacji z Active Directory Federation Services (AD FS) do uwierzytelniania przekazywanego.

> [!NOTE]
> Zmiana metody uwierzytelniania wymaga planowania, testowania i ewentualnych przestojów. [Wdrażanie etapowe](how-to-connect-staged-rollout.md) zapewnia alternatywny sposób testowania i stopniowego migrowania z Federacji do uwierzytelniania w chmurze przy użyciu uwierzytelniania przekazywanego.
> 
> Jeśli planujesz użycie wdrożenia przemieszczanego, pamiętaj, aby wyłączyć funkcje wdrażania etapowego po zakończeniu wycinania.  Aby uzyskać więcej informacji, zobacz [Migrowanie do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Wymagania wstępne dotyczące migracji do uwierzytelniania przekazywanego

Poniższe wymagania wstępne są wymagane do migracji z programu przy użyciu AD FS do korzystania z uwierzytelniania przekazywanego.

### <a name="update-azure-ad-connect"></a>Azure AD Connect aktualizacji

Aby pomyślnie wykonać kroki, które należy wykonać, aby przeprowadzić migrację do korzystania z uwierzytelniania przekazywanego, musisz mieć [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 lub nowszą wersję. W Azure AD Connect 1.1.819.0 sposób wykonywania konwersji logowania jest istotny. Całkowity czas migracji z AD FS do uwierzytelniania w chmurze w tej wersji jest krótszy od potencjalnie godzin do minuty.

> [!IMPORTANT]
> Można zapoznać się z nieaktualną dokumentacją, narzędziami i blogami, które konwersja użytkownika jest wymagana podczas konwertowania domen z tożsamości federacyjnej na tożsamość zarządzaną. *Konwertowanie użytkowników* nie jest już wymagane. Firma Microsoft pracuje nad aktualizacją dokumentacji i narzędzi w celu odzwierciedlenia tej zmiany.

Aby zaktualizować Azure AD Connect, wykonaj kroki opisane w [Azure AD Connect: Uaktualnij do najnowszej wersji](./how-to-upgrade-previous-version.md).

### <a name="plan-authentication-agent-number-and-placement"></a>Planowanie numeru i położenia agenta uwierzytelniania

Uwierzytelnianie przekazywane wymaga wdrożenia lekkich agentów na serwerze Azure AD Connect i na komputerze lokalnym, na którym działa system Windows Server. Aby zmniejszyć opóźnienie, zainstaluj agentów jak najbliżej kontrolerów domeny Active Directory.

W przypadku większości klientów dwa lub trzy agenci uwierzytelniania są wystarczające, aby zapewnić wysoką dostępność i wymaganą pojemność. Dzierżawa może mieć co najwyżej 12 agentów. Pierwszy Agent jest zawsze instalowany na samym serwerze Azure AD Connect. Aby dowiedzieć się więcej o ograniczeniach agenta i opcjach wdrażania agentów, zobacz [uwierzytelnianie przekazywane przez usługę Azure AD: bieżące ograniczenia](./how-to-connect-pta-current-limitations.md).

### <a name="plan-the-migration-method"></a>Planowanie metody migracji

Można wybrać jedną z dwóch metod migracji z zarządzania tożsamościami federacyjnymi do uwierzytelniania przekazywanego i bezproblemowego logowania jednokrotnego (SSO). Używana metoda zależy od konfiguracji wystąpienia AD FS.

* **Azure AD Connect**. Jeśli wcześniej skonfigurowano AD FS przy użyciu Azure AD Connect, *należy* zmienić uwierzytelnianie przekazywane za pomocą Kreatora Azure AD Connect.

   Azure AD Connect automatycznie uruchamia polecenie cmdlet **Set-MsolDomainAuthentication** po zmianie metody logowania użytkownika. Azure AD Connect automatycznie unfederates wszystkie zweryfikowane domeny federacyjne w dzierżawie usługi Azure AD.

   > [!NOTE]
   > Obecnie Jeśli Azure AD Connect do konfigurowania AD FS, nie można uniknąć unfederating wszystkich domen w dzierżawie w przypadku zmiany logowania użytkownika na uwierzytelnianie przekazywane.
‎
* **Azure AD Connect za pomocą programu PowerShell**. Tej metody można użyć tylko wtedy, gdy nie skonfigurowano jeszcze AD FS przy użyciu Azure AD Connect. W przypadku tej opcji nadal należy zmienić metodę logowania użytkownika za pomocą Kreatora Azure AD Connect. Podstawowa różnica z tą opcją polega na tym, że Kreator nie uruchamia automatycznie polecenia cmdlet **Set-MsolDomainAuthentication** . W przypadku tej opcji masz pełną kontrolę nad tym, które domeny są konwertowane i w jakiej kolejności.

Aby zrozumieć, której metody należy użyć, wykonaj kroki opisane w poniższych sekcjach.

#### <a name="verify-current-user-sign-in-settings"></a>Weryfikowanie bieżących ustawień logowania użytkownika

1. Zaloguj się do [portalu usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego.
2. W sekcji **Logowanie użytkownika** sprawdź następujące ustawienia:
   * **Federacja** jest ustawiona na wartość **Enabled (włączone**).
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **wyłączone**.
   * **Uwierzytelnianie przekazywane** jest ustawione na **wyłączone**.

   ![Zrzut ekranu ustawień w sekcji Logowanie użytkownika Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Sprawdź, jak została skonfigurowana Federacja

1. Na serwerze Azure AD Connect Otwórz Azure AD Connect. Wybierz pozycję **Konfiguruj**.
2. Na stronie **dodatkowe zadania** wybierz pozycję **Wyświetl bieżącą konfigurację**, a następnie wybierz przycisk **dalej**.<br />
 
   ![Zrzut ekranu przedstawiający opcję Wyświetl bieżącą konfigurację na stronie dodatkowe zadania](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. W obszarze **dodatkowe zadania > zarządzanie federacyjnym**przewiń do **Active Directory Federation Services (AD FS)**.<br />

   * Jeśli konfiguracja AD FS zostanie wyświetlona w tej sekcji, można bezpiecznie założyć, że AD FS pierwotnie skonfigurowane przy użyciu Azure AD Connect. Możesz skonwertować domeny z tożsamości federacyjnej na tożsamość zarządzaną przy użyciu opcji Zaloguj Azure AD Connect **zmienić użytkownika** . Aby uzyskać więcej informacji o tym procesie, zobacz sekcję **Opcja A: Konfigurowanie uwierzytelniania przekazywanego przy użyciu Azure AD Connect**.
   * Jeśli AD FS nie ma na liście bieżących ustawień, należy ręcznie skonwertować domeny z tożsamości federacyjnej na tożsamość zarządzaną przy użyciu programu PowerShell. Aby uzyskać więcej informacji o tym procesie, zobacz sekcję **Opcja B: przełączenie z Federacji do uwierzytelniania przekazywanego przy użyciu Azure AD Connect i programu PowerShell**.

### <a name="document-current-federation-settings"></a>Bieżące ustawienia Federacji dokumentu

Aby znaleźć bieżące ustawienia Federacji, uruchom polecenie cmdlet **Get-MsolDomainFederationSettings** :

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Przykład:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Sprawdź wszystkie ustawienia, które mogły zostać dostosowane do projektu Federacji i dokumentacji wdrożenia. Zapoznaj się z tematem dostosowania w programie **PreferredAuthenticationProtocol**, **SupportsMfa**i **PromptLoginBehavior**.

Więcej informacji można znaleźć w następujących artykułach:

* [AD FS Prompt = obsługa parametrów logowania](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Jeśli **SupportsMfa** ma **wartość true**, korzystasz z lokalnego rozwiązania do uwierzytelniania wieloskładnikowego, aby wstrzyknąć wyzwanie drugiego czynnika do przepływu uwierzytelniania użytkownika. Ta konfiguracja nie działa już w scenariuszach uwierzytelniania usługi Azure AD. 
>
> Zamiast tego należy użyć usługi Azure Multi-Factor Authentication opartej na chmurze, aby wykonać tę samą funkcję. Przed kontynuowaniem Oceń wymagania dotyczące uwierzytelniania wieloskładnikowego. Przed przekonwertowaniem domen upewnij się, że rozumiesz, jak korzystać z usługi Azure Multi-Factor Authentication, implikacji licencjonowania i procesu rejestracji użytkownika.

#### <a name="back-up-federation-settings"></a>Tworzenie kopii zapasowej ustawień federacyjnych

Chociaż AD FS w ramach procesów opisanych w tym artykule nie wprowadzono żadnych zmian w innych jednostkach uzależnionych, zalecamy utworzenie aktualnej kopii zapasowej farmy AD FS, z której można przywrócić program. Bieżącą kopię zapasową można utworzyć, korzystając z bezpłatnego [narzędzia Microsoft AD FS Rapid Restore](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Za pomocą narzędzia można tworzyć kopie zapasowe AD FS oraz przywracać istniejące farmy lub tworzyć nowe farmy.

Jeśli zdecydujesz się nie używać narzędzia AD FS szybkiej przywracania, należy wyeksportować Microsoft 365 zaufania jednostki uzależnionej platformy tożsamości i wszystkich skojarzonych z nimi reguł niestandardowych, które zostały dodane. Można wyeksportować zaufania jednostki uzależnionej i powiązane reguły dotyczące roszczeń, korzystając z następującego przykładu programu PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Zagadnienia dotyczące wdrażania i korzystanie z AD FS

W tej sekcji opisano zagadnienia dotyczące wdrażania i szczegółowe informacje dotyczące korzystania z AD FS.

### <a name="current-ad-fs-use"></a>Bieżące użycie AD FS

Przed przekonwertowaniem tożsamości federacyjnej na tożsamość zarządzaną należy dokładnie zapoznać się z AD FS usługi Azure AD, Microsoft 365 i innych aplikacji (zaufania jednostek zależnych). Należy wziąć pod uwagę scenariusze opisane w poniższej tabeli:

| Jeśli użytkownik | Następnie |
|-|-|
| Planujesz używać AD FS z innymi aplikacjami (innymi niż usługa Azure AD i Microsoft 365). | Po przeprowadzeniu konwersji domen będziesz używać obu AD FS i usługi Azure AD. Weź pod uwagę środowisko użytkownika. W niektórych scenariuszach użytkownicy mogą być zobowiązani do dwukrotnego uwierzytelnienia: raz w usłudze Azure AD (w przypadku gdy użytkownik uzyskuje dostęp do logowania jednokrotnego do innych aplikacji, takich jak Microsoft 365) i ponownie dla wszystkich aplikacji, które są nadal powiązane AD FS jako zaufanie jednostki uzależnionej. |
| Wystąpienie AD FS jest w dużym stopniu dostosowywane i opiera się na określonych ustawieniach dostosowania w pliku onload.js (na przykład w przypadku zmiany środowiska logowania, tak aby użytkownicy używali tylko formatu **sAMAccountName** dla nazwy użytkownika, a nie główna nazwa użytkownika (UPN), lub Twoja organizacja ma silnie oznakowane środowisko logowania. Nie można zduplikować pliku onload.js w usłudze Azure AD. | Przed kontynuowaniem należy sprawdzić, czy usługa Azure AD może spełniać bieżące wymagania dotyczące dostosowywania. Aby uzyskać więcej informacji i uzyskać wskazówki, zobacz sekcję dotyczącą AD FS znakowania i AD FS dostosowywania.|
| Aby zablokować wcześniejsze wersje klientów uwierzytelniania, należy użyć AD FS.| Należy rozważyć zastępowanie AD FS formantów blokujących wcześniejsze wersje klientów uwierzytelniania przy użyciu kombinacji [kontroli dostępu warunkowego](../conditional-access/concept-conditional-access-conditions.md) i [reguł dostępu klienta usługi Exchange Online](https://aka.ms/EXOCAR). |
| Użytkownik wymaga od użytkowników przeprowadzenia uwierzytelniania wieloskładnikowego w przypadku lokalnego rozwiązania do obsługi serwera usługi wieloskładnikowego, gdy użytkownicy uwierzytelniają się do AD FS.| W zarządzanej domenie tożsamości nie można wstrzyknąć wyzwania usługi uwierzytelnianie wieloskładnikowe za pośrednictwem lokalnego rozwiązania do uwierzytelniania wieloskładnikowego do przepływu uwierzytelniania. Można jednak użyć usługi Azure Multi-Factor Authentication do uwierzytelniania wieloskładnikowego po przeprowadzeniu konwersji domeny.<br /><br /> Jeśli użytkownicy nie korzystają obecnie z usługi Azure Multi-Factor Authentication, wymagany jest krok rejestracji użytkownika jednorazowej. Należy przygotować się do planowanej rejestracji i przekazać ją do użytkowników. |
| Obecnie używasz zasad kontroli dostępu (reguł autoryzacji) w AD FS, aby kontrolować dostęp do Microsoft 365.| Rozważ zastąpienie zasad zasadami [dostępu warunkowego](../conditional-access/overview.md) usługi Azure AD i [regułami dostępu klienta usługi Exchange Online](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Typowe dostosowania AD FS

W tej sekcji opisano typowe dostosowania AD FS.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork

AD FS wystawia żądania **InsideCorporateNetwork** , jeśli uwierzytelniany użytkownik jest w sieci firmowej. To zastrzeżenie można następnie przesłać do usługi Azure AD. To zastrzeżenie służy do obejścia uwierzytelniania wieloskładnikowego na podstawie lokalizacji sieciowej użytkownika. Aby dowiedzieć się, jak ustalić, czy ta funkcja jest obecnie dostępna w AD FS, zobacz [Zaufane adresy IP dla użytkowników federacyjnych](../authentication/howto-mfa-adfs.md).

**InsideCorporateNetwork** oświadczenia nie jest dostępne po przekonwertowaniu domen na uwierzytelnianie przekazywane. Aby zastąpić tę funkcję, można użyć [nazwanych lokalizacji w usłudze Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) .

Po skonfigurowaniu nazwanych lokalizacji należy zaktualizować wszystkie zasady dostępu warunkowego, które zostały skonfigurowane w taki sposób, aby uwzględniać lub wykluczać Sieć **wszystkie Zaufane lokalizacje** lub **Zaufane adresy IP usługi MFA** w celu odzwierciedlenia nowych nazwanych lokalizacji.

Aby uzyskać więcej informacji o warunku **lokalizacji** w dostępie warunkowym, zobacz [Active Directory lokalizacji dostępu warunkowego](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrydowe urządzenia dołączone do usługi Azure AD

Po dołączeniu urządzenia do usługi Azure AD można utworzyć reguły dostępu warunkowego, które wymuszają, aby urządzenia spełniały standardy dostępu do zabezpieczeń i zgodności. Ponadto użytkownicy mogą logować się do urządzenia przy użyciu konta służbowego lub szkolnego, a nie konta osobistego. W przypadku korzystania z hybrydowych urządzeń przyłączonych do usługi Azure AD można przyłączyć Active Directory urządzenia przyłączone do domeny do usługi Azure AD. Środowisko federacyjne mogło zostać skonfigurowane do korzystania z tej funkcji.

Aby zapewnić, że sprzężenie hybrydowe będzie nadal działało w przypadku urządzeń przyłączonych do domeny po przekonwertowaniu domen na uwierzytelnianie przekazywane, w przypadku klientów z systemem Windows 10 należy użyć Azure AD Connect do synchronizowania kont komputerów Active Directory z usługą Azure AD.

W przypadku kont komputerów z systemem Windows 8 i Windows 7 sprzężenie hybrydowe używa bezproblemowego logowania jednokrotnego do zarejestrowania komputera w usłudze Azure AD. Nie musisz synchronizować kont komputerów z systemem Windows 8 i Windows 7, takich jak w przypadku urządzeń z systemem Windows 10. Należy jednak wdrożyć zaktualizowany plik workplacejoin.exe (za pośrednictwem pliku msi) na klientach z systemami Windows 8 i Windows 7, aby mogli zarejestrować się przy użyciu bezproblemowego logowania jednokrotnego. [Pobierz plik msi](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie hybrydowych urządzeń przyłączonych do usługi Azure AD](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Znakowanie

Jeśli Twoja organizacja [dostosował AD FS strony logowania](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) , aby wyświetlić informacje, które są bardziej przydatne dla organizacji, rozważ wprowadzenie podobnych [dostosowań na stronie logowania do usługi Azure AD](../fundamentals/customize-branding.md).

Chociaż podobne dostosowania są dostępne, niektóre zmiany wizualne na stronach logowania powinny być oczekiwane po konwersji. Możesz chcieć podać informacje o oczekiwanych zmianach w komunikacji z użytkownikami.

> [!NOTE]
> Znakowanie organizacji jest dostępne tylko w przypadku zakupienia licencji Premium lub Basic dla Azure Active Directory lub w przypadku posiadania licencji Microsoft 365.

## <a name="plan-for-smart-lockout"></a>Planowanie inteligentnej blokady

Usługa inteligentnego blokowania usługi Azure AD chroni przed atakami polegającymi na wymuszeniu hasła. Inteligentna blokada uniemożliwia zablokowanie lokalnego konta Active Directory w przypadku użycia uwierzytelniania przekazywanego, a zasady grupy blokady konta są ustawiane w Active Directory.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory inteligentnego blokowania](../authentication/howto-password-smart-lockout.md).

## <a name="plan-deployment-and-support"></a>Planowanie wdrożenia i pomocy technicznej

Wykonaj zadania, które są opisane w tej sekcji, aby ułatwić Planowanie wdrażania i obsługi.

### <a name="plan-the-maintenance-window"></a>Planowanie okna obsługi

Chociaż proces konwersji domeny jest stosunkowo szybki, usługa Azure AD może nadal wysyłać niektóre żądania uwierzytelniania do serwerów AD FS przez maksymalnie cztery godziny po zakończeniu konwersji domeny. W tym cztery-godzinnym oknie i w zależności od różnych pamięci podręcznych po stronie usługi usługa Azure AD może nie akceptować tych uwierzytelnień. Użytkownicy mogą otrzymać komunikat o błędzie. Użytkownik może nadal pomyślnie uwierzytelniać się w AD FS, ale usługa Azure AD nie akceptuje już tokenu wystawionego przez użytkownika, ponieważ ta relacja zaufania federacji jest teraz usuwana.

Dotyczy tylko użytkowników, którzy uzyskują dostęp do usług za pośrednictwem przeglądarki sieci Web podczas tego okna po konwersji przed wyczyszczeniem pamięci podręcznej po stronie usługi. Nie ma to wpływu na starszych klientów (Exchange ActiveSync, Outlook 2010/2013), ponieważ usługa Exchange Online przechowuje pamięć podręczną swoich poświadczeń przez określony czas. Pamięć podręczna jest używana do dyskretnego uwierzytelniania użytkownika. Użytkownik nie musi powrócić do AD FS. Poświadczenia przechowywane na urządzeniu dla tych klientów są używane do dyskretnego uwierzytelniania samodzielnego po wyczyszczeniu tej pamięci podręcznej. Użytkownicy nie będą otrzymywać żadnych monitów o hasło w wyniku procesu konwersji domeny.

Nowoczesne komputery klienckie uwierzytelniania (aplikacje pakietu Office 2016 i pakietu Office 2013, iOS i Android) używają prawidłowego tokenu odświeżania, aby uzyskać nowe tokeny dostępu w celu uzyskania ciągłego dostępu do zasobów, a nie powrotu do AD FS. Ci klienci są odporne na monity hasła, które wynikają z procesu konwersji domeny. Klienci będą nadal działać bez dodatkowej konfiguracji.

> [!IMPORTANT]
> Nie zamykaj środowiska AD FS ani nie usuwaj relacji zaufania jednostki uzależnionej Microsoft 365 do momentu zweryfikowania, że wszyscy użytkownicy mogą pomyślnie uwierzytelniać się przy użyciu uwierzytelniania w chmurze.

### <a name="plan-for-rollback"></a>Planowanie wycofywania

W przypadku wystąpienia poważnego problemu, którego nie można szybko rozwiązać, może być konieczne wycofanie rozwiązania do Federacji. Ważne jest, aby zaplanować czynności, które należy wykonać, jeśli wdrożenie nie zostanie wdrożone zgodnie z oczekiwaniami. Jeśli konwersja domeny lub użytkowników zakończy się niepowodzeniem podczas wdrażania lub jeśli musisz wrócić do Federacji, musisz zrozumieć, jak wyeliminować wszelkie przestoje i ograniczyć wpływ na użytkowników.

#### <a name="to-roll-back"></a>Aby wycofać

Aby zaplanować wycofanie, należy zapoznać się z dokumentacją dotyczącą projektu Federacji i wdrożenia określonego wdrożenia. Proces powinien obejmować następujące zadania:

* Konwertowanie domen zarządzanych na domeny federacyjne przy użyciu polecenia cmdlet **Convert-MSOLDomaintoFederated** .
* W razie potrzeby skonfigurowanie dodatkowych reguł oświadczeń.

### <a name="plan-communications"></a>Planowanie komunikacji

Ważnym elementem planowania wdrożenia i pomocy technicznej jest upewnienie się, że użytkownicy są świadomie informowani o nadchodzących zmianach. Użytkownicy powinni z wyprzedzeniem znać, co się stało, i co jest wymagane.

Po wdrożeniu zarówno uwierzytelniania przekazywanego, jak i bezproblemowego logowania jednokrotnego użytkownicy mogą logować się do Microsoft 365 i innych zasobów, które są uwierzytelniane za pomocą zmian usługi Azure AD. Użytkownicy spoza sieci zobaczą tylko stronę logowania usługi Azure AD. Ci użytkownicy nie są przekierowywani do strony opartej na formularzach, która jest prezentowana przez zewnętrzne serwery proxy aplikacji sieci Web.

Uwzględnij następujące elementy w strategii komunikacji:

* Powiadamiaj użytkowników o nadchodzących i wydanych funkcjach przy użyciu:
   * Wiadomości e-mail i innych wewnętrznych kanałów komunikacyjnych.
   * Wizualizacje, takie jak plakaty.
   * Executive, na żywo lub w innej komunikacji.
* Określ, kto będzie w trakcie dostosowywania komunikacji i kto wyśle wiadomość.

## <a name="implement-your-solution"></a>Implementowanie rozwiązania

Twoje rozwiązanie zostało zaplanowane. Teraz można go zaimplementować. Implementacja obejmuje następujące składniki:

* Przygotowanie do bezproblemowego logowania jednokrotnego.
* Zmiana metody logowania na uwierzytelnianie przekazywane i włączenie bezproblemowego logowania jednokrotnego.

### <a name="step-1-prepare-for-seamless-sso"></a>Krok 1: przygotowanie do bezproblemowego logowania jednokrotnego

Aby urządzenia używały bezproblemowego logowania jednokrotnego, należy dodać adres URL usługi Azure AD do ustawień strefy intranetowej użytkowników przy użyciu zasad grupy w Active Directory.

Domyślnie przeglądarki sieci Web automatycznie obliczają poprawną strefę, Internet lub intranet, z adresu URL. Na przykład **http: \/ \/ contoso/** mapuje do strefy intranet i **http: \/ \/ intranet.contoso.com** mapuje do strefy Internet (ponieważ adres URL zawiera kropkę). Przeglądarki wysyłają bilety Kerberos do punktu końcowego w chmurze, takiego jak adres URL usługi Azure AD, tylko wtedy, gdy jawnie dodasz adres URL do strefy intranetowej przeglądarki.

Wykonaj kroki, aby [wdrożyć](./how-to-connect-sso-quick-start.md) wymagane zmiany na urządzeniach.

> [!IMPORTANT]
> Wprowadzenie tej zmiany nie modyfikuje sposobu logowania użytkowników do usługi Azure AD. Ważne jest jednak, aby zastosować tę konfigurację do wszystkich urządzeń przed kontynuowaniem. Użytkownicy logujący się na urządzeniach, które nie otrzymały tej konfiguracji po prostu, muszą wprowadzić nazwę użytkownika i hasło, aby zalogować się do usługi Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Krok 2. zmiana metody logowania na uwierzytelnianie przekazywane i włączenie bezproblemowego logowania jednokrotnego

Dostępne są dwie opcje zmiany metody logowania na uwierzytelnianie przekazywane i włączenie bezproblemowego logowania jednokrotnego.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Opcja A: Konfigurowanie uwierzytelniania przekazywanego za pomocą Azure AD Connect

Użyj tej metody, jeśli początkowo skonfigurujesz środowisko AD FS przy użyciu Azure AD Connect. Nie można użyć tej metody, jeśli *nie* skonfigurowano pierwotnie środowiska AD FS przy użyciu Azure AD Connect.

> [!IMPORTANT]
> Po wykonaniu poniższych kroków wszystkie domeny zostaną przekonwertowane z tożsamości federacyjnej na tożsamość zarządzaną. Aby uzyskać więcej informacji, zobacz [Planowanie metody migracji](#plan-the-migration-method).

Najpierw Zmień metodę logowania:

1. Na serwerze Azure AD Connect Otwórz Kreatora Azure AD Connect.
2. Wybierz pozycję **Zmień Logowanie użytkownika**, a następnie wybierz przycisk **dalej**. 
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na stronie **logowania użytkownika** wybierz przycisk **uwierzytelniania przekazywanego** , wybierz opcję **Włącz logowanie jednokrotne**, a następnie wybierz przycisk **dalej**.
5. Na stronie **Włącz logowanie jednokrotne** wprowadź poświadczenia konta administratora domeny, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > Aby umożliwić bezproblemowe logowanie jednokrotne, wymagane są poświadczenia konta administratora domeny. Proces wykonuje następujące akcje, które wymagają podniesionych uprawnień. Poświadczenia konta administratora domeny nie są przechowywane w Azure AD Connect ani w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko w celu włączenia tej funkcji. Poświadczenia są odrzucane po pomyślnym zakończeniu procesu.
   >
   > 1. Konto komputera o nazwie AZUREADSSOACC (reprezentujące usługę Azure AD) jest tworzone w lokalnym wystąpieniu Active Directory.
   > 2. Klucz odszyfrowywania Kerberos konta komputera jest bezpiecznie współużytkowany z usługą Azure AD.
   > 3. Dwie główne nazwy usługi (SPN) protokołu Kerberos są tworzone w celu reprezentowania dwóch adresów URL, które są używane podczas logowania do usługi Azure AD.

6. Na stronie **gotowy do skonfigurowania** upewnij się, że jest zaznaczone pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji** . Następnie wybierz pozycję **Konfiguruj**.<br />

   ![Zrzut ekranu przedstawiający stronę gotowy do konfiguracji](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. W portalu usługi Azure AD wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Azure AD Connect**.
8. Sprawdź te ustawienia:
   * Wartość **federacyjna** została **wyłączona**.
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **włączone**.
   * **Uwierzytelnianie przekazywane** jest ustawione na **włączone**.<br />

   ![Zrzut ekranu pokazujący ustawienia w sekcji logowania użytkownika](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Dalej. Wdróż dodatkowe metody uwierzytelniania:

1. W Azure Portal przejdź do **Azure Active Directory**  >  **Azure AD Connect**, a następnie wybierz pozycję **uwierzytelnianie przekazywane**.
2. Na stronie **uwierzytelnianie przekazywane** wybierz przycisk **Pobierz** .
3. Na stronie **Agent pobierania** wybierz pozycję **Zaakceptuj warunki i Pobierz**.

   Rozpocznie się pobieranie dodatkowych agentów uwierzytelniania. Zainstaluj pomocniczy Agent uwierzytelniania na serwerze przyłączonym do domeny. 

   > [!NOTE]
   > Pierwszy Agent jest zawsze instalowany na serwerze Azure AD Connect w ramach zmian konfiguracji wprowadzonych w sekcji **logowania użytkownika** w narzędziu Azure AD Connect. Zainstaluj wszystkich dodatkowych agentów uwierzytelniania na osobnym serwerze. Zalecamy, aby dostępne były dwa lub trzy dodatkowe agenci uwierzytelniania. 

4. Uruchom instalację agenta uwierzytelniania. Podczas instalacji należy wprowadzić poświadczenia konta administratora globalnego.

   ![Zrzut ekranu pokazujący przycisk Zainstaluj, który służy do uruchamiania pakietu Microsoft Azure AD Connect Authentication Agent.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Zrzut ekranu przedstawiający stronę logowania firmy Microsoft.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Po zainstalowaniu agenta uwierzytelniania można wrócić do strony kondycja agenta uwierzytelniania przekazywanego, aby sprawdzić stan dodatkowych agentów.

Przejdź do [testowania i następnych kroków](#testing-and-next-steps).

> [!IMPORTANT]
> Pomiń opcję sekcja **B: przełączenie z Federacji do uwierzytelniania przekazywanego przy użyciu Azure AD Connect i programu PowerShell**. Kroki opisane w tej sekcji nie mają zastosowania w przypadku wybrania opcji A w celu zmiany metody logowania na uwierzytelnianie przekazywane i włączenia bezproblemowego logowania jednokrotnego. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Opcja B: przełączenie z Federacji do uwierzytelniania przekazywanego przy użyciu Azure AD Connect i programu PowerShell

Użyj tej opcji, jeśli nie skonfigurowano wstępnie domen federacyjnych przy użyciu Azure AD Connect.

Najpierw Włącz uwierzytelnianie przekazywane:

1. Na serwerze Azure AD Connect Otwórz Kreatora Azure AD Connect.
2. Wybierz pozycję **Zmień Logowanie użytkownika**, a następnie wybierz przycisk **dalej**.
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na stronie **logowania użytkownika** wybierz przycisk **uwierzytelnianie przekazywane** . Wybierz pozycję **Włącz logowanie jednokrotne**, a następnie wybierz pozycję **dalej**.
5. Na stronie **Włącz logowanie jednokrotne** wprowadź poświadczenia konta administratora domeny, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > Aby umożliwić bezproblemowe logowanie jednokrotne, wymagane są poświadczenia konta administratora domeny. Proces wykonuje następujące akcje, które wymagają podniesionych uprawnień. Poświadczenia konta administratora domeny nie są przechowywane w Azure AD Connect ani w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko w celu włączenia tej funkcji. Poświadczenia są odrzucane po pomyślnym zakończeniu procesu.
   > 
   > 1. Konto komputera o nazwie AZUREADSSOACC (reprezentujące usługę Azure AD) jest tworzone w lokalnym wystąpieniu Active Directory.
   > 2. Klucz odszyfrowywania Kerberos konta komputera jest bezpiecznie współużytkowany z usługą Azure AD.
   > 3. Dwie główne nazwy usługi (SPN) protokołu Kerberos są tworzone w celu reprezentowania dwóch adresów URL, które są używane podczas logowania do usługi Azure AD.

6. Na stronie **gotowy do skonfigurowania** upewnij się, że jest zaznaczone pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji** . Następnie wybierz pozycję **Konfiguruj**.<br />

   ![Zrzut ekranu przedstawiający stronę gotowy do skonfigurowania i przycisk Konfiguruj](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Po wybraniu opcji **Konfiguruj**należy wykonać następujące czynności:

   1. Zostanie zainstalowany pierwszy Agent uwierzytelniania przekazującego.
   2. Funkcja przekazywania jest włączona.
   3. Bezproblemowe logowanie jednokrotne jest włączone.

7. Sprawdź te ustawienia:
   * **Federacja** jest ustawiona na wartość **Enabled (włączone**).
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **włączone**.
   * **Uwierzytelnianie przekazywane** jest ustawione na **włączone**.
   
   ![Zrzut ekranu pokazujący ustawienia, które należy sprawdzić w sekcji Logowanie użytkownika.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Wybierz pozycję **uwierzytelnianie przekazywane** i sprawdź, czy stan jest **aktywny**.<br />
   
   Jeśli Agent uwierzytelniania nie jest aktywny, przed kontynuowaniem procesu konwersji domeny w następnym kroku wykonaj kilka [kroków rozwiązywania problemów](./tshoot-connect-pass-through-authentication.md) . Istnieje ryzyko powstania przestoju uwierzytelniania w przypadku konwersji domen przed potwierdzeniem, że agenci uwierzytelniania przekazywanego zostały pomyślnie zainstalowani i że ich stan jest **aktywny** w Azure Portal.

Następnie wdróż dodatkowych agentów uwierzytelniania:

1. W Azure Portal przejdź do **Azure Active Directory**  >  **Azure AD Connect**, a następnie wybierz pozycję **uwierzytelnianie przekazywane**.
2. Na stronie **uwierzytelnianie przekazywane** wybierz przycisk **Pobierz** . 
3. Na stronie **Agent pobierania** wybierz pozycję **Zaakceptuj warunki i Pobierz**.
 
   Rozpocznie się pobieranie agenta uwierzytelniania. Zainstaluj pomocniczy Agent uwierzytelniania na serwerze przyłączonym do domeny.

   > [!NOTE]
   > Pierwszy Agent jest zawsze instalowany na serwerze Azure AD Connect w ramach zmian konfiguracji wprowadzonych w sekcji **logowania użytkownika** w narzędziu Azure AD Connect. Zainstaluj wszystkich dodatkowych agentów uwierzytelniania na osobnym serwerze. Zalecamy, aby dostępne były dwa lub trzy dodatkowe agenci uwierzytelniania.
 
4. Uruchom instalację agenta uwierzytelniania. Podczas instalacji należy wprowadzić poświadczenia konta administratora globalnego.<br />

   ![Zrzut ekranu pokazujący przycisk Instaluj na stronie pakietu agenta uwierzytelniania Microsoft Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Zrzut ekranu przedstawiający stronę logowania](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Po zainstalowaniu agenta uwierzytelniania można wrócić do strony kondycja agenta uwierzytelniania przekazywanego, aby sprawdzić stan dodatkowych agentów.

W tym momencie uwierzytelnianie federacyjne jest nadal aktywne i działa dla Twoich domen. Aby kontynuować wdrażanie, należy przekonwertować każdą domenę z tożsamości federacyjnej na tożsamość zarządzaną, aby uwierzytelnianie przekazywane było obsługiwane przez żądania uwierzytelniania dla domeny.

Nie musisz konwertować wszystkich domen w tym samym czasie. Możesz zacząć od domeny testowej w dzierżawie produkcyjnej lub rozpocząć pracę z domeną o najmniejszej liczbie użytkowników.

Ukończ konwersję przy użyciu modułu Azure AD PowerShell:

1. W programie PowerShell Zaloguj się do usługi Azure AD przy użyciu konta administratora globalnego.
2. Aby skonwertować pierwszą domenę, uruchom następujące polecenie:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. W portalu usługi Azure AD wybierz pozycję **Azure Active Directory**  >  **Azure AD Connect**.
4. Po przeprowadzeniu konwersji wszystkich domen federacyjnych sprawdź następujące ustawienia:
   * Wartość **federacyjna** została **wyłączona**.
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **włączone**.
   * **Uwierzytelnianie przekazywane** jest ustawione na **włączone**.<br />

   ![Zrzut ekranu pokazujący ustawienia w sekcji logowania użytkownika w portalu usługi Azure AD.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testowanie i następne kroki

Wykonaj następujące zadania, aby zweryfikować uwierzytelnianie przekazywane i zakończyć proces konwersji.

### <a name="test-pass-through-authentication"></a>Testowanie uwierzytelniania przekazywanego 

Gdy dzierżawca używa tożsamości federacyjnej, użytkownicy zostali przekierowani ze strony logowania usługi Azure AD do środowiska AD FS. Teraz, gdy dzierżawca jest skonfigurowany do korzystania z uwierzytelniania przekazywanego zamiast uwierzytelniania federacyjnego, użytkownicy nie są przekierowywani do AD FS. Zamiast tego użytkownicy logują się bezpośrednio na stronie logowania usługi Azure AD.

Aby przetestować uwierzytelnianie przekazywane:

1. Otwórz program Internet Explorer w trybie InPrivate, aby bezproblemowe logowanie jednokrotne nie zalogować Cię automatycznie.
2. Przejdź do strony logowania do programu Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Wprowadź nazwę UPN użytkownika, a następnie wybierz przycisk **dalej**. Upewnij się, że wprowadzasz nazwę UPN użytkownika hybrydowego, który został zsynchronizowany z wystąpienia lokalnego Active Directory i kto wcześniej użył uwierzytelniania federacyjnego. Strona, na której zostanie wprowadzona nazwa użytkownika i hasło:

   ![Zrzut ekranu przedstawiający stronę logowania, w której wprowadzasz nazwę użytkownika](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Zrzut ekranu przedstawiający stronę logowania, w której można wprowadzić hasło](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Po wprowadzeniu hasła i wybraniu opcji **Zaloguj**nastąpi przekierowanie do portalu Office 365.

   ![Zrzut ekranu przedstawiający Portal Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Bezproblemowe testowanie logowania jednokrotnego

Aby przetestować bezproblemową rejestrację jednokrotną:

1. Zaloguj się na komputerze przyłączonym do domeny, który jest połączony z siecią firmową.
2. W programie Internet Explorer lub Chrome przejdź do jednego z następujących adresów URL (Zastąp ciąg "contoso" domeną):

   * https: \/ \/ myapps.Microsoft.com/contoso.com
   * https: \/ \/ myapps.Microsoft.com/contoso.onmicrosoft.com

   Użytkownik zostanie krótko przekierowany do strony logowania usługi Azure AD, która wyświetla komunikat "próba zalogowania". Użytkownik nie jest monitowany o podanie nazwy użytkownika lub hasła.<br />

   ![Zrzut ekranu przedstawiający stronę logowania i wiadomość usługi Azure AD](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. Użytkownik zostanie przekierowany i pomyślnie zalogował się do panelu dostępu:

   > [!NOTE]
   > Bezproblemowe logowanie jednokrotne działa na Microsoft 365 usługach, które obsługują wskazówkę domeny (na przykład myapps.microsoft.com/contoso.com). Obecnie portal Microsoft 365 (portal.office.com) nie obsługuje podpowiedzi do domeny. Użytkownicy muszą wprowadzić nazwę UPN. Po wprowadzeniu nazwy UPN bezproblemowe logowanie jednokrotne pobiera bilet protokołu Kerberos w imieniu użytkownika. Użytkownik jest zalogowany bez wprowadzania hasła.

   > [!TIP]
   > Rozważ wdrożenie funkcji [dołączania hybrydowego usługi Azure AD w systemie Windows 10](../devices/overview.md) w celu usprawnienia logowania jednokrotnego.

### <a name="remove-the-relying-party-trust"></a>Usuń relację zaufania jednostki uzależnionej

Po sprawdzeniu, czy wszyscy użytkownicy i klienci pomyślnie uwierzytelniają się za pośrednictwem usługi Azure AD, można bezpiecznie usunąć Microsoft 365 zaufania jednostki uzależnionej.

Jeśli nie używasz AD FS do innych celów (to oznacza, że w przypadku innych relacji zaufania jednostek uzależnionych), możesz bezpiecznie zlikwidować AD FS na tym etapie.

### <a name="rollback"></a>Wycofywania

Jeśli odkryjesz główny problem i nie można go szybko rozwiązać, możesz zdecydować się na wycofanie rozwiązania do Federacji.

Zapoznaj się z dokumentacją dotyczącą projektu federacyjnego i wdrożenia, aby poznać szczegóły dotyczące określonego wdrożenia. Proces ten powinien dotyczyć następujących zadań:

* Przekonwertuj domeny zarządzane na uwierzytelnianie federacyjne przy użyciu polecenia cmdlet **Convert-MSOLDomaintoFederated** .
* W razie potrzeby skonfiguruj dodatkowe reguły oświadczeń.

### <a name="sync-userprincipalname-updates"></a>Synchronizuj aktualizacje userPrincipalName

W przeszłości aktualizacje atrybutu **userPrincipalName** , który używa usługi synchronizacji ze środowiska lokalnego, są blokowane, chyba że oba te warunki są spełnione:

* Użytkownik należy do zarządzanej domeny tożsamości (innej niż federacyjna).
* Użytkownikowi nie przypisano licencji.

Aby dowiedzieć się, jak sprawdzić lub włączyć tę funkcję, zobacz [synchronizowanie aktualizacji userPrincipalName](./how-to-connect-syncservice-features.md).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Przetaczanie bezproblemowego klucza odszyfrowywania Kerberos logowania jednokrotnego

Ważne jest często przekazanie klucza odszyfrowywania Kerberos konta komputera AZUREADSSOACC (co reprezentuje usługę Azure AD). Konto komputera AZUREADSSOACC jest tworzone w lokalnym lesie Active Directory. Zdecydowanie zalecamy przeprowadzenie klucza odszyfrowywania Kerberos co najmniej co 30 dni, aby dostosować się do sposobu, w jaki Active Directory Członkowie domeny przesyłają zmiany hasła. Brak skojarzonego urządzenia dołączonego do obiektu konta komputera AZUREADSSOACC, dlatego należy ręcznie wykonać przerzucanie.

Zainicjuj Przerzucanie bezproblemowego klucza odszyfrowywania Kerberos protokołu SSO na serwerze lokalnym, na którym działa Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Jak mogę przejęcia klucza odszyfrowywania Kerberos konta komputera AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Monitoruj serwery z uruchomionymi agentami uwierzytelniania, aby zachować dostępność rozwiązania. Oprócz ogólnych liczników wydajności serwera, agenci uwierzytelniania uwidaczniają obiekty wydajności, które mogą pomóc w zrozumieniu statystyk i błędów związanych z uwierzytelnianiem.

Agenci uwierzytelniania mogą rejestrować operacje w dziennikach zdarzeń systemu Windows, które znajdują się w obszarze Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Możesz również włączyć rejestrowanie w celu rozwiązywania problemów.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure Active Directory uwierzytelnianie przekazywane](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [pojęć związanych z projektowaniem Azure AD Connect](plan-connect-design-concepts.md).
* Wybierz odpowiednie [uwierzytelnianie](./choose-ad-authn.md).
* Poznaj [obsługiwane topologie](plan-connect-design-concepts.md).