---
title: Tworzenie klastrów usługi Azure HDInsight przy użyciu portalu Data Lake Storage Gen1
description: Użyj Azure Portal, aby utworzyć klastry usługi HDInsight i korzystać z nich Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 666b39e2a600fe6ca004798ed4f8371cdd1dfe5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96340258"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Tworzenie klastrów usługi HDInsight z Azure Data Lake Storage Gen1 przy użyciu Azure Portal

> [!div class="op_single_selector"]
> * [Użyj Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (dla magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj Menedżer zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak za pomocą Azure Portal utworzyć klaster usługi HDInsight z Azure Data Lake Storage Gen1 jako magazyn domyślny lub dodatkowy magazyn. Mimo że dodatkowy magazyn jest opcjonalny dla klastra usługi HDInsight, zaleca się przechowywanie danych firmowych na dodatkowych kontach magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

* **Subskrypcja platformy Azure**. Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami podanymi w tematach [wprowadzenie do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md). Należy również utworzyć folder główny na koncie.  W tym artykule jest używany folder główny o nazwie __/Clusters__ .
* Nazwa **główna usługi Azure Active Directory**. Ten przewodnik zawiera instrukcje dotyczące sposobu tworzenia nazwy głównej usługi w Azure Active Directory (Azure AD). Jednak aby utworzyć nazwę główną usługi, musisz być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć to wymaganie wstępne i kontynuować.

>[!NOTE]
>Nazwę główną usługi można utworzyć tylko wtedy, gdy jesteś administratorem usługi Azure AD. Aby można było utworzyć klaster usługi HDInsight z Data Lake Storage Gen1, administrator usługi Azure AD musi utworzyć nazwę główną. Ponadto należy utworzyć nazwę główną usługi przy użyciu certyfikatu, zgodnie z opisem w temacie [Tworzenie jednostki usługi z certyfikatem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Tworzenie klastra HDInsight

W tej sekcji utworzysz klaster usługi HDInsight z Data Lake Storage Gen1m domyślnym lub dodatkowym magazynem. Ten artykuł koncentruje się tylko na części konfigurowania Data Lake Storage Gen1. Ogólne informacje i procedury dotyczące tworzenia klastra znajdują się [w temacie Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Tworzenie klastra z Data Lake Storage Gen1 jako magazyn domyślny

Aby utworzyć klaster usługi HDInsight z Data Lake Storage Gen1 jako domyślne konto magazynu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby uzyskać ogólne informacje na temat tworzenia klastrów usługi HDInsight, należy wykonać czynności opisane w tematach [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) .
3. W bloku **Magazyn** w obszarze **podstawowy typ magazynu** wybierz pozycję **Azure Data Lake Storage Gen1**, a następnie wprowadź następujące informacje:

    ![Ustawienia konta magazynu usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Wybierz konto Data Lake Store**: Wybierz istniejące konto Data Lake Storage Gen1. Istniejące konto Data Lake Storage Gen1 jest wymagane.  Zobacz [Wymagania wstępne](#prerequisites).
    * **Ścieżka katalogu głównego**: wprowadź ścieżkę, w której mają być przechowywane pliki specyficzne dla klastra. Na zrzucie ekranu jest __/Clusters/myhdiadlcluster/__, w którym folder __/Clusters__ musi istnieć, a Portal tworzy folder *myhdicluster* .  *Myhdicluster* jest nazwą klastra.
    * **Data Lake Store dostęp**: Konfigurowanie dostępu między kontem Data Lake Storage Gen1 i klastrem usługi HDInsight. Aby uzyskać instrukcje, zobacz [Konfigurowanie dostępu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Dodatkowe konta magazynu**: Dodaj konta usługi Azure Storage jako dodatkowe konta magazynu dla klastra. Aby dodać kolejne konta Data Lake Storage Gen1, należy nawiązać uprawnienia klastra dotyczące danych w większej Data Lake Storage Gen1 kontach podczas konfigurowania konta Data Lake Storage Gen1 jako podstawowego typu magazynu. Zobacz [Konfigurowanie dostępu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. Na stronie **dostępu Data Lake Store** kliknij pozycję **Wybierz**, a następnie Kontynuuj tworzenie klastra zgodnie z opisem w temacie [Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra z Data Lake Storage Gen1 jako magazyn dodatkowy

Poniższe instrukcje tworzą klaster HDInsight z kontem usługi Azure Blob Storage jako magazynem domyślnym, a konto magazynu z Data Lake Storage Gen1 jako dodatkowy magazyn.

Aby utworzyć klaster usługi HDInsight z Data Lake Storage Gen1 jako dodatkowe konto magazynu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby uzyskać ogólne informacje na temat tworzenia klastrów usługi HDInsight, należy wykonać czynności opisane w tematach [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) .
3. W bloku **Magazyn** w obszarze **podstawowy typ magazynu** wybierz pozycję **Magazyn Azure**, a następnie wprowadź następujące informacje:

    ![Magazyn dodatkowych ustawień konta magazynu usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Wybór metody** — aby określić konto magazynu, które jest częścią subskrypcji platformy Azure, wybierz pozycję **Moje subskrypcje**, a następnie wybierz konto magazynu. Aby określić konto magazynu znajdujące się poza subskrypcją platformy Azure, wybierz pozycję **klucz dostępu**, a następnie podaj informacje dotyczące konta magazynu zewnętrznego.

    * **Domyślny kontener** — Użyj wartości domyślnej lub określ własną nazwę.
    * **Dodatkowe konta magazynu** — Dodaj więcej kont usługi Azure Storage jako magazyn dodatkowy.
    * **Data Lake Store dostęp** — Konfigurowanie dostępu między kontem Data Lake Storage Gen1 i klastrem usługi HDInsight. Aby uzyskać instrukcje, zobacz [Konfigurowanie dostępu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurowanie dostępu Data Lake Storage Gen1

W tej sekcji skonfigurujesz Data Lake Storage Gen1 dostęp z klastrów HDInsight przy użyciu jednostki usługi Azure Active Directory.

### <a name="specify-a-service-principal"></a>Określanie nazwy głównej usługi

Z Azure Portal można użyć istniejącej nazwy głównej usługi lub utworzyć nową.

Aby utworzyć jednostkę usługi na podstawie Azure Portal:
1. Zobacz [Tworzenie nazwy głównej usługi i certyfikatów](../active-directory/develop/howto-create-service-principal-portal.md) przy użyciu Azure Active Directory.

Aby użyć istniejącej jednostki usługi z Azure Portal:

1. Nazwa główna usługi powinna mieć uprawnienia właściciela na koncie magazynu. Zobacz [Konfigurowanie uprawnień dla jednostki usługi jako właściciel na koncie magazynu](#configure-serviceprincipal-permissions).
1. Wybierz pozycję **Data Lake Store dostęp**.
1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **Użyj istniejącej**.
1. Wybierz pozycję Nazwa **główna usługi**, a następnie wybierz nazwę główną usługi.
1. Przekaż certyfikat (plik PFX) skojarzony z wybraną jednostką usługi, a następnie wprowadź hasło certyfikatu.

    ![Dodawanie nazwy głównej usługi do klastra HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Wybierz pozycję **dostęp** , aby skonfigurować dostęp do folderu.  Zobacz [Konfigurowanie uprawnień do plików](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Skonfiguruj uprawnienia dla jednostki usługi jako właściciel na koncie magazynu
1. W bloku Access Control (IAM) konta magazynu kliknij pozycję Dodaj przypisanie roli. 
2. W bloku Dodaj przypisanie roli wybierz rolę jako właściciela, a następnie wybierz nazwę SPN i kliknij przycisk Zapisz.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurowanie uprawnień do plików

Konfiguracja różni się w zależności od tego, czy konto jest używane jako magazyn domyślny, czy też do dodatkowego konta magazynu:

* Używany jako magazyn domyślny

  * uprawnienie na poziomie głównym konta Data Lake Storage Gen1
  * uprawnienia na poziomie głównym magazynu klastra usługi HDInsight. Na przykład folder __/Clusters__ używany wcześniej w samouczku.

* Używanie jako dodatkowego magazynu

  * Uprawnienie do folderów, do których potrzebny jest dostęp do plików.

Aby przypisać uprawnienia na koncie magazynu przy użyciu Data Lake Storage Gen1 na poziomie głównym:

1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **dostęp**. Zostanie otwarty blok **Wybierz uprawnienia do pliku** . Wyświetla listę wszystkich kont magazynu w ramach subskrypcji.
1. Umieść kursor (nie klikaj) myszą nad nazwą konta z Data Lake Storage Gen1, aby pole wyboru było widoczne, a następnie zaznacz pole wyboru.

    ![Wybieranie uprawnień do pliku](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   Domyślnie, __Odczyt__, __zapis__ i __wykonywanie__ są zaznaczone.

1. Kliknij przycisk **Wybierz** w dolnej części strony.
1. Wybierz pozycję **Uruchom** , aby przypisać uprawnienia.
1. Kliknij **Gotowe**.

Aby przypisać uprawnienia na poziomie głównym klastra usługi HDInsight:

1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **dostęp**. Zostanie otwarty blok **Wybierz uprawnienia do pliku** . Zawiera listę wszystkich kont magazynu z Data Lake Storage Gen1 w ramach subskrypcji.
1. W bloku **Wybierz uprawnienia do pliku** wybierz konto magazynu o nazwie Data Lake Storage Gen1, aby wyświetlić jego zawartość.
1. Wybierz element główny magazynu klastra usługi HDInsight, zaznaczając pole wyboru po lewej stronie folderu. Zgodnie z wcześniejszym zrzutem ekranu główny magazyn klastra jest folderem __/Clusters__ określonym podczas wybierania Data Lake Storage Gen1 jako magazynu domyślnego.
1. Ustaw uprawnienia do folderu.  Domyślnie, Odczyt, zapis i wykonywanie są zaznaczone.
1. Kliknij przycisk **Wybierz** w dolnej części strony.
1. Wybierz pozycję **Uruchom**.
1. Kliknij **Gotowe**.

Jeśli używasz Data Lake Storage Gen1 jako dodatkowego magazynu, musisz przypisać uprawnienia tylko do folderów, do których chcesz uzyskać dostęp z klastra usługi HDInsight. Na przykład na poniższym zrzucie ekranu można uzyskać dostęp tylko do folderu **mojnowyfolder** na koncie magazynu z Data Lake Storage Gen1.

![Przypisywanie uprawnień nazw głównych do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Weryfikowanie konfiguracji klastra

Po zakończeniu instalacji klastra w bloku klastra sprawdź wyniki, wykonując jedną lub obie następujące czynności:

* Aby sprawdzić, czy skojarzony magazyn klastra jest kontem o Data Lake Storage Gen1 określonym przez użytkownika, wybierz pozycję **konta magazynu** w okienku po lewej stronie.

    ![Weryfikowanie skojarzonego magazynu](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Aby sprawdzić, czy jednostka usługi jest prawidłowo skojarzona z klastrem HDInsight, wybierz pozycję **Data Lake Storage Gen1 dostęp** w okienku po lewej stronie.

    ![Weryfikowanie nazwy głównej usługi](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Przykłady

Po skonfigurowaniu klastra przy użyciu Data Lake Storage Gen1 jako magazynu należy zapoznać się z przykładami użycia klastra usługi HDInsight w celu przeanalizowania danych przechowywanych w programie Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Uruchamianie zapytania programu Hive względem danych w Data Lake Storage Gen1 (jako magazyn podstawowy)

Aby uruchomić zapytanie programu Hive, użyj interfejsu widoki programu Hive w portalu Ambari. Aby uzyskać instrukcje dotyczące korzystania z widoków Hive programu Ambari, zobacz [Korzystanie z widoku Hive z usługą Hadoop w usłudze HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Podczas pracy z danymi w Data Lake Storage Gen1 istnieje kilka ciągów do zmiany.

W przypadku użycia, na przykład klastra utworzonego przy użyciu Data Lake Storage Gen1 jako magazynu podstawowego, ścieżką do danych jest: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.NET/Path/to/File*. Zapytanie programu Hive w celu utworzenia tabeli na podstawie przykładowych danych, które są przechowywane w Data Lake Storage Gen1 wygląda następująco:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Znajduje

* `adl://hdiadlsg1storage.azuredatalakestore.net/` jest katalogiem głównym konta z Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` jest katalogiem głównym danych klastra, które zostały określone podczas tworzenia klastra.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` jest lokalizacją pliku przykładowego użytego w zapytaniu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Uruchamianie zapytania programu Hive względem danych w Data Lake Storage Gen1 (jako magazyn dodatkowy)

Jeśli klaster, który został utworzony, używa magazynu obiektów BLOB jako magazynu domyślnego, przykładowe dane nie znajdują się na koncie magazynu Data Lake Storage Gen1 używanym jako dodatkowy magazyn. W takim przypadku należy najpierw przenieść dane z magazynu obiektów BLOB do konta magazynu za pomocą Data Lake Storage Gen1, a następnie uruchomić zapytania, jak pokazano w powyższym przykładzie.

Aby uzyskać informacje na temat sposobu kopiowania danych z usługi BLOB Storage do konta magazynu z Data Lake Storage Gen1, zobacz następujące artykuły:

* [Używanie pomocą distcp do kopiowania danych między usługą Azure Blob Storage a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Użyj AdlCopy do kopiowania danych z usługi Azure Blob Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Używanie Data Lake Storage Gen1 z klastrem Spark

Klastra Spark można używać do uruchamiania zadań platformy Spark na danych przechowywanych w Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight Spark do analizowania danych w Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Używanie Data Lake Storage Gen1 w topologii burzy

Możesz użyć konta magazynu z Data Lake Storage Gen1, aby zapisać dane z topologii burzy. Aby uzyskać instrukcje dotyczące sposobu osiągnięcia tego scenariusza, zobacz [Korzystanie z Azure Data Lake Storage Gen1 z Apache Storm z usługą HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Zobacz też

* [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: Tworzenie klastra usługi HDInsight do użycia Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx