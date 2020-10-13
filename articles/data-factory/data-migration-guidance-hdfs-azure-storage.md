---
title: Migrowanie danych z lokalnego klastra Hadoop do usługi Azure Storage
description: Dowiedz się, jak używać Azure Data Factory do migrowania danych z lokalnego klastra Hadoop do usługi Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81417133"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Używanie Azure Data Factory do migrowania danych z lokalnego klastra Hadoop do usługi Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migrowania danych na dużą skalę z lokalnego systemu plików HDFS do magazynu obiektów blob platformy Azure lub Azure Data Lake Storage Gen2. 

Data Factory oferuje dwa podstawowe podejścia do migrowania danych z lokalnego systemu plików HDFS na platformę Azure. Możesz wybrać metodę opartą na Twoim scenariuszu. 

- **Data Factory tryb pomocą distcp** (zalecane): w Data Factory można użyć [pomocą distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (kopia rozproszona) do kopiowania plików jako-do magazynu obiektów blob platformy Azure (w tym [kopii przygotowanej](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) lub Azure Data Lake Store Gen2. Użyj Data Factory zintegrowane z pomocą distcp, aby skorzystać z istniejącego, wydajnego klastra, aby osiągnąć najlepszą przepustowość kopiowania. Możesz również skorzystać z elastycznego planowania i ujednoliconego środowiska monitorowania z poziomu Data Factory. W zależności od konfiguracji Data Factory działanie Copy automatycznie konstruuje polecenie pomocą distcp, przesyła dane do klastra usługi Hadoop, a następnie monitoruje stan kopiowania. Zalecamy Data Factory tryb pomocą distcp na potrzeby migrowania danych z lokalnego klastra Hadoop do platformy Azure.
- **Data Factory natywny tryb Integration Runtime**: pomocą distcp nie jest opcją we wszystkich scenariuszach. Na przykład w środowisku sieci wirtualnych platformy Azure narzędzie pomocą distcp nie obsługuje prywatnej komunikacji równorzędnej usługi Azure ExpressRoute z punktem końcowym sieci wirtualnej usługi Azure Storage. Ponadto w niektórych przypadkach nie chcesz używać istniejącego klastra usługi Hadoop jako aparatu do migrowania danych, aby nie umieszczać dużych obciążeń w klastrze, co może mieć wpływ na wydajność istniejących zadań ETL. Zamiast tego można użyć natywnej możliwości Data Factory Integration Runtime jako aparatu, który kopiuje dane z lokalnego systemu plików HDFS na platformę Azure.

Ten artykuł zawiera następujące informacje dotyczące obu metod:
> [!div class="checklist"]
> * Wydajność 
> * Odporność kopiowania
> * Bezpieczeństwo sieci
> * Architektura rozwiązania wysokiego poziomu 
> * Najlepsze rozwiązania w zakresie implementacji  

## <a name="performance"></a>Wydajność

W Data Factory trybie pomocą distcp przepływność jest taka sama jak w przypadku niezależnego użycia narzędzia pomocą distcp. Data Factory tryb pomocą distcp maksymalizuje pojemność istniejącego klastra Hadoop. Programu pomocą distcp można używać do kopiowania w dużych klastrach lub wewnątrz klastra. 

Pomocą distcp używa MapReduce do działania dystrybucji, obsługi błędów i odzyskiwania oraz raportowania. Rozszerza listę plików i katalogów na dane wejściowe dla mapowania zadań. Każde zadanie kopiuje partycję pliku określoną na liście źródłowej. Można użyć Data Factory zintegrowane z pomocą distcp, aby kompilować potoki w celu całkowitego wykorzystania przepustowości sieci, operacji we/wy na sekundę oraz przepustowości w celu zmaksymalizowania przepływności przenoszenia danych w danym środowisku.  

Data Factory tryb natywnej integracji środowiska uruchomieniowego umożliwia również równoległość na różnych poziomach. Równoległość umożliwia całkowite wykorzystanie przepustowości sieci, operacji we/wy na sekundę oraz przepustowości w celu zmaksymalizowania przepływności przenoszenia danych:

- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych. Przy użyciu własnego środowiska Integration Runtime możesz ręcznie skalować maszynę w górę lub w poziomie na wielu maszynach ([maksymalnie cztery węzły](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). Pojedyncze działanie kopiowania dzieli swój zestaw plików na wszystkie węzły. 
- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu wielu wątków. 
- Przepływ sterowania Data Factory można uruchomić równolegle wiele działań kopiowania. Na przykład można użyć [dla każdej pętli](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Aby uzyskać więcej informacji, zobacz [Przewodnik po wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odporności

W Data Factory trybie pomocą distcp można użyć różnych parametrów wiersza polecenia pomocą distcp (na przykład `-i` ignorowanie błędów lub `-update` zapisanie danych, gdy plik źródłowy i docelowy różnią się wielkością) dla różnych poziomów odporności.

Data Factory w trybie natywnego środowiska Integration Runtime w ramach jednego przebiegu działania kopiowania Data Factory ma wbudowany mechanizm ponawiania prób. Może obsłużyć określony poziom przejściowych błędów w magazynach danych lub w sieci podstawowej. 

Podczas kopiowania binarnego z lokalnego systemu plików HDFS do magazynu obiektów blob i z lokalnego systemu plików HDFS do Data Lake Store Gen2, Data Factory automatycznie wykonuje punkt kontrolny w dużym zakresie. Jeśli uruchomienie działania kopiowania zakończy się niepowodzeniem lub przejdzie do przełączenia, przy kolejnej ponowieniu próby (Upewnij się, że liczba ponownych prób jest > 1), kopiowanie zostanie wznowione od ostatniego punktu niepowodzenia, a nie od początku.

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie Data Factory transferuje dane z lokalnego systemu plików HDFS do magazynu obiektów blob lub Azure Data Lake Storage Gen2 za pomocą szyfrowanego połączenia za pośrednictwem protokołu HTTPS. Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i uniemożliwia podsłuchiwanie i ataki typu man-in-the-middle. 

Alternatywnie, jeśli nie chcesz, aby dane były transferowane za pośrednictwem publicznej sieci Internet, w celu zapewnienia wyższego poziomu zabezpieczeń możesz przesłać dane za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi ExpressRoute. 

## <a name="solution-architecture"></a>Architektura rozwiązania

Ten obraz przedstawia Migrowanie danych za pośrednictwem publicznej sieci Internet:

![Diagram przedstawiający architekturę rozwiązania do migrowania danych za pośrednictwem sieci publicznej](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- W tej architekturze dane są przesyłane bezpiecznie przy użyciu protokołu HTTPS przez publiczny Internet. 
- Zalecamy używanie Data Factory trybu pomocą distcp w środowisku sieci publicznej. Możesz skorzystać z zaawansowanego istniejącego klastra, aby osiągnąć najlepszą przepustowość kopiowania. Możesz również skorzystać z możliwości elastycznego planowania i ujednoliconego monitorowania w Data Factory.
- W przypadku tej architektury należy zainstalować Data Factory własne środowisko Integration Runtime na komputerze z systemem Windows za zaporą firmową, aby przesłać polecenie pomocą distcp do klastra Hadoop i monitorować stan kopiowania. Ponieważ maszyna nie jest aparatem, który przeniesie dane (tylko do celów kontroli), pojemność maszyny nie ma wpływu na przepływność przenoszenia danych.
- Istniejące parametry z polecenia pomocą distcp są obsługiwane.

Ten obraz przedstawia Migrowanie danych za pośrednictwem prywatnego linku: 

![Diagram przedstawiający architekturę rozwiązania do migrowania danych za pośrednictwem sieci prywatnej](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- W tej architekturze dane są migrowane za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure ExpressRoute. Dane nigdy nie przechodzą przez publiczny Internet.
- Narzędzie pomocą distcp nie obsługuje prywatnej komunikacji równorzędnej ExpressRoute z punktem końcowym sieci wirtualnej usługi Azure Storage. Zalecamy używanie natywnej możliwości Data Factory za pośrednictwem środowiska Integration Runtime do migrowania danych.
- W przypadku tej architektury należy zainstalować Data Factory własne środowisko Integration Runtime na maszynie wirtualnej z systemem Windows w sieci wirtualnej platformy Azure. Możesz ręcznie skalować maszynę wirtualną lub skalować ją do wielu maszyn wirtualnych, aby w pełni wykorzystać liczbę operacji we/wy sieci i magazynu.
- Zalecana konfiguracja do uruchomienia dla każdej maszyny wirtualnej platformy Azure (z zainstalowaną Data Factorym własnym środowisku Integration Runtime) jest Standard_D32s_v3 z 32 vCPU i 128 GB pamięci. Możesz monitorować użycie procesora i pamięci maszyny wirtualnej podczas migracji danych, aby zobaczyć, czy konieczne jest skalowanie maszyny wirtualnej w celu uzyskania lepszej wydajności lub skalowanie maszyny wirtualnej w dół w celu obniżenia kosztów.
- Można także skalować w poziomie, kojarząc do czterech węzłów maszyn wirtualnych przy użyciu pojedynczego środowiska Integration Runtime. Pojedyncze zadanie kopiowania uruchomione dla samodzielnego środowiska Integration Runtime automatycznie tworzy partycje zestawu plików i używa wszystkich węzłów maszyny wirtualnej do kopiowania plików równolegle. Aby zapewnić wysoką dostępność, zalecamy rozpoczęcie od dwóch węzłów maszyn wirtualnych, aby uniknąć scenariusza pojedynczego punktu awarii podczas migracji danych.
- W przypadku korzystania z tej architektury, migracja danych początkowej migawki i migracja danych różnicowych są dostępne dla użytkownika.

## <a name="implementation-best-practices"></a>Najlepsze rozwiązania w zakresie implementacji

Zalecamy przestrzeganie tych najlepszych rozwiązań podczas implementowania migracji danych.

### <a name="authentication-and-credential-management"></a>Zarządzanie uwierzytelnianiem i poświadczeniami 

- Aby przeprowadzić uwierzytelnianie w systemie plików HDFS, możesz użyć [systemu Windows (Kerberos) lub Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Do nawiązywania połączenia z usługą Azure Blob Storage są obsługiwane wiele typów uwierzytelniania.  Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). W oparciu o automatycznie zarządzaną tożsamość Data Factory w usłudze Azure Active Directory (Azure AD) tożsamości zarządzane umożliwiają skonfigurowanie potoków bez podawania poświadczeń w definicji połączonej usługi. Alternatywnie można uwierzytelniać w usłudze BLOB Storage przy użyciu [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Do łączenia się z Data Lake Storage Gen2 są również obsługiwane wiele typów uwierzytelniania.  Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), ale można również użyć [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 
- Jeśli nie używasz tożsamości zarządzanych dla zasobów platformy Azure, zdecydowanie zalecamy [przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) , aby ułatwić centralne zarządzanie kluczami i ich obracanie bez konieczności modyfikowania Data Factory połączonych usług. Jest to również [najlepsze rozwiązanie w przypadku ciągłej integracji/](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)ciągłego wdrażania. 

### <a name="initial-snapshot-data-migration"></a>Migracja początkowej danych migawek 

W Data Factory trybie pomocą distcp można utworzyć jedno działanie kopiowania, aby przesłać polecenie pomocą distcp i użyć innych parametrów do sterowania zachowaniem początkowej migracji danych. 

W Data Factory trybie natywnego środowiska Integration Runtime zalecamy korzystanie z partycji danych, szczególnie w przypadku migrowania więcej niż 10 TB danych. Aby podzielić dane na partycje, użyj nazw folderów w systemie plików HDFS. Następnie każde zadanie Data Factory Copy może skopiować jedną partycję folderu jednocześnie. Aby zapewnić lepszą przepływność, można uruchomić wiele zadań kopiowania Data Factory jednocześnie.

Jeśli którekolwiek z zadań kopiowania zakończy się niepowodzeniem z powodu przejściowych problemów z siecią lub magazynem danych, można ponownie uruchomić zadanie kopiowania zakończonego niepowodzeniem w celu ponownego załadowania określonej partycji z systemu plików HDFS. Nie ma to wpływu na inne zadania kopiowania, które ładują inne partycje.

### <a name="delta-data-migration"></a>Migracja danych różnicowych 

W Data Factory trybie pomocą distcp można użyć parametru wiersza polecenia pomocą distcp `-update` , zapisać dane, gdy plik źródłowy i plik docelowy różnią się w rozmiarze na potrzeby migracji danych różnicowych.

W trybie integracji natywnej Data Factory najbardziej wydajny sposób identyfikowania nowych lub zmienionych plików z systemu plików HDFS jest przy użyciu konwencji nazewnictwa z podziałem czasowym. Gdy dane w systemie HDFS zostały podzielone na partycje czasowo z informacjami o wycinku czasowym w nazwie pliku lub folderu (na przykład */yyyy/mm/dd/file.csv*), potok może łatwo identyfikować, które pliki i foldery mają być kopiowane przyrostowo.

Alternatywnie, jeśli dane w systemie plików HDFS nie są podzielone na partycje, Data Factory mogą identyfikować nowe lub zmienione pliki przy użyciu ich wartości **LastModifiedDate** . Data Factory skanuje wszystkie pliki z systemu plików HDFS i kopiuje tylko nowe i zaktualizowane pliki, które mają sygnaturę czasową, która jest większa niż określona wartość. 

Jeśli masz dużą liczbę plików w systemie plików HDFS, początkowe skanowanie plików może zająć dużo czasu, niezależnie od tego, ile plików jest zgodnych z warunkiem filtru. W tym scenariuszu zalecamy, aby najpierw podzielić dane na partycje przy użyciu tej samej partycji, która została użyta podczas migracji migawki początkowej. Następnie skanowanie plików może być wykonywane równolegle.

### <a name="estimate-price"></a>Cena szacunkowa 

Aby przeprowadzić migrację danych z systemu plików HDFS do magazynu obiektów blob platformy Azure, należy wziąć pod uwagę następujący potok: 

![Diagram przedstawiający potoku cenowe](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Załóżmy, że następujące informacje: 

- Całkowity wolumin danych to 1 PB.
- Migrowanie danych przy użyciu trybu macierzystego środowiska Integration Runtime Data Factory.
- 1 PB jest podzielony na partycje 1 000, a każda kopia przenosi jedną partycję.
- Każde działanie kopiowania jest skonfigurowane przy użyciu jednego środowiska Integration Runtime, które jest skojarzone z czterema maszynami, a następnie osiąga przepływność 500-MB/s.
- Ustawienie współbieżności ForEach jest ustawione na **4** , a przepustowość zagregowana to 2 GB/s.
- W sumie wykonanie migracji trwa 146 godzin.

Oto Szacowana cena oparta na naszych założeń: 

![Tabela, która zawiera obliczenia cen](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Jest to hipotetyczny przykład cen. Rzeczywiste ceny są uzależnione od rzeczywistej przepływności w danym środowisku.
> Cena maszyny wirtualnej z systemem Windows Azure (z zainstalowanym własnym środowiskiem Integration Runtime) nie jest uwzględniona.

### <a name="additional-references"></a>Dodatkowa dokumentacja

- [Łącznik HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Łącznik usługi Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Przewodnik dostrajania wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i konfigurowanie własnego środowiska Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Samodzielne środowisko Integration Runtime — wysoka dostępność i skalowalność](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia związane z zabezpieczeniami podczas przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w usłudze Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Przyrostowo Kopiuj plik na podstawie partycjonowanej nazwy pliku](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiuj nowe i zmienione pliki w oparciu o LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Strona cennika Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)