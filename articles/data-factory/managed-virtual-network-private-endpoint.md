---
title: Zarządzana Sieć wirtualna & zarządzanymi prywatnymi punktami końcowymi
description: Informacje na temat zarządzanej sieci wirtualnej i zarządzanych prywatnych punktów końcowych w Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: a34f2e50a5eb4ab87a02a5128cb912fa220849f1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317069"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory Managed Virtual Network (wersja zapoznawcza)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano zarządzane Virtual Network i zarządzane prywatne punkty końcowe w programie Azure Data Factory.


## <a name="managed-virtual-network"></a>Zarządzana Sieć wirtualna

Podczas tworzenia Azure Integration Runtime (IR) w Azure Data Factory zarządzanym Virtual Network (VNET) środowisko Integration Runtime zostanie udostępnione przy użyciu zarządzanego Virtual Network i będzie korzystać z prywatnych punktów końcowych w celu bezpiecznego łączenia się z obsługiwanymi magazynami danych. 

Utworzenie Azure IR w ramach zarządzanego Virtual Network gwarantuje, że proces integracji danych jest izolowany i bezpieczny. 

Zalety korzystania z Virtual Network zarządzanych:

- Za pomocą zarządzanego Virtual Network można odciążyć obciążenie zarządzaniem Virtual Network do Azure Data Factory. Nie musisz tworzyć podsieci dla Azure Integration Runtime, która może ostatecznie korzystać z wielu prywatnych adresów IP z Virtual Network i wymagać wcześniejszego zaplanowania infrastruktury sieciowej. 
- Nie wymaga to szczegółowej wiedzy o sieci platformy Azure w celu bezpiecznego zapewnienia integracji danych. Zamiast tego Rozpoczynanie pracy z bezpiecznym ETL jest znacznie uproszczone dla inżynierów danych. 
- Zarządzane Virtual Network wraz z zarządzanymi prywatnymi punktami końcowymi chronią przed eksfiltracji danych. 

> [!IMPORTANT]
>Obecnie zarządzana Sieć wirtualna jest obsługiwana tylko w tym samym regionie co Azure Data Factory region.
 

![Architektura Virtual Network zarządzana przez podajnik APD](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Zarządzane prywatne punkty końcowe

Zarządzane prywatne punkty końcowe są prywatnymi punktami końcowymi utworzonymi w Azure Data Factory zarządzanym Virtual Network ustanawiania prywatnego linku do zasobów platformy Azure. Azure Data Factory zarządza tymi prywatnymi punktami końcowymi w Twoim imieniu. 

![Nowy zarządzany prywatny punkt końcowy](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory obsługuje linki prywatne. Link prywatny umożliwia dostęp do usług platformy Azure (PaaS) (takich jak Azure Storage, Azure Cosmos DB, Azure Synapse Analytics (dawniej SQL Data Warehouse)).

W przypadku korzystania z prywatnego linku ruch między magazynami danych i zarządzanym Virtual Network przechodzi całkowicie za pośrednictwem sieci szkieletowej firmy Microsoft. Prywatne łącze chroniące przed ryzykiem eksfiltracji danych. Aby utworzyć prywatny link do zasobu, można utworzyć prywatny punkt końcowy.

Prywatny punkt końcowy używa prywatnego adresu IP w zarządzanym Virtual Network w celu efektywnego przełączenia usługi do niej. Prywatne punkty końcowe są mapowane na określony zasób na platformie Azure, a nie całej usługi. Klienci mogą ograniczyć łączność z określonym zasobem zatwierdzonym przez organizację. Dowiedz się więcej na temat [linków prywatnych i prywatnych punktów końcowych](../private-link/index.yml).

> [!NOTE]
> Zaleca się utworzenie zarządzanych prywatnych punktów końcowych, aby połączyć się ze wszystkimi źródłami danych platformy Azure. 
 
> [!WARNING]
> Jeśli magazyn danych PaaS (BLOB, ADLS Gen2, Azure Synapse Analytics) ma już utworzony prywatny punkt końcowy, a nawet jeśli zezwala na dostęp ze wszystkich sieci, funkcja ADF będzie mogła uzyskać do niego dostęp przy użyciu zarządzanego prywatnego punktu końcowego. Upewnij się, że tworzysz prywatny punkt końcowy w takich scenariuszach. 

Połączenie prywatnego punktu końcowego jest tworzone w stanie "oczekiwanie" podczas tworzenia zarządzanego prywatnego punktu końcowego w Azure Data Factory. Zainicjowano przepływ pracy zatwierdzania. Właściciel zasobu link prywatny jest odpowiedzialny za zaakceptowanie lub odrzucenie połączenia.

![Zarządzanie prywatnym punktem końcowym](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Jeśli właściciel zatwierdzi połączenie, zostanie nawiązane łącze prywatne. W przeciwnym razie połączenie prywatne nie zostanie nawiązane. W obu przypadkach zarządzany prywatny punkt końcowy zostanie zaktualizowany przy użyciu stanu połączenia.

![Zatwierdzanie zarządzanego prywatnego punktu końcowego](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Tylko zarządzany prywatny punkt końcowy w zatwierdzonym stanie może wysyłać ruch do podanego zasobu linku prywatnego.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy
### <a name="supported-data-sources"></a>Obsługiwane źródła danych
Poniższe źródła danych umożliwiają łączenie się za pośrednictwem prywatnego linku z Virtual Network zarządzanych przez usługi ADF.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (nie obejmuje wystąpienia zarządzanego usługi Azure SQL)
- Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)
- Azure CosmosDB — SQL
- W usłudze Azure Key Vault
- Usługa linków prywatnych platformy Azure
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory zarządzane Virtual Network jest dostępny w następujących regionach platformy Azure:
- East US
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- South Central US
- Central US
- Europa Północna
- Europa Zachodnia
- Południowe Zjednoczone Królestwo
- Southeast Asia
- Australia Wschodnia
- Australia Południowo-Wschodnia

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Komunikacja wychodząca za pośrednictwem publicznego punktu końcowego z zarządzanych Virtual Network APD
- Tylko port 443 jest otwarty dla komunikacji wychodzącej.
- Usługi Azure Storage i Azure Data Lake Gen2 nie są obsługiwane przez publiczny punkt końcowy z Virtual Network zarządzanych przez usługę ADF.

### <a name="linked-service-creation-of-azure-key-vault"></a>Tworzenie połączonej usługi Azure Key Vault 
- Podczas tworzenia połączonej usługi dla Azure Key Vault nie ma Azure Integration Runtime odwołanie. Nie można więc utworzyć prywatnego punktu końcowego podczas tworzenia połączonej usługi Azure Key Vault. Jednak podczas tworzenia połączonej usługi dla magazynów danych, które odwołują się do Azure Key Vault połączonej usługi, a ta połączona usługa odwołuje się Azure Integration Runtime z włączonym Virtual Network zarządzanym, można utworzyć prywatny punkt końcowy dla Azure Key Vault połączonej usługi podczas tworzenia. 
- Operacja **Test connection** dla połączonej usługi Azure Key Vault tylko weryfikuje format adresu URL, ale nie wykonuje żadnych operacji sieciowych.

## <a name="next-steps"></a>Następne kroki

- Samouczek: [Tworzenie potoku kopiowania przy użyciu zarządzanych Virtual Network i prywatnych punktów końcowych](tutorial-copy-data-portal-private.md) 
- Samouczek: [Kompiluj mapowanie przepływu danych potoku przy użyciu zarządzanych Virtual Network i prywatnych punktów końcowych](tutorial-data-flow-private.md)