---
title: Omówienie dzienników aprowizowania w Azure Portal (wersja zapoznawcza) | Microsoft Docs
description: Wprowadzenie do aprowizowania raportów dzienników w Azure Active Directory za pośrednictwem Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535992"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Omówienie dzienników aprowizowania w Azure Portal (wersja zapoznawcza)

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- Działanie: 
    - **Logowania: informacje** dotyczące użycia aplikacji zarządzanych i działań logowania użytkowników.
    - [Dzienniki inspekcji:](concept-audit-logs.md)informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupą, zarządzanymi aplikacjami i działaniami katalogu.
    - **Dzienniki aprowizowania:** aktywność systemu o użytkownikach, grupach i rolach, które są aprowowane przez usługę aprowizowania usługi Azure AD. 

- Zabezpieczeń: 
    - **Ryzykowne** logowania: Ryzykowne logowanie jest wskaźnikiem próby logowania, która mogła zostać wykonana przez osobę, która nie jest uprawnionym właścicielem konta użytkownika. [](../identity-protection/overview-identity-protection.md)
    - **Użytkownicy oflagowany w celu oceny ryzyka:** ryzykowny [użytkownik](../identity-protection/overview-identity-protection.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten temat zawiera omówienie dzienników aprowizowania. Dzienniki zawierają odpowiedzi na pytania, takie jak: 

* Jakie grupy zostały pomyślnie utworzone w usłudze ServiceNow?
* Którzy użytkownicy zostali pomyślnie usunięci z firmy Adobe?
* Którzy użytkownicy z aplikacji Workday zostali pomyślnie utworzeni w usłudze Active Directory? 

## <a name="prerequisites"></a>Wymagania wstępne

Ci użytkownicy mogą uzyskać dostęp do danych w dziennikach aprowizowania:

* Właściciele aplikacji (dzienniki dla własnych aplikacji)
* Użytkownicy z rolami Administrator zabezpieczeń, Czytelnik zabezpieczeń, Czytelnik raportów, Operator zabezpieczeń, Administrator aplikacji i Administrator aplikacji w chmurze
* Użytkownicy w roli niestandardowej z [uprawnieniem provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Administratorzy globalni


Aby można było wyświetlić raport działań związanych z aprowizowanie, dzierżawa musi mieć Azure AD — wersja Premium skojarzoną licencję usługi. Aby uaktualnić wersję usługi Azure AD, zobacz [Wprowadzenie do usługi Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Sposoby interakcji z dziennikami aprowizowania 
Klienci mogą wchodzić w interakcje z dziennikami aprowizowania na cztery sposoby:

- Uzyskiwanie dostępu do dzienników z Azure Portal zgodnie z opisem w następnej sekcji.
- Przesyłanie strumieniowe dzienników aprowizowania [do Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). Ta metoda umożliwia rozszerzone przechowywanie danych i tworzenie niestandardowych pulpitów nawigacyjnych, alertów i zapytań.
- Wykonywanie zapytania do [interfejsu API Microsoft Graph w](/graph/api/resources/provisioningobjectsummary) celu obsługi dzienników aprowizowania.
- Pobieranie dzienników aprowizowania w formacie CSV lub JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Uzyskiwanie dostępu do dzienników z Azure Portal
Dostęp do dzienników aprowizowania można  uzyskać, wybierając pozycję Dzienniki aprowowania w sekcji Monitorowanie Azure Active Directory **okienku** [Azure Portal.](https://portal.azure.com)  Niektóre rekordy aprowizowania mogą pojawić się w portalu po maksymalnie dwóch godzinach.

![Zrzut ekranu przedstawiający wybrane opcje uzyskiwania dostępu do dzienników aprowowania.](./media/concept-provisioning-logs/access-provisioning-logs.png "Dzienniki aprowizowania")


Dziennik aprowizowania ma domyślny widok listy, który pokazuje:

- Tożsamość
- Akcja
- System źródłowy
- System docelowy
- Stan
- Data


![Zrzut ekranu przedstawiający domyślne kolumny w dzienniku aprowizowania.](./media/concept-provisioning-logs/default-columns.png "Kolumny domyślne")

Widok listy można dostosować, wybierając pozycję **Kolumny** na pasku narzędzi.

![Zrzut ekranu przedstawiający przycisk dostosowywania kolumn.](./media/concept-provisioning-logs/column-chooser.png "S wyboru kolumn")

Ten obszar umożliwia wyświetlanie dodatkowych pól lub usuwanie pól, które są już wyświetlane.

![Zrzut ekranu przedstawiający dostępne kolumny z wybranymi.](./media/concept-provisioning-logs/available-columns.png "Dostępne kolumny")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Zrzut ekranu przedstawiający szczegółowe informacje.](./media/concept-provisioning-logs/steps.png "Filtr")


## <a name="filter-provisioning-activities"></a>Filtrowanie działań aprowizowania

Dane aprowizowania można filtrować. Niektóre wartości filtrów są wypełniane dynamicznie na podstawie dzierżawy. Jeśli na przykład nie masz żadnych zdarzeń "utwórz" w dzierżawie, nie będzie dostępna opcja Utwórz **filtr.**

W widoku domyślnym można wybrać następujące filtry:

- **Tożsamość**
- **Data**
- **Stan**
- **Akcja**


![Zrzut ekranu przedstawiający wartości filtru.](./media/concept-provisioning-logs/default-filter.png "Filtr")

Filtr **Tożsamość** umożliwia określenie nazwy lub tożsamości, która Cię zależy. Ta tożsamość może być użytkownikiem, grupą, rolą lub innym obiektem. 

Możesz wyszukiwać według nazwy lub identyfikatora obiektu. Identyfikator różni się w zależności od scenariusza. Na przykład podczas aprowizowania obiektu z usługi Azure AD do usługi Salesforce identyfikatorem źródłowym jest identyfikator obiektu użytkownika w usłudze Azure AD. Identyfikator docelowy jest identyfikatorem użytkownika w u usługi Salesforce. Podczas aprowizowania z produktu Workday do usługi Active Directory identyfikatorem źródłowym jest identyfikator pracownika procesu roboczego produktu Workday. 

> [!NOTE]
> Nazwa użytkownika może nie zawsze być obecna w kolumnie **Tożsamość.** Zawsze będzie jeden identyfikator. 


Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych. Możliwe wartości:

- 1 miesiąc
- 7 dni
- 30 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego okresu czasu można skonfigurować datę rozpoczęcia i datę zakończenia.

Filtr **Stan** umożliwia wybranie:

- **Wszystko**
- **Powodzenie**
- **Niepowodzenie**
- **Pominięto**

Filtr **Akcja** umożliwia filtrowanie tych akcji:

- **Utwórz** 
- **Aktualizowanie**
- **Usuwanie**
- **Wyłącz**
- **Inne**

Oprócz filtrów widoku domyślnego można ustawić następujące filtry.

![Zrzut ekranu przedstawiający pola, które można dodać jako filtry.](./media/concept-provisioning-logs/add-filter.png "Wybieranie pola")

- **Identyfikator zadania:** unikatowy identyfikator zadania jest skojarzony z każdą aplikacją, dla których włączono aprowizowanie.   

- **Identyfikator cyklu:** identyfikator cyklu unikatowo identyfikuje cykl aprowrowowania. Możesz udostępnić ten identyfikator pomocy technicznej produktu, aby sprawdzić cykl, w którym wystąpiło to zdarzenie.

- **Identyfikator zmiany:** identyfikator zmiany jest unikatowym identyfikatorem zdarzenia aprowizowania. Możesz udostępnić ten identyfikator pomocy technicznej produktu, aby odszukać zdarzenie aprowowania.   

- **System źródłowy:** możesz określić, gdzie jest aprowizowana tożsamość. Na przykład podczas aprowizowania obiektu z usługi Azure AD do usługi ServiceNow systemem źródłowym jest usługa Azure AD. 

- **System docelowy:** możesz określić, gdzie jest aprowizowana tożsamość. Na przykład podczas aprowizowania obiektu z usługi Azure AD do usługi ServiceNow system docelowy to ServiceNow. 

- **Aplikacja:** można wyświetlać tylko rekordy aplikacji o nazwie wyświetlanej zawierającej określony ciąg.

## <a name="provisioning-details"></a>Szczegóły aprowowania 

Po wybraniu elementu w widoku listy aprowizowania można uzyskać więcej szczegółowych informacji na temat tego elementu. Szczegóły są pogrupowane na poniższych kartach.

![Zrzut ekranu przedstawiający cztery karty zawierające szczegóły aprowowania.](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")

- **Kroki:** zawiera opis czynności, które należy wykonać w celu aprowizowanie obiektu. Aprowizowanie obiektu może składać się z czterech kroków:
  
  1. Zaimportuj obiekt .
  1. Ustal, czy obiekt znajduje się w zakresie.
  1. Dopasowanie obiektu między obiektem źródłowym i docelowym.
  1. Aprowizować obiekt (tworzyć, aktualizować, usuwać lub wyłączać).

  ![Zrzut ekranu przedstawia kroki aprowizowania na karcie Kroki.](./media/concept-provisioning-logs/steps.png "Filtr")

- **Rozwiązywanie problemów & zaleceniami:** zawiera kod błędu i przyczynę. Informacje o błędzie są dostępne tylko w przypadku wystąpienia błędu.

- **Zmodyfikowane właściwości:** pokazuje starą wartość i nową wartość. Jeśli nie ma starej wartości, ta kolumna jest pusta.

- **Podsumowanie:** zawiera omówienie tego, co się stało, oraz identyfikatory obiektu w systemach źródłowym i docelowym.

## <a name="download-logs-as-csv-or-json"></a>Pobieranie dzienników w formacie CSV lub JSON

Dzienniki aprowizowania można pobrać do późniejszego użycia, przechodząc do dzienników w Azure Portal i wybierając pozycję **Pobierz.** Plik zostanie odfiltrowany na podstawie wybranych kryteriów filtrowania. Aby zmniejszyć rozmiar i czas pobierania, należy jak najbardziej konkretną liczbę filtrów. 

Plik CSV do pobrania zawiera trzy pliki:

* **ProvisioningLogs:** pobiera wszystkie dzienniki, z wyjątkiem kroków aprowologii i zmodyfikowanych właściwości.
* **ProvisioningLogs_ProvisioningSteps:** zawiera kroki aprowizowania i identyfikator zmiany. Możesz użyć identyfikatora zmiany, aby dołączyć zdarzenie do dwóch pozostałych plików.
* **ProvisioningLogs_ModifiedProperties:** zawiera zmienione atrybuty i identyfikator zmiany. Możesz użyć identyfikatora zmiany, aby dołączyć zdarzenie do dwóch pozostałych plików.

#### <a name="open-the-json-file"></a>Otwieranie pliku JSON
Aby otworzyć plik JSON, użyj edytora tekstów, takiego [jak Microsoft Visual Studio Code.](https://aka.ms/vscode) Visual Studio Code plik jest łatwiejszy do odczytania dzięki udostępnianiu wyróżniania składni. Plik JSON można również otworzyć przy użyciu przeglądarek w formacie niezmiennym, takim [jak Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Prettify the JSON file (Prettify plik JSON)
Plik JSON jest pobierany w formacie minimalnym, aby zmniejszyć rozmiar pobierania. Ten format może sprawić, że ładunek będzie trudny do odczytania. Zapoznaj się z dwiema opcjami, aby prettify pliku:

- Użyj [Visual Studio Code, aby sformatować format JSON.](https://code.visualstudio.com/docs/languages/json#_formatting)

- Użyj programu PowerShell, aby sformatować format JSON. Ten skrypt wyprowadzi dane wyjściowe w formacie JSON, który zawiera tabulatory i spacje: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analizowanie pliku JSON

Oto kilka przykładowych poleceń do pracy z plikiem JSON przy użyciu programu PowerShell. Możesz użyć dowolnego języka programowania, który ci się dobrze przyjmuje.  

Najpierw [odczytaj plik JSON,](/powershell/module/microsoft.powershell.utility/convertfrom-json) uruchamiając to polecenie:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Teraz możesz analizujeć dane zgodnie ze swoim scenariuszem. Oto kilka przykładów: 

- Wy wyjściowe wszystkie identyfikatory zadań w pliku JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Dane wyjściowe wszystkich identyfikatorów zmian dla zdarzeń, w których akcja to "create":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Co należy wiedzieć

Oto kilka wskazówek i kwestii dotyczących aprowizowania raportów:

- W Azure Portal są przechowywane zgłoszone dane aprowowania przez 30 dni, jeśli masz wersję Premium, i przez 7 dni, jeśli masz bezpłatną wersję. Dzienniki aprowizowania można opublikować w [uciecie usługi Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) w celu przechowywania ich dłużej niż 30 dni. 

- Atrybutu change ID można użyć jako unikatowego identyfikatora. Jest to przydatne na przykład w przypadku interakcji z pomocą techniczną produktu.

- Pominięte zdarzenia mogą być dostępne dla użytkowników, którzy nie znajdują się w zakresie. Jest to oczekiwane, zwłaszcza gdy zakres synchronizacji jest ustawiony na wszystkich użytkowników i grupy. Usługa oceni wszystkie obiekty w dzierżawie, nawet te, które są poza zakresem. 

- Dzienniki aprowizowania są obecnie niedostępne w chmurze dla instytucji rządowych. Jeśli nie możesz uzyskać dostępu do dzienników aprowizowania, użyj dzienników inspekcji jako tymczasowego obejścia. 

- Dzienniki aprowizowania nie pokazują importów ról (dotyczy usług AWS, Salesforce i Zendesk). Dzienniki importów ról można znaleźć w dziennikach inspekcji. 

## <a name="error-codes"></a>Kody błędów

Skorzystaj z poniższej tabeli, aby lepiej zrozumieć sposób rozwiązywania błędów, które można znaleźć w dziennikach aprowowania. W przypadku brakujących kodów błędów podaj opinię, korzystając z linku w dolnej części tej strony. 

|Kod błędu|Opis|
|---|---|
|Conflict, EntryConflict|Popraw sprzeczne wartości atrybutów w usłudze Azure AD lub aplikacji. Możesz też przejrzeć konfigurację pasującego atrybutu, jeśli konto użytkownika powodujące konflikt miało zostać dopasowane i zajęte. Zapoznaj się z [dokumentacją,](../app-provisioning/customize-application-attributes.md) aby uzyskać więcej informacji na temat konfigurowania pasujących atrybutów.|
|TooManyRequests|Aplikacja docelowa odrzuciła tę próbę zaktualizowania użytkownika, ponieważ jest przeciążona i odbiera zbyt wiele żądań. Nie trzeba nic robić. Ta próba zostanie automatycznie wycofana. Firma Microsoft została również powiadomiona o tym problemie.|
|InternalServerError |Aplikacja docelowa zwróciła nieoczekiwany błąd. Problem z usługą aplikacji docelowej może uniemożliwiać jego działanie. Ta próba zostanie automatycznie wycofana w ciągu 40 minut.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Usługa Azure AD została uwierzytelniona za pomocą aplikacji docelowej, ale nie ma autoryzacji do przeprowadzenia aktualizacji. Przejrzyj wszystkie instrukcje dostarczone przez aplikację docelową wraz z odpowiednim samouczkiem [aplikacji](../saas-apps/tutorial-list.md).|
|UnprocessableEntity|Aplikacja docelowa zwróciła nieoczekiwaną odpowiedź. Konfiguracja aplikacji docelowej może być poprawna lub problem z usługą aplikacji docelowej może uniemożliwiać jego działanie.|
|WebExceptionProtocolError |Wystąpił błąd protokołu HTTP podczas nawiązywania połączenia z aplikacją docelową. Nie trzeba nic robić. Ta próba zostanie automatycznie wycofana w ciągu 40 minut.|
|InvalidAnchor|Użytkownik, który został wcześniej utworzony lub dorównany przez usługę aprowizowania, już nie istnieje. Upewnij się, że użytkownik istnieje. Aby wymusić nowe dopasowanie wszystkich użytkowników, użyj interfejsu API Microsoft Graph, aby [ponownie uruchomić zadanie](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). <br><br>Ponowne uruchomienie aprowizowania spowoduje wyzwolenie początkowego cyklu, który może zająć trochę czasu. Ponowne uruchomienie aprowizowania powoduje również usunięcie pamięci podręcznej używanej przez usługę aprowizowania do działania. Oznacza to, że należy ponownie ocenić wszystkich użytkowników i grupy w dzierżawie, a niektóre zdarzenia aprowowania mogą zostać porzucone.|
|NotImplemented | Aplikacja docelowa zwróciła nieoczekiwaną odpowiedź. Konfiguracja aplikacji może być poprawna lub problem z usługą aplikacji docelowej może uniemożliwiać jego działanie. Przejrzyj wszystkie instrukcje dostarczone przez aplikację docelową wraz z odpowiednim samouczkiem [aplikacji](../saas-apps/tutorial-list.md). |
|MandatoryFieldsMissing, MissingValues |Nie można utworzyć użytkownika, ponieważ brakuje wymaganych wartości. Popraw brakujące wartości atrybutów w rekordzie źródłowym lub przejrzyj konfigurację pasujących atrybutów, aby upewnić się, że wymagane pola nie zostaną pominięte. [Dowiedz się więcej](../app-provisioning/customize-application-attributes.md) o konfigurowaniu pasujących atrybutów.|
|SchemaAttributeNotFound |Nie można wykonać operacji, ponieważ określono atrybut, który nie istnieje w aplikacji docelowej. Zapoznaj się z [dokumentacją](../app-provisioning/customize-application-attributes.md) dotyczącą dostosowywania atrybutów i upewnij się, że konfiguracja jest poprawna.|
|InternalError |Wystąpił wewnętrzny błąd usługi w usłudze aprowizowania usługi Azure AD. Nie trzeba nic robić. Ta próba zostanie automatycznie ponoowana w ciągu 40 minut.|
|InvalidDomain |Nie można wykonać operacji, ponieważ wartość atrybutu zawiera nieprawidłową nazwę domeny. Zaktualizuj nazwę domeny użytkownika lub dodaj ją do listy dozwolonych w aplikacji docelowej. |
|Limit czasu |Nie można ukończyć operacji, ponieważ odpowiedź aplikacji docelowej trwała zbyt długo. Nie trzeba nic robić. Ta próba zostanie automatycznie ponoowana w ciągu 40 minut.|
|LicenseLimitExceeded|Nie można utworzyć użytkownika w aplikacji docelowej, ponieważ nie ma dostępnych licencji dla tego użytkownika. Pozyskaj więcej licencji dla aplikacji docelowej. Możesz też przejrzeć przypisania użytkowników i konfigurację mapowania atrybutów, aby upewnić się, że do odpowiednich użytkowników przypisano poprawne atrybuty.|
|DuplicateTargetEntries  |Nie można ukończyć operacji, ponieważ znaleziono więcej niż jednego użytkownika w aplikacji docelowej ze skonfigurowanymi pasującymi atrybutami. Usuń zduplikowanego użytkownika z aplikacji docelowej lub [skonfiguruj ponownie mapowania atrybutów](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Nie można ukończyć operacji, ponieważ znaleziono więcej niż jednego użytkownika ze skonfigurowanymi pasującymi atrybutami. Usuń zduplikowanego użytkownika lub [ponownie skonfiguruj mapowania atrybutów](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Podczas oceny każdego użytkownika system próbuje zaimportować użytkownika z systemu źródłowego. Ten błąd występuje często, gdy importowany użytkownik nie ma pasującej właściwości zdefiniowanej w mapowaniach atrybutów. Bez wartości obiektu użytkownika dla pasującego atrybutu system nie może ocenić zakresu, dopasowania ani wyeksportować zmian. Należy pamiętać, że obecność tego błędu nie wskazuje, że użytkownik znajduje się w zakresie, ponieważ nie oceniono jeszcze zakresu dla użytkownika.|
|EntrySynchronizationSkipped | Usługa aprowizowania pomyślnie zbadała system źródłowy i zidentyfikowała użytkownika. Nie zostały wykonane żadne dalsze działania na użytkowniku i zostały one pominięte. Użytkownik mógł być poza zakresem lub mógł już istnieć w systemie docelowym bez dodatkowych zmian.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Żądanie GET w celu pobrania użytkownika lub grupy otrzymało wielu użytkowników lub grupy w odpowiedzi. System oczekuje, że w odpowiedzi otrzyma tylko jednego użytkownika lub grupę. [](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)Jeśli na przykład zrobisz żądanie GET w celu pobrania grupy i udostępnisz filtr w celu wykluczenia elementów członkowskich, a punkt końcowy System dla zarządzania tożsamościami między domenami (SCIM) zwróci elementy członkowskie, wystąpi ten błąd.|

## <a name="next-steps"></a>Następne kroki

* [Sprawdzanie stanu aprowizowania użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problem z konfigurowaniem aprowizowania użytkowników w aplikacji z galerii usługi Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [interfejs Graph API do aprowizowania dzienników](/graph/api/resources/provisioningobjectsummary)