---
title: Obsługiwane zasoby dla alertów metryk w Azure Monitor
description: Informacje na temat metryk i dzienników pomocy technicznej na temat alertów dotyczących metryk w Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: efc1438fdc539af278ebff1f292c5fa0a91b7b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016104"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Obsługiwane zasoby dla alertów metryk w Azure Monitor

Azure Monitor teraz obsługuje [nowy typ alertu metryki](./alerts-overview.md) , który ma znaczące korzyści w stosunku do starszych [klasycznych alertów dotyczących metryk](./alerts-classic.overview.md). Metryki są dostępne dla [dużej listy usług platformy Azure](../essentials/metrics-supported.md). Nowsze alerty obsługują (rosnący) podzbiór typów zasobów. W tym artykule wymieniono ten podzbiór.

Możesz również używać nowszych alertów metryk dla popularnych danych dziennika przechowywanych w Log Analytics obszarze roboczym wyodrębnionym jako metryki. Aby uzyskać więcej informacji, zobacz [alerty metryk dla dzienników](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, interfejsu wiersza polecenia, obsługa REST
Obecnie można tworzyć nowsze alerty metryk tylko w [szablonach](./alerts-metric-create-templates.md)Azure Portal, [interfejsu API REST](/rest/api/monitor/metricalerts/)lub Menedżer zasobów. Wkrótce będzie dostępna obsługa konfigurowania nowszych alertów przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej.

## <a name="metrics-and-dimensions-supported"></a>Obsługiwane metryki i wymiary
Nowsze alerty metryk obsługują alerty dotyczące metryk, które korzystają z wymiarów. Wymiarów można użyć do filtrowania metryki na odpowiedni poziom. Wszystkie obsługiwane metryki wraz z odpowiednimi wymiarami mogą być zbadane i wizualizacje z [Azure Monitor Eksplorator metryk](../essentials/metrics-charts.md).

Oto pełna lista Azure Monitor źródeł metryk obsługiwanych przez nowsze alerty:

|Typ zasobu  |Obsługiwane wymiary |Alerty dotyczące obsługi zasobów| Dostępne metryki|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Tak | Nie | |
|Microsoft.ApiManagement/service | Tak | Nie | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Tak | Nie | [Konfiguracja aplikacji](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/Sprężyna | Tak | Nie | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Tak| Nie | [Konta automatyzacji](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. Automatyczna synchronizacja/privateClouds | Nie | Nie | [Rozwiązanie Azure VMware](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Tak | Nie | [Konta usługi Batch](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. BotService/botServices | Tak | Nie | [Usługi bot Services](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft. cache/Redis | Tak | Tak | [Azure Cache for Redis](../essentials/metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domainNames/gniazda/role | Nie | Nie | [Cloud Services klasyczny](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Nie | Nie | [Virtual Machines klasyczny](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Tak | Nie | [Konta magazynu (klasyczne)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Tak | Nie | [Konta magazynu (klasyczne) — obiekty blob](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Tak | Nie | [Konta magazynu (klasyczne) — pliki](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Tak | Nie | [Konta magazynu (klasyczne) — kolejki](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Tak | Nie | [Konta magazynu (klasyczne) — tabele](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. CognitiveServices/konta | Tak | Nie | [Cognitive Services](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft. COMPUTE/cloudServices | Tak | Nie |  [Usługi w chmurze](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft. COMPUTE/cloudServices/role | Tak | Nie |  [Role usługi w chmurze](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Tak | Tak<sup>1</sup> | [Virtual Machines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Tak | Nie |[Zestawy skalowania maszyn wirtualnych](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Tak| Nie | [Grupy kontenerów](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/rejestry | Nie | Nie | [Rejestry kontenerów](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Tak | Nie | [Zarządzane klastry](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Tak | Tak | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/datafactors| Tak| Nie | [Fabryki danych v1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/fabryki |Tak | Nie | [Fabryki danych v2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. dataudział/konta | Tak | Nie | [Udziały danych](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/serwery | Nie | Nie | [Baza danych dla MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/serwery | Nie | Nie |[Baza danych dla programu MySQL](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Tak | Nie | [DB for PostgreSQL (serwery elastyczne)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. DBforPostgreSQL/serverGroupsv2 | Tak | Nie | DB for PostgreSQL (skalowanie) |
|Microsoft. DBforPostgreSQL/serwery | Nie | Nie | [Baza danych dla PostgreSQL](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Nie | Nie | [DB for PostgreSQL v2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. Devices/IotHubs | Tak | Nie |[IoT Hub](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Tak | Nie | [Usługi Device Provisioning](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Tak | Nie | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Tak | Nie | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domeny | Tak | Nie | [Domeny usługi Event Grid](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Tak | Nie | [Tematy dotyczące systemu Event Grid](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/tematy |Tak | Nie | [Tematy usługi Event Grid](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/klastry |Tak| Nie | [Klastry Event Hubs](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/przestrzenie nazw |Tak| Nie | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/Klastry | Tak | Nie | [Klastry HDInsight](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/składniki | Tak | Nie | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft./magazyny kluczy | Tak |Tak |[Magazyny](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/klastry | Tak |Nie |[Klastry Eksplorator danych](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Tak | Nie |[Środowiska usługi integracji](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/przepływy pracy | Nie | Nie |[Logic Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/obszary robocze | Tak | Nie | [Usługa Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. MachineLearningServices/Workspaces/onlineEndpoints | Tak | Nie | Machine Learning — punkty końcowe |
|Microsoft. MachineLearningServices/Workspaces/onlineEndpoints/Deployments | Tak | Nie | Wdrożenia punktów końcowych Machine Learning |
|Microsoft. Maps/konta | Tak | Nie | [Mapuje konta](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | Nie | Nie | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/streamingEndpoints | Tak | Nie | [Media Services punkty końcowe przesyłania strumieniowego](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Tak | Tak | [Pule pojemności usługi Azure NetApp](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | Tak | Tak | [Woluminy usługi Azure NetApp](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Tak | Nie | [Bramy aplikacji](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Tak | Nie | [Zapory](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Nie | Nie | [Strefy DNS](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Tak | Nie |[Obwody usługi ExpressRoute](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Tak | Nie |[Usługa ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (tylko w przypadku standardowych jednostek SKU)| Tak| Nie | [Moduły równoważenia obciążenia](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Nie | Nie | [Bramy translatora adresów sieciowych](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Nie | Nie | [Prywatne punkty końcowe](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Nie | Nie | [Usługi linków prywatnych](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/adresów publicipaddress | Nie | Nie | [Publiczne adresy IP](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Tak | Nie | [Profile usługi Traffic Manager](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/obszary robocze| Tak | Nie | [Obszary robocze usługi Log Analytics](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. Komunikacja równorzędna/Komunikacja równorzędna | Tak | Nie | [Komunikacja równorzędna](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. Komunikacja równorzędna/peeringServices | Tak | Nie | [Usługa Peering Service](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/pojemności | Nie | Nie | [Pojemności](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/przestrzenie nazw | Tak | Nie | [Przekaźniki](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Nie | Nie | [Wyszukaj usługi](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/przestrzenie nazw | Tak | Nie | [Service Bus](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Nie | Tak | [Wystąpienia zarządzane SQL](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/serwery/bazy danych | Nie | Tak | [Bazy danych SQL](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/serwery/elasticPools | Nie | Tak | [Pule elastyczne SQL](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Tak | Nie | [Konta magazynu](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Tak| Nie | [Konta magazynu — obiekty blob](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Tak| Nie | [Konta magazynu — pliki](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Tak| Nie | [Konta magazynu — kolejki](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Tak| Nie | [Konta magazynu — tabele](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/pamięci podręczne | Tak | Nie | [Pamięci podręczne HPC](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Tak | Nie | [Usługi synchronizacji magazynu](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Tak | Nie | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. Synapse/obszary robocze | Tak | Nie | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. Synapse/Workspaces/bigDataPools | Tak | Nie | [Pule Apache Spark analizy Synapse](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. Synapse/obszary robocze/xmlpools | Tak | Nie | [Pule SQL Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Tak | Nie | [Maszyny wirtualne platformy CloudSimple](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Tak | Nie | [App Service Environment pul z obsługą ról](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Tak | Nie | [App Service Environment pule procesów roboczych](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/dopuszczalna | Tak | Nie | [Plany App Service](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/witryny | Tak | Nie | [App Services i funkcje](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/miejsca | Tak | Nie | [App Service gniazd](../essentials/metrics-supported.md#microsoftwebsitesslots)|

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

* Dowiedz się więcej o nowych [alertach](./alerts-overview.md).
* Dowiedz się więcej [na temat alertów dziennika na platformie Azure](./alerts-unified-log.md).
* Dowiedz się więcej o [alertach na platformie Azure](./alerts-overview.md).
