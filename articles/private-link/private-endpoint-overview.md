---
title: Co to jest prywatny punkt końcowy platformy Azure?
description: Dowiedz się więcej o prywatnym punkcie końcowym platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: f18d9cb2a1bf76986a5c77477085f2f12ab728ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771807"
---
# <a name="what-is-azure-private-endpoint"></a>Co to jest prywatny punkt końcowy platformy Azure?

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej. Usługa może być usługą platformy Azure, taką jak Azure Storage, Azure Cosmos DB, SQL itp. lub własną [usługą Private Link Service.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Właściwości prywatnego punktu końcowego 
 Prywatny punkt końcowy określa następujące właściwości: 


|Właściwość  |Opis |
|---------|---------|
|Nazwa    |    Unikatowa nazwa w grupie zasobów.      |
|Podsieć    |  Podsieć do wdrażania i przydzielania prywatnych adresów IP z sieci wirtualnej. Aby uzyskać informacje o wymaganiach dotyczących podsieci, zobacz sekcję Ograniczenia w tym artykule.         |
|Private Link zasobów    |   Zasób linku prywatnego do łączenia się przy użyciu identyfikatora zasobu lub aliasu z listy dostępnych typów. Dla całego ruchu wysyłanego do tego zasobu zostanie wygenerowany unikatowy identyfikator sieci.       |
|Docelowy podźródło   |      Podźródło do nawiązania połączenia. Każdy typ zasobu linku prywatnego ma różne opcje do wyboru w zależności od preferencji.    |
|Metoda zatwierdzania połączenia    |  Automatyczne lub ręczne. Na podstawie uprawnień kontroli dostępu opartej na rolach (RBAC) platformy Azure prywatny punkt końcowy może zostać zatwierdzony automatycznie. Jeśli spróbujesz nawiązać połączenie z zasobem łącza prywatnego bez kontroli RBAC platformy Azure, użyj metody ręcznej, aby zezwolić właścicielowi zasobu na zatwierdzenie połączenia.        |
|Komunikat żądania     |  Możesz określić komunikat dla żądanych połączeń do ręcznego zatwierdzenia. Ten komunikat może służyć do identyfikowania określonego żądania.        |
|Stan połączenia   |   Właściwość tylko do odczytu określająca, czy prywatny punkt końcowy jest aktywny. Do wysyłania ruchu można używać tylko prywatnych punktów końcowych w stanie zatwierdzonym. Dostępne dodatkowe stany: <br>-**Zatwierdzone:** połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia.</br><br>-**Oczekujące:** połączenie zostało utworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu łącza prywatnego.</br><br>-**Odrzucone:** połączenie zostało odrzucone przez właściciela zasobu łącza prywatnego.</br><br>-**Odłączony:** połączenie zostało usunięte przez właściciela zasobu linku prywatnego. Prywatny punkt końcowy staje się wartościowy i należy go usunąć w celu oczyszczenia. </br>|

Oto kilka kluczowych szczegółów dotyczących prywatnych punktów końcowych: 
- Prywatny punkt końcowy umożliwia łączność między konsumentami z tej samej sieci wirtualnej, regionalnych równorzędnych sieci wirtualnych, globalnie równorzędnych sieci wirtualnych i lokalnych przy użyciu sieci [VPN](https://azure.microsoft.com/services/vpn-gateway/) lub [usługi Express Route](https://azure.microsoft.com/services/expressroute/) i usług obsługiwanych przez Private Link.
 
- Połączenia sieciowe mogą być inicjowane tylko przez klientów łączących się z prywatnym punktem końcowym. Dostawcy usług nie mają żadnej konfiguracji routingu do inicjowania połączeń z użytkownikami usług. Połączenia można nawiązywać tylko w jednym kierunku.

- Podczas tworzenia prywatnego punktu końcowego dla cyklu życia zasobu jest również tworzony interfejs sieciowy tylko do odczytu. Interfejs jest przypisywany dynamicznie prywatnych adresów IP z podsieci, która jest mapowana na zasób łącza prywatnego. Wartość prywatnego adresu IP pozostaje niezmieniona przez cały cykl życia prywatnego punktu końcowego.
 
- Prywatny punkt końcowy musi być wdrożony w tym samym regionie i subskrypcji co sieć wirtualna. 
 
- Zasób łącza prywatnego można wdrożyć w innym regionie niż sieć wirtualna i prywatny punkt końcowy.
 
- Wiele prywatnych punktów końcowych można utworzyć przy użyciu tego samego zasobu łącza prywatnego. W przypadku pojedynczej sieci korzystającej ze wspólnej konfiguracji serwera DNS zaleca się użycie pojedynczego prywatnego punktu końcowego dla danego zasobu łącza prywatnego, aby uniknąć zduplikowanych wpisów lub konfliktów w rozpoznawania nazw DNS. 
 
- Wiele prywatnych punktów końcowych można utworzyć w tej samej lub różnych podsieciach w tej samej sieci wirtualnej. Istnieją limity liczby prywatnych punktów końcowych, które można utworzyć w ramach subskrypcji. Aby uzyskać szczegółowe informacje, zobacz [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)

- Subskrypcja z zasobu łącza prywatnego musi być również zarejestrowana u dostawcy zasobów Micosoft.Network. Aby uzyskać szczegółowe informacje, zobacz [Azure Resource Providers (Dostawcy zasobów platformy Azure).](../azure-resource-manager/management/resource-providers-and-types.md)

 
## <a name="private-link-resource"></a>Zasób łącza prywatnego 
Zasób łącza prywatnego to miejsce docelowe danego prywatnego punktu końcowego. Poniżej znajduje się lista dostępnych typów zasobów łącza prywatnego: 
 
|Nazwa zasobu linku prywatnego  |Typ zasobu   |Podźródła  |
|---------|---------|---------|
|**Private Link Service** (Twoja własna usługa)   |  Microsoft.Network/privateLinkServices       | puste |
|**Azure Automation** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Obiekt blob (blob, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Kolejka (kolejka, queue_secondary)<BR> Plik (plik, file_secondary)<BR> Sieć Web (sieć Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Obiekt blob (blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL — pojedynczy serwer** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT Hub** | Microsoft.Devices/IotHubs    | iotHub |
|**Usługa Azure Key Vault** | Microsoft.KeyVault/vaults    | magazyn |
|**Azure Kubernetes Service — interfejs API platformy Kubernetes** | Microsoft.ContainerService/managedClusters    | zarządzanie |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries    | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores    | configurationStores (magazyny konfiguracji) |
|**Azure Backup** | Microsoft.RecoveryServices/vaults    | magazyn |
|**Centrum zdarzeń Azure** | Microsoft.EventHub/namespaces    | namespace |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | namespace |
|**Azure Relay** | Microsoft.Relay/namespaces | namespace |
|**Azure Event Grid** | Microsoft.EventGrid/topics    | temat |
|**Azure Event Grid** | Microsoft.EventGrid/domains    | domena |
|**Azure App Service** | Microsoft.Web/sites    | lokacje |
|**Azure App Service Azure App Service danych** | Microsoft.Web/sites    | lokacje`<slot name>` |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | amlworkspace |
|**SignalR** | Microsoft.SignalRService/SignalR    | Signalr |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft.CognitiveServices/accounts    | account |
|**Azure File Sync** | Microsoft.StorageSync/storageSyncServices    | Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Zabezpieczenia sieci prywatnych punktów końcowych 
W przypadku korzystania z prywatnych punktów końcowych dla usług platformy Azure ruch jest zabezpieczony do określonego zasobu łącza prywatnego. Platforma wykonuje kontrolę dostępu w celu weryfikowania połączeń sieciowych, które docierają tylko do określonego zasobu łącza prywatnego. Aby uzyskać dostęp do dodatkowych zasobów w ramach tej samej usługi platformy Azure, wymagane są dodatkowe prywatne punkty końcowe. 
 
Możesz całkowicie zablokować dostęp obciążeń do publicznych punktów końcowych w celu nawiązania połączenia z obsługiwaną usługą platformy Azure. Ta kontrolka zapewnia dodatkową warstwę zabezpieczeń sieci dla zasobów, zapewniając wbudowaną ochronę przed eksfiltracją, która uniemożliwia dostęp do innych zasobów hostowanych w tej samej usłudze platformy Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Dostęp do zasobu linku prywatnego przy użyciu przepływu pracy zatwierdzania 
Połączenie z zasobem łącza prywatnego można nawiązać przy użyciu następujących metod zatwierdzania połączeń:
- **Zatwierdzona** automatycznie, gdy jesteś właścicielem lub masz uprawnienia do określonego zasobu łącza prywatnego. Wymagane uprawnienie jest oparte na typie zasobu linku prywatnego w następującym formacie: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Żądanie** ręczne, jeśli nie masz wymaganych uprawnień i chcesz zażądać dostępu. Zostanie zainicjowany przepływ pracy zatwierdzania. Prywatny punkt końcowy i kolejne połączenie prywatnego punktu końcowego zostaną utworzone ze stanem „Oczekiwanie”. Właściciel zasobu linku prywatnego jest odpowiedzialny za zaakceptowanie połączenia. Po zatwierdzeniu prywatny punkt końcowy może wysyłać ruch normalnie, jak pokazano na poniższym diagramie przepływu pracy zatwierdzania.  

![zatwierdzanie przepływu pracy](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Właściciel zasobu linku prywatnego może wykonywać następujące akcje za pośrednictwem połączenia prywatnego punktu końcowego: 
- Przejrzyj wszystkie szczegóły połączeń z prywatnym punktem końcowym. 
- Zatwierdzanie połączenia prywatnego punktu końcowego. Odpowiedni prywatny punkt końcowy będzie w stanie wysyłać ruch do zasobu łącza prywatnego. 
- Odrzuca połączenie prywatnego punktu końcowego. Odpowiedni prywatny punkt końcowy zostanie zaktualizowany w celu odzwierciedlenia stanu.
- Usuń połączenie prywatnego punktu końcowego w dowolnym stanie. Odpowiedni prywatny punkt końcowy zostanie zaktualizowany o stan odłączony, aby odzwierciedlić akcję. Właściciel prywatnego punktu końcowego może w tym momencie usunąć tylko zasób. 
 
> [!NOTE]
> Tylko prywatny punkt końcowy w stanie zatwierdzonym może wysyłać ruch do danego zasobu łącza prywatnego. 

### <a name="connecting-using-alias"></a>Nawiązywanie połączenia przy użyciu aliasu
Alias to unikatowa moniker, która jest generowana, gdy właściciel usługi tworzy usługę łącza prywatnego za standardowym usługą równoważenia obciążenia. Właściciel usługi może udostępnić ten alias swoim klientom w trybie offline. Użytkownicy mogą zażądać połączenia z usługą łącza prywatnego przy użyciu adresu URI zasobu lub aliasu. Jeśli chcesz nawiązać połączenie przy użyciu aliasu, musisz utworzyć prywatny punkt końcowy przy użyciu metody ręcznego zatwierdzania połączenia. W przypadku korzystania z metody zatwierdzania ręcznego połączenia ustaw parametr żądania ręcznego na wartość true podczas przepływu tworzenia prywatnego punktu końcowego. Aby uzyskać szczegółowe [informacje, zobacz New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) i [az network private-endpoint create.](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 

## <a name="dns-configuration"></a>Konfiguracja usługi DNS 
Podczas nawiązywania połączenia z zasobem łącza prywatnego przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia ważne jest prawidłowe skonfigurowanie ustawień DNS w celu rozpoznania przydzielonego prywatnego adresu IP. Istniejące usługi platformy Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia za pośrednictwem publicznego punktu końcowego. Należy to zastąpić, aby nawiązać połączenie przy użyciu prywatnego punktu końcowego. 
 
Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełny zestaw informacji wymaganych do skonfigurowania usługi DNS, w tym nazwy FQDN i prywatne adresy IP przydzielone do danego zasobu łącza prywatnego. 

Aby uzyskać szczegółowe informacje o najlepszych rozwiązaniach i zaleceniach dotyczących konfigurowania systemu DNS dla prywatnych punktów końcowych, zapoznaj się z artykułem Konfiguracja [usługi DNS prywatnego punktu końcowego.](private-endpoint-dns.md)



 
## <a name="limitations"></a>Ograniczenia
 
W poniższej tabeli przedstawiono listę znanych ograniczeń dotyczących korzystania z prywatnych punktów końcowych: 


|Ograniczenie |Opis |Ograniczanie ryzyka  |
|---------|---------|---------|
|Reguły sieciowej grupy zabezpieczeń i trasy zdefiniowane przez użytkownika nie mają zastosowania do prywatnego punktu końcowego    |Sieciowa żadna z tych sieci nie jest obsługiwana w prywatnych punktach końcowych. Chociaż z podsieciami zawierającymi prywatny punkt końcowy może być skojarzona sieciowa skojarzona sieciowa skojarzona, reguły nie będą obowiązywać w przypadku ruchu przetwarzanego przez prywatny punkt końcowy. Aby wdrożyć [prywatne](disable-private-endpoint-network-policy.md) punkty końcowe w podsieci, musisz mieć wyłączone wymuszanie zasad sieciowych. Sieciowa grupa zasad grupy jest nadal wymuszana w innych obciążeniach hostowanych w tej samej podsieci. Trasy w dowolnej podsieci klienta będą używać prefiksu /32. Zmiana domyślnego zachowania routingu wymaga podobnej trasy UDR  | Kontroluj ruch przy użyciu reguł sieciowej sieciowej sieciowej organizacji dla ruchu wychodzącego na klientach źródłowych. Wdrażanie poszczególnych tras z prefiksem /32 w celu zastąpienia tras prywatnego punktu końcowego. Dzienniki przepływu sieciowej organizacji zabezpieczeń i informacje dotyczące monitorowania połączeń wychodzących są nadal obsługiwane i mogą być używane        |


## <a name="next-steps"></a>Następne kroki
- [Tworzenie prywatnego punktu końcowego dla SQL Database przy użyciu portalu](create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego dla SQL Database przy użyciu programu PowerShell](create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego dla usługi SQL Database interfejsu wiersza polecenia](create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego dla konta magazynu przy użyciu portalu](./tutorial-private-endpoint-storage-portal.md)
- [Tworzenie prywatnego punktu końcowego dla konta usługi Azure Cosmos przy użyciu portalu](../cosmos-db/how-to-configure-private-endpoints.md)
- [Tworzenie własnej usługi Private Link przy użyciu Azure PowerShell](create-private-link-service-powershell.md)
- [Tworzenie własnych aplikacji Private Link dla Azure Database for PostgreSQL — pojedynczy serwer przy użyciu portalu](../postgresql/howto-configure-privatelink-portal.md)
- [Tworzenie własnego interfejsu Private Link dla Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia](../postgresql/howto-configure-privatelink-cli.md)
- [Tworzenie własnych aplikacji Private Link do Azure Database for MySQL przy użyciu portalu](../mysql/howto-configure-privatelink-portal.md)
- [Tworzenie własnego interfejsu Private Link do Azure Database for MySQL interfejsu wiersza polecenia](../mysql/howto-configure-privatelink-cli.md)
- [Tworzenie własnych aplikacji Private Link do Azure Database for MariaDB przy użyciu portalu](../mariadb/howto-configure-privatelink-portal.md)
- [Tworzenie własnego interfejsu Private Link dla Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia](../mariadb/howto-configure-privatelink-cli.md)
- [Tworzenie własnych aplikacji Private Link dla Azure Key Vault za pomocą portalu i interfejsu wiersza polecenia](../key-vault/general/private-link-service.md)
