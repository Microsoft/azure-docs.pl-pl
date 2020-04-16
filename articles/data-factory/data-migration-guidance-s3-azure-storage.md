---
title: Migrowanie danych z usługi Amazon S3 do usługi Azure Storage
description: Użyj usługi Azure Data Factory do migracji danych z amazon S3 do usługi Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 3f40ad7346219b48a38ade38b2a75ddf71940875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416418"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Użyj usługi Azure Data Factory do migracji danych z amazon S3 do usługi Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usługa Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z amazon S3 do usługi Azure Blob Storage lub Usługi Azure Data Lake Storage Gen2.  Ten artykuł zawiera następujące informacje dla inżynierów danych i deweloperów: 

> [!div class="checklist"]
> * Wydajność 
> * Kopiowanie odporności
> * Bezpieczeństwo sieci
> * Architektura rozwiązań wysokiego poziomu 
> * Najlepsze praktyki w zakresie wdrażania  

## <a name="performance"></a>Wydajność

ADF oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach, co umożliwia deweloperom tworzenie potoków w celu pełnego wykorzystania przepustowości sieci, a także operacje we/wy magazynu i przepustowość w celu zmaksymalizowania przepływności przepływu danych w danym środowisku. 

Klienci pomyślnie migrowali petabajty danych składające się z setek milionów plików z Amazon S3 do usługi Azure Blob Storage, z trwałą przepustowością 2 gb/s i wyższą. 

![wydajność](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Powyższy obraz ilustruje, jak można osiągnąć duże prędkości ruchu danych przez różne poziomy równoległości:
 
- Działanie pojedynczej kopii można skorzystać ze skalowalnych zasobów obliczeniowych: podczas korzystania z środowiska uruchomieniowego integracji platformy Azure, można określić [maksymalnie 256 jednostek DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy; podczas korzystania z samodzielnego środowiska wykonawczego integracji można ręcznie skalować komputer lub skalować w poziomie do wielu[komputerów (do 4 węzłów),](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)a pojedyncze działanie kopiowania będzie partycjonować jego zestaw plików we wszystkich węzłach. 
- Działanie pojedynczej kopii odczytuje i zapisuje do magazynu danych przy użyciu wielu wątków. 
- Przepływ sterowania ADF można uruchomić wiele działań kopiowania równolegle, na przykład za pomocą [dla każdej pętli](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Odporności

W ramach jednego uruchomienia działania kopiowania ADF ma wbudowany mechanizm ponawiania prób, dzięki czemu może obsługiwać określony poziom błędów przejściowych w magazynach danych lub w sieci podstawowej. 

Podczas kopiowania binarnego z S3 do obiektu Blob i z S3 do ADLS Gen2, ADF automatycznie wykonuje punkty kontrolne.  Jeśli uruchomienie działania kopiowania nie powiodło się lub upłynie limit czasu, w kolejnych ponownych prób, kopia wznawia od ostatniego punktu awarii, a nie zaczyna od początku. 

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie usługa ADF przesyła dane z usługi Amazon S3 do usługi Azure Blob Storage lub Usługi Azure Data Lake Storage Gen2 przy użyciu szyfrowanego połączenia za pośrednictwem protokołu HTTPS.  Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i zapobiega podsłuchiwaniem i atakom typu man-in-the-middle. 

Alternatywnie, jeśli nie chcesz, aby dane były przesyłane przez publiczny Internet, możesz osiągnąć wyższe bezpieczeństwo, przesyłając dane za pośrednictwem prywatnego łącza komunikacji równorzędnej między usługą AWS Direct Connect a trasą Azure Express.  Zapoznaj się z architekturą rozwiązania poniżej, w jaki sposób można to osiągnąć. 

## <a name="solution-architecture"></a>Architektura rozwiązania

Migrowanie danych przez publiczny Internet:

![rozwiązanie-architektura-sieć publiczna](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- W tej architekturze dane są przesyłane bezpiecznie przy użyciu protokołu HTTPS za pośrednictwem publicznego Internetu. 
- Zarówno źródło Amazon S3, jak i docelowy usługa Azure Blob Storage lub Usługa Azure Data Lake Storage Gen2 są skonfigurowane tak, aby zezwalały na ruch ze wszystkich sieciowych adresów IP.  Zapoznaj się z drugą architekturą poniżej, w jaki sposób można ograniczyć dostęp do sieci do określonego zakresu adresów IP. 
- Możesz łatwo zwiększyć moc w sposób bezserwerowy, aby w pełni wykorzystać przepustowość sieci i pamięci masowej, dzięki czemu można uzyskać najlepszą przepustowość dla swojego środowiska. 
- Za pomocą tej architektury można osiągnąć zarówno początkową migrację migawki, jak i migrację danych delta. 

Migrowanie danych za łącze prywatne: 

![rozwiązanie-architektura-prywatna sieć](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- W tej architekturze migracja danych odbywa się za pośrednictwem prywatnego łącza równorzędnego między usługą AWS Direct Connect i marszrutą azure express, dzięki czemu dane nigdy nie przechodzą przez publiczny Internet.  Wymaga użycia AWS VPC i sieci wirtualnej platformy Azure. 
- Aby osiągnąć tę architekturę, należy zainstalować środowisko wykonawcze integracji hostowanego przez usługę ADF na maszynie Wirtualnej systemu Windows w sieci wirtualnej platformy Azure.  Możesz ręcznie skalować samodzielnie hostowane maszyny wirtualne podczerwieni lub skalować w poziomie do wielu maszyn wirtualnych (do 4 węzłów), aby w pełni korzystać z sieci i pamięci masowej We/Wy/przepustowości. 
- Jeśli dopuszczalne jest przesyłanie danych za pośrednictwem protokołu HTTPS, ale chcesz zablokować dostęp do sieci do źródła S3 do określonego zakresu adresów IP, można przyjąć odmianę tej architektury, usuwając AWS VPC i zastępując prywatne łącze protokołem HTTPS.  Będziesz chciał zachować azure wirtualne i samodzielnie hostowane IR na maszynie Wirtualnej platformy Azure, dzięki czemu można mieć statycznie rutowalne IP do celów białej listy. 
- Za pomocą tej architektury można osiągnąć zarówno początkową migrację danych migawki, jak i migrację danych delta. 

## <a name="implementation-best-practices"></a>Najlepsze praktyki w zakresie wdrażania 

### <a name="authentication-and-credential-management"></a>Uwierzytelnianie i zarządzanie poświadczeniami 

- Aby uwierzytelnić się na koncie Amazon S3, należy użyć [klucza dostępu dla konta IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Wiele typów uwierzytelniania są obsługiwane, aby połączyć się z usługą Azure Blob Storage.  Użycie [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) jest wysoce zalecane: na podstawie automatycznie zarządzanej identyfikacji podajnika ADF w usłudze Azure AD umożliwia konfigurowanie potoków bez poświadczeń w definicji usługi połączonej.  Alternatywnie można uwierzytelnić w usłudze Azure Blob Storage przy użyciu [jednostki usługi,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [podpisu dostępu współdzielonego](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)lub [klucza konta magazynu.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Wiele typów uwierzytelniania są również obsługiwane, aby połączyć się z usługą Azure Data Lake Storage Gen2.  Użycie [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) jest wysoce zalecane, chociaż można również użyć klucza konta [jednostkowego usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [magazynu.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 
- Jeśli nie używasz tożsamości zarządzanych dla zasobów platformy Azure, [przechowywanie poświadczeń w usłudze Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) jest wysoce zalecane, aby ułatwić centralne zarządzanie kluczami i obracanie je bez modyfikowania połączonych usług ADF.  Jest to również jedna z [najlepszych praktyk dotyczących ciągłej integracji/ciągłego wdrażania.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) 

### <a name="initial-snapshot-data-migration"></a>Początkowa migracja danych migawki 

Partycja danych jest zalecane szczególnie podczas migracji więcej niż 100 TB danych.  Aby podzielić dane na partycje, wykorzystaj ustawienie "prefiks", aby filtrować foldery i pliki w Amazon S3 według nazwy, a następnie każde zadanie kopiowania ADF może kopiować jedną partycję naraz.  Można uruchomić wiele zadań kopiowania podajnika ADF jednocześnie w celu uzyskania lepszej przepływności. 

Jeśli którykolwiek z zadań kopiowania zakończy się niepowodzeniem z powodu problemu przejściowego magazynu danych lub danych, można ponownie uruchomić zadanie kopiowania, które nie powiodło się, aby ponownie załadować tę określoną partycję z usługi AWS S3.  Wszystkie inne zadania kopiowania ładowania innych partycji nie będzie miało wpływu. 

### <a name="delta-data-migration"></a>Migracja danych delta 

Najbardziej wydajnym sposobem identyfikacji nowych lub zmienionych plików z AWS S3 jest użycie konwencji nazewnictwa podzielonej na partycje czasowe - gdy dane w AWS S3 zostały podzielone na partycje z informacjami o wycinku czasu w nazwie pliku lub folderu (na przykład /yyyy/mm/dd/file.csv), a następnie potok może łatwo zidentyfikować, które pliki /foldery mają być kopiowane przyrostowo. 

Alternatywnie, Jeśli dane w AWS S3 nie jest podzielony na partycje czas, ADF można zidentyfikować nowe lub zmienione pliki przez ich LastModifiedDate.   Sposób, w jaki to działa, polega na tym, że ADF skanuje wszystkie pliki z AWS S3 i tylko kopiuje nowy i zaktualizowany plik, którego ostatni zmodyfikowany znacznik czasu jest większy niż określona wartość.  Należy pamiętać, że jeśli masz dużą liczbę plików w S3, początkowe skanowanie plików może zająć dużo czasu, niezależnie od tego, ile plików odpowiada warunkowi filtru.  W takim przypadku zaleca się najpierw partycjonowanie danych przy użyciu tego samego ustawienia "prefiksu" dla początkowej migracji migawki, aby skanowanie plików odbywało się równolegle.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>W przypadku scenariuszy wymagających samodzielnego hosta środowiska wykonawczego integracji na maszynie Wirtualnej platformy Azure 

Niezależnie od tego, czy przeprowadzasz migrację danych za pomocą łącza prywatnego, czy chcesz zezwolić na określony zakres adresów IP na zaporze Amazon S3, musisz zainstalować środowisko wykonawcze integracji hostowanego samodzielnie na maszynie Wirtualnej systemu Windows platformy Azure. 

- Zalecana konfiguracja, z których można rozpocząć dla każdej maszyny Wirtualnej platformy Azure, jest Standard_D32s_v3 z 32 procesorami wirtualnymi i pamięcią 128 GB.  Podczas migracji danych można monitorować wykorzystanie procesora CPU i pamięci maszyny Wirtualnej na podczerwień, aby sprawdzić, czy należy dalej skalować maszynę wirtualną w celu uzyskania lepszej wydajności lub zmniejszyć skalę maszyny Wirtualnej w celu zaoszczędzenia kosztów. 
- Można również skalować w poziomie, kojarząc maksymalnie 4 węzły maszyny Wirtualnej z jednym samodzielnie hostowanym podczerwienią.  Pojedyncze zadanie kopiowania uruchomione względem samodzielnego hostowania podczerwony automatycznie partycjonuje zestaw plików i wykorzystuje wszystkie węzły maszyny Wirtualnej do kopiowania plików równolegle.  Aby uzyskać wysoką dostępność, zaleca się rozpoczęcie od 2 węzłów maszyn wirtualnych, aby uniknąć pojedynczego punktu awarii podczas migracji danych. 

### <a name="rate-limiting"></a>Rate limiting (Ograniczanie szybkości) 

Najlepszym rozwiązaniem jest przeprowadzenie poc wydajności z reprezentatywnym zestawem danych próbki, dzięki czemu można określić odpowiedni rozmiar partycji. 

Zacznij od pojedynczej partycji i pojedynczej aktywności kopiowania z domyślnym ustawieniem DIU.  Stopniowo zwiększaj ustawienie DIU, aż do osiągnięcia limitu przepustowości sieci lub limitu IOPS/przepustowości magazynów danych lub osiągnięto maksymalną liczbę DIU 256 dozwoloną dla pojedynczej aktywności kopiowania. 

Następnie stopniowo zwiększaj liczbę równoczesnych działań kopiowania, aż do osiągnięcia limitów środowiska. 

W przypadku wystąpienia błędów ograniczania przepustowości zgłaszanych przez aktywność kopiowania usługi ADF należy zmniejszyć współbieżność lub ustawienie DIU w ujrzeniu ADF lub rozważyć zwiększenie limitów przepustowości/we/wy magazynów danych sieci i danych.  

### <a name="estimating-price"></a>Szacowanie ceny 

> [!NOTE]
> Jest to hipotetyczny przykład cenowy.  Rzeczywista cena zależy od rzeczywistej przepływności w twoim środowisku.

Należy wziąć pod uwagę następujące potoku skonstruowane do migracji danych z S3 do usługi Azure Blob Storage: 

![wyceny rurociągu](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Załóżmy, że: 

- Całkowita objętość danych wynosi 2 PB 
- Migrowanie danych za pośrednictwem protokołu HTTPS przy użyciu architektury pierwszego rozwiązania 
- 2 PB jest podzielony na partycje 1 K i każda kopia przenosi jedną partycję 
- Każda kopia jest skonfigurowana z DIU= 256 i osiąga przepustowość 1 GBps 
- Współbieżność ForEach jest ustawiona na 2, a łączna przepustowość wynosi 2 
- W sumie ukończenie migracji trwa 292 godziny 

Oto szacowana cena na podstawie powyższych założeń: 

![tabela cen](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Dodatkowa dokumentacja 
- [Złącze Amazon Simple Storage Service](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Łącznik usługi Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopiuj przewodnik po dostrajaniu wydajności aktywności](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i konfigurowanie samodzielnego środowiska wykonawczego integracji](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Środowisko uruchomieniowe integracji hostowanego i skalowalność](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia związane z zabezpieczeniami podczas przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w usłudze Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiowanie pliku przyrostowo na podstawie nazwy pliku podzielonego na partycje czasu](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiowanie nowych i zmienionych plików na podstawie lastmodifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Strona z cennikiem usługi ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Szablon

Oto [szablon,](solution-template-migration-s3-azure.md) który można rozpocząć, aby migrować petabajty danych składających się z setek milionów plików z Amazon S3 do usługi Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów za pomocą usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)
