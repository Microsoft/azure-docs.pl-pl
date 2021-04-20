---
title: Zasoby dotyczące tworzenia Azure Sentinel łączników niestandardowych | Microsoft Docs
description: Dowiedz się więcej o dostępnych zasobach dotyczących tworzenia łączników niestandardowych dla Azure Sentinel. Metody obejmują agenta i interfejs API usługi Log Analytics, usługę Logstash, Logic Apps, program PowerShell i Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: a1aaf89624f8d0ab48692629d859f3c1bdb4ba67
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738904"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Zasoby do tworzenia Azure Sentinel łączników niestandardowych

Azure Sentinel udostępnia szeroką gamę wbudowanych łączników dla usług platformy [Azure](connect-data-sources.md)i rozwiązań zewnętrznych, a także obsługuje pozysowanie danych z niektórych źródeł bez dedykowanego łącznika.

Jeśli nie możesz połączyć źródła danych z usługą Azure Sentinel przy użyciu dowolnego z istniejących dostępnych rozwiązań, rozważ utworzenie własnego łącznika źródła danych.

Aby uzyskać pełną listę obsługiwanych łączników, zobacz wpis w blogu Azure Sentinel: The [connectors grand (CEF, Syslog, Direct, Agent, Custom, and more) (Łączniki w wersji cef, syslog, direct, agent,](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) niestandardowe i inne).

## <a name="compare-custom-connector-methods"></a>Porównanie metod łączników niestandardowych

W poniższej tabeli porównano podstawowe szczegóły dotyczące każdej metody tworzenia łączników niestandardowych opisanych w tym artykule. Wybierz linki w tabeli, aby uzyskać więcej informacji o każdej metodzie.

|Opis metody  |Możliwość | Praca bezserwerowa    |Złożoność  |
|---------|---------|---------|---------|
|**[Log Analytics Agent](#connect-with-the-log-analytics-agent)** <br>Najlepsze rozwiązanie do zbierania plików ze źródeł lokalnych i IaaS   | Tylko zbieranie plików  |   Nie      |Niski         |
|**[Logstash](#connect-with-logstash)** <br>Najlepsze rozwiązanie w przypadku źródeł lokalnych i IaaS, dowolnego źródła, dla którego jest dostępna wtyczka, oraz organizacji, które już znają usługę Logstash  | Dostępne wtyczki oraz niestandardowe wtyczki zapewniają znaczącą elastyczność.   |   Nie; wymaga do uruchomienia maszyny wirtualnej lub klastra maszyn wirtualnych           |   Niski; obsługuje wiele scenariuszy z wtyczkami      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Wysoki koszt; unikaj danych o dużej ilości danych <br>Najlepsze rozwiązanie w przypadku źródeł chmury o małej ilości danych  | Programowanie bez kodu zapewnia ograniczoną elastyczność bez obsługi implementowania algorytmów.<br><br> Jeśli żadna dostępna akcja nie spełnia twoich wymagań, utworzenie akcji niestandardowej może spowodować złożoność.    |    Tak         |   Niski; proste, bez kodu opracowywanie      |
|**[PowerShell](#connect-with-powershell)** <br>Najlepsze rozwiązanie w przypadku tworzenia prototypów i okresowego przekazywania plików | Bezpośrednia obsługa zbierania plików. <br><br>Program PowerShell może służyć do zbierania większej liczby źródeł, ale wymaga kodowania i konfigurowania skryptu jako usługi.      |Nie               |  Niski       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>Najlepsze rozwiązanie w przypadku implementowania integracji przez isvs oraz unikatowych wymagań dotyczących kolekcji   | Obsługuje wszystkie możliwości dostępne w kodzie.  | Zależy od implementacji           |     Wys.    |
|**[Azure Functions](#connect-with-azure-functions)** Najlepsze rozwiązanie w przypadku źródeł w chmurze o dużej ilości danych i unikatowych wymagań dotyczących zbierania  | Obsługuje wszystkie możliwości dostępne w kodzie.  |  Tak             |     Wysoki; wymaga wiedzy z zakresu programowania    |
|     |         |                |

> [!TIP]
> Aby uzyskać porównanie użycia Logic Apps i Azure Functions dla tego samego łącznika, zobacz:
> 
> - [Ingest Fastly Web Application Firewall loguje się do Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel github community): [Logic Łącznik aplikacji](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)Azure  |  [Function Connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Nawiązywanie połączenia z agentem usługi Log Analytics

Jeśli źródło danych dostarcza zdarzenia w plikach, zalecamy użycie agenta usługi Azure Monitor Log Analytics do utworzenia łącznika niestandardowego.

- Aby uzyskać więcej informacji, zobacz [Zbieranie dzienników niestandardowych w Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Aby uzyskać przykład tej metody, zobacz Zbieranie niestandardowych źródeł danych [JSON](../azure-monitor/agents/data-sources-json.md)za pomocą agenta usługi Log Analytics dla systemu Linux w Azure Monitor .

## <a name="connect-with-logstash"></a>Nawiązywanie połączenia za pomocą usługi Logstash

Jeśli znasz program [Logstash,](https://www.elastic.co/logstash)możesz użyć usługi Logstash z wtyczką danych wyjściowych usługi [Logstash,](connect-logstash.md) aby Azure Sentinel łącznik niestandardowy.

Za pomocą Azure Sentinel Danych wyjściowych usługi Logstash możesz użyć dowolnych wtyczek wejściowych i filtrowania usługi Logstash oraz skonfigurować Azure Sentinel jako dane wyjściowe dla potoku usługi Logstash. Usługa Logstash ma dużą bibliotekę wtyczek, które umożliwiają wprowadzanie danych wejściowych z różnych źródeł, takich jak Event Hubs, Apache Kafka, pliki, bazy danych i usługi w chmurze. Wtyczki filtrowania są potrzebne do analizowania zdarzeń, filtrowania niepotrzebnych zdarzeń, zaciemniania wartości i nie tylko.

Aby uzyskać przykłady użycia usługi Logstash jako łącznika niestandardowego, zobacz:

- [Hunting for Capital One Breach TTPs in AWS logs using Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (Blog)
- [Radware Azure Sentinel implementation guide (Przewodnik wdrażania Azure Sentinel radware)](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Aby uzyskać przykłady przydatnych wtyczek usługi Logstash, zobacz:

- [Wtyczka wejściowa cloudwatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs wtyczki](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Wtyczka wejściowa usługi Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub wtyczki wejściowej](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Usługa Logstash umożliwia również skalowanie zbierania danych przy użyciu klastra. Aby uzyskać więcej informacji, zobacz [Using a load-balanced Logstash VM at scale (Używanie maszyny wirtualnej logstash](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)o zrównoważonym obciążeniu na dużą skalę).
>

## <a name="connect-with-logic-apps"></a>Nawiązywanie połączenia przy użyciu Logic Apps

Użyj aplikacji [logiki platformy Azure,](../logic-apps/index.yml) aby utworzyć bez serwera, niestandardowy łącznik dla Azure Sentinel.

> [!NOTE]
> Tworzenie łączników bez serwera przy użyciu Logic Apps może być wygodne, Logic Apps łączników może być kosztowne w przypadku dużych ilości danych.
>
> Zalecamy używanie tej metody tylko w przypadku źródeł danych o małej ilości danych lub wzbogacania przekazywania danych.
>

1. **Użyj jednego z następujących wyzwalaczy, aby uruchomić Logic Apps**:

    |Wyzwalacz  |Opis  |
    |---------|---------|
    |**Zadanie cykliczne**     |   Możesz na przykład zaplanować aplikacji logiki regularne pobieranie danych z określonych plików, baz danych lub zewnętrznych interfejsów API. <br>Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie](../connectors/connectors-native-recurrence.md)i uruchamianie cyklicznych zadań i przepływów pracy w Azure Logic Apps .      |
    |**Wyzwalanie na żądanie**     | Uruchamianie aplikacji logiki na żądanie w celu ręcznego zbierania i testowania danych. <br>Aby uzyskać więcej informacji, zobacz [Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki przy użyciu punktów końcowych HTTPS.](../logic-apps/logic-apps-http-endpoint.md)        |
    |**Punkt końcowy HTTP/S**     |  Zalecane do przesyłania strumieniowego oraz do uruchamiania transferu danych przez system źródłowy. <br>Aby uzyskać więcej informacji, zobacz [Call service endpoints over HTTP or HTTPs (Wywołaj punkty końcowe usługi za pośrednictwem protokołu HTTP lub HTTPS).](../connectors/connectors-native-http.md)       |
    |     |         |

1. **Użyj dowolnego łącznika aplikacji logiki, który odczytuje informacje w celu uzyskania zdarzeń**. Na przykład:

    - [Nawiązywanie połączenia z interfejsem API REST](/connectors/custom-connectors/)
    - [Nawiązywanie połączenia z SQL Server](/connectors/sql/)
    - [Nawiązywanie połączenia z systemem plików](/connectors/filesystem/)

    > [!TIP]
    > Łączniki niestandardowe do interfejsów API REST, serwerów SQL i systemów plików obsługują również pobieranie danych z lokalnych źródeł danych. Aby uzyskać więcej informacji, zobacz dokumentację Install [on-premises data gateway (Instalowanie lokalnej bramy](/connectors/filesystem/) danych).
    >

1. **Przygotuj informacje, które chcesz pobrać.**

    Na przykład użyj akcji Prze analizuj dane [JSON,](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) aby uzyskać dostęp do właściwości w zawartości JSON, umożliwiając wybranie tych właściwości z listy zawartości dynamicznej podczas określania danych wejściowych dla aplikacji logiki.

    Aby uzyskać więcej informacji, zobacz [Wykonywanie operacji na danych w programie Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Zapisz dane do usługi Log Analytics.**

    Aby uzyskać więcej informacji, zobacz [dokumentację modułu zbierającego](/connectors/azureloganalyticsdatacollector/) dane usługi Azure Log Analytics.

Aby uzyskać przykłady tworzenia łącznika niestandardowego dla usługi Azure Sentinel użyciu Logic Apps, zobacz:

- [Tworzenie potoku danych przy użyciu interfejsu API modułu zbierającego dane](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic Łącznik aplikacji pomocą webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel społeczności github)
- [Zabezpieczanie połączeń usługi Microsoft Teams za pomocą zaplanowanej aktywacji](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [Pozyskanie wskaźników zagrożeń OTX zvault](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) do Azure Sentinel (blog)

## <a name="connect-with-powershell"></a>Łączenie się z programem PowerShell

Skrypt [programu PowerShell Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) umożliwia przesyłanie strumieniowe zdarzeń lub informacji o Azure Sentinel z wiersza polecenia przy użyciu programu PowerShell. To przesyłanie strumieniowe skutecznie tworzy łącznik niestandardowy między źródłem danych i Azure Sentinel.

Na przykład poniższy skrypt przesyła plik CSV do Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Skrypt [programu PowerShell Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) używa następujących parametrów:

|Parametr  |Opis  |
|---------|---------|
|**WorkspaceId**     |   Identyfikator Azure Sentinel obszaru roboczego, w którym będą przechowywane dane.  [Znajdź identyfikator i klucz obszaru roboczego](#find-your-workspace-id-and-key).  |
|**Klucz obszaru roboczego**     |   Klucz podstawowy lub pomocniczy dla Azure Sentinel roboczego, w którym będą przechowywane dane. [Znajdź identyfikator i klucz obszaru roboczego](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Nazwa niestandardowej tabeli dziennika, w której mają być przechowywane dane. Sufiks **_CL** zostanie automatycznie dodany na końcu nazwy tabeli.  |
|**AddComputerName**     |   Jeśli ten parametr istnieje, skrypt dodaje bieżącą nazwę komputera do każdego rekordu dziennika w polu o **nazwie Komputer.**      |
|**TaggedAzureResourceId**     | Jeśli ten parametr istnieje, skrypt kojarzy wszystkie przekazane rekordy dziennika z określonym zasobem platformy Azure. <br><br>To skojarzenie umożliwia korzystanie z przekazanych rekordów dziennika dla zapytań kontekstowych zasobów i jest zgodne z kontrolą dostępu opartą na rolach opartą na zasobach.       |
|**AdditionalDataTaggingName**     |      Jeśli ten parametr istnieje, skrypt dodaje kolejne pole do każdego rekordu dziennika ze skonfigurowaną nazwą i wartością skonfigurowaną dla **parametru AdditionalDataTaggingValue.** <br><br>W takim przypadku wartość **AdditionalDataTaggingValue** nie może być pusta. |
|**AdditionalDataTaggingValue**     |   Jeśli ten parametr istnieje, skrypt dodaje kolejne pole do każdego rekordu dziennika ze skonfigurowaną wartością i nazwą pola skonfigurowaną dla **parametru AdditionalDataTaggingName.** <br><br>Jeśli parametr **AdditionalDataTaggingName** jest pusty, ale skonfigurowano wartość, domyślną nazwą pola jest **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Znajdowanie identyfikatora i klucza obszaru roboczego

Znajdź szczegóły parametrów **WorkspaceID** i **WorkspaceKey** w Azure Sentinel:

1. W Azure Sentinel wybierz pozycję **Ustawienia po** lewej stronie, a następnie wybierz **kartę Ustawienia obszaru roboczego.**

1. W **obszarze Rozpoczynanie pracy z usługą Log Analytics**  >  **1 Łączenie ze źródłem danych** wybierz pozycję Zarządzanie agentami systemów Windows i **Linux.**

1. Znajdź identyfikator obszaru roboczego, klucz podstawowy i klucz pomocniczy na **kartach Serwerów z systemem Windows.**
## <a name="connect-with-the-log-analytics-api"></a>Nawiązywanie połączenia za pomocą interfejsu API usługi Log Analytics

Zdarzenia można przesyłać strumieniowo do Azure Sentinel za pomocą interfejsu API modułu zbierającego dane usługi Log Analytics w celu bezpośredniego wywołania punktu końcowego RESTful.

Wywołanie punktu końcowego RESTful bezpośrednio wymaga większej liczby programów, ale zapewnia również większą elastyczność.

Aby uzyskać więcej informacji, zobacz Interfejs API modułu zbierającego [dane usługi Log Analytics,](../azure-monitor/logs/data-collector-api.md)a szczególnie następujące przykłady:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Nawiązywanie połączenia za pomocą Azure Functions

Użyj Azure Functions z interfejsem API RESTful i różnymi językami kodowania, takimi jak [program PowerShell,](../azure-functions/functions-reference-powershell.md)aby utworzyć bez serwera łącznik niestandardowy.

Aby uzyskać przykłady tej metody, zobacz:

- [Łączenie punktu końcowego VMware Carbon Black Cloud Endpoint Standard z Azure Sentinel funkcją platformy Azure](connect-vmware-carbon-black.md)
- [Łączenie aplikacji usługi Okta Single Sign-On z Azure Sentinel funkcją platformy Azure](connect-okta-single-sign-on.md)
- [Łączenie aplikacji Proofpoint TAP z Azure Sentinel funkcją platformy Azure](connect-proofpoint-tap.md)
- [Łączenie maszyny wirtualnej Qualys z usługą Azure Sentinel funkcją platformy Azure](connect-qualys-vm.md)
- [Pozyskowanie danych XML, CSV lub innych formatów danych](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Monitorowanie powiększenia za Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- Wdrażanie aplikacji funkcji w celu uzyskania danych usługi [Office 365 interfejs API Zarządzanie do](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) Azure Sentinel (Azure Sentinel społeczności usługi GitHub)

## <a name="parse-your-custom-connector-data"></a>Analizowanie danych łącznika niestandardowego

Możesz użyć wbudowanej techniki analizowania łącznika niestandardowego, aby wyodrębnić odpowiednie informacje i wypełnić odpowiednie pola w Azure Sentinel.

Na przykład:

- **Jeśli używasz usługi Logstash,** użyj wtyczki [filtru Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) do analizowania danych.
- **Jeśli używasz funkcji platformy Azure**, prze analizuj dane za pomocą kodu. Aby uzyskać więcej informacji, zobacz [Parsers](normalization.md#parsers).

Azure Sentinel obsługuje analizowanie w czasie wykonywania zapytania. Analizowanie w czasie wykonywania zapytania umożliwia wypychanie danych w oryginalnym formacie, a następnie analizowanie ich na żądanie w razie potrzeby.

Analizowanie w czasie wykonywania zapytania oznacza również, że nie musisz wcześniej znać dokładnej struktury danych, podczas tworzenia łącznika niestandardowego, a nawet informacji, które należy wyodrębnić. Zamiast tego analizuj dane w dowolnym momencie, nawet podczas badania.

> [!NOTE]
> Aktualizacja parsera ma również zastosowanie do danych, które zostały już pozyskane do Azure Sentinel.
> 
## <a name="next-steps"></a>Następne kroki

Użyj danych pozyowanych do Azure Sentinel, aby zabezpieczyć środowisko przy użyciu dowolnego z następujących procesów:

- [Uzyskiwanie wglądu w alerty](quickstart-get-visibility.md)
- [Wizualizowanie i monitorowanie danych](tutorial-monitor-your-data.md)
- [Badanie zdarzeń](tutorial-investigate-cases.md)
- [Wykrywania zagrożeń](tutorial-detect-threats-built-in.md)
- [Automatyzowanie zapobiegania zagrożeniom](tutorial-respond-threats-playbook.md)
- [Wyszukiwanie zagrożeń](hunting.md)
