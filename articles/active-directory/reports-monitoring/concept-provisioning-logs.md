---
title: Omówienie dzienników aprowizacji w Azure Portal (wersja zapoznawcza) | Microsoft Docs
description: Zapoznaj się z wprowadzeniem do obsługi raportów dzienników w Azure Active Directory przez Azure Portal.
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
ms.openlocfilehash: 7d8c4876faf9ebc2619309aa0095a8ffe1e9e93d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500550"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Omówienie dzienników aprowizacji w Azure Portal (wersja zapoznawcza)

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- Działanie: 
    - **Logowania**: informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - [Dzienniki inspekcji](concept-audit-logs.md): informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzane aplikacje i działania w katalogu.
    - **Dzienniki aprowizacji**: działania systemowe dotyczące użytkowników, grup i ról, które są obsługiwane przez usługę aprowizacji usługi Azure AD. 

- Bezpieczeństw 
    - **Ryzykowne logowania**: [ryzykowne logowanie](../identity-protection/overview-identity-protection.md) jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani w celu ryzyka**: [użytkownik](../identity-protection/overview-identity-protection.md) będący ryzykiem jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten temat zawiera omówienie dzienników aprowizacji. Dzienniki zawierają odpowiedzi na pytania, takie jak: 

* Które grupy zostały pomyślnie utworzone w usługi ServiceNow?
* Jakie użytkowników zostało pomyślnie usuniętych z firmy Adobe?
* Co użytkownicy z produktu Workday zostały pomyślnie utworzone w Active Directory? 

## <a name="prerequisites"></a>Wymagania wstępne

Ci użytkownicy mogą uzyskać dostęp do danych w dziennikach aprowizacji:

* Właściciele aplikacji (dzienniki dla własnych aplikacji)
* Użytkownicy w rolach administrator zabezpieczeń, czytelnik zabezpieczeń, czytelnik raportu, operator zabezpieczeń, administrator aplikacji i administrator aplikacji w chmurze
* Użytkownicy w roli niestandardowej z [uprawnieniem provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Administratorzy globalni


Aby można było wyświetlić raport aktywności aprowizacji, dzierżawa musi mieć skojarzoną z nią licencję Azure AD — wersja Premium. Aby uaktualnić wersję usługi Azure AD, zobacz [wprowadzenie do Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Sposoby współpracy z dziennikami aprowizacji 
Klienci mogą korzystać z dzienników aprowizacji na cztery sposoby:

- Uzyskiwanie dostępu do dzienników z Azure Portal, zgodnie z opisem w następnej sekcji.
- Przesyłanie strumieniowe dzienników aprowizacji do [Azure monitor](../app-provisioning/application-provisioning-log-analytics.md). Ta metoda pozwala na Rozszerzone przechowywanie danych i tworzenie niestandardowych pulpitów nawigacyjnych, alertów i zapytań.
- Wykonywanie zapytania dotyczącego [interfejsu API Microsoft Graph](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) dla dzienników aprowizacji.
- Pobieranie dzienników aprowizacji jako pliku CSV lub JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Uzyskaj dostęp do dzienników z Azure Portal
Dostęp do dzienników aprowizacji można uzyskać, wybierając pozycję **dzienniki aprowizacji** w sekcji **monitorowanie** okienka **Azure Active Directory** w [Azure Portal](https://portal.azure.com). Niektóre rekordy aprowizacji mogą pojawić się w portalu dopiero po dwóch godzinach.

![Zrzut ekranu pokazujący opcje uzyskiwania dostępu do dzienników aprowizacji.](./media/concept-provisioning-logs/access-provisioning-logs.png "Dzienniki aprowizowania")


Dziennik aprowizacji zawiera domyślny widok listy, który pokazuje:

- Tożsamość
- Akcja
- System źródłowy
- System docelowy
- Stan
- Data


![Zrzut ekranu pokazujący domyślne kolumny w dzienniku aprowizacji.](./media/concept-provisioning-logs/default-columns.png "Kolumny domyślne")

Widok listy można dostosować, zaznaczając **kolumny** na pasku narzędzi.

![Zrzut ekranu pokazujący przycisk dostosowywania kolumn.](./media/concept-provisioning-logs/column-chooser.png "Wybór kolumn")

Ten obszar umożliwia wyświetlenie dodatkowych pól lub usunięcie pól, które są już wyświetlane.

![Zrzut ekranu, na którym są wyświetlane dostępne kolumny z wybraną opcją.](./media/concept-provisioning-logs/available-columns.png "Dostępne kolumny")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Zrzut ekranu przedstawiający szczegółowe informacje.](./media/concept-provisioning-logs/steps.png "Filtr")


## <a name="filter-provisioning-activities"></a>Filtrowanie działań aprowizacji

Możesz filtrować dane aprowizacji. Niektóre wartości filtru są dynamicznie wypełniane na podstawie dzierżawy. Jeśli na przykład nie masz żadnych zdarzeń "Create" w dzierżawie, nie będzie dostępna opcja **Create** Filter.

W widoku domyślnym można wybrać następujące filtry:

- **Tożsamość**
- **Data**
- **Stan**
- **Akcja**


![Zrzut ekranu pokazujący wartości filtru.](./media/concept-provisioning-logs/default-filter.png "Filtr")

Filtr **tożsamości** umożliwia określenie nazwy lub tożsamości, o której Cię interesują. Ta tożsamość może być użytkownikiem, grupą, rolą lub innym obiektem. 

Można wyszukiwać według nazwy lub identyfikatora obiektu. Identyfikator różni się w zależności od scenariusza. Na przykład w przypadku aprowizacji obiektu z usługi Azure AD do usług Salesforce identyfikator źródłowy jest IDENTYFIKATORem obiektu użytkownika w usłudze Azure AD. Identyfikator docelowy jest IDENTYFIKATORem użytkownika w usłudze Salesforce. Po zainicjowaniu obsługi administracyjnej od dnia roboczego do Active Directory identyfikator źródła to identyfikator pracownika procesu roboczego programu Workday. 

> [!NOTE]
> Nazwa użytkownika może nie zawsze występować w kolumnie **tożsamość** . Zawsze będzie istnieć jeden identyfikator. 


Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych. Możliwe wartości:

- 1 miesiąc
- 7 dni
- 30 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu można skonfigurować datę początkową i datę końcową.

Filtr **stanu** umożliwia wybranie:

- **Wszystko**
- **Powodzenie**
- **Spraw**
- **Pominięto**

Filtr **akcji** umożliwia filtrowanie następujących akcji:

- **Utwórz** 
- **Aktualizowanie**
- **Usuwanie**
- **Wyłącz**
- **Inne**

Oprócz filtrów widoku domyślnego można ustawić następujące filtry.

![Zrzut ekranu pokazujący pola, które można dodać jako filtry.](./media/concept-provisioning-logs/add-filter.png "Wybierz pole")

- **Identyfikator zadania**: unikatowy identyfikator zadania jest skojarzony z każdą aplikacją, dla której włączono obsługę administracyjną.   

- **Identyfikator cyklu**: Identyfikator cyklu jednoznacznie identyfikuje cykl aprowizacji. Ten identyfikator można udostępnić z pomocą techniczną produktu, aby wyszukać cykl, w którym wystąpiło to zdarzenie.

- **Identyfikator zmiany**: Identyfikator zmiany jest unikatowym identyfikatorem dla zdarzenia aprowizacji. Ten identyfikator można udostępnić z pomocą techniczną produktu w celu wyszukania zdarzenia aprowizacji.   

- **System źródłowy**: możesz określić miejsce, z którego ma zostać zainicjowana tożsamość. Na przykład w przypadku aprowizacji obiektu z usługi Azure AD do usługi ServiceNow, system źródłowy to Azure AD. 

- **System docelowy**: możesz określić miejsce, do którego ma zostać zainicjowana tożsamość. Na przykład w przypadku aprowizacji obiektu z usługi Azure AD do usługi ServiceNow system docelowy to usługi ServiceNow. 

- **Aplikacja**: można wyświetlić tylko rekordy aplikacji o nazwie wyświetlanej zawierającej określony ciąg.

## <a name="provisioning-details"></a>Szczegóły aprowizacji 

Po wybraniu elementu w widoku listy aprowizacji uzyskasz więcej szczegółowych informacji na temat tego elementu. Szczegóły są pogrupowane na następujące karty.

![Zrzut ekranu przedstawiający cztery karty zawierające szczegóły aprowizacji.](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")

- **Kroki**: zawiera opis czynności podejmowanych w celu udostępnienia obiektu. Inicjowanie obsługi obiektu może składać się z czterech kroków:
  
  1. Zaimportuj obiekt.
  1. Ustal, czy obiekt znajduje się w zakresie.
  1. Dopasowuje obiekt między źródłowym i docelowym.
  1. Inicjowanie obsługi administracyjnej obiektu (tworzenie, aktualizowanie, usuwanie lub wyłączanie).

  ![Zrzut ekranu przedstawia kroki inicjowania obsługi na karcie kroki.](./media/concept-provisioning-logs/steps.png "Filtr")

- **Rozwiązywanie problemów & zalecenia**: zawiera kod błędu i przyczynę. Informacje o błędzie są dostępne tylko wtedy, gdy wystąpi awaria.

- **Zmodyfikowano właściwości**: pokazuje starą wartość i nową wartość. Jeśli nie ma starej wartości, ta kolumna jest pusta.

- **Podsumowanie**: zawiera przegląd informacji o tym, co się stało i identyfikatory dla obiektu w systemie źródłowym i docelowym.

## <a name="download-logs-as-csv-or-json"></a>Pobierz dzienniki jako plik CSV lub JSON

Dzienniki aprowizacji można pobrać do późniejszego użycia, przechodząc do dzienników w Azure Portal i wybierając pozycję **Pobierz**. Plik zostanie przefiltrowany na podstawie wybranych kryteriów filtrowania. Ustaw odpowiednie filtry jako możliwe do zmniejszenia rozmiaru i czasu pobierania. 

Pobieranie woluminów CSV obejmuje trzy pliki:

* **ProvisioningLogs**: Pobiera wszystkie dzienniki, z wyjątkiem kroków aprowizacji i zmodyfikowanych właściwości.
* **ProvisioningLogs_ProvisioningSteps**: zawiera kroki inicjowania obsługi administracyjnej i identyfikator zmiany. Możesz użyć identyfikatora zmiany, aby dołączyć zdarzenie z innymi dwoma plikami.
* **ProvisioningLogs_ModifiedProperties**: zawiera atrybuty, które zostały zmienione wraz z identyfikatorem zmiany. Możesz użyć identyfikatora zmiany, aby dołączyć zdarzenie z innymi dwoma plikami.

#### <a name="open-the-json-file"></a>Otwórz plik JSON
Aby otworzyć plik JSON, użyj edytora tekstów, takiego jak [kod Microsoft Visual Studio](https://aka.ms/vscode). Visual Studio Code ułatwia odczytywanie pliku, zapewniając wyróżnianie składni. Możesz również otworzyć plik JSON przy użyciu przeglądarek w formacie nieedytowalnym, takim jak [Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Prettify plik JSON
Plik JSON zostanie pobrany w formacie zminimalizowanego, aby zmniejszyć rozmiar pobierania. Ten format może spowodować, że ładunek będzie trudno odczytać. Zapoznaj się z dwoma opcjami, aby Prettify plik:

- Użyj [Visual Studio Code, aby sformatować kod JSON](https://code.visualstudio.com/docs/languages/json#_formatting).

- Sformatuj kod JSON przy użyciu programu PowerShell. Ten skrypt będzie wyprowadzał dane JSON w formacie zawierającym tabulatory i spacje: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analizowanie pliku JSON

Poniżej przedstawiono kilka przykładowych poleceń do pracy z plikiem JSON przy użyciu programu PowerShell. Możesz użyć dowolnego języka programowania, z którym masz doświadczenie.  

Najpierw [Przeczytaj plik JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) , uruchamiając następujące polecenie:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Teraz można analizować dane zgodnie z Twoim scenariuszem. Oto kilka przykładów: 

- Wyprowadź wszystkie identyfikatory zadań w pliku JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Wyprowadź wszystkie identyfikatory zmian dla zdarzeń, w których akcja została utworzona:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Co należy wiedzieć

Oto kilka porad i zagadnień dotyczących raportów dotyczących aprowizacji:

- W Azure Portal są przechowywane zgłoszone dane aprowizacji przez 30 dni, jeśli masz wersję Premium i 7 dni, jeśli masz bezpłatną wersję. Dzienniki aprowizacji można opublikować w celu [log Analytics](../app-provisioning/application-provisioning-log-analytics.md) przechowywania danych przez okres dłuższy niż 30 dni. 

- Można użyć atrybutu identyfikatora zmiany jako unikatowego identyfikatora. Jest to przydatne w przypadku współpracy z pomocą techniczną na przykład.

- W przypadku użytkowników, którzy nie znajdują się w zakresie, mogą pojawić się pominięte zdarzenia. Jest to oczekiwane, szczególnie w przypadku, gdy zakres synchronizacji jest ustawiony na wszystkich użytkowników i grupy. Usługa oceni wszystkie obiekty w dzierżawie, nawet te, które znajdują się poza zakresem. 

- Dzienniki aprowizacji są obecnie niedostępne w chmurze dla instytucji rządowych. Jeśli nie możesz uzyskać dostępu do dzienników aprowizacji, użyj dzienników inspekcji jako tymczasowego obejścia. 

- W dziennikach aprowizacji nie są wyświetlane Importy ról (dotyczy to AWS, Salesforce i systemu Zendesk). Dzienniki dotyczące importu ról można znaleźć w dziennikach inspekcji. 

## <a name="error-codes"></a>Kody błędów

Skorzystaj z poniższej tabeli, aby lepiej zrozumieć sposób rozwiązywania błędów znalezionych w dziennikach aprowizacji. W przypadku brakujących kodów błędów Prześlij opinię za pomocą linku w dolnej części tej strony. 

|Kod błędu|Opis|
|---|---|
|Konflikt, EntryConflict|Popraw sprzeczne wartości atrybutów w usłudze Azure AD lub aplikacji. Można też sprawdzić zgodną konfigurację atrybutów, jeśli konto użytkownika powodujące konflikt zostało dopasowane i przejęte. Zapoznaj się z [dokumentacją](../app-provisioning/customize-application-attributes.md) , aby uzyskać więcej informacji na temat konfigurowania pasujących atrybutów.|
|TooManyRequests|Aplikacja docelowa odrzuciła próbę zaktualizowania użytkownika, ponieważ jest przeciążona i otrzymuje zbyt wiele żądań. Nie ma nic do zrobienia. Ta próba zostanie automatycznie wycofana. Firma Microsoft otrzymała również powiadomienie o tym problemie.|
|InternalServerError |Aplikacja docelowa zwróciła nieoczekiwany błąd. Problem z usługą z aplikacją docelową może uniemożliwiać wykonanie tej działania. Ta próba zostanie automatycznie wycofana w ciągu 40 minut.|
|InsufficientRights, MethodNotAllowed, NotPermitted, nieautoryzowane| Usługa Azure AD została uwierzytelniona z aplikacją docelową, ale nie ma autoryzacji do wykonania tej aktualizacji. Przejrzyj wszelkie instrukcje dostarczone przez aplikację docelową wraz z odpowiednim [samouczekem](../saas-apps/tutorial-list.md)aplikacji.|
|UnprocessableEntity|Aplikacja docelowa zwróciła nieoczekiwaną odpowiedź. Konfiguracja aplikacji docelowej może być niepoprawna lub problem usługi z aplikacją docelową może uniemożliwiać wykonanie tej działania.|
|WebExceptionProtocolError |Wystąpił błąd protokołu HTTP podczas nawiązywania połączenia z aplikacją docelową. Nie ma nic do zrobienia. Ta próba zostanie automatycznie wycofana w ciągu 40 minut.|
|InvalidAnchor|Użytkownik, który został wcześniej utworzony lub dopasowany przez usługę aprowizacji, już nie istnieje. Upewnij się, że użytkownik istnieje. Aby wymusić nowe dopasowanie wszystkich użytkowników, należy [ponownie uruchomić zadanie](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)przy użyciu interfejsu API Microsoft Graph. <br><br>Ponowne uruchomienie aprowizacji wyzwoli cykl początkowy, który może zająć trochę czasu. Ponowne uruchomienie aprowizacji spowoduje również usunięcie pamięci podręcznej używanej przez usługę aprowizacji do działania. Oznacza to, że wszyscy użytkownicy i grupy w dzierżawie będą musieli ponownie ocenić, a niektóre zdarzenia aprowizacji mogą zostać porzucone.|
|Nie zaimplementowano | Aplikacja docelowa zwróciła nieoczekiwaną odpowiedź. Konfiguracja aplikacji może być niepoprawna lub problem z usługą z aplikacją docelową może uniemożliwiać wykonanie tej działania. Przejrzyj wszelkie instrukcje dostarczone przez aplikację docelową wraz z odpowiednim [samouczekem](../saas-apps/tutorial-list.md)aplikacji. |
|MandatoryFieldsMissing, MissingValues |Nie można utworzyć użytkownika, ponieważ brakuje wymaganych wartości. Popraw brakujące wartości atrybutów w rekordzie źródłowym lub przejrzyj zgodną konfigurację atrybutów, aby upewnić się, że wymagane pola nie zostały pominięte. [Dowiedz się więcej](../app-provisioning/customize-application-attributes.md) o konfigurowaniu pasujących atrybutów.|
|SchemaAttributeNotFound |Nie można wykonać operacji, ponieważ określono atrybut, który nie istnieje w aplikacji docelowej. Zapoznaj się z [dokumentacją](../app-provisioning/customize-application-attributes.md) dotyczącą dostosowywania atrybutów i upewnij się, że konfiguracja jest poprawna.|
|InternalError |Wystąpił wewnętrzny błąd usługi w usłudze Azure AD Provisioning. Nie ma nic do zrobienia. Ta próba zostanie ponowiona automatycznie w ciągu 40 minut.|
|InvalidDomain |Nie można wykonać operacji, ponieważ wartość atrybutu zawiera nieprawidłową nazwę domeny. Zaktualizuj nazwę domeny użytkownika lub Dodaj ją do listy dozwolonych w aplikacji docelowej. |
|Limit czasu |Nie można ukończyć operacji, ponieważ aplikacja docelowa nie może odpowiedzieć. Nie ma nic do zrobienia. Ta próba zostanie ponowiona automatycznie w ciągu 40 minut.|
|LicenseLimitExceeded|Nie można utworzyć użytkownika w aplikacji docelowej, ponieważ nie ma żadnych dostępnych licencji dla tego użytkownika. Uzyskaj więcej licencji dla aplikacji docelowej. Można też sprawdzić przypisania użytkowników i konfigurację mapowania atrybutów, aby upewnić się, że poprawni użytkownicy są przypisani przy użyciu poprawnych atrybutów.|
|DuplicateTargetEntries  |Nie można ukończyć operacji, ponieważ znaleziono więcej niż jednego użytkownika w aplikacji docelowej ze skonfigurowanymi pasującymi atrybutami. Usuń zduplikowanego użytkownika z aplikacji docelowej lub [Skonfiguruj ponownie mapowania atrybutów](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Nie można ukończyć operacji, ponieważ znaleziono więcej niż jednego użytkownika ze skonfigurowanymi pasującymi atrybutami. Usuń zduplikowanego użytkownika lub [Skonfiguruj ponownie mapowania atrybutów](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Podczas oceniania każdego użytkownika system podejmie próbę zaimportowania użytkownika z systemu źródłowego. Ten błąd występuje często, gdy użytkownik, który jest importowany, nie ma właściwości pasującej zdefiniowanej w mapowaniu atrybutów. Bez wartości znajdującej się w obiekcie użytkownika dla pasującego atrybutu System nie może oszacować zakresu, dopasowywania ani eksportu zmian. Należy zauważyć, że obecność tego błędu nie wskazuje, że użytkownik należy do zakresu, ponieważ nie oceniono jeszcze zakresu dla użytkownika.|
|EntrySynchronizationSkipped | Usługa aprowizacji pomyślnie zbadał system źródłowy i zidentyfikował użytkownika. Nie wykonano żadnych dalszych akcji dla użytkownika i zostały one pominięte. Użytkownik mógł znajdować się poza zakresem lub użytkownik mógł już znajdować się w systemie docelowym bez konieczności wprowadzania dalszych zmian.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Żądanie GET umożliwiające pobranie użytkownika lub grupy odebrało wielu użytkowników lub grupy w odpowiedzi. System oczekuje na odebranie tylko jednego użytkownika lub grupy w odpowiedzi. Jeśli [na przykład](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)zostanie wysłane żądanie Get w celu pobrania grupy i przekroczenia filtru, aby wykluczyć członków, a punkt końcowy zarządzania tożsamościami między domenami (standard scim) zwróci członków, zostanie wyświetlony ten błąd.|

## <a name="next-steps"></a>Następne kroki

* [Sprawdź stan aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [interfejs API programu Graph do obsługi dzienników aprowizacji](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)