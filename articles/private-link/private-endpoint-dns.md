---
title: Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure
description: Informacje o konfiguracji DNS prywatnego punktu końcowego platformy Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209030"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure


W przypadku nawiązywania połączenia z prywatnym zasobem linku przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia należy prawidłowo skonfigurować ustawienia DNS w celu rozpoznania przydzielony prywatny adres IP. Istniejące usługi platformy Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia przez publiczny punkt końcowy. Należy je zastąpić, aby nawiązać połączenie przy użyciu prywatnego punktu końcowego. 
 
Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełen zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatnych adresów IP przyznanych dla danego zasobu linku prywatnego. 
 
Aby skonfigurować ustawienia DNS dla prywatnych punktów końcowych, można użyć następujących opcji: 
- **Użyj pliku hosta (zalecane tylko do testowania)**. Aby zastąpić serwer DNS, można użyć pliku hosta na maszynie wirtualnej.  
- **Użyj prywatnej strefy DNS**. Za pomocą [prywatnych stref DNS](../dns/private-dns-privatednszone.md) można zastąpić rozpoznawanie nazw DNS dla danego prywatnego punktu końcowego. Prywatna strefa DNS może być połączona z siecią wirtualną w celu rozpoznania określonych domen.
- **Użyj niestandardowego serwera DNS**. Możesz użyć własnego serwera DNS do zastąpienia rozpoznawania nazw DNS dla danego zasobu linku prywatnego. Jeśli [serwer DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) jest hostowany w sieci wirtualnej, można utworzyć regułę przekazywania DNS w celu użycia prywatnej strefy DNS w celu uproszczenia konfiguracji wszystkich prywatnych zasobów łączy.
 
> [!IMPORTANT]
> Nie zaleca się przesłonięcia strefy, która aktywnie używa do rozpoznawania publicznych punktów końcowych. Połączenia z zasobami nie będą działać prawidłowo, bez przekazywania DNS do publicznej usługi DNS. Aby uniknąć problemów, Utwórz inną nazwę domeny lub postępuj według sugerowanej nazwy dla każdej usługi poniżej. 

## <a name="azure-services-dns-zone-configuration"></a>Konfiguracja strefy DNS usług platformy Azure
Usługi platformy Azure będą tworzyć rekordy DNS o nazwie kanonicznej (CNAME) na publicznym serwerze DNS, aby przekierować rozwiązanie do sugerowanych nazw domen prywatnych. Możliwe będzie przesłonięcie rozwiązania przy użyciu prywatnego adresu IP prywatnych punktów końcowych. 
 
Aplikacje nie muszą zmieniać adresu URL połączenia. Podczas próby rozwiązania przy użyciu publicznego serwera DNS serwer DNS zostanie teraz rozpoznany jako prywatne punkty końcowe. Ten proces nie ma wpływu na istniejące aplikacje. 

W przypadku usług platformy Azure Użyj zalecanych nazw stref, zgodnie z opisem w poniższej tabeli:

|Typ zasobu link prywatny   |Układ zasobów podrzędnych  |Nazwa strefy  |
|---------|---------|---------|
|Baza danych SQL (Microsoft. SQL/Server)    |  Program SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft. SQL/serwery)    |  Program SQL Server (sqlServer)        | privatelink.database.windows.net |
|Konto magazynu (Microsoft. Storage/storageAccounts)    |  Obiekt BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Konto magazynu (Microsoft. Storage/storageAccounts)    |    Tabela (tabela, table_secondary)      |   privatelink.table.core.windows.net       |
|Konto magazynu (Microsoft. Storage/storageAccounts)    |    Kolejka (Kolejka, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Konto magazynu (Microsoft. Storage/storageAccounts)   |    Plik (plik, file_secondary)      |    privatelink.file.core.windows.net      |
|Konto magazynu (Microsoft. Storage/storageAccounts)     |  Sieć Web (sieć Web, web_secondary)        |    privatelink.web.core.windows.net      |
|Gen2 systemu plików Data Lake (Microsoft. Storage/storageAccounts)  |  Gen2 systemu plików Data Lake (system plików DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Tabela|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL — pojedynczy serwer (Microsoft. DBforPostgreSQL/serwery)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft. DBforMySQL/serwery)|Mójserwersql|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (Microsoft. DBforMariaDB/serwery)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (magazyn/magazyny Microsoft. kluczy)|magazyn|privatelink.vaultcore.azure.net|
|Usługa Azure Kubernetes Service — Kubernetes API (Microsoft. ContainerService/managedClusters)    | managedCluster | {GUID}. privatelink. {Region}. azmk8s. IO|
|Azure Search (Microsoft. Search/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft. ContainerRegistry/rejestry) | registry | privatelink.azurecr.io |
|Konfiguracja aplikacji platformy Azure (Microsoft. Appconfiguration/configurationStores)| Magazyn konfiguracji | privatelink.azconfig.io|
|Azure Backup (Microsoft. RecoveryServices/magazyny)| magazyn |privatelink. {Region}. Backup. windowsazure. com|
|Centrum zdarzeń platformy Azure (Microsoft. EventHub/przestrzenie nazw)| namespace |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft. ServiceBus/przestrzenie nazw) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft. Relay/przestrzenie nazw) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft. EventGrid/tematy)     | temat | rozdziału. {Region}. privatelink. eventgrid. Azure. NET|
|Azure Event Grid (Microsoft. EventGrid/domen) | domena | domeny. {Region}. privatelink. eventgrid. Azure. NET |
|Azure webapps (Microsoft. Web/Sites)    | lokacja | privatelink.azurewebsites.net |
|Azure Machine Learning (Microsoft. MachineLearningServices/obszary robocze)    | obszar roboczy | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Scenariusze konfiguracji DNS

Nazwa FQDN usług rozpoznaje publiczny adres IP, należy zmienić konfigurację DNS w celu rozpoznania prywatnego adresu IP prywatnego punktu końcowego.

System DNS jest składnikiem krytycznym, aby aplikacja działała poprawnie przez rozwiązanie w sposób właściwy dla prywatnego adresu IP punktu końcowego.

W oparciu o Twoje preferencje następujące scenariusze są dostępne dla zintegrowanego rozpoznawania nazw DNS:

- [Virtual Network obciążeń bez niestandardowego serwera DNS](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtual Network obciążeń bez niestandardowego serwera DNS

Ta konfiguracja jest odpowiednia dla obciążeń sieci wirtualnej bez niestandardowego serwera DNS. W tym scenariuszu klient wysyła zapytania o adres IP prywatnego punktu końcowego do platformy Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS. Azure DNS będzie odpowiedzialna za rozpoznawanie nazw DNS prywatnych stref DNS.


 > [!NOTE]
> W tym scenariuszu jest używana zalecana Prywatna strefa DNS strefa usługi Azure SQL Database. W przypadku innych usług można dostosować model przy użyciu następującej [konfiguracji strefy DNS usług platformy Azure](#azure-services-dns-zone-configuration).

Aby można było skonfigurować poprawne, potrzebne są następujące zasoby:

- Sieć wirtualna klienta

- Prywatna strefa DNS strefy [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) z [rekordem typu A](../dns/dns-zones-records.md#record-types)

- Prywatne informacje o punkcie końcowym (nazwa rekordu FQDN i prywatny adres IP)

Na poniższym diagramie przedstawiono sekwencję rozpoznawania nazw DNS z obciążeń sieci wirtualnej przy użyciu prywatnej strefy DNS

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="jedna sieć wirtualna i usługa DNS udostępniona przez platformę Azure":::

Ten model można rozszerzyć na wiele równorzędnych sieci wirtualnych, które są skojarzone z tym samym prywatnym punktem końcowym. Można to zrobić przez [dodanie nowych linków sieci wirtualnej](../dns/private-dns-virtual-network-links.md) do prywatnej strefy DNS dla wszystkich równorzędnych sieci wirtualnych.

 > [!IMPORTANT]
>  W przypadku tej konfiguracji wymagana jest pojedyncza prywatna strefa DNS, tworząc wiele stref o takiej samej nazwie dla różnych sieci wirtualnych, aby scalić rekordy DNS, należy wykonać czynności ręczne

W tym scenariuszu istnieje topologia sieci w [centrum & szprychy](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) z sieciami szprych, które udostępniają wspólny prywatny punkt końcowy, a wszystkie sieci wirtualne szprych są połączone z tą samą prywatną strefą DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="centrum i szprycha z systemem DNS dostarczonym przez platformę Azure":::


## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o prywatnych punktach końcowych](private-endpoint-overview.md)
