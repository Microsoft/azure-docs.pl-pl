---
title: Przesyłanie strumieniowe danych monitorowania platformy Azure do Centrum zdarzeń
description: Dowiedz się, jak przesyłać strumieniowo dane monitorowania platformy Azure do centrum zdarzeń, aby uzyskać dane do siem partnera lub narzędzia analitycznego.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 32bc90cc069ac82641c3aa7692c900c60db7ba87
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733098"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń
Usługa Azure Monitor zapewnia kompletne rozwiązanie do monitorowania pełnego stosu dla aplikacji i usług na platformie Azure, w innych chmurach i lokalnie. Oprócz korzystania z usługi Azure Monitor do analizowania tych danych i wykorzystywania ich do różnych scenariuszy monitorowania, może być konieczne wysłanie ich do innych narzędzi monitorowania w danym środowisku. Najbardziej skuteczną metodą przesyłania strumieniowego danych monitorowania do narzędzi zewnętrznych w większości przypadków jest użycie [usługi Azure Event Hubs.](/azure/event-hubs/) Ten artykuł zawiera krótki opis sposobu przesyłania strumieniowego danych monitorowania z różnych źródeł do centrum zdarzeń i łączy się ze szczegółowymi wskazówkami.


## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przed skonfigurowaniem przesyłania strumieniowego dla dowolnego źródła danych należy [utworzyć obszar nazw centrum zdarzeń i centrum zdarzeń](../../event-hubs/event-hubs-create.md). Ten obszar nazw i centrum zdarzeń jest miejscem docelowym dla wszystkich danych monitorowania. Obszar nazw Centrum zdarzeń to logiczne grupowanie centrów zdarzeń, które współużytkują te same zasady dostępu, podobnie jak konto magazynu ma poszczególne obiekty blob w ramach tego konta magazynu. Należy wziąć pod uwagę następujące szczegóły dotyczące obszaru nazw centrów zdarzeń i centrów zdarzeń używanych do przesyłania strumieniowego danych monitorowania:

* Liczba jednostek przepływności umożliwia zwiększenie skali przepływności dla centrów zdarzeń. Zazwyczaj konieczne jest tylko jedno jednostki przepływności. Jeśli trzeba skalować w górę wraz ze wzrostem użycia dziennika, można ręcznie zwiększyć liczbę jednostek przepływności dla obszaru nazw lub włączyć inflację automatyczną.
* Liczba partycji umożliwia równoległość zużycia przez wielu konsumentów. Pojedyncza partycja może obsługiwać do 20 MB/s lub około 20 000 komunikatów na sekundę. W zależności od narzędzia zużywającego dane może lub nie może obsługiwać zużywa z wielu partycji. Cztery partycje jest uzasadnione, aby rozpocząć, jeśli nie masz pewności, jeśli nie jesteś pewien liczby partycji do skonfigurowania.
* Przechowywanie wiadomości w centrum zdarzeń jest ustawione na co najmniej 7 dni. Jeśli narzędzie zużywające znika na dłużej niż jeden dzień, zapewnia to, że narzędzie może odebrać miejsce, w którym zostało przerwane dla wydarzeń do 7 dni.
* Należy użyć domyślnej grupy odbiorców dla centrum zdarzeń. Nie ma potrzeby tworzenia innych grup odbiorców lub używania oddzielnej grupy odbiorców, chyba że planujesz, że dwa różne narzędzia zużywają te same dane z tego samego centrum zdarzeń.
* W dzienniku aktywności platformy Azure należy wybrać obszar nazw centrum zdarzeń, a usługa Azure Monitor tworzy centrum zdarzeń w tym obszarze nazw o nazwie _insights-logs-operational-logs_. W przypadku innych typów dzienników można wybrać istniejące centrum zdarzeń lub utworzyć centrum zdarzeń usługi Azure Monitor dla każdej kategorii dziennika.
* Port wychodzący 5671 i 5672 zazwyczaj muszą być otwierane na komputerze lub sieci wirtualnej zużywających dane z centrum zdarzeń.

## <a name="monitoring-data-available"></a>Dostępne dane z monitorowania
[Źródła danych monitorowania dla usługi Azure Monitor](data-sources.md) opisuje różne warstwy danych dla aplikacji platformy Azure i rodzaje danych monitorowania dostępnych dla każdego. W poniższej tabeli wymieniono każdą z tych warstw i opis sposobu przesyłania tych danych strumieniowo do centrum zdarzeń. Skorzystaj z linków podanych w celu uzyskania dalszych szczegółów.

| Warstwa | Dane | Metoda |
|:---|:---|:---|
| [Dzierżawa platformy Azure](data-sources.md#azure-tenant) | Dzienniki inspekcji usługi Azure Active Directory | Skonfiguruj ustawienie diagnostyczne dzierżawy w dzierżawie usługi AAD. Zobacz [samouczek: Strumieniowanie dzienników usługi Azure Active Directory do centrum zdarzeń platformy Azure, aby](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) uzyskać szczegółowe informacje. |
| [Subskrypcja platformy Azure](data-sources.md#azure-subscription) | Dziennik aktywności platformy Azure | Utwórz profil dziennika, aby wyeksportować zdarzenia dziennika aktywności do centrów zdarzeń.  Zobacz [dzienniki platformy Azure stream do usługi Azure Event Hubs, aby](resource-logs-stream-event-hubs.md) uzyskać szczegółowe informacje. |
| [Zasoby platformy Azure](data-sources.md#azure-resources) | Dane dotyczące platformy<br> Dzienniki zasobów |Oba typy danych są wysyłane do centrum zdarzeń przy użyciu ustawienia diagnostycznego zasobów. Zobacz [dzienniki zasobów platformy Azure strumienia do centrum zdarzeń, aby](resource-logs-stream-event-hubs.md) uzyskać szczegółowe informacje. |
| [System operacyjny (gość)](data-sources.md#operating-system-guest) | Maszyny wirtualne platformy Azure | Zainstaluj [rozszerzenie diagnostyki platformy Azure](diagnostics-extension-overview.md) na maszynach wirtualnych systemu Windows i Linux na platformie Azure. Zobacz [przesyłania strumieniowego danych diagnostyki platformy Azure w ścieżce gorącej przy użyciu centrum zdarzeń, aby](diagnostics-extension-stream-event-hubs.md) uzyskać szczegółowe informacje na temat maszyn wirtualnych systemu Windows i użyj rozszerzenia [diagnostycznego systemu Linux do monitorowania metryk i dzienników, aby](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) uzyskać szczegółowe informacje na temat maszyn wirtualnych z systemem Linux. |
| [Kod aplikacji](data-sources.md#application-code) | Application Insights | Usługa Application Insights nie zapewnia bezpośredniej metody przesyłania strumieniowego danych do centrów zdarzeń. Można [skonfigurować ciągłe eksportowanie](../../azure-monitor/app/export-telemetry.md) danych usługi Application Insights do konta magazynu, a następnie użyć aplikacji logiki do wysyłania danych do centrum zdarzeń, zgodnie z opisem w [ręcznym przesyłaniu strumieniowym za pomocą aplikacji logiki](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Ręczne przesyłanie strumieniowe za pomocą aplikacji Logic
W przypadku danych, których nie można bezpośrednio przesyłać strumieniowo do Centrum zdarzeń, można zapisać w magazynie platformy Azure, a następnie użyć wyzwalanej w czasie aplikacji logiki, która [pobiera dane z magazynu obiektów blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) i [wypycha je jako komunikat do centrum zdarzeń.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Narzędzia partnerów z integracją z usługą Azure Monitor

Routing danych monitorowania do centrum zdarzeń za pomocą usługi Azure Monitor umożliwia łatwą integrację z zewnętrznymi narzędziami SIEM i monitorowania. Przykłady narzędzi z integracją usługi Azure Monitor są następujące:

| Narzędzie | Hostowane na platformie Azure | Opis |
|:---|:---| :---|
|  IBM QRadar | Nie | Protokół Microsoft Azure DSM i Microsoft Azure Event Hub Protocol są dostępne do pobrania z [witryny pomocy technicznej IBM.](https://www.ibm.com/support) Więcej informacji na temat integracji z platformą Azure można uzyskać w [konfiguracji QRadar DSM.](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) |
| Splunk | Nie | [Dodatek Azure Monitor dla Splunk](https://splunkbase.splunk.com/app/3534/) to projekt typu open source dostępny w splunkbase. Dokumentacja jest dostępna w [usłudze Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Jeśli nie można zainstalować dodatek w wystąpieniu Splunk, jeśli na przykład używasz serwera proxy lub działa w Splunk Cloud, można przesłać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk przy użyciu [funkcji Azure dla splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), który jest wyzwalany przez nowe wiadomości w centrum zdarzeń. |
| SumoLogic | Nie | Instrukcje konfigurowania sumologic do korzystania z danych z centrum zdarzeń są dostępne w [collect dzienniki dla aplikacji inspekcji platformy Azure z Centrum zdarzeń](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Nie | Inteligentny łącznik usługi ArcSight Azure Event Hub jest dostępny jako część [kolekcji inteligentnych łączników ArcSight.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Serwer Syslog | Nie | Jeśli chcesz przesyłać strumieniowo dane usługi Azure Monitor bezpośrednio do serwera syslog, możesz użyć [rozwiązania opartego na funkcji platformy Azure.](https://github.com/miguelangelopereira/azuremonitor2syslog/)
| LogRhythm (LogRhythm) | Nie| Instrukcje konfigurowania LogRhythm do zbierania dzienników z centrum zdarzeń są dostępne [tutaj](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Tak | Aby uzyskać więcej informacji, zobacz [Wprowadzenie do monitorowania i rejestrowania przy użyciu aplikacji Logz.io dla aplikacji Java działających na platformie Azure](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>Następne kroki
* [Archiwizowanie dziennika aktywności na koncie magazynu](../../azure-monitor/platform/archive-activity-log.md)
* [Przeczytaj omówienie dziennika aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Konfigurowanie alertu na podstawie zdarzenia dziennika aktywności](../../azure-monitor/platform/alerts-log-webhook.md)


