---
title: Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure
description: Informacje o konfiguracji DNS prywatnego punktu końcowego platformy Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: e71325246b69f501ec8af91c59cb4f042180542c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999665"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure


W przypadku nawiązywania połączenia z prywatnym zasobem linku przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia należy prawidłowo skonfigurować ustawienia DNS w celu rozpoznania przydzielony prywatny adres IP. Istniejące usługi Microsoft Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia przez publiczny punkt końcowy. Ta konfiguracja musi zostać zastąpiona, aby można było nawiązać połączenie przy użyciu prywatnego punktu końcowego. 
 
Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełen zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatnych adresów IP przyznanych dla określonego zasobu linku prywatnego. 
 
Aby skonfigurować ustawienia DNS dla prywatnych punktów końcowych, można użyć następujących opcji: 
- **Użyj pliku hosta (zalecane tylko do testowania)**. Aby zastąpić serwer DNS, można użyć pliku hosta na maszynie wirtualnej.  
- **Użyj prywatnej strefy DNS**. Za pomocą [prywatnych stref DNS](../dns/private-dns-privatednszone.md) można zastąpić rozpoznawanie nazw DNS dla określonego prywatnego punktu końcowego. Prywatna strefa DNS może być połączona z siecią wirtualną w celu rozpoznania określonych domen.
- **Użyj usługi przesyłania dalej DNS (opcjonalnie)**. Możesz użyć usługi przesyłania dalej DNS, aby zastąpić rozpoznawanie nazw DNS dla określonego zasobu linku prywatnego. Jeśli [serwer DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) jest hostowany w sieci wirtualnej, można utworzyć regułę przekazywania DNS w celu użycia prywatnej strefy DNS w celu uproszczenia konfiguracji wszystkich prywatnych zasobów łączy.
 
> [!IMPORTANT]
> Nie zaleca się przesłonięcia strefy, która aktywnie używa do rozpoznawania publicznych punktów końcowych. Połączenia z zasobami nie będą działać prawidłowo, bez przekazywania DNS do publicznej usługi DNS. Aby uniknąć problemów, Utwórz inną nazwę domeny lub postępuj według sugerowanej nazwy dla każdej usługi poniżej. 

## <a name="azure-services-dns-zone-configuration"></a>Konfiguracja strefy DNS usług platformy Azure
Usługi platformy Azure będą tworzyć rekordy DNS o nazwie kanonicznej (CNAME) w publicznej usłudze DNS, aby przekierować rozwiązanie do sugerowanej nazwy domeny prywatnej. Można zastąpić rozwiązanie przy użyciu prywatnego adresu IP prywatnych punktów końcowych. 
 
Aplikacje nie muszą zmieniać adresu URL połączenia. Podczas próby rozwiązania przy użyciu publicznej usługi DNS serwer DNS zostanie teraz rozpoznany jako prywatne punkty końcowe. Ten proces nie ma wpływu na istniejące aplikacje. 

> [!IMPORTANT]
> Sieci prywatne używające już prywatnej strefy DNS danego typu mogą łączyć się tylko z zasobami publicznymi, jeśli nie mają żadnych prywatnych połączeń punktów końcowych. w przeciwnym razie odpowiednia Konfiguracja DNS jest wymagana w prywatnej strefie DNS, aby zakończyć sekwencję rozpoznawania nazw DNS. 

W przypadku usług platformy Azure Użyj zalecanych nazw stref, zgodnie z opisem w poniższej tabeli:

| Prywatny typ zasobu linku/zasób |Nazwa strefy Prywatna strefa DNS | Publiczne usługi przesyłania dalej strefy DNS |
|---|---|---|---|
| Azure Automation/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft. SQL/Server)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft. SQL/Server)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Konto magazynu (Microsoft. Storage/storageAccounts)/obiekt BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Konto magazynu (Microsoft. Storage/storageAccounts)/tabela (tabela, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Konto magazynu (Microsoft. Storage/storageAccounts)/Queue (Queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Konto magazynu (Microsoft. Storage/storageAccounts)/plik (plik, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Konto magazynu (Microsoft. Storage/storageAccounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake Gen2 systemu plików (Microsoft. Storage/storageAccounts)/Data Lake system plików Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tabela | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL — pojedynczy serwer (Microsoft. DBforPostgreSQL/serwery)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/Servers)/sqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/serwery)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (magazyn/magazyny firmy Microsoft)/magazyn | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Usługa Azure Kubernetes Service — interfejs API Kubernetes (Microsoft. ContainerService/managedClusters)/Zarządzanie | privatelink. {Region}. azmk8s. IO | {Region}. azmk8s. IO |
| Azure Search (Microsoft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/rejestry)/rejestr | privatelink.azurecr.io | azurecr.io |
| Konfiguracja aplikacji platformy Azure (Microsoft. AppConfiguration/configurationStores)/magazyn konfiguracji | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. RecoveryServices/magazyny)/magazyn | privatelink. {Region}. Backup. windowsazure. com | {Region}. Backup. windowsazure. com |
| Event Hubs platformy Azure (Microsoft. EventHub/przestrzenie nazw)/przestrzeń nazw | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/przestrzenie nazw)/przestrzeń nazw | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Azure Relay (Microsoft. Relay/przestrzenie nazw)/przestrzeń nazw | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/tematy)/temat | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/domen)/domena | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/Sites)/Lokacje | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/Workspaces)/obszar roboczy | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| Sygnalizujący (Microsoft. SignalRService/Signaler)/sygnalizujący | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. CognitiveServices/accounts)/Account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |

 
## <a name="dns-configuration-scenarios"></a>Scenariusze konfiguracji DNS

Nazwa FQDN usług jest automatycznie rozpoznawana jako publiczny adres IP. Aby rozwiązać ten prywatny adres IP prywatnego punktu końcowego, należy odpowiednio zmienić konfigurację DNS.

System DNS jest krytycznym składnikiem, aby aplikacja działała poprawnie przez pomyślne rozpoznanie prywatnego adresu IP punktu końcowego.

W oparciu o Twoje preferencje w ramach rozpoznawania nazw DNS są dostępne następujące scenariusze:

- [Obciążenia sieci wirtualnej bez niestandardowego serwera DNS](#virtual-network-workloads-without-custom-dns-server)
- [Obciążenia lokalne przy użyciu usługi przesyłania dalej DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Obciążenia sieci wirtualnej i lokalne przy użyciu usługi przesyłania dalej DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Obciążenia sieci wirtualnej bez niestandardowego serwera DNS

Ta konfiguracja jest odpowiednia dla obciążeń sieci wirtualnej bez niestandardowego serwera DNS. W tym scenariuszu klient wysyła zapytanie o adres IP prywatnego punktu końcowego do [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)usługi DNS platformy Azure. Azure DNS będzie odpowiedzialna za rozpoznawanie nazw DNS prywatnych stref DNS.

> [!NOTE]
> W tym scenariuszu jest wykorzystywana prywatna strefa DNS Azure SQL Database. W przypadku innych usług można dostosować model, korzystając z następującego odwołania: [Konfiguracja strefy DNS usług platformy Azure](#azure-services-dns-zone-configuration).

Aby prawidłowo skonfigurować program, potrzebne są następujące zasoby:

- Sieć wirtualna klienta

- Prywatna strefa DNS strefy [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  z [rekordem typu A](../dns/dns-zones-records.md#record-types)

- Prywatne informacje o punkcie końcowym (nazwa rekordu FQDN i prywatny adres IP)

Poniższy zrzut ekranu przedstawia sekwencję rozpoznawania nazw DNS z obciążeń sieci wirtualnej przy użyciu prywatnej strefy DNS:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Pojedyncza Sieć wirtualna i usługa DNS udostępniona przez platformę Azure":::

Ten model można rozszerzyć na wiele równorzędnych sieci wirtualnych, które są skojarzone z tym samym prywatnym punktem końcowym. Można to zrobić przez [dodanie nowych linków sieci wirtualnej](../dns/private-dns-virtual-network-links.md) do prywatnej strefy DNS dla wszystkich równorzędnych sieci wirtualnych.

> [!IMPORTANT]
> Ta konfiguracja wymaga pojedynczej prywatnej strefy DNS. Utworzenie wielu stref o tej samej nazwie dla różnych sieci wirtualnych wymaga ręcznej operacji do scalenia rekordów DNS.

> [!IMPORTANT]
> Jeśli używasz prywatnego punktu końcowego w modelu gwiazdy i gwiazdy z innej subskrypcji, Użyj tej samej prywatnej strefy DNS w centrum.

W tym scenariuszu istnieje topologia sieci typu [Hub i szprych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) z sieciami szprych, które udostępniają wspólny prywatny punkt końcowy, a wszystkie sieci wirtualne szprych są połączone z tą samą prywatną strefą DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Pojedyncza Sieć wirtualna i usługa DNS udostępniona przez platformę Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Obciążenia lokalne przy użyciu usługi przesyłania dalej DNS

W przypadku obciążeń lokalnych w celu rozpoznania nazwy FQDN prywatnego punktu końcowego w prywatnym adresie IP należy użyć usługi przesyłania dalej DNS, aby wdrożyć rozwiązanie [publicznej strefy DNS](#azure-services-dns-zone-configuration) usługi platformy Azure na platformie Azure.

Następujący scenariusz jest odpowiedni dla sieci lokalnej, która ma usługę przesyłania dalej systemu DNS na platformie Azure, która z kolei jest odpowiedzialna za rozpoznawanie wszystkich zapytań DNS za pośrednictwem usługi przesyłania dalej na poziomie serwera do [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS podanej przez platformę Azure. 

> [!NOTE]
> W tym scenariuszu jest wykorzystywana prywatna strefa DNS Azure SQL Database.W przypadku innych usług można dostosować model, korzystając z następującego odwołania: [Konfiguracja strefy DNS usług platformy Azure](#azure-services-dns-zone-configuration).

Aby prawidłowo skonfigurować program, potrzebne są następujące zasoby:

- Sieć lokalna
- Sieć wirtualna [podłączona do lokalnego](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Usługa przesyłania dalej DNS wdrożona na platformie Azure 
- Prywatna strefa DNS stref [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)   z [rekordem typu A](../dns/dns-zones-records.md#record-types)
- Prywatne informacje o punkcie końcowym (nazwa rekordu FQDN i prywatny adres IP)

Na poniższym diagramie przedstawiono sekwencję rozpoznawania nazw DNS z sieci lokalnej, która używa usługi przesyłania dalej DNS wdrożonej na platformie Azure, w której rozwiązanie jest nawiązywane przez prywatną strefę DNS [połączoną z siecią wirtualną](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Pojedyncza Sieć wirtualna i usługa DNS udostępniona przez platformę Azure":::

Tę konfigurację można rozszerzyć dla sieci lokalnej, w której znajduje się już rozwiązanie DNS. 
Lokalne rozwiązanie DNS musi być skonfigurowane do przekazywania ruchu DNS do Azure DNS za pośrednictwem [usługi przesyłania dalej warunkowego](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) , która odwołuje się do usługi przesyłania dalej DNS wdrożonej na platformie Azure.

> [!NOTE]
> W tym scenariuszu jest wykorzystywana prywatna strefa DNS Azure SQL Database. W przypadku innych usług można dostosować model, korzystając z następującego odwołania: [Konfiguracja strefy DNS usług platformy Azure](#azure-services-dns-zone-configuration)

Aby prawidłowo skonfigurować program, potrzebne są następujące zasoby:

- Sieć lokalna z niestandardowym rozwiązaniem DNS 
- Sieć wirtualna [podłączona do lokalnego](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Usługa przesyłania dalej DNS wdrożona na platformie Azure
- Prywatna strefa DNS stref [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)    z [rekordem typu A](../dns/dns-zones-records.md#record-types)
- Prywatne informacje o punkcie końcowym (nazwa rekordu FQDN i prywatny adres IP)

Na poniższym diagramie przedstawiono sekwencję rozpoznawania nazw DNS z sieci lokalnej, która warunkowo przekazuje ruch DNS do platformy Azure, gdzie rozwiązanie jest nawiązywane przez prywatną strefę DNS [połączoną z siecią wirtualną](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Warunkowe przekazywanie należy wykonać do zalecanej [publicznej usługi przesyłania dalej strefy DNS](#azure-services-dns-zone-configuration).Na przykład:  `database.windows.net`   zamiast **privatelink**. Database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Pojedyncza Sieć wirtualna i usługa DNS udostępniona przez platformę Azure":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Obciążenia sieci wirtualnej i lokalne przy użyciu usługi przesyłania dalej DNS

Aby zapoznać się z ogólnym podejściem do obciążeń, które wymagają dostępu do prywatnego punktu końcowego z sieci wirtualnych i lokalnych, należy użyć udostępnionej usługi przesyłania dalej DNS w celu zapewnienia rozdzielczości [publicznej strefy DNS](#azure-services-dns-zone-configuration) usługi platformy Azure wdrożonej na platformie Azure.

Następujący scenariusz jest odpowiedni dla sieci lokalnej, która ma usługę przesyłania dalej DNS na platformie Azure, i sieci wirtualnych, które wymagają dostępu do prywatnego punktu końcowego znajdującego się w udostępnionej sieci centrów.  

Usługa przesyłania dalej DNS jest odpowiedzialna za rozpoznawanie wszystkich zapytań DNS za pośrednictwem usług przesyłania dalej na poziomie serwera do usługi DNS udostępnionej przez platformę Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Ta konfiguracja wymaga pojedynczej prywatnej strefy DNS. Wszystkie połączenia klienckie nawiązane z lokalnych i [równorzędnych sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) również muszą używać tej samej prywatnej strefy DNS.

> [!NOTE]
> W tym scenariuszu jest wykorzystywana prywatna strefa DNS Azure SQL Database. W przypadku innych usług można dostosować model, korzystając z następującego odwołania: [Konfiguracja strefy DNS usług platformy Azure](#azure-services-dns-zone-configuration).

Aby prawidłowo skonfigurować program, potrzebne są następujące zasoby:

- Sieć lokalna
- Sieć wirtualna [podłączona do lokalnego](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- [Równorzędna Sieć wirtualna](../virtual-network/virtual-network-peering-overview.md) 
- Usługa przesyłania dalej DNS wdrożona na platformie Azure
- Prywatna strefa DNS stref [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)    z [rekordem typu A](../dns/dns-zones-records.md#record-types)
- Prywatne informacje o punkcie końcowym (nazwa rekordu FQDN i prywatny adres IP)

Na poniższym diagramie przedstawiono sekwencję rozpoznawania nazw DNS z sieci lokalnej i wirtualnej, która korzysta z usługi przesyłania dalej DNS wdrożonej na platformie Azure, gdzie rozwiązanie jest nawiązywane przez prywatną strefę DNS [połączoną z siecią wirtualną](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Pojedyncza Sieć wirtualna i usługa DNS udostępniona przez platformę Azure":::

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o prywatnych punktach końcowych](private-endpoint-overview.md)
