---
title: Wyświetlanie danych aplikacji Azure Application Insights | Microsoft Docs
description: Możesz użyć rozwiązania Application Insights Connector, aby zdiagnozować problemy z wydajnością i zrozumieć, co użytkownicy robią z aplikacją po monitorowaniu z Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: 09485d3279e4ca4fff5b6492bab432d8034d7e42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449412"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Rozwiązanie do zarządzania Application Insights Connector (przestarzałe)

![Symbol Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> W przypadku obsługi [zapytań między zasobami](../log-query/cross-workspace-query.md), rozwiązanie do zarządzania Application Insights Connector nie jest już wymagane. Jest on przestarzały i usuwany z portalu Azure Marketplace wraz z portalem pakietu OMS, który został oficjalnie uznany za przestarzały 15 stycznia 2019 dla chmury komercyjnej platformy Azure. Zostanie ona wycofana 30 marca 2019 dla chmury rządowej USA platformy Azure.
>
>Istniejące połączenia będą nadal działały do 30 czerwca 2019.  W przypadku wycofania portalu pakietu OMS nie istnieje sposób konfigurowania i usuwania istniejących połączeń z portalu. Zobacz [usuwanie łącznika za pomocą programu PowerShell](#removing-the-connector-with-powershell) poniżej, aby uzyskać skrypt dotyczący usuwania istniejących połączeń przy użyciu programu PowerShell.
>
>Aby uzyskać wskazówki dotyczące wykonywania zapytań dotyczących danych dziennika Application Insights dla wielu aplikacji, zobacz [ujednolicaniaing multiple Azure Monitor Application Insights Resources](../log-query/unify-app-resource-data.md). Aby uzyskać więcej informacji na temat zaniechania portalu pakietu OMS, zobacz Portal pakietu OMS [przeniesiony na platformę Azure](./oms-portal-transition.md).
>
> 

Rozwiązanie Application Insights Connector pomaga zdiagnozować problemy z wydajnością i zrozumieć, co użytkownicy robią z aplikacją podczas jej monitorowania za pomocą [Application Insights](../app/app-insights-overview.md). Widoki tych samych danych telemetrycznych aplikacji, które deweloperzy widzą w Application Insights są dostępne w Log Analytics. Jednak w przypadku integrowania aplikacji Application Insights z Log Analytics widoczność aplikacji jest zwiększana przez posiadanie danych operacyjnych i aplikacji w jednym miejscu. Posiadanie tych samych widoków ułatwia współpracę z programistami aplikacji. Typowe widoki mogą pomóc skrócić czas wykrywania i rozwiązywania problemów dotyczących aplikacji i platformy.

W przypadku korzystania z rozwiązania można:

- Wyświetlaj wszystkie Application Insights aplikacje w jednym miejscu, nawet jeśli znajdują się w różnych subskrypcjach platformy Azure
- Skorelowanie danych infrastruktury z danymi aplikacji
- Wizualizowanie danych aplikacji z perspektywami w przeszukiwaniu dzienników
- Przestawiaj dane Log Analytics do aplikacji Application Insights w Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań Log Analytics dane nie są zbierane dla Application Insights Connector przez agentów. Wszystkie dane używane przez rozwiązanie pochodzą bezpośrednio z platformy Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](./agent-windows.md) | Nie | Rozwiązanie nie zbiera informacji z agentów systemu Windows. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie nie zbiera informacji od agentów systemu Linux. |
| [Grupa zarządzania programu SCOM](./om-agents.md) | Nie | Rozwiązanie nie zbiera informacji od agentów w połączonej grupie zarządzania SCOM. |
| [Konto usługi Azure Storage](./resource-logs.md#send-to-log-analytics-workspace) | Nie | Rozwiązanie nie zbiera informacji z usługi Azure Storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do Application Insights Connector informacji, musisz mieć subskrypcję platformy Azure
- Musisz mieć co najmniej jeden skonfigurowany Application Insights zasób.
- Musisz być właścicielem lub współautorem zasobu Application Insights.

## <a name="configuration"></a>Konfiguracja

1. Włącz rozwiązanie Azure Web Apps Analytics z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) lub przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań log Analytics z Galeria rozwiązań](../insights/solutions.md).
2. Przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie usługi** , aby otworzyć Application Insights. Następnie wyszukaj Application Insights. 
3. W obszarze **subskrypcje**wybierz subskrypcję zawierającą Application Insights zasoby, a następnie w obszarze **Nazwa**wybierz co najmniej jedną aplikację.
4. Kliknij przycisk **Zapisz**.

W ciągu około 30 minut dane staną się dostępne, a kafelek Application Insights zostanie zaktualizowany danymi, takimi jak na poniższej ilustracji:

![Kafelek Application Insights](./media/app-insights-connector/app-insights-tile.png)

Inne kwestie, o których należy pamiętać:

- Możesz łączyć Application Insights aplikacje tylko z jednym obszarem roboczym Log Analytics.
- Do Log Analytics można połączyć tylko [zasoby podstawowe lub Application Insights przedsiębiorstwa](https://azure.microsoft.com/pricing/details/application-insights) . Można jednak użyć warstwy Bezpłatna Log Analytics.

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie instaluje żadnych pakietów administracyjnych w podłączonych grupach zarządzania.

## <a name="use-the-solution"></a>Korzystanie z rozwiązania

W poniższych sekcjach opisano, jak korzystać z bloków widocznych na pulpicie nawigacyjnym Application Insights do wyświetlania danych z aplikacji i korzystania z nich.

### <a name="view-application-insights-connector-information"></a>Wyświetl informacje Application Insights Connector

Kliknij kafelek **Application Insights** , aby otworzyć pulpit nawigacyjny **Application Insights** , aby zobaczyć następujące bloki.

![Zrzut ekranu przedstawiający pulpit nawigacyjny Application Insights pokazujący bloki dla aplikacji, ilości danych i dostępności.](./media/app-insights-connector/app-insights-dash01.png)

![Zrzut ekranu przedstawiający pulpit nawigacyjny Application Insights pokazujący bloki żądań serwera, błędów i wyjątków.](./media/app-insights-connector/app-insights-dash02.png)

Pulpit nawigacyjny zawiera bloki wyświetlane w tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Można uruchomić wyszukiwanie w dzienniku, które zwraca wszystkie rekordy po kliknięciu przycisku **Pokaż wszystkie** w dolnej części bloku lub po kliknięciu nagłówka bloku.


| **Kolumna** | **Opis** |
| --- | --- |
| Aplikacje — liczba aplikacji | Pokazuje liczbę aplikacji w zasobach aplikacji. Wyświetla również nazwy aplikacji i dla każdej z nich, liczbę rekordów aplikacji. Kliknij liczbę, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknij nazwę aplikacji, aby uruchomić wyszukiwanie w dzienniku dla aplikacji, która zawiera rekordy aplikacji na hosta, rekordy według typu telemetrii i wszystkie dane według typu (w oparciu o ostatni dzień). |
| Wolumin danych — hosty wysyłające dane | Pokazuje liczbę hostów komputerów, które wysyłają dane. Zawiera również listę hostów komputerów i liczby rekordów dla każdego hosta. Kliknij liczbę, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kliknij nazwę komputera, aby uruchomić wyszukiwanie w dzienniku dla hosta, który wyświetla rekordy aplikacji na hosta, rekordy według typu telemetrii i wszystkie dane według typu (w oparciu o ostatni dzień). |
| Dostępność — wyniki WebTest | Pokazuje wykres pierścieniowy dla wyników testu sieci Web, wskazujący, że przebieg lub niepowodzenie. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Wyniki przedstawiają liczbę operacji zakończonych powodzeniem i niepowodzeń dla wszystkich testów. Przedstawia on wszystkie Web Apps z ruchem przez ostatnią minutę. Kliknij nazwę aplikacji, aby wyświetlić przeszukiwanie dzienników przedstawiające szczegóły nieudanych testów sieci Web. |
| Żądania serwera — liczba żądań na godzinę | Przedstawia wykres liniowy żądań serwera na godzinę dla różnych aplikacji. Umieść kursor nad wierszem na wykresie, aby zobaczyć pierwsze 3 aplikacje otrzymujące żądania dla punktu w czasie. Pokazuje także listę aplikacji, które otrzymują żądania oraz liczbę żądań w wybranym okresie. <br><br>Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , które pokazuje bardziej szczegółowy wykres liniowy żądań serwera na godzinę dla różnych aplikacji. <br><br> Kliknij aplikację na liście, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , które pokazuje listę żądań, wykresy żądań w czasie i czas trwania żądania oraz listę kodów odpowiedzi na żądania.   |
| Niepowodzenia — liczba żądań zakończonych niepowodzeniem na godzinę | Pokazuje wykres liniowy nieudanych żądań aplikacji na godzinę. Umieść wskaźnik myszy na wykresie, aby zobaczyć pierwsze 3 aplikacje z nieudanymi żądaniami dla punktu w czasie. Pokazuje także listę aplikacji o liczbie nieudanych żądań dla każdego z nich. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , które pokazuje bardziej szczegółowy wykres liniowy zakończonych niepowodzeniem żądań aplikacji. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie w dzienniku, <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>  które pokazuje Nieudane żądania, wykresy dla żądań zakończonych niepowodzeniem w czasie trwania i czas żądania oraz listę kodów odpowiedzi na żądanie zakończone niepowodzeniem. |
| Wyjątki — liczba wyjątków na godzinę | Pokazuje wykres liniowy wyjątków na godzinę. Umieść wskaźnik myszy na wykresie, aby wyświetlić 3 najważniejsze aplikacje z wyjątkami dla punktu w czasie. Pokazuje także listę aplikacji z liczbą wyjątków dla każdej z nich. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , które pokazuje bardziej szczegółowy wykres z wyjątkami. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie w dzienniku, <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> które pokazuje listę wyjątków, wykresy dla wyjątków w czasie i żądania zakończone niepowodzeniem oraz listę typów wyjątków.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Wyświetl perspektywę Application Insights z wyszukiwaniem w dzienniku

Po kliknięciu dowolnego elementu na pulpicie nawigacyjnym zobaczysz perspektywę Application Insights pokazana w obszarze wyszukiwania. Perspektywa zawiera rozszerzoną wizualizację w oparciu o wybrany typ telemetrii. Dzięki temu zmiany zawartości wizualizacji dla różnych typów telemetrii.

Po kliknięciu dowolnego miejsca w bloku aplikacje zostanie wyświetlony domyślny perspektywa **aplikacji** .

![Perspektywa aplikacji Application Insights](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektywa przedstawia przegląd wybranej aplikacji.

Blok **dostępności** pokazuje inny widok perspektywy, w którym można zobaczyć wyniki testu sieci Web i powiązane żądania zakończone niepowodzeniem.

![Application Insights perspektywę dostępności](./media/app-insights-connector/availability-blade-drill-search.png)

Po kliknięciu dowolnego miejsca w blokach żądania lub **Błędy** **serwera** składniki perspektywy zmieniają się w celu udostępnienia wizualizacji powiązanej z żądaniami.

![Blok niepowodzeń Application Insights](./media/app-insights-connector/server-requests-failures-drill-search.png)

Po kliknięciu dowolnego miejsca w bloku **wyjątki** zostanie wyświetlona Wizualizacja dostosowana do wyjątków.

![Blok wyjątków Application Insights](./media/app-insights-connector/exceptions-blade-drill-search.png)

Bez względu na to, czy klikniesz coś jednego z pulpitów nawigacyjnych **Application Insights Connector** na stronie **wyszukiwania** , wszystkie zapytania zwracające Application Insights dane pokazują Application Insights perspektywę. Na przykład jeśli przeglądasz Application Insights dane, zapytanie **&#42;** również pokazuje kartę perspektywy, jak na poniższym obrazie:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Składniki perspektywy są aktualizowane w zależności od zapytania wyszukiwania. Oznacza to, że można filtrować wyniki przy użyciu dowolnego pola wyszukiwania, które daje możliwość wyświetlenia danych z:

- Wszystkie aplikacje
- Pojedyncza wybrana aplikacja
- Grupa aplikacji

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Przestawianie do aplikacji w Azure Portal

Bloki Application Insights Connector zaprojektowano w celu umożliwienia przestawiania na wybraną aplikację Application Insights *podczas korzystania z Azure Portal*. Możesz użyć rozwiązania jako platformy monitorowania wysokiego poziomu, która ułatwia rozwiązywanie problemów z aplikacją. Gdy zobaczysz potencjalny problem w dowolnej z połączonych aplikacji, możesz przejść do niego w Log Analytics wyszukiwania lub przestawić bezpośrednio do aplikacji Application Insights.

Aby przestawić, kliknij wielokropek (**...**), który pojawia się na końcu każdego wiersza, a następnie wybierz pozycję **Otwórz w Application Insights**.

>[!NOTE]
>**Aplet Otwórz w Application Insights** nie jest dostępny w Azure Portal.

![Otwórz w Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dane poprawione na przykład

Application Insights zapewnia *[korekcję próbkowania](../app/sampling.md)* , aby pomóc w zmniejszeniu ruchu telemetrii. Po włączeniu próbkowania w aplikacji Application Insights można uzyskać zredukowaną liczbę wpisów przechowywanych zarówno w Application Insights, jak i w Log Analytics. Chociaż spójność danych jest zachowywana na stronie **Application Insights Connector** i perspektywach, należy ręcznie skorygować dane próbkowania dla zapytań niestandardowych.

Oto przykład korekcji próbkowania w zapytaniu przeszukiwania dzienników:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Pole **Liczba próbek** jest obecna we wszystkich wpisach i pokazuje liczbę punktów danych, które reprezentuje wpis. Jeśli włączysz próbkowanie dla aplikacji Application Insights, **Liczba próbek** jest większa niż 1. Aby policzyć rzeczywistą liczbę wpisów generowanych przez aplikację, należy zsumować pola **liczby próbkowanej** .

Próbkowanie ma wpływ tylko na łączną liczbę wpisów generowanych przez aplikację. Nie ma potrzeby poprawiania próbkowania dla pól metryk, takich jak **RequestDuration** lub **AvailabilityDuration**  , ponieważ te pola pokazują średnią dla wpisów reprezentowanych.

## <a name="input-data"></a>Dane wejściowe

Rozwiązanie odbiera następujące typy danych telemetrycznych z połączonych Application Insights aplikacji:

- Dostępność
- Wyjątki
- Żądania
- Wyświetlenia stron — w obszarze roboczym do odbierania wyświetleń stron należy skonfigurować aplikacje w celu zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [PageViews](../app/api-custom-events-metrics.md#page-views).
- Zdarzenia niestandardowe — w przypadku obszaru roboczego do odbierania zdarzeń niestandardowych należy skonfigurować aplikacje w celu zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [poleceń trackEvent](../app/api-custom-events-metrics.md#trackevent).

Dane są odbierane przez Log Analytics od Application Insights, gdy staną się dostępne.

## <a name="output-data"></a>Dane wyjściowe

Rekord z *typem* *ApplicationInsights* jest tworzony dla każdego typu danych wejściowych. Rekordy ApplicationInsights mają właściwości wyświetlane w następujących sekcjach:

### <a name="generic-fields"></a>Pola ogólne

| Właściwość | Opis |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Czas rekordu |
| ApplicationId | Klucz Instrumentacji aplikacji Application Insights |
| ApplicationName | Nazwa aplikacji Application Insights |
| RoleInstance | Identyfikator hosta serwera |
| DeviceType | Urządzenie klienckie |
| ScreenResolution |   |
| Kontynent | Kontynent, w którym pochodziło żądanie |
| Kraj | Kraj/region, w którym pochodziło żądanie |
| Prowincji | Województwo, stan lub ustawienia regionalne, w których pochodziło żądanie |
| City (Miasto) | Miasto lub miejscowość, w której pochodziło żądanie |
| issyntetyczne | Wskazuje, czy żądanie zostało utworzone przez użytkownika, czy przez metodę zautomatyzowaną. True = Metoda zautomatyzowana lub FAŁSZ = wygenerowane przez użytkownika |
| SamplingRate | Procent danych telemetrycznych generowanych przez zestaw SDK, który jest wysyłany do portalu. Zakres 0.0-100,0. |
| SampledCount | 100/(SamplingRate). Na przykład 4 = &gt; 25% |
| IsAuthenticated | Prawda lub FAŁSZ |
| OperationID | Elementy, które mają ten sam Identyfikator operacji, są wyświetlane jako elementy pokrewne w portalu. Zazwyczaj identyfikator żądania |
| ParentOperationID | Identyfikator operacji nadrzędnej |
| OperationName |   |
| SessionId | Identyfikator GUID służący do unikatowej identyfikacji sesji, w której zostało utworzone żądanie |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Pola specyficzne dla dostępności

| Właściwość | Opis |
| --- | --- |
| Telemetriatype | Dostępność |
| AvailabilityTestName | Nazwa testu sieci Web |
| AvailabilityRunLocation | Źródło geograficzne żądania http |
| AvailabilityResult | Wskazuje wynik sukcesu testu sieci Web |
| AvailabilityMessage | Komunikat dołączony do testu sieci Web |
| AvailabilityCount | 100/(częstotliwość próbkowania). Na przykład 4 = &gt; 25% |
| DataSizeMetricValue | 1,0 lub 0,0 |
| DataSizeMetricCount | 100/(częstotliwość próbkowania). Na przykład 4 = &gt; 25% |
| AvailabilityDuration | Czas trwania testu sieci Web w milisekundach |
| AvailabilityDurationCount | 100/(częstotliwość próbkowania). Na przykład 4 = &gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unikatowy identyfikator GUID dla testu sieci Web |
| AvailabilityTimestamp | Precyzyjne sygnatura czasowa testu dostępności |
| AvailabilityDurationMin | W przypadku rekordów próbkowanych to pole pokazuje minimalny czas trwania testu sieci Web (w milisekundach) dla przedstawionych punktów danych. |
| AvailabilityDurationMax | W przypadku rekordów próbkowanych to pole zawiera maksymalny czas trwania testu sieci Web (w milisekundach) dla przedstawionych punktów danych. |
| AvailabilityDurationStdDev | W przypadku rekordów próbkowanych to pole pokazuje odchylenie standardowe między wszystkimi okresami trwania testu sieci Web (w milisekundach) dla przedstawionych punktów danych. |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pola specyficzne dla wyjątku

| Type | ApplicationInsights |
| --- | --- |
| Telemetriatype | Wyjątek |
| Typ | Typ wyjątku |
| ExceptionMethod | Metoda, która tworzy wyjątek |
| ExceptionAssembly | Zestaw zawiera strukturę i wersję, a także token klucza publicznego |
| Zasada wyjątku | Typ wyjątku |
| ExceptionHandledAt | Wskazuje poziom, który obsłużył wyjątek |
| ExceptionCount | 100/(częstotliwość próbkowania). Na przykład 4 = &gt; 25% |
| ExceptionMessage | Komunikat wyjątku |
| ExceptionStack | Pełny stos wyjątku |
| ExceptionHasStack | True, jeśli wyjątek ma stos |



### <a name="request-specific-fields"></a>Pola specyficzne dla żądania

| Właściwość | Opis |
| --- | --- |
| Type | ApplicationInsights |
| Telemetriatype | Żądanie |
| ResponseCode | Odpowiedź HTTP wysłana do klienta |
| RequestSuccess | Wskazuje powodzenie lub niepowodzenie. TRUE lub FALSE. |
| RequestID | Identyfikator do unikatowego identyfikowania żądania |
| Żądanie | Pobierz/Ogłoś i adres URL bazy |
| RequestDuration | Czas trwania żądania (w sekundach) |
| Adres URL | Adres URL żądania nie obejmujący hosta |
| Host | Host serwera sieci Web |
| URLBase | Pełny adres URL żądania |
| ApplicationProtocol | Typ protokołu używany przez aplikację |
| RequestCount | 100/(częstotliwość próbkowania). Na przykład 4 = &gt; 25% |
| RequestDurationCount | 100/(częstotliwość próbkowania). Na przykład 4 = &gt; 25% |
| RequestDurationMin | W przypadku rekordów próbkowanych to pole pokazuje minimalny czas trwania żądania (w milisekundach) dla przedstawionych punktów danych. |
| RequestDurationMax | W przypadku rekordów próbkowanych to pole zawiera maksymalny czas trwania żądania (w milisekundach) dla przedstawionych punktów danych. |
| RequestDurationStdDev | W przypadku rekordów próbkowanych to pole pokazuje odchylenie standardowe między wszystkimi czasami trwania żądania (w milisekundach) dla przedstawionych punktów danych. |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

To rozwiązanie nie ma zestawu przykładowych wyszukiwań dzienników wyświetlanych na pulpicie nawigacyjnym. Jednak przykładowe zapytania wyszukiwania w dzienniku zawierające opisy są wyświetlane w sekcji [Wyświetl informacje Application Insights Connector](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>Usuwanie łącznika przy użyciu programu PowerShell
W przypadku wycofania portalu pakietu OMS nie istnieje sposób konfigurowania i usuwania istniejących połączeń z portalu. Istniejące połączenia można usunąć za pomocą poniższego skryptu programu PowerShell. Aby wykonać tę operację, musisz być właścicielem lub współautorem obszaru roboczego i czytnika zasobów Application Insights.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Listę aplikacji można pobrać przy użyciu następującego skryptu programu PowerShell, który wywołuje wywołanie interfejsu API REST. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Ten skrypt wymaga tokenu uwierzytelniania okaziciela do uwierzytelniania w odniesieniu do Azure Active Directory. Jednym ze sposobów pobrania tego tokenu jest użycie artykułu w [witrynie dokumentacji interfejsu API REST](/rest/api/loganalytics/datasources/createorupdate). Kliknij przycisk Wypróbuj i zaloguj **się** do subskrypcji platformy Azure. Token okaziciela można skopiować z **wersji zapoznawczej żądania** , jak pokazano na poniższej ilustracji.


![Token okaziciela](media/app-insights-connector/bearer-token.png)


Możesz również pobrać listę aplikacji, korzystając z kwerendy dziennika:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Następne kroki

- Użyj [wyszukiwania w dzienniku](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe informacje dotyczące Application Insights aplikacji.

