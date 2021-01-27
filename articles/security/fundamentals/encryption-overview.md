---
title: Omówienie usługi Azure Encryption | Microsoft Docs
description: Dowiedz się więcej na temat opcji szyfrowania na platformie Azure. Zapoznaj się z informacjami na temat szyfrowania przechowywanych danych, szyfrowania w locie i zarządzania kluczami przy użyciu Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879718"
---
# <a name="azure-encryption-overview"></a>Omówienie szyfrowania na platformie Azure

Ten artykuł zawiera omówienie sposobu użycia szyfrowania w Microsoft Azure. Obejmuje to główne obszary szyfrowania, w tym szyfrowanie w spoczynku, szyfrowanie w locie i zarządzanie kluczami za pomocą Azure Key Vault. Każda sekcja zawiera linki do bardziej szczegółowych informacji.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych magazynowanych

Dane przechowywane w programie obejmują informacje, które znajdują się w magazynie trwałym na nośniku fizycznym, w dowolnym formacie cyfrowym. Nośnik może zawierać pliki na nośnikach magnetycznych lub optycznych, zarchiwizowane dane i kopie zapasowe danych. Microsoft Azure oferuje różne rozwiązania do magazynowania danych, które są zgodne z różnymi potrzebami, w tym plikami, dyskami, obiektami BLOB i magazynem tabel. Firma Microsoft udostępnia również szyfrowanie w celu ochrony [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)i Azure Data Lake.

Szyfrowanie danych w spoczynku jest dostępne dla usług dla modeli w chmurze (oprogramowanie jako usługa), platforma jako usługa (PaaS) i infrastruktura jako usługa (IaaS). Ten artykuł zawiera podsumowanie i zasoby ułatwiające korzystanie z opcji szyfrowania platformy Azure.

Aby zapoznać się z bardziej szczegółowym omówieniem, jak dane przechowywane są szyfrowane na platformie Azure, zobacz [szyfrowanie danych platformy Azure w spoczynku](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modele szyfrowania platformy Azure

Platforma Azure obsługuje różne modele szyfrowania, w tym szyfrowanie po stronie serwera, które używa kluczy zarządzanych przez usługę, kluczy zarządzanych przez klienta w Key Vault lub kluczy zarządzanych przez klienta na sprzęcie kontrolowanym przez klienta. W przypadku szyfrowania po stronie klienta można zarządzać kluczami i przechowywać je lokalnie lub w innej bezpiecznej lokalizacji.

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Szyfrowanie po stronie klienta jest wykonywane poza platformą Azure. Obejmuje:

- Dane zaszyfrowane przez aplikację uruchomioną w centrum danych klienta lub przez aplikację usługi.
- Dane, które są już zaszyfrowane, gdy są odbierane przez platformę Azure.

Dzięki szyfrowaniu po stronie klienta dostawcy usług w chmurze nie mają dostępu do kluczy szyfrowania i nie mogą odszyfrować tych danych. Zachowujesz pełną kontrolę nad kluczami.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Trzy modele szyfrowania po stronie serwera oferują różne charakterystyki zarządzania kluczami, które można wybrać zgodnie z wymaganiami:

- **Klucze zarządzane przez usługę**: zapewniają kombinację kontroli i wygody z niskim obciążeniem.

- **Klucze zarządzane przez klienta**: zapewnia kontrolę nad kluczami, w tym przeprowadzenie obsługi własnych kluczy (BYOK), lub umożliwia generowanie nowych.

- **Klucze zarządzane przez klienta**: umożliwiają zarządzanie kluczami w repozytorium zastrzeżonym poza kontrolą firmy Microsoft. Ta cecha jest nazywana własnym kluczem hosta (HYOK). Jednak konfiguracja jest złożona i większość usług platformy Azure nie obsługuje tego modelu.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków Azure

Maszyny wirtualne z systemem Windows i Linux można chronić za pomocą [usługi Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md), która korzysta z technologii [Windows BitLocker i systemu](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) do ochrony zarówno dysków systemu operacyjnego, jak i dysków danych z pełnym szyfrowaniem woluminów.

Klucze szyfrowania i wpisy tajne są chronione w [subskrypcji Azure Key Vault](../../key-vault/general/overview.md). Za pomocą usługi Azure Backup można tworzyć kopie zapasowe i przywracać zaszyfrowane maszyny wirtualne (VM) korzystające z konfiguracji klucza szyfrowania kluczy (KEK).

### <a name="azure-storage-service-encryption"></a>szyfrowanie usługi Storage platformy Azure

Dane przechowywane w usłudze Azure Blob Storage i udziały plików platformy Azure mogą być szyfrowane zarówno w scenariuszach po stronie serwera, jak i po stronie klienta.

[Usługa Azure szyfrowanie usługi Storage (SSE)](../../storage/common/storage-service-encryption.md) może automatycznie szyfrować dane przed ich zapisaniem i automatycznie odszyfrowuje dane podczas ich pobierania. Ten proces jest całkowicie niewidoczny dla użytkowników. Szyfrowanie usługi Storage używa szyfrowania 256-bitowego [Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), które jest jednym z najsilniejszych szyfrów blokowych. AES obsługuje szyfrowanie, odszyfrowywanie i zarządzanie kluczami w niewidoczny sposób.

### <a name="client-side-encryption-of-azure-blobs"></a>Szyfrowanie po stronie klienta obiektów blob platformy Azure

Szyfrowanie po stronie klienta obiektów blob platformy Azure można wykonać na różne sposoby.

Możesz użyć biblioteki klienta usługi Azure Storage dla pakietu NuGet platformy .NET do szyfrowania danych w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage.

Aby dowiedzieć się więcej na temat i pobrać bibliotekę klienta usługi Azure Storage dla pakietu NuGet programu .NET, zobacz [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

W przypadku korzystania z szyfrowania po stronie klienta w programie Key Vault dane są szyfrowane przy użyciu jednorazowego klucza szyfrowania zawartości (CEK), który jest generowany przez zestaw SDK klienta usługi Azure Storage. CEK jest szyfrowany przy użyciu klucza szyfrowania klucza (KEK), który może być kluczem symetrycznym lub parę kluczy asymetrycznych. Można nim zarządzać lokalnie lub przechowywać ją w Key Vault. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

Aby dowiedzieć się więcej o szyfrowaniu po stronie klienta Key Vault i rozpoczynaniu pracy z instrukcjami, zobacz [Samouczek: szyfrowanie i odszyfrowywanie obiektów BLOB w usłudze Azure Storage przy użyciu Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Na koniec można także użyć biblioteki klienta usługi Azure Storage dla języka Java, aby przeprowadzić szyfrowanie po stronie klienta przed przekazaniem danych do usługi Azure Storage oraz odszyfrowanie danych podczas pobierania ich do klienta. Ta biblioteka obsługuje także integrację z usługą [Key Vault](https://azure.microsoft.com/services/key-vault/) dla zarządzania kluczami konta magazynu.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Szyfrowanie danych magazynowanych przy użyciu Azure SQL Database

[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) to usługa relacyjnej bazy danych ogólnego przeznaczenia na platformie Azure, która obsługuje struktury, takie jak dane relacyjne, JSON, przestrzenne i XML. SQL Database obsługuje szyfrowanie po stronie serwera za pomocą funkcji Transparent Data Encryption (TDE) i szyfrowania po stronie klienta za pośrednictwem funkcji Always Encrypted.

#### <a name="transparent-data-encryption"></a>Niewidoczne szyfrowanie danych

[TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) jest używany do szyfrowania plików danych programu [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) w czasie rzeczywistym przy użyciu klucza szyfrowania bazy danych, który jest przechowywany w rekordzie rozruchowym bazy danych w celu zapewnienia dostępności podczas odzyskiwania.

TDE chroni pliki danych i dziennika przy użyciu algorytmów szyfrowania AES i Triple Data Encryption Standard (3DES). Szyfrowanie pliku bazy danych odbywa się na poziomie strony. Strony w zaszyfrowanej bazie danych są szyfrowane przed zapisaniem ich na dysku i są odszyfrowywane, gdy są one odczytywane w pamięci. Funkcja TDE jest teraz domyślnie włączona w nowo utworzonych bazach danych SQL platformy Azure.

#### <a name="always-encrypted-feature"></a>Funkcja Always Encrypted

Funkcja [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) w usłudze Azure SQL umożliwia szyfrowanie danych w aplikacjach klienckich przed ich przechowywaniem w Azure SQL Database. Można także włączyć delegowanie lokalnego administrowania bazą danych do stron trzecich i zachować rozdzielenie między tymi, które są właścicielami i mogą wyświetlać dane oraz tymi, które zarządzają nimi, ale nie powinni mieć do nich dostępu.

#### <a name="cell-level-or-column-level-encryption"></a>Szyfrowanie na poziomie komórki lub na poziomie kolumny

Za pomocą Azure SQL Database można zastosować szyfrowanie symetryczne do kolumny danych przy użyciu języka Transact-SQL. Takie podejście nazywa się [szyfrowaniem na poziomie komórki lub szyfrowaniem na poziomie kolumny (cle)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), ponieważ można go użyć do szyfrowania określonych kolumn lub nawet określonych komórek danych z różnymi kluczami szyfrowania. Zapewnia to bardziej szczegółową funkcję szyfrowania niż TDE, która szyfruje dane na stronach.

CLE ma wbudowane funkcje, których można użyć do szyfrowania danych przy użyciu kluczy symetrycznych lub asymetrycznych, klucza publicznego certyfikatu lub hasła przy użyciu algorytmu 3DES.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB szyfrowanie bazy danych

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) to globalnie dystrybuowana, wielomodelowa baza danych firmy Microsoft. Dane użytkownika przechowywane w Cosmos DB w magazynie nietrwałym (dyski półprzewodnikowe) są domyślnie szyfrowane. Brak kontrolek do włączenia lub wyłączenia. Szyfrowanie w spoczynku jest implementowane przy użyciu wielu technologii zabezpieczeń, w tym bezpiecznych systemów magazynowania kluczy, szyfrowanych sieci i kryptograficznych interfejsów API. Klucze szyfrowania są zarządzane przez firmę Microsoft i obracane zgodnie ze wskazówkami wewnętrznymi firmy Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Szyfrowanie w usłudze REST w Data Lake

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) jest repozytorium w całym przedsiębiorstwie dla każdego typu danych zbieranych w jednym miejscu przed żadną formalną definicją wymagań lub schematu. Data Lake Store obsługuje "domyślnie włączone" przezroczyste szyfrowanie danych w spoczynku, które zostały skonfigurowane podczas tworzenia konta. Domyślnie program Azure Data Lake Store zarządza kluczami, ale istnieje możliwość ich samodzielnego zarządzania.

Do szyfrowania i odszyfrowywania danych używane są trzy typy kluczy: główny klucz szyfrowania (główny klucz szyfrowania), klucz szyfrowania danych (.) i klucz szyfrowania bloku (klucz szyfrowania bloków). GŁÓWNY klucz szyfrowania jest używany do szyfrowania klucza danych firmy, który jest przechowywany na nośniku trwałym, a klucz szyfrowania bloków jest uzyskiwany z tego klucza. Jeśli zarządzasz własnymi kluczami, możesz obrócić główny klucz szyfrowania.

## <a name="encryption-of-data-in-transit"></a>Szyfrowanie danych podczas przesyłania

Platforma Azure oferuje wiele mechanizmów utrzymywania prywatnych danych w trakcie ich przenoszenia z jednej lokalizacji do innej.

### <a name="data-link-layer-encryption-in-azure"></a>Szyfrowanie warstwy linku danych na platformie Azure

Za każdym razem, gdy ruch klientów platformy Azure jest przenoszony między centrami danych — poza granicami fizycznymi niekontrolowanymi przez firmę Microsoft (lub w imieniu firmy Microsoft) — Metoda szyfrowania warstwy linku z użyciem [IEEE 802.1 AE](https://1.ieee802.org/security/802-1ae/) (znana także jako MACsec) jest stosowana między punkt-punkt w ramach podstawowego sprzętu sieciowego. Pakiety są szyfrowane i odszyfrowywane na urządzeniach przed ich wysłaniem, co uniemożliwia fizyczne ataki typu "man-in-the-middle" lub "śledzenie/wiretapping". Ponieważ ta technologia jest zintegrowana z samym sprzętem sieciowym, zapewnia szyfrowanie przy użyciu stawki liniowej sprzętu sieciowego bez wymiernego wzrostu opóźnienia linków. To szyfrowanie MACsec jest domyślnie włączone dla całego ruchu platformy Azure podróżującego w obrębie regionu lub między regionami, a żadna akcja nie jest wymagana przez część klientów do włączenia. 

### <a name="tls-encryption-in-azure"></a>Szyfrowanie TLS na platformie Azure

Firma Microsoft zapewnia klientom możliwość korzystania z protokołu [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) w celu ochrony danych podczas podróży między usługami w chmurze i klientami. Centra danych firmy Microsoft negocjują połączenie TLS z systemami klienckimi, które łączą się z usługami platformy Azure. Protokół TLS zapewnia silne uwierzytelnianie, prywatność komunikatów i integralność (umożliwiając wykrywanie manipulowania komunikatami, przechwycenie i fałszowanie), współdziałanie, elastyczność algorytmu oraz łatwość wdrażania i używania.

[Doskonałe](https://en.wikipedia.org/wiki/Forward_secrecy) utajnienie przekazywania (PFS) chroni połączenia między systemami klienta i usługami w chmurze firmy Microsoft przez unikatowe klucze. Połączenia korzystają również z 2 048-bitowych kluczy szyfrowania RSA. Ta kombinacja utrudnia przechwycenie i dostęp do danych przesyłanych przez inną osobę.

### <a name="azure-storage-transactions"></a>Transakcje usługi Azure Storage

W przypadku korzystania z usługi Azure Storage za pośrednictwem Azure Portal wszystkie transakcje odbywają się za pośrednictwem protokołu HTTPS. Możesz również użyć interfejsu API REST usługi Storage za pośrednictwem protokołu HTTPS, aby współdziałać z usługą Azure Storage. Można wymusić użycie protokołu HTTPS podczas wywoływania interfejsów API REST w celu uzyskania dostępu do obiektów w kontach magazynu, włączając bezpieczny transfer wymagany dla konta magazynu.

Sygnatury dostępu współdzielonego ([SAS](../../storage/common/storage-sas-overview.md)), które mogą służyć do delegowania dostępu do obiektów usługi Azure Storage, obejmują opcję określającą, że podczas korzystania z sygnatur dostępu współdzielonego można używać tylko protokołu HTTPS. Takie podejście zapewnia, że każdy, który wysyła linki z tokenami SAS, używa odpowiedniego protokołu.

Protokół [SMB 3,0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), który służy do uzyskiwania dostępu do Azure Files udziałów, obsługuje szyfrowanie i jest dostępny w systemie windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10. Umożliwia dostęp między regionami, a nawet dostęp na pulpicie.

Szyfrowanie po stronie klienta szyfruje dane przed wysłaniem ich do wystąpienia usługi Azure Storage, dzięki czemu jest zaszyfrowana w sieci.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Szyfrowanie SMB za pośrednictwem sieci wirtualnych platformy Azure 

Korzystając z [protokołu SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) na maszynach wirtualnych z systemem Windows Server 2012 lub nowszym, można zabezpieczyć transfery danych, szyfrując dane przesyłane za pośrednictwem sieci wirtualnych platformy Azure. Szyfrowanie danych pomaga chronić przed manipulacjami i podsłuchiwaniem ataków. Administratorzy mogą włączyć szyfrowanie SMB dla całego serwera lub tylko określonych udziałów.

Domyślnie po włączeniu szyfrowania SMB dla udziału lub serwera, tylko klienci SMB 3,0 mogą uzyskać dostęp do udziałów szyfrowanych.

## <a name="in-transit-encryption-in-vms"></a>Szyfrowanie w trakcie przesyłania na maszynach wirtualnych

Dane przesyłane do, z i między maszynami wirtualnymi z systemem Windows, mogą być szyfrowane na wiele sposobów, w zależności od charakteru połączenia.

### <a name="rdp-sessions"></a>Sesje protokołu RDP

Można nawiązać połączenie z maszyną wirtualną i zalogować się do niej przy użyciu [Remote Desktop Protocol (RDP)](/windows/win32/termserv/remote-desktop-protocol) z komputera klienckiego z systemem Windows lub z komputerów Mac z zainstalowanym klientem RDP. Dane przesyłane przez sieć w ramach sesji protokołu RDP mogą być chronione przy użyciu protokołu TLS.

Możesz również użyć Pulpit zdalny, aby nawiązać połączenie z maszyną wirtualną z systemem Linux na platformie Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Bezpieczny dostęp do maszyn wirtualnych z systemem Linux przy użyciu protokołu SSH

Do zarządzania zdalnego można używać [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) do nawiązywania połączeń z maszynami wirtualnymi z systemem Linux działającymi na platformie Azure. SSH to protokół połączenia szyfrowanego, który umożliwia bezpieczne logowanie za pośrednictwem niezabezpieczonych połączeń. Jest to domyślny protokół połączeń dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Przy użyciu kluczy SSH do uwierzytelniania należy wyeliminować konieczność zalogowania się haseł. Protokół SSH używa pary kluczy publiczny/prywatny (szyfrowanie asymetryczne) do uwierzytelniania.

## <a name="azure-vpn-encryption"></a>Szyfrowanie sieci VPN platformy Azure

Możesz połączyć się z platformą Azure za pośrednictwem wirtualnej sieci prywatnej, która tworzy bezpieczny tunel, aby chronić prywatność danych przesyłanych przez sieć.

### <a name="azure-vpn-gateways"></a>Bramy sieci VPN platformy Azure

Za pomocą [bramy sieci VPN platformy Azure](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) można wysyłać zaszyfrowany ruch sieciowy między siecią wirtualną i lokalizacją lokalną w ramach połączenia publicznego lub do wysyłania ruchu między sieciami wirtualnymi.

Sieci VPN typu lokacja-lokacja używają [protokołu IPSec](https://en.wikipedia.org/wiki/IPsec) do szyfrowania transportowego. Bramy sieci VPN platformy Azure używają zestawu domyślnych propozycji. Bramy sieci VPN platformy Azure można skonfigurować tak, aby używały niestandardowych zasad protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i silnymi kluczami, a nie z domyślnymi zestawami zasad platformy Azure.

### <a name="point-to-site-vpns"></a>Sieci VPN typu punkt-lokacja

Sieci VPN typu punkt-lokacja umożliwiają indywidualnym komputerom klienckim dostęp do sieci wirtualnej platformy Azure. [Protokół SSTP (Secure Socket Tunneling Protocol)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) służy do tworzenia tunelu sieci VPN. Może przechodzić przez zapory (tunel jest wyświetlany jako połączenie HTTPS). Do połączeń punkt-lokacja można użyć własnego głównego urzędu certyfikacji infrastruktury kluczy publicznych (PKI).

Można skonfigurować połączenie sieci VPN typu punkt-lokacja z siecią wirtualną przy użyciu Azure Portal z uwierzytelnianiem certyfikatu lub programem PowerShell.

Aby dowiedzieć się więcej o połączeniach sieci VPN typu punkt-lokacja z sieciami wirtualnymi platformy Azure, zobacz:

[Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu uwierzytelniania certyfikacji: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu uwierzytelniania certyfikatu: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Sieci VPN typu lokacja-lokacja 

Możesz użyć połączenia bramy sieci VPN typu lokacja-lokacja, aby połączyć sieć lokalną z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN protokołu IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga, aby lokalne urządzenie sieci VPN, które ma przypisany zewnętrznie publiczny adres IP.

Połączenie sieci VPN typu lokacja-lokacja można skonfigurować w sieci wirtualnej przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji, zobacz:

[Utwórz połączenie lokacja-lokacja w Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Tworzenie połączenia typu lokacja-lokacja w programie PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu interfejsu wiersza polecenia](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Szyfrowanie w trakcie przesyłania w Data Lake

Dane przesyłane (inaczej dane w ruchu) również są zawsze szyfrowane w usłudze Data Lake Store. Oprócz szyfrowania danych przed ich przechowywaniem na nośniku trwałym dane są również zawsze zabezpieczane podczas przesyłania przy użyciu protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST usługi Data Lake Store.

Aby dowiedzieć się więcej o szyfrowaniu danych podczas przesyłania Data Lake, zobacz [szyfrowanie danych w Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Zarządzanie kluczami za pomocą Key Vault

Bez właściwej ochrony i zarządzania kluczami szyfrowanie jest renderowane jako bezużyteczny. Key Vault to rozwiązanie zalecane przez firmę Microsoft służące do zarządzania dostępem do kluczy szyfrowania używanych przez usługi Cloud Services i kontrolowania dostępu do nich. Uprawnienia dostępu do kluczy mogą być przypisywane do usług lub użytkownikom przy użyciu kont Azure Active Directory.

Usługa Key Vault pozwala organizacjom zrezygnować z konfigurowania, poprawiania oraz utrzymywania modułów HSM i oprogramowania do zarządzania kluczami. W przypadku korzystania z Key Vault należy zachować kontrolę. Firma Microsoft nigdy nie widzi kluczy, a aplikacje nie mają bezpośredniego dostępu do nich. Możesz również importować lub generować klucze w sprzętowych modułów zabezpieczeń.

## <a name="next-steps"></a>Następne kroki

- [Przegląd zabezpieczeń platformy Azure](./overview.md)
- [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)
- [Omówienie zabezpieczeń usługi Azure Database](../../azure-sql/database/security-overview.md)
- [Omówienie zabezpieczeń usługi Azure Virtual Machines](virtual-machines-overview.md)
- [Szyfrowanie danych w spoczynku](encryption-atrest.md)
- [Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych](data-encryption-best-practices.md)