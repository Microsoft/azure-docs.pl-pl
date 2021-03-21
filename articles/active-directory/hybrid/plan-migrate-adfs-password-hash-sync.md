---
title: 'Azure AD Connect: Migrowanie z Federacji do PHS dla usługi Azure AD | Microsoft Docs'
description: Ten artykuł zawiera informacje o przenoszeniu hybrydowego środowiska tożsamości z Federacji do synchronizacji skrótów haseł.
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
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861820"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migruj z Federacji do synchronizacji skrótów haseł dla Azure Active Directory

W tym artykule opisano sposób przenoszenia domen organizacji z Active Directory Federation Services (AD FS) do synchronizacji skrótów haseł.

> [!NOTE]
> Zmiana metody uwierzytelniania wymaga planowania, testowania i ewentualnych przestojów. [Wdrażanie etapowe](how-to-connect-staged-rollout.md) zapewnia alternatywny sposób testowania i stopniowego migrowania z Federacji do uwierzytelniania w chmurze przy użyciu funkcji synchronizacji skrótów haseł.
>
> Jeśli planujesz użycie wdrożenia przemieszczanego, pamiętaj, aby wyłączyć funkcje wdrażania etapowego po zakończeniu wycinania.  Aby uzyskać więcej informacji, zobacz [Migrowanie do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Wymagania wstępne dotyczące migracji do synchronizacji skrótów haseł

Aby przeprowadzić migrację z używania AD FS do korzystania z synchronizacji skrótów haseł, wymagane są następujące wymagania wstępne.


### <a name="update-azure-ad-connect"></a>Azure AD Connect aktualizacji

Aby pomyślnie wykonać kroki migracji do synchronizacji skrótów haseł, należy zainstalować program [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Ta wersja zawiera istotne zmiany w sposobie wykonywania konwersji logowania i skraca łączny czas migracji z Federacji do uwierzytelniania w chmurze z potencjalnie godzin do minuty.


> [!IMPORTANT]
> Można zapoznać się z nieaktualną dokumentacją, narzędziami i blogami, które konwersja użytkownika jest wymagana podczas konwertowania domen z tożsamości federacyjnej na tożsamość zarządzaną. *Konwertowanie użytkowników* nie jest już wymagane. Firma Microsoft pracuje nad aktualizacją dokumentacji i narzędzi w celu odzwierciedlenia tej zmiany.

Aby zaktualizować Azure AD Connect, wykonaj kroki opisane w [Azure AD Connect: Uaktualnij do najnowszej wersji](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Wymagane uprawnienia synchronizacji skrótów haseł

Azure AD Connect można skonfigurować przy użyciu ustawień ekspresowych lub instalacji niestandardowej. Jeśli użyto opcji instalacji niestandardowej, [wymagane uprawnienia](./reference-connect-accounts-permissions.md) do synchronizacji skrótów haseł mogą nie być stosowane.

Konto usługi Azure AD Connect Active Directory Domain Services (AD DS) wymaga następujących uprawnień do synchronizowania skrótów haseł:

* Replikowanie zmian w katalogu
* Replikuj wszystkie zmiany katalogu

Teraz jest to dobry moment, aby sprawdzić, czy te uprawnienia są stosowane dla wszystkich domen w lesie.

### <a name="plan-the-migration-method"></a>Planowanie metody migracji

Można wybrać jedną z dwóch metod migracji z usługi federacyjnej tożsamości do synchronizacji skrótów haseł i bezproblemowego logowania jednokrotnego (SSO). Używana metoda zależy od konfiguracji wystąpienia AD FS.

* **Azure AD Connect**. Jeśli wcześniej skonfigurowano AD FS przy użyciu Azure AD Connect, *należy* zmienić na synchronizację skrótów haseł przy użyciu kreatora Azure AD Connect.

   Azure AD Connect automatycznie uruchamia polecenie cmdlet **Set-MsolDomainAuthentication** po zmianie metody logowania użytkownika. Azure AD Connect automatycznie unfederates wszystkie zweryfikowane domeny federacyjne w dzierżawie usługi Azure AD.

   > [!NOTE]
   > Obecnie Jeśli Azure AD Connect do konfigurowania AD FS, nie można uniknąć unfederating wszystkich domen w dzierżawie w przypadku zmiany logowania użytkownika do synchronizacji skrótów haseł. ‎
* **Azure AD Connect za pomocą programu PowerShell**. Tej metody można użyć tylko wtedy, gdy nie skonfigurowano jeszcze AD FS przy użyciu Azure AD Connect. W przypadku tej opcji nadal należy zmienić metodę logowania użytkownika za pomocą Kreatora Azure AD Connect. Podstawowa różnica z tą opcją polega na tym, że Kreator nie uruchamia automatycznie polecenia cmdlet **Set-MsolDomainAuthentication** . W przypadku tej opcji masz pełną kontrolę nad tym, które domeny są konwertowane i w jakiej kolejności.

Aby zrozumieć, której metody należy użyć, wykonaj kroki opisane w poniższych sekcjach.

#### <a name="verify-current-user-sign-in-settings"></a>Weryfikowanie bieżących ustawień logowania użytkownika

Aby sprawdzić bieżące ustawienia logowania użytkownika:

1. Zaloguj się do [portalu usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego.
2. W sekcji **Logowanie użytkownika** sprawdź następujące ustawienia:
   * **Federacja** jest ustawiona na wartość **Enabled (włączone**).
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **wyłączone**.
   * **Uwierzytelnianie przekazywane** jest ustawione na **wyłączone**.

   ![Zrzut ekranu ustawień w sekcji Logowanie użytkownika Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Weryfikowanie konfiguracji Azure AD Connect

1. Na serwerze Azure AD Connect Otwórz Azure AD Connect. Wybierz pozycję **Konfiguruj**.
2. Na stronie **dodatkowe zadania** wybierz pozycję **Wyświetl bieżącą konfigurację**, a następnie wybierz przycisk **dalej**.<br />

   ![Zrzut ekranu przedstawiający opcję Wyświetl bieżącą konfigurację wybraną na stronie dodatkowe zadania](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na stronie **Przejrzyj swoje rozwiązanie** Sprawdź stan **synchronizacji skrótu hasła** .<br /> 

   * Jeśli **Synchronizacja skrótów haseł** jest **wyłączona**, wykonaj kroki opisane w tym artykule, aby je włączyć.
   * Jeśli **Synchronizacja skrótów haseł** jest ustawiona na wartość **włączone**, można pominąć sekcję **krok 1. Włączanie synchronizacji skrótów haseł** w tym artykule.
4. Na stronie **Przejrzyj rozwiązanie** przewiń do **Active Directory Federation Services (AD FS)**.<br />

   * Jeśli konfiguracja AD FS zostanie wyświetlona w tej sekcji, można bezpiecznie założyć, że AD FS pierwotnie skonfigurowane przy użyciu Azure AD Connect. Możesz skonwertować domeny z tożsamości federacyjnej na tożsamość zarządzaną przy użyciu opcji Zaloguj Azure AD Connect **zmienić użytkownika** . Ten proces jest szczegółowo opisany w sekcji **Opcja A: przełączenie z Federacji do synchronizacji skrótów haseł przy użyciu Azure AD Connect**.
   * Jeśli AD FS nie ma na liście bieżących ustawień, należy ręcznie skonwertować domeny z tożsamości federacyjnej na tożsamość zarządzaną przy użyciu programu PowerShell. Aby uzyskać więcej informacji o tym procesie, zobacz sekcję **Opcja B: przełączenie z Federacji do synchronizacji skrótów haseł przy użyciu Azure AD Connect i programu PowerShell**.

### <a name="document-current-federation-settings"></a>Bieżące ustawienia Federacji dokumentu

Aby znaleźć bieżące ustawienia Federacji, uruchom polecenie cmdlet **Get-MsolDomainFederationSettings** :

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Przykład:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Sprawdź wszystkie ustawienia, które mogły zostać dostosowane do projektu Federacji i dokumentacji wdrożenia. Zapoznaj się z tematem dostosowania w programie **PreferredAuthenticationProtocol**, **SupportsMfa** i **PromptLoginBehavior**.

Więcej informacji można znaleźć w następujących artykułach:

* [AD FS Prompt = obsługa parametrów logowania](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Jeśli **SupportsMfa** ma **wartość true**, korzystasz z lokalnego rozwiązania do uwierzytelniania wieloskładnikowego, aby wstrzyknąć wyzwanie drugiego czynnika do przepływu uwierzytelniania użytkownika. Ta konfiguracja nie działa już w przypadku scenariuszy uwierzytelniania usługi Azure AD po przeprowadzeniu konwersji tej domeny z federacyjnego na uwierzytelnianie zarządzane. Po wyłączeniu Federacji można nawiązać relację z Federacją w Federacji lokalnej, co obejmuje lokalne karty usługi MFA. 
>
> Zamiast tego należy użyć usługi Azure AD Multi-Factor Authentication opartej na chmurze, aby wykonać tę samą funkcję. Przed kontynuowaniem Oceń wymagania dotyczące uwierzytelniania wieloskładnikowego. Przed przekonwertowaniem domen upewnij się, że rozumiesz, jak korzystać z usługi Azure AD Multi-Factor Authentication, implikacji licencjonowania i procesu rejestracji użytkownika.

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
| Aby zablokować wcześniejsze wersje klientów uwierzytelniania, należy użyć AD FS.| Należy rozważyć zastępowanie AD FS formantów blokujących wcześniejsze wersje klientów uwierzytelniania przy użyciu kombinacji [kontroli dostępu warunkowego](../conditional-access/concept-conditional-access-conditions.md) i [reguł dostępu klienta usługi Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| Użytkownik wymaga od użytkowników przeprowadzenia uwierzytelniania wieloskładnikowego w przypadku lokalnego rozwiązania do obsługi serwera usługi wieloskładnikowego, gdy użytkownicy uwierzytelniają się do AD FS.| W zarządzanej domenie tożsamości nie można wstrzyknąć wyzwania usługi uwierzytelnianie wieloskładnikowe za pośrednictwem lokalnego rozwiązania do uwierzytelniania wieloskładnikowego do przepływu uwierzytelniania. Można jednak użyć usługi Azure AD Multi-Factor Authentication do uwierzytelniania wieloskładnikowego po przeprowadzeniu konwersji domeny.<br /><br /> Jeśli użytkownicy nie korzystają obecnie z usługi Azure AD Multi-Factor Authentication, wymagany jest krok rejestracji użytkownika jednorazowej. Należy przygotować się do planowanej rejestracji i przekazać ją do użytkowników. |
| Obecnie używasz zasad kontroli dostępu (reguł autoryzacji) w AD FS, aby kontrolować dostęp do Microsoft 365.| Rozważ zastąpienie zasad zasadami [dostępu warunkowego](../conditional-access/overview.md) usługi Azure AD i [regułami dostępu klienta usługi Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### <a name="common-ad-fs-customizations"></a>Typowe dostosowania AD FS

W tej sekcji opisano typowe dostosowania AD FS.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork

AD FS wystawia żądania **InsideCorporateNetwork** , jeśli uwierzytelniany użytkownik jest w sieci firmowej. To zastrzeżenie można następnie przesłać do usługi Azure AD. To zastrzeżenie służy do obejścia uwierzytelniania wieloskładnikowego na podstawie lokalizacji sieciowej użytkownika. Aby dowiedzieć się, jak ustalić, czy ta funkcja jest obecnie włączona w AD FS, zobacz [Zaufane adresy IP dla użytkowników federacyjnych](../authentication/howto-mfa-adfs.md).

**InsideCorporateNetwork** oświadczenia nie jest dostępne po przekonwertowaniu domen na synchronizację skrótów haseł. Aby zastąpić tę funkcję, można użyć [nazwanych lokalizacji w usłudze Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) .

Po skonfigurowaniu nazwanych lokalizacji należy zaktualizować wszystkie zasady dostępu warunkowego, które zostały skonfigurowane w taki sposób, aby uwzględniać lub wykluczać Sieć **wszystkie Zaufane lokalizacje** lub **Zaufane adresy IP usługi MFA** w celu odzwierciedlenia nowych nazwanych lokalizacji.

Aby uzyskać więcej informacji o warunku **lokalizacji** w dostępie warunkowym, zobacz [Active Directory lokalizacji dostępu warunkowego](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrydowe urządzenia dołączone do usługi Azure AD

Po dołączeniu urządzenia do usługi Azure AD można utworzyć reguły dostępu warunkowego, które wymuszają, aby urządzenia spełniały standardy dostępu do zabezpieczeń i zgodności. Ponadto użytkownicy mogą logować się do urządzenia przy użyciu konta służbowego lub szkolnego, a nie konta osobistego. W przypadku korzystania z hybrydowych urządzeń przyłączonych do usługi Azure AD można przyłączyć Active Directory urządzenia przyłączone do domeny do usługi Azure AD. Środowisko federacyjne mogło zostać skonfigurowane do korzystania z tej funkcji.

Aby zapewnić, że sprzężenie hybrydowe będzie nadal działało w przypadku urządzeń przyłączonych do domeny po przekonwertowaniu domen na synchronizację skrótów haseł dla klientów z systemem Windows 10, należy użyć opcji Azure AD Connect urządzenia do synchronizowania kont komputerów Active Directory z usługą Azure AD. 

W przypadku kont komputerów z systemem Windows 8 i Windows 7 sprzężenie hybrydowe używa bezproblemowego logowania jednokrotnego do zarejestrowania komputera w usłudze Azure AD. Nie musisz synchronizować kont komputerów z systemem Windows 8 i Windows 7, takich jak w przypadku urządzeń z systemem Windows 10. Należy jednak wdrożyć zaktualizowany plik workplacejoin.exe (za pośrednictwem pliku msi) na klientach z systemami Windows 8 i Windows 7, aby mogli zarejestrować się przy użyciu bezproblemowego logowania jednokrotnego. [Pobierz plik msi](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie hybrydowych urządzeń przyłączonych do usługi Azure AD](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Znakowanie

Jeśli Twoja organizacja [dostosował AD FS strony logowania](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) , aby wyświetlić informacje, które są bardziej przydatne dla organizacji, rozważ wprowadzenie podobnych [dostosowań na stronie logowania do usługi Azure AD](../fundamentals/customize-branding.md).

Chociaż podobne dostosowania są dostępne, niektóre zmiany wizualne na stronach logowania powinny być oczekiwane po konwersji. Możesz chcieć podać informacje o oczekiwanych zmianach w komunikacji z użytkownikami.

> [!NOTE]
> Znakowanie organizacji jest dostępne tylko w przypadku zakupienia licencji Premium lub Basic dla Azure Active Directory lub w przypadku posiadania licencji Microsoft 365.

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

Po wdrożeniu zarówno synchronizacji skrótów haseł, jak i bezproblemowego logowania jednokrotnego środowisko logowania użytkownika umożliwia dostęp do Microsoft 365 i innych zasobów, które są uwierzytelniane za pomocą zmian usługi Azure AD. Użytkownicy spoza sieci zobaczą tylko stronę logowania usługi Azure AD. Ci użytkownicy nie są przekierowywani do strony opartej na formularzach, która jest prezentowana przez zewnętrzne serwery proxy aplikacji sieci Web.

Uwzględnij następujące elementy w strategii komunikacji:

* Powiadamiaj użytkowników o nadchodzących i wydanych funkcjach przy użyciu:
   * Wiadomości e-mail i innych wewnętrznych kanałów komunikacyjnych.
   * Wizualizacje, takie jak plakaty.
   * Executive, na żywo lub w innej komunikacji.
* Określ, kto będzie w trakcie dostosowywania komunikacji i kto wyśle wiadomość.

## <a name="implement-your-solution"></a>Implementowanie rozwiązania

Twoje rozwiązanie zostało zaplanowane. Teraz można go zaimplementować. Implementacja obejmuje następujące składniki:

* Włączanie synchronizacji skrótów haseł.
* Przygotowanie do bezproblemowego logowania jednokrotnego.
* Zmiana metody logowania na synchronizację skrótów haseł i włączenie bezproblemowego logowania jednokrotnego.

### <a name="step-1-enable-password-hash-synchronization"></a>Krok 1. Włączanie synchronizacji skrótów haseł

Pierwszym krokiem wdrożenia tego rozwiązania jest włączenie synchronizacji skrótów haseł przy użyciu kreatora Azure AD Connect. Synchronizacja skrótów haseł jest opcjonalną funkcją, którą można włączyć w środowiskach korzystających z Federacji. Nie ma to wpływu na przepływ uwierzytelniania. W takim przypadku Azure AD Connect rozpocznie synchronizowanie skrótów haseł bez wpływu na użytkowników logujących się przy użyciu Federacji.

Z tego powodu zaleca się wykonanie tego kroku jako zadania przygotowania przed zmianą metody logowania w domenie. Następnie uzyskasz dużo czasu, aby sprawdzić, czy synchronizacja skrótów haseł działa prawidłowo.

Aby włączyć synchronizację skrótów haseł:

1. Na serwerze Azure AD Connect Otwórz Kreatora Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.
2. Wybierz opcję **Dostosuj opcje synchronizacji**, a następnie wybierz przycisk **dalej**.
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na stronie **łączenie katalogów** wybierz pozycję **dalej**.
5. Na stronie **filtrowanie domeny i jednostki organizacyjnej** kliknij przycisk **dalej**.
6. Na stronie **funkcje opcjonalne** wybierz pozycję **Synchronizacja haseł**, a następnie wybierz przycisk **dalej**.
 
   ![Zrzut ekranu przedstawiający opcję synchronizacji haseł wybraną na stronie funkcje opcjonalne](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Na pozostałych stronach wybierz pozycję **dalej** . Na ostatniej stronie wybierz pozycję **Konfiguruj**.
8. Azure AD Connect zaczyna synchronizować skróty haseł podczas następnej synchronizacji.

Po włączeniu synchronizacji skrótów haseł dla wszystkich użytkowników w zakresie synchronizacji Azure AD Connect są ponownie tworzone skróty i zapisywane w usłudze Azure AD. W zależności od liczby użytkowników ta operacja może potrwać kilka minut.

W celu planowania należy oszacować, że około 20 000 użytkowników jest przetwarzanych w ciągu 1 godziny.

Aby sprawdzić, czy synchronizacja skrótów haseł działa prawidłowo, wykonaj zadanie **rozwiązywania problemów** w Kreatorze Azure AD Connect:

1. Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji Uruchom jako administrator.
2. Uruchom system `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted` .
3. Uruchom Kreatora Azure AD Connect.
4. Przejdź do strony **dodatkowe zadania** , wybierz pozycję **Rozwiązywanie problemów**, a następnie wybierz przycisk **dalej**.
5. Na stronie **Rozwiązywanie problemów** wybierz pozycję **Uruchom** , aby uruchomić menu Rozwiązywanie problemów w programie PowerShell.
6. W menu głównym wybierz pozycję **Rozwiązywanie problemów synchronizacja skrótów haseł**.
7. W podmenu wybierz opcję **Synchronizacja skrótów haseł nie działa wcale**.

Rozwiązywanie problemów można znaleźć w temacie [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją Azure AD Connect](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Krok 2: przygotowanie do bezproblemowego logowania jednokrotnego

Aby urządzenia używały bezproblemowego logowania jednokrotnego, należy dodać adres URL usługi Azure AD do ustawień strefy intranetowej użytkowników przy użyciu zasad grupy w Active Directory.

Domyślnie przeglądarki sieci Web automatycznie obliczają poprawną strefę, Internet lub intranet, z adresu URL. Na przykład **http: \/ \/ contoso/** mapuje do strefy intranet i **http: \/ \/ intranet.contoso.com** mapuje do strefy Internet (ponieważ adres URL zawiera kropkę). Przeglądarki wysyłają bilety Kerberos do punktu końcowego w chmurze, takiego jak adres URL usługi Azure AD, tylko wtedy, gdy jawnie dodasz adres URL do strefy intranetowej przeglądarki.

Wykonaj kroki, aby [wdrożyć](./how-to-connect-sso-quick-start.md) wymagane zmiany na urządzeniach.

> [!IMPORTANT]
> Wprowadzenie tej zmiany nie modyfikuje sposobu logowania użytkowników do usługi Azure AD. Ważne jest jednak, aby zastosować tę konfigurację do wszystkich urządzeń przed kontynuowaniem. Użytkownicy logujący się na urządzeniach, które nie otrzymały tej konfiguracji po prostu, muszą wprowadzić nazwę użytkownika i hasło, aby zalogować się do usługi Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Krok 3. zmiana metody logowania na synchronizację skrótów haseł i włączenie bezproblemowego logowania jednokrotnego

Dostępne są dwie opcje zmiany metody logowania na synchronizację skrótów haseł i włączenie bezproblemowego logowania jednokrotnego.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opcja A: przełączenie z Federacji do synchronizacji skrótów haseł przy użyciu Azure AD Connect

Użyj tej metody, jeśli początkowo skonfigurujesz środowisko AD FS przy użyciu Azure AD Connect. Nie można użyć tej metody, jeśli *nie* skonfigurowano pierwotnie środowiska AD FS przy użyciu Azure AD Connect.

Najpierw Zmień metodę logowania:

1. Na serwerze Azure AD Connect Otwórz Kreatora Azure AD Connect.
2. Wybierz pozycję **Zmień Logowanie użytkownika**, a następnie wybierz przycisk **dalej**. 

   ![Zrzut ekranu przedstawiający opcję zmiany logowania użytkownika na stronie dodatkowe zadania](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na stronie **logowania użytkownika** wybierz **przycisk Synchronizacja skrótów haseł**. Upewnij się, że pole wyboru nie **Konwertuj kont użytkowników** jest zaznaczone. Opcja jest przestarzała. Wybierz pozycję **Włącz logowanie jednokrotne**, a następnie wybierz pozycję **dalej**.

   ![Zrzut ekranu przedstawiający stronę Włączanie logowania jednokrotnego](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Począwszy od Azure AD Connect wersja 1.1.880.0, pole wyboru **bezproblemowego logowania jednokrotnego** jest domyślnie zaznaczone.

   > [!IMPORTANT]
   > Można bezpiecznie zignorować ostrzeżenia wskazujące, że konwersja użytkownika i pełna synchronizacja skrótów haseł są wymagane do konwersji z Federacji na uwierzytelnianie w chmurze. Należy zauważyć, że te kroki nie są już wymagane. Jeśli nadal widzisz te ostrzeżenia, upewnij się, że korzystasz z najnowszej wersji programu Azure AD Connect i że używasz najnowszej wersji tego przewodnika. Aby uzyskać więcej informacji, zobacz sekcję [aktualizacja Azure AD Connect](#update-azure-ad-connect).

5. Na stronie **Włącz logowanie jednokrotne** wprowadź poświadczenia konta administratora domeny, a następnie wybierz przycisk **dalej**.

   ![Zrzut ekranu przedstawiający stronę Włączanie logowania jednokrotnego, na której można wprowadzić poświadczenia konta administratora domeny.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Aby umożliwić bezproblemowe logowanie jednokrotne, wymagane są poświadczenia konta administratora domeny. Proces wykonuje następujące akcje, które wymagają podniesionych uprawnień. Poświadczenia konta administratora domeny nie są przechowywane w Azure AD Connect ani w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko w celu włączenia tej funkcji. Poświadczenia są odrzucane po pomyślnym zakończeniu procesu.
   >
   > 1. Konto komputera o nazwie AZUREADSSOACC (reprezentujące usługę Azure AD) jest tworzone w lokalnym wystąpieniu Active Directory.
   > 2. Klucz odszyfrowywania Kerberos konta komputera jest bezpiecznie współużytkowany z usługą Azure AD.
   > 3. Dwie główne nazwy usługi (SPN) protokołu Kerberos są tworzone w celu reprezentowania dwóch adresów URL, które są używane podczas logowania do usługi Azure AD.

6. Na stronie **gotowy do skonfigurowania** upewnij się, że jest zaznaczone pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji** . Następnie wybierz pozycję **Konfiguruj**.

      ![Zrzut ekranu przedstawiający stronę gotowy do konfiguracji](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > W tym momencie wszystkie domeny federacyjne zmienią się na uwierzytelnianie zarządzane. Synchronizacja skrótów haseł jest nową metodą uwierzytelniania.

7. W portalu usługi Azure AD wybierz pozycję **Azure Active Directory**  >  **Azure AD Connect**.
8. Sprawdź te ustawienia:
   * Wartość **federacyjna** została **wyłączona**.
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **włączone**.
   * **Synchronizacja haseł** jest ustawiona na wartość **włączone**.<br /> 

   ![Zrzut ekranu pokazujący ustawienia w sekcji Logowanie użytkownika w portalu usługi Azure AD.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Przejdź do [testowania i następnych kroków](#testing-and-next-steps).

   > [!IMPORTANT]
   > Pomiń opcję sekcja **B: przełączenie z Federacji do synchronizacji skrótów haseł przy użyciu Azure AD Connect i programu PowerShell**. Kroki opisane w tej sekcji nie mają zastosowania w przypadku wybrania opcji A w celu zmiany metody logowania na synchronizację skrótów haseł i włączenia bezproblemowego logowania jednokrotnego.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opcja B: przełączanie z Federacji na synchronizację skrótów haseł przy użyciu Azure AD Connect i programu PowerShell

Użyj tej opcji, jeśli nie skonfigurowano wstępnie domen federacyjnych przy użyciu Azure AD Connect. W trakcie tego procesu włączasz bezproblemowe logowanie jednokrotne i przełączasz domeny z federacyjnego do zarządzanego.

1. Na serwerze Azure AD Connect Otwórz Kreatora Azure AD Connect.
2. Wybierz pozycję **Zmień Logowanie użytkownika**, a następnie wybierz przycisk **dalej**.
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło dla konta administratora globalnego.
4. Na stronie **logowania użytkownika** wybierz przycisk **Synchronizacja skrótów haseł** . Wybierz pozycję **Włącz logowanie jednokrotne**, a następnie wybierz pozycję **dalej**.

   Przed włączeniem synchronizacji skrótów haseł: ![ zrzut ekranu przedstawiający opcję nie Konfiguruj na stronie logowania użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Po włączeniu synchronizacji skrótów haseł: ![ zrzut ekranu przedstawiający nowe opcje na stronie logowania użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Począwszy od Azure AD Connect wersja 1.1.880.0, pole wyboru **bezproblemowego logowania jednokrotnego** jest domyślnie zaznaczone.

5. Na stronie **Włącz logowanie jednokrotne** wprowadź poświadczenia dla konta administratora domeny, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > Aby umożliwić bezproblemowe logowanie jednokrotne, wymagane są poświadczenia konta administratora domeny. Proces wykonuje następujące akcje, które wymagają podniesionych uprawnień. Poświadczenia konta administratora domeny nie są przechowywane w Azure AD Connect ani w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko w celu włączenia tej funkcji. Poświadczenia są odrzucane po pomyślnym zakończeniu procesu.
   >
   > 1. Konto komputera o nazwie AZUREADSSOACC (reprezentujące usługę Azure AD) jest tworzone w lokalnym wystąpieniu Active Directory.
   > 2. Klucz odszyfrowywania Kerberos konta komputera jest bezpiecznie współużytkowany z usługą Azure AD.
   > 3. Dwie główne nazwy usługi (SPN) protokołu Kerberos są tworzone w celu reprezentowania dwóch adresów URL, które są używane podczas logowania do usługi Azure AD.

6. Na stronie **gotowy do skonfigurowania** upewnij się, że jest zaznaczone pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji** . Następnie wybierz pozycję **Konfiguruj**.

   ![Zrzut ekranu pokazujący przycisk Konfiguruj na stronie gotowy do konfiguracji](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Po wybraniu przycisku **Konfiguruj** bezproblemowo logowanie jest konfigurowane zgodnie z opisem w poprzednim kroku. Konfiguracja synchronizacji skrótów haseł nie jest modyfikowana, ponieważ została włączona wcześniej.

   > [!IMPORTANT]
   > W tej chwili nie wprowadzono żadnych zmian w sposobie logowania użytkowników.

7. Sprawdź następujące ustawienia w portalu usługi Azure AD:
   * **Federacja** jest ustawiona na wartość **Enabled (włączone**).
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **włączone**.
   * **Synchronizacja haseł** jest ustawiona na wartość **włączone**.

   ![Zrzut ekranu pokazujący ustawienia w sekcji logowania użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konwertuj domeny z federacyjnego na zarządzane

W tym momencie Federacja jest nadal włączona i działa dla Twoich domen. Aby kontynuować wdrażanie, należy przekonwertować wszystkie domeny z federacyjnego na zarządzane, aby wymusić uwierzytelnianie użytkowników za pośrednictwem synchronizacji skrótów haseł.

> [!IMPORTANT]
> Nie musisz konwertować wszystkich domen w tym samym czasie. Możesz zacząć od domeny testowej w dzierżawie produkcyjnej lub rozpocząć pracę z domeną o najmniejszej liczbie użytkowników.

Ukończ konwersję przy użyciu modułu Azure AD PowerShell:

1. W programie PowerShell Zaloguj się do usługi Azure AD przy użyciu konta administratora globalnego.
2. Aby skonwertować pierwszą domenę, uruchom następujące polecenie:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. W portalu usługi Azure AD wybierz pozycję **Azure Active Directory**  >  **Azure AD Connect**.
4. Sprawdź, czy domena została przekonwertowana na zarządzane, uruchamiając następujące polecenie:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testowanie i następne kroki

Wykonaj następujące zadania, aby zweryfikować synchronizację skrótów haseł i zakończyć proces konwersji.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testowanie uwierzytelniania przy użyciu funkcji synchronizacji skrótów haseł 

Gdy dzierżawca używa tożsamości federacyjnej, użytkownicy zostali przekierowani ze strony logowania usługi Azure AD do środowiska AD FS. Teraz, gdy dzierżawa jest skonfigurowana do używania synchronizacji skrótów haseł zamiast uwierzytelniania federacyjnego, użytkownicy nie są przekierowywani do AD FS. Zamiast tego użytkownicy logują się bezpośrednio na stronie logowania usługi Azure AD.

Aby przetestować synchronizację skrótów haseł:

1. Otwórz program Internet Explorer w trybie InPrivate, aby bezproblemowe logowanie jednokrotne nie zalogować Cię automatycznie.
2. Przejdź do strony logowania do programu Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Wprowadź nazwę UPN użytkownika, a następnie wybierz przycisk **dalej**. Upewnij się, że wprowadzasz nazwę UPN użytkownika hybrydowego, który został zsynchronizowany z wystąpienia lokalnego Active Directory i kto wcześniej użył uwierzytelniania federacyjnego. Strona, na której zostanie wprowadzona nazwa użytkownika i hasło:

   ![Zrzut ekranu przedstawiający stronę logowania, w której wprowadzasz nazwę użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Zrzut ekranu przedstawiający stronę logowania, w której można wprowadzić hasło](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Po wprowadzeniu hasła i wybraniu opcji **Zaloguj** nastąpi przekierowanie do portalu Office 365.

   ![Zrzut ekranu przedstawiający Portal Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Bezproblemowe testowanie logowania jednokrotnego

1. Zaloguj się na komputerze przyłączonym do domeny, który jest połączony z siecią firmową.
2. W programie Internet Explorer lub Chrome przejdź do jednego z następujących adresów URL (Zastąp ciąg "contoso" domeną):

   * https: \/ \/ myapps.Microsoft.com/contoso.com
   * https: \/ \/ myapps.Microsoft.com/contoso.onmicrosoft.com

   Użytkownik zostanie krótko przekierowany do strony logowania usługi Azure AD, która wyświetla komunikat "próba zalogowania". Użytkownik nie jest monitowany o podanie nazwy użytkownika lub hasła.<br />

   ![Zrzut ekranu przedstawiający stronę logowania i wiadomość usługi Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. Użytkownik zostanie przekierowany i pomyślnie zalogował się do panelu dostępu:

   > [!NOTE]
   > Bezproblemowe logowanie jednokrotne działa na Microsoft 365 usługach, które obsługują wskazówkę domeny (na przykład myapps.microsoft.com/contoso.com). Obecnie portal Microsoft 365 (portal.office.com) nie obsługuje podpowiedzi do domeny. Użytkownicy muszą wprowadzić nazwę UPN. Po wprowadzeniu nazwy UPN bezproblemowe logowanie jednokrotne pobiera bilet protokołu Kerberos w imieniu użytkownika. Użytkownik jest zalogowany bez wprowadzania hasła.

   > [!TIP]
   > Rozważ wdrożenie funkcji [dołączania hybrydowego usługi Azure AD w systemie Windows 10](../devices/overview.md) w celu usprawnienia logowania jednokrotnego.

### <a name="remove-the-relying-party-trust"></a>Usuń relację zaufania jednostki uzależnionej

Po sprawdzeniu, czy wszyscy użytkownicy i klienci pomyślnie uwierzytelniają się za pośrednictwem usługi Azure AD, można bezpiecznie usunąć Microsoft 365 zaufania jednostki uzależnionej.

Jeśli nie używasz AD FS do innych celów (to oznacza, że w przypadku innych relacji zaufania jednostek uzależnionych), możesz bezpiecznie zlikwidować AD FS na tym etapie.

### <a name="rollback"></a>Wycofywanie

Jeśli odkryjesz główny problem i nie można go szybko rozwiązać, możesz zdecydować się na wycofanie rozwiązania do Federacji.

Zapoznaj się z dokumentacją dotyczącą projektu federacyjnego i wdrożenia, aby poznać szczegóły dotyczące określonego wdrożenia. Proces ten powinien dotyczyć następujących zadań:

* Przekonwertuj domeny zarządzane na uwierzytelnianie federacyjne przy użyciu polecenia cmdlet **Convert-MSOLDomaintoFederated** .
* W razie potrzeby skonfiguruj dodatkowe reguły oświadczeń.

### <a name="sync-userprincipalname-updates"></a>Synchronizuj aktualizacje userPrincipalName

W przeszłości aktualizacje atrybutu **userPrincipalName** , który używa usługi synchronizacji ze środowiska lokalnego, są blokowane, chyba że oba te warunki są spełnione:

* Użytkownik należy do zarządzanej domeny tożsamości (innej niż federacyjna).
* Użytkownikowi nie przypisano licencji.

Aby dowiedzieć się, jak sprawdzić lub włączyć tę funkcję, zobacz [synchronizowanie aktualizacji userPrincipalName](./how-to-connect-syncservice-features.md).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Zespół pomocy technicznej powinien zrozumieć, jak rozwiązywać problemy z uwierzytelnianiem występujące podczas lub po zmianie z Federacji na zarządzaną. Skorzystaj z następującej dokumentacji dotyczącej rozwiązywania problemów, aby pomóc zespołowi pomocy technicznej zapoznać się z typowymi krokami rozwiązywania problemów oraz odpowiednimi akcjami, które mogą pomóc w odizolowaniu i rozwiązaniu problemu.

[Rozwiązywanie problemów z synchronizacją skrótów haseł Azure Active Directory](./tshoot-connect-password-hash-synchronization.md)

[Rozwiązywanie problemów Azure Active Directory bezproblemowe logowanie jednokrotne](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Przetaczanie bezproblemowego klucza odszyfrowywania Kerberos logowania jednokrotnego

Ważne jest często przekazanie klucza odszyfrowywania Kerberos konta komputera AZUREADSSOACC (co reprezentuje usługę Azure AD). Konto komputera AZUREADSSOACC jest tworzone w lokalnym lesie Active Directory. Zdecydowanie zalecamy przeprowadzenie klucza odszyfrowywania Kerberos co najmniej co 30 dni, aby dostosować się do sposobu, w jaki Active Directory Członkowie domeny przesyłają zmiany hasła. Brak skojarzonego urządzenia dołączonego do obiektu konta komputera AZUREADSSOACC, dlatego należy ręcznie wykonać przerzucanie.

Zainicjuj Przerzucanie bezproblemowego klucza odszyfrowywania Kerberos protokołu SSO na serwerze lokalnym, na którym działa Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Jak mogę przejęcia klucza odszyfrowywania Kerberos konta komputera AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [pojęć związanych z projektowaniem Azure AD Connect](plan-connect-design-concepts.md).
* Wybierz odpowiednie [uwierzytelnianie](./choose-ad-authn.md).
* Poznaj [obsługiwane topologie](plan-connect-design-concepts.md).
