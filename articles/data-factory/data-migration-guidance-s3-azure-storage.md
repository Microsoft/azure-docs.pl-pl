---
title: Migrowanie danych z usługi Amazon S3 do usługi Azure Storage
description: Użyj Azure Data Factory do migrowania danych z usługi Amazon S3 do magazynu Azure.
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
ms.openlocfilehash: be1cb7abbc243e3f79e183223fbbb32380f5d02d
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638044"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Używanie Azure Data Factory do migrowania danych z usługi Amazon S3 do magazynu Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z usługi Amazon S3 do platformy Azure Blob Storage lub Azure Data Lake Storage Gen2.  Ten artykuł zawiera następujące informacje dotyczące inżynierów danych i deweloperów: 

> [!div class="checklist"]
> * Wydajność 
> * Odporność kopiowania
> * Bezpieczeństwo sieci
> * Architektura rozwiązania wysokiego poziomu 
> * Najlepsze rozwiązania w zakresie implementacji  

## <a name="performance"></a>Wydajność

System ADF oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach, dzięki czemu deweloperzy mogą tworzyć potoki w celu pełnego wykorzystania przepustowości sieci, a także liczby operacji we/wy magazynu i przepustowości w celu zmaksymalizowania przepływności przenoszenia danych w środowisku. 

Klienci zostali pomyślnie zmigrowani petabajtów danych składających się z setek milionów plików z usługi Amazon S3 do platformy Azure Blob Storage z trwałą przepływność wynoszącą 2 GB/s lub wyższą. 

![Diagram przedstawia kilka partycji plików w sklepie w trybie S3 ze skojarzonymi akcjami kopiowania na platformie Azure Blob Storage D L S Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

Na powyższym obrazie przedstawiono sposób osiągnięcia doskonałej szybkości przenoszenia danych przy użyciu różnych poziomów równoległości:
 
- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych: w przypadku korzystania z Azure Integration Runtime można określić [maksymalnie 256 DIUs](./copy-activity-performance.md#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy. w przypadku korzystania z Integration Runtime samoobsługowego można ręcznie skalować maszynę w górę lub w poziomie na wielu maszynach ([maksymalnie 4 węzły](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)), a działanie pojedynczej kopii spowoduje partycjonowanie zestawu plików we wszystkich węzłach. 
- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu wielu wątków. 
- Przepływ sterowania ADF można uruchomić równolegle wiele działań kopiowania, na przykład przy użyciu [dla każdej pętli](./control-flow-for-each-activity.md). 

## <a name="resilience"></a>Odporności

W ramach pojedynczego uruchomienia działania kopiowania moduł ADF ma wbudowany mechanizm ponawiania prób, dlatego może obsłużyć określony poziom przejściowych błędów w magazynach danych lub w sieci źródłowej. 

Podczas kopiowania binarnego z S3 do obiektów blob i z S3 do ADLS Gen2, funkcja ADF automatycznie wykonuje punkty kontrolne.  Jeśli uruchomienie działania kopiowania nie powiodło się lub upłynął limit czasu przy kolejnej ponowieniu próby, kopiowanie zostanie wznowione od ostatniego punktu awarii zamiast od początku. 

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie usługa ADF przesyła dane z usługi Amazon S3 do platformy Azure Blob Storage lub Azure Data Lake Storage Gen2 przy użyciu szyfrowanego połączenia za pośrednictwem protokołu HTTPS.  Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i uniemożliwia podsłuchiwanie i ataki typu man-in-the-middle. 

Alternatywnie, jeśli nie chcesz, aby dane były transferowane za pośrednictwem publicznej sieci Internet, możesz uzyskać wyższe zabezpieczenia, przekazując dane za pośrednictwem połączenia prywatnego komunikacji równorzędnej między usługą AWS Direct Connect i usługą Azure Express Route.  Zapoznaj się z architekturą rozwiązania poniżej, w jaki sposób można to osiągnąć. 

## <a name="solution-architecture"></a>Architektura rozwiązania

Migrowanie danych za pośrednictwem publicznej sieci Internet:

![Diagram przedstawia migrację za pośrednictwem Internetu przez H t T z magazynu A W trybie S3 za pośrednictwem Azure Integration Runtime w D F Azure do magazynu platformy Azure. Środowisko uruchomieniowe ma Kanał kontrolny z Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- W tej architekturze dane są przesyłane bezpiecznie przy użyciu protokołu HTTPS przez publiczny Internet. 
- Zarówno źródło Amazon S3, jak i docelowe Blob Storage platformy Azure lub Azure Data Lake Storage Gen2 są skonfigurowane tak, aby zezwalały na ruch ze wszystkich sieciowych adresów IP.  Zapoznaj się z drugą architekturą poniżej, w jaki sposób można ograniczyć dostęp do sieci do określonego zakresu adresów IP. 
- Można łatwo skalować w górę ilość mocy bezserwerowej, aby w pełni wykorzystać przepustowość sieci i magazynu, dzięki czemu można uzyskać najlepszą przepływność dla danego środowiska. 
- Migracja początkowej migawki i migracja danych różnicowych można osiągnąć przy użyciu tej architektury. 

Migruj dane za pośrednictwem prywatnego linku: 

![Diagram przedstawia migrację za pośrednictwem prywatnej komunikacji równorzędnej ze sklepu A W trybie S3 za pośrednictwem samodzielnego środowiska Integration Runtime na maszynach wirtualnych platformy Azure do usługi Azure Storage. Środowisko uruchomieniowe ma Kanał kontrolny z Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- W tej architekturze migracja danych odbywa się za pośrednictwem połączenia prywatnego komunikacji równorzędnej między usługą AWS Direct Connect i trasą usługi Azure Express, takich jak dane nigdy nie przechodzą przez publiczny Internet.  Wymaga korzystania z AWS VPC i usługi Azure Virtual Network. 
- Aby osiągnąć tę architekturę, należy zainstalować środowisko Integration Runtime (samodzielne) na maszynie wirtualnej z systemem Windows w sieci wirtualnej platformy Azure.  Możesz ręcznie skalować swoje samodzielnie hostowane maszyny wirtualne IR lub skalować je do wielu maszyn wirtualnych (do 4 węzłów), aby w pełni wykorzystać możliwości operacji we/wy na sekundę w sieci i pamięci masowej. 
- Jeśli jest akceptowalny do transferu danych za pośrednictwem protokołu HTTPS, ale chcesz zablokować dostęp do sieci do źródła S3 do określonego zakresu adresów IP, możesz wprowadzić zmianę tej architektury, usuwając AWS VPC i zastępując prywatny link przy użyciu protokołu HTTPS.  Należy zachować wirtualne i samoobsługowe środowisko IR platformy Azure na maszynie wirtualnej platformy Azure, aby można było utworzyć statyczny, publiczny adres IP w celu filtrowania. 
- W ramach tej architektury można osiągnąć zarówno migrację danych początkowej migawki, jak i migrację danych różnicowych. 

## <a name="implementation-best-practices"></a>Najlepsze rozwiązania w zakresie implementacji 

### <a name="authentication-and-credential-management"></a>Zarządzanie uwierzytelnianiem i poświadczeniami 

- Aby uwierzytelnić konto usługi Amazon S3, należy użyć [klucza dostępu dla konta usługi IAM](./connector-amazon-simple-storage-service.md#linked-service-properties). 
- Aby nawiązać połączenie z usługą Azure Blob Storage, obsługiwane są wiele typów uwierzytelniania.  Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](./connector-azure-blob-storage.md#managed-identity) jest zdecydowanie zalecane: Wbudowana na podstawie automatycznie zarządzanego podajnika APD w usłudze Azure AD umożliwia konfigurowanie potoków bez podawania poświadczeń w definicji połączonej usługi.  Alternatywnie można uwierzytelnić się w usłudze Azure Blob Storage przy użyciu [nazwy głównej usługi](./connector-azure-blob-storage.md#service-principal-authentication), [sygnatury dostępu współdzielonego](./connector-azure-blob-storage.md#shared-access-signature-authentication)lub [klucza konta magazynu](./connector-azure-blob-storage.md#account-key-authentication). 
- Do łączenia się z Azure Data Lake Storage Gen2 są również obsługiwane wiele typów uwierzytelniania.  Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](./connector-azure-data-lake-storage.md#managed-identity) jest zdecydowanie zalecane, ale można również użyć [nazwy głównej usługi](./connector-azure-data-lake-storage.md#service-principal-authentication) lub [klucza konta magazynu](./connector-azure-data-lake-storage.md#account-key-authentication) . 
- Jeśli nie używasz tożsamości zarządzanych dla zasobów platformy Azure, [przechowywanie poświadczeń w Azure Key Vault](./store-credentials-in-key-vault.md) jest zdecydowanie zalecane, aby ułatwić centralne zarządzanie kluczami i ich obracanie bez konieczności modyfikowania połączonych usług ADF.  Jest to również jedno z [najlepszych rozwiązań dotyczących](./continuous-integration-deployment.md#best-practices-for-cicd)ciągłej integracji/ciągłego wdrażania. 

### <a name="initial-snapshot-data-migration"></a>Migracja początkowej danych migawek 

Partycja danych jest zalecana szczególnie w przypadku migrowania więcej niż 100 TB danych.  Aby podzielić dane na partycje, Skorzystaj z ustawienia "prefiks" w celu odfiltrowania folderów i plików w usłudze Amazon S3 według nazwy, a następnie każde zadanie kopiowania ADF może skopiować jedną partycję w danym momencie.  Aby zapewnić lepszą przepływność, można uruchomić wiele zadań kopiowania ADF współbieżnie. 

Jeśli którekolwiek z zadań kopiowania zakończy się niepowodzeniem z powodu przejściowego problemu z siecią lub magazynem danych, można ponownie uruchomić zadanie kopiowania zakończonego niepowodzeniem, aby ponownie załadować tę konkretną partycję z AWS S3.  Nie wpłynie to na wszystkie inne zadania kopiowania ładujące inne partycje. 

### <a name="delta-data-migration"></a>Migracja danych różnicowych 

Najbardziej wydajnym sposobem identyfikowania nowych lub zmienionych plików z AWS S3 jest użycie konwencji nazewnictwa z podziałem czasowym — Jeśli dane w AWS S3 zostały podzielone na partycje czasowo w nazwie pliku lub folderu (na przykład/yyyy/mm/dd/file.csv), potok może łatwo identyfikować, które pliki/foldery mają być kopiowane przyrostowo. 

Alternatywnie, jeśli dane w AWS S3 nie są podzielone na partycje, ADF może identyfikować nowe lub zmienione pliki przez ich LastModifiedDate.   W ten sposób jest to, że zestaw ADF przeskanuje wszystkie pliki z AWS S3 i skopiuje nowy i zaktualizowany plik, którego Ostatnia modyfikowana sygnatura czasowa jest większa niż określona wartość.  Należy pamiętać, że jeśli w usłudze S3 istnieje duża liczba plików, początkowe skanowanie plików może zająć dużo czasu, niezależnie od liczby plików pasujących do warunku filtru.  W takim przypadku sugerowane jest Partycjonowanie danych przy użyciu tego samego ustawienia "prefix" dla początkowej migracji migawek, dzięki czemu skanowanie plików może być wykonywane równolegle.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Scenariusze wymagające własnego środowiska Integration Runtime na maszynie wirtualnej platformy Azure 

Niezależnie od tego, czy migrujesz dane za pośrednictwem prywatnego linku, czy chcesz zezwolić na określony zakres adresów IP w zaporze Amazon S3, musisz zainstalować własne środowisko Integration Runtime na maszynie wirtualnej z systemem Windows Azure. 

- Zalecaną konfiguracją początkową dla każdej maszyny wirtualnej platformy Azure jest Standard_D32s_v3 z 32 vCPU i 128 GB pamięci.  Podczas migracji danych można utrzymywać monitorowanie użycia procesora CPU i pamięci przez maszynę wirtualną w celu sprawdzenia, czy konieczne jest dalsze skalowanie maszyny wirtualnej w górę w celu uzyskania lepszej wydajności lub skalowanie maszyny wirtualnej w dół w celu zaoszczędzenia kosztów. 
- Możesz również skalować w poziomie, kojarząc maksymalnie 4 węzły maszyny wirtualnej za pomocą pojedynczego środowiska IR obsługiwanego przez siebie.  Pojedyncze zadanie kopiowania uruchomione dla samodzielnego środowiska IR będzie automatycznie dzielić zestaw plików i korzystać ze wszystkich węzłów maszyny wirtualnej w celu równoległego kopiowania plików.  Aby zapewnić wysoką dostępność, zaleca się Rozpoczynanie pracy z 2 węzłami maszyn wirtualnych w celu uniknięcia single point of failure podczas migracji danych. 

### <a name="rate-limiting"></a>Rate limiting (Ograniczanie szybkości) 

Najlepszym rozwiązaniem jest przeprowadzenie koncepcji oceny wydajności z reprezentatywnym przykładowym zestawem danych, dzięki czemu można określić odpowiedni rozmiar partycji. 

Zacznij od pojedynczej partycji i jednego działania kopiowania z domyślnym ustawieniem DIU.  Stopniowo zwiększaj ustawienie DIU, dopóki nie osiągniesz limitu przepustowości sieci lub liczby operacji we/wy na sekundę w magazynach danych, lub osiągnięto maksymalną 256 DIUą dla jednego działania kopiowania. 

Następnie stopniowo Zwiększaj liczbę współbieżnych działań kopiowania do momentu osiągnięcia limitów środowiska. 

W przypadku wystąpienia błędów ograniczania wydajności raportowanych przez działanie kopiowania APD, należy zmniejszyć ustawienie współbieżności lub DIU w podajniku APD lub rozważyć zwiększenie limitów przepustowości/liczby operacji we/wy sieci i magazynów danych.  

### <a name="estimating-price"></a>Cena szacunkowa 

> [!NOTE]
> Jest to hipotetyczny przykład cen.  Rzeczywiste ceny są uzależnione od rzeczywistej przepływności w danym środowisku.

Weź pod uwagę następujące potoki skonstruowane do migrowania danych z S3 do platformy Azure Blob Storage: 

![Diagram przedstawia potok migracji danych, z wyzwalaczem ręcznym przepływające do wyszukiwania, przepływające do ForEach, przepływające do potoku dla każdej partycji, która zawiera kopię przepływu do procedury składowanej. Poza potokiem procedura składowana przepływa do usługi Azure SQL D B, która przenosi do wyszukiwania i przepływy W warstwie S3 w celu kopiowania, które przepływa do magazynu obiektów BLOB.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Załóżmy, że: 

- Łączny wolumin danych to 2 PB 
- Migrowanie danych za pośrednictwem protokołu HTTPS przy użyciu architektury pierwszego rozwiązania 
- 2 PB jest podzielony na partycje 1 K, a każda kopia przenosi jedną partycję 
- Każda kopia jest konfigurowana z DIU = 256 i osiąga przepustowość 1 GB/s 
- Ustawienie współbieżności ForEach jest ustawione na 2, a przepustowość zagregowana to 2 GB/s 
- Łącznie trwa 292 godzin do ukończenia migracji 

Oto Szacowana cena oparta na powyższych założeniach: 

![Zrzut ekranu tabeli pokazuje szacowaną cenę.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Dodatkowa dokumentacja 
- [Łącznik prostego usługi magazynu Amazon](./connector-amazon-simple-storage-service.md)
- [Łącznik usługi Azure Blob Storage](./connector-azure-blob-storage.md)
- [Łącznik usługi Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md)
- [Przewodnik dostrajania wydajności działania kopiowania](./copy-activity-performance.md)
- [Tworzenie i Konfigurowanie samoobsługowego Integration Runtime](./create-self-hosted-integration-runtime.md)
- [Własne środowisko Integration Runtime HA i skalowalność](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Zagadnienia związane z zabezpieczeniami podczas przenoszenia danych](./data-movement-security-considerations.md)
- [Przechowywanie poświadczeń w usłudze Azure Key Vault](./store-credentials-in-key-vault.md)
- [Przyrostowo Kopiuj plik na podstawie nazwy pliku podzielonego na partycje](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Kopiuj nowe i zmienione pliki w oparciu o LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Strona cennika ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Szablon

Oto [szablon](solution-template-migration-s3-azure.md) do rozpoczęcia migracji petabajtów danych składających się z setek milionów plików z usługi Amazon S3 do Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)