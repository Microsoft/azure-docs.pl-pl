---
title: Migrowanie danych z lokalnego klastra Hadoop do usługi Azure Storage
description: Dowiedz się, jak używać usługi Azure Data Factory do migracji danych z lokalnego klastra Hadoop do usługi Azure Storage.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417133"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Użyj usługi Azure Data Factory do migracji danych z lokalnego klastra Hadoop do usługi Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usługa Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z lokalnego systemu plików HDFS do magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Storage Gen2. 

Usługa Data Factory oferuje dwa podstawowe metody migracji danych z lokalnego systemu plików HDFS na platformę Azure. Można wybrać podejście na podstawie scenariusza. 

- **Tryb DistCp fabryki danych** (zalecane): W fabryce danych można użyć [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (kopia rozproszona) do kopiowania plików w stanie niejako do magazynu obiektów Blob platformy Azure (w tym [kopii etapowej)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)lub usługi Azure Data Lake Store Gen2. Użyj usługi Data Factory zintegrowanej z DistCp, aby skorzystać z istniejącego zaawansowanego klastra, aby uzyskać najlepszą przepływność kopiowania. Możesz również uzyskać korzyści z elastycznego planowania i ujednoliconego środowiska monitorowania z usługi Data Factory. W zależności od konfiguracji usługi Data Factory, działanie kopiowania automatycznie konstruuje polecenie DistCp, przesyła dane do klastra Hadoop, a następnie monitoruje stan kopiowania. Zaleca się tryb DistCp fabryki danych do migracji danych z lokalnego klastra Hadoop na platformę Azure.
- **Tryb środowiska uruchomieniowego integracji natywnej usługi Data Factory:** DistCp nie jest opcją we wszystkich scenariuszach. Na przykład w środowisku sieci wirtualnych platformy Azure narzędzie DistCp nie obsługuje prywatnej komunikacji równorzędnej usługi Azure ExpressRoute z punktem końcowym sieci wirtualnej usługi Azure Storage. Ponadto w niektórych przypadkach nie chcesz używać istniejącego klastra Hadoop jako aparatu do migracji danych, aby nie obciążać klastra dużymi obciążeniami, co może mieć wpływ na wydajność istniejących zadań ETL. Zamiast tego można użyć natywnej możliwości środowiska wykonawczego integracji usługi Data Factory jako aparatu, który kopiuje dane z lokalnego serwera HDFS na platformę Azure.

Ten artykuł zawiera następujące informacje na temat obu podejść:
> [!div class="checklist"]
> * Wydajność 
> * Kopiowanie odporności
> * Bezpieczeństwo sieci
> * Architektura rozwiązań wysokiego poziomu 
> * Najlepsze praktyki w zakresie wdrażania  

## <a name="performance"></a>Wydajność

W trybie DistCp fabryki danych przepływność jest taka sama, jak w przypadku korzystania z narzędzia DistCp niezależnie. Tryb DistCp fabryki danych maksymalizuje pojemność istniejącego klastra Hadoop. DistCp można używać do kopiowania dużych klastrów lub wewnątrz klastra. 

DistCp używa MapReduce do wykonania jego dystrybucji, obsługi błędów i odzyskiwania oraz raportowania. Rozszerza listę plików i katalogów do danych wejściowych do mapowania zadań. Każde zadanie kopiuje partycję pliku określoną na liście źródłowej. Za pomocą usługi Data Factory zintegrowanej z DistCp można tworzyć potoki, aby w pełni wykorzystać przepustowość sieci, operacje we/wy magazynu i przepustowość, aby zmaksymalizować przepływność przepływu danych w danym środowisku.  

Tryb środowiska uruchomieniowego natywnej integracji danych Factory umożliwia również równoległość na różnych poziomach. Równoległość umożliwia pełne wykorzystanie przepustowości sieci, obsługi we/wy magazynu i przepustowości w celu maksymalizacji przepływności przepływu danych:

- Działanie pojedynczej kopii może korzystać ze skalowalnych zasobów obliczeniowych. Dzięki samodzielnemu programowi działania integracyjnego można ręcznie skalować maszynę lub skalować w poziomie do wielu komputerów[(maksymalnie czterech węzłów).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) Działanie pojedynczej kopii dzieli swój zestaw plików we wszystkich węzłach. 
- Działanie pojedynczej kopii odczytuje i zapisuje do magazynu danych przy użyciu wielu wątków. 
- Przepływ sterowania fabryki danych można uruchomić wiele działań kopiowania równolegle. Na przykład można użyć [pętli Dla każdego](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Aby uzyskać więcej informacji, zobacz [przewodnik wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odporności

W trybie DistCp fabryki danych można użyć różnych parametrów wiersza polecenia DistCp (na przykład , ignoruj błędy lub `-i` `-update`, zapisuj dane, gdy plik źródłowy i plik docelowy różnią się rozmiarem) dla różnych poziomów odporności.

W trybie wykonywania natywnej integracji usługi Data Factory w jednym uruchomieniu działania kopiowania usługa Data Factory ma wbudowany mechanizm ponawiania. Może obsługiwać pewien poziom błędów przejściowych w magazynach danych lub w sieci podstawowej. 

Podczas kopiowania binarnego z lokalnego systemu plików HDFS do magazynu obiektów Blob i z lokalnego systemu plików HDFS do usługi Data Lake Store Gen2 usługa Data Factory automatycznie wykonuje punkty kontrolne w dużym stopniu. Jeśli uruchomienie działania kopiowania nie powiedzie się lub upłynie upłynie, przy kolejnej próbie (upewnij się, że liczba ponownych prób jest > 1), kopia zostanie wznowiona od ostatniego punktu awarii, a nie zaczyna się od początku.

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie usługa Data Factory przesyła dane z lokalnego systemu plików HDFS do magazynu obiektów Blob lub usługi Azure Data Lake Storage Gen2 przy użyciu szyfrowanego połączenia za pośrednictwem protokołu HTTPS. Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i zapobiega podsłuchiwaniem i atakom typu man-in-the-middle. 

Alternatywnie, jeśli nie chcesz, aby dane były przesyłane przez publiczny Internet, aby uzyskać większe bezpieczeństwo, możesz przesyłać dane za pośrednictwem prywatnego łącza równorzędnego za pośrednictwem usługi ExpressRoute. 

## <a name="solution-architecture"></a>Architektura rozwiązania

Ten obraz przedstawia migrację danych przez publiczny Internet:

![Diagram przedstawiający architekturę rozwiązania do migracji danych za pośrednictwem sieci publicznej](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- W tej architekturze dane są przesyłane bezpiecznie przy użyciu protokołu HTTPS za pośrednictwem publicznego Internetu. 
- Zalecamy użycie trybu DistCp fabryki danych w środowisku sieci publicznej. Można skorzystać z zaawansowanego istniejącego klastra, aby osiągnąć najlepszą przepływność kopiowania. Możesz również uzyskać korzyści z elastycznego planowania i ujednoliconego środowiska monitorowania z usługi Data Factory.
- W przypadku tej architektury należy zainstalować środowisko uruchomieniowe integracji hostowane samodzielnie w usłudze Data Factory na komputerze z systemem Windows za zaporą firmową, aby przesłać polecenie DistCp do klastra Hadoop i monitorować stan kopiowania. Ponieważ maszyna nie jest aparatem, który będzie przenosić dane (tylko do celów sterowania), pojemność maszyny nie wpływa na przepływność przepływu przepływu danych.
- Obsługiwane są istniejące parametry z polecenia DistCp.

Ten obraz przedstawia migrację danych za łącze prywatne: 

![Diagram przedstawiający architekturę rozwiązania do migracji danych za pośrednictwem sieci prywatnej](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- W tej architekturze dane są migrowane za pośrednictwem prywatnego łącza równorzędnego za pośrednictwem usługi Azure ExpressRoute. Dane nigdy nie przechodzą przez publiczny internet.
- Narzędzie DistCp nie obsługuje prywatnej komunikacji równorzędnej usługi ExpressRoute z punktem końcowym sieci wirtualnej usługi Azure Storage. Zaleca się użycie natywnej funkcji usługi Data Factory za pośrednictwem środowiska wykonawczego integracji w celu migracji danych.
- Dla tej architektury należy zainstalować środowisko uruchomieniowe integracji hostowane samodzielnie w usłudze Data Factory na maszynie Wirtualnej systemu Windows w sieci wirtualnej platformy Azure. Można ręcznie skalować maszynę wirtualną lub skalować w poziomie do wielu maszyn wirtualnych, aby w pełni korzystać z sieci i magazynu We/Wy lub przepustowości.
- Zalecana konfiguracja, z których można rozpocząć dla każdej maszyny Wirtualnej platformy Azure (z zainstalowanym własnym czasem wykonywania integracji usługi Data Factory) jest Standard_D32s_v3 z 32 procesorami wirtualnymi i 128 GB pamięci. Można monitorować użycie procesora CPU i pamięci maszyny Wirtualnej podczas migracji danych, aby zobaczyć, czy należy skalować maszynę wirtualną w górę, aby uzyskać lepszą wydajność, czy też skalować w dół maszyny Wirtualnej w celu zmniejszenia kosztów.
- Można również skalować w poziomie, kojarząc maksymalnie cztery węzły maszyn wirtualnych z jednym środowiskom uruchomieniowym integracji hostowanego samodzielnie. Pojedyncze zadanie kopiowania uruchomione względem środowiska wykonawczego integracji hostowanego automatycznie dzieli zestaw plików i korzysta ze wszystkich węzłów maszyn wirtualnych do kopiowania plików równolegle. Aby uzyskać wysoką dostępność, zaleca się rozpoczęcie od dwóch węzłów maszyn wirtualnych, aby uniknąć scenariusza pojedynczego punktu awarii podczas migracji danych.
- Korzystając z tej architektury, wstępna migracja danych migawki i migracja danych różnicowych są dostępne dla Ciebie.

## <a name="implementation-best-practices"></a>Najlepsze praktyki w zakresie wdrażania

Zaleca się, aby postępować zgodnie z tymi najlepszymi rozwiązaniami podczas implementowania migracji danych.

### <a name="authentication-and-credential-management"></a>Uwierzytelnianie i zarządzanie poświadczeniami 

- Aby uwierzytelnić się w systemie plików HDFS, można użyć [systemu Windows (Kerberos) lub anonimowego](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Wiele typów uwierzytelniania są obsługiwane do łączenia się z magazynem obiektów Blob platformy Azure.  Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Oparte na automatycznie zarządzanej tożsamości fabryki danych w usłudze Azure Active Directory (Azure AD) tożsamości zarządzane umożliwiają konfigurowanie potoków bez poświadczeń w definicji usługi połączonej. Alternatywnie można uwierzytelnić do magazynu obiektów Blob przy użyciu [jednostki usługi,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [podpisu dostępu współdzielonego](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)lub [klucza konta magazynu.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Wiele typów uwierzytelniania są również obsługiwane do łączenia się z Data Lake Storage Gen2.  Zdecydowanie zalecamy używanie [tożsamości zarządzanych dla zasobów platformy Azure,](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)ale można również użyć [jednostki usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [klucza konta magazynu.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 
- Jeśli nie używasz zarządzanych tożsamości dla zasobów platformy Azure, zdecydowanie [zalecamy przechowywanie poświadczeń w usłudze Azure Key Vault,](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) aby ułatwić centralne zarządzanie kluczami i obracanie nimi bez modyfikowania połączonych usług usługi Data Factory. Jest to również [najlepsza praktyka dla ciągłej integracji/ciągłego wdrażania.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) 

### <a name="initial-snapshot-data-migration"></a>Początkowa migracja danych migawki 

W trybie DistCp fabryki danych można utworzyć jedno działanie kopiowania w celu przesłania polecenia DistCp i użyć różnych parametrów do kontrolowania zachowania migracji danych początkowych. 

W trybie środowiska wykonawczego natywnej integracji usługi Data Factory zaleca się partycję danych, szczególnie podczas migracji więcej niż 10 TB danych. Aby podzielić dane na partycje, użyj nazw folderów w uinak. Następnie każde zadanie kopiowania usługi Data Factory może kopiować jedną partycję folderu naraz. Można uruchomić wiele zadań kopiowania usługi Data Factory jednocześnie dla lepszej przepływności.

Jeśli którykolwiek z zadań kopiowania zakończy się niepowodzeniem z powodu problemów przejściowych magazynu danych lub danych, można ponownie uruchomić zadanie kopiowania nie powiodło się, aby ponownie załadować tę określoną partycję z usługi HDFS. Inne zadania kopiowania, które są ładowania innych partycji nie ma wpływu.

### <a name="delta-data-migration"></a>Migracja danych delta 

W trybie DistCp fabryki danych można użyć parametru `-update`wiersza polecenia DistCp , zapisuj dane, gdy plik źródłowy i plik docelowy różnią się rozmiarem, do migracji danych różnicowych.

W trybie integracji natywnej usługi Data Factory najbardziej wydajnym sposobem identyfikowania nowych lub zmienionych plików z usługi HDFS jest użycie konwencji nazewnictwa podzielonej na partycje czasu. Gdy dane w systemie PLIKÓW HDFS zostały podzielone na partycje czasowe z informacjami o wycinku czasu w nazwie pliku lub folderu (na przykład */yyyy/mm/dd/file.csv),* potok może łatwo zidentyfikować pliki i foldery, które mają być kopiowane przyrostowo.

Alternatywnie, jeśli dane w hdfs nie jest podzielony na partycje czasowe, fabryka danych można zidentyfikować nowe lub zmienione pliki przy użyciu ich **LastModifiedDate** wartość. Data Factory skanuje wszystkie pliki z systemu PLIKÓW HDFS i kopiuje tylko nowe i zaktualizowane pliki, które mają ostatnio zmodyfikowany znacznik czasu, który jest większy niż ustawiona wartość. 

Jeśli w systemie HDFS jest duża liczba plików, początkowe skanowanie plików może zająć dużo czasu, niezależnie od tego, ile plików odpowiada warunkowi filtru. W tym scenariuszu zaleca się najpierw partycję danych przy użyciu tej samej partycji, która została użyta do początkowej migracji migawki. Następnie skanowanie plików może odbywać się równolegle.

### <a name="estimate-price"></a>Szacowana cena 

Należy wziąć pod uwagę następujący potok do migracji danych z usługi HDFS do magazynu obiektów blob platformy Azure: 

![Diagram przedstawiający potok cenowy](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Załóżmy, że następujące informacje: 

- Całkowita objętość danych wynosi 1 PB.
- Przeprowadzasz migrację danych przy użyciu trybu środowiska uruchomieniowego integracji natywnej usługi Data Factory.
- 1 PB jest podzielony na 1000 partycji i każda kopia przenosi jedną partycję.
- Każde działanie kopiowania jest skonfigurowane z jednym własnym hostowanym środowiskom uruchomieniowym integracji, które jest skojarzone z czterema komputerami i które osiąga przepustowość 500 MB/s.
- Współbieżność ForEach jest ustawiona na **4,** a przepływność agregująca wynosi 2 GB/s.
- W sumie trwa 146 godzin, aby zakończyć migrację.

Oto szacowana cena na podstawie naszych założeń: 

![Tabela przedstawiająca obliczenia cen](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Jest to hipotetyczny przykład cenowy. Rzeczywista cena zależy od rzeczywistej przepływności w twoim środowisku.
> Cena maszyny Wirtualnej systemu Windows platformy Azure (z zainstalowanym własnym czasem wykonywania integracji) nie jest uwzględniona.

### <a name="additional-references"></a>Dodatkowa dokumentacja

- [Złącze HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Łącznik magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopiuj przewodnik po dostrajaniu wydajności aktywności](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i konfigurowanie własnego środowiska Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Samoorganizowana integracja, wysoka dostępność i skalowalność środowiska wykonawczego](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia związane z zabezpieczeniami podczas przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w usłudze Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiowanie pliku przyrostowo na podstawie nazwy pliku podzielonego na partycje](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiowanie nowych i zmienionych plików na podstawie lastmodifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Strona z cennikiem fabryka danych](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów przy użyciu usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)