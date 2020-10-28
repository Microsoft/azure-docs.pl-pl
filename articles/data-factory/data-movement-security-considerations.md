---
title: Zagadnienia dotyczące bezpieczeństwa
description: Opisuje podstawową infrastrukturę zabezpieczeń, za pomocą której usługi przenoszenia danych w Azure Data Factory używać w celu zabezpieczania danych.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 7669b49735843bf941c52aee5cc3b71d1644c01a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635817"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
>
> * [Wersja 1](v1/data-factory-data-movement-security-considerations.md)
> * [Bieżąca wersja](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano podstawową infrastrukturę zabezpieczeń, za pomocą której usługi przenoszenia danych w Azure Data Factory mogą pomóc w zabezpieczeniu danych. Zasoby zarządzania Data Factory są oparte na infrastrukturze zabezpieczeń platformy Azure i wykorzystują wszystkie możliwe środki bezpieczeństwa oferowane przez platformę Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](concepts-pipelines-activities.md) danych. Potok jest logicznym grupowaniem działań, które wspólnie wykonują zadanie. Te potoki znajdują się w regionie, w którym została utworzona Fabryka danych. 

Mimo że Data Factory jest dostępna tylko w kilku regionach, Usługa przenoszenia danych jest [dostępna globalnie](concepts-integration-runtime.md#integration-runtime-location) w celu zapewnienia zgodności danych, wydajności i obniżenia kosztów ruchu wychodzącego w sieci. 

Azure Data Factory nie przechowuje żadnych danych z wyjątkiem poświadczeń połączonej usługi dla magazynów danych w chmurze, które są szyfrowane przy użyciu certyfikatów. Program Data Factory umożliwia tworzenie przepływów pracy opartych na danych w celu organizowania przenoszenia danych między [obsługiwanymi magazynami danych](copy-activity-overview.md#supported-data-stores-and-formats)oraz przetwarzania danych przy użyciu [usług obliczeniowych](compute-linked-services.md) w innych regionach lub w środowisku lokalnym. Możesz również monitorować przepływy pracy i zarządzać nimi za pomocą zestawów SDK i Azure Monitor.

Data Factory został certyfikowany dla:

| **[Wydawanie certyfikatów w witrynie CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Jeśli interesuje Cię zgodność z platformą Azure i sposób zabezpieczania infrastruktury przez platformę Azure, odwiedź [Centrum zaufania firmy Microsoft](https://microsoft.com/en-us/trustcenter/default.aspx). Aby uzyskać najnowszą listę wszystkich kontroli zgodności z platformą Azure —  https://aka.ms/AzureCompliance .

W tym artykule opisano zagadnienia dotyczące zabezpieczeń w następujących dwóch scenariuszach przenoszenia danych: 

- **Scenariusz chmury** : w tym scenariuszu zarówno źródło, jak i miejsce docelowe są publicznie dostępne przez Internet. Obejmują one zarządzane usługi magazynu w chmurze, takie jak Azure Storage, Azure Synapse Analytics (dawniej SQL Data Warehouse), Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon RedShift, SaaS Services, takie jak Salesforce i protokoły sieci Web, takie jak FTP i OData. Zapoznaj się z pełną listą obsługiwanych źródeł danych w  [obsługiwanych sklepach i formatach danych](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scenariusz hybrydowy** : w tym scenariuszu Źródło lub miejsce docelowe znajduje się za zaporą lub wewnątrz lokalnej sieci firmowej. Magazyn danych znajduje się w sieci prywatnej lub sieci wirtualnej (najczęściej w źródle) i nie jest publicznie dostępny. Serwery baz danych hostowane na maszynach wirtualnych są również objęte tym scenariuszem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Scenariusze chmury

### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych

- **Przechowuj poświadczenia zaszyfrowane w magazynie zarządzanym Azure Data Factory** . Data Factory pomaga chronić poświadczenia magazynu danych przez szyfrowanie ich certyfikatami zarządzanymi przez firmę Microsoft. Te certyfikaty są obracane co dwa lata (co obejmuje odnowienie certyfikatu i migrację poświadczeń). Aby uzyskać więcej informacji o zabezpieczeniach usługi Azure Storage, zobacz [Omówienie zabezpieczeń usługi Azure Storage](../storage/blobs/security-recommendations.md).
- **Przechowuj poświadczenia w Azure Key Vault** . Możesz również przechowywać poświadczenia magazynu danych w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory Pobiera poświadczenia podczas wykonywania działania. Aby uzyskać więcej informacji, zobacz [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania
Jeśli magazyn danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie transfery danych między usługami przenoszenia danych w Data Factory i magazynem danych w chmurze są realizowane za pośrednictwem protokołu HTTPS lub TLS bezpiecznego kanału.

> [!NOTE]
> Wszystkie połączenia do Azure SQL Database i Azure Synapse Analytics wymagają szyfrowania (SSL/TLS), gdy dane są przesyłane do i z bazy danych. Podczas tworzenia potoku przy użyciu formatu JSON należy dodać właściwość szyfrowania i ustawić jej **wartość na true** w parametrach połączenia. W przypadku usługi Azure Storage można użyć **protokołu HTTPS** w parametrach połączenia.

> [!NOTE]
> Aby włączyć szyfrowanie podczas przesyłania danych z programu Oracle, wykonaj jedną z poniższych opcji:
> 1. W programie Oracle Server przejdź do pozycji Oracle Advanced Security (OAS) i skonfiguruj ustawienia szyfrowania, które obsługują szyfrowanie Triple-DES (3DES) i Advanced Encryption Standard (AES). Aby uzyskać szczegółowe informacje, zapoznaj się z [tym](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) artykułem. Funkcja ADF automatycznie negocjuje metodę szyfrowania, aby użyć konfiguracji w OAS podczas ustanawiania połączenia z bazą danych Oracle.
> 2. W podajniku APD można dodać EncryptionMethod = 1 w parametrach połączenia (w połączonej usłudze). Spowoduje to użycie protokołu SSL/TLS jako metody szyfrowania. Aby to użyć, należy wyłączyć ustawienia szyfrowania inne niż SSL w programie OAS na stronie serwera Oracle, aby uniknąć konfliktu szyfrowania.

> [!NOTE]
> Używana wersja protokołu TLS to 1,2.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre magazyny danych obsługują szyfrowanie danych magazynowanych. Zalecamy włączenie mechanizmu szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Transparent Data Encryption (TDE) w usłudze Azure Synapse Analytics pomaga chronić przed zagrożeniami złośliwymi działaniami, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych przechowywanych na bieżąco. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w usłudze Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Usługa Azure SQL Database
Azure SQL Database obsługuje również funkcję transparent Data Encryption (TDE), która pomaga chronić przed zagrożeniami złośliwego działania przez wykonywanie szyfrowania i odszyfrowywania danych w czasie rzeczywistym bez konieczności wprowadzania zmian w aplikacji. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [przezroczyste szyfrowanie danych dla SQL Database i magazynu danych](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store również zapewnia szyfrowanie danych przechowywanych na koncie. Po włączeniu Data Lake Store automatycznie szyfruje dane przed utrwaleniem i odszyfrowaniem przed pobraniem, co sprawia, że jest ono niewidoczne dla klienta, który uzyskuje dostęp do danych. Aby uzyskać więcej informacji, zobacz [zabezpieczenia w Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage i Azure Table Storage
Usługa Azure Blob Storage i usługa Azure Table Storage obsługują szyfrowanie usługi Storage (SSE), które automatycznie szyfruje dane przed utrwaleniem do magazynu i odszyfrowuje przed pobraniem. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Firma Amazon S3 obsługuje szyfrowanie danych między klientem i serwerem. Aby uzyskać więcej informacji, zobacz [Ochrona danych przy użyciu szyfrowania](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon RedShift obsługuje szyfrowanie klastra dla danych przechowywanych w spoczynku. Aby uzyskać więcej informacji, zobacz temat [szyfrowanie bazy danych Amazon RedShift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>SalesForce
Usługi Salesforce obsługują szyfrowanie platformy osłony, które umożliwiają szyfrowanie wszystkich plików, załączników i pól niestandardowych. Aby uzyskać więcej informacji, zobacz [Omówienie przepływu uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Scenariusze hybrydowe
Scenariusze hybrydowe wymagają, aby środowisko Integration Runtime było instalowane w sieci lokalnej, wewnątrz sieci wirtualnej (Azure) lub wewnątrz wirtualnej chmury prywatnej (Amazon). Własne środowisko Integration Runtime musi mieć dostęp do lokalnych magazynów danych. Aby uzyskać więcej informacji na temat środowiska Integration Runtime (własne środowisko uruchomieniowe), zobacz [jak utworzyć i skonfigurować własne środowisko Integration Runtime](./create-self-hosted-integration-runtime.md). 

![własne kanały środowiska Integration Runtime](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kanał polecenia umożliwia komunikację między usługami przenoszenia danych w ramach Data Factory i samodzielnego środowiska Integration Runtime. Komunikacja zawiera informacje związane z działaniem. Kanał danych służy do przesyłania danych między lokalnymi magazynami danych i magazynami danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Poświadczenia lokalnego magazynu danych
Poświadczenia mogą być przechowywane w usłudze Fabryka danych lub [odwoływać się do nich przez fabrykę danych](store-credentials-in-key-vault.md) podczas środowiska uruchomieniowego z Azure Key Vault. W przypadku przechowywania poświadczeń w usłudze Fabryka danych są one zawsze przechowywane w postaci zaszyfrowanej w ramach własnego środowiska Integration Runtime. 
 
- **Przechowuj poświadczenia lokalnie** . Jeśli bezpośrednio używasz polecenia cmdlet **Set-AzDataFactoryV2LinkedService** z parametrami połączenia i poświadczeniami wbudowanymi w kod JSON, połączona usługa jest zaszyfrowana i zapisywana w środowisku Integration Runtime.  W takim przypadku poświadczenia są przesyłane za pomocą usługi zaplecza platformy Azure, która jest niezwykle bezpieczna, do samodzielnej maszyny integracyjnej, w której jest ona zaszyfrowana i przechowywana. Własne środowisko Integration runtime używa funkcji [DPAPI](/previous-versions/ms995355(v=msdn.10)) systemu Windows do szyfrowania poufnych danych i informacji o poświadczeniach.

- **Przechowuj poświadczenia w Azure Key Vault** . Możesz również przechowywać poświadczenia magazynu danych w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory Pobiera poświadczenia podczas wykonywania działania. Aby uzyskać więcej informacji, zobacz [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md).

- **Przechowuj poświadczenia lokalnie bez przekazywania poświadczeń za pomocą zaplecza platformy Azure do własnego środowiska Integration Runtime** . Jeśli chcesz zaszyfrować i przechowywać poświadczenia lokalnie w środowisku środowiska Integration Runtime (samodzielne) bez konieczności przekazywania poświadczeń za pomocą zaplecza usługi Data Factory, wykonaj kroki opisane w temacie [szyfrowanie poświadczeń dla lokalnych magazynów danych w Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Wszystkie łączniki obsługują tę opcję. Własne środowisko Integration runtime używa funkcji [DPAPI](/previous-versions/ms995355(v=msdn.10)) systemu Windows do szyfrowania poufnych danych i informacji o poświadczeniach. 

   Użyj polecenia cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** , aby zaszyfrować poświadczenia połączonej usługi i szczegółowe informacje poufne w połączonej usłudze. Następnie można użyć zwróconego JSON (z elementem **EncryptedCredential** w parametrach połączenia), aby utworzyć połączoną usługę za pomocą polecenia cmdlet **Set-AzDataFactoryV2LinkedService** .  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty używane podczas szyfrowania połączonej usługi w ramach własnego środowiska Integration Runtime
Domyślnie program PowerShell używa portu 8060 na komputerze przy użyciu własnego środowiska Integration Runtime na potrzeby bezpiecznej komunikacji. W razie potrzeby można zmienić ten port.  

![Port HTTPS dla bramy](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Wszystkie transfery danych są nawiązywane za pośrednictwem protokołu HTTPS Secure Channel i TLS over TCP, aby zapobiec atakom typu man-in-the-Middle podczas komunikacji z usługami platformy Azure.

W celu dodatkowego zabezpieczenia kanału komunikacyjnego między siecią lokalną i platformą Azure można również użyć [protokołu IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [Azure ExpressRoute](../expressroute/expressroute-introduction.md) .

Usługa Azure Virtual Network to logiczna reprezentacja sieci w chmurze. Sieć lokalną można połączyć z siecią wirtualną przez skonfigurowanie protokołu IPSec VPN (lokacja-lokacja) lub ExpressRoute (prywatna Komunikacja równorzędna).    

Poniższa tabela zawiera podsumowanie zaleceń dotyczących sieci i samodzielnego środowiska Integration Runtime na podstawie różnych kombinacji lokalizacji źródłowej i docelowej na potrzeby przenoszenia danych hybrydowych.

| Element źródłowy      | Element docelowy                              | Konfiguracja sieci                    | Instalacja środowiska Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Środowiska lokalne | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | IPSec sieci VPN (punkt-lokacja lub lokacja-lokacja) | Własne środowisko Integration Runtime należy zainstalować na maszynie wirtualnej platformy Azure w sieci wirtualnej.  |
| Środowiska lokalne | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | ExpressRoute (prywatna Komunikacja równorzędna)           | Własne środowisko Integration Runtime należy zainstalować na maszynie wirtualnej platformy Azure w sieci wirtualnej.  |
| Środowiska lokalne | Usługi oparte na platformie Azure, które mają publiczny punkt końcowy | ExpressRoute (Komunikacja równorzędna firmy Microsoft)            | Własne środowisko Integration runtime można zainstalować lokalnie lub na maszynie wirtualnej platformy Azure. |

Na poniższych ilustracjach przedstawiono użycie własnego środowiska Integration Runtime do przeniesienia danych między lokalną bazą danych i usługami platformy Azure przy użyciu ExpressRoute i sieci VPN IPSec (z usługą Azure Virtual Network):

**ExpressRoute**

![Korzystanie z ExpressRoute z bramą](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec sieci VPN**

![IPSec VPN z bramą](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a> Konfiguracja zapory i lista dozwolonych ustawień dla adresów IP

> [!NOTE] 
> Może być konieczne zarządzanie portami lub Konfigurowanie listy dozwolonych domen na poziomie zapory firmowej zgodnie z wymaganiami odpowiednich źródeł danych. Ta tabela używa tylko Azure SQL Database, usługi Azure Synapse Analytics i Azure Data Lake Store jako przykładów.

> [!NOTE] 
> Aby uzyskać szczegółowe informacje na temat strategii dostępu do danych za poorednictwem Azure Data Factory, zobacz [ten artykuł](./data-access-strategies.md#data-access-strategies-through-azure-data-factory).

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory dla sieci lokalnej/prywatnej    
W przedsiębiorstwie firmowa Zapora jest uruchamiana na centralnym routerze organizacji. Zapora systemu Windows jest uruchamiana jako demon na komputerze lokalnym, na którym jest zainstalowany własny środowisko Integration Runtime. 

W poniższej tabeli przedstawiono wymagania dotyczące portów i domen wychodzących dla zapór firmowych:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Może być konieczne zarządzanie portami lub Konfigurowanie listy dozwolonych domen na poziomie zapory firmowej zgodnie z wymaganiami odpowiednich źródeł danych. Ta tabela używa tylko Azure SQL Database, usługi Azure Synapse Analytics i Azure Data Lake Store jako przykładów.   

W poniższej tabeli przedstawiono wymagania dotyczące portów ruchu przychodzącego dla zapory systemu Windows:

| Porty wejściowe | Opis                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Wymagane przez polecenie cmdlet szyfrowania programu PowerShell, zgodnie z opisem w artykule [szyfrowanie poświadczeń dla lokalnych magazynów danych w Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)oraz przez aplikację Menedżer poświadczeń w celu bezpiecznego ustawiania poświadczeń dla lokalnych magazynów danych w ramach własnego środowiska Integration Runtime. |

![Wymagania dotyczące portów bramy](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Konfiguracje IP i lista dozwolonych ustawień w magazynach danych
Niektóre magazyny danych w chmurze wymagają również, aby adres IP komputera miał dostęp do magazynu. Upewnij się, że adres IP maszyny z obsługą środowiska Integration Runtime jest odpowiednio dozwolony lub skonfigurowany w zaporze.

Następujące magazyny danych w chmurze wymagają zezwolenia na adres IP komputera Integration Runtime. Niektóre z tych magazynów danych domyślnie mogą nie wymagać listy dozwolonych. 

- [Baza danych SQL Azure](../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Czy samodzielne środowisko Integration Runtime może być współużytkowane przez różne fabryki danych?**

Tak. Więcej informacji znajdziesz [tutaj](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Jakie są wymagania dotyczące portów dla środowiska Integration Runtime, które mają być obsługiwane?**

Własne środowisko Integration Runtime umożliwia nawiązywanie połączeń opartych na protokole HTTP z dostępem do Internetu. Porty wychodzące 443 muszą być otwarte dla własnego środowiska Integration Runtime, aby nawiązać to połączenie. Otwórz port przychodzący 8060 tylko na poziomie komputera (nie na poziomie zapory firmowej) dla aplikacji Menedżer poświadczeń. Jeśli Azure SQL Database lub usługa Azure Synapse Analytics jest używana jako źródło lub miejsce docelowe, należy również otworzyć port 1433. Aby uzyskać więcej informacji, zobacz sekcję [konfiguracje zapory i lista dozwolonych ustawień dla adresów IP](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) . 


## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o wydajności działania kopiowania Azure Data Factory, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](copy-activity-performance.md).

