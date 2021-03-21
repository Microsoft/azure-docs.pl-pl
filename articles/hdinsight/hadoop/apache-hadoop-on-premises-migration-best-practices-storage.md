---
title: 'Magazyn: Migrowanie Apache Hadoop lokalnych do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące magazynu na potrzeby migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715415"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight

W tym artykule przedstawiono zalecenia dotyczące magazynu danych w systemach usługi Azure HDInsight. Jest ona częścią serii, która oferuje najlepsze rozwiązania ułatwiające Migrowanie lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Wybieranie odpowiedniego systemu magazynu dla klastrów usługi HDInsight

Strukturę katalogów lokalnego systemu plików Apache Hadoop (HDFS) można utworzyć w usłudze Azure Blob Storage lub Azure Data Lake Storage. Następnie można bezpiecznie usunąć klastry usługi HDInsight używane do obliczeń bez utraty danych użytkownika. Obie usługi mogą być używane zarówno jako domyślny system plików, jak i dodatkowy system plików dla klastra usługi HDInsight. Klaster usługi HDInsight i konto magazynu muszą być hostowane w tym samym regionie.

### <a name="azure-storage"></a>Azure Storage

Klastry usługi HDInsight mogą używać kontenera obiektów BLOB w usłudze Azure Storage jako domyślny system plików lub dodatkowy system plików. Konto magazynu w warstwie Standardowa jest obsługiwane w przypadku klastrów usługi HDInsight. Warstwa Premier nie jest obsługiwana. Domyślny kontener obiektów blob przechowuje informacje dotyczące klastra, takie jak dzienniki i historia zadań. Udostępnianie jednego kontenera obiektów BLOB jako domyślnego systemu plików dla wielu klastrów nie jest obsługiwane.

Konta magazynu zdefiniowane w procesie tworzenia i ich odpowiednie klucze są przechowywane w `%HADOOP_HOME%/conf/core-site.xml` węzłach klastra. Dostęp do nich można uzyskać również w sekcji "niestandardowa witryna podstawowa" w konfiguracji systemu plików HDFS w interfejsie użytkownika Ambari. Klucz konta magazynu jest domyślnie szyfrowany i w celu odszyfrowania kluczy przed przekazaniem do demonów Hadoop jest używany skrypt odszyfrowywania niestandardowego. Do zadań, w tym Hive, MapReduce, przesyłania strumieniowego Hadoop i trzody chlewnej, należy przenieść do nich opis kont magazynu i metadanych.

Usługa Azure Storage może być replikowana geograficznie. Chociaż replikacja geograficzna umożliwia odzyskiwanie geograficzne i nadmiarowość danych, przełączenie w tryb failover do lokalizacji z replikacją geograficzną poważnie wpływa na wydajność i może pociągnąć za sobą dodatkowe koszty. Zalecenie polega na wybraniu replikacji geograficznej i tylko wtedy, gdy wartość danych jest cenny koszt dodatkowy.

Jeden z następujących formatów może służyć do uzyskiwania dostępu do danych przechowywanych w usłudze Azure Storage:

|Format dostępu do danych |Opis |
|---|---|
|`wasb:///`|Dostęp do magazynu domyślnego przy użyciu nieszyfrowanej komunikacji.|
|`wasbs:///`|Dostęp do magazynu domyślnego przy użyciu komunikacji szyfrowanej.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Używane podczas komunikacji z kontem magazynu innym niż domyślne. |

[Elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../../storage/common/scalability-targets-standard-account.md) zawierają bieżące limity dla kont usługi Azure Storage. Jeśli wymagania aplikacji przekraczają tarcze skalowalności pojedynczego konta magazynu, aplikacja może zostać skompilowana w celu użycia wielu kont magazynu, a następnie partycjonowania obiektów danych na tych kontach magazynu.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) dostarcza metryki dla wszystkich usług magazynu, a Azure Portal można skonfigurować metryki zbierania do wizualizacji za poorednictwem wykresów. Można utworzyć alerty w celu powiadomienia o osiągnięciu progów dla metryk zasobów magazynu.

Usługa Azure Storage oferuje [nietrwałe usuwanie obiektów BLOB](../../storage/blobs/soft-delete-blob-overview.md) , które ułatwiają odzyskiwanie danych po ich przypadkowe zmodyfikowaniu lub usunięciu przez aplikację lub innego użytkownika konta magazynu.

Można tworzyć [migawki obiektów BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Migawka to wersja obiektu BLOB w trybie tylko do odczytu, która jest wykonywana w danym momencie i udostępnia sposób tworzenia kopii zapasowej obiektu BLOB. Po utworzeniu migawki można ją odczytywać, kopiować lub usuwać, ale nie modyfikować.

> [!Note]
> W przypadku starszych wersji lokalnych dystrybucji usługi Hadoop, które nie mają certyfikatu "wasbs", należy je zaimportować do magazynu zaufania Java.

Do importowania certyfikatów do magazynu zaufania Java można użyć następujących metod:

Pobierz certyfikat TLS/SSL usługi Azure BLOB do pliku

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Zaimportuj powyższy plik do magazynu Java Trusts na wszystkich węzłach

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Sprawdź, czy dodany certyfikat znajduje się w magazynie zaufania

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Korzystanie z usługi Azure Storage z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../../storage/common/scalability-targets-standard-account.md)
- [Cele skalowalności i wydajności dla usługi BLOB Storage](../../storage/blobs/scalability-targets.md)
- [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../../storage/blobs/storage-performance-checklist.md)
- [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorowanie konta magazynu w witrynie Azure Portal](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

Azure Data Lake Storage Gen1 implementuje model kontroli dostępu do stylu systemu plików HDFS i POSIX. Zapewnia ona integrację pierwszej klasy z usługą Azure AD w celu uzyskania szczegółowej kontroli dostępu. Nie ma ograniczeń dotyczących rozmiaru danych, które mogą być przechowywane, lub zdolności do uruchamiania analizy równoległej.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Tworzenie klastrów usługi HDInsight z Data Lake Storage Gen1 przy użyciu Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

Azure Data Lake Storage Gen2 to Najnowsza oferta magazynu. Łączy podstawowe możliwości od pierwszej generacji Azure Data Lake Storage Gen1 za pomocą punktu końcowego systemu plików zgodnego z usługą Hadoop bezpośrednio zintegrowanego z platformą Azure Blob Storage. To ulepszenie umożliwia łączenie skalowalności i kosztów magazynu obiektów z niezawodnością i wydajnością zwykle skojarzoną tylko z lokalnymi systemami plików.

Azure Data Lake Storage Gen 2 jest oparta na [usłudze Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) i umożliwia interfejsowanie danych przy użyciu zarówno odmian systemu plików, jak i magazynu obiektów. Funkcje z [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), takie jak semantyka systemu plików, zabezpieczenia na poziomie plików i skalowanie, są połączone z niskimi kosztami magazynowymi, wysoką dostępnością/odzyskiwaniem po awarii oraz dużym ekosystemem w [usłudze Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md). W Data Lake Storage Gen2, wszystkie jakości magazynu obiektów pozostaną, dodając zalety interfejsu systemu plików zoptymalizowanego pod kątem obciążeń analitycznych.

Podstawową funkcją Data Lake Storage Gen2 jest dodanie [hierarchicznej przestrzeni nazw](../../storage/blobs/data-lake-storage-namespace.md) do usługi BLOB Storage, która organizuje obiekty/pliki w hierarchii katalogów na potrzeby wykonywania dostępu do danych. Struktura hierarchiczna umożliwia wykonywanie operacji, takich jak zmiana nazwy lub usuwanie katalogu, aby były jednocyfrowymi operacjami metadanych w katalogu, a nie wyliczeniem i przetwarzaniem wszystkich obiektów, które współużytkują prefiks nazwy katalogu.

W przeszłości Analiza oparta na chmurze musiała być naruszona w obszarach wydajności, zarządzania i zabezpieczeń. Najważniejsze funkcje Azure Data Lake Storage (ADLS) Gen2 są następujące:

- **Dostęp zgodny** z usługą Hadoop: Azure Data Lake Storage Gen2 umożliwia zarządzanie danymi i uzyskiwanie do nich dostępu tak samo jak w przypadku [rozproszony system plików Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [Sterownik ABFS](../../storage/blobs/data-lake-storage-abfs-driver.md) jest dostępny we wszystkich środowiskach Apache Hadoop, które znajdują się w [usłudze Azure HDInsight](../index.yml). Ten sterownik umożliwia dostęp do danych przechowywanych w Data Lake Storage Gen2.

- **Nadzbiór uprawnień systemu POSIX**: model zabezpieczeń dla Data Lake Gen2 w pełni obsługuje uprawnienia ACL i POSIX oraz kilka dodatkowych postanowień dotyczących Data Lake Storage Gen2. Ustawienia można skonfigurować za poorednictwem narzędzi administracyjnych lub platform, takich jak Hive i Spark.

- **Oszczędne**: Data Lake Storage Gen2 funkcje magazynu kosztów i transakcji. Jako że dane są przenoszone przez cały cykl życia, stawki rozliczeń zmieniają się, aby zminimalizować koszty dzięki wbudowanym funkcjom, takim jak [cykl życiowy usługi Azure Blob Storage](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Współpracuje z narzędziami, strukturami i aplikacjami usługi BLOB Storage**: Data Lake Storage Gen2 nadal pracuje z szeroką gamę narzędzi, platform i aplikacji, które już istnieją dla usługi BLOB Storage.

- **Zoptymalizowany sterownik**: Sterownik systemu plików obiektów blob platformy Azure (ABFS) jest [zoptymalizowany pod](../../storage/blobs/data-lake-storage-abfs-driver.md) kątem analizy danych Big Data. Odpowiednie interfejsy API REST są nadane przez punkt końcowy systemu plików DFS, dfs.core.windows.net.

Jeden z następujących formatów może służyć do uzyskiwania dostępu do danych przechowywanych w ADLS Gen2:
- `abfs:///`: Dostęp do Data Lake Storage domyślnego klastra.
- `abfs://file_system@account_name.dfs.core.windows.net`: Używane podczas komunikowania się z Data Lake Storageem innym niż domyślny.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Sterownik systemu plików obiektów blob platformy Azure (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Zabezpieczanie kluczy usługi Azure Storage w ramach lokalnej konfiguracji klastra Hadoop

Klucze usługi Azure Storage, które są dodawane do plików konfiguracji Hadoop, ustanawiają łączność między lokalnym systemem HDFS i magazynem obiektów blob platformy Azure. Te klucze mogą być chronione przez zaszyfrowanie ich przy użyciu struktury dostawcy poświadczeń usługi Hadoop. Po zaszyfrowaniu można je przechowywać i uzyskiwać do nich dostęp bezpieczny.

**Aby zainicjować obsługę administracyjną poświadczeń:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Aby dodać powyższą ścieżkę dostawcy do core-site.xml lub konfiguracji Ambari w obszarze Custom Core-site:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Właściwość ścieżka dostawcy może być również dodawana do wiersza polecenia pomocą distcp zamiast zapisywania klucza na poziomie klastra w core-site.xml w następujący sposób:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Ograniczanie dostępu do danych usługi Azure Storage za pomocą SAS

Usługa HDInsight domyślnie ma pełny dostęp do danych na kontach usługi Azure Storage skojarzonych z klastrem. Sygnatury dostępu współdzielonego (SAS) w kontenerze obiektów BLOB mogą służyć do ograniczania dostępu do danych, takich jak zapewnianie użytkownikom dostępu tylko do odczytu do danych.

### <a name="using-the-sas-token-created-with-python"></a>Korzystanie z tokenu sygnatury dostępu współdzielonego utworzonego przy użyciu języka Python

1. Otwórz plik  [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) i Zmień następujące wartości:

    |Właściwość tokenu|Opis|
    |---|---|
    |policy_name|Nazwa, która ma zostać użyta do utworzenia zasad przechowywanych.|
    |storage_account_name|Nazwa konta magazynu.|
    |storage_account_key|Klucz dla konta magazynu.|
    |storage_container_name|Kontener na koncie magazynu, do którego ma zostać ograniczony dostęp.|
    |example_file_path|Ścieżka do pliku, który jest przekazywany do kontenera.|

2. Plik SASToken.py zawiera `ContainerPermissions.READ + ContainerPermissions.LIST` uprawnienia i można go dostosować w zależności od przypadku użycia.

3. Wykonaj skrypt w następujący sposób: `python SASToken.py`

4. Po zakończeniu działania skryptu jest wyświetlany token sygnatury dostępu współdzielonego podobny do następującego: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Aby ograniczyć dostęp do kontenera z sygnaturą dostępu współdzielonego, Dodaj niestandardowy wpis do konfiguracji lokacja podstawowa dla klastra w obszarze Ambari HDFS config zaawansowane niestandardowe rdzeń-lokacja Dodaj właściwość.

6. Użyj następujących wartości dla pól **klucza** i **wartości** :

    **Klucz**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **wartość**: klucz sygnatury dostępu współdzielonego zwracany przez aplikację języka Python z kroku 4 powyżej.

7. Kliknij przycisk **Dodaj** , aby zapisać ten klucz i wartość, a następnie kliknij przycisk **Zapisz** , aby zapisać zmiany konfiguracji. Po wyświetleniu monitu Dodaj opis zmiany (na przykład "Dodawanie dostępu do magazynu SAS"), a następnie kliknij przycisk **Zapisz**.

8. W interfejsie użytkownika sieci Web Ambari na liście po lewej stronie wybierz pozycję HDFS, a następnie wybierz pozycję **Uruchom ponownie wszystkie** z listy rozwijanej akcje usługi po prawej stronie. Po wyświetleniu monitu wybierz pozycję **Potwierdź ponowne uruchomienie wszystkich**.

9. Powtórz ten proces dla MapReduce2 i PRZĘDZy.

Istnieją trzy ważne kwestie dotyczące używania tokenów SAS na platformie Azure:

1. Gdy tokeny SAS są tworzone z uprawnieniami "READ + LIST", użytkownicy, którzy uzyskują dostęp do kontenera obiektów blob z tokenem SAS, nie będą mogli "zapisywać ani usuwać" danych. Użytkownicy, którzy uzyskują dostęp do kontenera obiektów BLOB przy użyciu tego tokenu SAS i próbują wykonać operację zapisu lub usuwania, otrzymają komunikat podobny do tego `"This request is not authorized to perform this operation"` .

2. Gdy tokeny sygnatury dostępu współdzielonego są generowane z `READ + LIST + WRITE` uprawnieniami (w celu ograniczenia `DELETE` tylko), poleceniami, takimi jak `hadoop fs -put` Pierwszy zapis do `\_COPYING\_` pliku, a następnie próba zmiany nazwy pliku. Ta operacja systemu plików HDFS jest mapowana na `copy+delete` WASB. Ponieważ `DELETE` nie podano uprawnienia, "Put" nie powiedzie się. `\_COPYING\_`Operacja jest funkcją usługi Hadoop zaprojektowaną w celu zapewnienia pewnej kontroli współbieżności. Obecnie nie ma możliwości ograniczenia tylko operacji usuwania bez wpływu na operacje zapisu.

3. Niestety, Dostawca poświadczeń usługi Hadoop i dostawca klucza odszyfrowywania (ShellDecryptionKeyProvider) obecnie nie współpracują z tokenami sygnatury dostępu współdzielonego, dlatego obecnie nie można chronić przed widocznością.

Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego usługi Azure Storage w celu ograniczenia dostępu do danych w usłudze HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Korzystanie z szyfrowania i replikacji danych

Wszystkie dane zapisywane w usłudze Azure Storage są automatycznie szyfrowane przy użyciu [szyfrowanie usługi Storage (SSE)](../../storage/common/storage-service-encryption.md). Dane na koncie usługi Azure Storage są zawsze replikowane w celu zapewnienia wysokiej dostępności. Podczas tworzenia konta magazynu można wybrać jedną z następujących opcji replikacji:

- [Magazyn lokalnie nadmiarowy (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Magazyn strefowo nadmiarowy (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Magazyn geograficznie nadmiarowy (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Magazyn geograficznie nadmiarowy dostępny do odczytu (RA-GRS)](../../storage/common/storage-redundancy.md)

Usługa Azure Storage zapewnia Magazyn lokalnie nadmiarowy (LRS), ale dane o kluczowym znaczeniu należy kopiować na inne konto usługi Azure Storage w innym regionie o częstotliwości dopasowanej do potrzeb planu odzyskiwania po awarii. Istnieją różne metody kopiowania danych, w tym [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [pomocą distcp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)lub [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Zaleca się również wymuszenie zasad dostępu dla konta usługi Azure Storage, aby zapobiec przypadkowemu usunięciu.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Replikacja usługi Azure Storage](../../storage/common/storage-redundancy.md)
- [Wskazówki dotyczące awarii dla Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Dołącz dodatkowe konta usługi Azure Storage do klastra

Podczas procesu tworzenia usługi HDInsight jako domyślny system plików jest wybierane konto magazynu platformy Azure, Azure Data Lake Storage Gen1 lub Azure Data Lake Storage Gen2. Oprócz tego domyślnego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu tworzenia klastra lub po utworzeniu klastra.

Dodatkowe konto magazynu można dodać na jeden z następujących sposobów:
- Konfiguracja Ambari HDFS — zaawansowane niestandardowe rdzeń — lokacja Dodaj nazwę i klucz konta magazynu, aby ponownie uruchomić usługi
- Używanie [akcji skryptu](../hdinsight-hadoop-add-storage.md) przez przekazanie nazwy i klucza konta magazynu

> [!Note]
> W przypadku prawidłowych przypadków użycia limity w usłudze Azure Storage można zwiększyć za pośrednictwem żądania wysłanego do [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/).

Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następnym artykułem z tej serii: [najlepsze rozwiązania dotyczące migracji danych dla lokalnego programu do Azure HDInsight Hadoop migracji](apache-hadoop-on-premises-migration-best-practices-data-migration.md).