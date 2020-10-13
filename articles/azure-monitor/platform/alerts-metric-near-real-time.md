---
title: Obsługiwane zasoby dla alertów metryk w Azure Monitor
description: Informacje na temat metryk i dzienników pomocy technicznej na temat alertów dotyczących metryk w Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 9/30/2020
ms.subservice: alerts
ms.openlocfilehash: 96f0cda9bdf6a17adb4d8d50b63ef5588e52f700
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578108"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Obsługiwane zasoby dla alertów metryk w Azure Monitor

Azure Monitor teraz obsługuje [nowy typ alertu metryki](./alerts-overview.md) , który ma znaczące korzyści w stosunku do starszych [klasycznych alertów dotyczących metryk](./alerts-classic.overview.md). Metryki są dostępne dla [dużej listy usług platformy Azure](./metrics-supported.md). Nowsze alerty obsługują (rosnący) podzbiór typów zasobów. W tym artykule wymieniono ten podzbiór.

Możesz również używać nowszych alertów metryk dla popularnych danych dziennika przechowywanych w Log Analytics obszarze roboczym wyodrębnionym jako metryki. Aby uzyskać więcej informacji, zobacz [alerty metryk dla dzienników](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, interfejsu wiersza polecenia, obsługa REST
Obecnie można tworzyć nowsze alerty metryk tylko w [szablonach](./alerts-metric-create-templates.md)Azure Portal, [interfejsu API REST](/rest/api/monitor/metricalerts/)lub Menedżer zasobów. Wkrótce będzie dostępna obsługa konfigurowania nowszych alertów przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej.

## <a name="metrics-and-dimensions-supported"></a>Obsługiwane metryki i wymiary
Nowsze alerty metryk obsługują alerty dotyczące metryk, które korzystają z wymiarów. Wymiarów można użyć do filtrowania metryki na odpowiedni poziom. Wszystkie obsługiwane metryki wraz z odpowiednimi wymiarami mogą być zbadane i wizualizacje z [Azure Monitor Eksplorator metryk](./metrics-charts.md).

Oto pełna lista źródeł metryk usługi Azure monitor obsługiwanych przez nowsze alerty:

|Typ zasobu  |Obsługiwane wymiary |Alerty dotyczące obsługi zasobów| Dostępne metryki|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Tak | Nie | |
|Microsoft.ApiManagement/service | Tak | Nie | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Tak | Nie | [Konfiguracja aplikacji](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/Sprężyna | Tak | Nie | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Tak| Nie | [Konta automatyzacji](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. Automatyczna synchronizacja/privateClouds | Nie | Nie | |
|Microsoft.Batch/batchAccounts | Tak | Nie | [Konta usługi Batch](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Tak | Tak | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domainNames/gniazda/role | Nie | Nie | [Cloud Services klasyczny](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nie | Nie | [Virtual Machines klasyczny](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Tak | Nie | [Konta magazynu (klasyczne)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Tak | Nie | |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Tak | Nie | |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Tak | Nie | |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Tak | Nie | |
|Microsoft. CognitiveServices/konta | Tak | Nie | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Tak | Tak<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Tak | Nie |[Zestawy skalowania maszyn wirtualnych](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Tak| Nie | [Grupy kontenerów](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/rejestry | Nie | Nie | [Rejestry kontenerów](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Tak | Nie | [Zarządzane klastry](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Tak | Tak | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactors| Tak| Nie | [Fabryki danych v1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/fabryki |Tak | Nie | [Fabryki danych v2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. dataudział/konta | Tak | Nie | |
|Microsoft. DBforMariaDB/serwery | Nie | Nie | [Baza danych dla MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/serwery | Nie | Nie |[Baza danych dla programu MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Tak | Nie | |
|Microsoft. DBforPostgreSQL/serwery | Nie | Nie | [Baza danych dla PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nie | Nie | [DB for PostgreSQL v2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/singleservers | Nie | Nie | [DB for PostgreSQL (pojedyncze serwery)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft. Devices/IotHubs | Tak | Nie |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Tak | Nie | [Usługi Device Provisioning](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Tak | Nie | |
|Microsoft.DocumentDB/databaseAccounts | Tak | Nie | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domeny | Tak | Nie | [Domeny usługi Event Grid](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Tak | Nie | [Tematy dotyczące systemu Event Grid](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/tematy |Tak | Nie | [Tematy usługi Event Grid](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/klastry |Tak| Nie | [Klastry Event Hubs](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/przestrzenie nazw |Tak| Nie | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/Klastry | Tak | Nie | [Klastry HDInsight](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/składniki | Tak | Nie | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft./magazyny kluczy | Tak |Tak |[Magazyny](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/klastry | Tak |Nie |[Klastry Eksplorator danych](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Tak | Nie |[Środowiska usługi integracji](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/przepływy pracy | Nie | Nie |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/obszary robocze | Tak | Nie | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/konta | Tak | Nie | [Mapuje konta](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | Nie | Nie | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/streamingEndpoints | Tak | Nie | [Media Services punkty końcowe przesyłania strumieniowego](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Tak | Tak | [Pule pojemności usługi Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | Tak | Tak | [Woluminy usługi Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Tak | Nie | [Bramy aplikacji](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Tak | Nie | [Zapory](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nie | Nie | [Strefy DNS](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Nie dotyczy | Nie |[Obwody usługi ExpressRoute](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/loadBalancers (tylko w przypadku standardowych jednostek SKU)| Tak| Nie | [Moduły równoważenia obciążenia](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nie | Nie | |
|Microsoft. Network/privateEndpoints| Nie | Nie | |
|Microsoft. Network/privateLinkServices| Nie | Nie |
|Microsoft. Network/adresów publicipaddress | Nie | Nie |[Publiczne adresy IP](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Tak | Nie | [Profile usługi Traffic Manager](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/obszary robocze| Tak | Nie | [Obszary robocze usługi Log Analytics](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. Komunikacja równorzędna/Komunikacja równorzędna | Tak | Nie | [Komunikacja równorzędna](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. Komunikacja równorzędna/peeringServices | Tak | Nie | [Usługa Peering Service](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/pojemności | Nie | Nie | [Pojemności](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/przestrzenie nazw | Tak | Nie | [Przekaźniki](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Nie | Nie | [Wyszukaj usługi](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/przestrzenie nazw | Tak | Nie | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nie | Tak | [Wystąpienia zarządzane SQL](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/serwery/bazy danych | Nie | Tak | [Bazy danych SQL](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/serwery/elasticPools | Nie | Tak | [Pule elastyczne SQL](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Tak | Nie | [Konta magazynu](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/usługi | Tak| Nie | [Usługi BLOB Services](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [usługi plików](./metrics-supported.md#microsoftstoragestorageaccountsfileservices), [usługi kolejkowania](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) i [usługi tabel](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft. StorageCache/pamięci podręczne | Tak | Nie | |
|Microsoft. StorageSync/storageSyncServices | Tak | Nie | [Usługi synchronizacji magazynu](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Tak | Nie | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. VMWareCloudSimple/virtualMachines | Tak | Nie | [Maszyny wirtualne platformy CloudSimple](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Tak | Nie | [App Service Environment pul z obsługą ról](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Tak | Nie | [App Service Environment pule procesów roboczych](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/dopuszczalna | Tak | Nie | [Plany App Service](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/witryny | Tak | Nie | [App Services](./metrics-supported.md#microsoftwebsites-excluding-functions) i [funkcje](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft. Web/Sites/miejsca | Tak | Nie | [App Service gniazd](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> nie są obsługiwane w przypadku metryk sieci maszyn wirtualnych (łącznie z siecią, łączny czas, przepływy przychodzące, przepływy wychodzące, maksymalna szybkość tworzenia przepływów wychodzących, maksymalna szybkość tworzenia przepływów ruchu wychodzącego) i metryki niestandardowe.

## <a name="payload-schema"></a>Schemat ładunku

> [!NOTE]
> Można również użyć [typowego schematu alertu](https://aka.ms/commonAlertSchemaDocs), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor, dla integracji elementów webhook. [Dowiedz się więcej na temat typowych definicji schematu alertów.](https://aka.ms/commonAlertSchemaDefinitions)


Operacja POST zawiera następujący ładunek i schemat JSON dla wszystkich niemal nowszych alertów metryk, gdy zostanie użyta odpowiednio skonfigurowana [Grupa akcji](./action-groups.md) :

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o nowych [alertach](./alerts-overview.md).
* Dowiedz się więcej [na temat alertów dziennika na platformie Azure](./alerts-unified-log.md).
* Dowiedz się więcej o [alertach na platformie Azure](./alerts-overview.md).

