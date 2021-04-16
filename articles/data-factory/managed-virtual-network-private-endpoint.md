---
title: Zarządzana sieć wirtualna & zarządzane prywatne punkty końcowe
description: Dowiedz się więcej o zarządzanej sieci wirtualnej i zarządzanych prywatnych punktach końcowych w Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 1213d5f7421cc71255f29d013fa47878559110ee
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481586"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory Managed Virtual Network (wersja zapoznawcza)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule wyjaśniono zarządzane Virtual Network prywatne punkty końcowe w Azure Data Factory.


## <a name="managed-virtual-network"></a>Zarządzana sieć wirtualna

Podczas tworzenia środowiska Azure Integration Runtime (IR) w programie Azure Data Factory Managed Virtual Network (VNET) środowisko Integration Runtime zostanie aprowizowane za pomocą zarządzanego środowiska Virtual Network i będzie korzystać z prywatnych punktów końcowych do bezpiecznego łączenia się z obsługiwanymi magazynami danych. 

Utworzenie Azure IR w ramach zarządzanego Virtual Network gwarantuje, że proces integracji danych jest izolowany i bezpieczny. 

Zalety korzystania z usługi Managed Virtual Network:

- Dzięki zarządzanej Virtual Network można odciążyć zarządzanie Virtual Network, aby Azure Data Factory. Nie musisz tworzyć podsieci dla usługi Azure Integration Runtime, która może ostatecznie korzystać z wielu prywatnych ip z twojego Virtual Network i wymagałaby wcześniejszego planowania infrastruktury sieciowej. 
- Do bezpiecznego integrowania danych nie jest wymagana głęboka wiedza na temat sieci platformy Azure. Zamiast tego rozpoczęcie pracy z bezpiecznym procesem ETL jest znacznie uproszczone dla inżynierów danych. 
- Zarządzane Virtual Network wraz z zarządzanymi prywatnymi punktami końcowymi chronią przed eksfiltracją danych. 

> [!IMPORTANT]
>Obecnie zarządzana sieć wirtualna jest obsługiwana tylko w tym samym regionie co Azure Data Factory wirtualnej.
 

![Architektura zarządzanej usługi ADF Virtual Network ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Zarządzane prywatne punkty końcowe

Zarządzane prywatne punkty końcowe to prywatne punkty końcowe utworzone w usłudze Azure Data Factory Managed Virtual Network ustanowienie prywatnego połączenia z zasobami platformy Azure. Usługa Azure Data Factory zarządza tymi prywatnymi punktami końcowymi w Twoim imieniu. 

![Nowy zarządzany prywatny punkt końcowy](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory obsługuje linki prywatne. Usługa Private Link umożliwia dostęp do usług platformy Azure (PaaS), takich jak Azure Storage, Azure Cosmos DB, Azure Synapse Analytics).

W przypadku korzystania z łącza prywatnego ruch między magazynami danych i zarządzanymi Virtual Network odbywa się całkowicie za pośrednictwem sieci szkieletowej firmy Microsoft. Private Link chroni przed zagrożeniami eksfiltracji danych. Połączenie prywatne z zasobem jest ustanawiane przez utworzenie prywatnego punktu końcowego.

Prywatny punkt końcowy używa prywatnego adresu IP w zarządzanym Virtual Network, aby skutecznie wprowadzić do niego usługę. Prywatne punkty końcowe są mapowane na określony zasób na platformie Azure, a nie na całą usługę. Klienci mogą ograniczyć łączność do określonego zasobu zatwierdzonego przez ich organizację. Dowiedz się więcej na [temat linków prywatnych i prywatnych punktów końcowych.](../private-link/index.yml)

> [!NOTE]
> Zaleca się utworzenie zarządzanych prywatnych punktów końcowych w celu nawiązania połączenia ze wszystkimi źródłami danych platformy Azure. 
 
> [!WARNING]
> Jeśli magazyn danych PaaS (Blob, ADLS Gen2, Azure Synapse Analytics) ma już utworzony prywatny punkt końcowy, a nawet jeśli zezwala na dostęp ze wszystkich sieci, usługa ADF będzie mogła uzyskać do niego dostęp tylko przy użyciu zarządzanego prywatnego punktu końcowego. Upewnij się, że w takich scenariuszach tworzysz prywatny punkt końcowy. 

Połączenie prywatnego punktu końcowego jest tworzone w stanie "Oczekiwanie" podczas tworzenia zarządzanego prywatnego punktu końcowego w Azure Data Factory. Inicjowany jest przepływ pracy zatwierdzania. Właściciel zasobu łącza prywatnego jest odpowiedzialny za zatwierdzenie lub odrzucenie połączenia.

![Zarządzanie prywatnym punktem końcowym](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Jeśli właściciel zatwierdzi połączenie, zostanie nawiązane połączenie prywatne. W przeciwnym razie łącze prywatne nie zostanie ustanowione. W obu przypadkach zarządzany prywatny punkt końcowy zostanie zaktualizowany o stan połączenia.

![Zatwierdzanie zarządzanego prywatnego punktu końcowego](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Tylko zarządzany prywatny punkt końcowy w stanie zatwierdzonym może wysyłać ruch do danego zasobu łącza prywatnego.

## <a name="interactive-authoring"></a>Tworzenie interakcyjne
Funkcje tworzenia interakcyjnego są używane dla funkcji, takich jak połączenie testowe, przeglądanie listy folderów i listy tabel, uzyskiwanie schematu i wyświetlanie podglądu danych. Tworzenie interakcyjne można włączyć podczas tworzenia lub edytowania Azure Integration Runtime, która znajduje się w sieci wirtualnej zarządzanej przez usługę ADF. Usługa zaplecza wstępnie przydzieli zasoby obliczeniowe dla funkcji tworzenia interakcyjnego. W przeciwnym razie obliczenia będą przydzielane za każdym razem, gdy zostanie wykonana dowolna interaktywna operacja, co zajmie więcej czasu. Czas wygaśnięcia (TTL) dla tworzenia interaktywnego wynosi 60 minut, co oznacza, że zostanie on automatycznie wyłączony po 60 minutach od ostatniej operacji tworzenia interaktywnego.

![Tworzenie interakcyjne](./media/managed-vnet/interactive-authoring.png)

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Tworzenie zarządzanej sieci wirtualnej za pośrednictwem Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}"

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
### <a name="supported-data-sources"></a>Obsługiwane źródła danych
Poniższe źródła danych są obsługiwane w celu nawiązywania połączeń za pośrednictwem łącza prywatnego z usługi ADF Managed Virtual Network.
- Azure Blob Storage (bez konta magazynu w wersji 1)
- Azure Table Storage (bez konta magazynu w wersji 1)
- Azure Files (bez konta magazynu w wersji 1)
- Azure Data Lake Gen2
- Azure SQL Database (bez uwzględnienia Azure SQL Managed Instance)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private Link Service
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory zarządzane Virtual Network są dostępne w następujących regionach świadczenia usługi Azure:
- East US
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- South Central US
- Central US
- Europa Północna
- West Europe
- Południowe Zjednoczone Królestwo
- Southeast Asia
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Holandia Wschodnia
- Japonia Wschodnia
- Japonia Zachodnia
- Korea Środkowa
- Brazylia Południowa
- Francja Środkowa
- Korea Północna
- Zachodnie Zjednoczone Królestwo
- Kanada Wschodnia
- Kanada Środkowa

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Komunikacja wychodząca za pośrednictwem publicznego punktu końcowego z usługi ADF Managed Virtual Network
- Tylko port 443 jest otwarty dla komunikacji wychodzącej.
- W przypadku usług Azure Storage i Azure Data Lake Gen2 nie jest obsługiwane połączenie za pośrednictwem publicznego punktu końcowego z sieci wirtualnej zarządzanej przez usługę ADF.

### <a name="linked-service-creation-of-azure-key-vault"></a>Tworzenie połączonej usługi Azure Key Vault 
- W przypadku tworzenia połączonej usługi dla usługi Azure Key Vault nie można odwołać się do środowiska Azure Integration Runtime. Dlatego nie można utworzyć prywatnego punktu końcowego podczas tworzenia usługi połączonej Azure Key Vault. Jednak po utworzeniu połączonej usługi dla magazynów danych, która odwołuje się do połączonej usługi usługi Azure Key Vault i ta połączona usługa odwołuje się do usługi Azure Integration Runtime z włączoną usługą Managed Virtual Network, podczas tworzenia można utworzyć prywatny punkt końcowy dla połączonej usługi Azure Key Vault. 
- **Test connection** dla połączonej usługi usługi Azure Key Vault tylko weryfikuje format adresu URL, ale nie robi żadnych operacji sieciowych.
- Kolumna Korzystanie **z prywatnego punktu końcowego** jest zawsze wyświetlana jako pusta, nawet jeśli tworzysz prywatny punkt końcowy dla Azure Key Vault.
![Prywatny punkt końcowy usługi AKV](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Następne kroki

- Samouczek: [tworzenie potoku kopiowania przy użyciu zarządzanych Virtual Network prywatnych punktów końcowych](tutorial-copy-data-portal-private.md) 
- Samouczek: [kompilowanie potoku przepływu danych mapowania przy użyciu Virtual Network i prywatnych punktów końcowych](tutorial-data-flow-private.md)