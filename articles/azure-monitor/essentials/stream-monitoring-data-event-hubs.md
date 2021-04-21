---
title: Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń i partnerów zewnętrznych
description: Dowiedz się, jak przesyłać strumieniowo dane monitorowania platformy Azure do centrum zdarzeń, aby pobrać dane do rozwiązania SIEM partnera lub narzędzia analitycznego.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 7592935afadc88c4b9e0e5f3c5f9c83d42c63209
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768745"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń lub partnera zewnętrznego

Azure Monitor zapewnia kompletne rozwiązanie do monitorowania pełnego stosu dla aplikacji i usług na platformie Azure, w innych chmurach i w środowisku lokalnym. Oprócz używania Azure Monitor do analizowania tych danych i korzystania z nich w różnych scenariuszach monitorowania może być konieczne wysłanie ich do innych narzędzi do monitorowania w środowisku. W większości przypadków najbardziej efektywną metodą przesyłania strumieniowego danych monitorowania do narzędzi zewnętrznych jest użycie Azure Event Hubs [.](../../event-hubs/index.yml) Ten artykuł zawiera krótki opis tego, jak to zrobić, a następnie zawiera listę niektórych partnerów, do których można wysyłać dane. Niektóre z nich mają specjalną integrację z Azure Monitor i mogą być hostowane na platformie Azure.  

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przed skonfigurowaniem przesyłania strumieniowego dla dowolnego źródła danych należy utworzyć przestrzeń [Event Hubs i centrum zdarzeń](../../event-hubs/event-hubs-create.md). Ta przestrzeń nazw i centrum zdarzeń są miejscem docelowym wszystkich danych monitorowania. Przestrzeń Event Hubs to logiczne grupowanie centrów zdarzeń, które współużytkuje te same zasady dostępu, podobnie jak konto magazynu ma pojedyncze obiekty blob w ramach tego konta magazynu. Rozważ następujące szczegółowe informacje o przestrzeni nazw usługi Event Hubs i centrach zdarzeń, które są wykorzystywane do przesyłania strumieniowego danych monitorowania:

* Liczba jednostek przepływności pozwala zwiększyć skalę przepływności dla centrów zdarzeń. Zazwyczaj potrzebna jest tylko jedna jednostka przepływności. Jeśli musisz skalować w górę w miarę wzrostu użycia dziennika, możesz ręcznie zwiększyć liczbę jednostek przepływności dla przestrzeni nazw lub włączyć automatyczne zwiększanie.
* Liczba partycji umożliwia zrównoleglić zużycie przez wielu użytkowników. Pojedyncza partycja może obsługiwać do 20 MB/s lub około 20 000 komunikatów na sekundę. W zależności od narzędzia zużywającego dane może ono obsługiwać korzystanie z wielu partycji lub nie. Jeśli nie masz pewności co do liczby partycji do ustawienia, warto zacząć od czterech partycji.
* Okres przechowywania komunikatów w centrum zdarzeń jest ustawiany na co najmniej 7 dni. Jeśli narzędzie konsumowania nie działa dłużej niż przez jeden dzień, gwarantuje to, że narzędzie będzie w stanie chcieć odebrać miejsce, w którym zostało ono wyłączone w przypadku zdarzeń do 7 dni wcześniej.
* Należy użyć domyślnej grupy odbiorców dla centrum zdarzeń. Nie ma potrzeby tworzenia innych grup odbiorców ani używania oddzielnej grupy odbiorców, chyba że dwa różne narzędzia używają tych samych danych z tego samego centrum zdarzeń.
* W przypadku dziennika aktywności platformy Azure należy wybrać przestrzeń nazw Event Hubs, a Azure Monitor tworzy centrum zdarzeń w tej przestrzeni nazw o nazwie _insights-logs-operational-logs._ W przypadku innych typów dzienników można wybrać istniejące centrum zdarzeń lub utworzyć Azure Monitor dla kategorii dzienników.
* Port wychodzący 5671 i 5672 musi być zwykle otwarty na komputerze lub w sieci wirtualnej, na których są używane dane z centrum zdarzeń.

## <a name="monitoring-data-available"></a>Dostępne dane monitorowania
[Źródła danych monitorowania dla Azure Monitor](../agents/data-sources.md) opisują różne warstwy danych dla aplikacji platformy Azure i rodzaje danych monitorowania dostępnych dla każdej z nich. W poniższej tabeli wymieniono każdą z tych warstw oraz opis sposobu przesyłania strumieniowego tych danych do centrum zdarzeń. Aby uzyskać więcej informacji, skorzystaj z podanych linków.

| Warstwa | Dane | Metoda |
|:---|:---|:---|
| [Dzierżawa platformy Azure](../agents/data-sources.md#azure-tenant) | Azure Active Directory inspekcji | Skonfiguruj ustawienie diagnostyczne dzierżawy w dzierżawie usługi AAD. Aby  [uzyskać szczegółowe informacje, zobacz Samouczek:](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure. |
| [Subskrypcja platformy Azure](../agents/data-sources.md#azure-subscription) | Dziennik aktywności platformy Azure | Utwórz profil dziennika, aby wyeksportować zdarzenia dziennika aktywności do Event Hubs.  Aby uzyskać szczegółowe informacje, zobacz [Stream Azure platform logs to Azure Event Hubs (Przesyłanie](../essentials/resource-logs.md#send-to-azure-event-hubs) strumieniowe dzienników platformy Azure do usługi Azure Stream). |
| [Zasoby platformy Azure](../agents/data-sources.md#azure-resources) | Metryki platformy<br> Dzienniki zasobów |Oba typy danych są wysyłane do centrum zdarzeń przy użyciu ustawienia diagnostycznego zasobu. Aby uzyskać szczegółowe informacje, zobacz Stream [Azure resource logs to an event hub (Przesyłanie](../essentials/resource-logs.md#send-to-azure-event-hubs) strumieniowe dzienników zasobów platformy Azure do centrum zdarzeń). |
| [System operacyjny (gość)](../agents/data-sources.md#operating-system-guest) | Maszyny wirtualne platformy Azure | Zainstaluj rozszerzenie [Diagnostyka Azure na maszynach](../agents/diagnostics-extension-overview.md) wirtualnych z systemami Windows i Linux na platformie Azure. Aby uzyskać szczegółowe informacje na temat maszyn wirtualnych z systemem Linux, zobacz Streaming Diagnostyka Azure data in the hot path by using Event Hubs (Przesyłanie strumieniowe Diagnostyka Azure danych w ścieżce gorąca przy użyciu programu [Event Hubs)](../agents/diagnostics-extension-stream-event-hubs.md) i Use Linux Diagnostic Extension to monitor [metrics and](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) logs (Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników). |
| [Kod aplikacji](../agents/data-sources.md#application-code) | Application Insights | Application Insights nie zapewnia bezpośredniej metody przesyłania strumieniowego danych do centrów zdarzeń. Możesz skonfigurować [ciągły eksport](../app/export-telemetry.md) danych usługi Application Insights na konto magazynu, a następnie użyć aplikacji logiki do wysyłania danych do centrum zdarzeń zgodnie z opisem w teście Ręczne przesyłanie strumieniowe za pomocą aplikacji [logiki](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Ręczne przesyłanie strumieniowe za pomocą aplikacji logiki
W przypadku danych, których nie można bezpośrednio przesyłać strumieniowo do centrum zdarzeń, można zapisywać w usłudze Azure Storage, a następnie używać wyzwalanych przez czas aplikacji logiki, która ściąga dane z magazynu [obiektów blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) i wypycha je jako komunikat do [centrum zdarzeń.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Narzędzia partnerskie z integracją Azure Monitor integracji

Routing danych monitorowania do centrum zdarzeń za pomocą Azure Monitor umożliwia łatwe integrowanie z zewnętrznymi rozwiązaniami SIEM i narzędziami do monitorowania. Przykłady narzędzi z integracją Azure Monitor są następujące:

| Narzędzie | Hostowane na platformie Azure | Opis |
|:---|:---| :---|
|  IBM QRadar | Nie | Pliki Microsoft Azure DSM i Microsoft Azure Event Hub Protocol można pobrać ze strony pomocy technicznej [firmy IBM.](https://www.ibm.com/support) Aby dowiedzieć się więcej na temat integracji z platformą Azure, zobacz [QRadar DSM configuration (Konfiguracja QRadar DSM).](https://www.ibm.com/docs/en/dsm?topic=options-configuring-microsoft-azure-event-hubs-communicate-qradar) |
| Splunk | Nie | [Microsoft Azure Add-On for Splunk](https://splunkbase.splunk.com/app/3757/) to open source dostępny w programie Splunkbase. <br><br> Jeśli nie możesz zainstalować dodatku w wystąpieniu splunku, jeśli na przykład używasz serwera proxy lub działasz w chmurze Splunk Cloud, możesz przesyłać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk przy użyciu funkcji platformy Azure dla [splunk,](https://github.com/Microsoft/AzureFunctionforSplunkVS)która jest wyzwalana przez nowe komunikaty w centrum zdarzeń. |
| SumoLogic | Nie | Instrukcje dotyczące konfigurowania serwera SumoLogic w celu przetwarzania danych z centrum zdarzeń są dostępne na stronie Collect Logs for the Azure Audit App from Event Hub (Zbieranie dzienników dla aplikacji inspekcji platformy [Azure z centrum zdarzeń).](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) |
| ArcSight | Nie | Inteligentny łącznik usługi Azure Event Hub w usłudze ArcSight jest dostępny w ramach [kolekcji inteligentnych łączników usługi ArcSight.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Serwer Syslog | Nie | Jeśli chcesz strumieniowo przesyłać Azure Monitor bezpośrednio do serwera syslog, możesz użyć rozwiązania [opartego na funkcji platformy Azure.](https://github.com/miguelangelopereira/azuremonitor2syslog/)
| LogRhythm | Nie| Instrukcje dotyczące skonfigurowania usługi LogRhythm do zbierania dzienników z centrum zdarzeń są [dostępne tutaj.](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/) 
|Logz.io | Tak | Aby uzyskać więcej informacji, zobacz Getting started with monitoring and logging using Logz.io for Java apps running on Azure (Wprowadzenie do monitorowania i rejestrowania przy [użyciu programu Logz.io dla aplikacji Java działających na platformie Azure)](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Inni partnerzy mogą być również dostępni. Aby uzyskać bardziej pełną listę wszystkich partnerów Azure Monitor i ich możliwości, zobacz [Azure Monitor integracji z partnerami.](../partners.md)

## <a name="next-steps"></a>Następne kroki
* [Archiwizowanie dziennika aktywności na koncie magazynu](./activity-log.md#legacy-collection-methods)
* [Przeczytaj omówienie dziennika aktywności platformy Azure](../essentials/platform-logs-overview.md)
* [Konfigurowanie alertu na podstawie zdarzenia dziennika aktywności](../alerts/alerts-log-webhook.md)
