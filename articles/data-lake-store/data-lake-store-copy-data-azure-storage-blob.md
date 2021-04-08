---
title: Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1
description: Użyj narzędzia AdlCopy, aby skopiować dane z obiektów BLOB usługi Azure Storage do Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85980093"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów BLOB usługi Azure Storage do Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 udostępnia narzędzie wiersza polecenia [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), aby skopiować dane z następujących źródeł:

* Z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1. Nie można użyć AdlCopy do kopiowania danych z Data Lake Storage Gen1 do obiektów BLOB usługi Azure Storage.
* Między dwoma kontami Data Lake Storage Gen1.

Ponadto można użyć narzędzia AdlCopy w dwóch różnych trybach:

* **Autonomiczny**, gdzie narzędzie używa Data Lake Storage Gen1 zasobów do wykonania zadania.
* **Użycie konta Data Lake Analytics**, w którym jednostki przypisane do konta Data Lake Analytics są używane do wykonywania operacji kopiowania. Możesz chcieć użyć tej opcji, jeśli chcesz wykonać zadania kopiowania w przewidywalny sposób.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Kontener **obiektów BLOB usługi Azure Storage** z danymi.
* **Konto Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Konto Data Lake Analytics (opcjonalnie)** — zobacz [wprowadzenie do Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) , aby uzyskać instrukcje dotyczące sposobu tworzenia konta Data Lake Analytics.
* **Narzędzie AdlCopy**. Zainstaluj [Narzędzie AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Składnia narzędzia AdlCopy

Użyj następującej składni, aby współpracować z narzędziem AdlCopy

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

Parametry w składni opisano poniżej:

| Opcja | Opis |
| --- | --- |
| Element źródłowy |Określa lokalizację danych źródłowych w obiekcie blob usługi Azure Storage. Źródłem może być kontener obiektów blob, obiekt BLOB lub inne konto Data Lake Storage Gen1. |
| Dest |Określa miejsce docelowe Data Lake Storage Gen1, do którego ma zostać skopiowane. |
| SourceKey |Określa klucz dostępu do magazynu dla źródła obiektów BLOB usługi Azure Storage. Jest to wymagane tylko wtedy, gdy źródłem jest kontener obiektów blob lub obiekt BLOB. |
| Konto |**Opcjonalne**. Użyj tego, jeśli chcesz użyć konta Azure Data Lake Analytics do uruchomienia zadania kopiowania. Jeśli używasz opcji/Account w składni, ale nie określisz konta Data Lake Analytics, AdlCopy używa konta domyślnego do uruchomienia zadania. Ponadto w przypadku korzystania z tej opcji należy dodać źródło (Azure Storage Blob) i miejsce docelowe (Azure Data Lake Storage Gen1) jako źródła danych dla konta Data Lake Analytics. |
| Lekcji |Określa liczbę jednostek Data Lake Analytics, które będą używane dla zadania kopiowania. Ta opcja jest wymagana, jeśli używasz opcji **/Account** , aby określić konto Data Lake Analytics. |
| Wzorce |Określa wzorzec wyrażenia regularnego, który wskazuje, które obiekty blob lub pliki do skopiowania. AdlCopy używa dopasowania uwzględniającego wielkość liter. Domyślny wzorzec, gdy nie jest określony żaden wzorzec, to skopiowanie wszystkich elementów. Określanie wzorców wielu plików nie jest obsługiwane. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Używanie AdlCopy (jako autonomicznego) do kopiowania danych z obiektu BLOB usługi Azure Storage

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowano AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy` .
1. Uruchom następujące polecenie, aby skopiować określony obiekt BLOB z kontenera źródłowego do folderu Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Na przykład:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >Powyższa składnia określa plik, który ma zostać skopiowany do folderu na koncie Data Lake Storage Gen1. Narzędzie AdlCopy tworzy folder, jeśli określona nazwa folderu nie istnieje.

    Zostanie wyświetlony monit o wprowadzenie poświadczeń dla subskrypcji platformy Azure, w ramach której masz konto Data Lake Storage Gen1. Zostaną wyświetlone dane wyjściowe podobne do następujących:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. Możesz również skopiować wszystkie obiekty blob z jednego kontenera do konta Data Lake Storage Gen1 przy użyciu następującego polecenia:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Na przykład:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

W przypadku kopiowania z konta usługi Azure Blob Storage w ramach kopiowania po stronie magazynu obiektów BLOB mogą być ograniczone ograniczenia. Spowoduje to spadek wydajności zadania kopiowania. Aby dowiedzieć się więcej na temat limitów usługi Azure Blob Storage, zobacz limity usługi Azure Storage w obszarze [limity subskrypcji i usług platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Używanie AdlCopy (jako autonomicznego) do kopiowania danych z innego konta Data Lake Storage Gen1

Można również użyć AdlCopy do kopiowania danych między dwoma kontami Data Lake Storage Gen1.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowano AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy` .
1. Uruchom następujące polecenie, aby skopiować określony plik z jednego konta Data Lake Storage Gen1 do innego.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Na przykład:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > Powyższa składnia określa plik, który ma zostać skopiowany do folderu na docelowym koncie Data Lake Storage Gen1. Narzędzie AdlCopy tworzy folder, jeśli określona nazwa folderu nie istnieje.
   >
   >

    Zostanie wyświetlony monit o wprowadzenie poświadczeń dla subskrypcji platformy Azure, w ramach której masz konto Data Lake Storage Gen1. Zostaną wyświetlone dane wyjściowe podobne do następujących:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. Następujące polecenie kopiuje wszystkie pliki z określonego folderu na koncie źródłowego Data Lake Storage Gen1 do folderu na koncie Data Lake Storage Gen1 docelowy.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

W przypadku korzystania z AdlCopy jako autonomicznego narzędzia Kopia jest uruchamiana na udostępnionych zasobach zarządzanych przez platformę Azure. Wydajność, którą można uzyskać w tym środowisku, zależy od obciążenia systemu i dostępnych zasobów. Ten tryb najlepiej wykorzystuje się w przypadku małych transferów na zasadzie ad hoc. W przypadku korzystania z AdlCopy jako autonomicznego narzędzia nie trzeba dostrajać żadnych parametrów.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Użyj AdlCopy (z kontem Data Lake Analytics) do kopiowania danych

Możesz również użyć konta Data Lake Analytics do uruchomienia zadania AdlCopy, aby skopiować dane z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1. Tej opcji zazwyczaj należy używać, gdy dane, które mają być przenoszone, są z zakresu gigabajtów i terabajtów, a następnie potrzebna jest lepsza i przewidywalna przepływność wydajności.

Aby można było użyć konta Data Lake Analytics z AdlCopy do kopiowania z Azure Storage Blob, należy dodać źródło (Azure Storage Blob) jako źródło danych dla konta Data Lake Analytics. Aby uzyskać instrukcje dotyczące dodawania dodatkowych źródeł danych do konta Data Lake Analytics, zobacz [Zarządzanie źródłami danych konta usługi Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Jeśli kopiujesz z konta Azure Data Lake Storage Gen1 jako źródło przy użyciu konta Data Lake Analytics, nie musisz kojarzyć konta Data Lake Storage Gen1 z kontem Data Lake Analytics. Wymagane jest skojarzenie magazynu źródłowego z kontem Data Lake Analytics tylko wtedy, gdy źródło jest kontem usługi Azure Storage.
>
>

Uruchom następujące polecenie, aby skopiować z usługi Azure Storage BLOB do konta Data Lake Storage Gen1 przy użyciu konta Data Lake Analytics:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Na przykład:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

Analogicznie Uruchom następujące polecenie, aby skopiować wszystkie pliki z określonego folderu na koncie źródłowego Data Lake Storage Gen1 do folderu na koncie Data Lake Storage Gen1 docelowego przy użyciu konta Data Lake Analytics:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Podczas kopiowania danych z zakresu terabajtów użycie AdlCopy z własnym kontem Azure Data Lake Analytics zapewnia lepszą i bardziej przewidywalną wydajność. Parametr, który powinien być dostrojony, to liczba jednostek Azure Data Lake Analytics do użycia w ramach zadania kopiowania. Zwiększenie liczby jednostek spowoduje zwiększenie wydajności zadania kopiowania. Każdy plik do skopiowania może używać maksymalnie jednej jednostki. Określenie większej liczby jednostek niż liczba kopiowanych plików nie zwiększa wydajności.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Użyj AdlCopy do kopiowania danych przy użyciu dopasowania wzorca

W tej sekcji dowiesz się, jak używać AdlCopy do kopiowania danych ze źródła (w naszym przykładzie poniżej używamy Azure Storage Blob) do docelowego konta Data Lake Storage Gen1 przy użyciu dopasowania do wzorca. Na przykład możesz wykonać poniższe kroki, aby skopiować wszystkie pliki z rozszerzeniem CSV ze źródłowego obiektu BLOB do lokalizacji docelowej.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowano AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy` .
1. Uruchom następujące polecenie, aby skopiować wszystkie pliki z rozszerzeniem *. CSV z określonego obiektu BLOB z kontenera źródłowego do folderu Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Na przykład:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Rozliczenia

* Jeśli używasz narzędzia AdlCopy jako samodzielnego, naliczane są opłaty za wychodzące koszty związane z transferem danych, jeśli źródłowe konto usługi Azure Storage nie znajduje się w tym samym regionie co konto Data Lake Storage Gen1.
* Jeśli używasz narzędzia AdlCopy z kontem Data Lake Analytics, będą stosowane stawki za standardowe [Data Lake Analytics rozliczania](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Zagadnienia dotyczące używania AdlCopy

* AdlCopy (w przypadku wersji 1.0.5) obsługuje kopiowanie danych ze źródeł, które łącznie mają więcej niż tysiące plików i folderów. Jednak jeśli wystąpią problemy z kopiowaniem dużego zestawu danych, można dystrybuować pliki/foldery do różnych podfolderów i użyć ścieżki do tych podfolderów jako źródła.

## <a name="performance-considerations-for-using-adlcopy"></a>Zagadnienia dotyczące wydajności związane z korzystaniem z AdlCopy

AdlCopy obsługuje kopiowanie danych zawierających tysiące plików i folderów. Jeśli jednak wystąpią problemy z kopiowaniem dużego zestawu danych, można dystrybuować pliki/foldery do mniejszych podfolderów. AdlCopy została skompilowana dla kopii ad hoc. Jeśli próbujesz kopiować dane cyklicznie, należy rozważyć użycie [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) , które zapewnia pełne zarządzanie operacjami kopiowania.

## <a name="release-notes"></a>Informacje o wersji

* 1.0.13 — Jeśli kopiujesz dane do tego samego konta Azure Data Lake Storage Gen1 w wielu poleceniach adlcopy, nie musisz już ponownie wprowadzać poświadczeń dla każdego przebiegu. Adlcopy będzie teraz buforować te informacje w wielu przebiegach.

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
