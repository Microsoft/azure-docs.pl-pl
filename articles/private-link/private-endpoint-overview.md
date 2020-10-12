---
title: Co to jest prywatny punkt końcowy platformy Azure?
description: Informacje o prywatnym punkcie końcowym platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 7456402605328592d4f5677767bcd985941173ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639838"
---
# <a name="what-is-azure-private-endpoint"></a>Co to jest prywatny punkt końcowy platformy Azure?

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej. Usługa może być usługą platformy Azure, taką jak Azure Storage, Azure Cosmos DB, SQL itp. lub własną [usługą linku prywatnego](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Właściwości prywatnego punktu końcowego 
 Prywatny punkt końcowy określa następujące właściwości: 


|Właściwość  |Opis |
|---------|---------|
|Nazwa    |    Unikatowa nazwa w obrębie grupy zasobów.      |
|Podsieć    |  Podsieć do wdrożenia i przydzielenia prywatnych adresów IP z sieci wirtualnej. Wymagania dotyczące podsieci znajdują się w sekcji ograniczenia w tym artykule.         |
|Zasób link prywatny    |   Zasób link prywatny do łączenia się przy użyciu identyfikatora zasobu lub aliasu z listy dostępnych typów. Dla całego ruchu wysyłanego do tego zasobu zostanie wygenerowany unikatowy identyfikator sieci.       |
|Podzasób docelowy   |      Podzasób do nawiązania połączenia. Każdy typ zasobu link prywatny ma różne opcje, które można wybrać na podstawie preferencji.    |
|Metoda zatwierdzania połączeń    |  Automatyczne lub ręczne. W oparciu o uprawnienia kontroli dostępu opartej na rolach (RBAC) można automatycznie zatwierdzać prywatny punkt końcowy. Jeśli spróbujesz nawiązać połączenie z prywatnym zasobem linku bez RBAC, użyj metody ręcznej, aby zezwolić właścicielowi zasobu na zatwierdzanie połączenia.        |
|Komunikat żądania     |  Można określić komunikat dla żądanych połączeń, które mają być zatwierdzane ręcznie. Ten komunikat może służyć do identyfikowania konkretnego żądania.        |
|Stan połączenia   |   Właściwość tylko do odczytu określająca, czy prywatny punkt końcowy jest aktywny. Do wysyłania ruchu można używać tylko prywatnych punktów końcowych w zatwierdzonym stanie. Dostępne są dodatkowe Stany: <br>-**Zatwierdzono**: połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia.</br><br>-**Oczekiwanie**: połączenie zostało utworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu link prywatny.</br><br>-**Odrzucone**: połączenie zostało odrzucone przez właściciela zasobu linku prywatnego.</br><br>-**Rozłączono**: połączenie zostało usunięte przez właściciela zasobu link prywatny. Prywatny punkt końcowy zmienia się na format i powinien zostać usunięty do oczyszczenia. </br>|

Poniżej przedstawiono niektóre kluczowe szczegóły dotyczące prywatnych punktów końcowych: 
- Prywatny punkt końcowy umożliwia łączność między konsumentami z tej samej sieci wirtualnej, z regionalnie równorzędną sieci wirtualnych, globalnie równorzędną sieci wirtualnych i lokalnie przy użyciu [sieci VPN](https://azure.microsoft.com/services/vpn-gateway/) lub [Express Route](https://azure.microsoft.com/services/expressroute/) i usługi obsługiwane przez link prywatny.
 
- Połączenia sieciowe mogą być inicjowane tylko przez klientów nawiązujących połączenie z prywatnym punktem końcowym, ale dostawcy usług nie mają żadnej konfiguracji routingu do inicjowania połączeń z klientami usługi. Połączenia można nawiązywać tylko w jednym kierunku.

- Podczas tworzenia prywatnego punktu końcowego jest również tworzony interfejs sieciowy tylko do odczytu dla cyklu życia zasobu. Interfejsowi przypisano dynamicznie prywatne adresy IP z podsieci, która jest mapowana na zasób link prywatny. Wartość prywatnego adresu IP pozostaje niezmieniona dla całego cyklu życia prywatnego punktu końcowego.
 
- Prywatny punkt końcowy musi być wdrożony w tym samym regionie, w którym znajduje się sieć wirtualna. 
 
- Zasób link prywatny można wdrożyć w innym regionie niż sieć wirtualna i prywatny punkt końcowy.
 
- Można utworzyć wiele prywatnych punktów końcowych przy użyciu tego samego zasobu linku prywatnego. W przypadku pojedynczej sieci używającej typowej konfiguracji serwera DNS zalecanym rozwiązaniem jest użycie jednego prywatnego punktu końcowego dla danego zasobu linku prywatnego, aby uniknąć zduplikowanych wpisów lub konfliktów w rozpoznawaniu nazw DNS. 
 
- Wiele prywatnych punktów końcowych można utworzyć w tej samej lub różnych podsieciach w ramach tej samej sieci wirtualnej. Istnieją limity liczby prywatnych punktów końcowych, które można utworzyć w ramach subskrypcji. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

- Subskrypcję z zasobu linku prywatnego należy również zarejestrować przy użyciu dostawcy zasobów pomocą. Network. Aby uzyskać szczegółowe informacje, zobacz [dostawcy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

 
## <a name="private-link-resource"></a>Zasób link prywatny 
Zasób link prywatny jest docelowym miejscem docelowym danego prywatnego punktu końcowego. Poniżej znajduje się lista dostępnych typów zasobów łącza prywatnego: 
 
|Nazwa zasobu linku prywatnego  |Typ zasobu   |Dowolnych podrzędnych  |
|---------|---------|---------|
|**Usługa link prywatny** (Twoja usługa)   |  Microsoft. Network/privateLinkServices       | puste |
|**Azure Automation** |  Microsoft. Automation/automationAccounts | Element webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft. SQL/serwery    |  Program SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft. SQL/serwery    |  Program SQL Server (sqlServer)        | 
|**Azure Storage**  | Microsoft. Storage/storageAccounts    |  Obiekt BLOB (BLOB, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Kolejka (Kolejka, queue_secondary)<BR> Plik (plik, file_secondary)<BR> Sieć Web (sieć Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft. Storage/storageAccounts    |  Obiekt BLOB (BLOB, blob_secondary)<BR> Gen2 systemu plików Data Lake (system plików DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, tabela|
|**Azure Database for PostgreSQL — pojedynczy serwer** | Microsoft. DBforPostgreSQL/serwery    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft. DBforMySQL/serwery    | Mójserwersql |
|**Azure Database for MariaDB** | Microsoft. DBforMariaDB/serwery    | mariadbServer |
|**Azure IoT Hub** | Microsoft. Devices/IotHubs    | iotHub |
|**Usługa Azure Key Vault** | Microsoft./magazyny kluczy    | magazyn |
|**Usługa Azure Kubernetes Service — interfejs API Kubernetes** | Microsoft. ContainerService/managedClusters    | zarządzanie |
|**Azure Search** | Microsoft. Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft. ContainerRegistry/rejestry    | registry |
|**Azure App Configuration** | Microsoft. Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft. RecoveryServices/magazyny    | magazyn |
|**Centrum zdarzeń Azure** | Microsoft. EventHub/przestrzenie nazw    | namespace |
|**Azure Service Bus** | Microsoft. ServiceBus/przestrzenie nazw | namespace |
|**Azure Relay** | Microsoft. Relay/przestrzenie nazw | namespace |
|**Azure Event Grid** | Microsoft. EventGrid/tematy    | temat |
|**Azure Event Grid** | Microsoft. EventGrid/domeny    | domena |
|**Azure webapps** | Microsoft. Web/witryny    | lokacje |
|**Azure Machine Learning** | Microsoft. MachineLearningServices/obszary robocze    | obszar roboczy |
|**SignalR** | Microsoft. SignalRService/sygnalizujący    | signalR |
|**Azure Monitor** | Microsoft. Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft. CognitiveServices/accounts    | account |
|**Azure File Sync** | Microsoft. StorageSync/storageSyncServices    | AFS |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Zabezpieczenia sieciowe prywatnych punktów końcowych 
Gdy korzystasz z prywatnych punktów końcowych dla usług platformy Azure, ruch jest zabezpieczony do określonego zasobu linku prywatnego. Platforma wykonuje kontrolę dostępu, aby sprawdzić poprawność połączeń sieciowych tylko do określonego zasobu linku prywatnego. Aby uzyskać dostęp do dodatkowych zasobów w ramach tej samej usługi platformy Azure, wymagane są dodatkowe prywatne punkty końcowe. 
 
Możesz całkowicie zablokować obciążenia, aby uzyskać dostęp do publicznych punktów końcowych w celu nawiązania połączenia z obsługiwaną usługą platformy Azure. Ta kontrolka zapewnia dodatkową warstwę zabezpieczeń sieci dla zasobów, udostępniając wbudowaną ochronę eksfiltracji, która uniemożliwia dostęp do innych zasobów hostowanych w tej samej usłudze platformy Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Dostęp do prywatnego zasobu linku przy użyciu przepływu pracy zatwierdzania 
Możesz połączyć się z zasobem link prywatny przy użyciu następujących metod zatwierdzania połączeń:
- **Automatycznie** zatwierdzane podczas posiadania lub masz uprawnienia do określonego zasobu łącza prywatnego. Wymagane uprawnienie jest oparte na typie zasobu link prywatny w następującym formacie: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Ręczne** żądanie, gdy nie masz wymaganego uprawnienia i chcesz zażądać dostępu. Zostanie zainicjowany przepływ pracy zatwierdzania. Prywatny punkt końcowy i kolejne połączenie prywatnego punktu końcowego zostaną utworzone ze stanem „Oczekiwanie”. Właściciel zasobu linku prywatnego jest odpowiedzialny za zaakceptowanie połączenia. Po jego zatwierdzeniu prywatny punkt końcowy jest włączony do wysyłania ruchu normalnie, jak pokazano na poniższym diagramie przepływu pracy zatwierdzania.  

![zatwierdzenie przepływu pracy](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Właściciel zasobu link prywatny może wykonywać następujące akcje za pośrednictwem połączenia prywatnego punktu końcowego: 
- Przejrzyj wszystkie szczegóły połączeń prywatnych punktów końcowych. 
- Zatwierdź połączenie prywatnego punktu końcowego. Odpowiedni prywatny punkt końcowy zostanie włączony do wysyłania ruchu do prywatnego zasobu linku. 
- Odrzuć połączenie prywatnego punktu końcowego. Odpowiedni prywatny punkt końcowy zostanie zaktualizowany w celu odzwierciedlenia stanu.
- Usuń połączenie prywatnego punktu końcowego w dowolnym stanie. Odpowiedni prywatny punkt końcowy zostanie zaktualizowany ze stanem rozłączenia w celu odzwierciedlenia akcji, właściciel prywatnego punktu końcowego może usunąć tylko zasób w tym momencie. 
 
> [!NOTE]
> Tylko prywatny punkt końcowy w zatwierdzonym stanie może wysyłać ruch do podanego zasobu linku prywatnego. 

### <a name="connecting-using-alias"></a>Łączenie przy użyciu aliasu
Alias jest unikatowy moniker, który jest generowany, gdy właściciel usługi tworzy prywatną usługę linku za pomocą usługi równoważenia obciążenia w warstwie Standardowa. Właściciel usługi może udostępniać ten alias użytkownikom w trybie offline. Konsumenci mogą zażądać połączenia z usługą linku prywatnego przy użyciu identyfikatora URI zasobu lub aliasu. Jeśli chcesz nawiązać połączenie przy użyciu aliasu, musisz utworzyć prywatny punkt końcowy przy użyciu metody ręcznego zatwierdzania połączeń. W przypadku używania metody zatwierdzania ręcznego połączenia ustaw parametr żądania ręcznego na wartość true podczas tworzenia przepływu prywatnego punktu końcowego. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) i [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) . 

## <a name="dns-configuration"></a>Konfiguracja usługi DNS 
W przypadku nawiązywania połączenia z prywatnym zasobem linku przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części parametrów połączenia należy prawidłowo skonfigurować ustawienia DNS w celu rozpoznania przydzielony prywatny adres IP. Istniejące usługi platformy Azure mogą już mieć konfigurację DNS do użycia podczas nawiązywania połączenia przez publiczny punkt końcowy. Należy je zastąpić, aby nawiązać połączenie przy użyciu prywatnego punktu końcowego. 
 
Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełen zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatnych adresów IP przyznanych dla danego zasobu linku prywatnego. 

Aby uzyskać szczegółowe informacje o najlepszych rozwiązaniach i zaleceniach dotyczących konfigurowania usługi DNS dla prywatnych punktów końcowych, zobacz [artykuł Konfiguracja usługi DNS dla prywatnego punktu końcowego](private-endpoint-dns.md).



 
## <a name="limitations"></a>Ograniczenia
 
Poniższa tabela zawiera listę znanych ograniczeń dotyczących używania prywatnych punktów końcowych: 


|Ograniczenie |Opis |Ograniczanie ryzyka  |
|---------|---------|---------|
|Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i trasy zdefiniowane przez użytkownika nie mają zastosowania do prywatnego punktu końcowego    |SIECIOWEJ grupy zabezpieczeń nie jest obsługiwany w prywatnych punktach końcowych. W podsieciach zawierających prywatny punkt końcowy może być skojarzonych sieciowej grupy zabezpieczeń, więc reguły nie będą obowiązywać w przypadku ruchu przetwarzanego przez prywatny punkt końcowy. Aby wdrażać prywatne punkty końcowe w podsieci, należy [wyłączyć wymuszanie zasad sieciowych](disable-private-endpoint-network-policy.md) . SIECIOWEJ grupy zabezpieczeń jest nadal wymuszane dla innych obciążeń hostowanych w tej samej podsieci. Trasy w dowolnej podsieci klienta będą używać prefiksu/32, zmiana domyślnego zachowania routingu wymaga podobnego UDR  | Kontroluj ruch przy użyciu reguł sieciowej grupy zabezpieczeń dla ruchu wychodzącego na klientach źródłowych. Wdróż pojedyncze trasy z prefiksem/32, aby przesłonić prywatne trasy punktów końcowych. Dzienniki przepływu sieciowej grupy zabezpieczeń i informacje monitorowania dla połączeń wychodzących są nadal obsługiwane i mogą być używane        |


## <a name="next-steps"></a>Następne kroki
- [Tworzenie prywatnego punktu końcowego dla SQL Database przy użyciu portalu ](create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego dla SQL Database przy użyciu programu PowerShell ](create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego dla SQL Database przy użyciu interfejsu wiersza polecenia ](create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego dla konta magazynu przy użyciu portalu ](create-private-endpoint-storage-portal.md)
- [Tworzenie prywatnego punktu końcowego dla konta usługi Azure Cosmos za pomocą portalu ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Tworzenie własnej usługi linku prywatnego przy użyciu Azure PowerShell](create-private-link-service-powershell.md)
- [Utwórz własne łącze prywatne dla Azure Database for PostgreSQL-pojedynczego serwera przy użyciu portalu](../postgresql/howto-configure-privatelink-portal.md)
- [Utwórz własne połączenie prywatne dla Azure Database for PostgreSQL-pojedynczego serwera przy użyciu interfejsu wiersza polecenia](../postgresql/howto-configure-privatelink-cli.md)
- [Tworzenie własnego linku prywatnego dla Azure Database for MySQL przy użyciu portalu](../mysql/howto-configure-privatelink-portal.md)
- [Utwórz własne łącze prywatne dla Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](../mysql/howto-configure-privatelink-cli.md)
- [Tworzenie własnego linku prywatnego dla Azure Database for MariaDB przy użyciu portalu](../mariadb/howto-configure-privatelink-portal.md)
- [Utwórz własne łącze prywatne dla Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia](../mariadb/howto-configure-privatelink-cli.md)
