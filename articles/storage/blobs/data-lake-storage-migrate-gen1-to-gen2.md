---
title: Migrowanie Azure Data Lake Storage z Gen1 do Gen2
description: Przeprowadź migrację Azure Data Lake Storage z Gen1 do Gen2, która jest oparta na usłudze Azure Blob Storage i oferuje zestaw funkcji przeznaczonych do analizy danych Big Data.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9d160738208bcef7b066567137a3c3a9738c26a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727012"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrowanie Azure Data Lake Storage z Gen1 do Gen2

Dane, obciążenia i aplikacje można migrować z Data Lake Storage Gen1 do Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 jest oparta na [usłudze Azure Blob Storage](storage-blobs-introduction.md) i oferuje zestaw funkcji przeznaczonych do analizy danych Big Data. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) łączy funkcje [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), takie jak semantyka systemu plików, katalog i zabezpieczenia na poziomie pliku oraz skalowanie przy użyciu niskich kosztów, magazynu warstwowego, wysokiej dostępności/możliwości odzyskiwania po awarii z [usługi Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> Aby ułatwić odczytywanie, w tym artykule jest stosowany termin *Gen1* do odwoływania się do Azure Data Lake Storage Gen1 i termin *Gen2* do odwoływania się do Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Zalecane podejście

Aby przeprowadzić migrację do Gen2, zalecamy wykonanie poniższych czynności.

: heavy_check_mark: Krok 1: Ocena gotowości

: heavy_check_mark: Krok 2: przygotowanie do migracji

: heavy_check_mark: Krok 3: Migrowanie obciążeń danych i aplikacji

: heavy_check_mark: Krok 4: uruchomienie produkcyjne z Gen1 do Gen2

> [!NOTE]
> Gen1 i Gen2 są różnymi usługami, ale nie ma możliwości uaktualnienia w miejscu, ale wymaganego wysiłku migracji. 

### <a name="step-1-assess-readiness"></a>Krok 1. Ocena gotowości

1. Dowiedz się więcej o [ofercie Data Lake Storage Gen2ej](https://azure.microsoft.com/services/storage/data-lake-storage/); korzyści, koszty i ogólna architektura. 

2. [PORÓWNAJ możliwości](#gen1-gen2-feature-comparison) Gen1 z tymi z Gen2. 

3. Przejrzyj listę [znanych problemów](data-lake-storage-known-issues.md) , aby ocenić wszelkie luki w działaniu.

4. Gen2 obsługuje funkcje magazynu obiektów blob, takie jak [rejestrowanie diagnostyczne](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem życia magazynu obiektów BLOB](storage-lifecycle-management-concepts.md). Jeśli interesuje Cię korzystanie z dowolnej z tych funkcji, przejrzyj [bieżący poziom wsparcia](./data-lake-storage-supported-blob-storage-features.md).

5. Zapoznaj się z bieżącym stanem [obsługi ekosystemu platformy Azure](./data-lake-storage-multi-protocol-access.md) , aby upewnić się, że Gen2 obsługuje wszystkie usługi, od których zależą Twoje rozwiązania.

### <a name="step-2-prepare-to-migrate"></a>Krok 2. Przygotowanie do migracji

1. Zidentyfikuj zestawy danych, które zostaną zmigrowane.

   Skorzystaj z tej możliwości, aby wyczyścić zestawy danych, które nie są już używane. Jeśli nie planujesz migrować wszystkich danych jednocześnie, poświęć ten czas, aby zidentyfikować logiczne grupy danych, które można migrować w fazach.
   
2. Określ wpływ migracji na firmę.

   Rozważmy na przykład, czy możesz zapewnić jakikolwiek przestój w czasie migracji. Te zagadnienia mogą pomóc w zidentyfikowaniu odpowiedniego wzorca migracji i wybraniu najbardziej odpowiednich narzędzi.

3. Utwórz plan migracji. 

   Zalecamy stosowanie tych [wzorców migracji](#migration-patterns). Można wybrać jeden z tych wzorców, połączyć je ze sobą lub zaprojektować niestandardowy wzorzec własny.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Krok 3. Migrowanie danych, obciążeń i aplikacji

Migrowanie danych, obciążeń i aplikacji przy użyciu preferowanego wzorca. Zalecamy Weryfikowanie scenariuszy przyrostowo.

1. [Utwórz konto magazynu](../common/storage-account-create.md) i Włącz funkcję hierarchicznej przestrzeni nazw. 

2. Migruj dane. 

3. Skonfiguruj [usługi w swoich obciążeniach](./data-lake-storage-supported-azure-services.md) , aby wskazywały punkt końcowy usługi Gen2. 
   
4. Aktualizowanie aplikacji do używania interfejsów API Gen2. Zobacz przewodniki dla [platform .NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) i [rest](/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aktualizuj skrypty, aby używać poleceń [cmdlet programu PowerShell](data-lake-storage-directory-file-acl-powershell.md)i [interfejsu wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)Data Lake Storage Gen2.
   
6. Wyszukaj odwołania do identyfikatorów URI, które zawierają ciąg `adl://` w plikach kodu lub notesy datacegły, Apache Hive pliki HQL lub dowolny inny plik używany jako część obciążeń. Zamień te odwołania na [Gen2 sformatowany identyfikator URI](data-lake-storage-introduction-abfs-uri.md) nowego konta magazynu. Na przykład: identyfikator URI Gen1: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` może stać się `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. Skonfiguruj zabezpieczenia na koncie w taki sposób, aby obejmowały [role platformy Azure](../common/storage-auth-aad-rbac-portal.md), [zabezpieczenia na poziomie plików i folderów](data-lake-storage-access-control.md)oraz [zapory i sieci wirtualne usługi Azure Storage](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Krok 4: uruchomienie produkcyjne z Gen1 do Gen2

Po upewnieniu się, że Twoje aplikacje i obciążenia są stabilne w Gen2, możesz zacząć korzystać z Gen2, aby zaspokoić scenariusze biznesowe. Wyłącz wszystkie pozostałe potoki, które działają w Gen1 i likwidowanie konta Gen1. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Możliwości Gen1 vs Gen2

W tej tabeli porównano możliwości Gen1 z Gen2.

|Warstwowy |Gen1   |Gen2 |
|---|---|---|
|Organizacja danych|[Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)<br>Obsługa plików i folderów|[Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)<br>Obsługa kontenera, plików i folderów |
|Nadmiarowość geograficzna| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[Tożsamość zarządzana usługi AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Jednostki usług](../../active-directory/develop/app-objects-and-service-principals.md)|[Tożsamość zarządzana usługi AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Jednostki usług](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Współużytkowany klucz dostępu](/rest/api/storageservices/authorize-with-shared-key)|
|Autoryzacja|Zarządzanie — kontrola [RBAC na platformie Azure](../../role-based-access-control/overview.md)<br>Dane — [listy ACL](data-lake-storage-access-control.md)|Zarządzanie — kontrola [RBAC platformy Azure](../../role-based-access-control/overview.md)<br>Dane —  [listy kontroli dostępu](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|Szyfrowanie — dane przechowywane w spoczynku|Po stronie serwera — z kluczami [zarządzanymi](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) przez [firmę Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub zarządzanymi przez klienta|Po stronie serwera — z kluczami [zarządzanymi](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) przez [firmę Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub zarządzanymi przez klienta|
|Obsługa sieci wirtualnej|[Integracja z siecią wirtualną](../../data-lake-store/data-lake-store-network-security.md)|[Punkty końcowe usługi](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [prywatne punkty końcowe](../common/storage-private-endpoints.md)|
|Środowisko deweloperskie|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [interfejs wiersza polecenia platformy Azure](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Ogólnie dostępne — [rest](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Publiczna wersja zapoznawcza — [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)|
|Dzienniki zasobów|Dzienniki klasyczne<br>[Azure Monitor zintegrowany](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Dzienniki klasyczne](../common/storage-analytics-logging.md) — ogólnie dostępne<br>[Azure monitor zintegrowane](monitor-blob-storage.md) — wersja zapoznawcza|
|Ekosystem|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 i nowsze)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 i nowsze)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 Gen2 wzorców

Wybierz wzorzec migracji, a następnie zmodyfikuj ten wzorzec zgodnie z wymaganiami.

|Wzorzec migracji | Szczegóły |
|---|---|
|**Unieś i Przenieś**|Najprostszym wzorcem. Idealne rozwiązanie, jeśli potoki danych mogą zapewnić przestoje.|
|**Kopia przyrostowa**|Podobnie jak w przypadku *podnoszenia i przesunięcia*, ale z mniejszym przestojem. Idealne rozwiązanie w przypadku dużych ilości danych, które trwają dłużej niż kopiowanie.|
|**Podwójny potok**|Idealne rozwiązanie w przypadku potoków, które nie zapewniają przestojów.|
|**Synchronizacja dwukierunkowa**|Podobnie jak *podwójny potok*, ale z bardziej stopniowanym podejściem, które jest odpowiednie dla bardziej złożonych potoków.|

Przyjrzyjmy się bliżej każdej z wzorców.
 
### <a name="lift-and-shift-pattern"></a>Wzorzec podnoszenia i przesunięcia

Jest to najprostszy wzorzec. 

1. Zatrzymaj wszystkie zapisy w Gen1.

2. Przenieś dane z Gen1 do Gen2. Zalecamy [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Listy ACL są kopiowane z danymi.

3. Wypróbowanie operacji pozyskiwania i obciążeń do Gen2.

4. Likwidowanie Gen1.

Zapoznaj się z naszym przykładowym kodem dotyczącym wzorca dźwigu i przesunięcia w ramach [przykładu migracji i przesunięcia](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md).

> [!div class="mx-imgBorder"]
> ![wzorzec podnoszenia i przesunięcia](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Zagadnienia dotyczące używania wzorca dźwigu i przesunięcia

: heavy_check_mark: uruchomienie produkcyjne od Gen1 do Gen2 dla wszystkich obciążeń w tym samym czasie.

: heavy_check_mark: oczekuje przestoju podczas migracji i okresu uruchomienie produkcyjne.

: heavy_check_mark: idealne dla potoków, które mogą zapewnić przestoje i można uaktualnić jednocześnie wszystkie aplikacje.

### <a name="incremental-copy-pattern"></a>Wzorzec kopiowania przyrostowego

1. Rozpocznij przeniesienie danych z Gen1 do Gen2. Zalecamy [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Listy ACL są kopiowane z danymi.

2. Przyrostowe kopiowanie nowych danych z Gen1.

3. Po skopiowaniu wszystkich danych Zatrzymaj wszystkie operacje zapisu do Gen1 i punkty obciążenia do Gen2.

4. Likwidowanie Gen1.

Zapoznaj się z naszym przykładowym kodem wzorca kopiowania przyrostowego w ramach [przykładu migracji kopii przyrostowej](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md).


> [!div class="mx-imgBorder"]
> ![Wzorzec kopiowania przyrostowego](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Zagadnienia dotyczące używania wzorca kopiowania przyrostowego:

: heavy_check_mark: uruchomienie produkcyjne od Gen1 do Gen2 dla wszystkich obciążeń w tym samym czasie.

: heavy_check_mark: oczekiwano przestoju w okresie uruchomienie produkcyjne.

: heavy_check_mark: idealne dla potoków, w których wszystkie aplikacje są uaktualnione w tym samym czasie, ale kopiowanie danych wymaga więcej czasu.

### <a name="dual-pipeline-pattern"></a>Podwójny wzorzec potoku

1. Przenieś dane z Gen1 do Gen2. Zalecamy [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Listy ACL są kopiowane z danymi.

2. Pozyskiwanie nowych danych zarówno w Gen1, jak i w Gen2.

3. Punkty obciążeń do Gen2.

4. Zatrzymaj wszystkie zapisy w Gen1, a następnie likwidowanie Gen1.

Zapoznaj się z [naszym przykładowym](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md)kodem, aby uzyskać wzorzec podwójnego potoku.

> [!div class="mx-imgBorder"]
> ![Podwójny wzorzec potoku](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Zagadnienia dotyczące korzystania z podwójnego wzorca potoku:

: heavy_check_mark: potoki Gen1 i Gen2 są uruchamiane obok siebie.

: heavy_check_mark: obsługuje zero przestojów.

: heavy_check_mark: idealne w sytuacjach, gdy obciążenia i aplikacje nie mogą zapewnić przestoju i można pozyskać je na obu kontach magazynu.

### <a name="bi-directional-sync-pattern"></a>Wzorzec synchronizacji dwukierunkowej

1. Skonfiguruj replikację dwukierunkową między Gen1 i Gen2. Zalecamy [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Oferuje funkcję naprawy dla istniejących danych.

3. Po zakończeniu wszystkich operacji przenoszenia Zatrzymaj wszystkie zapisy do Gen1 i Wyłącz replikację dwukierunkową.

4. Likwidowanie Gen1.

Zapoznaj się z naszym przykładowym kodem dla wzorca synchronizacji dwukierunkowej w naszym [przykładowej migracji z synchronizacją dwukierunkową](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md).

> [!div class="mx-imgBorder"]
> ![Wzorzec dwukierunkowy](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Zagadnienia dotyczące korzystania ze wzorca synchronizacji dwukierunkowej:

: heavy_check_mark: idealne dla złożonych scenariuszy obejmujących dużą liczbę potoków i zależności, w przypadku których podejście etapowe może być bardziej zrozumiałe.  

: heavy_check_mark: nakład migracji jest wysoki, ale zapewnia obsługę równoczesną dla Gen1 i Gen2.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat różnych części konfigurowania zabezpieczeń dla konta magazynu. Zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](./security-recommendations.md).
- Zoptymalizuj wydajność Data Lake Store. Zobacz [optymalizacja Azure Data Lake Storage Gen2 pod kątem wydajności](data-lake-storage-performance-tuning-guidance.md)
- Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi zarządzania Data Lake Store. Zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi używania Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)