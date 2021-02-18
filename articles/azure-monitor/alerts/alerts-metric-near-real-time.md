---
title: Obsługiwane zasoby dla alertów metryk w Azure Monitor
description: Informacje na temat metryk i dzienników pomocy technicznej na temat alertów dotyczących metryk w Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614640"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Obsługiwane zasoby dla alertów metryk w Azure Monitor

Azure Monitor teraz obsługuje [nowy typ alertu metryki](../platform/alerts-overview.md) , który ma znaczące korzyści w stosunku do starszych [klasycznych alertów dotyczących metryk](./alerts-classic.overview.md). Metryki są dostępne dla [dużej listy usług platformy Azure](../platform/metrics-supported.md). Nowsze alerty obsługują (rosnący) podzbiór typów zasobów. W tym artykule wymieniono ten podzbiór.

Możesz również używać nowszych alertów metryk dla popularnych danych dziennika przechowywanych w Log Analytics obszarze roboczym wyodrębnionym jako metryki. Aby uzyskać więcej informacji, zobacz [alerty metryk dla dzienników](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, interfejsu wiersza polecenia, obsługa REST
Obecnie można tworzyć nowsze alerty metryk tylko w [szablonach](./alerts-metric-create-templates.md)Azure Portal, [interfejsu API REST](/rest/api/monitor/metricalerts/)lub Menedżer zasobów. Wkrótce będzie dostępna obsługa konfigurowania nowszych alertów przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej.

## <a name="metrics-and-dimensions-supported"></a>Obsługiwane metryki i wymiary
Nowsze alerty metryk obsługują alerty dotyczące metryk, które korzystają z wymiarów. Wymiarów można użyć do filtrowania metryki na odpowiedni poziom. Wszystkie obsługiwane metryki wraz z odpowiednimi wymiarami mogą być zbadane i wizualizacje z [Azure Monitor Eksplorator metryk](../essentials/metrics-charts.md).

Oto pełna lista Azure Monitor źródeł metryk obsługiwanych przez nowsze alerty:

|Typ zasobu  |Obsługiwane wymiary |Alerty dotyczące obsługi zasobów| Dostępne metryki|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Tak | Nie | |
|Microsoft.ApiManagement/service | Tak | Nie | [API Management](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Tak | Nie | [Konfiguracja aplikacji](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/Sprężyna | Tak | Nie | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Tak| Nie | [Konta automatyzacji](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. Automatyczna synchronizacja/privateClouds | Nie | Nie | [Rozwiązanie Azure VMware](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Tak | Nie | [Konta usługi Batch](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Tak | Tak | [Azure Cache for Redis](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domainNames/gniazda/role | Nie | Nie | [Cloud Services klasyczny](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nie | Nie | [Virtual Machines klasyczny](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Tak | Nie | [Konta magazynu (klasyczne)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Tak | Nie | [Konta magazynu (klasyczne) — obiekty blob](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Tak | Nie | [Konta magazynu (klasyczne) — pliki](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Tak | Nie | [Konta magazynu (klasyczne) — kolejki](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Tak | Nie | [Konta magazynu (klasyczne) — tabele](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/konta | Tak | Nie | [Cognitive Services](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Tak | Tak<sup>1</sup> | [Virtual Machines](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Tak | Nie |[Zestawy skalowania maszyn wirtualnych](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Tak| Nie | [Grupy kontenerów](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/rejestry | Nie | Nie | [Rejestry kontenerów](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Tak | Nie | [Zarządzane klastry](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Tak | Tak | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactors| Tak| Nie | [Fabryki danych v1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/fabryki |Tak | Nie | [Fabryki danych v2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. dataudział/konta | Tak | Nie | [Udziały danych](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/serwery | Nie | Nie | [Baza danych dla MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/serwery | Nie | Nie |[Baza danych dla programu MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/serwery | Nie | Nie | [Baza danych dla PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nie | Nie | [DB for PostgreSQL v2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/flexibleServers | Tak | Nie | [DB for PostgreSQL (serwery elastyczne)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. Devices/IotHubs | Tak | Nie |[IoT Hub](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Tak | Nie | [Usługi Device Provisioning](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Tak | Nie | [Digital Twins](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Tak | Nie | [Cosmos DB](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domeny | Tak | Nie | [Domeny usługi Event Grid](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Tak | Nie | [Tematy dotyczące systemu Event Grid](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/tematy |Tak | Nie | [Tematy usługi Event Grid](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/klastry |Tak| Nie | [Klastry Event Hubs](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/przestrzenie nazw |Tak| Nie | [Event Hubs](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/Klastry | Tak | Nie | [Klastry HDInsight](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/składniki | Tak | Nie | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft./magazyny kluczy | Tak |Tak |[Magazyny](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/klastry | Tak |Nie |[Klastry Eksplorator danych](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Tak | Nie |[Środowiska usługi integracji](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/przepływy pracy | Nie | Nie |[Logic Apps](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/obszary robocze | Tak | Nie | [Usługa Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/konta | Tak | Nie | [Mapuje konta](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | Nie | Nie | [Media Services](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/streamingEndpoints | Tak | Nie | [Media Services punkty końcowe przesyłania strumieniowego](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Tak | Tak | [Pule pojemności usługi Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | Tak | Tak | [Woluminy usługi Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Tak | Nie | [Bramy aplikacji](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Tak | Nie | [Zapory](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nie | Nie | [Strefy DNS](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Tak | Nie |[Obwody usługi ExpressRoute](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Tak | Nie |[Usługa ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (tylko w przypadku standardowych jednostek SKU)| Tak| Nie | [Moduły równoważenia obciążenia](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nie | Nie | [Bramy translatora adresów sieciowych](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Nie | Nie | [Prywatne punkty końcowe](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Nie | Nie | [Usługi linków prywatnych](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/adresów publicipaddress | Nie | Nie | [Publiczne adresy IP](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Tak | Nie | [Profile usługi Traffic Manager](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/obszary robocze| Tak | Nie | [Obszary robocze usługi Log Analytics](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. Komunikacja równorzędna/Komunikacja równorzędna | Tak | Nie | [Komunikacja równorzędna](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. Komunikacja równorzędna/peeringServices | Tak | Nie | [Usługa Peering Service](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/pojemności | Nie | Nie | [Pojemności](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/przestrzenie nazw | Tak | Nie | [Przekaźniki](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Nie | Nie | [Wyszukaj usługi](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/przestrzenie nazw | Tak | Nie | [Service Bus](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nie | Tak | [Wystąpienia zarządzane SQL](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/serwery/bazy danych | Nie | Tak | [Bazy danych SQL](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/serwery/elasticPools | Nie | Tak | [Pule elastyczne SQL](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Tak | Nie | [Konta magazynu](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Tak| Nie | [Konta magazynu — obiekty blob](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Tak| Nie | [Konta magazynu — pliki](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Tak| Nie | [Konta magazynu — kolejki](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Tak| Nie | [Konta magazynu — tabele](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/pamięci podręczne | Tak | Nie | [Pamięci podręczne HPC](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Tak | Nie | [Usługi synchronizacji magazynu](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Tak | Nie | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. Synapse/obszary robocze | Tak | Nie | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/Workspaces/bigDataPools | Tak | Nie | [Pule Apache Spark analizy Synapse](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/obszary robocze/xmlpools | Tak | Nie | [Pule SQL Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Tak | Nie | [Maszyny wirtualne platformy CloudSimple](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Tak | Nie | [App Service Environment pul z obsługą ról](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Tak | Nie | [App Service Environment pule procesów roboczych](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/dopuszczalna | Tak | Nie | [Plany App Service](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/witryny | Tak | Nie | [App Services i funkcje](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/miejsca | Tak | Nie | [App Service gniazd](../platform/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> nie są obsługiwane w przypadku metryk sieci maszyn wirtualnych (łącznie z siecią, łączny czas, przepływy przychodzące, przepływy wychodzące, maksymalna szybkość tworzenia przepływów wychodzących, maksymalna szybkość tworzenia przepływów ruchu wychodzącego) i metryki niestandardowe.

## <a name="payload-schema"></a>Schemat ładunku

> [!NOTE]
> Można również użyć [typowego schematu alertu](./alerts-common-schema.md), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor, dla integracji elementów webhook. [Dowiedz się więcej na temat typowych definicji schematu alertów.](./alerts-common-schema-definitions.md)


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

* Dowiedz się więcej o nowych [alertach](../platform/alerts-overview.md).
* Dowiedz się więcej [na temat alertów dziennika na platformie Azure](./alerts-unified-log.md).
* Dowiedz się więcej o [alertach na platformie Azure](../platform/alerts-overview.md).