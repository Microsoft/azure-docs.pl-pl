---
title: Reguły dotyczące dynamicznego uzupełniania członkostwa grup — Azure AD | Microsoft Docs
description: Sposób tworzenia reguł członkostwa w celu automatycznego wypełniania grup i odwołania do reguły.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f36e5d8974caea0eecff7e0b399b6aab5d200664
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88797109"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Reguły członkostwa dynamicznego dla grup w Azure Active Directory

W Azure Active Directory (Azure AD) można tworzyć złożone reguły oparte na atrybutach, aby włączyć dynamiczne członkostwa dla grup. Członkostwo w grupie dynamicznej zmniejsza narzuty administracyjne dotyczące dodawania i usuwania użytkowników. W tym artykule szczegółowo opisano właściwości i składnię tworzenia reguł członkostwa dynamicznego dla użytkowników lub urządzeń. Można skonfigurować regułę dynamicznego członkostwa w grupach zabezpieczeń lub grupach Microsoft 365.

W przypadku zmiany atrybutów użytkownika lub urządzenia system oblicza wszystkie dynamiczne reguły grupy w katalogu, aby sprawdzić, czy dana zmiana spowodowałaby dodanie lub usunięcie grupy. Jeśli użytkownik lub urządzenie spełnia regułę w grupie, są one dodawane jako członek tej grupy. Jeśli nie spełniają już reguły, zostaną one usunięte. Nie można ręcznie dodać lub usunąć członka grupy dynamicznej.

- Można utworzyć grupę dynamiczną dla urządzeń lub użytkowników, ale nie można utworzyć reguły zawierającej użytkowników i urządzenia.
- Nie można utworzyć grupy urządzeń opartej na atrybutach właścicieli urządzeń. Reguły członkostwa urządzeń mogą odwoływać się tylko do atrybutów urządzeń.

> [!NOTE]
> Ta funkcja wymaga Azure AD — wersja Premium licencji P1 dla każdego unikatowego użytkownika, który jest członkiem jednej lub więcej grup dynamicznych. Nie musisz przypisywać licencji użytkownikom, aby były członkami grup dynamicznych, ale musisz mieć minimalną liczbę licencji w organizacji usługi Azure AD, aby uwzględnić wszystkich tych użytkowników. Na przykład jeśli masz łącznie 1 000 unikatowych użytkowników we wszystkich grupach dynamicznych w organizacji, musisz mieć co najmniej 1 000 licencji na Azure AD — wersja Premium P1, aby spełnić wymagania dotyczące licencji.
> Dla urządzeń, które są członkami dynamicznej grupy urządzeń, nie jest wymagana żadna licencja.

## <a name="rule-builder-in-the-azure-portal"></a>Konstruktor reguł w Azure Portal

Usługa Azure AD udostępnia Konstruktor reguł, który umożliwia szybsze tworzenie i aktualizowanie ważnych reguł. Konstruktor reguł obsługuje konstruowanie do pięciu wyrażeń. Konstruktor reguł ułatwia tworzenie reguły z kilkoma prostymi wyrażeniami, ale nie można ich użyć do odtworzenia każdej reguły. Jeśli Konstruktor reguł nie obsługuje reguły, którą chcesz utworzyć, możesz użyć pola tekstowego.

Poniżej przedstawiono kilka przykładów zaawansowanych reguł lub składni, dla których zalecamy utworzenie przy użyciu pola tekstowego:

- Reguła z więcej niż pięcioma wyrażeniami
- Reguła bezpośrednich podwładnych
- Ustawianie [pierwszeństwa operatorów](groups-dynamic-membership.md#operator-precedence)
- [Reguły z wyrażeniami złożonymi](groups-dynamic-membership.md#rules-with-complex-expressions); na przykład `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Konstruktor reguł może nie być w stanie wyświetlić niektórych reguł skonstruowanych w polu tekstowym. Gdy Konstruktor reguł nie może wyświetlić reguły, może zostać wyświetlony komunikat. Konstruktor reguł nie zmienia obsługiwanej składni, walidacji lub przetwarzania reguł grupy dynamicznej w dowolny sposób.

Aby uzyskać więcej instrukcji krok po kroku, zobacz [Tworzenie lub aktualizowanie grupy dynamicznej](groups-create-rule.md).

![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Składnia reguły dla pojedynczego wyrażenia

Pojedyncze wyrażenie jest najprostszą formą reguły członkostwa i zawiera tylko trzy wymienione powyżej elementy. Reguła z jednym wyrażeniem wygląda podobnie do następującej: `Property Operator Value` , gdzie składnia właściwości jest nazwą Object. Property.

Poniżej znajduje się przykład prawidłowo skonstruowanej reguły członkostwa z pojedynczym wyrażeniem:

```
user.department -eq "Sales"
```

Nawiasy są opcjonalne dla jednego wyrażenia. Łączna długość treści reguły członkostwa nie może przekraczać 2048 znaków.

## <a name="constructing-the-body-of-a-membership-rule"></a>Konstruowanie treści reguły członkostwa

Reguła członkostwa, która automatycznie wypełnia grupę użytkownikami lub urządzeniami, jest wyrażeniem binarnym, które powoduje wynik PRAWDA lub FAŁSZ. Trzy części prostej reguły są:

- Właściwość
- Operator
- Wartość

Kolejność części w wyrażeniu jest ważna, aby uniknąć błędów składniowych.

## <a name="supported-properties"></a>Obsługiwane właściwości

Istnieją trzy typy właściwości, których można użyć do skonstruowania reguły członkostwa.

- Boolean (wartość logiczna)
- String (ciąg)
- Kolekcja ciągów

Poniżej przedstawiono właściwości użytkownika, których można użyć do utworzenia jednego wyrażenia.

### <a name="properties-of-type-boolean"></a>Właściwości typu Boolean

| Właściwości | Dozwolone wartości | Użycie |
| --- | --- | --- |
| accountEnabled |PRAWDA FAŁSZ |User. accountEnabled-EQ true |
| dirSyncEnabled |PRAWDA FAŁSZ |User. dirSyncEnabled-EQ true |

### <a name="properties-of-type-string"></a>Właściwości typu String

| Właściwości | Dozwolone wartości | Użycie |
| --- | --- | --- |
| city |Dowolna wartość ciągu lub wartość *null* |(User. City-EQ "wartość") |
| country |Dowolna wartość ciągu lub wartość *null* |(User. Country-EQ "wartość") |
| companyName | Dowolna wartość ciągu lub wartość *null* | (User. NazwaFirmy-EQ "wartość") |
| działu, |Dowolna wartość ciągu lub wartość *null* |(User. Department-EQ "wartość") |
| displayName |Dowolna wartość ciągu |(User. displayName-EQ "value") |
| IDPracownika |Dowolna wartość ciągu |(User. IDPracownika-EQ "value")<br>(User. IDPracownika-ne *null*) |
| facsimileTelephoneNumber |Dowolna wartość ciągu lub wartość *null* |(User. facsimileTelephoneNumber-EQ "wartość") |
| givenName |Dowolna wartość ciągu lub wartość *null* |(User. podaną wartośćname-EQ ") |
| Stanowiska |Dowolna wartość ciągu lub wartość *null* |(User. stanowiska-EQ "wartość") |
| mail (poczta) |Dowolna wartość ciągu lub wartość *null* (adres SMTP użytkownika) |(User. mail-EQ "wartość") |
| mailNickName |Dowolna wartość ciągu (alias poczty użytkownika) |(User. mailNickName-EQ "wartość") |
| telefon komórkowy |Dowolna wartość ciągu lub wartość *null* |(User. Mobile-EQ "wartość") |
| Obiektu |Identyfikator GUID obiektu użytkownika |(User. objectId-EQ "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Lokalny identyfikator zabezpieczeń (SID) dla użytkowników, którzy zostali zsynchronizowani z lokalnego do chmury. |(User. onPremisesSecurityIdentifier-EQ "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Brak DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(User. passwordPolicies-EQ "DisableStrongPassword") |
| physicalDeliveryOfficeName |Dowolna wartość ciągu lub wartość *null* |(User. physicalDeliveryOfficeName-EQ "wartość") |
| Pocztowy |Dowolna wartość ciągu lub wartość *null* |(User. KodPocztowy-EQ "value") |
| preferredLanguage |Kod ISO 639-1 |(User. preferredLanguage-EQ "pl-US") |
| sipProxyAddress |Dowolna wartość ciągu lub wartość *null* |(User. sipProxyAddress-EQ "wartość") |
| stan |Dowolna wartość ciągu lub wartość *null* |(User. State-EQ "value") |
| streetAddress |Dowolna wartość ciągu lub wartość *null* |(User. streetAddress-EQ "wartość") |
| surname |Dowolna wartość ciągu lub wartość *null* |(User. nazwisko-EQ "wartość") |
| telephoneNumber |Dowolna wartość ciągu lub wartość *null* |(User. teletelefon-EQ "wartość") |
| usageLocation |Dwuliterowy kod kraju/regionu |(User. usageLocation-EQ "US") |
| userPrincipalName |Dowolna wartość ciągu |(User. userPrincipalName-EQ " alias@domain ") |
| userType |Gość elementu członkowskiego *ma wartość null* |(User. UserType-EQ "member") |

### <a name="properties-of-type-string-collection"></a>Właściwości kolekcji ciągów typu

| Właściwości | Dozwolone wartości | Użycie |
| --- | --- | --- |
| otherMails |Dowolna wartość ciągu |(User. otherMails-zawiera " alias@domain ") |
| proxyAddresses |alias@domainSMTP:alias@domain |(User. proxyAddresses-zawiera "SMTP: alias@domain ") |

Aby uzyskać właściwości używane dla reguł urządzeń, zobacz [reguły dotyczące urządzeń](#rules-for-devices).

## <a name="supported-expression-operators"></a>Obsługiwane operatory wyrażeń

W poniższej tabeli wymieniono wszystkie obsługiwane operatory i ich składnię dla jednego wyrażenia. Operatory mogą być używane z prefiksem łącznika (-) lub bez niego.

| Operator | Składnia |
| --- | --- |
| Nie równa się |-ne |
| Równa się |-EQ |
| Nie zaczyna się od |-notStartsWith |
| Rozpoczyna się od |-startsWith |
| Nie zawiera |-notContains |
| Contains |-zawiera |
| Niezgodne |-notMatch |
| Dopasowanie |-dopasowanie |
| W | -in |
| Nie w | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Używanie operatorów-in i-notIn

Jeśli chcesz porównać wartość atrybutu użytkownika z liczbą różnych wartości, możesz użyć operatorów-in lub-notIn. Użyj symboli nawiasów "[" i "]", aby rozpocząć i zakończyć listę wartości.

 W poniższym przykładzie wyrażenie ma wartość true, jeśli wartość User. Department jest równa którejkolwiek z wartości na liście:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Używanie operatora-Match 
Operator **-Match** służy do dopasowywania dowolnego wyrażenia regularnego. Przykłady:

```
user.displayName -match "Da.*"   
```
Da, DAV, David ma wartość true, aDa ma wartość false.

```
user.displayName -match ".*vid"
```
David ma wartość true, da w przypadku wartości false.

## <a name="supported-values"></a>Obsługiwane wartości

Wartości używane w wyrażeniu mogą składać się z kilku typów, w tym:

* Ciągi
* Wartość logiczna — prawda, FAŁSZ
* Liczby
* Tablice — tablica liczbowa, tablica ciągów

Podczas określania wartości w wyrażeniu ważne jest używanie poprawnej składni w celu uniknięcia błędów. Niektóre wskazówki dotyczące składni:

* Podwójne cudzysłowy są opcjonalne, chyba że wartość jest ciągiem.
* W operacjach typu String i wyrażeń regularnych nie jest rozróżniana wielkość liter.
* Gdy wartość ciągu zawiera podwójne cudzysłowy, oba cudzysłowy powinny być wyprowadzane przy użyciu \` znaku, na przykład user. Department-EQ \` "Sales \` " jest poprawną składnią, gdy "Sales" jest wartością.
* Można również przeprowadzać sprawdzanie wartości null, używając wartości null jako wartości, na przykład `user.department -eq null` .

### <a name="use-of-null-values"></a>Użycie wartości null

Aby określić wartość null w regule, można użyć wartości *null* . 

* Użyj-EQ lub-ne podczas porównywania wartości *null* w wyrażeniu.
* Używaj cudzysłowów wokół słowa *null* tylko, jeśli chcesz, aby był interpretowany jako wartość ciągu literału.
* Nie można użyć operatora-not jako operatora porównawczego dla wartości null. Jeśli używasz tego elementu, wystąpi błąd w przypadku używania wartości null lub $null.

Prawidłowy sposób odwołania do wartości null jest następujący:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Reguły z wieloma wyrażeniami

Reguła członkostwa w grupie może składać się z więcej niż jednego wyrażenia, które jest połączone z operatorami-i,-lub i-not logicznych. Operatory logiczne mogą być również używane w połączeniu. 

Poniżej przedstawiono przykłady prawidłowo skonstruowanych reguł członkostwa z wieloma wyrażeniami:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Pierwszeństwo operatorów

Wszystkie operatory są wymienione poniżej w kolejności pierwszeństwa od najwyższego do najniższego. Operatory w tym samym wierszu mają równy priorytet:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Poniżej znajduje się przykład priorytetu operatora, w którym są oceniane dwa wyrażenia dla użytkownika:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Nawiasy są wymagane tylko wtedy, gdy pierwszeństwo nie spełnia wymagań. Na przykład jeśli chcesz, aby dział został oceniony jako pierwszy, poniżej przedstawiono sposób, w jaki nawiasy mogą być używane do określenia kolejności:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Reguły z wyrażeniami złożonymi

Reguła członkostwa może składać się z złożonych wyrażeń, w których właściwości, operatory i wartości są wykonywane na bardziej złożonych formularzach. Wyrażenia są uważane za złożone, gdy spełniony jest dowolny z następujących warunków:

* Właściwość składa się z kolekcji wartości; szczegółowe właściwości wielowartościowe
* Wyrażenia używają operatorów-any i-All
* Wartość wyrażenia może być sama co najmniej jednym wyrażeniem

## <a name="multi-value-properties"></a>Właściwości wielowartościowe

Właściwości wielowartościowe to kolekcje obiektów tego samego typu. Mogą służyć do tworzenia reguł członkostwa przy użyciu-any i-All operatorów logicznych.

| Właściwości | Wartości | Użycie |
| --- | --- | --- |
| assignedPlans | Każdy obiekt w kolekcji uwidacznia następujące właściwości ciągu: capabilityStatus, Service, servicePlanId |User. assignedPlans-any (assignedPlan. servicePlanId-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-and assignedPlan. capabilityStatus-EQ "Enabled") |
| proxyAddresses| alias@domainSMTP:alias@domain | (User. proxyAddresses-any ( \_ -zawiera "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Używanie operatorów-any i-All

Można użyć operatora-any i-All, aby zastosować warunek do jednego lub wszystkich elementów w kolekcji.

* -any (spełnione, gdy co najmniej jeden element w kolekcji pasuje do warunku)
* -All (spełnione, gdy wszystkie elementy w kolekcji są zgodne z warunkiem)

#### <a name="example-1"></a>Przykład 1

assignedPlans to właściwość wielowartościowa, która wyświetla listę wszystkich planów usług przypisanych do użytkownika. Poniższe wyrażenie służy do wybierania użytkowników, którzy mają plan usługi Exchange Online (plan 2) (jako wartość identyfikatora GUID), który jest również w stanie włączony:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Reguła, taka jak ta, może służyć do grupowania wszystkich użytkowników, dla których włączono obsługę Microsoft 365 (lub inne usługi online firmy Microsoft). Następnie można zastosować zestaw zasad do grupy.

#### <a name="example-2"></a>Przykład 2

Poniższe wyrażenie umożliwia wybranie wszystkich użytkowników, którzy mają dowolny plan usługi skojarzony z usługą Intune (identyfikowany przez nazwę usługi "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Używanie znaku podkreślenia ( \_ )

Składnia podkreślenia ( \_ ) dopasowuje wystąpienia określonej wartości w jednej z wielowartościowych właściwości kolekcji ciągów do dodawania użytkowników lub urządzeń do grupy dynamicznej. Jest używana z operatorami-any lub-ALL.

Oto przykład użycia podkreślenia ( \_ ) w regule, aby dodać członków na podstawie User. ProxyAddress (działa tak samo dla User. otherMails). Ta reguła dodaje do grupy dowolnego użytkownika z adresem serwera proxy, który zawiera "contoso".

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Inne właściwości i typowe reguły

### <a name="create-a-direct-reports-rule"></a>Tworzenie reguły "raporty bezpośrednie"

Można utworzyć grupę zawierającą wszystkie bezpośrednie raporty Menedżera. Gdy raporty bezpośrednie Menedżera zmieniają się w przyszłości, członkostwo w grupie jest dostosowywane automatycznie.

Reguła bezpośrednich raportów jest zbudowana przy użyciu następującej składni:

```
Direct Reports for "{objectID_of_manager}"
```

Oto przykład prawidłowej reguły, w której "62e19b97-8b3d-4d4a-A106-4ce66896a863" jest identyfikatorem objectID Menedżera:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Poniższe porady mogą pomóc w poprawnym użyciu zasady.

- **Identyfikator Menedżera** jest identyfikatorem obiektu menedżera. Można je znaleźć w **profilu**Menedżera.
- Aby reguła działała, upewnij się, że właściwość **Manager** została ustawiona poprawnie dla użytkowników w organizacji. Bieżącą wartość można sprawdzić w **profilu**użytkownika.
- Ta reguła obsługuje tylko raporty bezpośrednie Menedżera. Innymi słowy, nie można utworzyć grupy z bezpośrednimi raportami Menedżera *i* ich raportami.
- Tej reguły nie można łączyć z żadną inną regułą członkostwa.

### <a name="create-an-all-users-rule"></a>Tworzenie reguły "Wszyscy użytkownicy"

Można utworzyć grupę zawierającą wszystkich użytkowników w organizacji przy użyciu reguły członkostwa. Gdy użytkownicy są dodawani lub usuwani z organizacji w przyszłości, członkostwo w grupie jest dostosowywane automatycznie.

Reguła "Wszyscy użytkownicy" jest zbudowana przy użyciu wyrażenia pojedynczego, przy użyciu operatora-ne i wartości null. Ta zasada umożliwia dodanie użytkowników-Gości, a także użytkowników należących do grupy.

```
user.objectId -ne null
```
Jeśli chcesz, aby grupa wykluczać użytkowników-Gości i dołączać tylko członków organizacji, możesz użyć następującej składni:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Tworzenie reguły "wszystkie urządzenia"

Można utworzyć grupę zawierającą wszystkie urządzenia w organizacji przy użyciu reguły członkostwa. Gdy urządzenia są dodawane lub usuwane z organizacji w przyszłości, członkostwo w grupie jest dostosowywane automatycznie.

Reguła "wszystkie urządzenia" jest zbudowana przy użyciu wyrażenia pojedynczego, przy użyciu operatora-ne i wartości null:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Właściwości rozszerzenia i niestandardowe właściwości rozszerzenia

Atrybuty rozszerzenia i niestandardowe właściwości rozszerzenia są obsługiwane jako właściwości ciągu w regułach dynamicznego członkostwa. [Atrybuty rozszerzenia](/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) są synchronizowane z lokalnego serwera okien usługi AD i przyjmują format "ExtensionAttributeX", gdzie X jest równe 1-15. Oto przykład reguły, która używa atrybutu rozszerzenia jako właściwości:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Niestandardowe właściwości rozszerzenia](../hybrid/how-to-connect-sync-feature-directory-extensions.md) są synchronizowane z lokalnej usługi AD systemu Windows Server lub z połączonej aplikacji SaaS i mają format `user.extension_[GUID]_[Attribute]` , gdzie:

* [GUID] jest unikatowym identyfikatorem w usłudze Azure AD dla aplikacji, która utworzyła właściwość w usłudze Azure AD
* [Attribute] jest nazwą właściwości, która została utworzona

Przykładem reguły korzystającej z niestandardowej właściwości rozszerzenia jest:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Nazwę właściwości niestandardowej można znaleźć w katalogu, wykonując zapytania dotyczące właściwości użytkownika przy użyciu Eksploratora grafów i wyszukując nazwę właściwości. Ponadto można teraz wybrać łącze **Pobierz niestandardowe właściwości rozszerzenia** w konstruktorze reguły dynamicznej grupy użytkowników, aby wprowadzić unikatowy identyfikator aplikacji i otrzymać pełną listę niestandardowych właściwości rozszerzenia, które będą używane podczas tworzenia reguły członkostwa dynamicznego. Tę listę można również odświeżyć, aby uzyskać nowe niestandardowe właściwości rozszerzenia dla tej aplikacji.

## <a name="rules-for-devices"></a>Reguły dla urządzeń

Możesz również utworzyć regułę, która wybiera obiekty urządzeń dla członkostwa w grupie. Użytkownicy i urządzenia nie mogą być członkami grupy. 

> [!NOTE]
> Atrybut **OrganizationalUnit** nie jest już wyświetlany i nie powinien być używany. Ten ciąg jest ustawiany przez usługę Intune w określonych przypadkach, ale nie jest rozpoznawany przez usługę Azure AD, więc żadne urządzenia nie są dodawane do grup na podstawie tego atrybutu.

> [!NOTE]
> systemlabels to atrybut tylko do odczytu, którego nie można ustawić za pomocą usługi Intune.
>
> W przypadku systemu Windows 10 prawidłowy format atrybutu deviceOSVersion jest następujący: (Device. deviceOSVersion-EQ "10.0.17763"). Formatowanie można sprawdzić za pomocą polecenia cmdlet Get-MsolDevice programu PowerShell.

Można użyć następujących atrybutów urządzeń.

 Atrybut urządzenia  | Wartości | Przykład
 ----- | ----- | ----------------
 accountEnabled | PRAWDA FAŁSZ | (Device. accountEnabled-EQ true)
 displayName | dowolna wartość ciągu |(Device. displayName-EQ "Rob iPhone")
 deviceOSType | dowolna wartość ciągu | (Device. deviceOSType-EQ "iPad") — lub (Device. deviceOSType-EQ "iPhone")<br>(Device. deviceOSType-zawiera "AndroidEnterprise")<br>(Device. deviceOSType-EQ "AndroidForWork")
 deviceOSVersion | dowolna wartość ciągu | (Device. deviceOSVersion-EQ "9,1")
 deviceCategory | prawidłowa nazwa kategorii urządzeń | (Device. deviceCategory-EQ "BYOD")
 deviceManufacturer | dowolna wartość ciągu | (Device. deviceManufacturer-EQ "Samsung")
 deviceModel | dowolna wartość ciągu | (Device. deviceModel-EQ "iPad Air")
 deviceOwnership | Osobiste, firma, nieznane | (Device. deviceOwnership-EQ "Company")
 enrollmentProfileName | Nazwa profilu rejestracji urządzeń firmy Apple, nazwa profilu rejestracji dedykowanego urządzenia z systemem Android Enterprise lub nazwa profilu autopilotażu systemu Windows | (Device. enrollmentProfileName-EQ "telefony iPhone")
 isrootd | PRAWDA FAŁSZ | (Device. isrootd-EQ true)
 managementtype | MDM (dla urządzeń przenośnych)<br>Komputer PC (dla komputerów zarządzanych przez agenta komputera usługi Intune) | (Device. managementtype-EQ "MDM")
 deviceId | prawidłowy identyfikator urządzenia usługi Azure AD | (Device. deviceId-EQ "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Obiektu | prawidłowy identyfikator obiektu usługi Azure AD |  (Device. objectId-EQ "76ad43c9-32c5-45e8-A272-7b58b58f596d")
 devicePhysicalIds | dowolna wartość ciągu używana przez autopilotaż, taka jak wszystkie urządzenia autopilotażowe, IDZamówienia lub PurchaseOrderID  | (Device. devicePhysicalIDs-any _-zawiera "[ZTDId]") (Device. devicePhysicalIds-any _-EQ "[IDZamówienia]: 179887111881") (Device. devicePhysicalIds-any _-EQ "[PurchaseOrderId]: 76222342342")
 systemLabels | dowolny ciąg zgodny z właściwością urządzenia usługi Intune w celu tagowania nowoczesnych urządzeń w miejscu pracy | (device.systemLabels-zawiera "M365Managed")

> [!Note]  
> Dla deviceOwnership podczas tworzenia grup dynamicznych dla urządzeń należy ustawić wartość równą "Company". W usłudze Intune własność urządzenia jest reprezentowana jako firma. Aby uzyskać więcej informacji, zobacz [OwnerTypes](/intune/reports-ref-devices#ownertypes) . 

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje dotyczące grup w Azure Active Directory.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-create-rule.md)