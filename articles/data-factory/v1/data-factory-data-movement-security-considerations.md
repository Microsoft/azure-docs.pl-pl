---
title: Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w Azure Data Factory
description: Informacje na temat zabezpieczania przenoszenia danych w Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9ae4970383802adad755fff4a6ce382db6ce32fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619920"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory — zagadnienia dotyczące zabezpieczeń związane z przenoszeniem danych

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [zagadnienia dotyczące zabezpieczeń przenoszenia danych w przypadku Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Wprowadzenie
W tym artykule opisano podstawową infrastrukturę zabezpieczeń, której usługi przenoszenia danych w Azure Data Factory używać do zabezpieczania danych. Zasoby zarządzania Azure Data Factory są oparte na infrastrukturze zabezpieczeń platformy Azure i wykorzystują wszystkie możliwe środki bezpieczeństwa oferowane przez platformę Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](data-factory-create-pipelines.md) danych. Potok jest logicznym grupowaniem działań, które wspólnie wykonują zadanie. Te potoki znajdują się w regionie, w którym została utworzona Fabryka danych. 

Mimo że Data Factory jest dostępna tylko w regionach **zachodnie stany USA**, **Wschodnie stany USA**i **Europa Północna** , Usługa przenoszenia danych jest dostępna [globalnie w kilku regionach](data-factory-data-movement-activities.md#global). Usługa Data Factory zapewnia, że dane nie opuszczają obszaru geograficznego/regionu, chyba że jawnie poinstruuje usługę, aby używała alternatywnego regionu, jeśli usługa przenoszenia danych nie została jeszcze wdrożona w tym regionie. 

Azure Data Factory samo nie przechowuje żadnych danych z wyjątkiem poświadczeń połączonej usługi dla magazynów danych w chmurze, które są szyfrowane przy użyciu certyfikatów. Umożliwia tworzenie przepływów pracy opartych na danych w celu organizowania przenoszenia danych między [obsługiwanymi magazynami danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) i przetwarzaniem danych przy użyciu [usług obliczeniowych](data-factory-compute-linked-services.md) w innych regionach lub w środowisku lokalnym. Umożliwia także [monitorowanie przepływów pracy i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) przy użyciu mechanizmów programistycznych i interfejsu użytkownika.

Przenoszenie danych przy użyciu Azure Data Factory jest **certyfikowane** dla:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [GWIAZDA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Jeśli interesuje Cię zgodność z platformą Azure i sposób, w jaki platforma Azure zabezpiecza własną infrastrukturę, odwiedź [Centrum zaufania firmy Microsoft](https://microsoft.com/en-us/trustcenter/default.aspx). 

W tym artykule opisano zagadnienia dotyczące zabezpieczeń w następujących dwóch scenariuszach przenoszenia danych: 

- **Scenariusz w chmurze**— w tym scenariuszu zarówno źródło, jak i miejsce docelowe są publicznie dostępne za poorednictwem Internetu. Obejmują one zarządzane usługi magazynu w chmurze, takie jak Azure Storage, Azure Synapse Analytics (dawniej SQL Data Warehouse), Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon RedShift, SaaS Services, takie jak Salesforce i protokoły sieci Web, takie jak FTP i OData. Pełną listę obsługiwanych źródeł danych można znaleźć [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Scenariusz hybrydowy**— w tym scenariuszu źródłowa lub docelowa znajduje się za zaporą lub wewnątrz lokalnej sieci firmowej albo magazyn danych znajduje się w sieci prywatnej/sieci wirtualnej (najczęściej w źródle) i nie jest publicznie dostępna. Serwery baz danych hostowane na maszynach wirtualnych są również objęte tym scenariuszem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Scenariusze chmury
### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych
Azure Data Factory chroni poświadczenia magazynu danych przez ich **szyfrowanie** przy użyciu **certyfikatów zarządzanych przez firmę Microsoft**. Te certyfikaty są obracane co **dwa lata** (co obejmuje odnowienie certyfikatu i migracji poświadczeń). Te zaszyfrowane poświadczenia są bezpiecznie przechowywane w usłudze **Azure Storage zarządzanej przez Azure Data Factory usług zarządzania**. Aby uzyskać więcej informacji o zabezpieczeniach usługi Azure Storage, zobacz [Omówienie zabezpieczeń usługi Azure Storage](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania
Jeśli magazyn danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie transfery danych między usługami przenoszenia danych w Data Factory i magazynem danych w chmurze są realizowane za pośrednictwem protokołu HTTPS lub TLS bezpiecznego kanału.

> [!NOTE]
> Wszystkie połączenia do **Azure SQL Database** i **Azure Synapse Analytics** zawsze wymagają szyfrowania (SSL/TLS), gdy dane są przesyłane do i z bazy danych. Podczas tworzenia potoku przy użyciu edytora JSON należy dodać właściwość **szyfrowania** i ustawić dla niej **wartość true** w **parametrach połączenia**. W przypadku korzystania z [Kreatora kopiowania](data-factory-azure-copy-wizard.md)Kreator domyślnie ustawia tę właściwość. W przypadku **usługi Azure Storage**można użyć **protokołu HTTPS** w parametrach połączenia.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre magazyny danych obsługują szyfrowanie danych magazynowanych. Zalecamy włączenie mechanizmu szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Transparent Data Encryption (TDE) w usłudze Azure Synapse Analytics pomaga chronić przed zagrożeniami złośliwych działań, wykonując szyfrowanie i odszyfrowywanie danych w czasie rzeczywistym. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w programie Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database obsługuje również funkcję transparent Data Encryption (TDE), która pomaga chronić przed zagrożeniami złośliwego działania przez wykonywanie szyfrowania i odszyfrowywanie danych w czasie rzeczywistym bez konieczności wprowadzania zmian w aplikacji. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [transparent Data Encryption z Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Magazyn Azure Data Lake udostępnia również szyfrowanie danych przechowywanych na koncie. Po włączeniu Data Lake magazyn automatycznie szyfruje dane przed utrwaleniem i odszyfrowaniem przed pobraniem, co sprawia, że jest ono niewidoczne dla klienta uzyskującego dostęp do danych. Aby uzyskać więcej informacji, zobacz [zabezpieczenia w Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage i Azure Table Storage
Usługa Azure Blob Storage i usługa Azure Table Storage obsługują funkcję szyfrowanie usługi Storage (SSE), która automatycznie szyfruje dane przed utrwaleniem do magazynu i odszyfrowuje przed pobraniem. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Firma Amazon S3 obsługuje szyfrowanie danych między klientem i serwerem. Aby uzyskać więcej informacji, zobacz [Ochrona danych przy użyciu szyfrowania](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Obecnie Data Factory nie obsługuje usługi Amazon S3 w ramach wirtualnej chmury prywatnej (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon RedShift obsługuje szyfrowanie klastra dla danych przechowywanych w spoczynku. Aby uzyskać więcej informacji, zobacz temat [szyfrowanie bazy danych Amazon RedShift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Obecnie Data Factory nie obsługuje usługi Amazon RedShift wewnątrz VPC. 

#### <a name="salesforce"></a>SalesForce
Usługi Salesforce obsługują szyfrowanie platformy osłony, które umożliwiają szyfrowanie wszystkich plików, załączników, pól niestandardowych. Aby uzyskać więcej informacji, zobacz [Omówienie przepływu uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Scenariusze hybrydowe (przy użyciu bramy Zarządzanie danymi)
Scenariusze hybrydowe wymagają zainstalowania bramy Zarządzanie danymi w sieci lokalnej lub wewnątrz sieci wirtualnej (Azure) lub wirtualnej chmury prywatnej (Amazon). Brama musi mieć możliwość dostępu do lokalnych magazynów danych. Aby uzyskać więcej informacji o bramie, zobacz [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md). 

![Zarządzanie danymi kanałów bramy](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kanał polecenia** umożliwia komunikację między usługami przenoszenia danych w usłudze Data Factory i zarządzanie danymi Gateway. Komunikacja zawiera informacje związane z działaniem. Kanał danych służy do przesyłania danych między lokalnymi magazynami danych i magazynami danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Poświadczenia lokalnego magazynu danych
Poświadczenia lokalnych magazynów danych są przechowywane lokalnie (nie w chmurze). Można je ustawić na trzy różne sposoby. 

- Używanie **zwykłego tekstu** (mniej bezpieczne) za pośrednictwem protokołu HTTPS w witrynie Azure Portal/Kreator kopiowania. Poświadczenia są przesyłane w postaci zwykłego tekstu do bramy lokalnej.
- Używanie **biblioteki kryptograficznej JavaScript w Kreatorze kopiowania**.
- Za pomocą **aplikacji Menedżer poświadczeń opartych na kliknięciach**. Aplikacja dwukrotnego kliknięcia jest uruchamiana na maszynie lokalnej, która ma dostęp do bramy i ustawia poświadczenia dla magazynu danych. Ta opcja i następny z nich są najbardziej bezpiecznymi opcjami. Aplikacja Menedżer poświadczeń domyślnie używa portu 8050 na komputerze z bramą do bezpiecznej komunikacji.  
- Użyj polecenia cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) programu PowerShell, aby zaszyfrować poświadczenia. Polecenie cmdlet używa certyfikatu, który jest skonfigurowany do szyfrowania poświadczeń przez bramę. Można użyć zaszyfrowanych poświadczeń zwracanych przez to polecenie cmdlet i dodać je do elementu **EncryptedCredential** obiektu **CONNECTIONSTRING** w pliku JSON, który jest używany z poleceniem cmdlet [New-AZDATAFACTORYLINKEDSERVICE](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) lub w fragmencie kodu JSON w edytorze Data Factory w portalu. Ta opcja i aplikacja dwukrotnego kliknięcia są najbardziej bezpiecznymi opcjami. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Szyfrowanie oparte na bibliotece Java kryptografii
Poświadczenia magazynu danych można szyfrować przy użyciu [biblioteki kryptografii JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) z [Kreatora kopiowania](data-factory-copy-wizard.md). Po wybraniu tej opcji Kreator kopiowania Pobiera klucz publiczny bramy i używa go do szyfrowania poświadczeń magazynu danych. Poświadczenia są odszyfrowywane przez maszynę bramy i chronione przez [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx)systemu Windows.

**Obsługiwane przeglądarki:** IE8, IE9, programu IE10, IE11, Microsoft Edge i najnowsze przeglądarki Firefox, Chrome, operac i Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplikacja Menedżer poświadczeń jednokrotnego kliknięcia
Korzystając z Kreatora Azure Portal/Copy, można uruchomić aplikację Menedżer poświadczeń opartej na krotnym kliknięciu, która zostanie utworzona podczas tworzenia potoków. Ta aplikacja zapewnia, że poświadczenia nie są przekazywane w postaci zwykłego tekstu przez sieć. Domyślnie używa portu **8050** na komputerze z bramą do bezpiecznej komunikacji. W razie potrzeby można zmienić ten port.  
  
![Port HTTPS dla bramy](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Obecnie Brama Zarządzanie danymi używa jednego **certyfikatu**. Ten certyfikat jest tworzony podczas instalacji bramy (dotyczy Zarządzanie danymi Brama utworzona po listopad 2016 i w wersji 2.4. xxxx. x lub nowszej). Można zastąpić ten certyfikat własnym certyfikatem SSL/TLS. Ten certyfikat jest używany przez aplikację Menedżer poświadczeń kliknij raz, aby bezpiecznie połączyć się z maszyną bramy w celu ustawienia poświadczeń magazynu danych. Przechowuje bezpieczne poświadczenia magazynu danych przy użyciu funkcji [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) systemu Windows na komputerze z bramą. 

> [!NOTE]
> Starsze bramy zainstalowane przed listopadem 2016 lub wersja 2.3. xxxx. x nadal używają poświadczeń zaszyfrowanych i przechowywanych w chmurze. Nawet w przypadku uaktualnienia bramy do najnowszej wersji poświadczenia nie są migrowane do maszyny lokalnej    
  
| Wersja bramy (podczas tworzenia) | Przechowywane poświadczenia | Szyfrowanie/zabezpieczenia poświadczeń | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3. xxxx. x | W chmurze | Zaszyfrowane przy użyciu certyfikatu (innego niż używany przez aplikację Menedżer poświadczeń) | 
| > = 2.4. xxxx. x | Lokalnie | Zabezpieczone za pomocą funkcji DPAPI | 
  

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Wszystkie transfery danych są nawiązywane za pośrednictwem protokołu **https** Secure Channel i **TLS over TCP** , aby zapobiec atakom typu man-in-the-Middle podczas komunikacji z usługami platformy Azure.
 
Aby dodatkowo zabezpieczyć kanał komunikacyjny między siecią lokalną i platformą Azure, można także użyć [protokołu IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [Express Route](../../expressroute/expressroute-introduction.md) .

Usługa Virtual Network to logiczna reprezentacja sieci w chmurze. Możesz połączyć sieć lokalną z siecią wirtualną platformy Azure, konfigurując sieci VPN IPSec (lokacja-lokacja) lub Express Route (prywatna Komunikacja równorzędna)     

Poniższa tabela zawiera podsumowanie zaleceń dotyczących konfiguracji sieci i bramy na podstawie różnych kombinacji lokalizacji źródłowej i docelowej do przenoszenia danych hybrydowych.

| Element źródłowy | Element docelowy | Konfiguracja sieci | Konfiguracja bramy |
| ------ | ----------- | --------------------- | ------------- | 
| Środowiska lokalne | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | IPSec sieci VPN (punkt-lokacja lub lokacja-lokacja) | Bramę można zainstalować lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej | 
| Środowiska lokalne | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | ExpressRoute (prywatna Komunikacja równorzędna) | Bramę można zainstalować lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej | 
| Środowiska lokalne | Usługi oparte na platformie Azure, które mają publiczny punkt końcowy | ExpressRoute (publiczna Komunikacja równorzędna) | Brama musi być zainstalowana lokalnie | 

Na poniższych ilustracjach przedstawiono użycie bramy Zarządzanie danymi do przeniesienia danych między lokalną bazą danych i usługami platformy Azure przy użyciu usługi Express Route i sieci VPN IPSec (z Virtual Network):

**Trasa Express:**
 
![Korzystanie z bramy Express Route z bramą](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec sieci VPN:**

![IPSec VPN z bramą](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Konfiguracje zapory i filtrowanie adresów IP bramy

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory dla sieci lokalnej/prywatnej  
W przedsiębiorstwie **firmowa Zapora** jest uruchamiana na centralnym routerze organizacji. Ponadto **Zapora systemu Windows** jest uruchamiana jako demon na komputerze lokalnym, na którym zainstalowano bramę. 

W poniższej tabeli przedstawiono wymagania dotyczące **portów wychodzących** i domen dla **zapory firmowej**.

| Nazwy domen | Porty wychodzące | Opis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Wymagane przez bramę do nawiązywania połączenia z usługami przenoszenia danych w Data Factory |
| `*.core.windows.net` | 443 | Używane przez bramę do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) . | 
| `*.frontend.clouddatahub.net` | 443 | Wymagane przez bramę do nawiązywania połączenia z usługą Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Opcjonalnie) wymagana, gdy lokalizacja docelowa to Azure SQL Database/analiza Synapse Azure. Funkcja kopiowania przemieszczania służy do kopiowania danych do usługi Azure SQL Database/Azure Synapse Analytics bez konieczności otwierania portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (Opcjonalnie) wymagana, gdy lokalizacja docelowa to Magazyn Azure Data Lake | 

> [!NOTE] 
> Może być konieczne zarządzanie portami/filtrowanie domen na poziomie zapory firmowej zgodnie z wymaganiami odpowiednich źródeł danych. Ta tabela używa tylko Azure SQL Database usługi Azure Synapse Analytics, Azure Data Lake Store jako przykłady.   

W poniższej tabeli przedstawiono wymagania dotyczące **portów ruchu przychodzącego** dla **zapory systemu Windows**.

| Porty wejściowe | Opis | 
| ------------- | ----------- | 
| 8050 (TCP) | Wymagane przez aplikację Menedżer poświadczeń do bezpiecznego ustawiania poświadczeń dla lokalnych magazynów danych w bramie. | 

![Wymagania dotyczące portów bramy](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>Konfiguracje/filtrowanie adresów IP w magazynie danych
Niektóre magazyny danych w chmurze wymagają również zatwierdzenia adresu IP komputera, do którego uzyskuje dostęp. Upewnij się, że adres IP komputera bramy jest odpowiednio zatwierdzony/skonfigurowany w zaporze.

Następujące magazyny danych w chmurze wymagają zatwierdzenia adresu IP maszyny bramy. Niektóre z tych magazynów danych domyślnie mogą nie wymagać zatwierdzania adresu IP. 

- [Azure SQL Database](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie:** Czy brama może być współużytkowana przez różne fabryki danych?
**Odpowiedź:** Ta funkcja nie jest jeszcze obsługiwana. Aktywnie nad nią pracujemy.

**Pytanie:** Jakie są wymagania dotyczące portów, które mają być wykonywane przez bramę?
**Odpowiedź:** Brama umożliwia nawiązywanie połączeń z Internetem przy użyciu protokołu HTTP. **Porty wychodzące 443 i 80** muszą zostać otwarte dla bramy w celu nawiązania połączenia. Otwórz **port Przychodzący 8050** tylko na poziomie komputera (nie na poziomie zapory firmowej) dla aplikacji Menedżer poświadczeń. Jeśli Azure SQL Database lub usługa Azure Synapse Analytics jest używana jako źródło/miejsce docelowe, należy również otworzyć port **1433** . Aby uzyskać więcej informacji, zobacz sekcję [konfiguracje zapory i filtrowanie adresów IP](#firewall-configurations-and-filtering-ip-address-of gateway) . 

**Pytanie:** Jakie są wymagania dotyczące certyfikatów dla bramy?
**Odpowiedź:** Bieżąca Brama wymaga certyfikatu, który jest używany przez aplikację Menedżer poświadczeń do bezpiecznego ustawiania poświadczeń magazynu danych. Ten certyfikat jest certyfikatem z podpisem własnym utworzonym i skonfigurowanym przez Instalatora bramy. Zamiast tego możesz użyć własnego certyfikatu TLS/SSL. Aby uzyskać więcej informacji, zobacz sekcję kliknij jednokrotne [polecenie aplikacji Menedżer poświadczeń](#click-once-credentials-manager-app) . 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o wydajności działania kopiowania, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).
