---
title: Strategie dostępu do danych
description: Azure Data Factory teraz obsługuje zakresy statycznych adresów IP.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 785381e0a42f2b502e4ea7054753d5f3fb67f385
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632774"
---
# <a name="data-access-strategies"></a>Strategie dostępu do danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Istotnym celem zabezpieczeń organizacji jest ochrona swoich magazynów danych przed przypadkowym dostępem przez Internet. może to być magazyn danych lokalnych lub w chmurze/SaaS. 

Zwykle magazyn danych w chmurze kontroluje dostęp przy użyciu poniższych mechanizmów:
* Prywatne łącze z Virtual Network do prywatnych punktów końcowych z włączonymi źródłami danych
* Reguły zapory ograniczające łączność przy użyciu adresu IP
* Mechanizmy uwierzytelniania, które wymagają, aby użytkownicy udowadniali swoją tożsamość
* Mechanizmy autoryzacji, które ograniczają użytkowników do określonych akcji i danych

> [!TIP]
> [Wprowadzając zakres statycznego adresu IP](./azure-integration-runtime-ip-addresses.md), możesz teraz zezwolić na listę zakresów adresów IP dla określonego regionu Azure Integration Runtime, aby upewnić się, że nie trzeba zezwalać na wszystkie adresy IP platformy Azure w magazynach danych w chmurze. W ten sposób można ograniczyć adresy IP, które mogą uzyskiwać dostęp do magazynów danych.

> [!NOTE] 
> Zakresy adresów IP są blokowane dla Azure Integration Runtime i są obecnie używane tylko w przypadku przenoszenia danych, potoku i działań zewnętrznych. Przepływy pracy i Azure Integration Runtime, które umożliwiają zarządzanie Virtual Network teraz nie używają tych zakresów adresów IP. 

Ta czynność powinna działać w wielu scenariuszach. wiemy, że unikatowy statyczny adres IP dla środowiska Integration Runtime jest pożądany, ale nie będzie można użyć Azure Integration Runtime obecnie, który jest bezserwerowy. W razie potrzeby można zawsze skonfigurować samodzielny Integration Runtime i korzystać z niego przy użyciu statycznego adresu IP. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Strategie dostępu do danych za poorednictwem Azure Data Factory

* **[Link prywatny](../private-link/private-link-overview.md)** — można utworzyć Azure Integration Runtime w ramach Azure Data Factory zarządzanym Virtual Network i wykorzystać prywatne punkty końcowe do bezpiecznego łączenia się z obsługiwanymi magazynami danych. Ruch między zarządzanymi Virtual Networkami i źródłami danych porusza się w sieci szkieletowej firmy Microsoft i nie jest narażony na sieć publiczną.
* **[Usługa zaufana](../storage/common/storage-network-security.md#exceptions)** — Magazyn Azure (Blob, ADLS Gen2) obsługuje konfigurację zapory, która umożliwia bezpieczne wybieranie zaufanych usług platformy Azure w celu bezpiecznego uzyskiwania dostępu do konta magazynu. Usługi zaufane wymuszają uwierzytelnianie tożsamości zarządzanej, co gwarantuje, że żadna inna Fabryka danych nie może nawiązać połączenia z tym magazynem, chyba że zostanie zatwierdzone do tego celu przy użyciu tożsamości zarządzanej. Więcej szczegółów można znaleźć w **[tym blogu](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** . W związku z tym jest to niezwykle bezpieczne i zalecane. 
* **Unikatowy statyczny adres IP** — należy skonfigurować własne środowisko Integration Runtime, aby uzyskać statyczny adres IP dla łączników Data Factory. Dzięki temu mechanizmowi można zablokować dostęp ze wszystkich innych adresów IP. 
* **[Statyczny zakres adresów IP](./azure-integration-runtime-ip-addresses.md)** — można użyć adresów IP Azure Integration Runtime, aby zezwolić na wyświetlanie listy w magazynie (wypowiedz S3, Salesforce itp.). Na pewno ogranicza adresy IP, które mogą łączyć się z magazynami danych, ale również opierają się na regułach uwierzytelniania/autoryzacji.
* **[Tag usługi](../virtual-network/service-tags-overview.md)** — tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure (np. Azure Data Factory). Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Jest to przydatne podczas filtrowania dostępu do danych w hostowanych magazynach danych IaaS w Virtual Network.
* **Zezwalaj na korzystanie z usług platformy Azure** — niektóre usługi umożliwiają nawiązywanie połączenia z usługą Azure w przypadku wybrania tej opcji. 

Aby uzyskać więcej informacji na temat obsługiwanych mechanizmów zabezpieczeń sieci w magazynach danych w Azure Integration Runtime i samoobsługowe Integration Runtime, zobacz poniżej dwóch tabel.  
* **Azure Integration Runtime**

    | Magazyny danych                  | Obsługiwany mechanizm zabezpieczeń sieci w magazynach danych | Private Link     | Usługa zaufana     | Statyczny zakres adresów IP | Tagi usługi | Zezwalaj na usługi platformy Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Magazyny danych Azure PaaS       | Azure Cosmos DB                                     | Tak              | -                   | Tak             | -            | Tak                  |
    |                              | Azure Data Explorer                                 | -                | -                   | Tak*            | Tak*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Tak             | -            | Tak                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | Tak             | -            | Tak                  |
    |                              | Azure File Storage                                  | Tak              | -                   | Tak             | -            | .                    |
    |                              | Azure Storage (BLOB, ADLS Gen2)                     | Tak              | Tak (tylko uwierzytelnianie MSI) | Tak             | -            | .                    |
    |                              | Azure SQL DB, Azure Synapse Analytics), SQL ml  | Tak (tylko usługa Azure SQL DB/DW)        | -                   | Tak             | -            | Tak                  |
    |                              | Azure Key Vault (na potrzeby pobierania kluczy tajnych/parametrów połączenia) | yes      | Tak                 | Tak             | -            | -                    |
    | Inne magazyny danych PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage itd.    | -                | -                   | Tak             | -            | -                    |
    | Azure laaS                   | SQL Server, Oracle itd.                          | -                | -                   | Tak             | Tak          | -                    |
    | Lokalna laaS              | SQL Server, Oracle itd.                          | -                | -                   | Tak             | -            | -                    |
    
    **Dotyczy tylko sytuacji, gdy usługa Azure Eksplorator danych jest wstrzykiwana przez sieć wirtualną i zakres adresów IP można zastosować do sieciowej grupy zabezpieczeń/zapory.* 

* **Samodzielna Integration Runtime (w sieciach wirtualnych i lokalnych)**
    
    | Magazyny danych                  | Obsługiwany mechanizm zabezpieczeń sieci w magazynach danych         | Statyczny adres IP | Usługi zaufane  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Magazyny danych Azure PaaS       | Azure Cosmos DB                                               | Tak       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Tak       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Tak       | -                   |
    |                                | Azure File Storage                                            | Tak       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Tak       | Tak (tylko uwierzytelnianie MSI) |
    |                                | Azure SQL DB, Azure Synapse Analytics), SQL ml          | Tak       | -                   |
    |                                | Azure Key Vault (na potrzeby pobierania kluczy tajnych/parametrów połączenia) | Tak       | Tak                 |
    | Inne magazyny danych PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage itd.              | Tak       | -                   |
    | Azure laaS                     | SQL Server, Oracle itd.                                  | Tak       | -                   |
    | Lokalna laaS              | SQL Server, Oracle itd.                                  | Tak       | -                   |    

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły pokrewne:
* [Obsługiwane magazyny danych](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Azure Key Vault "zaufanych usług"](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Usługa Azure Storage "zaufane usługi firmy Microsoft"](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Tożsamość zarządzana dla usługi Data Factory](./data-factory-service-identity.md)