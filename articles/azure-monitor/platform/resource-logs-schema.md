---
title: Obsługiwane usługi i schematy dzienników zasobów platformy Azure
description: Poznaj obsługiwane usługi i schemat zdarzeń dla dzienników zasobów platformy Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 17b4b161e76f018d8f669ee7e9b5dd578bb3e035
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278400"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Wspólny schemat dotyczący dzienników zasobów platformy Azure

> [!NOTE]
> Dzienniki zasobów były wcześniej znane jako dzienniki diagnostyczne. Nazwa została zmieniona w październiku 2019, ponieważ typy dzienników zebrane przez Azure Monitor przesunięte w celu uwzględnienia więcej niż tylko zasobów platformy Azure. Ponadto lista kategorii dzienników zasobów, które można zbierać, została wyświetlona w tym artykule. Zostały przeniesione do [kategorii dzienników zasobów](resource-logs-categories.md). 

[Dzienniki zasobów Azure monitor](./platform-logs-overview.md) to dzienniki wysyłane przez usługi platformy Azure opisujące operacje tych usług lub zasobów. Wszystkie dzienniki zasobów dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń.

Kombinacja typu zasobu (dostępnego we `resourceId` Właściwości) i `category` unikatowo identyfikujący schemat. W tym artykule opisano schemat najwyższego poziomu dla dzienników zasobów i linki do schematu dla każdej usługi.


## <a name="top-level-common-schema"></a>Wspólny schemat najwyższego poziomu

| Nazwa | Wymagane/Opcjonalne | Opis |
|---|---|---|
| time | Wymagane | Sygnatura czasowa zdarzenia (UTC). |
| resourceId | Wymagane | Identyfikator zasobu, który emituje zdarzenie. W przypadku usług dzierżawców jest to forma/tenants/tenant-ID/Providers/Provider-Name. |
| tenantId | Wymagane w przypadku dzienników dzierżawy | Identyfikator dzierżawy dzierżawy Active Directory, z którym jest powiązane to zdarzenie. Ta właściwość jest używana tylko w przypadku dzienników na poziomie dzierżawy, ale nie jest wyświetlana w dziennikach na poziomie zasobów. |
| operationName | Wymagane | Nazwa operacji reprezentowanej przez to zdarzenie. Jeśli zdarzenie reprezentuje operację RBAC, jest to nazwa operacji RBAC (na przykład Microsoft. Storage/storageAccounts/blobServices/BLOB/Read). Zwykle modelowane w formie Menedżer zasobów operacji, nawet jeśli nie są rzeczywiste udokumentowane operacje Menedżer zasobów ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Opcjonalne | Wersja interfejsu API skojarzona z operacją, jeśli OperationName została wykonana przy użyciu interfejsu API (na przykład `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Jeśli nie ma interfejsu API odpowiadającego tej operacji, wersja reprezentuje wersję tej operacji w przypadku, gdy właściwości skojarzone z operacją zmieniają się w przyszłości. |
| category | Wymagane | Kategoria dziennika zdarzenia. Kategoria to stopień szczegółowości, w którym można włączyć lub wyłączyć dzienniki dla określonego zasobu. Właściwości, które pojawiają się w obiekcie blob właściwości zdarzenia są takie same w określonej kategorii dziennika i typie zasobu. Typowe kategorie dzienników to "inspekcja" "działania" "wykonywanie" i "żądanie". |
| resultType | Opcjonalne | Stan zdarzenia. Typowe wartości to: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne i rozwiązane. |
| resultSignature | Opcjonalne | Stan podrzędny zdarzenia. Jeśli ta operacja odnosi się do wywołania interfejsu API REST, to pole jest kodem stanu HTTP odpowiedniego wywołania REST. |
| resultDescription | Opcjonalne | Opis tekstu statycznego tej operacji, na przykład "Pobierz plik magazynu". |
| durationMs | Opcjonalne | Czas trwania operacji w milisekundach. |
| callerIpAddress | Opcjonalne | Adres IP wywołującego, jeśli operacja odnosi się do wywołania interfejsu API, które mogłoby pochodzić z jednostki z publicznie dostępnym adresem IP. |
| correlationId | Opcjonalne | Identyfikator GUID służący do grupowania razem z zestawem powiązanych zdarzeń. Zazwyczaj Jeśli dwa zdarzenia mają tę samą wartość OperationName, ale dwa różne stany (na przykład "uruchomiono" i "powodzenie"), współużytkują ten sam identyfikator korelacji. Może to również reprezentować inne relacje między zdarzeniami. |
| identity | Opcjonalne | Obiekt BLOB JSON, który opisuje tożsamość użytkownika lub aplikacji, która wykonała operację. Zazwyczaj to pole zawiera token autoryzacji i oświadczeń/tokenu JWT z usługi Active Directory. |
| Poziom | Opcjonalne | Poziom ważności zdarzenia. Musi to być jeden z informacji, ostrzegawczy, błąd lub krytyczny. |
| location | Opcjonalne | Region zasobu emitującego zdarzenie, na przykład "Wschodnie stany USA" lub "Francja Południowa" |
| properties | Opcjonalne | Wszystkie rozszerzone właściwości powiązane z tą określoną kategorią zdarzeń. Wszystkie właściwości niestandardowe/unikatowe należy umieścić w tym "części B" schematu. |

## <a name="service-specific-schemas"></a>Schematy specyficzne dla usługi

Schemat dzienników zasobów różni się w zależności od kategorii zasobów i dzienników. Ta lista zawiera usługi, które udostępniają dostępne dzienniki zasobów i linki do usługi i schematu specyficznego dla kategorii, gdzie są dostępne. Ta lista jest zmieniana przez cały czas po dodaniu nowych usług, więc jeśli nie widzisz tego, czego potrzebujesz, użyj aparatu wyszukiwania do odnajdywania dodatkowej dokumentacji. Możesz otworzyć problem z usługą GitHub w tym artykule, aby móc go zaktualizować.

| Usługa | Dokumentacja & schematu |
| --- | --- |
| Usługa Azure Active Directory | [Przegląd](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schemat dziennika inspekcji](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) i [schemat logowania](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services — rejestrowanie diagnostyczne](../../analysis-services/analysis-services-logging.md) |
| API Management | [Dzienniki zasobów API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Bramy aplikacji |[Rejestrowanie Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics dla Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Rejestrowanie Azure Batch](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Rejestrowanie w usłudze Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Rejestrowanie Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Dzienniki platformy Azure dla usługi CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Rejestrowanie Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Fabryka danych | [Monitorowanie fabryk danych przy użyciu Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Uzyskiwanie dostępu do dzienników dla Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Uzyskiwanie dostępu do dzienników dla Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Data Explorer | [Dzienniki usługi Azure Eksplorator danych](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Azure Database for MySQL dzienników diagnostycznych](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Dzienniki Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Rejestrowanie diagnostyczne w usłudze Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Konfigurowanie diagnostyki usługi Azure Digital bliźniaczych reprezentacji](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Dzienniki usługi Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat jest niedostępny. |
| Azure Firewall | Schemat jest niedostępny. |
| Front Door | [Rejestrowanie dla drzwi przednich](../../frontdoor/front-door-diagnostics.md) |
| Usługa IoT Hub | [Operacje IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Usługa Key Vault |[Rejestrowanie Azure Key Vault](../../key-vault/general/logging.md) |
| Kubernetes Service |[Rejestrowanie usługi Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Moduł równoważenia obciążenia |[Analiza dzienników dotyczących usługi Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps — niestandardowy schemat śledzenia B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupy zabezpieczeń sieci |[Usługa Log Analytics dla sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrona przed atakami DDOS | [Zarządzanie Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI — warstwa Dedykowana | [Rejestrowanie Power BI Embedded na platformie Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Model danych dla Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Wyszukaj |[Włączanie i używanie Analiza ruchu wyszukiwania](../../search/search-traffic-analytics.md) |
| Service Bus |[Dzienniki Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Baza danych SQL | [Rejestrowanie Azure SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Dzienniki zadań](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager schematu dziennika](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sieci wirtualne | Schemat jest niedostępny. |
| Bramy sieci wirtualnej | Schemat jest niedostępny. |



## <a name="next-steps"></a>Następne kroki

* [Zobacz kategorie dzienników zasobów, które można zbierać](resource-logs-categories.md)
* [Dowiedz się więcej o dziennikach zasobów](./platform-logs-overview.md)
* [Przesyłanie strumieniowe dzienników zasobów zasobów do **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Zmienianie ustawień diagnostycznych dziennika zasobów przy użyciu interfejsu API REST Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analizowanie dzienników z usługi Azure Storage za pomocą Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

