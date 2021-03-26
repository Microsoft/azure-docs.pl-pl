---
title: Omówienie tagów usługi platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o tagach usługi. Tagi usług pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2d14ca2423d34926a9e297823a6515c2c5dde06a
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607120"
---
# <a name="virtual-network-service-tags"></a>Tagi usługi sieci wirtualnej
<a name="network-service-tags"></a>

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych.

Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](./network-security-groups-overview.md#security-rules) lub w [zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi, taką jak **ApiManagement**, w odpowiednim polu *źródłowym* lub *docelowym* reguły można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. 

> [!NOTE] 
> Począwszy od marca 2021, można również używać tagów usługi zamiast jawnych zakresów adresów IP w ramach [tras zdefiniowanych przez użytkownika](./virtual-networks-udr-overview.md). Ta funkcja jest obecnie w publicznej wersji zapoznawczej. 

Tagi usług umożliwiają uzyskanie izolacji sieci i ochronę zasobów platformy Azure z ogólnego Internetu podczas uzyskiwania dostępu do usług platformy Azure z publicznymi punktami końcowymi. Utwórz przychodzące/wychodzące reguły sieciowej grupy zabezpieczeń, aby odmówić ruch do/z **Internetu** i zezwolić na ruch do/z **AzureCloud** lub innych [dostępnych tagów usługi](#available-service-tags) określonych usług platformy Azure.

![Izolacja sieci usług platformy Azure przy użyciu tagów usługi](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Dostępne Tagi usług
Poniższa tabela zawiera wszystkie Tagi usługi dostępne do użycia w regułach [sieciowej grupy zabezpieczeń](./network-security-groups-overview.md#security-rules) .

Kolumny wskazują, czy tag:

- Jest odpowiednie dla reguł obejmujących ruch przychodzący lub wychodzący.
- Obsługuje zakres [regionalny](https://azure.microsoft.com/regions) .
- Jest użyteczny w regułach [zapory platformy Azure](../firewall/service-tags.md) .

Domyślnie Tagi usług odzwierciedlają zakresy dla całej chmury. Niektóre Tagi usług umożliwiają również dokładniejszą kontrolę poprzez ograniczenie odpowiednich zakresów adresów IP do określonego regionu. Na przykład **Magazyn** znacznika usługi reprezentuje usługę Azure Storage dla całej chmury, ale w obszarze **Magazyn. Zachodnie** zawęża zakres tylko do zakresów adresów IP magazynu z regionu zachód. Poniższa tabela wskazuje, czy każdy tag usługi obsługuje taki zakres regionalny.  

| Tag | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Grupa akcji. | Przychodzący | Nie | Nie |
| **ApiManagement** | Ruch związany z zarządzaniem dla wdrożeń dedykowanych API Management platformy Azure. <br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi Azure API Management dla płaszczyzny kontroli na region. Dzięki temu klienci mogą wykonywać operacje związane z zarządzaniem w ramach interfejsów API, operacji, zasad, NamedValues skonfigurowanych w usłudze API Management.  | Przychodzący | Tak | Tak |
| **ApplicationInsightsAvailability** | Dostępność Application Insights. | Przychodzący | Nie | Nie |
| **AppConfiguration** | Konfiguracja aplikacji. | Wychodzący | Nie | Nie |
| **AppService**    | Azure App Service. Ten tag jest zalecany w przypadku reguł zabezpieczeń wychodzących do aplikacji sieci Web i aplikacji funkcji.  | Wychodzący | Tak | Tak |
| **AppServiceManagement** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla App Service Environment. | Oba | Nie | Tak |
| **Usługi azureactivedirectory** | Azure Active Directory. | Wychodzący | Nie | Tak |
| **AzureActiveDirectoryDomainServices** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla Azure Active Directory Domain Services. | Oba | Nie | Tak |
| **AzureAdvancedThreatProtection** | Zaawansowana ochrona przed zagrożeniami na platformie Azure. | Wychodzący | Nie | Nie |
| **AzureAPIForFHIR** | Azure API for FHIR (zasoby współdziałania szybkich usług opieki zdrowotnej).<br/><br/> *Uwaga: ten tag nie jest obecnie konfigurowalny za pomocą witryny Azure Portal.*| Wychodzący | Nie | Nie |
| **AzureArcInfrastructure** | Serwery z obsługą usługi Azure ARC, usługa Azure ARC z włączonym Kubernetes i ruchem konfiguracyjnym gościa.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **usługi azureactivedirectory**,**AzureTrafficManager** i **AzureResourceManager** . *Ten tag nie jest obecnie konfigurowalny za pomocą witryny Azure Portal*.| Wychodzący | Nie | Tak |
| **AzureBackup** |Azure Backup.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **Storage** i **usługi azureactivedirectory** . | Wychodzący | Nie | Tak |
| **AzureBotService** | Azure Bot Service. | Wychodzący | Nie | Nie |
| **AzureCloud** | Wszystkie [publiczne adresy IP centrum](https://www.microsoft.com/download/details.aspx?id=56519)danych. | Wychodzący | Tak | Tak |
| **AzureCognitiveSearch** | Wyszukiwanie poznawcze platformy Azure. <br/><br/>Ten tag lub adresy IP objęte tym tagiem mogą służyć do przyznawania indeksatorów bezpiecznego dostępu do źródeł danych. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą połączenia indeksatora](../search/search-indexer-troubleshooting.md#connection-errors) . <br/><br/> *Uwaga*: adres IP usługi wyszukiwania nie jest uwzględniony na liście zakresów adresów IP dla tego tagu usługi, a **także należy dodać** do zapory IP źródeł danych. | Przychodzący | Nie | Nie |
| **AzureConnectors** | Ten tag reprezentuje adresy IP używane dla łączników zarządzanych, które tworzą przychodzące wywołania zwrotne elementu webhook do usługi Azure Logic Apps i wychodzące wywołania do odpowiednich usług, na przykład Azure Storage lub Azure Event Hubs. | Przychodzące/wychodzące | Tak | Tak |
| **AzureContainerRegistry** | Azure Container Registry. | Wychodzący | Tak | Tak |
| **AzureCosmosDB** | Azure Cosmos DB. | Wychodzący | Tak | Tak |
| **AzureDatabricks** | Azure Databricks. | Oba | Nie | Nie |
| **AzureDataExplorerManagement** | Zarządzanie Eksplorator danych platformy Azure. | Przychodzący | Nie | Nie |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Wychodzący | Nie | Tak |
| **AzureDevSpaces** | Azure Dev Spaces. | Wychodzący | Nie | Nie |
| **AzureDevOps** | Azure dev Ops.<br/><br/>*Uwaga: ten tag nie jest obecnie konfigurowalny za pomocą witryny Azure Portal*| Przychodzący | Nie | Tak |
| **AzureDigitalTwins** | Usługa Azure Digital bliźniaczych reprezentacji.<br/><br/>*Uwaga:* Ten tag lub adresy IP objęte tym tagiem mogą służyć do ograniczania dostępu do punktów końcowych skonfigurowanych dla tras zdarzeń. *Ten tag nie jest obecnie konfigurowalny za pomocą witryny Azure Portal* | Przychodzący | Nie | Tak |
| **AzureEventGrid** | Azure Event Grid. | Oba | Nie | Nie |
| **AzureFrontDoor. frontonu** <br/> **AzureFrontDoor. zaplecza** <br/> **AzureFrontDoor.FirstParty**  | Moje drzwi platformy Azure. | Oba | Nie | Nie |
| **Moduł AzureInformationProtection** | Azure Information Protection.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **usługi azureactivedirectory**, **AzureFrontDoor. frontonu** i **AzureFrontDoor. FirstParty** . | Wychodzący | Nie | Nie |
| **AzureIoTHub** | Azure IoT Hub. | Wychodzący | Nie | Nie |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Uwaga:* Ten tag ma zależność od znacznika **usługi azureactivedirectory** . | Wychodzący | Tak | Tak |
| **AzureLoadBalancer** | Moduł równoważenia obciążenia infrastruktury platformy Azure. Tag tłumaczy na [wirtualny adres IP hosta](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16), z którego pochodzą sondy kondycji platformy Azure. Obejmuje to tylko ruch sondy, a nie rzeczywisty ruch do zasobu zaplecza. Jeśli nie używasz Azure Load Balancer, możesz zastąpić tę regułę. | Oba | Nie | Nie |
| **AzureMachineLearning** | Azure Machine Learning. | Oba | Nie | Tak |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon i metryki niestandardowe (punkty końcowe rdzeniowym procesorem).<br/><br/>*Uwaga:* W przypadku Log Analytics wymagany jest również tag **magazynu** . Jeśli są używane agenci systemu Linux, wymagany jest również tag **GuestAndHybridManagement** . | Wychodzący | Nie | Tak |
| **AzureOpenDatasets** | Otwarte zestawy danych platformy Azure.<br/><br/>*Uwaga:* Ten tag ma zależność od tagu **AzureFrontDoor. frontonu** i **magazynu** . | Wychodzący | Nie | Nie |
| **AzurePlatformDNS** | Podstawowa infrastruktura (domyślna) usługi DNS.<br/><br>Za pomocą tego tagu można wyłączyć domyślny system DNS. Należy zachować ostrożność w przypadku użycia tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](./network-security-groups-overview.md#azure-platform-considerations). Zalecamy również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), czyli podstawowa usługa infrastruktury.<br/><br/>Ten tag służy do wyłączania domyślnego IMDS. Należy zachować ostrożność w przypadku użycia tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](./network-security-groups-overview.md#azure-platform-considerations). Zalecamy również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformLKM** | Licencjonowanie systemu Windows lub usługa zarządzania kluczami.<br/><br/>Ten tag służy do wyłączania ustawień domyślnych dla licencjonowania. Należy zachować ostrożność w przypadku użycia tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](./network-security-groups-overview.md#azure-platform-considerations).  Zalecamy również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzureResourceManager** | W usłudze Azure Resource Manager. | Wychodzący | Nie | Nie |
| **AzureSignalR** | Usługa Azure sygnalizująca. | Wychodzący | Nie | Nie |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **usługi azureactivedirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** i **Storage** . | Wychodzący | Nie | Nie |
| **AzureTrafficManager** | Adresy IP badania Traffic Manager platformy Azure.<br/><br/>Aby uzyskać więcej informacji na temat Traffic Manager adresów IP sondowania, zobacz [często zadawane pytania dotyczące usługi Azure Traffic Manager](../traffic-manager/traffic-manager-faqs.md). | Przychodzący | Nie | Tak |  
| **BatchNodeManagement** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla Azure Batch. | Oba | Nie | Tak |
| **CognitiveServicesManagement** | Zakresy adresów dla ruchu dla Cognitive Services platformy Azure. | Oba | Nie | Nie |
| **DataFactory**  | Azure Data Factory | Oba | Nie | Nie |
| **DataFactoryManagement** | Ruch związany z zarządzaniem dla Azure Data Factory. | Wychodzący | Nie | Nie |
| **Dynamics365ForMarketingEmail** | Zakresy adresów dla usługi marketingowej poczty programu Dynamics 365. | Wychodzący | Tak | Nie |
| **EventHub** | Event Hubs platformy Azure. | Wychodzący | Tak | Tak |
| **GatewayManager** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla usługi Azure VPN Gateway i Application Gateway. | Przychodzący | Nie | Nie |
| **GuestAndHybridManagement** | Azure Automation i konfiguracja gościa. | Wychodzący | Nie | Tak |
| **HDInsight** | Usługa Azure HDInsight. | Przychodzący | Tak | Nie |
| **Internet** | Przestrzeń adresów IP, która znajduje się poza siecią wirtualną i jest dostępna przez publiczny Internet.<br/><br/>Zakres adresów obejmuje [publiczną przestrzeń adresów IP należącą do platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Oba | Nie | Nie |
| **LogicApps** | Logic Apps. | Oba | Nie | Nie |
| **LogicAppsManagement** | Ruch związany z zarządzaniem dla Logic Apps. | Przychodzący | Nie | Nie |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Wychodzący | Nie | Nie |
| **MicrosoftContainerRegistry** | Rejestr kontenerów dla obrazów kontenerów firmy Microsoft. <br/><br/>*Uwaga:* Ten tag ma zależność od tagu **AzureFrontDoor. FirstParty** . | Wychodzący | Tak | Tak |
| **PowerBI** | PowerBi. *Uwaga: ten tag nie jest obecnie konfigurowalny za pomocą witryny Azure Portal.* | Oba | Nie | Nie|
| **PowerQueryOnline** | Power Query w trybie online. | Oba | Nie | Nie |
| **ServiceBus** | Azure Service Bus ruch korzystający z warstwy usługi Premium. | Wychodzący | Tak | Tak |
| **ServiceFabric** | Service Fabric platformy Azure.<br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi Service Fabric dla płaszczyzny kontroli na region. Dzięki temu klienci mogą wykonywać operacje związane z zarządzaniem Service Fabric klastrami z sieci wirtualnej (np. https://westus.servicefabric.azure.com) | Oba | Nie | Nie |
| **Server** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure Synapse Analytics.<br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. Ten tag nie ma zastosowania do wystąpienia zarządzanego SQL. | Wychodzący | Tak | Tak |
| **Xmlmanagement** | Ruch związany z zarządzaniem dla wdrożeń dedykowanych przez program SQL Server. | Oba | Nie | Tak |
| **Storage** | Azure Storage. <br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. | Wychodzący | Tak | Tak |
| **StorageSyncService** | Usługa synchronizacji magazynu. | Oba | Nie | Nie |
| **WindowsVirtualDesktop** | Pulpit wirtualny systemu Windows. | Oba | Nie | Tak |
| **VirtualNetwork** | Przestrzeń adresowa sieci wirtualnej (wszystkie zakresy adresów IP zdefiniowane dla sieci wirtualnej), wszystkie połączone lokalne przestrzenie adresowe, [równorzędne](virtual-network-peering-overview.md) sieci wirtualne, sieci wirtualne połączone z [bramą sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), [wirtualny adres IP hosta](./network-security-groups-overview.md#azure-platform-considerations)i prefiksy adresów używane na [trasach zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md). Ten tag może również zawierać trasy domyślne. | Oba | Nie | Nie |

>[!NOTE]
>W klasycznym modelu wdrażania (przed Azure Resource Manager) jest obsługiwany podzestaw tagów wymienionych w poprzedniej tabeli. Te Tagi są napisane inaczej:
>
>| Pisownia klasyczna | Odpowiednik tagu Menedżer zasobów |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tagi usług platformy Azure oznaczają prefiksy adresów z określonej chmury. Na przykład bazowe zakresy adresów IP, które odpowiadają wartości tagu **SQL** w chmurze publicznej platformy Azure, będą inne niż bazowe zakresy w chmurze platformy Azure w Chinach.

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje [trasę](virtual-networks-udr-overview.md#optional-default-routes) do podsieci sieci wirtualnej dla usługi. Prefiksy adresów w marszrucie to te same prefiksy adresów lub zakresy CIDR, jak w przypadku odpowiedniego tagu usługi.

## <a name="service-tags-on-premises"></a>Lokalne znaczniki usług  
Możesz uzyskać informacje o bieżącym znaczniku i zakresie usługi, aby uwzględnić je w ramach konfiguracji zapory lokalnej. Te informacje to bieżąca lista punktów adresów IP, które odpowiadają każdemu tagowi usługi. Możesz uzyskać informacje programowo lub za pomocą pobierania pliku JSON, zgodnie z opisem w poniższych sekcjach.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Korzystanie z interfejsu API odnajdywania tagów usługi (publiczna wersja zapoznawcza)
Można programowo pobrać bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> W publicznej wersji zapoznawczej interfejs API odnajdywania może zwracać informacje, które są mniej aktualne niż informacje zwrócone przez pobrane dane JSON. (Zobacz następną sekcję).


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Odnajdywanie tagów usługi przy użyciu plików JSON do pobrania 
Możesz pobrać pliki JSON zawierające bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP. Te listy są aktualizowane i publikowane co tydzień. Lokalizacje dla każdej chmury są następujące:

- [Usługa Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Chiny platformy Azure](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)   

Zakresy adresów IP w tych plikach są w notacji CIDR. 

> [!NOTE]
>Podzestaw tych informacji został opublikowany w plikach XML dla usług [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Chiny](https://www.microsoft.com/download/details.aspx?id=42064)i [Azure (Niemcy](https://www.microsoft.com/download/details.aspx?id=54770)). Te pobrania XML będą przestarzałe do 30 czerwca 2020 i nie będą już dostępne po tej dacie. Należy przeprowadzić migrację do programu przy użyciu interfejsu API odnajdywania lub plików JSON, jak opisano w poprzednich sekcjach.

### <a name="tips"></a>Porady 
- Możesz wykryć aktualizacje z jednej publikacji do następnej, zwracając więcej wartości *changeNumber* w pliku JSON. Każda podsekcja (na przykład **Storage. zachodni**) ma własne *changeNumber* , które są zwiększane, gdy wystąpią zmiany. Najwyższy poziom *changeNumber* pliku jest zwiększany, gdy dowolna z podsekcji jest zmieniana.
- Aby zapoznać się z przykładami sposobu analizowania informacji o tagu usługi (na przykład Pobierz wszystkie zakresy adresów dla magazynu w zachodnich), zobacz dokumentację [interfejsu API odnajdywania tagów usług](/powershell/module/az.network/Get-AzNetworkServiceTag) w programie PowerShell.
- Gdy nowe adresy IP zostaną dodane do tagów usługi, nie będą używane na platformie Azure przez co najmniej jeden tydzień. Dzięki temu można aktualizować wszystkie systemy, które mogą wymagać śledzenia adresów IP skojarzonych z tagami usług.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [utworzyć sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md).
