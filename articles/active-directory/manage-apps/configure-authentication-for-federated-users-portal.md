---
title: Konfigurowanie autoprzyspieszania logowania przy użyciu funkcji odnajdowania obszaru macierzystego
description: Informacje o konfigurowaniu zasad odnajdywania obszaru macierzystego na potrzeby uwierzytelniania Azure Active Directory dla użytkowników federacyjnych, w tym funkcji autodostrajania i wskazówek dotyczących domen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: kenwith
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ed101282a69120162d6e3b526693c0a83df45b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607113"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurowanie zachowania Azure Active Directory logowania dla aplikacji przy użyciu zasad odnajdywania obszaru głównego

Ten artykuł zawiera wprowadzenie do konfigurowania Azure Active Directory zachowanie uwierzytelniania dla użytkowników federacyjnych przy użyciu zasad odnajdowania obszaru macierzystego (HRD).  Obejmuje użycie autoprzyspieszania, aby pominąć ekran wprowadzania nazwy użytkownika i automatycznie przekazywać użytkowników do punktów końcowych logowania federacyjnego.  Firma Microsoft nie zaleca konfigurowania autoprzyspieszania, ponieważ może to uniemożliwić korzystanie z silniejszych metod uwierzytelniania, takich jak FIDO, i utrudnia współpracę.

## <a name="home-realm-discovery"></a>Odnajdowanie obszaru głównego

Odnajdowanie obszaru macierzystego (HRD) to proces, który umożliwia Azure Active Directory (Azure AD) określenie dostawcy tożsamości ("dostawcy tożsamości"), którego użytkownik musi uwierzytelnić w czasie logowania.  Gdy użytkownik loguje się do dzierżawy usługi Azure AD w celu uzyskania dostępu do zasobu lub do typowej strony logowania usługi Azure AD, wpisz nazwę użytkownika (UPN). Usługa Azure AD używa tego programu do wykrywania miejsca, w którym użytkownik musi się zalogować.

Użytkownik zostanie przełączony do jednego z następujących dostawców tożsamości w celu uwierzytelnienia:

- Dzierżawa w domu użytkownika (może być tym samym dzierżawcą, który jest zasobem, do którego użytkownik próbuje uzyskać dostęp).

- konto Microsoft.  Użytkownik jest gościem w dzierżawie zasobu, który korzysta z konta odbiorcy na potrzeby uwierzytelniania.

- Lokalny dostawca tożsamości, taki jak Active Directory Federation Services (AD FS).

- Inny dostawca tożsamości, który jest federacyjny z dzierżawą usługi Azure AD.

## <a name="auto-acceleration"></a>Przyspieszenie autodostrajania

Niektóre organizacje konfigurują domeny w dzierżawie Azure Active Directory sfederować z inną dostawcy tożsamości, na przykład AD FS do uwierzytelniania użytkowników.  

Gdy użytkownik loguje się do aplikacji, najpierw jest wyświetlana strona logowania usługi Azure AD. Po wpisaniu nazwy UPN, jeśli znajdują się w domenie federacyjnej, są następnie podejmowane na stronie logowania dostawcy tożsamości obsługującej tę domenę. W pewnych okolicznościach Administratorzy mogą chcieć skierować użytkowników do strony logowania, gdy logują się do określonych aplikacji.

W związku z tym użytkownicy mogą pominąć początkową stronę Azure Active Directory. Ten proces jest nazywany "logowaniem autodostrajania".

W przypadkach, gdy dzierżawca jest federacyjnym innym dostawcy tożsamości do logowania, funkcja autoprzyspieszania sprawia, że logowanie użytkownika jest bardziej usprawnione.  Można skonfigurować przyspieszenie autodostrajania dla poszczególnych aplikacji.

>[!NOTE]
>W przypadku skonfigurowania aplikacji do autoprzyspieszania użytkownicy nie mogą używać poświadczeń zarządzanych (takich jak FIDO), a użytkownicy-Goście nie mogą się zalogować. W przypadku podjęcia przez użytkownika prostego dostawcy tożsamości na potrzeby uwierzytelniania nie istnieje sposób, aby można było wrócić do strony logowania Azure Active Directory. Użytkownicy-Goście, którzy muszą być kierowani do innych dzierżawców lub dostawcy tożsamości zewnętrznych, takich jak konto Microsoft, nie mogą zalogować się do tej aplikacji, ponieważ pomijają krok odnajdowania obszaru macierzystego.  

Istnieją trzy sposoby sterowania autoprzyspieszaniem do dostawcy tożsamości federacyjnego:

- Użyj wskazówki dotyczącej [domeny](#domain-hints) w przypadku żądań uwierzytelniania dla aplikacji.
- Skonfiguruj zasady odnajdywania obszaru macierzystego, aby [wymusić przyspieszenie autodostrajania](#home-realm-discovery-policy-for-auto-acceleration).
- Skonfiguruj zasady odnajdywania obszaru macierzystego, aby [ignorować wskazówki dotyczące domen](prevent-domain-hints-with-home-realm-discovery.md) z określonych aplikacji lub dla określonych domen.

### <a name="domain-hints"></a>Wskazówki dotyczące domeny

Wskazówki dotyczące domen to dyrektywy, które są zawarte w żądaniu uwierzytelniania z aplikacji. Mogą one służyć do przyspieszenia użytkownika do swojej strony logowania dostawcy tożsamości. Mogą też być używane przez aplikację wielodostępną, aby przyspieszyć użytkownika bezpośrednio do strony logowania do usługi Azure AD dla swojej dzierżawy.  

Na przykład aplikacja "largeapp.com" może umożliwić klientom dostęp do aplikacji przy użyciu niestandardowego adresu URL "contoso.largeapp.com". Aplikacja może również zawierać wskazówkę domeny do contoso.com w żądaniu uwierzytelniania.

Składnia wskazówki domeny różni się w zależności od używanego protokołu i zazwyczaj jest konfigurowana w aplikacji.

**WS-Federation**: WH = contoso. com w ciągu zapytania.

**SAML**: żądanie UWIERZYTELNIENIa SAML zawierające wskazówkę domeny lub ciąg zapytania WH = contoso. com.

**Otwórz identyfikator Connect**: ciąg zapytania domain_hint = contoso. com.

Domyślnie usługa Azure AD próbuje przekierować logowanie do dostawcy tożsamości, który jest skonfigurowany dla domeny, jeśli są spełnione **następujące warunki:**

- Wskazówka domeny jest uwzględniona w żądaniu uwierzytelniania z aplikacji **i**
- Dzierżawca jest federacyjny z tą domeną.

Jeśli Wskazówka domeny nie odwołuje się do zweryfikowanej domeny federacyjnej, zostanie zignorowana.

Aby uzyskać więcej informacji na temat autoprzyspieszania przy użyciu wskazówek dotyczących domeny, które są obsługiwane przez Azure Active Directory, zobacz [blog Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Jeśli w żądaniu uwierzytelniania uwzględniona jest Wskazówka dotycząca domeny i [powinna być przestrzegana](#home-realm-discovery-policy-to-prevent-auto-acceleration), jego obecność zastępuje funkcję autoprzyspieszania, która jest ustawiana dla aplikacji w zasadach HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Zasady odnajdywania obszaru macierzystego dla autoprzyspieszania

Niektóre aplikacje nie umożliwiają konfigurowania żądania uwierzytelniania, które emitują. W takich przypadkach nie jest możliwe użycie wskazówek domeny w celu sterowania autoprzyspieszaniem. Funkcję autoprzyspieszania można skonfigurować za pomocą zasad odnajdywania obszaru głównego w celu osiągnięcia tego samego zachowania.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Zasady odnajdywania obszaru macierzystego, które uniemożliwiają przyspieszenie autoprzyspieszania

Niektóre aplikacje firmy Microsoft i SaaS automatycznie uwzględniają domain_hints (na przykład `https://outlook.com/contoso.com` w wyniku żądania logowania z `&domain_hint=contoso.com` dołączonymi), co może zakłócać wprowadzanie zarządzanych poświadczeń, takich jak Fido.  [Zasad odnajdywania obszaru macierzystego](/graph/api/resources/homeRealmDiscoveryPolicy) można użyć do ignorowania wskazówek dotyczących domen z określonych aplikacji lub dla określonych domen podczas wprowadzania poświadczeń zarządzanych.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Włącz bezpośrednią ROPC uwierzytelniania użytkowników federacyjnych dla starszych aplikacji

Najlepszym rozwiązaniem jest użycie bibliotek usługi AAD i interakcyjnego logowania do uwierzytelniania użytkowników. Biblioteki są obsługiwane przez federacyjne przepływy użytkowników.  Czasami starsze aplikacje, szczególnie te, które korzystają z ROPC, przesyłają nazwy użytkownika i hasła bezpośrednio do usługi Azure AD i nie są zapisywane w celu zrozumienia Federacji. Nie wykonują odnajdywania obszaru macierzystego i nie współpracują z prawidłowym federacyjnym punktem końcowym w celu uwierzytelnienia użytkownika. Jeśli zdecydujesz się na to, możesz użyć zasad HRDymi, aby włączyć określone starsze aplikacje, które przesyłają poświadczenia nazwy użytkownika/hasła przy użyciu ROPC Grant do uwierzytelniania bezpośrednio z Azure Active Directory. Należy włączyć synchronizację skrótów haseł.

> [!IMPORTANT]
> Włącz uwierzytelnianie bezpośrednie tylko wtedy, gdy jest włączona synchronizacja skrótów haseł i wiadomo, że jest to możliwe, aby uwierzytelnić tę aplikację bez żadnych zasad wdrożonych przez lokalne dostawcy tożsamości. W przypadku wyłączenia synchronizacji skrótów haseł lub wyłączenia synchronizacji katalogów przy użyciu programu AD Connect z dowolnego powodu należy usunąć te zasady, aby uniemożliwić bezpośrednie uwierzytelnianie przy użyciu przestarzałego skrótu hasła.

## <a name="set-hrd-policy"></a>Ustaw zasady HRD

Należy wykonać trzy kroki, aby ustawić zasady HRDymi w aplikacji na potrzeby federacyjnych lub bezpośrednich aplikacji opartych na chmurze:

1. Utwórz zasady HRD.

2. Znajdź nazwę główną usługi, do której chcesz dołączyć zasady.

3. Dołącz zasady do jednostki usługi.

Zasady zaczną obowiązywać tylko dla określonej aplikacji po dołączeniu ich do nazwy głównej usługi.

Tylko jedna zasada HRD może być aktywna w jednostce usługi jednocześnie.  

Za pomocą poleceń cmdlet programu PowerShell Azure Active Directory można tworzyć zasady HRD i zarządzać nimi.

Poniżej znajduje się przykładowa definicja zasad HRD:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

Typ zasad to "[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)".

**AccelerateToFederatedDomain** jest opcjonalny. Jeśli **AccelerateToFederatedDomain** ma wartość false, zasady nie mają wpływu na funkcję autoprzyspieszania. Jeśli **AccelerateToFederatedDomain** ma wartość true, a w dzierżawie jest tylko jedna zweryfikowana i federacyjna domena, użytkownicy zostaną przekierowani do federacyjnego dostawcy tożsamości w celu zalogowania się. Jeśli jest prawdziwe i istnieje więcej niż jedna zweryfikowana domena w dzierżawie, należy określić **PreferredDomain** .

**PreferredDomain** jest opcjonalny. **PreferredDomain** powinna wskazywać domenę, do której ma zostać przyspieszone przyspieszenie. Można go pominąć, jeśli dzierżawa ma tylko jedną domenę federacyjną.  Jeśli zostanie pominięta, a istnieje więcej niż jedna zweryfikowana domena federacyjna, zasady nie będą miały zastosowania.

 Jeśli **PreferredDomain** jest określony, musi pasować do zweryfikowanej, federacyjnej domeny dla dzierżawcy. Wszyscy użytkownicy aplikacji muszą mieć możliwość zalogowania się do tej domeny — Użytkownicy, którzy nie mogą zalogować się w domenie federacyjnej, zostaną zarejestrowani i nie będą mogli zakończyć logowania.

**AllowCloudPasswordValidation** jest opcjonalny. Jeśli **AllowCloudPasswordValidation** ma wartość true, aplikacja może uwierzytelniać użytkownika federacyjnego przez zaprezentowanie poświadczeń nazwy użytkownika/hasła bezpośrednio do punktu końcowego tokenu Azure Active Directory. Działa to tylko wtedy, gdy jest włączona synchronizacja skrótów haseł.

Ponadto istnieją dwie opcje HRD na poziomie dzierżawy, które nie są pokazane powyżej:

- **AlternateIdLogin** jest opcjonalny.  [Umożliwia użytkownikom logowanie się przy użyciu adresów e-mail zamiast nazw UPN](../authentication/howto-authentication-use-email-signin.md) na stronie logowania do usługi Azure AD.  Identyfikatory alternatywne polegają na tym, że użytkownik nie jest autoprzyspieszany do dostawcy tożsamości federacyjnego.

- **DomainHintPolicy** to opcjonalny obiekt złożony, który [ *zapobiega* wskazówkom domeny z autodostrajania użytkowników do domen federacyjnych](prevent-domain-hints-with-home-realm-discovery.md). To ustawienie dla dzierżawy służy do upewnienia się, że aplikacje, które wysyłają wskazówki dotyczące domen, nie uniemożliwiają użytkownikom logowania się przy użyciu poświadczeń zarządzanych przez chmurę.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorytet i Ocena zasad HRDymi

Zasady HRD można tworzyć, a następnie przypisywać do określonych organizacji i jednostek usługi. Oznacza to, że istnieje możliwość zastosowania wielu zasad do określonej aplikacji, więc usługa Azure AD musi zdecydować, która z nich ma pierwszeństwo. Zestaw reguł decyduje o tym, które zasady HRD (wiele zastosowanych) obowiązują:

- Jeśli w żądaniu uwierzytelniania jest obecna Wskazówka dotycząca domeny, sprawdzane są zasady HRDymi dla dzierżawy (zasady ustawione jako domyślne dzierżawy), aby sprawdzić, czy [wskazówki domeny powinny być ignorowane](prevent-domain-hints-with-home-realm-discovery.md). Jeśli są dozwolone wskazówki dotyczące domeny, stosowane jest zachowanie określone przez wskazówkę domeny.

- W przeciwnym razie, jeśli zasady są jawnie przypisane do nazwy głównej usługi, jest wymuszane.

- Jeśli nie ma żadnej wskazówki dotyczącej domeny, a żadne zasady nie są jawnie przypisane do nazwy głównej usługi, wymuszane są zasady, które są jawnie przypisane do organizacji nadrzędnej jednostki usługi.

- Jeśli nie ma żadnej wskazówki dotyczącej domeny i nie przypisano żadnych zasad do jednostki usługi lub organizacji, używane jest domyślne zachowanie HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Samouczek dotyczący ustawiania zasad HRDymi w aplikacji

Użyjemy poleceń cmdlet programu PowerShell usługi Azure AD, aby zapoznać się z kilkoma scenariuszami, w tym:

- Konfigurowanie zasad HRDymi w celu przeprowadzenia autoprzyspieszania dla aplikacji w dzierżawie z jedną domeną federacyjną.

- Konfigurowanie zasad HRDymi w celu przeprowadzenia autoprzyspieszania dla aplikacji w jednej z kilku domen, które są weryfikowane dla dzierżawy.

- Konfigurowanie zasad HRDymi, aby umożliwić starszej aplikacji wykonywanie uwierzytelniania przy użyciu nazwy użytkownika i hasła do Azure Active Directory dla użytkownika federacyjnego.

- Wyświetlanie listy aplikacji, dla których skonfigurowano zasady.

### <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach tworzysz, aktualizujesz, łączysz i usuwasz zasady dla podmiotów usługi aplikacji w usłudze Azure AD.

1. Aby rozpocząć, Pobierz najnowszą wersję zapoznawczą poleceń cmdlet programu PowerShell usługi Azure AD.

2. Po pobraniu poleceń cmdlet programu PowerShell dla usługi Azure AD Uruchom polecenie Połącz, aby zalogować się do usługi Azure AD przy użyciu konta administratora:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Uruchom następujące polecenie, aby wyświetlić wszystkie zasady w organizacji:

    ``` powershell
    Get-AzureADPolicy
    ```

Jeśli nic nie zostanie zwrócone, oznacza to, że nie masz żadnych zasad utworzonych w dzierżawie.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Przykład: Ustawianie zasad HRD dla aplikacji

W tym przykładzie utworzysz zasady, które są przypisywane do aplikacji:

- Funkcja autoprzyspiesza użytkowników do AD FS logowania, gdy logują się do aplikacji, gdy w dzierżawie jest pojedyncza domena.
- Funkcja autodostrajania użytkowników do AD FS logowania zawiera więcej niż jedną domenę federacyjną w dzierżawie.
- Umożliwia logowanie się nieinteraktywnej nazwy użytkownika/hasła bezpośrednio do Azure Active Directory dla użytkowników federacyjnych dla aplikacji, do których zasady są przypisane.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1. Tworzenie zasad HRDymi

Następujące zasady umożliwiają autoprzyspieszanie użytkowników do AD FS logowania, gdy logują się do aplikacji, gdy w dzierżawie jest pojedyncza domena.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Następujące zasady umożliwiają autoprzyspieszanie użytkowników do AD FS logowania w dzierżawie znajduje się więcej niż jedna domena federacyjna. Jeśli masz więcej niż jedną domenę federacyjną, która uwierzytelnia użytkowników dla aplikacji, musisz określić domenę do autoprzyspieszania.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Aby utworzyć zasady umożliwiające użytkownikom federacyjnym uwierzytelnianie przy Azure Active Directory użyciu nazwy użytkownika/hasła, należy uruchomić następujące polecenie:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Aby wyświetlić nowe zasady i uzyskać jego identyfikator **objectid**, uruchom następujące polecenie:

``` powershell
Get-AzureADPolicy
```

Aby zastosować zasady HRD po utworzeniu, można przypisać je do wielu nazw głównych usługi aplikacji.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2. zlokalizuj jednostkę usługi, do której chcesz przypisać zasady

Potrzebujesz **identyfikatora objectid** nazwy głównej usługi, do której chcesz przypisać zasady. Istnieje kilka sposobów znajdowania **identyfikatora** obiektu nazw podmiotów usługi.

Możesz użyć portalu lub można wysyłać zapytania do [Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta). Możesz również przejść do [narzędzia Eksplorator wykresu](https://developer.microsoft.com/graph/graph-explorer) i zalogować się do konta usługi Azure AD, aby zobaczyć wszystkie nazwy główne usługi w organizacji.

Ponieważ używasz programu PowerShell, możesz użyć następującego polecenia cmdlet, aby wyświetlić listę jednostek usługi i ich identyfikatorów.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3. przypisanie zasad do nazwy głównej usługi

Po utworzeniu **identyfikatora** obiektu nazwy głównej usługi aplikacji, dla której chcesz skonfigurować autoprzyspieszanie, uruchom następujące polecenie. To polecenie kojarzy zasady HRD utworzone w kroku 1 z jednostką usługi, która znajduje się w kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Możesz powtórzyć to polecenie dla każdej jednostki usługi, do której chcesz dodać zasady.

W przypadku, gdy aplikacja ma już przypisane zasady HomeRealmDiscovery, nie będzie można dodać drugiej.  W takim przypadku należy zmienić definicję zasad odnajdywania obszaru macierzystego przypisanych do aplikacji w celu dodania dodatkowych parametrów.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4. sprawdzenie, do których podmiotów usługi aplikacji są przypisane zasady HRD

Aby sprawdzić, które aplikacje mają skonfigurowane zasady HRD, użyj polecenia cmdlet **Get-AzureADPolicyAppliedObject** . Przekaż go identyfikator **objectid** zasad, które chcesz sprawdzić.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Krok 5. wszystko gotowe!

Wypróbuj aplikację, aby sprawdzić, czy nowe zasady działają.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Przykład: Utwórz listę aplikacji, dla których skonfigurowano zasady HRDymi

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1. Wyświetlanie listy wszystkich zasad, które zostały utworzone w organizacji

``` powershell
Get-AzureADPolicy
```

Zanotuj identyfikator **objectid** zasad, dla których chcesz wyświetlić listę przypisań.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2. Lista jednostek usługi, do których przypisane są zasady  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Przykład: usuwanie zasad HRD z aplikacji

#### <a name="step-1-get-the-objectid"></a>Krok 1. Uzyskiwanie identyfikatora ObjectID

Użyj poprzedniego przykładu, aby uzyskać identyfikator **objectid** zasad i nazwę główną usługi aplikacji, z której chcesz ją usunąć.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2. usuwanie przypisania zasad z jednostki usługi aplikacji  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3. sprawdzenie usunięcia przez wystawienie nazwy głównej usługi, do której przypisane są zasady

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat sposobu działania uwierzytelniania w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-vs-authorization.md).
- Aby uzyskać więcej informacji na temat logowania jednokrotnego użytkownika, zobacz Logowanie jednokrotne [do aplikacji w Azure Active Directory](what-is-single-sign-on.md).
- Odwiedź [platformę tożsamości firmy Microsoft](../develop/v2-overview.md) , aby zapoznać się z omówieniem całej zawartości związanej z deweloperem.