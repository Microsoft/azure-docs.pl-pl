---
title: Zagadnienia związane z zabezpieczeniami
description: W tym artykule opisano podstawową infrastrukturę zabezpieczeń, której używają usługi przenoszenia danych w usłudze Azure Data Factory w celu zabezpieczenia danych.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bb3f22223bd64c06cfa4a5f6ffabe7b128dff1d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416463"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
>
> * [Wersja 1](v1/data-factory-data-movement-security-considerations.md)
> * [Bieżąca wersja](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano podstawową infrastrukturę zabezpieczeń, której usługi przenoszenia danych w usłudze Azure Data Factory używają w celu zabezpieczenia danych. Zasoby do zarządzania fabryką danych są oparte na infrastrukturze zabezpieczeń platformy Azure i wykorzystują wszystkie możliwe środki bezpieczeństwa oferowane przez platformę Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](concepts-pipelines-activities.md) danych. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Te potoki znajdują się w regionie, w którym utworzono fabrykę danych. 

Mimo że usługa Data Factory jest dostępna tylko w kilku regionach, usługa przenoszenia danych jest [dostępna globalnie,](concepts-integration-runtime.md#integration-runtime-location) aby zapewnić zgodność danych, wydajność i zmniejszone koszty transferu danych wychodzących. 

Usługa Azure Data Factory nie przechowuje żadnych danych z wyjątkiem poświadczeń usługi połączonej dla magazynów danych w chmurze, które są szyfrowane przy użyciu certyfikatów. Za pomocą usługi Data Factory można tworzyć oparte na danych przepływy pracy w celu organizowania przepływu danych między [obsługiwanymi magazynami danych](copy-activity-overview.md#supported-data-stores-and-formats)i przetwarzania danych przy użyciu [usług obliczeniowych](compute-linked-services.md) w innych regionach lub w środowisku lokalnym. Można również monitorować przepływy pracy i zarządzać nimi przy użyciu zestawów SDK i usługi Azure Monitor.

Data Factory posiada certyfikat:

| **[Certyfikacja CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Jeśli interesuje Cię zgodność z usługą Azure i sposób, w jaki platforma Azure zabezpiecza własną infrastrukturę, odwiedź [Centrum zaufania firmy Microsoft.](https://microsoft.com/en-us/trustcenter/default.aspx) Aby uzyskać najnowszą listę wszystkich ofert https://aka.ms/AzureCompliancezgodności platformy Azure, sprawdź - .

W tym artykule przejrzyjmy zagadnienia dotyczące zabezpieczeń w następujących dwóch scenariuszach przenoszenia danych: 

- **Scenariusz chmury:** W tym scenariuszu zarówno źródła, jak i miejsca docelowego są publicznie dostępne za pośrednictwem Internetu. Należą do nich zarządzane usługi magazynu w chmurze, takie jak Usługa Azure Storage, usługa Azure SQL Data Warehouse, usługa Azure SQL Database, usługa Azure Data Lake Store, Amazon S3, Amazon Redshift, usługi SaaS, takie jak Salesforce, oraz protokoły internetowe, takie jak FTP i OData. Znajdź pełną listę obsługiwanych źródeł danych w [obsługiwanych magazynach danych i formatach](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scenariusz hybrydowy:** W tym scenariuszu źródło lub miejsce docelowe znajduje się za zaporą lub wewnątrz lokalnej sieci firmowej. Lub magazyn danych znajduje się w sieci prywatnej lub sieci wirtualnej (najczęściej źródłowej) i nie jest publicznie dostępny. Serwery bazy danych hostowane na maszynach wirtualnych również mieszczą się w tym scenariuszu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Scenariusze dotyczące chmury

### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych

- **Przechowywanie zaszyfrowanych poświadczeń w magazynie zarządzanym usługi Azure Data Factory**. Usługa Data Factory pomaga chronić poświadczenia magazynu danych, szyfrując je za pomocą certyfikatów zarządzanych przez firmę Microsoft. Certyfikaty te są obracane co dwa lata (co obejmuje odnawianie certyfikatów i migrację poświadczeń). Aby uzyskać więcej informacji na temat zabezpieczeń usługi Azure Storage, zobacz [omówienie zabezpieczeń usługi Azure Storage](../security/fundamentals/storage-overview.md).
- **Przechowuj poświadczenia w usłudze Azure Key Vault**. Poświadczenia magazynu danych można również przechowywać w [usłudze Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Fabryka danych pobiera poświadczenia podczas wykonywania działania. Aby uzyskać więcej informacji, zobacz [Przechowywanie poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania danych
Jeśli magazyn danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie transfery danych między usługami przenoszenia danych w fabryce danych a magazynem danych w chmurze są przesyłane za pośrednictwem bezpiecznego kanału HTTPS lub TLS.

> [!NOTE]
> Wszystkie połączenia z usługą Azure SQL Database i usługą Azure SQL Data Warehouse wymagają szyfrowania (SSL/TLS), gdy dane są przesyłane do i z bazy danych. Podczas tworzenia potoku przy użyciu JSON, dodaj właściwość szyfrowania i ustaw ją na **true** w ciągu połączenia. W przypadku usługi Azure Storage można użyć **protokołu HTTPS** w ciągu połączenia.

> [!NOTE]
> Aby włączyć szyfrowanie podczas przesyłania danych z oracle, wykonaj jedną z poniższych opcji:
> 1. Na serwerze Oracle przejdź do programu Oracle Advanced Security (OAS) i skonfiguruj ustawienia szyfrowania, które obsługuje szyfrowanie Triple-DES (3DES) i Advanced Encryption Standard (AES), szczegółowe informacje można znaleźć [tutaj.](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) ADF automatycznie negocjuje metodę szyfrowania, aby używać metody skonfigurowanej w UA podczas ustanawiania połączenia z Oracle.
> 2. W usłudze ADF można dodać EncryptionMethod=1 w ciągu połączenia (w usłudze połączonej). Spowoduje to użycie protokołu SSL/TLS jako metody szyfrowania. Aby użyć tego, należy wyłączyć ustawienia szyfrowania innych niż SSL w OAS po stronie serwera Oracle, aby uniknąć konfliktu szyfrowania.

> [!NOTE]
> Używana wersja TLS to 1.2.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre magazyny danych obsługują szyfrowanie danych w spoczynku. Zaleca się włączenie mechanizmu szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Przezroczyste szyfrowanie danych (TDE) w usłudze Azure SQL Data Warehouse pomaga chronić przed zagrożeniem złośliwą aktywnością, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych w spoczynku. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w magazynie danych SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Usługa Azure SQL Database obsługuje również przezroczyste szyfrowanie danych (TDE), które pomaga chronić przed zagrożeniem złośliwą aktywnością, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych, bez konieczności wprowadzania zmian w aplikacji. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [Przezroczyste szyfrowanie danych dla bazy danych SQL i magazynu danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Usługa Azure Data Lake Store zapewnia również szyfrowanie danych przechowywanych na koncie. Po włączeniu usługa Data Lake Store automatycznie szyfruje dane przed utrwalaniem i odszyfrowywaniem przed pobraniem, dzięki czemu jest przezroczysta dla klienta, który uzyskuje dostęp do danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia w magazynie usługi Azure Data Lake](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Magazyn obiektów Blob platformy Azure i magazyn tabel platformy Azure
Usługa Azure Blob storage i usługa Azure Table storage obsługują szyfrowanie usługi storage (SSE), które automatycznie szyfruje dane przed utrwaleniem w magazynie i odszyfrowuje przed pobraniem. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage Service dla danych w spoczynku](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 obsługuje zarówno szyfrowanie danych klienta, jak i serwera w stanie spoczynku. Aby uzyskać więcej informacji, zobacz [Ochrona danych przy użyciu szyfrowania](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift obsługuje szyfrowanie klastrów dla danych w spoczynku. Aby uzyskać więcej informacji, zobacz [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>SalesForce
Salesforce obsługuje szyfrowanie platformy Shield, które umożliwia szyfrowanie wszystkich plików, załączników i pól niestandardowych. Aby uzyskać więcej informacji, zobacz [Opis przepływu uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Scenariusze hybrydowe
Scenariusze hybrydowe wymagają samodzielnego hostowanego środowiska wykonawczego integracji do zainstalowania w sieci lokalnej, wewnątrz sieci wirtualnej (Azure) lub wewnątrz wirtualnej chmury prywatnej (Amazon). Środowisko uruchomieniowe integracji hostowanego samodzielnie musi mieć dostęp do lokalnych magazynów danych. Aby uzyskać więcej informacji na temat środowiska uruchomieniowego integracji hostowanego samodzielnie, zobacz [Jak utworzyć i skonfigurować środowisko uruchomieniowe integracji hostowanego samodzielnie.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) 

![samodzielnie hostowane kanały wykonawcze integracji](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kanał poleceń umożliwia komunikację między usługami przenoszenia danych w fabryce danych a samodzielnym czasem wykonywania integracji. Komunikat zawiera informacje związane z działalnością. Kanał danych służy do przesyłania danych między lokalnymi magazynami danych a magazynami danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Poświadczenia lokalnego magazynu danych
Poświadczenia mogą być przechowywane w fabryce danych lub [odwoływać się przez fabrykę danych](store-credentials-in-key-vault.md) w czasie wykonywania z usługi Azure Key Vault. Jeśli przechowywanie poświadczeń w fabryce danych, jest zawsze przechowywane zaszyfrowane w środowisku uruchomieniowym integracji hostowanej samodzielnie. 
 
- **Przechowywanie poświadczeń lokalnie**. Jeśli bezpośrednio użyjesz polecenia cmdlet **Set-AzDataFactoryV2LinkedService** z ciągami połączeń i poświadczeniami wbudowanymi w JSON, usługa połączona jest szyfrowana i przechowywana w czasie wykonywania integracji hostowanej samodzielnie.  W takim przypadku poświadczenia przepływają za pośrednictwem usługi zaplecza azure, która jest bardzo bezpieczna, do komputera integracji hostowanego samodzielnie, gdzie jest ostatecznie szyfrowana i przechowywana. Środowisko wykonawcze integracji hostowanej samodzielnie używa interfejsu [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) systemu Windows do szyfrowania poufnych danych i informacji o poświadczeniach.

- **Przechowuj poświadczenia w usłudze Azure Key Vault**. Poświadczenia magazynu danych można również przechowywać w [usłudze Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Fabryka danych pobiera poświadczenia podczas wykonywania działania. Aby uzyskać więcej informacji, zobacz [Przechowywanie poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md).

- **Przechowywanie poświadczeń lokalnie bez przepływu poświadczeń za pośrednictwem zaplecza platformy Azure do środowiska wykonawczego integracji hostowanego samodzielnie.** Jeśli chcesz szyfrować i przechowywać poświadczenia lokalnie w środowisku uruchomieniowym integracji hostowanej bez konieczności przepływu poświadczeń za pośrednictwem wewnętrznej bazy danych, wykonaj kroki opisane w [obszarze Szyfruj poświadczenia dla lokalnych magazynów danych w usłudze Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Wszystkie złącza obsługują tę opcję. Środowisko wykonawcze integracji hostowanej samodzielnie używa interfejsu [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) systemu Windows do szyfrowania poufnych danych i informacji o poświadczeniach. 

   Użyj polecenia cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredCredential** do szyfrowania poświadczeń usługi połączonej i poufnych szczegółów w połączonej usłudze. Następnie można użyć JSON zwrócone (z **EncryptedCredential** element w ciągu połączenia) do utworzenia połączonej usługi przy użyciu polecenia cmdlet **Set-AzDataFactoryV2LinkedService.**  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty używane podczas szyfrowania połączonej usługi w czasie wykonywania integracji hostowanej samodzielnie
Domyślnie program PowerShell używa portu 8060 na komputerze z własnym hostowanym czasem wykonywania integracji w celu bezpiecznej komunikacji. W razie potrzeby można zmienić ten port.  

![Port HTTPS dla bramy](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Wszystkie transfery danych są za pośrednictwem bezpiecznego kanału HTTPS i TLS przez TCP, aby zapobiec atakom typu man-in-the-middle podczas komunikacji z usługami platformy Azure.

Można również użyć [protokołu IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [usługi Azure ExpressRoute,](../expressroute/expressroute-introduction.md) aby dodatkowo zabezpieczyć kanał komunikacji między siecią lokalną a platformą Azure.

Usługa Azure Virtual Network jest logiczną reprezentacją sieci w chmurze. Sieć lokalną można połączyć z siecią wirtualną, konfigurując sieć VPN IPSec (lokacja lokacja) lub ExpressRoute (prywatna komunikacja równorzędna).    

W poniższej tabeli podsumowano zalecenia konfiguracji środowiska wykonawczego integracji sieciowej i samodzielnej integracji na podstawie różnych kombinacji lokalizacji źródłowych i docelowych dla hybrydowego przenoszenia danych.

| Element źródłowy      | Element docelowy                              | Konfiguracja sieci                    | Instalacja środowiska Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | IPSec VPN (punkt-lokacja lub lokacja) | Środowisko wykonawcze integracji hostowanego samodzielnie należy zainstalować na maszynie wirtualnej platformy Azure w sieci wirtualnej.  |
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | Usługa ExpressRoute (prywatna komunikacja równorzędna)           | Środowisko wykonawcze integracji hostowanego samodzielnie należy zainstalować na maszynie wirtualnej platformy Azure w sieci wirtualnej.  |
| Lokalnie | Usługi oparte na platformie Azure, które mają publiczny punkt końcowy | Usługa ExpressRoute (komunikacja równorzędna firmy Microsoft)            | Środowisko wykonawcze integracji hostowanego samodzielnie można zainstalować lokalnie lub na maszynie wirtualnej platformy Azure. |

Na poniższych obrazach przedstawiono użycie środowiska wykonawczego integracji hostowanej przez własny host do przenoszenia danych między lokalną bazą danych a usługami platformy Azure przy użyciu usługi ExpressRoute i IPSec VPN (z usługą Azure Virtual Network):

**ExpressRoute**

![Używanie usługi ExpressRoute z bramą](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![Sieć VPN IPSec z bramą](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Konfiguracje zapory i zezwalaj na konfigurowanie listy adresów IP

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory sieci lokalnej/prywatnej    
W przedsiębiorstwie zapora firmowa działa na centralnym routerze organizacji. Zapora systemu Windows działa jako demon na komputerze lokalnym, na którym jest zainstalowane środowisko uruchomieniowe integracji hostowanego samodzielnie. 

Poniższa tabela zawiera wymagania dotyczące portów wychodzących i domen dla zapór firmowych:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Może być konieczne zarządzanie portami lub konfigurowanie listy dozwolonych dla domen na poziomie zapory firmowej, zgodnie z wymaganiami odpowiednich źródeł danych. W tej tabeli używa tylko usługi Azure SQL Database, usługi Azure SQL Data Warehouse i Usługi Azure Data Lake Store jako przykłady.   

Poniższa tabela zawiera wymagania dotyczące portów przychodzących dla Zapory systemu Windows:

| Porty wejściowe | Opis                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Wymagane przez polecenie cmdlet szyfrowania programu PowerShell, zgodnie z opisem w [szyfruj poświadczenia dla lokalnych magazynów danych w usłudze Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)i przez aplikację menedżera poświadczeń, aby bezpiecznie ustawić poświadczenia dla lokalnych magazynów danych w środowisku uruchomieniowym integracji hostowanej samodzielnie. |

![Wymagania dotyczące portu bramy](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Konfiguracje adresów IP i zezwalaj na konfigurowanie listy w magazynach danych
Niektóre magazyny danych w chmurze wymagają również zezwalania na adres IP komputera uzyskującego dostęp do magazynu. Upewnij się, że adres IP komputera wykonawczego integracji hostowanego jest dozwolone lub odpowiednio skonfigurowane w zaporze.

Następujące magazyny danych w chmurze wymagają zezwolenia na adres IP komputera wykonawczego integracji hostowanego samodzielnie. Niektóre z tych magazynów danych, domyślnie, może nie wymagać listy dozwolonych. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Magazyn danych SQL platformy Azure](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Czy środowisko uruchomieniowe integracji hostowane samodzielnie może być współużytkowane w różnych fabrykach danych?**

Tak. Więcej szczegółów można znaleźć [tutaj](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Jakie są wymagania dotyczące portu dla środowiska uruchomieniowego integracji hostowanego samodzielnie do pracy?**

Środowisko wykonawcze integracji hostowanego samodzielnie sprawia, że połączenia oparte na protokonasie HTTP mają dostęp do Internetu. Porty wychodzące 443 muszą zostać otwarte dla środowiska wykonawczego integracji hostowanego samodzielnie, aby nawiązać to połączenie. Otwórz port przychodzący 8060 tylko na poziomie komputera (nie na poziomie zapory firmowej) dla aplikacji menedżera poświadczeń. Jeśli usługa Azure SQL Database lub usługa Azure SQL Data Warehouse jest używana jako źródło lub miejsce docelowe, należy również otworzyć port 1433. Aby uzyskać więcej informacji, zobacz [konfiguracje zapory i zezwolić na konfigurowanie listy dla adresów IP](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) sekcji. 


## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat wydajności działania kopiowania danych w usłudze Azure Data Factory, zobacz [Przewodnik wydajności i dostrajania działania kopiowania.](copy-activity-performance.md)

 
