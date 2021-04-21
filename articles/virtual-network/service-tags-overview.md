---
title: Omówienie tagów usług platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o tagach usługi. Tagi usługi pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/14/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4a18e0f257ac8d7b7d981d53249cf29f2f8f38bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790153"
---
# <a name="virtual-network-service-tags"></a>Tagi usługi dla sieci wirtualnej
<a name="network-service-tags"></a>

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieci.

Tagi usług mogą być używane do definiowania kontroli dostępu do sieci w [sieciowych grupach zabezpieczeń](./network-security-groups-overview.md#security-rules) [lub Azure Firewall](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń użyj tagów usługi w miejsce określonych adresów IP. Określając nazwę tagu usługi, taką jak **ApiManagement,**  w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.  

> [!NOTE] 
> Od marca 2021 r. można również używać tagów usługi w miejsce jawnych zakresów adresów IP w trasach [zdefiniowanych przez użytkownika.](./virtual-networks-udr-overview.md) Ta funkcja jest obecnie w publicznej wersji zapoznawczej. 

Tagi usług mogą być używane do osiągnięcia izolacji sieci i ochrony zasobów platformy Azure przed ogólnym Internetem podczas uzyskiwania dostępu do usług platformy Azure, które mają publiczne punkty końcowe. Utwórz reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego/wychodzącego, aby odrzucać [](#available-service-tags) ruch do/z Internetu i zezwalać na ruch do/z usługi **AzureCloud** lub innych tagów dostępnych usług określonych usług platformy Azure. 

![Izolacja sieciowa usług platformy Azure przy użyciu tagów usług](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Dostępne tagi usługi
W poniższej tabeli przedstawiono wszystkie tagi usługi dostępne do użycia w zasadach [sieciowej grupy](./network-security-groups-overview.md#security-rules) zabezpieczeń.

Kolumny wskazują, czy tag:

- Jest odpowiednia dla reguł, które obejmują ruch przychodzący lub wychodzący.
- Obsługuje [zakres](https://azure.microsoft.com/regions) regionalny.
- Można go używać w [Azure Firewall](../firewall/service-tags.md) reguł.

Domyślnie tagi usługi odzwierciedlają zakresy dla całej chmury. Niektóre tagi usługi umożliwiają również bardziej szczegółową kontrolę przez ograniczenie odpowiednich zakresów adresów IP do określonego regionu. Na przykład tag usługi **Storage** reprezentuje usługę Azure Storage dla całej chmury, ale **storage.WestUS** zawęża zakres tylko do zakresów adresów IP magazynu z regionu WestUS. W poniższej tabeli przedstawiono, czy każdy tag usługi obsługuje taki zakres regionalny.  

| Tag | Przeznaczenie | Czy można używać ruchu przychodzącego lub wychodzącego? | Może być regionem? | Czy można używać z Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Grupa akcji. | Przychodzący | Nie | Nie |
| **ApiManagement** | Ruch zarządzania w przypadku API Management platformy Azure dedykowanych wdrożeń. <br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi Azure API Management dla płaszczyzny sterowania na region. Dzięki temu klienci mogą wykonywać operacje zarządzania na interfejsach API, operacjach, zasadach i wartościach nazwanych skonfigurowanych w API Management usługi.  | Przychodzący | Tak | Tak |
| **ApplicationInsightsAvailability** | Application Insights dostępności. | Przychodzący | Nie | Nie |
| **AppConfiguration** | App Configuration. | Wychodzący | Nie | Nie |
| **AppService**    | Azure App Service. Ten tag jest zalecany w przypadku reguł zabezpieczeń dla ruchu wychodzącego do aplikacji internetowych i aplikacji funkcji.  | Wychodzący | Tak | Tak |
| **AppServiceManagement** | Ruch zarządzania dla wdrożeń dedykowanych dla App Service Environment. | Oba | Nie | Tak |
| **AzureActiveDirectory** | Azure Active Directory. | Wychodzący | Nie | Tak |
| **AzureActiveDirectoryDomainServices** | Ruch zarządzania dla wdrożeń dedykowanych dla Azure Active Directory Domain Services. | Oba | Nie | Tak |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Wychodzący | Nie | Nie |
| **AzureAPIForFHIR** | Azure API for FHIR (projekt standardu Fast Healthcare Interoperability Resources).<br/><br/> *Uwaga: tego tagu nie można obecnie konfigurować za pośrednictwem witryny Azure Portal.*| Wychodzący | Nie | Nie |
| **AzureArcInfrastructure** | Azure Arc z włączonymi serwerami, Azure Arc kubernetes i ruch konfiguracji gościa.<br/><br/>*Uwaga:* Ten tag jest zależny od tagów **AzureActiveDirectory,****AzureTrafficManager** **i AzureResourceManager.** *Tego tagu nie można obecnie konfigurować za pośrednictwem witryny Azure Portal.*| Wychodzący | Nie | Tak |
| **AzureBackup** |Azure Backup.<br/><br/>*Uwaga:* Ten tag jest zależny od **tagów Storage** i **AzureActiveDirectory.** | Wychodzący | Nie | Tak |
| **AzureBotService** | Azure Bot Service. | Wychodzący | Nie | Nie |
| **AzureCloud** | Wszystkie [publiczne adresy IP centrum danych.](https://www.microsoft.com/download/details.aspx?id=56519) | Wychodzący | Tak | Tak |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Ten tag lub adresy IP objęte tym tagiem mogą służyć do udzielania indeksatorom bezpiecznego dostępu do źródeł danych. Aby uzyskać więcej informacji, [zapoznaj](../search/search-indexer-troubleshooting.md#connection-errors) się z dokumentacją połączenia indeksatora. <br/><br/> *Uwaga:* adres IP usługi wyszukiwania nie znajduje się na liście zakresów  adresów IP dla tego tagu usługi i należy go również dodać do zapory ADRESÓW IP źródeł danych. | Przychodzący | Nie | Nie |
| **AzureConnectors** | Ten tag reprezentuje adresy IP używane przez łączniki zarządzane, które wywołują wywołania zwrotne przychodzącego modułu webhook do usługi Azure Logic Apps i wychodzące wywołania do odpowiednich usług, na przykład usługi Azure Storage lub Azure Event Hubs. | Ruch przychodzący/wychodzący | Tak | Tak |
| **AzureContainerRegistry** | Azure Container Registry. | Wychodzący | Tak | Tak |
| **AzureCosmosDB** | Azure Cosmos DB. | Wychodzący | Tak | Tak |
| **AzureDatabricks** | Azure Databricks. | Oba | Nie | Nie |
| **AzureDataExplorerManagement** | Azure Data Explorer zarządzania. | Przychodzący | Nie | Nie |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Wychodzący | Nie | Tak |
| **AzureDevSpaces** | Azure Dev Spaces. | Wychodzący | Nie | Nie |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>*Uwaga: tego tagu nie można obecnie konfigurować za pośrednictwem witryny Azure Portal*| Przychodzący | Nie | Tak |
| **AzureDigitalTwins** | Azure Digital Twins.<br/><br/>*Uwaga:* Ten tag lub adresy IP objęte tym tagiem mogą służyć do ograniczania dostępu do punktów końcowych skonfigurowanych dla tras zdarzeń. *Tego tagu nie można obecnie konfigurować za pośrednictwem witryny Azure Portal* | Przychodzący | Nie | Tak |
| **AzureEventGrid** | Azure Event Grid. | Oba | Nie | Nie |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door. | Oba | Nie | Nie |
| **Moduł AzureInformationProtection** | Azure Information Protection.<br/><br/>*Uwaga:* Ten tag jest zależny od tagów **AzureActiveDirectory,** **AzureFrontDoor.Frontend** i **AzureFrontDoor.FirstParty.** | Wychodzący | Nie | Nie |
| **AzureIoTHub** | Azure IoT Hub. | Wychodzący | Nie | Nie |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Uwaga:* Ten tag jest zależny od **tagu AzureActiveDirectory.** | Wychodzący | Tak | Tak |
| **AzureLoadBalancer** | Usługa równoważenia obciążenia infrastruktury platformy Azure. Ten tag przekłada się na [wirtualny adres IP hosta](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16), z którego pochodzą sondy kondycji platformy Azure. Obejmuje to tylko ruch sondy, a nie rzeczywisty ruch do zasobu zaplecza. Jeśli nie używasz tej Azure Load Balancer, możesz przesłonić tę regułę. | Oba | Nie | Nie |
| **AzureMachineLearning** | Azure Machine Learning. | Oba | Nie | Tak |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon i metryki niestandardowe (punkty końcowe GiG).<br/><br/>*Uwaga:* W przypadku usługi Log Analytics **wymagany** jest również tag Magazynu. Jeśli są używane agenci systemu Linux, wymagany jest również tag **GuestAndHybridManagement.** | Wychodzący | Nie | Tak |
| **AzureOpenDatasets** | Azure Open Datasets.<br/><br/>*Uwaga:* Ten tag jest zależny od **tagów AzureFrontDoor.Frontend** **i Storage.** | Wychodzący | Nie | Nie |
| **AzurePlatformDNS** | Podstawowa infrastruktura (domyślna) usługa DNS.<br/><br>Możesz użyć tego tagu, aby wyłączyć domyślny system DNS. Należy zachować ostrożność podczas korzystania z tego tagu. Zalecamy przeczytanie [tematu Zagadnienia dotyczące platformy Azure.](./network-security-groups-overview.md#azure-platform-considerations) Zalecamy również przeprowadzenie testowania przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), która jest podstawową usługą infrastruktury.<br/><br/>Możesz użyć tego tagu, aby wyłączyć domyślne ustawienia IMDS. Należy zachować ostrożność podczas korzystania z tego tagu. Zalecamy przeczytanie [tematu Zagadnienia dotyczące platformy Azure.](./network-security-groups-overview.md#azure-platform-considerations) Zalecamy również przeprowadzenie testowania przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformLKM** | Licencjonowanie systemu Windows lub usługa zarządzania kluczami.<br/><br/>Możesz użyć tego tagu, aby wyłączyć wartości domyślne licencjonowania. Należy zachować ostrożność podczas korzystania z tego tagu. Zalecamy zapoznanie się z [tematem Zagadnienia dotyczące platformy Azure.](./network-security-groups-overview.md#azure-platform-considerations)  Zalecamy również przeprowadzenie testowania przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzureResourceManager** | W usłudze Azure Resource Manager. | Wychodzący | Nie | Nie |
| **AzureSignalR** | Azure SignalR. | Wychodzący | Nie | Nie |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Uwaga:* Ten tag jest zależny od tagów **AzureActiveDirectory,** **AzureKeyVault,** **EventHub,****GuestAndHybridManagement** **i Storage.** | Wychodzący | Nie | Nie |
| **AzureTrafficManager** | Azure Traffic Manager adresów IP sondy.<br/><br/>Aby uzyskać więcej informacji na Traffic Manager IP sondy, [zobacz Azure Traffic Manager — często zadawane pytania.](../traffic-manager/traffic-manager-faqs.md) | Przychodzący | Nie | Tak |  
| **BatchNodeManagement** | Ruch zarządzania dla wdrożeń dedykowanych dla Azure Batch. | Oba | Nie | Tak |
| **CognitiveServicesManagement** | Zakresy adresów dla ruchu dla Azure Cognitive Services. | Oba | Nie | Nie |
| **DataFactory**  | Azure Data Factory | Oba | Nie | Nie |
| **DataFactoryManagement** | Ruch zarządzania dla Azure Data Factory. | Wychodzący | Nie | Nie |
| **Dynamics365ForMarketingEmail** | Zakresy adresów dla marketingowej usługi poczty e-mail usługi Dynamics 365. | Wychodzący | Tak | Nie |
| **EventHub** | Azure Event Hubs. | Wychodzący | Tak | Tak |
| **GatewayManager** | Ruch zarządzania dla wdrożeń dedykowanych dla usług Azure VPN Gateway i Application Gateway. | Przychodzący | Nie | Nie |
| **GuestAndHybridManagement** | Azure Automation i konfiguracja gościa. | Wychodzący | Nie | Tak |
| **HDInsight** | Azure HDInsight. | Przychodzący | Tak | Nie |
| **Internet** | Przestrzeń adresowa IP, która znajduje się poza siecią wirtualną i jest osiągalna przez publiczny Internet.<br/><br/>Zakres adresów obejmuje publiczną przestrzeń adresów [IP należącą do platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653) | Oba | Nie | Nie |
| **LogicApps** | Logic Apps. | Oba | Nie | Nie |
| **LogicAppsManagement** | Ruch zarządzania dla Logic Apps. | Przychodzący | Nie | Nie |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Wychodzący | Nie | Nie |
| **MicrosoftContainerRegistry** | Rejestr kontenerów dla obrazów kontenerów firmy Microsoft. <br/><br/>*Uwaga:* Ten tag jest zależny od **tagu AzureFrontDoor.FirstParty.** | Wychodzący | Tak | Tak |
| **PowerBI** | PowerBi. *Uwaga: tego tagu nie można obecnie konfigurować za pośrednictwem witryny Azure Portal.* | Oba | Nie | Nie|
| **PowerQueryOnline** | Power Query Online. | Oba | Nie | Nie |
| **ServiceBus** | Azure Service Bus ruchu sieciowego, który korzysta z warstwy usługi Premium. | Wychodzący | Tak | Tak |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy Service Fabric dla płaszczyzny sterowania na region. Dzięki temu klienci mogą wykonywać operacje zarządzania dla swoich klastrów Service Fabric z sieci wirtualnej (punktu końcowego, np. https:// westus.servicefabric.azure.com) | Oba | Nie | Nie |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure Synapse Analytics.<br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. Ten tag nie ma zastosowania do wystąpienia zarządzanego SQL. | Wychodzący | Tak | Tak |
| **SqlManagement** | Ruch zarządzania dla wdrożeń dedykowanych w języku SQL. | Oba | Nie | Tak |
| **Storage** | Azure Storage. <br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. | Wychodzący | Tak | Tak |
| **StorageSyncService** | Usługa synchronizacji magazynu. | Oba | Nie | Nie |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Oba | Nie | Tak |
| **VirtualNetwork** | Przestrzeń adresowa sieci wirtualnej (wszystkie zakresy adresów IP zdefiniowane dla sieci [](virtual-network-peering-overview.md) wirtualnej), wszystkie połączone lokalne przestrzenie adresowe, równorzędne sieci wirtualne, sieci wirtualne połączone z bramą sieci wirtualnej, [](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)wirtualny adres IP [hosta](./network-security-groups-overview.md#azure-platform-considerations)i prefiksy adresów używane w trasach zdefiniowanych przez [użytkownika.](virtual-networks-udr-overview.md) Ten tag może również zawierać trasy domyślne. | Oba | Nie | Nie |

>[!NOTE]
>W klasycznym modelu wdrażania (przed Azure Resource Manager) obsługiwany jest podzbiór tagów wymienionych w poprzedniej tabeli. Te tagi są pisane inaczej:
>
>| Pisownia klasyczna | Równoważny Resource Manager tagu |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tagi usług platformy Azure oznaczają prefiksy adresów z określonej używanej chmury. Na przykład podstawowe zakresy adresów IP, które odpowiadają wartości tagu **SQL** w chmurze publicznej Platformy Azure, będą różnić się od podstawowych zakresów w chmurze Azure w Chinach.

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje [trasę](virtual-networks-udr-overview.md#optional-default-routes) do podsieci sieci wirtualnej dla usługi. Prefiksy adresów w trasie są tymi samymi prefiksami adresów lub zakresami CIDR co prefiksy odpowiedniego tagu usługi.

## <a name="service-tags-on-premises"></a>Tagi usług w środowisku lokalnym  
Możesz uzyskać bieżący tag usługi i informacje o zakresie, które mają zostać zawarte w ramach konfiguracji zapory lokalnej. Te informacje to bieżąca lista zakresów adresów IP odpowiadających każdemu tagowi usługi. Informacje można uzyskać programowo lub za pośrednictwem pobierania pliku JSON, zgodnie z opisem w poniższych sekcjach.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Korzystanie z interfejsu API odnajdywania tagów usługi (publiczna wersja zapoznawcza)
Możesz programowo pobrać bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/network#az_network_list_service_tags)

> [!NOTE]
> Propagacja nowych danych tagu usługi w wynikach interfejsu API trwa do 4 tygodni. W takim przypadku numer zmiany w metadanych odpowiedzi będzie zwiększany. W przypadku określonej innej lokalizacji mogą wystąpić tymczasowe różnice w wynikach. Korzystając z wyników w celu utworzenia reguł sieciowej grupy bezpieczeństwa, należy ustawić paramater lokalizacji, aby był zgodne z regionem sieciowej grupy bezpieczeństwa. 

> [!NOTE]
> Dane interfejsu API będą reprezentować te tagi, które mogą być używane z regułami sieciowej grupy danych, podzbiorem tagów aktualnie w pliku JSON do pobrania. W publicznej wersji zapoznawczej nie gwarantujemy, że dane pozostaną takie same od jednej aktualizacji do następnej. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Odnajdywanie tagów usługi przy użyciu plików JSON do pobrania 
Możesz pobrać pliki JSON zawierające bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP. Te listy są aktualizowane i publikowane co tydzień. Lokalizacje dla każdej chmury to:

- [Publiczna platforma Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Chiny na platformie Azure](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)   

Zakresy adresów IP w tych plikach są w notacji CIDR. 

> [!NOTE]
>Podzestaw tych informacji został opublikowany w plikach XML dla publicznej platformy [Azure,](https://www.microsoft.com/download/details.aspx?id=41653) [chińskiej](https://www.microsoft.com/download/details.aspx?id=42064)wersji platformy Azure i [platformy Azure (Niemcy).](https://www.microsoft.com/download/details.aspx?id=54770) Te pliki do pobrania XML zostaną wycofane do 30 czerwca 2020 r. i nie będą już dostępne po tej dacie. Należy przeprowadzić migrację do programu przy użyciu interfejsu API odnajdywania lub plików JSON do pobrania zgodnie z opisem w poprzednich sekcjach.

### <a name="tips"></a>Porady 
- Aktualizacje z jednej publikacji do następnej można wykryć, notując zwiększone wartości *changeNumber* w pliku JSON. Każda podsekcja (na przykład **Storage.WestUS**) ma własną *wartość changeNumber,* która jest zwiększana w przypadku wystąpienia zmian. Najwyższy poziom wartości *changeNumber* pliku jest zwiększany, gdy dowolna z podsekcji zostanie zmieniona.
- Przykłady sposobu analizowania informacji o tagu usługi (na przykład uzyskania wszystkich zakresów adresów dla usługi Storage w westUS) można znaleźć w dokumentacji interfejsu API odnajdywania tagów usług w [programie PowerShell.](/powershell/module/az.network/Get-AzNetworkServiceTag)
- Po dodaniu nowych adresów IP do tagów usługi nie będą one używane na platformie Azure przez co najmniej tydzień. Dzięki temu można zaktualizować wszystkie systemy, które mogą wymagać śledzenia adresów IP skojarzonych z tagami usług.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, [jak utworzyć sieciową grupę zabezpieczeń.](tutorial-filter-network-traffic.md)
