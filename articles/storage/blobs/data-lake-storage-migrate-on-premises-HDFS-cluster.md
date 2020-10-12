---
title: Migrowanie ze sklepu premium HDFS do usługi Azure Storage za pomocą Azure Data Box
description: Migruj dane z lokalnego magazynu systemu plików HDFS do usługi Azure Storage (BLOB Storage lub Data Lake Storage Gen2) za pomocą urządzenia urządzenie Data Box.
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: a50f85e76f16f1e5ba8823adb1ea1aa02157fcee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88032564"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrowanie ze sklepu premium HDFS do usługi Azure Storage za pomocą Azure Data Box

Dane można migrować z lokalnego magazynu systemu plików HDFS klastra Hadoop do usługi Azure Storage (BLOB Storage lub Data Lake Storage Gen2) za pomocą urządzenia urządzenie Data Box. Można wybrać jedną z Data Box Disk, urządzenie Data Box 80-TB lub 770-TB Data Box Heavy.

Ten artykuł ułatwia wykonywanie następujących zadań:

> [!div class="checklist"]
> * Przygotuj się do migracji danych.
> * Skopiuj dane do Data Box Disk, urządzenie Data Box lub urządzenia Data Box Heavy.
> * Wyślij urządzenie z powrotem do firmy Microsoft.
> * Zastosuj uprawnienia dostępu do plików i katalogów (tylko Data Lake Storage Gen2)

## <a name="prerequisites"></a>Wymagania wstępne

Te elementy są niezbędne do ukończenia migracji.

* Konto usługi Azure Storage.

* Lokalny klaster Hadoop, który zawiera dane źródłowe.

* [Urządzenie Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Zamów urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) lub [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Podłącz [urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) lub [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) do sieci lokalnej przy użyciu kabla.

Jeśli wszystko jest gotowe, zacznijmy.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopiowanie danych na urządzenie urządzenie Data Box

Jeśli dane pasują do jednego urządzenie Data Boxego urządzenia, skopiujesz dane na urządzenie urządzenie Data Box. 

Jeśli rozmiar danych przekracza pojemność urządzenia urządzenie Data Box, należy użyć [opcjonalnej procedury w celu podzielenia danych na wiele urządzenie Data Box urządzeń](#appendix-split-data-across-multiple-data-box-devices) , a następnie wykonać ten krok. 

Aby skopiować dane z lokalnego magazynu systemu plików HDFS na urządzenie urządzenie Data Box, należy ustawić kilka rzeczy, a następnie użyć narzędzia [pomocą distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

Wykonaj następujące kroki, aby skopiować dane za pośrednictwem interfejsów API REST magazynu obiektów BLOB/obiektów do urządzenia urządzenie Data Box. Interfejs API REST sprawia, że urządzenie będzie widoczne jako magazyn systemu plików HDFS w klastrze.

1. Przed skopiowaniem danych za pomocą usługi REST Zidentyfikuj elementy podstawowe zabezpieczeń i połączenia w celu nawiązania połączenia z interfejsem REST na urządzenie Data Box lub Data Box Heavy. Zaloguj się do lokalnego interfejsu użytkownika sieci Web urządzenie Data Box i przejdź do strony **łączenie i kopiowanie** . Na koncie usługi Azure Storage dla urządzenia w obszarze **Ustawienia dostępu**Znajdź i wybierz pozycję **rest**.

    ![Strona "łączenie i kopiowanie"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. W oknie dialogowym dostęp do konta magazynu i przekazywania danych Skopiuj **BLOB Service punkt końcowy** i **klucz konta magazynu**. W punkcie końcowym usługi BLOB należy pominąć `https://` i końcowy ukośnik.

    W tym przypadku punkt końcowy to: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` . Część hosta identyfikatora URI, który będzie używany, to: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` . Aby zapoznać się z przykładem, zobacz jak [nawiązać połączenie z usługą REST za pośrednictwem protokołu HTTP](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Okno dialogowe uzyskiwanie dostępu do konta magazynu i przekazywanie danych](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Dodaj punkt końcowy oraz adres IP urządzenie Data Box lub Data Box Heavy węzła do `/etc/hosts` każdego węzła.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Jeśli używasz innego mechanizmu dla systemu DNS, upewnij się, że można rozpoznać urządzenie Data Box punkt końcowy.

4. Ustaw zmienną Shell `azjars` na lokalizację `hadoop-azure` `azure-storage` plików jar i. Te pliki można znaleźć w katalogu instalacji usługi Hadoop.

    Aby określić, czy te pliki istnieją, użyj następującego polecenia: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` . Zastąp `<hadoop_install_dir>` symbol zastępczy ścieżką do katalogu, w którym zainstalowano Hadoop. Upewnij się, że używasz w pełni kwalifikowanych ścieżek.

    Przykłady:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Utwórz kontener magazynu, który ma być używany do kopiowania danych. Należy również określić katalog docelowy jako część tego polecenia. Może to być w tym momencie fikcyjny katalog docelowy.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Zastąp `<blob_service_endpoint>` symbol zastępczy nazwą punktu końcowego usługi BLOB Service.

    * Zastąp `<account_key>` symbol zastępczy kluczem dostępu do konta.

    * Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

    * Zastąp `<destination_directory>` symbol zastępczy nazwą katalogu, do którego chcesz skopiować dane.

6. Uruchom polecenie list, aby upewnić się, że kontener i katalog zostały utworzone.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Zastąp `<blob_service_endpoint>` symbol zastępczy nazwą punktu końcowego usługi BLOB Service.

   * Zastąp `<account_key>` symbol zastępczy kluczem dostępu do konta.

   * Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

7. Skopiuj dane z usługi Hadoop HDFS do urządzenie Data Box Storage BLOB do utworzonego wcześniej kontenera. Jeśli nie można odnaleźć katalogu, do którego ma zostać skopiowane, polecenie zostanie automatycznie utworzone.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Zastąp `<blob_service_endpoint>` symbol zastępczy nazwą punktu końcowego usługi BLOB Service.

    * Zastąp `<account_key>` symbol zastępczy kluczem dostępu do konta.

    * Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

    * Zastąp `<exlusion_filelist_file>` symbol zastępczy nazwą pliku, który zawiera listę wykluczeń plików.

    * Zastąp `<source_directory>` symbol zastępczy nazwą katalogu zawierającego dane, które chcesz skopiować.

    * Zastąp `<destination_directory>` symbol zastępczy nazwą katalogu, do którego chcesz skopiować dane.

    `-libjars`Opcja służy do udostępniania `hadoop-azure*.jar` plików zależnych i `azure-storage*.jar` `distcp` . Może to być spowodowane niektórymi klastrami.

    Poniższy przykład pokazuje, jak `distcp` polecenie jest używane do kopiowania danych.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Aby zwiększyć szybkość kopiowania:

    * Spróbuj zmienić liczbę odwzorów. (W powyższym przykładzie zastosowano funkcję `m` = 4 mapowania).

    * Spróbuj uruchomić wiele `distcp` równolegle.

    * Należy pamiętać, że duże pliki działają lepiej niż małe pliki.

## <a name="ship-the-data-box-to-microsoft"></a>Wyślij urządzenie Data Box do firmy Microsoft

Wykonaj następujące kroki, aby przygotować i dostarczyć urządzenie Data Box urządzenie do firmy Microsoft.

1. Najpierw  [przygotowanie do wysłania na urządzenie Data Box lub Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Po zakończeniu przygotowywania urządzenia Pobierz pliki BOM. Te pliki BOM lub manifestów zostaną użyte później do zweryfikowania danych przekazywanych do platformy Azure.

3. Zamknij urządzenie i Usuń kable.

4. Zaplanuj odbiór przez firmę UPS.

    * Aby uzyskać urządzenie Data Box urządzeń, zobacz artykuł [dostarczanie urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Aby uzyskać Data Box Heavy urządzeń, zobacz artykuł [dostarczanie Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Gdy firma Microsoft odbierze urządzenie, jest ono połączone z siecią centrum danych i dane zostaną przekazane do konta magazynu określonego podczas umieszczania zamówienia urządzenia. Sprawdź pliki BOM, które wszystkie dane są przekazywane do platformy Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Zastosuj uprawnienia dostępu do plików i katalogów (tylko Data Lake Storage Gen2)

Masz już dane do konta usługi Azure Storage. Teraz zostaną zastosowane uprawnienia dostępu do plików i katalogów.

> [!NOTE]
> Ten krok jest niezbędny tylko w przypadku używania Azure Data Lake Storage Gen2 jako magazynu danych. Jeśli używasz tylko konta usługi BLOB Storage bez hierarchicznej przestrzeni nazw jako magazynu danych, możesz pominąć tę sekcję.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Tworzenie nazwy głównej usługi dla konta Azure Data Lake Storage Gen2

Aby utworzyć jednostkę usługi, zobacz [How to: Use the Portal, aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) tego artykułu, upewnij się, że przypisano rolę **Współautor danych obiektu blob magazynu** do jednostki usługi.

* Podczas wykonywania kroków opisanych w sekcji [pobieranie wartości dla logowania w](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artykule Zapisz identyfikator aplikacji i klucz tajny klienta w pliku tekstowym. Wkrótce będą potrzebne.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generuj listę skopiowanych plików z ich uprawnieniami

W lokalnym klastrze usługi Hadoop Uruchom następujące polecenie:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

To polecenie generuje listę skopiowanych plików z ich uprawnieniami.

> [!NOTE]
> W zależności od liczby plików w systemie HDFS uruchomienie tego polecenia może zająć dużo czasu.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Wygeneruj listę tożsamości i zamapuj je na Azure Active Directory (Dodaj) tożsamości

1. Pobierz `copy-acls.py` skrypt. Zobacz sekcję [pobieranie skryptów pomocnika i skonfiguruj węzeł brzegowy, aby uruchomić je w](#download-helper-scripts) tym artykule.

2. Uruchom to polecenie, aby wygenerować listę unikatowych tożsamości.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Ten skrypt generuje plik o nazwie `id_map.json` , który zawiera tożsamości, które są potrzebne do mapowania na tożsamości oparte na dodawaniu.

3. Otwórz `id_map.json` plik w edytorze tekstu.

4. Dla każdego obiektu JSON, który pojawia się w pliku, zaktualizuj `target` atrybut nazwy głównej użytkownika usługi AAD (UPN) lub objectid (OID) przy użyciu odpowiedniej zamapowanej tożsamości. Po zakończeniu Zapisz plik. Ten plik będzie potrzebny w następnym kroku.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Zastosuj uprawnienia do skopiowanych plików i Zastosuj mapowania tożsamości

Uruchom to polecenie, aby zastosować uprawnienia do danych skopiowanych na konto Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

* Zastąp `<application-id>` `<client-secret>` symbole zastępcze i identyfikator aplikacji oraz klucz tajny klienta zebrane podczas tworzenia jednostki usługi.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Dodatek: dzielenie danych między wieloma urządzenie Data Box urządzeniami

Przed przeniesieniem danych na urządzenie urządzenie Data Box należy pobrać niektóre skrypty pomocnika, upewnić się, że dane są zorganizowane tak, aby mieściły się na urządzeniu urządzenie Data Box i wykluczyć zbędne pliki.

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Pobierz skrypty pomocnika i skonfiguruj węzeł brzegowy, aby uruchomić je

1. Z poziomu brzegu lub węzła głównego lokalnego klastra Hadoop Uruchom następujące polecenie:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   To polecenie klonuje repozytorium GitHub zawierające skrypty pomocnika.

2. Upewnij się, że na komputerze lokalnym jest zainstalowany pakiet [JQ](https://stedolan.github.io/jq/) .

   ```bash
   
   sudo apt-get install jq
   ```

3. Zainstaluj pakiet [żądań](https://2.python-requests.org/en/master/) języka Python.

   ```bash
   
   pip install requests
   ```

4. Ustaw uprawnienia EXECUTE dla wymaganych skryptów.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Upewnij się, że dane są zorganizowane tak, aby zmieściły się na urządzenie Data Box urządzeniu

Jeśli rozmiar danych przekracza rozmiar pojedynczego urządzenia urządzenie Data Box, można podzielić pliki do grup, które można przechowywać na wielu urządzenie Data Box urządzeniach.

Jeśli dane nie przekroczą rozmiaru pojedynczego urządzenia urządzenie Data Box, można przejdź do następnej sekcji.

1. Mając podwyższony poziom uprawnień, uruchom `generate-file-list` pobrany skrypt, postępując zgodnie ze wskazówkami w poprzedniej sekcji.

   Oto opis parametrów poleceń:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Skopiuj wygenerowane listy plików do systemu plików HDFS, aby były dostępne dla zadania [pomocą distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Wyklucz niepotrzebne pliki

Konieczne będzie wykluczenie niektórych katalogów z zadania DisCp. Na przykład Wyklucz katalogi zawierające informacje o stanie, które przechowują klaster.

W lokalnym klastrze usługi Hadoop, w którym planujesz zainicjować zadanie pomocą distcp, Utwórz plik, który określa listę katalogów, które chcesz wykluczyć.

Oto przykład:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Data Lake Storage Gen2 współpracuje z klastrami usługi HDInsight. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
