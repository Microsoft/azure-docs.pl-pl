---
title: Konfigurowanie automatycznego przyspieszania logowania przy użyciu funkcji odnajdywania home realm
description: Dowiedz się, jak skonfigurować zasady odnajdywania w domenie głównej na Azure Active Directory dla użytkowników federowanych, w tym automatyczne przyspieszanie i wskazówki dotyczące domeny.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af80979a4712f6d25d994835128f9d5d2205f42
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534737"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurowanie Azure Active Directory logowania dla aplikacji przy użyciu zasad odnajdywania home realm

Ten artykuł zawiera wprowadzenie do konfigurowania zachowania uwierzytelniania Azure Active Directory dla użytkowników federowanych przy użyciu zasad odnajdywania home realm discovery (HRD).  Obejmuje to korzystanie z automatycznego przyspieszania w celu pominięcia ekranu wprowadzania nazwy użytkownika i automatycznego przekazywania użytkowników do punktów końcowych logowania federowego.  Firma Microsoft nie zaleca już konfigurowania automatycznego przyspieszania, ponieważ może uniemożliwić stosowanie silniejszych metod uwierzytelniania, takich jak FIDO, i zakłóca współpracę.

## <a name="home-realm-discovery"></a>Odnajdowanie obszaru głównego

Odnajdywanie głównej maszyny wirtualnej (HRD, Home Realm Discovery) to proces, który umożliwia usłudze Azure Active Directory (Azure AD) określenie dostawcy tożsamości (IdP), za pomocą którego użytkownik musi się uwierzytelnić podczas logowania.  Gdy użytkownik loguje się do dzierżawy usługi Azure AD w celu uzyskania dostępu do zasobu lub do wspólnej strony logowania usługi Azure AD, wpisuje nazwę użytkownika (UPN). Usługa Azure AD używa jej do odnajdywania miejsca, w którym użytkownik musi się zalogować.

Użytkownik zostanie zabrany do jednego z następujących dostawców tożsamości w celu uwierzytelnienia:

- Dzierżawa główna użytkownika (może to być ta sama dzierżawa co zasób, do których użytkownik próbuje uzyskać dostęp).

- konto Microsoft.  Użytkownik jest gościem w dzierżawie zasobów, która używa konta użytkownika do uwierzytelniania.

- Dostawca tożsamości lokalnej, taki jak Active Directory Federation Services (AD FS).

- Inny dostawca tożsamości, który jest federowany z dzierżawą usługi Azure AD.

## <a name="auto-acceleration"></a>Automatyczne przyspieszanie

Niektóre organizacje konfigurują domeny w dzierżawie Azure Active Directory do federowania z innym identyfikatorem, na przykład AD FS uwierzytelniania użytkowników.  

Gdy użytkownik zaloguje się do aplikacji, najpierw zostanie im przedstawiona strona logowania usługi Azure AD. Po wpisaniu nazwy UPN, jeśli znajdują się w domenie federskiej, są one następnie przekierowyowane do strony logowania usługi tożsamości obsługującej tę domenę. W pewnych okolicznościach administratorzy mogą chcieć skierować użytkowników do strony logowania podczas logowania się do określonych aplikacji.

W związku z tym użytkownicy mogą pominąć początkową Azure Active Directory strony. Ten proces jest określany jako "automatyczne przyspieszanie logowania".

W przypadkach, gdy dzierżawa jest federowana z innym identyfikatorem tożsamości w celu logowania, automatyczne przyspieszanie sprawia, że logowanie użytkowników jest bardziej usprawnione.  Możesz skonfigurować automatyczne przyspieszanie dla poszczególnych aplikacji.

>[!NOTE]
>W przypadku skonfigurowania aplikacji do automatycznego przyspieszania użytkownicy nie mogą używać poświadczeń zarządzanych (takich jak FIDO), a użytkownicy-goście nie mogą się logować. Jeśli zabierzesz użytkownika bezpośrednio do feder typowego identyfikatora tożsamości w celu uwierzytelnienia, nie ma możliwości powrotu do strony logowania Azure Active Directory tożsamości. Użytkownicy-goście, którzy mogą być kierowani do innych dzierżaw lub zewnętrznego podmiotu tożsamości, takiego jak konto Microsoft, nie mogą zalogować się do tej aplikacji, ponieważ pomijają krok odnajdywania w całym kraju.  

Istnieją trzy sposoby kontrolowania automatycznego przyspieszania do federowania idP:

- Użyj wskazówki [dotyczącej domeny na](#domain-hints) żądaniach uwierzytelniania dla aplikacji.
- Skonfiguruj zasady odnajdywania home w celu [wymusenia automatycznego przyspieszenia](#home-realm-discovery-policy-for-auto-acceleration).
- Skonfiguruj zasady odnajdywania domeny głównej, aby [ignorować wskazówki dotyczące domeny z](prevent-domain-hints-with-home-realm-discovery.md) określonych aplikacji lub dla niektórych domen.

### <a name="domain-hints"></a>Wskazówki dotyczące domeny

Wskazówki dotyczące domeny to dyrektywy uwzględnione w żądaniu uwierzytelniania z aplikacji. Mogą one służyć do przyspieszania działania użytkownika do strony logowania federacyjną tożsamości. Mogą być one również używane przez aplikację wielodostępną w celu przyspieszenia działania użytkownika bezpośrednio na stronie logowania do usługi Azure AD pod marką swojej dzierżawy.  

Na przykład aplikacja "largeapp.com" może umożliwić swoim klientom dostęp do aplikacji pod niestandardowym adresem URL "contoso.largeapp.com". Aplikacja może również zawierać wskazówkę domeny, która contoso.com w żądaniu uwierzytelniania.

Składnia wskazówki dotyczącej domeny różni się w zależności od używanego protokołu i jest zwykle konfigurowana w aplikacji.

**WS-Federation**: whr=contoso.com w ciągu zapytania.

**SAML:** żądanie uwierzytelniania SAML zawierające wskazówkę domeny lub ciąg zapytania whr=contoso.com.

**Open ID Connect:** ciąg zapytania domain_hint=contoso.com.

Domyślnie usługa Azure AD próbuje przekierować logowanie do identyfikatora skonfigurowanego dla domeny, jeśli są spełnione następujące kwestie: 

- Wskazówka domeny jest uwzględniona w żądaniu uwierzytelniania z aplikacji **i**
- Dzierżawa jest federowana z tej domeny.

Jeśli wskazówka domeny nie odnosi się do zweryfikowanych domen federowanych, jest ignorowana.

Aby uzyskać więcej informacji na temat automatycznego przyspieszania przy użyciu wskazówek dotyczących domeny obsługiwanych przez Azure Active Directory, zobacz blog Enterprise Mobility + Security [domeny](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Jeśli wskazówka domeny jest uwzględniona [](#home-realm-discovery-policy-to-prevent-auto-acceleration)w żądaniu uwierzytelniania i powinna być uwzględniana, jej obecność zastępuje automatyczne przyspieszanie ustawione dla aplikacji w zasadach HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Zasady wykrywania home realm dla automatycznego przyspieszania

Niektóre aplikacje nie zapewniają sposobu konfigurowania emitowane przez nie żądania uwierzytelniania. W takich przypadkach nie jest możliwe używanie wskazówek dotyczących domeny do sterowania automatycznym przyspieszaniem. Automatyczne przyspieszanie można skonfigurować za pomocą zasad odnajdywania home realm w celu osiągnięcia tego samego zachowania.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Zasady odnajdywania home realm w celu zapobiegania automatycznemu przyspieszaniu

Niektóre aplikacje Firmy Microsoft i SaaS automatycznie dołączają domain_hints (na przykład powoduje żądanie logowania z dołączonym), co może zakłócić dołączanie poświadczeń zarządzanych, takich jak `https://outlook.com/contoso.com` `&domain_hint=contoso.com` FIDO.  Za pomocą zasad [odnajdywania home realm](/graph/api/resources/homeRealmDiscoveryPolicy) można ignorować wskazówki dotyczące domeny z określonych aplikacji lub dla niektórych domen podczas stosowania poświadczeń zarządzanych.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Włączanie bezpośredniego uwierzytelniania ZAC dla użytkowników federowanych dla starszych aplikacji

Najlepszym rozwiązaniem jest, aby aplikacje używały bibliotek usługi AAD i logowania interakcyjnego do uwierzytelniania użytkowników. Biblioteki te zajmą się przepływami użytkowników federowanych.  Czasami starsze aplikacje, szczególnie te, które korzystają z funkcji grantowania ZCC, przesyłają nazwę użytkownika i hasło bezpośrednio do usługi Azure AD i nie są napisane w celu zrozumienia federacji. Nie wykonują odnajdywania w domu i nie wchodzą w interakcję z poprawnym federacyjną punktem końcowym w celu uwierzytelnienia użytkownika. Jeśli wybierzesz tę opcję, możesz użyć zasad HRD, aby włączyć określone starsze aplikacje, które przesyłają poświadczenia nazwy użytkownika/hasła przy użyciu grantu SMTPC, aby uwierzytelniać się bezpośrednio w Azure Active Directory. Synchronizacja skrótów haseł musi być włączona.

> [!IMPORTANT]
> Włącz uwierzytelnianie bezpośrednie tylko wtedy, gdy masz włączoną synchronizację skrótów haseł i wiesz, że możesz uwierzytelnić tę aplikację bez żadnych zasad implementowanych przez lokalnego administratora tożsamości. Jeśli z jakiegokolwiek powodu wyłączysz synchronizację skrótów haseł lub wyłączysz synchronizację katalogów z programem AD Connect, usuń te zasady, aby zapobiec możliwości bezpośredniego uwierzytelniania przy użyciu nieaktualnego skrótu hasła.

## <a name="set-hrd-policy"></a>Ustawianie zasad HRD

Istnieją trzy kroki ustawiania zasad HRD dla aplikacji dla automatycznego przyspieszania logowania federowego lub bezpośrednich aplikacji opartych na chmurze:

1. Utwórz zasady HRD.

2. Znajdź jednostkę usługi, do której chcesz dołączyć zasady.

3. Dołącz zasady do jednostki usługi.

Zasady mają zastosowanie tylko w przypadku określonej aplikacji, gdy są dołączone do jednostki usługi.

Tylko jedna zasada HRD może być aktywna dla jednostki usługi w dowolnym momencie.  

Za pomocą poleceń cmdlet Azure Active Directory PowerShell można tworzyć zasady HRD i zarządzać nimi.

Poniżej przedstawiono przykładową definicję zasad HRD:

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

Typ zasad to "[HomeRealmDiscoveryPolicy".](/graph/api/resources/homeRealmDiscoveryPolicy)

**Parametr AccelerateToFederatedDomain** jest opcjonalny. Jeśli **wartość AccelerateToFederatedDomain ma** wartość false, zasady nie mają wpływu na automatyczne przyspieszanie. Jeśli **wartość AccelerateToFederatedDomain** jest prawdziwa, a w dzierżawie znajduje się tylko jedna zweryfikowana i federowana domena, użytkownicy będą przekierowyani bezpośrednio do federatora tożsamości w celu zalogowania. Jeśli ta wartość jest prawdziwa i w dzierżawie znajduje się więcej niż jedna zweryfikowana domena, należy określić **wartość PreferredDomain.**

**Parametr PreferredDomain** jest opcjonalny. **Domena PreferredDomain** powinna wskazywać domenę, do której ma na celu przyspieszenie. Można go pominąć, jeśli dzierżawa ma tylko jedną domenę federacyjną.  Jeśli zostanie ona pominięta i istnieje więcej niż jedna zweryfikowana domena federowana, zasady nie będą obowiązywać.

 Jeśli **określono wartość PreferredDomain,** musi ona być dopasowana do zweryfikowanych domen federowanych dla dzierżawy. Wszyscy użytkownicy aplikacji muszą mieć możliwość logowania się do tej domeny — użytkownicy, którzy nie mogą zalogować się w domenie federacji, zostaną wyłamieni i nie będą mogli ukończyć logowania.

**Parametr AllowCloudPasswordValidation** jest opcjonalny. Jeśli **parametr AllowCloudPasswordValidation** ma wartość true, aplikacja może uwierzytelnić użytkownika federowego, prezentując poświadczenia nazwy użytkownika/hasła bezpośrednio w Azure Active Directory tokenu. Ta funkcja działa tylko wtedy, gdy synchronizacja skrótów haseł jest włączona.

Ponadto istnieją dwie opcje HRD na poziomie dzierżawy, które nie zostały pokazane powyżej:

- **Parametr AlternateIdLogin** jest opcjonalny.  Jeśli ta opcja jest [włączona, umożliwia to użytkownikom](../authentication/howto-authentication-use-email-signin.md) logowanie się przy użyciu ich adresów e-mail zamiast ich głównej głównej osoby na stronie logowania usługi Azure AD.  Alternatywne identyfikatory polegają na tym, że użytkownik nie jest automatycznie przyspieszany do federowania dostawcy tożsamości.

- **DomainHintPolicy** to opcjonalny obiekt złożony, który uniemożliwia wskazówkom domeny automatyczne przyspieszanie użytkowników [  do domen federowanych.](prevent-domain-hints-with-home-realm-discovery.md) To ustawienie dla całej dzierżawy służy do zapewnienia, że aplikacje, które wysyłają wskazówki dotyczące domeny, nie uniemożliwiają użytkownikom logowania się przy użyciu poświadczeń zarządzanych przez chmurę.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorytet i ocena zasad HRD

Zasady HRD można tworzyć, a następnie przypisywać do określonych organizacji i podmiotów zabezpieczeń usług. Oznacza to, że istnieje możliwość zastosowania wielu zasad do określonej aplikacji, dlatego usługa Azure AD musi zdecydować, która z nich ma pierwszeństwo. Zestaw reguł decyduje o tym, które zasady HRD (wiele zastosowanych zasad) mają zastosowanie:

- Jeśli wskazówka domeny jest obecna w żądaniu uwierzytelniania, zasady HRD dla dzierżawy (zasady ustawione jako domyślne dzierżawy) są sprawdzane, czy wskazówki dotyczące domeny powinny być [ignorowane.](prevent-domain-hints-with-home-realm-discovery.md) Jeśli wskazówki dotyczące domeny są dozwolone, używane jest zachowanie określone przez wskazówkę domeny.

- W przeciwnym razie, jeśli zasady są jawnie przypisane do jednostki usługi, są wymuszane.

- Jeśli nie ma wskazówki dotyczącej domeny i żadne zasady nie są jawnie przypisane do jednostki usługi, wymuszane są zasady jawnie przypisane do organizacji nadrzędnej jednostki usługi.

- Jeśli nie ma wskazówki dotyczącej domeny i żadne zasady nie zostały przypisane do jednostki usługi lub organizacji, zostanie użyte domyślne zachowanie HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Samouczek dotyczący ustawiania zasad HRD dla aplikacji

Użyjemy poleceń cmdlet programu PowerShell usługi Azure AD, aby przejść przez kilka scenariuszy, w tym:

- Konfigurowanie zasad HRD w celu automatycznego przyspieszenia aplikacji w dzierżawie z jedną domeną federacyjną.

- Konfigurowanie zasad HRD w celu automatycznego przyspieszenia aplikacji w jednej z kilku domen, które są weryfikowane dla dzierżawy.

- Konfigurowanie zasad HRD w celu umożliwienia starszej aplikacji bezpośredniego uwierzytelniania nazwy użytkownika/hasła w Azure Active Directory dla użytkownika federowego.

- Wyświetlanie listy aplikacji, dla których skonfigurowano zasady.

### <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach utworzysz, zaktualizujemy, połączysz i usuniesz zasady dla jednostki usługi aplikacji w usłudze Azure AD.

1. Aby rozpocząć, pobierz najnowszą wersję zapoznawczą polecenia cmdlet programu PowerShell usługi Azure AD.

2. Po pobraniu poleceń cmdlet programu PowerShell usługi Azure AD uruchom polecenie Connect, aby zalogować się do usługi Azure AD przy użyciu konta administratora:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Uruchom następujące polecenie, aby wyświetlić wszystkie zasady w organizacji:

    ``` powershell
    Get-AzureADPolicy
    ```

Jeśli nic nie zostanie zwrócone, oznacza to, że w dzierżawie nie utworzono żadnych zasad.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Przykład: ustawianie zasad HRD dla aplikacji

W tym przykładzie utworzysz zasady, które po przypisaniu do aplikacji:

- Automatyczne przyspiesza logowanie użytkowników do AD FS logowania podczas logowania się do aplikacji, gdy w dzierżawie istnieje pojedyncza domena.
- Automatyczne przyspiesza użytkowników do AD FS logowania, w dzierżawie znajduje się więcej niż jedna domena federowana.
- Umożliwia nieinterakcyjne logowanie nazwy użytkownika/hasła bezpośrednio Azure Active Directory dla użytkowników federowanych dla aplikacji, do których przypisano zasady.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1. Tworzenie zasad HRD

Następujące zasady automatycznie przyspieszają użytkownikom AD FS ekranu logowania podczas logowania się do aplikacji, gdy w dzierżawie istnieje pojedyncza domena.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Następujące zasady automatycznie przyspieszają użytkowników do AD FS logowania, w dzierżawie znajduje się więcej niż jedna domena federowana. Jeśli masz więcej niż jedną domenę federacyjną, która uwierzytelnia użytkowników na potrzeby aplikacji, musisz określić domenę do automatycznego przyspieszenia.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Aby utworzyć zasady umożliwiające uwierzytelnianie nazwy użytkownika/hasła dla użytkowników federowanych bezpośrednio z Azure Active Directory dla określonych aplikacji, uruchom następujące polecenie:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Aby wyświetlić nowe zasady i uzyskać ich **identyfikator ObjectID,** uruchom następujące polecenie:

``` powershell
Get-AzureADPolicy
```

Aby zastosować zasady HRD po ich utworzeniu, możesz przypisać je do wielu podmiotów zabezpieczeń usługi aplikacji.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2. Zlokalizowanie jednostki usługi, do której mają zostać przypisane zasady

Potrzebujesz wartości **ObjectID** jednostki usługi, do której chcesz przypisać zasady. Istnieje kilka sposobów znalezienia **identyfikatorów obiektów** jednostki usługi.

Możesz użyć portalu lub utworzyć zapytanie dotyczące [Microsoft Graph](/graph/api/resources/serviceprincipal). Możesz również przejść do narzędzia Eksplorator programu [Graph](https://developer.microsoft.com/graph/graph-explorer) i zalogować się do konta usługi Azure AD, aby wyświetlić wszystkie jednostki usług w organizacji.

Ponieważ używasz programu PowerShell, możesz użyć następującego polecenia cmdlet, aby wyświetlić listę jednostki usługi i ich identyfikatorów.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3. Przypisywanie zasad do jednostki usługi

Gdy masz identyfikator **ObjectID** jednostki usługi aplikacji, dla której chcesz skonfigurować automatyczne przyspieszanie, uruchom następujące polecenie. To polecenie kojarzy zasady HRD utworzone w kroku 1 z jednostką usługi znajdującą się w kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

To polecenie można powtórzyć dla każdej jednostki usługi, do której chcesz dodać zasady.

Jeśli aplikacja ma już przypisane zasady HomeRealmDiscovery, nie będzie można dodać drugiej.  W takim przypadku zmień definicję zasad odnajdywania home realm przypisaną do aplikacji, aby dodać dodatkowe parametry.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4. Sprawdzenie, do których podmiotów usługi aplikacji przypisano zasady HRD

Aby sprawdzić, które aplikacje mają skonfigurowane zasady HRD, użyj polecenia cmdlet **Get-AzureADPolicyAppliedObject.** Przekaż do **niego identyfikator ObjectID** zasad, które chcesz sprawdzić.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Krok 5. Wszystko gotowe!

Wypróbuj aplikację, aby sprawdzić, czy nowe zasady działają.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Przykład: lista aplikacji, dla których skonfigurowano zasady HRD

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1. Lista wszystkich zasad utworzonych w organizacji

``` powershell
Get-AzureADPolicy
```

**Zanotuj** identyfikator ObjectID zasad, dla których chcesz wyświetlić listę przypisań.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2. Lista podmiotów usługi, do których przypisano zasady  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Przykład: usuwanie zasad HRD z aplikacji

#### <a name="step-1-get-the-objectid"></a>Krok 1. Uzyskiwanie identyfikatorów ObjectID

Użyj poprzedniego przykładu, aby uzyskać **identyfikator ObjectID** zasad oraz identyfikator jednostki usługi aplikacji, z której chcesz go usunąć.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2. Usuwanie przypisania zasad z jednostki usługi aplikacji  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3. Sprawdzenie usunięcia przez listę podmiotów usługi, do których przypisano zasady

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat sposobu działania uwierzytelniania w usłudze Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD.](../develop/authentication-vs-authorization.md)
- Aby uzyskać więcej informacji na temat logowania pojedynczego użytkownika, zobacz [Single sign-on to applications in Azure Active Directory](what-is-single-sign-on.md).
- Odwiedź stronę [platformy tożsamości firmy Microsoft,](../develop/v2-overview.md) aby uzyskać przegląd całej zawartości związanej z deweloperami.