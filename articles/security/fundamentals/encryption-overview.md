---
title: Omówienie szyfrowania platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o różnych opcjach szyfrowania na platformie Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: mbaldwin
ms.openlocfilehash: ce78ade4df3c5bcea9e4e44750c430065cbfc5b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454649"
---
# <a name="azure-encryption-overview"></a>Omówienie szyfrowania platformy Azure

Ten artykuł zawiera omówienie sposobu użycia szyfrowania na platformie Microsoft Azure. Obejmuje główne obszary szyfrowania, w tym szyfrowanie w spoczynku, szyfrowanie w locie i zarządzanie kluczami za pomocą usługi Azure Key Vault. Każda sekcja zawiera łącza do bardziej szczegółowych informacji.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych w stanie spoczynku

Dane w stanie spoczynku obejmują informacje, które znajdują się w trwałej pamięci masowej na nośnikach fizycznych, w dowolnym formacie cyfrowym. Nośnik może zawierać pliki na nośnikach magnetycznych lub optycznych, zarchiwizowanych danych i kopie zapasowe danych. Platforma Microsoft Azure oferuje wiele rozwiązań do przechowywania danych, aby spełnić różne potrzeby, w tym pliki, dysk, obiekt blob i magazyn tabel. Firma Microsoft zapewnia również szyfrowanie w celu ochrony [usługi Azure SQL Database](../../sql-database/sql-database-technical-overview.md), Usługi Azure [Cosmos DB](../../data-factory/introduction.md)i usługi Azure Data Lake.

Szyfrowanie danych w stanie spoczynku jest dostępne dla usług w całym oprogramowaniu jako usługi (SaaS), platformie jako usługi (PaaS) i modelach chmury infrastruktury jako usługi (IaaS). W tym artykule podsumowano i udostępnia zasoby ułatwiające korzystanie z opcji szyfrowania platformy Azure.

Aby uzyskać bardziej szczegółowe omówienie sposobu szyfrowania danych w stanie spoczynku na platformie Azure, zobacz [Szyfrowanie danych platformy Azure w spoczynku.](encryption-atrest.md)

## <a name="azure-encryption-models"></a>Modele szyfrowania platformy Azure

Platforma Azure obsługuje różne modele szyfrowania, w tym szyfrowanie po stronie serwera, które używa kluczy zarządzanych przez usługę, kluczy zarządzanych przez klienta w usłudze Key Vault lub kluczy zarządzanych przez klienta na sprzęcie kontrolowanym przez klienta. Za pomocą szyfrowania po stronie klienta można zarządzać kluczami lokalnymi i przechowywać je w innej bezpiecznej lokalizacji.

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Szyfrowanie po stronie klienta jest wykonywane poza platformą Azure. Obejmuje ona:

- Dane zaszyfrowane przez aplikację, która jest uruchomiona w centrum danych klienta lub przez aplikację usługi.
- Dane, które są już szyfrowane po odebraniu ich przez platformę Azure.

Dzięki szyfrowaniu po stronie klienta dostawcy usług w chmurze nie mają dostępu do kluczy szyfrowania i nie mogą odszyfrować tych danych. Zachowujesz pełną kontrolę nad klawiszami.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Trzy modele szyfrowania po stronie serwera oferują różne cechy zarządzania kluczami, które można wybrać zgodnie z wymaganiami:

- **Klucze zarządzane przez serwis:** Zapewnia połączenie kontroli i wygody przy niskim obciążeniu.

- **Klucze zarządzane przez klienta:** Daje kontrolę nad kluczami, w tym Bring Your Own Keys (BYOK) wsparcie, lub pozwala na generowanie nowych.

- **Klucze zarządzane przez usługę w sprzęcie kontrolowanym przez klienta:** Umożliwia zarządzanie kluczami w zastrzeżonym repozytorium, poza kontrolą firmy Microsoft. Ta cecha nazywa się Host Your Own Key (HYOK). Jednak konfiguracja jest złożona, a większość usług platformy Azure nie obsługuje tego modelu.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków platformy Azure

Maszyny wirtualne systemu Windows i Linux można chronić za pomocą [szyfrowania dysków platformy Azure,](/azure/security/fundamentals/azure-disk-encryption-vms-vmss)które wykorzystuje technologię [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) i Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do ochrony zarówno dysków systemu operacyjnego, jak i dysków danych za pomocą pełnego szyfrowania woluminów.

Klucze szyfrowania i wpisy tajne są chronione w [subskrypcji usługi Azure Key Vault.](../../key-vault/general/overview.md) Korzystając z usługi Azure Backup, można wykonać kopię zapasową i przywrócić zaszyfrowane maszyny wirtualne (VM), które używają konfiguracji klucza szyfrowania klucza (KEK).

### <a name="azure-storage-service-encryption"></a>Szyfrowanie usługi Azure Storage

Dane w stanie Spoczynku w magazynie obiektów Blob platformy Azure i udziałów plików platformy Azure mogą być szyfrowane zarówno w scenariuszach po stronie serwera, jak i po stronie klienta.

[Szyfrowanie usługi Azure Storage Service (SSE)](../../storage/common/storage-service-encryption.md) może automatycznie szyfrować dane przed ich przechowywaniem i automatycznie odszyfrowuje dane podczas ich pobierania. Proces jest całkowicie przejrzysty dla użytkowników. Szyfrowanie usługi pamięci masowej wykorzystuje 256-bitowe [szyfrowanie Advanced Encryption Standard (AES),](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)które jest jednym z najsilniejszych dostępnych szyfrów blokowych. AES obsługuje szyfrowanie, odszyfrowywanie i zarządzanie kluczami w sposób przejrzysty.

### <a name="client-side-encryption-of-azure-blobs"></a>Szyfrowanie po stronie klienta obiektów blob platformy Azure

Szyfrowanie obiektów blob platformy Azure po stronie klienta można wykonywać na różne sposoby.

Biblioteka klienta usługi Azure Storage dla pakietu NuGet .NET służy do szyfrowania danych w aplikacjach klienckich przed przekazaniem ich do magazynu platformy Azure.

Aby dowiedzieć się więcej o bibliotece klienta usługi Azure Storage dla pakietu NuGet usługi Azure Storage i pobrać ją, zobacz [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Podczas korzystania z szyfrowania po stronie klienta z Usługi Key Vault dane są szyfrowane przy użyciu jednorazowego symetrycznego klucza szyfrowania zawartości (CEK), który jest generowany przez zestaw SDK klienta usługi Azure Storage. CEK jest szyfrowany przy użyciu klucza szyfrowania klucza (KEK), który może być kluczem symetrycznym lub asymetryczną parą kluczy. Możesz zarządzać nim lokalnie lub przechowywać w magazynie kluczy. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

Aby dowiedzieć się więcej na temat szyfrowania po stronie klienta za pomocą usługi Key Vault i rozpocząć pracę z instrukcjami, zobacz [Samouczek: Szyfrowanie i odszyfrowywanie obiektów blob w usłudze Azure Storage przy użyciu usługi Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Na koniec można również użyć biblioteki klienta usługi Azure Storage dla środowiska Java do wykonywania szyfrowania po stronie klienta przed przekazaniem danych do usługi Azure Storage i odszyfrować dane podczas pobierania ich do klienta. Ta biblioteka obsługuje również integrację z [usługą Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami konta magazynu.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Szyfrowanie danych w spoczynku za pomocą usługi Azure SQL Database

[Usługa Azure SQL Database](../../sql-database/sql-database-technical-overview.md) to usługa relacyjnej bazy danych ogólnego przeznaczenia na platformie Azure, która obsługuje struktury, takie jak dane relacyjne, JSON, przestrzenne i XML. Baza danych SQL obsługuje zarówno szyfrowanie po stronie serwera za pośrednictwem funkcji przezroczystego szyfrowania danych (TDE), jak i szyfrowanie po stronie klienta za pośrednictwem funkcji Zawsze szyfrowane.

#### <a name="transparent-data-encryption"></a>Niewidoczne szyfrowanie danych

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) jest używany do szyfrowania [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), Azure [SQL Database](../../sql-database/sql-database-technical-overview.md)i Usługi Azure SQL Data [Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) plików danych w czasie rzeczywistym, przy użyciu klucza szyfrowania bazy danych (DEK), który jest przechowywany w rekordzie rozruchu bazy danych dla dostępności podczas odzyskiwania.

TDE chroni pliki danych i dzienników, korzystając z algorytmów szyfrowania AES i Triple Data Encryption Standard (3DES). Szyfrowanie pliku bazy danych jest wykonywane na poziomie strony. Strony w zaszyfrowanej bazie danych są szyfrowane przed zapisaniem ich na dysku i odszyfrowywane po ich odczytaniu do pamięci. TDE jest teraz domyślnie włączona w nowo utworzonych bazach danych SQL platformy Azure.

#### <a name="always-encrypted-feature"></a>Funkcja zawsze szyfrowana

Funkcja [Zawsze szyfrowana](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) w usłudze Azure SQL umożliwia szyfrowanie danych w aplikacjach klienckich przed zapisaniem ich w bazie danych SQL azure. Można również włączyć delegowanie administracji lokalnej bazy danych do stron trzecich i zachować separację między tymi, którzy są właścicielami i mogą przeglądać dane, a tymi, którzy nimi zarządzają, ale nie powinni mieć do nich dostępu.

#### <a name="cell-level-or-column-level-encryption"></a>Szyfrowanie na poziomie komórki lub kolumny

Za pomocą usługi Azure SQL Database można zastosować szyfrowanie symetryczne do kolumny danych przy użyciu usługi Transact-SQL. Takie podejście jest nazywane [szyfrowaniem na poziomie komórki lub szyfrowaniem na poziomie kolumny (CLE),](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)ponieważ można go używać do szyfrowania określonych kolumn lub nawet określonych komórek danych za pomocą różnych kluczy szyfrowania. W ten sposób zapewnia bardziej szczegółowe możliwości szyfrowania niż TDE, który szyfruje dane na stronach.

Cle posiada wbudowane funkcje, których można używać do szyfrowania danych przy użyciu kluczy symetrycznych lub asymetrycznych, klucza publicznego certyfikatu lub hasła przy użyciu 3DES.

### <a name="cosmos-db-database-encryption"></a>Szyfrowanie bazy danych usługi Cosmos DB

[Usługa Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) to globalnie rozproszona, wielomodelowa baza danych firmy Microsoft. Dane użytkownika przechowywane w usłudze Cosmos DB w magazynie nieulotnym (dyski PÓŁPRZEWODNIKOWE) są domyślnie szyfrowane. Nie ma żadnych elementów sterujących, aby go włączyć lub wyłączyć. Szyfrowanie w spoczynku jest implementowane przy użyciu wielu technologii zabezpieczeń, w tym bezpiecznych systemów przechowywania kluczy, zaszyfrowanych sieci i kryptograficznych interfejsów API. Klucze szyfrowania są zarządzane przez firmę Microsoft i są obracane zgodnie z wewnętrznymi wytycznymi firmy Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Szyfrowanie w spoczynku w umiań danych

[Usługa Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) to repozytorium dla całego przedsiębiorstwa każdego typu danych zebranych w jednym miejscu przed formalną definicją wymagań lub schematu. Data Lake Store obsługuje "domyślnie włączone", przejrzyste szyfrowanie danych w spoczynku, które jest skonfigurowane podczas tworzenia konta. Domyślnie usługa Azure Data Lake Store zarządza kluczami, ale masz możliwość samodzielnego zarządzania nimi.

W szyfrowaniu i odszyfrowywanie danych używane są trzy typy kluczy: główny klucz szyfrowania (MEK), klucz szyfrowania danych (DEK) i klucz szyfrowania bloku (BEK). MEK jest używany do szyfrowania DEK, który jest przechowywany na nośnikach trwałych, a BEK pochodzi od DEK i bloku danych. Jeśli zarządzasz własnymi kluczami, możesz obrócić MEK.

## <a name="encryption-of-data-in-transit"></a>Szyfrowanie przesyłanych danych

Platforma Azure oferuje wiele mechanizmów przechowywania danych w tajemnicy podczas przenoszenia danych z jednej lokalizacji do drugiej.

### <a name="tlsssl-encryption-in-azure"></a>Szyfrowanie TLS/SSL na platformie Azure

Firma Microsoft używa protokołu [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) do ochrony danych podczas podróży między usługami w chmurze a klientami. Centra danych firmy Microsoft negocjują połączenie TLS z systemami klienckimi, które łączą się z usługami platformy Azure. Protokół TLS zapewnia silne uwierzytelnianie, prywatność wiadomości i integralność (umożliwiając wykrywanie manipulowania, przechwytywania i fałszowania wiadomości), współdziałanie, elastyczność algorytmów oraz łatwość wdrażania i używania.

[Doskonałe utajnienie przekazywania](https://en.wikipedia.org/wiki/Forward_secrecy) danych (PFS) chroni połączenia między systemami klienckimi klientów a usługami w chmurze firmy Microsoft za pomocą unikatowych kluczy. Połączenia używają również 2048-bitowych kluczy szyfrowania opartych na rsa. Ta kombinacja utrudnia komuś przechwytywanie i dostęp do danych, które są w tranzycie.

### <a name="azure-storage-transactions"></a>Transakcje usługi Azure Storage

Podczas interakcji z usługą Azure Storage za pośrednictwem witryny Azure portal, wszystkie transakcje odbywają się za pośrednictwem protokołu HTTPS. Można również użyć interfejsu API REST magazynu za pośrednictwem protokołu HTTPS do interakcji z usługą Azure Storage. Można wymusić użycie protokołu HTTPS podczas wywoływania interfejsów API REST w celu uzyskania dostępu do obiektów na kontach magazynu, włączając bezpieczny transfer wymagany dla konta magazynu.

Sygnatury dostępu współdzielonego[(SAS),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)które mogą służyć do delegowania dostępu do obiektów usługi Azure Storage, zawierają opcję określającą, że podczas korzystania z podpisów dostępu współdzielonego można używać tylko protokołu HTTPS. Takie podejście gwarantuje, że każdy, kto wysyła łącza z tokenami sygnatury dostępu Współdzielonego używa odpowiedniego protokołu.

[Protokół SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), który służy do uzyskiwania dostępu do udziałów usługi Azure Files, obsługuje szyfrowanie i jest dostępny w systemach Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10. Umożliwia dostęp między regionami, a nawet dostęp na pulpicie.

Szyfrowanie po stronie klienta szyfruje dane przed wysłaniem ich do wystąpienia usługi Azure Storage, dzięki czemu są szyfrowane podczas przepływów przez sieć.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Szyfrowanie SMB za pośrednictwem sieci wirtualnych platformy Azure 

Korzystając z [modelu SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) na maszynach wirtualnych z systemem Windows Server 2012 lub nowszym, można zabezpieczyć transfery danych, szyfrując dane przesyłane przez sieci wirtualne platformy Azure. Szyfrując dane, pomagasz chronić przed atakami sabotażu i podsłuchiwania. Administratorzy mogą włączyć szyfrowanie SMB dla całego serwera lub tylko określone udziały.

Domyślnie po włączeniu szyfrowania SMB dla udziału lub serwera tylko klienci SMB 3.0 mogą uzyskiwać dostęp do zaszyfrowanych udziałów.

## <a name="in-transit-encryption-in-vms"></a>Szyfrowanie w tranzycie na maszynach wirtualnych

Dane przesyłane do, z i między maszynami wirtualnymi z systemem Windows są szyfrowane na wiele sposobów, w zależności od charakteru połączenia.

### <a name="rdp-sessions"></a>Sesje PROW

Można połączyć się i zalogować się do maszyny Wirtualnej przy użyciu [protokołu RDP (Remote Desktop Protocol)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) z komputera klienckiego systemu Windows lub komputera Mac z zainstalowanym klientem RDP. Dane przesyłane przez sieć w sesjach RDP mogą być chronione przez TLS.

Pulpit zdalny można również użyć do łączenia się z maszyną wirtualną z systemem Linux na platformie Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Bezpieczny dostęp do maszyn wirtualnych z systemem Linux dzięki SSH

Do zdalnego zarządzania można użyć [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) do łączenia się z maszynami wirtualnymi z systemem Linux uruchomionymi na platformie Azure. SSH to szyfrowany protokół połączenia, który umożliwia bezpieczne logowania za nawiązywanie niezabezpieczonych połączeń. Jest to domyślny protokół połączenia dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Korzystając z kluczy SSH do uwierzytelniania, eliminujesz potrzebę logowania się haseł. SSH używa pary kluczy publicznych/prywatnych (szyfrowanie asymetryczne) do uwierzytelniania.

## <a name="azure-vpn-encryption"></a>Szyfrowanie sieci VPN platformy Azure

Można połączyć się z platformą Azure za pośrednictwem wirtualnej sieci prywatnej, która tworzy bezpieczny tunel w celu ochrony prywatności danych wysyłanych przez sieć.

### <a name="azure-vpn-gateways"></a>Bramy sieci VPN platformy Azure

Za pomocą [bramy sieci VPN platformy Azure](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) można wysyłać zaszyfrowany ruch między siecią wirtualną a lokalizacją lokalną za pośrednictwem połączenia publicznego lub wysyłać ruch między sieciami wirtualnymi.

Sieci VPN typu lokacja lokacja używają [protokołu IPsec](https://en.wikipedia.org/wiki/IPsec) do szyfrowania transportu. Bramy sieci VPN platformy Azure używają zestawu propozycji domyślnych. Bramy sieci VPN platformy Azure można skonfigurować tak, aby używały niestandardowych zasad IPsec/IKE z określonymi algorytmami kryptograficznymi i mocnymi eniami kluczy, a nie domyślnymi zestawami zasad platformy Azure.

### <a name="point-to-site-vpns"></a>Sieci VPN typu punkt-lokacja

Sieci VPN typu punkt-lokacja umożliwiają poszczególnym komputerom klienckim dostęp do sieci wirtualnej platformy Azure. [Protokół SSTP (Secure Socket Tunneling Protocol)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) służy do tworzenia tunelu sieci VPN. Może przechodzić przez zapory (tunel jest wyświetlany jako połączenie HTTPS). Do łączności typu punkt-lokacja można używać własnego urzędu certyfikacji klucza publicznego (CKI) do korzystania z własnych wewnętrznych instytucji certyfikacji klucza publicznego (CKI).

Połączenie sieci VPN typu punkt-lokacja z siecią wirtualną można skonfigurować przy użyciu portalu Azure z uwierzytelnianiem certyfikatów lub programem PowerShell.

Aby dowiedzieć się więcej o połączeniach sieci VPN typu lokacja z sieciami wirtualnymi platformy Azure, zobacz:

[Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu uwierzytelniania certyfikacji: Witryna Azure portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu uwierzytelniania certyfikatów: Program PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Sieci VPN typu lokacja lokacja 

Za pomocą połączenia bramy sieci VPN między lokacjami można połączyć sieć lokalną z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN, które ma przypisany publiczny adres IP skierowany do zewnątrz.

Połączenie sieci VPN między lokacjami z siecią wirtualną można skonfigurować przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji, zobacz:

[Tworzenie połączenia lokacja-lokacja w witrynie Azure portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Tworzenie połączenia lokacja-lokacja w programie PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Tworzenie sieci wirtualnej z połączeniem sieci VPN między lokacjami przy użyciu interfejsu wiersza polecenia](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Szyfrowanie w tranzycie w umiań danych

Dane przesyłane (inaczej dane w ruchu) również są zawsze szyfrowane w usłudze Data Lake Store. Oprócz szyfrowania danych przed zapisaniem ich w mediach trwałych, dane są również zawsze zabezpieczone podczas przesyłania przy użyciu protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST usługi Data Lake Store.

Aby dowiedzieć się więcej o szyfrowaniu danych przesyłanych w umiańskim umiołów, zobacz [Szyfrowanie danych w sklepie Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Zarządzanie kluczami za pomocą usługi Key Vault

Bez odpowiedniej ochrony i zarządzania kluczami szyfrowanie staje się bezużyteczne. Key Vault to zalecane przez firmę Microsoft rozwiązanie do zarządzania i kontrolowania dostępu do kluczy szyfrowania używanych przez usługi w chmurze. Uprawnienia dostępu do kluczy można przypisać do usług lub do użytkowników za pośrednictwem kont usługi Azure Active Directory.

Usługa Key Vault zwalnia organizacje z konieczności konfigurowania, instalowania poprawek i obsługi sprzętowych modułów zabezpieczeń (HSM) i oprogramowania do zarządzania kluczami. Korzystając z usługi Key Vault, należy zachować kontrolę. Firma Microsoft nigdy nie widzi twoich kluczy, a aplikacje nie mają do nich bezpośredniego dostępu. Można również importować lub generować klucze w modułach HSM.

## <a name="next-steps"></a>Następne kroki

- [Przegląd zabezpieczeń platformy Azure](get-started-overview.md)
- [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)
- [Omówienie zabezpieczeń bazy danych platformy Azure](database-security-overview.md)
- [Omówienie zabezpieczeń maszyn wirtualnych platformy Azure](virtual-machines-overview.md)
- [Szyfrowanie danych w spoczynku](encryption-atrest.md)
- [Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych](data-encryption-best-practices.md)
