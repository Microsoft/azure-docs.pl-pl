---
title: Tworzenie klastrów usługi Azure HDInsight przy użyciu portalu Data Lake Storage Gen1
description: Użyj Azure Portal, aby utworzyć klastry usługi HDInsight i korzystać z nich Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8b516180f09634dfa430275ef39370f0328a0927
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577904"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Tworzenie klastrów usługi HDInsight z Azure Data Lake Storage Gen1 przy użyciu Azure Portal

> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (dla magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj Menedżer zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak za pomocą Azure Portal utworzyć klaster usługi HDInsight przy użyciu konta Azure Data Lake Storage Gen1 jako magazynu domyślnego lub dodatkowego magazynu. Mimo że dodatkowy magazyn jest opcjonalny dla klastra usługi HDInsight, zaleca się przechowywanie danych firmowych na dodatkowych kontach magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

* **Subskrypcja platformy Azure**. Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami podanymi w tematach [wprowadzenie do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md). Należy również utworzyć folder główny na koncie.  W tym artykule jest używany folder główny o nazwie __/Clusters__ .
* Nazwa **główna usługi Azure Active Directory**. Ten przewodnik zawiera instrukcje dotyczące sposobu tworzenia nazwy głównej usługi w Azure Active Directory (Azure AD). Jednak aby utworzyć nazwę główną usługi, musisz być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć to wymaganie wstępne i kontynuować.

>[!NOTE]
>Nazwę główną usługi można utworzyć tylko wtedy, gdy jesteś administratorem usługi Azure AD. Aby można było utworzyć klaster usługi HDInsight z Data Lake Storage Gen1, administrator usługi Azure AD musi utworzyć nazwę główną. Ponadto należy utworzyć nazwę główną usługi przy użyciu certyfikatu, zgodnie z opisem w temacie [Tworzenie jednostki usługi z certyfikatem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Tworzenie klastra HDInsight

W tej sekcji utworzysz klaster usługi HDInsight z kontami Data Lake Storage Gen1 jako domyślny lub dodatkowy magazyn. Ten artykuł koncentruje się tylko na części konfigurowania kont Data Lake Storage Gen1. Ogólne informacje i procedury dotyczące tworzenia klastra znajdują się [w temacie Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Tworzenie klastra z Data Lake Storage Gen1 jako magazyn domyślny

Aby utworzyć klaster usługi HDInsight z kontem Data Lake Storage Gen1 jako domyślne konto magazynu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby uzyskać ogólne informacje na temat tworzenia klastrów usługi HDInsight, należy wykonać czynności opisane w tematach [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) .
3. W bloku **Magazyn** w obszarze **podstawowy typ magazynu**wybierz pozycję **Azure Data Lake Storage Gen1**, a następnie wprowadź następujące informacje:

    ![Zrzut ekranu przedstawiający blok magazyn z opcją podstawowy typ magazynu wybierz opcję konto Data Lake Store i opcję dostępu Data Lake Store o nazwie wychodzącą.](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Dodawanie nazwy głównej usługi do klastra HDInsight")

    * **Wybierz konto Data Lake Store**: Wybierz istniejące konto Data Lake Storage Gen1. Istniejące konto Data Lake Storage Gen1 jest wymagane.  Zobacz [Wymagania wstępne](#prerequisites).
    * **Ścieżka katalogu głównego**: wprowadź ścieżkę, w której mają być przechowywane pliki specyficzne dla klastra. Na zrzucie ekranu jest __/Clusters/myhdiadlcluster/__, w którym folder __/Clusters__ musi istnieć, a Portal tworzy folder *myhdicluster* .  *Myhdicluster* jest nazwą klastra.
    * **Data Lake Store dostęp**: Konfigurowanie dostępu między kontem Data Lake Storage Gen1 i klastrem usługi HDInsight. Aby uzyskać instrukcje, zobacz [Konfigurowanie dostępu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Dodatkowe konta magazynu**: Dodaj konta usługi Azure Storage jako dodatkowe konta magazynu dla klastra. Aby dodać kolejne konta Data Lake Storage Gen1, należy nawiązać uprawnienia klastra dotyczące danych w większej Data Lake Storage Gen1 kontach podczas konfigurowania konta Data Lake Storage Gen1 jako podstawowego typu magazynu. Zobacz [Konfigurowanie dostępu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. Na stronie **dostępu Data Lake Store**kliknij pozycję **Wybierz**, a następnie Kontynuuj tworzenie klastra zgodnie z opisem w temacie [Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra z Data Lake Storage Gen1 jako magazyn dodatkowy

Poniższe instrukcje tworzą klaster HDInsight z kontem usługi Azure Storage jako magazynem domyślnym, a konto Data Lake Storage Gen1 jako dodatkowy magazyn.

Aby utworzyć klaster usługi HDInsight z kontem Data Lake Storage Gen1 jako dodatkowe konto magazynu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby uzyskać ogólne informacje na temat tworzenia klastrów usługi HDInsight, należy wykonać czynności opisane w tematach [Tworzenie klastrów](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) .
3. W bloku **Magazyn** w obszarze **podstawowy typ magazynu**wybierz pozycję **Magazyn Azure**, a następnie wprowadź następujące informacje:

    ![Zrzut ekranu przedstawiający blok magazyn z opcją podstawowy typ magazynu i opcją dostępu Data Lake Store wywołana.](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Dodawanie nazwy głównej usługi do klastra HDInsight")

    * **Wybór metody** — aby określić konto magazynu, które jest częścią subskrypcji platformy Azure, wybierz pozycję **Moje subskrypcje**, a następnie wybierz konto magazynu. Aby określić konto magazynu znajdujące się poza subskrypcją platformy Azure, wybierz pozycję **klucz dostępu**, a następnie podaj informacje dotyczące konta magazynu zewnętrznego.

    * **Domyślny kontener** — Użyj wartości domyślnej lub określ własną nazwę.
    * **Dodatkowe konta magazynu** — Dodaj więcej kont usługi Azure Storage jako magazyn dodatkowy.
    * **Data Lake Store dostęp** — Konfigurowanie dostępu między kontem Data Lake Storage Gen1 i klastrem usługi HDInsight. Aby uzyskać instrukcje, zobacz [Konfigurowanie dostępu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurowanie dostępu Data Lake Storage Gen1

W tej sekcji skonfigurujesz Data Lake Storage Gen1 dostęp z klastrów HDInsight przy użyciu jednostki usługi Azure Active Directory.

### <a name="specify-a-service-principal"></a>Określanie nazwy głównej usługi

Z Azure Portal można użyć istniejącej nazwy głównej usługi lub utworzyć nową.

Aby utworzyć jednostkę usługi na podstawie Azure Portal:
1. Zobacz [Tworzenie nazwy głównej usługi i certyfikatów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) przy użyciu Azure Active Directory.

Aby użyć istniejącej jednostki usługi z Azure Portal:

1. Nazwa główna usługi powinna mieć uprawnienia właściciela na koncie magazynu. Zobacz [Konfigurowanie uprawnień dla jednostki usługi jako właściciel na koncie magazynu](#configure-serviceprincipal-permissions)
1. Wybierz pozycję **Data Lake Store dostęp**.
1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **Użyj istniejącej**.
1. Wybierz pozycję Nazwa **główna usługi**, a następnie wybierz nazwę główną usługi.
1. Przekaż certyfikat (plik PFX) skojarzony z wybraną jednostką usługi, a następnie wprowadź hasło certyfikatu.

    ![Zrzut ekranu przedstawiający blok Data Lake Storage Gen1 dostęp z użyciem istniejącej opcji o nazwie wychodzącej i bloku wybierz nazwę główną usługi z opcją h p Insight s i wybierz opcję wywołana.](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Dodawanie nazwy głównej usługi do klastra HDInsight")

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

Aby przypisać uprawnienia na poziomie głównym konta Data Lake Storage Gen1:

1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **dostęp**. Zostanie otwarty blok **Wybierz uprawnienia do pliku** . Zawiera listę wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji.
1. Umieść kursor (nie klikaj) myszą nad nazwą konta Data Lake Storage Gen1, aby pole wyboru było widoczne, a następnie zaznacz pole wyboru.

    ![Zrzut ekranu przedstawiający blok uprawnień do samego pliku z wyróżnionym i wybranym kontem Data Lake Storage generacji 1.](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Dodawanie nazwy głównej usługi do klastra HDInsight")

   Domyślnie, __Odczyt__, __zapis__i __wykonywanie__ są zaznaczone.

1. Kliknij przycisk **Wybierz** w dolnej części strony.
1. Wybierz pozycję **Uruchom** , aby przypisać uprawnienia.
1. Wybierz pozycję **Gotowe**.

Aby przypisać uprawnienia na poziomie głównym klastra usługi HDInsight:

1. W bloku **dostępu Data Lake Storage Gen1** wybierz pozycję **dostęp**. Zostanie otwarty blok **Wybierz uprawnienia do pliku** . Zawiera listę wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji.
1. W bloku **Wybierz uprawnienia do pliku** wybierz nazwę konta Data Lake Storage Gen1, aby wyświetlić jego zawartość.
1. Wybierz element główny magazynu klastra usługi HDInsight, zaznaczając pole wyboru po lewej stronie folderu. Zgodnie z wcześniejszym zrzutem ekranu główny magazyn klastra jest folderem __/Clusters__ określonym podczas wybierania Data Lake Storage Gen1 jako magazynu domyślnego.
1. Ustaw uprawnienia do folderu.  Domyślnie, Odczyt, zapis i wykonywanie są zaznaczone.
1. Kliknij przycisk **Wybierz** w dolnej części strony.
1. Wybierz pozycję **Uruchom**.
1. Wybierz pozycję **Gotowe**.

Jeśli używasz Data Lake Storage Gen1 jako dodatkowego magazynu, musisz przypisać uprawnienia tylko do folderów, do których chcesz uzyskać dostęp z klastra usługi HDInsight. Na przykład na poniższym zrzucie ekranu można uzyskać dostęp tylko do folderu **mojnowyfolder** na koncie Data Lake Storage Gen1.

![Przypisywanie uprawnień nazw głównych do klastra usługi HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Przypisywanie uprawnień nazw głównych do klastra usługi HDInsight")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Weryfikowanie konfiguracji klastra

Po zakończeniu instalacji klastra w bloku klastra sprawdź wyniki, wykonując jedną lub obie następujące czynności:

* Aby sprawdzić, czy skojarzony magazyn klastra jest określonym kontem Data Lake Storage Gen1, wybierz pozycję **konta magazynu** w okienku po lewej stronie.

    ![Zrzut ekranu przedstawiający konto Data Lake Storage Gen 1 z wyróżnioną opcją konta magazynu.](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Dodawanie nazwy głównej usługi do klastra HDInsight")

* Aby sprawdzić, czy jednostka usługi jest prawidłowo skojarzona z klastrem HDInsight, wybierz pozycję **Data Lake Storage Gen1 dostęp** w okienku po lewej stronie.

    ![Zrzut ekranu przedstawiający konto Data Lake Storage Gen 1 z wyróżnioną opcją dostępu Data Lake Storage Gen 1.](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Dodawanie nazwy głównej usługi do klastra HDInsight")

## <a name="examples"></a>Przykłady

Po skonfigurowaniu klastra przy użyciu Data Lake Storage Gen1 jako magazynu należy zapoznać się z przykładami użycia klastra usługi HDInsight w celu przeanalizowania danych przechowywanych w programie Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Uruchamianie zapytania programu Hive względem danych w ramach konta Data Lake Storage Gen1 (jako magazynu podstawowego)

Aby uruchomić zapytanie programu Hive, użyj interfejsu widoki programu Hive w portalu Ambari. Aby uzyskać instrukcje dotyczące korzystania z widoków Hive programu Ambari, zobacz [Korzystanie z widoku Hive z usługą Hadoop w usłudze HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Podczas pracy z danymi w ramach konta Data Lake Storage Gen1 istnieje kilka ciągów do zmiany.

W przypadku użycia, na przykład klastra utworzonego przy użyciu Data Lake Storage Gen1 jako magazynu podstawowego, ścieżką do danych jest: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.NET/Path/to/File*. Zapytanie programu Hive w celu utworzenia tabeli na podstawie przykładowych danych przechowywanych na koncie Data Lake Storage Gen1 wygląda następująco:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Znajduje

* `adl://hdiadlsg1storage.azuredatalakestore.net/` jest katalogiem głównym konta Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` jest katalogiem głównym danych klastra, które zostały określone podczas tworzenia klastra.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` jest lokalizacją pliku przykładowego użytego w zapytaniu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Uruchamianie zapytania programu Hive względem danych w ramach konta Data Lake Storage Gen1 (jako dodatkowego magazynu)

Jeśli utworzony klaster używa magazynu obiektów BLOB jako magazynu domyślnego, dane przykładowe nie są zawarte na koncie Data Lake Storage Gen1 używanym jako dodatkowy magazyn. W takim przypadku należy najpierw przenieść dane z magazynu obiektów BLOB do konta Data Lake Storage Gen1, a następnie uruchomić zapytania, jak pokazano w powyższym przykładzie.

Aby uzyskać informacje na temat sposobu kopiowania danych z usługi BLOB Storage do konta Data Lake Storage Gen1, zobacz następujące artykuły:

* [Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Użyj AdlCopy, aby skopiować dane z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Używanie Data Lake Storage Gen1 z klastrem Spark

Klastra Spark można używać do uruchamiania zadań platformy Spark na danych przechowywanych w ramach konta Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight Spark do analizowania danych w Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Używanie Data Lake Storage Gen1 w topologii burzy

Przy użyciu konta Data Lake Storage Gen1 można pisać dane z topologii burzowej. Aby uzyskać instrukcje dotyczące sposobu osiągnięcia tego scenariusza, zobacz [Korzystanie z Azure Data Lake Storage Gen1 z Apache Storm z usługą HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Zobacz też

* [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Tworzenie klastra usługi HDInsight do użycia Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
