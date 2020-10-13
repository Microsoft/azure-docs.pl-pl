---
title: Informacje o migracji Azure Monitor alertów
description: Informacje na temat działania migracji alertów i rozwiązywania problemów.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 6509425f11b09a2fa5229f9dd68a508241391925
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875924"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Omówienie opcji migracji do nowszych alertów

Alerty klasyczne są [wycofywane](./monitoring-classic-retirement.md), mimo że nadal są ograniczone dla zasobów, które jeszcze nie obsługują nowych alertów. Nowa data zostanie ogłoszona wkrótce w przypadku migracji pozostałych alertów, [Azure Government chmury](../../azure-government/documentation-government-welcome.md)i [platformy Azure w Chinach](https://docs.azure.cn/).

W tym artykule wyjaśniono, jak działa ręczne migrowanie i dobrowolne narzędzie do migracji, które zostanie użyte do migracji pozostałych reguł alertów. Opisano w nim również sposoby rozwiązywania niektórych typowych problemów.

> [!IMPORTANT]
> Migracja nie ma wpływu na alerty dziennika aktywności (w tym alerty kondycji usługi) i alerty dziennika. Migracja dotyczy tylko klasycznych reguł alertów opisanych [tutaj](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

> [!NOTE]
> Jeśli klasyczne reguły alertów są nieprawidłowe, tj. znajdują się na [przestarzałych metrykach](#classic-alert-rules-on-deprecated-metrics) lub zasobach, które zostały usunięte, nie zostaną zmigrowane i nie będą dostępne po wycofaniu usługi.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Ręczne Migrowanie alertów klasycznych do nowszych alertów

Klienci, którzy chcą ręcznie migrować pozostałe alerty, mogą już skorzystać z następujących sekcji. Te sekcje również definiują metryki, które zostały wycofane przez dostawcę zasobów i nie mogą być bezpośrednio migrowane.

### <a name="guest-metrics-on-virtual-machines"></a>Metryki gościa na maszynach wirtualnych

Aby można było tworzyć nowe alerty metryki dla metryk gościa, należy wysłać metryki gościa do magazynu metryk niestandardowych Azure Monitor. Postępuj zgodnie z poniższymi instrukcjami, aby włączyć ujścia Azure Monitor w ustawieniach diagnostycznych:

- [Włączanie metryk gościa dla maszyn wirtualnych z systemem Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Włączanie metryk gościa dla maszyn wirtualnych z systemem Linux](collect-custom-metrics-linux-telegraf.md)

Po wykonaniu tych kroków można utworzyć nowe alerty metryki dla metryk gościa. Po utworzeniu nowych alertów metryk można usunąć alerty klasyczne.

### <a name="storage-account-metrics"></a>Metryki konta magazynu

Wszystkie klasyczne alerty na kontach magazynu można migrować z wyjątkiem alertów dotyczących tych metryk:

- PercentAuthorizationError
- PercentClientOtherError
- Wzrost percentnetworkerror
- PercentServerOtherError
- PercentSuccess
- Wzrost percentthrottlingerror
- Wzrost percenttimeouterror
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Reguły alertów klasycznych na metrykach procentowych muszą zostać zmigrowane na podstawie [mapowania między starymi i nowymi metrykami magazynu](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Wartości progowe muszą być odpowiednio modyfikowane, ponieważ nowa Metryka jest dostępna jako wartość bezwzględna.

Klasyczne reguły alertów w AnonymousThrottlingError, SASThrottlingError i ThrottlingError muszą zostać podzielone na dwa nowe alerty, ponieważ nie ma żadnej połączonej metryki, która zapewnia te same funkcje. Progi należy odpowiednio dostosować.

### <a name="cosmos-db-metrics"></a>Metryki usługi Cosmos DB

Wszystkie klasyczne alerty dotyczące Cosmos DB metryk można migrować z wyjątkiem alertów dotyczących tych metryk:

- Średnia liczba żądań na sekundę
- Poziom spójności
- Http 2xx
- Http 3xx
- Http 400
- HTTP 401
- Wewnętrzny błąd serwera
- Maksymalna liczba zużytych promocja na minutę
- Maksymalna liczba jednostek ru na sekundę
- Liczba żądań zakończonych niepowodzeniem Mongo
- Mongo Usuń Nieudane żądania
- Mongo Wstawianie nieudanych żądań
- Mongo inne żądania zakończone niepowodzeniem
- Mongo inne żądania
- Mongo inną liczbę żądań
- Nieudane żądania zapytania Mongo
- Nieudane żądania aktualizacji Mongo
- Obserwowane opóźnienie odczytu
- Obserwowane opóźnienie zapisu
- Dostępność usługi
- Pojemność magazynu
- Ograniczone żądania
- Łączna liczba żądań

Średnia liczba żądań na sekundę, poziom spójności, Maksymalna liczba zużytych promocja na minutę, maksymalna jednostek ru na sekundę, obserwowane opóźnienie odczytu, zaobserwowane opóźnienie zapisu, pojemność magazynu nie jest obecnie dostępna w [nowym systemie](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Alerty dotyczące metryk żądania, takich jak http 2xx, http 3xx, http 400, HTTP 401, wewnętrzny błąd serwera, dostępność usługi, żądania ograniczające i łączna liczba żądań nie są migrowane, ponieważ sposób liczenia żądań różni się od klasycznych metryk i nowych metryk. Alerty na tych należy ręcznie odtworzyć z uwzględnieniem progów.

Alerty w przypadku żądań Mongo zakończonych niepowodzeniem muszą być podzielone na wiele alertów, ponieważ nie ma żadnej połączonej metryki, która zapewnia te same funkcje. Progi należy odpowiednio dostosować.

### <a name="classic-compute-metrics"></a>Klasyczne metryki obliczeń

Jakiekolwiek alerty dotyczące klasycznych metryk obliczeniowych nie będą migrowane przy użyciu narzędzia migracji, ponieważ klasyczne zasoby obliczeniowe nie są jeszcze obsługiwane w przypadku nowych alertów. Obsługa nowych alertów dotyczących tych typów zasobów jest obecnie w publicznej wersji zapoznawczej, a klienci mogą ponownie tworzyć nowe równoważne reguły alertów na podstawie ich klasycznych reguł alertów.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasyczne reguły alertów dla przestarzałych metryk

Są to klasyczne reguły alertów dotyczące metryk, które były wcześniej obsługiwane, ale były ostatecznie przestarzałe. Niewielki odsetek klientów może mieć nieprawidłowe reguły klasycznego alertu na takich metrykach. Ponieważ te reguły alertów są nieprawidłowe, nie będą migrowane.

| Typ zasobu| Przestarzałe metryki |
|-------------|----------------- |
| Microsoft. DBforMySQL/serwery | compute_consumption_percent, compute_limit |
| Microsoft. DBforPostgreSQL/serwery | compute_consumption_percent, compute_limit |
| Microsoft. Network/adresów publicipaddress | defaultddostriggerrate |
| Microsoft. SQL/serwery/bazy danych | service_level_objective, storage_limit, storage_used, ograniczanie, dtu_consumption_percent, storage_used |
| Microsoft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft. Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Jak są tworzone równoważne nowe reguły alertów i grupy akcji

Narzędzie migracji umożliwia konwertowanie klasycznych reguł alertów na równoważne nowe reguły alertów i grupy akcji. W przypadku większości klasycznych reguł alertów równoważne nowe reguły alertów mają tę samą metrykę z tymi samymi właściwościami, takimi jak `windowSize` i `aggregationType` . Istnieje jednak kilka klasycznych reguł alertów dotyczących metryk, które mają inną równoważną metrykę w nowym systemie. Następujące zasady mają zastosowanie do migracji klasycznych alertów, chyba że określono w poniższej sekcji:

- **Częstotliwość**: definiuje, jak często klasyczna lub Nowa reguła alertu sprawdza warunek. `frequency`Reguły alertów klasycznych nie zostały konfigurowalne przez użytkownika i zawsze zostały 5 minut dla wszystkich typów zasobów, z wyjątkiem składników Application Insights, dla których miało 1 min. Tak więc częstotliwość równoważnych reguł jest również ustawiona na odpowiednio 5 minut i 1 min.
- **Typ agregacji**: definiuje sposób agregowania metryki w oknie zainteresowań. `aggregationType`Jest również taka sama między klasycznymi alertami i nowymi alertami dla większości metryk. W niektórych przypadkach, ponieważ Metryka różni się od alertów klasycznych, a nowe alerty, `aggregationType` są równoważne lub `primary Aggregation Type` zdefiniowane dla metryki.
- **Units**: Właściwość metryki, w której tworzony jest alert. Niektóre równoważne metryki mają różne jednostki. Próg jest odpowiednio dostosowany zgodnie z wymaganiami. Na przykład jeśli oryginalna Metryka zawiera sekundy jako jednostki, ale równoważna Nowa Metryka ma liczbę milisekund jako jednostki, pierwotny próg jest mnożony przez 1000 w celu zapewnienia tego samego zachowania.
- **Rozmiar okna**: definiuje okno, nad którym dane metryk są agregowane w celu porównania z progiem. W przypadku `windowSize` wartości standardowych, takich jak 5mins, 15mins, 30mins, 1-godzinnego, 3hours, 6 godzin, 12 godzin, 1 dzień, nie wprowadzono zmian w przypadku równoważnej nowej reguły alertu. W przypadku innych wartości `windowSize` jest wybierany najbliższy, który ma być używany. W przypadku większości klientów nie ma to wpływu na tę zmianę. W przypadku niewielkiej części klientów może być konieczne dostosowanie progu w celu uzyskania dokładnego zachowania.

W poniższych sekcjach podano szczegółowe informacje o metrykach, które mają inną równoważną metrykę w nowym systemie. Wszystkie metryki, które pozostają takie same dla klasycznych i nowych reguł alertów, nie są wymienione na liście. Listę metryk obsługiwanych w nowym systemie można znaleźć [tutaj](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft. StorageAccounts/usługi

W przypadku usług konta magazynu, takich jak obiekty blob, tabele, pliki i kolejki, następujące metryki są mapowane na równoważne metryki, jak pokazano poniżej:

| Metryka w klasycznych alertach | Równoważna Metryka w nowych alertach | Komentarze|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metryka transakcji z wymiarami "responsetype" = "AuthorizationError" i "Authentication" = "Anonymous"| |
| AnonymousClientOtherError | Metryka transakcji z wymiarami "responsetype" = "ClientOtherError" i "Authentication" = "Anonymous" | |
| AnonymousClientTimeOutError| Metryka transakcji z wymiarami "responsetype" = "ClientTimeOutError" i "Authentication" = "Anonymous" | |
| AnonymousNetworkError | Metryka transakcji z wymiarami "responsetype" = "NetworkError" i "Authentication" = "Anonymous" | |
| AnonymousServerOtherError | Metryka transakcji z wymiarami "responsetype" = "ServerOtherError" i "Authentication" = "Anonymous" | |
| AnonymousServerTimeOutError | Metryka transakcji z wymiarami "responsetype" = "ServerTimeOutError" i "Authentication" = "Anonymous" | |
| AnonymousSuccess | Metryka transakcji z wymiarami "responsetype" = "Success" i "Authentication" = "Anonymous" | |
| AuthorizationError | Metryka transakcji z wymiarami "responsetype" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Pojemność | BlobCapacity | Użyj `aggregationType` elementu "Average" zamiast "Last". Metryka dotyczy tylko usługi BLOB Services |
| ClientOtherError | Metryka transakcji z wymiarami "responsetype" = "ClientOtherError"  | |
| ClientTimeoutError | Metryka transakcji z wymiarami "responsetype" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Użyj `aggregationType` elementu "Average" zamiast "Last". Metryka dotyczy tylko usługi BLOB Services |
| NetworkError | Metryka transakcji z wymiarami "responsetype" = "NetworkError" | |
| ObjectCount | BlobCount| Użyj `aggregationType` elementu "Average" zamiast "Last". Metryka dotyczy tylko usługi BLOB Services |
| SASAuthorizationError | Metryka transakcji z wymiarami "responsetype" = "AuthorizationError" i "Authentication" = "SAS" | |
| SASClientOtherError | Metryka transakcji z wymiarami "responsetype" = "ClientOtherError" i "Authentication" = "SAS" | |
| SASClientTimeOutError | Metryka transakcji z wymiarami "responsetype" = "ClientTimeOutError" i "Authentication" = "SAS" | |
| SASNetworkError | Metryka transakcji z wymiarami "responsetype" = "NetworkError" i "Authentication" = "SAS" | |
| SASServerOtherError | Metryka transakcji z wymiarami "responsetype" = "ServerOtherError" i "Authentication" = "SAS" | |
| SASServerTimeOutError | Metryka transakcji z wymiarami "responsetype" = "ServerTimeOutError" i "Authentication" = "SAS" | |
| SASSuccess | Metryka transakcji z wymiarami "responsetype" = "Success" i "Authentication" = "SAS" | |
| ServerOtherError | Metryka transakcji z wymiarami "responsetype" = "ServerOtherError" | |
| ServerTimeOutError | Metryka transakcji z wymiarami "responsetype" = "ServerTimeOutError"  | |
| Powodzenie | Metryka transakcji z wymiarami "responsetype" = "Success" | |
| TotalBillableRequests| Transakcje | |
| TotalEgress | Ruch wychodzący | |
| TotalIngress | Ruch przychodzący | |
| TotalRequests | Transakcje | |

### <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

W przypadku Application Insights równoważne metryki są przedstawione poniżej:

| Metryka w klasycznych alertach | Równoważna Metryka w nowych alertach | Komentarze|
|--------------------------|---------------------------------|---------|
| Availability. availabilityMetric. Value | availabilityResults/availabilityPercentage|   |
| Availability. durationMetric. Value | availabilityResults/czas trwania| Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach.  |
| basicExceptionBrowser. Count | wyjątki/przeglądarka|  Użyj `aggregationType` elementu "Count" zamiast elementu "Sum". |
| basicExceptionServer. Count | wyjątki/serwer| Użyj `aggregationType` elementu "Count" zamiast elementu "Sum".  |
| clientPerformance. clientProcess. Value | browserTimings/processingDuration| Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach.  |
| clientPerformance. networkConnection. Value | browserTimings/networkDuration|  Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach. |
| clientPerformance. receiveRequest. Value | browserTimings/receiveDuration| Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach.  |
| clientPerformance. sendRequest. Value | browserTimings/sendDuration| Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach.  |
| clientPerformance. Total. Value | browserTimings/totalDuration| Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach.  |
| performanceCounter.available_bytes. Value | Liczniki wydajności/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec. Value | Liczniki wydajności/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec. Value | Liczniki wydajności/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized. Value | Liczniki wydajności/processCpuPercentage|   |
| performanceCounter.percentage_processor_time. Value | Liczniki wydajności/processCpuPercentage| Próg musi być odpowiednio zmodyfikowany, ponieważ oryginalna Metryka była dla wszystkich rdzeni, a nowa Metryka jest znormalizowana do jednego rdzenia. Narzędzie migracji nie zmienia progów.  |
| performanceCounter.percentage_processor_total. Value | Liczniki wydajności/processorCpuPercentage|   |
| performanceCounter.process_private_bytes. Value | Liczniki wydajności/processPrivateBytes|   |
| performanceCounter.request_execution_time. Value | Liczniki wydajności/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue. Value | Liczniki wydajności/requestsInQueue|   |
| performanceCounter.requests_per_sec. Value | Liczniki wydajności/requestsPerSecond|   |
| żądanie. czas trwania | żądania/czas trwania| Pomnóż pierwotny próg o 1000, ponieważ jednostki dla metryki klasycznej są w sekundach, a nowe są w milisekundach.  |
| żądanie. rate | żądania/częstotliwość|   |
| requestFailed. Count | żądania/niepowodzenie| Użyj `aggregationType` elementu "Count" zamiast elementu "Sum".   |
| widok. Count | pageViews/liczba| Użyj `aggregationType` elementu "Count" zamiast elementu "Sum".   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

W przypadku Cosmos DB równoważne metryki są przedstawione poniżej:

| Metryka w klasycznych alertach | Równoważna Metryka w nowych alertach | Komentarze|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Rozmiar danych | Datausage| |
| Liczba dokumentów | DocumentCount||
| Rozmiar indeksu | IndexUsage||
| Opłata żądania liczby Mongo| MongoRequestCharge z wymiarem "CommandName" = "Count"||
| Częstotliwość żądania liczby Mongo | MongoRequestsCount z wymiarem "CommandName" = "Count"||
| Opłata za żądanie usunięcia Mongo | MongoRequestCharge z wymiarem "CommandName" = "Delete"||
| Mongo — wskaźnik żądania usunięcia | MongoRequestsCount z wymiarem "CommandName" = "Delete"||
| Mongo | MongoRequestCharge z wymiarem "CommandName" = "INSERT"||
| Liczba żądań wstawienia Mongo | MongoRequestsCount z wymiarem "CommandName" = "INSERT"||
| Opłata za żądanie zapytania Mongo | MongoRequestCharge z wymiarem "CommandName" = "Find"||
| Częstotliwość żądań zapytań Mongo | MongoRequestsCount z wymiarem "CommandName" = "Find"||
| Opłata za żądanie aktualizacji Mongo | MongoRequestCharge z wymiarem "CommandName" = "Update"||
| Usługa jest niedostępna| Dostępność||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Jak są tworzone równoważne grupy akcji

Klasyczne reguły alertów mają akcje poczty e-mail, elementu webhook, aplikacji logiki i elementu Runbook powiązane z samą regułą alertu. Nowe reguły alertów używają grup akcji, które mogą być ponownie używane dla wielu reguł alertów. Narzędzie migracji tworzy jedną grupę akcji dla tych samych akcji, niezależnie od liczby reguł alertów korzystających z tej akcji. Grupy akcji utworzone za pomocą narzędzia migracji używają formatu nazewnictwa "Migrated_AG *".

> [!NOTE]
> Klasyczne alerty wysyłają zlokalizowane wiadomości e-mail na podstawie ustawień regionalnych administratora klasycznego, gdy są używane do powiadamiania klasycznych ról administratora. Nowe wiadomości e-mail dotyczące alertów są wysyłane za pośrednictwem grup akcji i są tylko w języku angielskim.

## <a name="rollout-phases"></a>Etapy wdrażania

Narzędzie migracji jest wdrażane w fazach dla klientów korzystających z klasycznych reguł alertów. Właściciele subskrypcji otrzymają wiadomość e-mail, gdy subskrypcja będzie gotowa do migracji za pomocą narzędzia.

> [!NOTE]
> Ponieważ narzędzie jest wdrażane w fazach, może się okazać, że niektóre subskrypcje nie są jeszcze gotowe do migracji w trakcie wczesnych faz.

Większość subskrypcji jest obecnie oznaczona jako gotowa do migracji. Tylko subskrypcje, które mają klasyczne alerty dla następujących typów zasobów, nadal nie są gotowe do migracji.

- Microsoft. classicCompute/domainNames/gniazda/role
- Microsoft. Insights/składniki

## <a name="who-can-trigger-the-migration"></a>Kto może wyzwolić migrację?

Każdy użytkownik, który ma wbudowaną rolę współautor monitorowania na poziomie subskrypcji, może wyzwolić migrację. Użytkownicy, którzy mają rolę niestandardową z następującymi uprawnieniami, mogą również wyzwolić migrację:

- */read
- Microsoft. Insights/actiongroups/*
- Microsoft. Insights/AlertRules/*
- Microsoft. Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Oprócz powyższych uprawnień subskrypcja powinna być dodatkowo rejestrowana przy użyciu dostawcy zasobów Microsoft. AlertsManagement. Jest to wymagane do pomyślnej migracji alertów anomalii błędów w Application Insights. 

## <a name="common-problems-and-remedies"></a>Typowe problemy i środki zaradcze

Po [zainicjowaniu migracji](alerts-using-migration-tool.md)otrzymasz wiadomości e-mail pod podanymi adresami, aby powiadomić, że migracja została ukończona, lub jeśli jest wymagana jakakolwiek akcja. W tej sekcji opisano niektóre typowe problemy i sposoby postępowania z nimi.

### <a name="validation-failed"></a>Weryfikacja nie powiodła się

Ze względu na ostatnie zmiany reguł alertów klasycznych w Twojej subskrypcji nie można zmigrować subskrypcji. Ten problem jest tymczasowy. Możesz ponownie uruchomić migrację po przeniesieniu stanu migracji **do tyłu w** ciągu kilku dni.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Blokada zakresu uniemożliwia nam Migrowanie reguł

W ramach migracji zostaną utworzone nowe alerty metryki i nowe grupy akcji, a następnie zostaną usunięte reguły klasycznego alertu. Jednak blokada zakresu może uniemożliwić nam tworzenie lub usuwanie zasobów. W zależności od blokady zakresu nie można migrować niektórych lub wszystkich reguł. Ten problem można rozwiązać, usuwając blokadę zakresu dla subskrypcji, grupy zasobów lub zasobu, która jest wymieniona w [narzędziu do migracji](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), i ponownie wyzwoli migrację. Nie można wyłączyć blokady zakresu i należy ją usunąć w czasie trwania procesu migracji. [Dowiedz się więcej na temat zarządzania blokadami zakresu](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Zasady z efektem "Odmów" uniemożliwiają nam Migrowanie reguł

W ramach migracji zostaną utworzone nowe alerty metryki i nowe grupy akcji, a następnie zostaną usunięte reguły klasycznego alertu. Jednak przypisanie [Azure Policy](../../governance/policy/index.yml) może uniemożliwić nam tworzenie zasobów. W zależności od przypisania zasad nie można migrować niektórych lub wszystkich reguł. Przypisania zasad, które blokują proces, są wymienione w [narzędziu migracji](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Rozwiąż ten problem, korzystając z:

- Wykluczanie subskrypcji, grup zasobów lub poszczególnych zasobów w czasie trwania procesu migracji z przypisania zasad. [Dowiedz się więcej o zarządzaniu zakresami wykluczeń zasad](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Ustaw tryb wymuszania na **wyłączony** w przypisaniu zasad. [Dowiedz się więcej o właściwości wymuszania przypisania zasad](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Ustaw wykluczenie Azure Policy (wersja zapoznawcza) dla subskrypcji, grup zasobów lub poszczególnych zasobów w ramach przypisania zasad. [Dowiedz się więcej o strukturze wykluczenia Azure Policy](../../governance/policy/concepts/exemption-structure.md).
- Usunięcie lub zmiana efektu na "Disabled", "Audit", "append" lub "Modify" (na przykład może rozwiązać problemy związane z brakującymi tagami). [Dowiedz się więcej o zarządzaniu efektami zasad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Następne kroki

- [Jak korzystać z narzędzia do migracji](alerts-using-migration-tool.md)
- [Przygotowanie do migracji](alerts-prepare-migration.md)
