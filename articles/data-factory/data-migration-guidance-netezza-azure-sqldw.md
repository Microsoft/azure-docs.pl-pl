---
title: Migrowanie danych z lokalnego serwera Netezza na platformę Azure
description: Użyj usługi Azure Data Factory do migracji danych z lokalnego serwera Netezza na platformę Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: a0263880262da95f4d26ee8388da464e9a59efca
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416457"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Użyj usługi Azure Data Factory do migracji danych z lokalnego serwera Netezza na platformę Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usługa Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z lokalnego serwera Netezza do konta magazynu platformy Azure lub bazy danych usługi Azure SQL Data Warehouse. 

Ten artykuł zawiera następujące informacje dla inżynierów danych i deweloperów:

> [!div class="checklist"]
> * Wydajność 
> * Kopiowanie odporności
> * Bezpieczeństwo sieci
> * Architektura rozwiązań wysokiego poziomu 
> * Najlepsze praktyki w zakresie wdrażania  

## <a name="performance"></a>Wydajność

Usługa Azure Data Factory oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach. Jeśli jesteś deweloperem, oznacza to, że można tworzyć potoki, aby w pełni używać przepustowości sieci i bazy danych, aby zmaksymalizować przepływność przepływu danych w danym środowisku.

![Diagram wydajności](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Poprzedni schemat można interpretować w następujący sposób:

- Działanie pojedynczej kopii może korzystać ze skalowalnych zasobów obliczeniowych. Korzystając ze środowiska uruchomieniowego integracji platformy Azure, można określić [maksymalnie 256 diu](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy. Dzięki samodzielnemu programowi uruchomieniemu integracji (samodzielne środowisko IR) można ręcznie skalować maszynę lub skalować w poziomie do wielu komputerów[(maksymalnie cztery węzły),](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)a działanie pojedynczej kopii dystrybuuje jego partycję we wszystkich węzłach. 

- Działanie pojedynczej kopii odczytuje i zapisuje do magazynu danych przy użyciu wielu wątków. 

- Przepływ sterowania usługi Azure Data Factory można uruchomić wiele działań kopiowania równolegle. Na przykład można je uruchomić za pomocą [for each pętli](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Aby uzyskać więcej informacji, zobacz [Kopiowanie wydajności działania i skalowalności przewodnika](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odporności

W ramach jednego uruchomienia działania kopiowania usługa Azure Data Factory ma wbudowany mechanizm ponawiania prób, który umożliwia obsługę określonego poziomu błędów przejściowych w magazynach danych lub w sieci podstawowej.

Dzięki aktywności kopiowania usługi Azure Data Factory podczas kopiowania danych między magazynami danych źródłowych i magazynów danych ujścia masz dwa sposoby obsługi niezgodnych wierszy. Można przerwać i zakończyć działanie kopiowania lub kontynuować kopiowanie pozostałych danych, pomijając niezgodne wiersze danych. Ponadto, aby dowiedzieć się, przyczynę błędu, można rejestrować niezgodne wiersze w magazynie obiektów Blob platformy Azure lub usługi Azure Data Lake Store, naprawić dane w źródle danych i ponowić próbę wykonania działania kopiowania.

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie usługa Usługa Azure Data Factory przesyła dane z lokalnego serwera Netezza do konta magazynu platformy Azure lub bazy danych usługi Azure SQL Data Warehouse przy użyciu szyfrowanego połączenia za pośrednictwem protokołu HTTPS (Hypertext Transfer Protocol Secure). Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i zapobiega podsłuchiwaniem i atakom typu man-in-the-middle.

Alternatywnie, jeśli nie chcesz, aby dane były przesyłane za pośrednictwem publicznego Internetu, możesz pomóc w osiągnięciu wyższych zabezpieczeń, przesyłając dane za pośrednictwem prywatnego łącza komunikacji równorzędnej za pośrednictwem usługi Azure Express Route. 

W następnej sekcji omówiono sposób osiągnięcia wyższych zabezpieczeń.

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji omówiono dwa sposoby migracji danych.

### <a name="migrate-data-over-the-public-internet"></a>Migrowanie danych przez publiczny Internet

![Migrowanie danych przez publiczny Internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Poprzedni schemat można interpretować w następujący sposób:

- W tej architekturze można bezpiecznie przesyłać dane przy użyciu protokołu HTTPS za pośrednictwem publicznego Internetu.

- Aby osiągnąć tę architekturę, należy zainstalować środowisko uruchomieniowe integracji usługi Azure Data Factory (hostowane samodzielnie) na komputerze z systemem Windows za zaporą firmową. Upewnij się, że to środowisko wykonawcze integracji może bezpośrednio uzyskać dostęp do serwera Netezza. Aby w pełni korzystać z przepustowości sieci i magazynów danych do kopiowania danych, można ręcznie skalować komputer lub skalować w poziomie na wiele komputerów.

- Korzystając z tej architektury, można migrować zarówno dane początkowej migawki, jak i dane delta.

### <a name="migrate-data-over-a-private-network"></a>Migrowanie danych za pośrednictwem sieci prywatnej 

![Migrowanie danych za pośrednictwem sieci prywatnej](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Poprzedni schemat można interpretować w następujący sposób:

- W tej architekturze można migrować dane za pośrednictwem prywatnego łącza równorzędnego za pośrednictwem usługi Azure Express Route, a dane nigdy nie przechodzą przez publiczny Internet. 

- Aby osiągnąć tę architekturę, należy zainstalować środowisko uruchomieniowe integracji usługi Azure Data Factory (hostowane samodzielnie) na maszynie wirtualnej systemu Windows (VM) w sieci wirtualnej platformy Azure. Aby w pełni używać przepustowości sieci i magazynów danych do kopiowania danych, można ręcznie skalować maszynę wirtualną lub skalować w poziomie do wielu maszyn wirtualnych.

- Korzystając z tej architektury, można migrować zarówno dane początkowej migawki, jak i dane delta.

## <a name="implement-best-practices"></a>Wdrażanie najlepszych rozwiązań 

### <a name="manage-authentication-and-credentials"></a>Zarządzanie uwierzytelnianiem i poświadczeniami 

- Aby uwierzytelnić się w Netezzie, można użyć [uwierzytelniania ODBC za pomocą ciągu połączenia](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 

- Aby uwierzytelnić się w magazynie obiektów blob platformy Azure: 

   - Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Oparte na automatycznie zarządzanej tożsamości usługi Azure Data Factory w usłudze Azure Active Directory (Azure AD) tożsamości zarządzane umożliwiają konfigurowanie potoków bez konieczności poświadczeń w definicji usługi połączonej.  

   - Alternatywnie można uwierzytelnić do magazynu obiektów Blob platformy Azure przy użyciu [jednostki usługi,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [podpisu dostępu współdzielonego](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)lub [klucza konta magazynu.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 

- Aby uwierzytelnić się w usłudze Azure Data Lake Storage Gen2: 

   - Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)
   
   - Można również użyć [jednostki usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 

- Aby uwierzytelnić się w usłudze Azure SQL Data Warehouse:

   - Zdecydowanie zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)
   
   - Można również użyć [jednostki usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) lub [uwierzytelniania SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- Jeśli nie używasz zarządzanych tożsamości dla zasobów platformy Azure, zdecydowanie [zalecamy przechowywanie poświadczeń w usłudze Azure Key Vault,](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) aby ułatwić centralne zarządzanie kluczami i obracanie nimi bez konieczności modyfikowania połączonych usług usługi Azure Data Factory. Jest to również jedna z [najlepszych praktyk dotyczących ciągłej integracji/ciągłego wdrażania.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) 

### <a name="migrate-initial-snapshot-data"></a>Migrowanie danych migawki początkowej 

W przypadku małych tabel (czyli tabel o woluminie mniejszym niż 100 GB lub które można przeprowadzić migrację na platformę Azure w ciągu dwóch godzin), można wprowadzić dane ładowania każdego zadania kopiowania na tabelę. Aby uzyskać większą przepływność, można uruchomić wiele zadań kopiowania usługi Azure Data Factory, aby załadować oddzielne tabele jednocześnie. 

W ramach każdego zadania kopiowania, aby uruchamiać zapytania równoległe i kopiować dane przez partycje, można również osiągnąć pewien poziom równoległości przy użyciu [ `parallelCopies` ustawienia właściwości](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) z jedną z następujących opcji partycji danych:

- Aby uzyskać większą wydajność, zachęcamy do rozpoczęcia od wycinka danych.  Upewnij się, że `parallelCopies` wartość w ustawieniu jest mniejsza niż całkowita liczba partycji plasterka danych w tabeli na serwerze Netezza.  

- Jeśli wolumin każdej partycji plasterka danych jest nadal duży (na przykład 10 GB lub więcej), zachęcamy do przełączenia się na partycję zakresu dynamicznego. Ta opcja zapewnia większą elastyczność, aby zdefiniować liczbę partycji i objętość każdej partycji przez kolumnę partycji, górną granicę i dolną granicę.

W przypadku większych tabel (czyli tabel o woluminie 100 GB lub większym lub *nie można* przeprowadzić migracji na platformę Azure w ciągu dwóch godzin), zaleca się partycjonowanie danych według kwerendy niestandardowej, a następnie wykonanie każdej kopii zadania kopiowania kopii naraz. Aby uzyskać lepszą przepływność, można jednocześnie uruchomić wiele zadań kopiowania usługi Azure Data Factory. Dla każdego obiektu docelowego zadania kopiowania ładowania jednej partycji przez kwerendę niestandardową można zwiększyć przepływność, włączając równoległość za pośrednictwem plasterka danych lub zakresu dynamicznego. 

Jeśli każde zadanie kopiowania nie powiedzie się z powodu problemu przejściowego magazynu danych sieci lub magazynu danych, można ponownie uruchomić zadanie kopiowania, które nie powiodło się, aby ponownie załadować określoną partycję z tabeli. Inne zadania kopiowania, które ładują inne partycje nie są zagrożone.

Podczas ładowania danych do bazy danych usługi Azure SQL Data Warehouse, sugerujemy, aby włączyć PolyBase w ramach zadania kopiowania z magazynu obiektów blob platformy Azure jako tymczasowe.

### <a name="migrate-delta-data"></a>Migrowanie danych różnicowych 

Aby zidentyfikować nowe lub zaktualizowane wiersze z tabeli, użyj kolumny sygnatury czasowej lub klucza przyrostowego w schemacie. Następnie można zapisać najnowszą wartość jako wysoki znak wodny w tabeli zewnętrznej, a następnie użyć jej do filtrowania danych różnicowych przy następnym załadowaniu danych. 

Każda tabela może używać innej kolumny znaku wodnego do identyfikowania jej nowych lub zaktualizowanych wierszy. Sugerujemy utworzenie tabeli kontroli zewnętrznej. W tabeli każdy wiersz reprezentuje jedną tabelę na serwerze Netezza z określoną nazwą kolumny znaku wodnego i wysoką wartością znaku wodnego. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie

Jeśli przeprowadzasz migrację danych z serwera Netezza na platformę Azure, niezależnie od tego, czy serwer znajduje się lokalnie za zaporą firmy, czy w środowisku sieci wirtualnej, musisz zainstalować samodzielnie hostowane środowisko IR na komputerze z systemem Windows lub maszynie wirtualnej, czyli aparat, który jest używany do przenoszenia danych. Podczas instalowania samodzielnego podczerwony, zaleca się następujące podejście:

- Dla każdego komputera lub maszyny wirtualnej z systemem Windows rozpocznij konfigurację 32 procesorów wirtualnych i 128 GB pamięci. Podczas migracji danych można monitorować użycie procesora CPU i pamięci komputera podczerwego, aby sprawdzić, czy należy dalej skalować komputer w celu uzyskania lepszej wydajności lub zmniejszyć poziom pracy komputera w celu zaoszczędzenia kosztów.

- Można również skalować w poziomie, kojarząc maksymalnie cztery węzły z jednym samodzielnie hostowanym podczerwień. Pojedyncze zadanie kopiowania, które jest uruchomione względem samodzielnego hostowania podczerwony automatycznie stosuje wszystkie węzły maszyny Wirtualnej do kopiowania danych równolegle. Aby uzyskać wysoką dostępność, należy rozpocząć od czterech węzłów maszyn wirtualnych, aby uniknąć pojedynczego punktu awarii podczas migracji danych.

### <a name="limit-your-partitions"></a>Ogranicz partycje

Najlepszym rozwiązaniem jest przeprowadzenie weryfikacji wydajności koncepcji (POC) z reprezentatywnym przykładowym zestawem danych, dzięki czemu można określić odpowiedni rozmiar partycji dla każdego działania kopiowania. Sugerujemy załadowanie każdej partycji na platformę Azure w ciągu dwóch godzin.  

Aby skopiować tabelę, zacznij od pojedynczego działania kopiowania z jednym, samodzielnym urządzeniem podczerwonym. Stopniowo zwiększaj `parallelCopies` ustawienie na podstawie liczby partycji plasterka danych w tabeli. Sprawdź, czy cała tabela może zostać załadowana na platformę Azure w ciągu dwóch godzin, zgodnie z przepływnością, która wynika z zadania kopiowania. 

Jeśli nie można załadować na platformę Azure w ciągu dwóch godzin, a pojemność samodzielnie hostowanego węzła podczerwony i magazynu danych nie są w pełni używane, stopniowo zwiększaj liczbę równoczesnych działań kopiowania, aż do osiągnięcia limitu sieci lub limitu przepustowości magazynów danych. 

Monitoruj użycie procesora CPU i pamięci na samodzielnym komputerze podczerwieni i bądź gotowy do skalowania w górę komputera lub skalowania w poziomie na wiele komputerów, gdy zobaczysz, że procesor i pamięć są w pełni używane. 

W przypadku wystąpienia błędów ograniczania przepustowości, zgodnie z raportami działania kopiowania `parallelCopies` usługi Azure Data Factory, zmniejsz współbieżność lub ustawienie w usłudze Azure Data Factory lub rozważ zwiększenie przepustowości lub operacji we/wy na sekundę (IOPS) limitów sieci i magazynów danych. 


### <a name="estimate-your-pricing"></a>Oszacuj ceny 

Należy wziąć pod uwagę następujący potok, który jest skonstruowany do migracji danych z lokalnego serwera Netezza do bazy danych usługi Azure SQL Data Warehouse:

![Potok cenowy](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Załóżmy, że następujące instrukcje są prawdziwe: 

- Całkowita ilość danych wynosi 50 terabajtów (TB). 

- Przeprowadzamy migrację danych przy użyciu architektury pierwszego rozwiązania (serwer Netezza jest lokalny, za zaporą).

- Wolumin o pojemności 50 TB jest podzielony na partycje 500, a każde działanie kopiowania przenosi jedną partycję.

- Każde działanie kopiowania jest skonfigurowane z jednym samodzielnie hostowanym podczerwem na czterech komputerach i osiąga przepływność 20 megabajtów na sekundę (MB/s). (W ramach `parallelCopies` działania kopiowania jest ustawiona na 4, a każdy wątek do ładowania danych z tabeli osiąga przepływność 5 MB/s).

- Współbieżność ForEach jest ustawiona na 3, a przepływność agregacji wynosi 60 MB/s.

- W sumie trwa 243 godzin, aby zakończyć migrację.

Na podstawie powyższych założeń, oto szacowana cena: 

![Tabela cen](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Ceny przedstawione w powyższej tabeli są hipotetyczne. Rzeczywista cena zależy od rzeczywistej przepływności w twoim środowisku. Cena komputera z systemem Windows (z zainstalowanym samodzielnym podczerwem) nie jest wliczona w cenę. 

### <a name="additional-references"></a>Dodatkowa dokumentacja

Aby uzyskać więcej informacji, zobacz następujące artykuły i przewodniki:

- [Migrowanie danych z lokalnej relacyjnej bazy danych hurtowni na platformę Azure przy użyciu usługi Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Złącze Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Łącznik ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Łącznik magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik usługi Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Łącznik usługi Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Kopiuj przewodnik po dostrajaniu wydajności aktywności](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i konfigurowanie własnego środowiska Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Środowisko uruchomieniowe integracji hostowanego i skalowalność](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia związane z zabezpieczeniami podczas przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w usłudze Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiowanie danych przyrostowo z jednej tabeli](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Kopiowanie danych przyrostowo z wielu tabel](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Strona z cennikiem usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów przy użyciu usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)
