---
title: Migrowanie magazynu usługi Azure Data Lake z gen1 do gen2
description: Migrowanie usługi Azure Data Lake Storage z gen1 do gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677146"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrowanie magazynu usługi Azure Data Lake z gen1 do gen2

Można migrować dane, obciążenia i aplikacje z usługi Data Lake Storage Gen1 do Data Lake Storage Gen2.

Usługa Azure Data Lake Storage Gen2 jest oparta na [magazynie obiektów Blob platformy Azure](storage-blobs-introduction.md) i zapewnia zestaw funkcji dedykowanych do analizy dużych zbiorów danych. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) łączy w sobie funkcje z [usługi Azure Data Lake Storage Gen1,](https://docs.microsoft.com/azure/data-lake-store/index)takie jak semantyka systemu plików, zabezpieczenia katalogu i poziomu plików oraz skala z tanim, warstwowym magazynem, wysoką dostępnością/odzyskiwaniem po awarii z magazynu obiektów [Blob platformy Azure.](storage-blobs-introduction.md)

> [!NOTE]
> Aby ułatwić czytanie, w tym artykule użyto terminu *Gen1* w odniesieniu do usługi Azure Data Lake Storage Gen1 i terminu *Gen2* w odniesieniu do usługi Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Zalecane podejście

Aby przeprowadzić migrację do Gen2, zaleca się następujące podejście.

:heavy_check_mark: Krok 1: Ocena gotowości

:heavy_check_mark: Krok 2: Przygotowanie do migracji

:heavy_check_mark: Krok 3: Migrowanie obciążeń danych i aplikacji

:heavy_check_mark: Krok 4: Przekroj z Gen1 do Gen2

> [!NOTE]
> Gen1 i Gen2 są różne usługi, nie ma doświadczenia w miejscu uaktualnienia, celowe wysiłku migracji wymagane. 

### <a name="step-1-assess-readiness"></a>Krok 1: Ocena gotowości

1. Dowiedz się więcej o [ofercie Data Lake Storage Gen2;](https://azure.microsoft.com/services/storage/data-lake-storage/) to korzyści, koszty i ogólna architektura. 

2. [Porównaj możliwości](#gen1-gen2-feature-comparison) Gen1 z możliwościami Gen2. 

3. Przejrzyj listę [znanych problemów,](data-lake-storage-known-issues.md) aby ocenić luki w funkcjonalności.

4. Gen2 obsługuje funkcje magazynu obiektów Blob, takie jak rejestrowanie [diagnostyczne, warstwy dostępu](storage-blob-storage-tiers.md)i zasady [zarządzania cyklem](../common/storage-analytics-logging.md) [życia magazynu obiektów blob.](storage-lifecycle-management-concepts.md) Jeśli korzystasz z którejkolwiek z tych funkcji, zapoznaj się [z bieżącym poziomem pomocy technicznej.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)

5. Przejrzyj bieżący stan [obsługi ekosystemu platformy Azure,](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) aby upewnić się, że gen2 obsługuje wszystkie usługi, od których zależą rozwiązania.

### <a name="step-2-prepare-to-migrate"></a>Krok 2: Przygotuj się do migracji

1. Zidentyfikuj zestawy danych, które będą migrowane.

   Skorzystaj z tej okazji, aby wyczyścić zestawy danych, które nie są już używane. Jeśli nie planujesz migracji wszystkich danych w tym czasie, Poświęć ten czas, aby zidentyfikować logiczne grupy danych, które można migrować w fazach.
   
2. Określ wpływ migracji na Twoją firmę.

   Na przykład należy rozważyć, czy można sobie pozwolić na przestoje podczas migracji ma miejsce. Te zagadnienia mogą pomóc w zidentyfikowaniu odpowiedniego wzorca migracji i wybraniu najbardziej odpowiednich narzędzi.

3. Tworzenie planu migracji. 

   Zalecamy te [wzorce migracji](#migration-patterns). Możesz wybrać jeden z tych wzorów, połączyć je ze sobą lub zaprojektować własny niestandardowy wzór.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Krok 3: Migrowanie danych, obciążeń i aplikacji

Migruj dane, obciążenia i aplikacje przy użyciu preferowanego wzorca. Zaleca się, aby sprawdzić poprawność scenariuszy przyrostowo.

1. [Utwórz konto magazynu](data-lake-storage-quickstart-create-account.md) i włącz hierarchiczną funkcję obszaru nazw. 

2. Migrowanie danych. 

3. Skonfiguruj [usługi w obciążeniach,](data-lake-storage-integrate-with-azure-services.md) aby wskazywały punkt końcowy Gen2. 
   
4. Aktualizowanie aplikacji w celu używania interfejsów API Gen2. Zobacz przewodniki dla [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) i [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aktualizowanie skryptów w celu [używania poleceń cmdlet programu PowerShell](data-lake-storage-directory-file-acl-powershell.md)usługi Data Lake Storage Gen2 i [interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure.](data-lake-storage-directory-file-acl-cli.md)
   
6. Wyszukaj odwołania do identyfikatora URI, które zawierają ciąg `adl://` w plikach kodu lub w notesach Databricks, plikach APache Hive HQL lub innych plikach używanych jako część obciążeń. Zastąp te odwołania [identyfikatorem URI w formacie Gen2](data-lake-storage-introduction-abfs-uri.md) nowego konta magazynu. Na przykład: identyfikator URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` Gen1: może stać się `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

7. Skonfiguruj zabezpieczenia na koncie tak, aby uwzględniał [role kontroli dostępu oparte na rolach (RBAC),](../common/storage-auth-aad-rbac-portal.md)zabezpieczenia na poziomie plików i [folderów](data-lake-storage-access-control.md)oraz [zapory usługi Azure Storage i sieci wirtualne.](../common/storage-network-security.md)

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Krok 4: Przekroj z Gen1 do Gen2

Po upewnieniu się, że aplikacje i obciążenia są stabilne w gen2, można rozpocząć korzystanie z Gen2, aby spełnić scenariusze biznesowe. Wyłącz wszystkie pozostałe potoki, które są uruchomione w gen1 i likwiduj swoje konto Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Możliwości Gen1 i Gen2

W tej tabeli porównano możliwości Gen1 z możliwościami Gen2.

|Obszar |Gen1   |Gen2 |
|---|---|---|
|Organizacja danych|[Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)<br>Obsługa plików i folderów|[Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)<br>Obsługa kontenerów, plików i folderów |
|Nadmiarowość geograficzna| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[Tożsamość zarządzana AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Podmioty świadczące usługi](../../active-directory/develop/app-objects-and-service-principals.md)|[Tożsamość zarządzana AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Podmioty świadczące usługi](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Klucz dostępu współdzielonego](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autoryzacja|Zarządzanie - [RBAC](../../role-based-access-control/overview.md)<br>Dane — [listy ACL](data-lake-storage-access-control.md)|Zarządzanie – [RBAC](../../role-based-access-control/overview.md)<br>Dane - [Listy ACL](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Szyfrowanie – dane w spoczynku|Po stronie serwera — z [kluczami zarządzanymi przez firmę Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub [zarządzanymi przez klienta](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Po stronie serwera — z [kluczami zarządzanymi przez firmę Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub [zarządzanymi przez klienta](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Obsługa sieci wirtualnych|[Integracja sieci wirtualnej](../../data-lake-store/data-lake-store-network-security.md)|[Punkty końcowe usługi](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Prywatne punkty końcowe](../common/storage-private-endpoints.md)|
|Doświadczenie programistów|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Ogólnie dostępne - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Publiczna wersja zapoznawcza — [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Dzienniki diagnostyczne|Dzienniki klasyczne<br>[Zintegrowany monitor Azure Monitor](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Dzienniki klasyczne](../common/storage-analytics-logging.md) — ogólnie dostępne<br>Integracja z monitorem platformy Azure — tbd osi czasu|
|Ekosystem|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 i powyżej),](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html) [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 i powyżej),](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2) [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Wzory Gen1 do Gen2

Wybierz wzorzec migracji, a następnie zmodyfikuj ten wzorzec zgodnie z potrzebami.

|||
|---|---|
|**Podnoszenie i przesuwanie**|Najprostszy wzór. Idealne rozwiązanie, jeśli potoki danych mogą pozwolić sobie na przestoje.|
|**Kopia przyrostowa**|Podobne do *podnoszenia i zmiany,* ale z mniejszą ilością przestojów. Idealny do kopiowania dużych ilości danych, których kopiowanie trwa dłużej.|
|**Podwójny rurociąg**|Idealny do rurociągów, które nie mogą sobie pozwolić na przestoje.|
|**Synchronizacja dwukierunkowa**|Podobnie jak *podwójny rurociąg*, ale z bardziej stopniowym podejściem, które nadaje się do bardziej skomplikowanych rurociągów.|

Przyjrzyjmy się bliżej każdemu wzorcowi.
 
### <a name="lift-and-shift-pattern"></a>Wzór podnoszenia i zmiany biegów

Jest to najprostszy wzór.

1. Zatrzymaj wszystkie zapisy do Gen1.

2. Przenoszenie danych z Gen1 do Gen2. Zalecamy [usługę Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Listy ACL kopiują dane.

3. Wskaż operacje pozyskiwania i obciążenia do Gen2.

4. Likwidacja Gen1.

> [!div class="mx-imgBorder"]
> ![wzór podnoszenia i zmiany biegów](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Zagadnienia dotyczące stosowania wzorca podnoszenia i zmiany biegów

:heavy_check_mark: Cutover z Gen1 do Gen2 dla wszystkich obciążeń w tym samym czasie.

:heavy_check_mark: Spodziewaj się przestojów podczas migracji i okresu przecięcia.

:heavy_check_mark: Idealny do rurociągów, które mogą sobie pozwolić na przestoje, a wszystkie aplikacje mogą być uaktualniane jednocześnie.

### <a name="incremental-copy-pattern"></a>Wzorzec kopiowania przyrostowego

1. Rozpocznij przenoszenie danych z Gen1 do Gen2. Zalecamy [usługę Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Listy ACL kopiują dane.

2. Przyrostowo kopiuj nowe dane z gen1.

3. Po skopiowaniu wszystkich danych zatrzymaj wszystkie zapisy w Gen1 i wskaż obciążenia do Gen2.

4. Likwidacja Gen1.

> [!div class="mx-imgBorder"]
> ![Wzorzec kopiowania przyrostowego](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Zagadnienia dotyczące używania wzorca kopiowania przyrostowego:

:heavy_check_mark: Cutover z Gen1 do Gen2 dla wszystkich obciążeń w tym samym czasie.

:heavy_check_mark: Spodziewaj się przestojów tylko w okresie przecięcia.

:heavy_check_mark: Idealny dla potoków, w których wszystkie aplikacje uaktualniono jednocześnie, ale kopia danych wymaga więcej czasu.

### <a name="dual-pipeline-pattern"></a>Wzór podwójnego rurociągu

1. Przenoszenie danych z Gen1 do Gen2. Zalecamy [usługę Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Listy ACL kopiują dane.

2. Połknąć nowe dane zarówno do Gen1, jak i Gen2.

3. Wskaż obciążenia do Gen2.

4. Zatrzymaj wszystkie zapisy do Gen1, a następnie likwiduj Gen1.

> [!div class="mx-imgBorder"]
> ![Wzór podwójnego rurociągu](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Zagadnienia dotyczące korzystania z wzorca podwójnego potoku:

:heavy_check_mark: Rurociągi Gen1 i Gen2 działają obok siebie.

:heavy_check_mark: Obsługuje zero przestojów.

:heavy_check_mark: Idealne rozwiązanie w sytuacjach, w których obciążenia i aplikacje nie mogą sobie pozwolić na przestoje i można je łzażyć na obu kontach magazynu.

### <a name="bi-directional-sync-pattern"></a>Wzorzec synchronizacji dwukierunkowej

1. Konfigurowanie replikacji dwukierunkowej między Gen1 i Gen2. Polecamy [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Oferuje funkcję naprawy istniejących danych.

3. Po zakończeniu wszystkich ruchów zatrzymaj wszystkie zapisy w Gen1 i wyłącz replikację dwukierunkową.

4. Likwidacja Gen1.

> [!div class="mx-imgBorder"]
> ![Wzór dwukierunkowy](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Zagadnienia dotyczące używania wzorca synchronizacji dwukierunkowej:

:heavy_check_mark: Idealny dla złożonych scenariuszy, które obejmują dużą liczbę potoków i zależności, gdzie podejście stopniowe może mieć większy sens.  

:heavy_check_mark: Nakład migracji jest wysoki, ale zapewnia wsparcie side-by-side dla Gen1 i Gen2.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o różnych częściach konfigurowania zabezpieczeń konta magazynu. Zobacz [przewodnik po zabezpieczeniach usługi Azure Storage](../common/storage-security-guide.md).
- Zoptymalizuj wydajność magazynu Usługi Data Lake Store. Aby uzyskać [wydajność, zobacz Optymalizowanie usługi Azure Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md)
- Zapoznaj się z najlepszymi rozwiązaniami w zakresie zarządzania magazynem usługi Data Lake Store. Zobacz [najważniejsze wskazówki dotyczące korzystania z usługi Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

