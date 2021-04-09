---
title: Zasoby do tworzenia łączników niestandardowych platformy Azure Microsoft Docs
description: Dowiedz się więcej o dostępnych zasobach na potrzeby tworzenia łączników niestandardowych dla platformy Azure. Metody obejmują agenta Log Analytics i interfejsów API, logstash, Logic Apps, PowerShell i Azure Functions.
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
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724357"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Zasoby do tworzenia łączników niestandardowych platformy Azure

Platforma Azure — wskaźnik przedstawia szeroki zakres [wbudowanych łączników dla usług platformy Azure i rozwiązań zewnętrznych](connect-data-sources.md), a także obsługuje pobieranie danych z niektórych źródeł bez dedykowanego łącznika.

Jeśli nie możesz połączyć źródła danych z usługą Azure wskaźnikowym przy użyciu dowolnego dostępnego rozwiązania, rozważ utworzenie własnego łącznika źródła danych.

Aby uzyskać pełną listę obsługiwanych łączników, zobacz wpis w blogu dotyczący [łączników sum (CEF, dziennika systemowego, Direct, Agent, Custom i więcej)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) .

## <a name="compare-custom-connector-methods"></a>Porównanie metod łączników niestandardowych

W poniższej tabeli porównano podstawowe informacje o każdej metodzie tworzenia łączników niestandardowych opisanych w tym artykule. Wybierz linki w tabeli, aby uzyskać więcej szczegółowych informacji o każdej z tych metod.

|Opis metody  |Możliwość | Praca bezserwerowa    |Złożoność  |
|---------|---------|---------|---------|
|**[Agent Log Analytics](#connect-with-the-log-analytics-agent)** <br>Najlepsze dla zbierania plików ze źródeł lokalnych i IaaS   | Tylko zbieranie plików  |   Nie      |Niski         |
|**[Logstash](#connect-with-logstash)** <br>Najlepsze dla źródeł lokalnych i IaaS, wszystkie źródła, dla których jest dostępna wtyczka, oraz organizacje już znające logstash  | Dostępne wtyczki i wtyczka niestandardowa zapewniają znaczną elastyczność.   |   Znaleziono wymaga maszyny wirtualnej lub klastra maszyn wirtualnych do uruchomienia           |   Małą obsługuje wiele scenariuszy z wtyczkami      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Wysoki koszt; Unikaj tworzenia dużej ilości danych <br>Najlepsze dla źródeł w chmurze o małym nasileniu  | Programowanie bezkodowe umożliwia ograniczoną elastyczność bez obsługi implementacji algorytmów.<br><br> Jeśli żadna dostępna akcja już nie obsługuje Twoich wymagań, utworzenie akcji niestandardowej może zwiększyć złożoność.    |    Tak         |   Małą proste, programowanie bezkodowe      |
|**[PowerShell](#connect-with-powershell)** <br>Najlepsza do tworzenia prototypów i okresowych przekazywania plików | Bezpośrednia pomoc techniczna dla zbierania plików. <br><br>Program PowerShell może służyć do zbierania większej liczby źródeł, ale wymaga kodowania i konfigurowania skryptu jako usługi.      |Nie               |  Niski       |
|**[Interfejs API Log Analytics](#connect-with-the-log-analytics-api)** <br>Najlepsza dla niezależnych dostawców oprogramowania, które wdrażają integrację, oraz wymagań dotyczących unikatowych kolekcji   | Obsługuje wszystkie funkcje dostępne w kodzie.  | Zależy od implementacji           |     Wys.    |
|**[Azure Functions](#connect-with-azure-functions)** Najlepsze dla źródeł w chmurze o dużej pojemności i unikatowych wymagań dotyczących kolekcji  | Obsługuje wszystkie funkcje dostępne w kodzie.  |  Tak             |     Wysokowydajn wymaga znajomości programowania    |
|     |         |                |

> [!TIP]
> Aby porównań przy użyciu Logic Apps i Azure Functions dla tego samego łącznika, zobacz:
> 
> - [Szybkie pozyskiwanie dzienników zapory aplikacji sieci Web na platformie Azure — wskaźnik](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (społeczność usługi GitHub na platformie Azure): Łącznik [aplikacji logiki](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Function Connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Nawiązywanie połączenia z agentem Log Analytics

Jeśli źródło danych dostarcza zdarzenia w plikach, zalecamy użycie agenta Azure Monitor Log Analytics do utworzenia łącznika niestandardowego.

- Aby uzyskać więcej informacji, zobacz [zbieranie niestandardowych dzienników w Azure monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Aby zapoznać się z przykładem tej metody, zobacz [zbieranie niestandardowych źródeł danych JSON przy użyciu agenta log Analytics dla systemu Linux w programie Azure monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Łączenie z usługą logstash

Jeśli znasz już usługę [logstash](https://www.elastic.co/logstash), możesz chcieć użyć logstash z [dodatkiem danych wyjściowych logstash dla platformy Azure](connect-logstash.md) , aby utworzyć łącznik niestandardowy.

Za pomocą wtyczki danych wyjściowych logstash platformy Azure, można użyć dowolnych wtyczek logstash i filtrowania, a także skonfigurować wskaźnik na platformie Azure jako dane wyjściowe dla potoku logstash. Logstash ma dużą bibliotekę wtyczek, które umożliwiają wprowadzanie danych z różnych źródeł, takich jak Event Hubs, Apache Kafka, pliki, bazy danych i usługi w chmurze. Wtyczki filtrowania umożliwiają analizowanie zdarzeń, filtrowanie niepotrzebnych zdarzeń, mieszanie wartości i nie tylko.

Przykłady użycia logstash jako łącznika niestandardowego można znaleźć w temacie:

- [Łowiectwo z naruszeniem kapitału TTPs w dziennikach AWS korzystających z platformy Azure](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Przewodnik implementacji Radware platformy Azure](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Przykłady przydatnych wtyczek logstash można znaleźć w temacie:

- [Wtyczka wejściowa CloudWatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Wtyczka Event Hubs platformy Azure](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Wtyczka wejściowa magazynu Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Wtyczka wejściowa Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash umożliwia również przeskalowanie zbierania danych przy użyciu klastra. Aby uzyskać więcej informacji, zobacz [Korzystanie z maszyny wirtualnej logstash z równoważeniem obciążenia na dużą skalę](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Połącz z Logic Apps

Użyj [aplikacji logiki platformy Azure](../logic-apps/index.yml) do utworzenia bezserwerowego, niestandardowego łącznika na potrzeby platformy Azure.

> [!NOTE]
> Tworzenie łączników bezserwerowych przy użyciu Logic Apps może być wygodne, przy użyciu Logic Apps dla łączników mogą być kosztowne w przypadku dużych ilości danych.
>
> Zalecamy używanie tej metody tylko dla źródeł danych o małym woluminie lub wzbogacanie przekazywania danych.
>

1. **Aby rozpocząć Logic Apps, użyj jednego z następujących wyzwalaczy**:

    |Wyzwalacz  |Opis  |
    |---------|---------|
    |**Zadanie cykliczne**     |   Na przykład Zaplanuj aplikację logiki, aby regularnie pobierać dane z określonych plików, baz danych lub zewnętrznych interfejsów API. <br>Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy w programie Azure Logic Apps](../connectors/connectors-native-recurrence.md).      |
    |**Wyzwalanie na żądanie**     | Uruchom aplikację logiki na żądanie, aby ręcznie gromadzenie i testowanie danych. <br>Aby uzyskać więcej informacji, zobacz  [wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki za pomocą punktów końcowych https](../logic-apps/logic-apps-http-endpoint.md).        |
    |**Punkt końcowy HTTP/S**     |  Zalecane na potrzeby przesyłania strumieniowego oraz tego, czy system źródłowy może uruchomić transfer danych. <br>Aby uzyskać więcej informacji, zobacz [wywoływanie punktów końcowych usługi za pośrednictwem protokołu HTTP lub https](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Użyj dowolnego łącznika aplikacji logiki, który odczytuje informacje w celu uzyskania zdarzeń**. Na przykład:

    - [Nawiązywanie połączenia z interfejsem API REST](/connectors/custom-connectors/)
    - [Nawiązywanie połączenia z SQL Server](/connectors/sql/)
    - [Nawiązywanie połączenia z systemem plików](/connectors/filesystem/)

    > [!TIP]
    > Łączniki niestandardowe do interfejsów API REST, serwerów SQL i systemów plików obsługują również pobieranie danych z lokalnych źródeł danych. Aby uzyskać więcej informacji, zobacz [Instalowanie lokalnej bramy danych](/connectors/filesystem/) .
    >

1. **Przygotuj informacje, które chcesz pobrać**.

    Na przykład użyj [akcji Analizuj dane JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) , aby uzyskać dostęp do właściwości w zawartości JSON, umożliwiając wybranie tych właściwości z listy zawartości dynamicznej po określeniu danych wejściowych dla aplikacji logiki.

    Aby uzyskać więcej informacji, zobacz [wykonywanie operacji na danych w Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Zapisz dane do log Analytics**.

    Aby uzyskać więcej informacji, zobacz dokumentację [modułu zbierającego dane log Analytics platformy Azure](/connectors/azureloganalyticsdatacollector/) .

Aby zapoznać się z przykładami tworzenia łącznika niestandardowego dla platformy Azure, przy użyciu Logic Apps, zobacz:

- [Tworzenie potoku danych za pomocą interfejsu API modułu zbierającego dane](/connectors/azureloganalyticsdatacollector/)
- [Łącznik aplikacji logiki Palo Alto Prisma przy użyciu elementu webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (społeczność usługi GitHub platformy Azure)
- [Zabezpieczanie wywołań zespołów firmy Microsoft przy użyciu zaplanowanej aktywacji](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- Pozyskiwanie [wskaźników zagrożeń ALIENVAULT OTX na platformie Azure](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)
- [Wysyłanie dzienników PROOFPOINT TAP do usługi Azure wskaźnikowej](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blog)


## <a name="connect-with-powershell"></a>Łączenie się z programem PowerShell

[Skrypt upload-AzMonitorLog programu PowerShell](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) umożliwia strumieniowe przesyłanie zdarzeń lub informacji kontekstowych do funkcji wskaźnikowej platformy Azure z wiersza polecenia przy użyciu programu PowerShell. To przesyłanie strumieniowe efektywnie tworzy łącznik niestandardowy między źródłem danych i wskaźnikiem kontrolnym platformy Azure.

Na przykład poniższy skrypt przekazuje plik CSV do kontrolki wskaźnikowej platformy Azure:

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

Skrypt [skryptu programu PowerShell upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) używa następujących parametrów:

|Parametr  |Opis  |
|---------|---------|
|**Identyfikator obszaru roboczego**     |   Identyfikator obszaru roboczego wskaźnikowego platformy Azure, w którym będą przechowywane dane.  [Znajdź identyfikator i klucz obszaru roboczego](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Klucz podstawowy lub pomocniczy obszaru roboczego Azure wskaźnikowego, w którym będą przechowywane dane. [Znajdź identyfikator i klucz obszaru roboczego](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Nazwa niestandardowej tabeli dziennika, w której mają być przechowywane dane. Sufiks **_CL** zostanie automatycznie dodany na końcu nazwy tabeli.  |
|**Addcomputername**     |   Gdy ten parametr istnieje, skrypt dodaje bieżącą nazwę komputera do każdego rekordu dziennika w polu Nazwa **komputera**.      |
|**TaggedAzureResourceId**     | Gdy ten parametr istnieje, skrypt kojarzy wszystkie przekazane rekordy dziennika z określonym zasobem platformy Azure. <br><br>To skojarzenie umożliwia przekazywanie rekordów dziennika dla zapytań kontekstu zasobów i jest zgodne ze skoncentrowaną na zasobach kontroli dostępu.       |
|**AdditionalDataTaggingName**     |      Gdy ten parametr istnieje, skrypt dodaje kolejne pole do każdego rekordu dziennika ze skonfigurowaną nazwą i wartością skonfigurowaną dla parametru **AdditionalDataTaggingValue** . <br><br>W tym przypadku **AdditionalDataTaggingValue** nie może być pusty. |
|**AdditionalDataTaggingValue**     |   Gdy ten parametr istnieje, skrypt dodaje kolejne pole do każdego rekordu dziennika ze skonfigurowaną wartością oraz nazwę pola skonfigurowaną dla parametru **AdditionalDataTaggingName** . <br><br>Jeśli parametr **AdditionalDataTaggingName** jest pusty, ale wartość jest skonfigurowana, domyślną nazwą pola jest **znakowanie** elementu.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Znajdź identyfikator i klucz obszaru roboczego

Znajdź szczegóły parametrów **Identyfikator obszaru roboczego** i **WorkspaceKey** na platformie Azure — wskaźnik:

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Ustawienia** po lewej stronie, a następnie wybierz kartę **Ustawienia obszaru roboczego** .

1. W obszarze **wprowadzenie do log Analytics**  >  **1 łączenie źródła danych** wybierz pozycję **Zarządzanie agentami systemu Windows i Linux**.

1. Znajdź identyfikator obszaru roboczego, klucz podstawowy i klucz pomocniczy na kartach **serwery z systemem Windows** .
## <a name="connect-with-the-log-analytics-api"></a>Nawiązywanie połączenia z interfejsem API Log Analytics

Zdarzenia można przesyłać do funkcji wskaźnikowej platformy Azure przy użyciu interfejsu API modułu zbierającego dane Log Analytics, aby bezpośrednio wywołać punkt końcowy RESTful.

Wywoływanie punktu końcowego RESTful bezpośrednio wymaga większej liczby programów programistycznych, ale zapewnia również większą elastyczność.

Aby uzyskać więcej informacji, zobacz [interfejs API modułu zbierającego dane log Analytics](../azure-monitor/logs/data-collector-api.md), w szczególności następujące przykłady:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Połącz z Azure Functions

Użyj Azure Functions wraz z interfejsem API RESTful oraz różnymi językami kodowania, takimi jak program [PowerShell](../azure-functions/functions-reference-powershell.md), aby utworzyć łącznik niestandardowy bez serwera.

Aby zapoznać się z przykładami tej metody, zobacz:

- [Połącz Standard punktu końcowego chmury VMware węgla czarnego z platformą Azure](connect-vmware-carbon-black.md)
- [Połącz usługi okta pojedyncze Sign-On z funkcją wskaźnikową platformy Azure przy użyciu funkcji platformy Azure](connect-okta-single-sign-on.md)
- [Połącz swoje Proofpoint z platformą Azure, korzystając z funkcji platformy Azure](connect-proofpoint-tap.md)
- [Połącz maszynę wirtualną z programem Qualys z platformą Azure wskaźnikiem przy użyciu funkcji platformy Azure](connect-qualys-vm.md)
- [Pozyskiwanie plików XML, CSV lub innych formatów danych](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Monitorowanie powiększania za pomocą platformy Azure — wskaźnik](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Wdróż aplikacja funkcji uzyskiwania danych interfejsu API zarządzania pakietu Office 365 na platformie Azure](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) — społeczność usługi GitHub.

## <a name="parse-your-custom-connector-data"></a>Analizowanie danych łącznika niestandardowego

Możesz użyć wbudowanej metody analizy łącznika niestandardowego do wyodrębnienia odpowiednich informacji i wypełnienia odpowiednich pól na platformie Azure.

Na przykład:

- **Jeśli używasz logstash**, Użyj wtyczki filtru [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) , aby przeanalizować dane.
- **Jeśli używasz funkcji platformy Azure**, Przeanalizuj dane przy użyciu kodu. Aby uzyskać więcej informacji, zobacz [parsery](normalization.md#parsers).

Wskaźnik platformy Azure obsługuje analizowanie w czasie wykonywania zapytania. Analiza w czasie zapytania umożliwia wypychanie danych w oryginalnym formacie, a następnie analizowanie na żądanie, w razie potrzeby.

Analiza w czasie wykonywania zapytania oznacza również, że nie trzeba znać dokładnej struktury danych przed czasem, podczas tworzenia łącznika niestandardowego, a nawet informacji, które należy wyodrębnić. Zamiast tego Analizuj dane w dowolnym momencie, nawet w trakcie badania.

> [!NOTE]
> Aktualizacja analizatora ma zastosowanie również do danych, które zostały już wprowadzone do usługi Azure wskaźnikowej.
> 
## <a name="next-steps"></a>Następne kroki

Użyj danych pozyskiwanych na platformie Azure wskaźnikowej, aby zabezpieczyć środowisko przy użyciu dowolnego z następujących procesów:

- [Uzyskiwanie wglądu w alerty](quickstart-get-visibility.md)
- [Wizualizuj i monitoruj dane](tutorial-monitor-your-data.md)
- [Badanie zdarzeń](tutorial-investigate-cases.md)
- [Wykrywania zagrożeń](tutorial-detect-threats-built-in.md)
- [Automatyzowanie zapobiegania zagrożeniom](tutorial-respond-threats-playbook.md)
- [Wyszukiwanie zagrożeń](hunting.md)