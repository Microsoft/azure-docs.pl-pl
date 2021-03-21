---
title: Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń i partnerów zewnętrznych
description: Dowiedz się, jak przesyłać dane monitorowania platformy Azure do centrum zdarzeń w celu pobrania danych do narzędzia partner SIEM lub analizy.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: a929563df3e7e98575056d07519abfda0d6ac13b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032963"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń lub zewnętrznego partnera

Azure Monitor zapewnia pełne rozwiązanie do monitorowania stosu dla aplikacji i usług na platformie Azure, w innych chmurach i lokalnie. Oprócz Azure Monitor do analizowania danych i korzystania z nich w różnych scenariuszach monitorowania może być konieczne wysłanie ich do innych narzędzi do monitorowania w środowisku. W większości przypadków najbardziej efektywna metoda przesyłania strumieniowego danych monitorowania do zewnętrznych narzędzi korzysta z [usługi Azure Event Hubs](../../event-hubs/index.yml). Ten artykuł zawiera krótki opis sposobu wykonania tej czynności, a następnie zawiera listę partnerów, w których można wysyłać dane. Niektóre mają specjalną integrację z usługą Azure Monitor i mogą być hostowane na platformie Azure.  

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przed skonfigurowaniem przesyłania strumieniowego dla dowolnego źródła danych należy [utworzyć Event Hubs przestrzeni nazw i centrum zdarzeń](../../event-hubs/event-hubs-create.md). Ta przestrzeń nazw i centrum zdarzeń to miejsce docelowe dla wszystkich danych monitorowania. Przestrzeń nazw Event Hubs to logiczna Grupa centrów zdarzeń, które współużytkują te same zasady dostępu, podobnie jak konto magazynu ma poszczególne obiekty blob w ramach tego konta magazynu. Należy wziąć pod uwagę następujące szczegóły dotyczące przestrzeni nazw centrów zdarzeń i centrów zdarzeń, które są używane do przesyłania strumieniowego danych monitorowania:

* Liczba jednostek przepływności pozwala zwiększyć skalę przepływności dla centrów zdarzeń. Zwykle konieczna jest tylko jedna jednostka przepływności. Jeśli konieczne jest skalowanie w górę w miarę wzrostu użycia dzienników, można ręcznie zwiększyć liczbę jednostek przepływności dla przestrzeni nazw lub włączyć automatyczne inflację.
* Liczba partycji pozwala zrównoleglanie użycie wielu odbiorców. Pojedyncza partycja może obsługiwać do 20MBps lub około 20 000 komunikatów na sekundę. W zależności od narzędzia, które korzysta z danych, może być nieobsługiwane w przypadku używania wielu partycji. Jeśli nie masz pewności o liczbie partycji do ustawienia, możesz zacząć od czterech partycji.
* Należy ustawić przechowywanie komunikatów w centrum zdarzeń na co najmniej 7 dni. Jeśli narzędzie do konsumowania działa przez więcej niż jeden dzień, zapewnia to, że narzędzie może zostać wznowione w miejscu, w którym wyłączono do 7 dni.
* Należy użyć domyślnej grupy odbiorców dla centrum zdarzeń. Nie ma potrzeby tworzenia innych grup konsumenckich ani używania oddzielnej grupy odbiorców, chyba że planujesz, aby dwa różne narzędzia korzystały z tych samych danych z tego samego centrum zdarzeń.
* W przypadku dziennika aktywności platformy Azure można wybrać Event Hubs przestrzeń nazw, a Azure Monitor tworzy centrum zdarzeń w tej przestrzeni nazw o nazwie _Insights-Logs-Logs-Logs_. W przypadku innych typów dzienników można wybrać istniejące centrum zdarzeń lub Azure Monitor utworzyć centrum zdarzeń dla każdej kategorii dziennika.
* Porty wychodzące 5671 i 5672 muszą być zwykle otwarte na komputerze lub w sieci wirtualnej wykorzystującej dane z centrum zdarzeń.

## <a name="monitoring-data-available"></a>Dane monitorowania są dostępne
[Źródła danych monitorowania dla Azure monitor](../agents/data-sources.md) opisuje różne warstwy danych dla aplikacji platformy Azure oraz rodzaje danych monitorowania dostępnych dla każdego z nich. W poniższej tabeli wymieniono wszystkie te warstwy oraz opis sposobu przesyłania strumieniowego danych do centrum zdarzeń. Postępuj zgodnie z podanymi linkami, aby uzyskać więcej szczegółowych informacji.

| Warstwa | Dane | Metoda |
|:---|:---|:---|
| [Dzierżawa platformy Azure](../agents/data-sources.md#azure-tenant) | Azure Active Directory dzienników inspekcji | Skonfiguruj ustawienia diagnostyczne dzierżawy w dzierżawie usługi AAD. Aby uzyskać szczegółowe informacje, zobacz  [Samouczek: przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) . |
| [Subskrypcja platformy Azure](../agents/data-sources.md#azure-subscription) | Dziennik aktywności platformy Azure | Utwórz profil dziennika, aby wyeksportować zdarzenia dziennika aktywności do Event Hubs.  Aby uzyskać szczegółowe informacje [, zobacz Przesyłanie strumieniowe dzienników platformy Azure do usługi azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) . |
| [Zasoby platformy Azure](../agents/data-sources.md#azure-resources) | Metryki platformy<br> Dzienniki zasobów |Oba typy danych są wysyłane do centrum zdarzeń przy użyciu ustawienia diagnostycznego zasobu. Aby uzyskać szczegółowe informacje [, zobacz Przesyłanie strumieniowe dzienników zasobów platformy Azure do centrum zdarzeń](../essentials/resource-logs.md#send-to-azure-event-hubs) . |
| [System operacyjny (gościa)](../agents/data-sources.md#operating-system-guest) | Maszyny wirtualne platformy Azure | Zainstaluj [rozszerzenie Diagnostyka Azure](../agents/diagnostics-extension-overview.md) na maszynach wirtualnych z systemem Windows i Linux na platformie Azure. Zobacz [przesyłanie strumieniowe danych Diagnostyka Azure w ścieżce aktywnej przy użyciu Event Hubs](../agents/diagnostics-extension-stream-event-hubs.md) , aby uzyskać szczegółowe informacje na temat maszyn wirtualnych z systemem Windows i [użyć rozszerzenia diagnostyki systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) w celu uzyskania szczegółowych informacji na temat maszyn wirtualnych |
| [Kod aplikacji](../agents/data-sources.md#application-code) | Application Insights | Application Insights nie zapewnia bezpośredniej metody przesyłania strumieniowego danych do centrów zdarzeń. Można [skonfigurować ciągły eksport](../app/export-telemetry.md) danych Application Insights do konta magazynu, a następnie użyć aplikacji logiki do wysłania danych do centrum zdarzeń zgodnie z opisem w artykule [Ręczne przesyłanie strumieniowe za pomocą aplikacji logiki](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Ręczne przesyłanie strumieniowe za pomocą aplikacji logiki
W przypadku danych, które nie są bezpośrednio przesyłane do centrum zdarzeń, możesz zapisywać w usłudze Azure Storage, a następnie używać wyzwalanej przez czas aplikacji logiki, która [Pobiera dane z magazynu obiektów BLOB](../../connectors/connectors-create-api-azureblobstorage.md#add-action) , i [wypycha ją jako komunikat do centrum zdarzeń](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Narzędzia partnerskie z integracją Azure Monitor

Kierowanie danych monitorowania do centrum zdarzeń za pomocą Azure Monitor pozwala na łatwą integrację z zewnętrznymi narzędziami SIEM i monitorowania. Przykłady narzędzi z integracją Azure Monitor są następujące:

| Narzędzie | Hostowane na platformie Azure | Opis |
|:---|:---| :---|
|  IBM QRadar | Nie | Microsoft Azure DSM i Microsoft Azure centrum zdarzeń jest dostępny do pobrania z [witryny sieci Web pomocy technicznej firmy IBM](https://www.ibm.com/support). Możesz dowiedzieć się więcej o integracji z platformą Azure w [konfiguracji DSM QRadar](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Nie | [Microsoft Azure Add-On dla Splunk](https://splunkbase.splunk.com/app/3757/) jest projektem open source dostępnym w Splunkbase. <br><br> Jeśli nie można zainstalować dodatku w wystąpieniu usługi Splunk, jeśli na przykład używasz serwera proxy lub działającego w chmurze Splunk, możesz przesłać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk za pomocą [funkcji platformy Azure dla Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), która jest wyzwalana przez nowe komunikaty w centrum zdarzeń. |
| SumoLogic | Nie | Instrukcje dotyczące konfigurowania SumoLogic do korzystania z danych z centrum zdarzeń są dostępne w [dziennikach zbierania dla aplikacji inspekcji platformy Azure z centrum zdarzeń](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Nie | Łącznik inteligentny ArcSight usługi Azure Event Hub jest dostępny jako część [kolekcji inteligentnych łączników ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Serwer Syslog | Nie | Jeśli chcesz przesyłać strumieniowo Azure Monitor dane bezpośrednio do serwera dziennika systemowego, możesz użyć [rozwiązania na podstawie funkcji platformy Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Nie| Instrukcje dotyczące konfigurowania LogRhythm do zbierania dzienników z centrum zdarzeń są dostępne [tutaj](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Tak | Aby uzyskać więcej informacji, zobacz [wprowadzenie do monitorowania i rejestrowania przy użyciu programu Logz.IO for Java Apps uruchomionego na platformie Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Inni partnerzy mogą również być dostępni. Aby zapoznać się z bardziej kompletną listą wszystkich partnerów Azure Monitor i ich możliwości, zobacz [Azure monitor integracji partnerów](../partners.md).

## <a name="next-steps"></a>Następne kroki
* [Archiwizowanie dziennika aktywności na koncie magazynu](./activity-log.md#legacy-collection-methods)
* [Zapoznaj się z omówieniem dziennika aktywności platformy Azure](../essentials/platform-logs-overview.md)
* [Konfigurowanie alertu na podstawie zdarzenia dziennika aktywności](../alerts/alerts-log-webhook.md)