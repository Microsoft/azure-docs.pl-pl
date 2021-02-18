---
title: Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal
description: Dowiedz się, jak tworzyć klastry usługi Azure HDInsight i zarządzać nimi przy użyciu Azure Portal.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: e21361b6d491f53f41754831d2bdf896ef2719db
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091437"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Za pomocą [Azure Portal](https://portal.azure.com)można zarządzać klastrami [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight. Aby uzyskać informacje na temat zarządzania klastrami Hadoop w usłudze HDInsight przy użyciu innych narzędzi, Skorzystaj z powyższej zakładki.

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący klaster Apache Hadoop w usłudze HDInsight.  Zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Wprowadzenie

Zaloguj się do witryny [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Wyświetlanie listy i wyświetlanie klastrów

Na stronie **Klastry usługi HDInsight** zostaną wystawione istniejące klastry.  Z poziomu portalu:
1. W menu po lewej stronie wybierz pozycję **wszystkie usługi** .
2. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Analiza**.

## <a name="cluster-home-page"></a><a name="homePage"></a> Strona główna klastra

Wybierz nazwę klastra na stronie [**Klastry usługi HDInsight**](#showClusters) .  Spowoduje to otwarcie widoku **przeglądu** , który wygląda podobnie do poniższego obrazu:

![Azure Portal podstawowe informacje o klastrze usługi HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu górne:**  

| Element| Opis |
|---|---|
|Move|Przenosi klaster do innej grupy zasobów lub innej subskrypcji.|
|Usuń|Usuwa klaster. |
|Odśwież|Odświeża widok.|

**Menu po lewej:**  

  - **Menu w lewym górnym rogu**

    | Element| Opis |
    |---|---|
    |Omówienie|Zawiera ogólne informacje o klastrze.|
    |Dziennik aktywności|Pokaż i zbadaj dzienniki aktywności.|
    |Kontrola dostępu (IAM)|Korzystanie z przypisań ról.  Zobacz [Przypisywanie ról platformy Azure, aby zarządzać dostępem do zasobów subskrypcji platformy Azure](../role-based-access-control/role-assignments-portal.md).|
    |Tagi|Pozwala ustawić pary klucz/wartość, aby zdefiniować niestandardową taksonomię usług w chmurze. Na przykład możesz utworzyć klucz o nazwie **projekt**, a następnie użyć wspólnej wartości dla wszystkich usług skojarzonych z określonym projektem.|
    |Diagnozowanie i rozwiązywanie problemów|Wyświetl informacje dotyczące rozwiązywania problemów.|
    |Szybki start|Wyświetla informacje, które ułatwiają rozpoczęcie pracy z usługą HDInsight.|
    |narzędzia|Informacje pomocy dotyczące narzędzi związanych z usługą HDInsight.|

  - **Menu ustawień**  

    | Element| Opis |
    |---|---|
    |Rozmiar klastra|Sprawdź, Zwiększ i zmniejsz liczbę węzłów procesu roboczego klastra. Zobacz [skalowanie klastrów](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limity przydziału|Wyświetl używane i dostępne rdzenie dla subskrypcji.|
    |SSH + logowanie do klastra|Zawiera instrukcje dotyczące łączenia się z klastrem przy użyciu połączenia Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Skonfiguruj Data Lake Storage Gen1 dostępu.  Zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Konta magazynu|Wyświetlanie kont magazynu i kluczy. Konta magazynu są konfigurowane podczas procesu tworzenia klastra.|
    |Aplikacje|Dodawanie/usuwanie aplikacji usługi HDInsight.  Zobacz [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Akcje skryptu|Uruchom skrypty bash w klastrze. Zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).|
    |Zewnętrzne magazyny metadanych|Wyświetl [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/) magazynu metadanych. Magazyny metadanych można skonfigurować tylko podczas procesu tworzenia klastra.|
    |Partner usługi HDInsight|Dodaj/Usuń bieżącego partnera usługi HDInsight.|
    |Właściwości|Wyświetl [właściwości klastra](#properties).|
    |Blokady|Dodaj blokadę, aby uniemożliwić modyfikowanie lub usuwanie klastra.|
    |Eksportowanie szablonu|Wyświetl i Eksportuj szablon Azure Resource Manager dla klastra. Obecnie można eksportować tylko zależne konto usługi Azure Storage. Zobacz [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu szablonów Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu monitorowanie**

    | Element| Opis |
    |---|---|
    |Alerty|Zarządzanie alertami i akcjami.|
    |Metryki|Monitoruj metryki klastra w dziennikach Azure Monitor.|
    |Ustawienia diagnostyki|Ustawienia, w których mają być przechowywane metryki diagnostyki.|
    |Azure Monitor|Monitoruj klaster w Azure Monitor.|

  - **Menu Pomoc techniczna i rozwiązywanie problemów**

    | Element| Opis |
    |---|---|
    |Kondycja zasobów|Zobacz [Omówienie usługi Azure Resource Health](../service-health/resource-health-overview.md).|
    |Nowy wniosek o pomoc techniczną|Umożliwia utworzenie biletu pomocy technicznej w ramach pomocy technicznej firmy Microsoft.|

## <a name="cluster-properties"></a><a name="properties"></a> Właściwości klastra

Na [stronie głównej klastra](#homePage)w obszarze **Ustawienia** wybierz pozycję **Właściwości**.

|Element | Opis |
|---|---|
|NAZW|Nazwa klastra.|
|ADRES URL KLASTRA|Adres URL interfejsu sieci Web Ambari.|
|Prywatny punkt końcowy|Prywatny punkt końcowy klastra.|
|Secure Shell (SSH)|Nazwa użytkownika i hosta, które mają być używane podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH.|
|STAN|Jedno z: przerwane, zaakceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, Error, usuwanie, usunięte, TimedOut, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued lub ClusterCustomization.|
|REGION|Lokalizacja platformy Azure. Aby zapoznać się z listą obsługiwanych lokalizacji platformy Azure, zobacz pole listy rozwijanej **region** w [cenniku usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA UTWORZENIA|Data wdrożenia klastra.|
|System operacyjny|**System Windows** lub **Linux**.|
|TYP|Hadoop, HBase, burza, Spark.|
|Wersja|Zobacz [wersje usługi HDInsight](hdinsight-component-versioning.md).|
|Wersja minimalna protokołu TLS|Wersja protokołu TLS.|
|SUBSKRYPCJA|Nazwa subskrypcji.|
|DOMYŚLNE ŹRÓDŁO DANYCH|Domyślny system plików klastra.|
|Rozmiary węzłów procesu roboczego|Wybrany rozmiar maszyny wirtualnej węzłów procesu roboczego.|
|Rozmiar węzła głównego|Wybrany rozmiar maszyny wirtualnej węzłów głównych.|
|Sieć wirtualna|Nazwa Virtual Network, który został wdrożony w klastrze, jeśli został wybrany w czasie wdrażania.|

## <a name="move-clusters"></a>Przenoszenie klastrów

Klaster usługi HDInsight można przenieść do innej grupy zasobów platformy Azure lub innej subskrypcji.

Na [stronie głównej klastra](#homePage):

1. Wybierz pozycję **Przenieś** z górnego menu.
2. Wybierz pozycję **Przenieś do innej grupy zasobów** lub **Przenieś do innej subskrypcji**.
3. Postępuj zgodnie z instrukcjami podanymi na nowej stronie.

## <a name="delete-clusters"></a>Usuwanie klastrów

Usunięcie klastra nie powoduje usunięcia domyślnego konta magazynu ani żadnych połączonych kont magazynu. Klaster można utworzyć ponownie, używając tych samych kont magazynu i tych samych metadanych. Zalecamy używanie nowego domyślnego kontenera obiektów BLOB podczas ponownego tworzenia klastra.

Na [stronie głównej klastra](#homePage):

1. Wybierz pozycję **Usuń** z górnego menu.
2. Postępuj zgodnie z instrukcjami podanymi na nowej stronie.

Zobacz również [wstrzymywanie/zamykanie klastrów](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Po utworzeniu klastra można dodać do niego dodatkowe konta usługi Azure Storage i konta Azure Data Lake Storage. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalowanie klastrów

Funkcja skalowania klastra pozwala zmienić liczbę węzłów procesu roboczego używanych przez klaster usługi Azure HDInsight bez konieczności ponownego tworzenia klastra.

Aby uzyskać pełne informacje, zobacz [skalowanie klastrów usługi HDInsight](./hdinsight-scaling-best-practices.md) .

## <a name="pauseshut-down-clusters"></a>Wstrzymywanie/zamykanie klastrów

Większość zadań usługi Hadoop to zadania wsadowe, które są uruchamiane tylko sporadycznie. W przypadku większości klastrów usługi Hadoop klaster jest w dużym stopniu nieużywany do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

Istnieje wiele sposobów programowania procesu:

- Azure Data Factory użytkownika. Aby utworzyć połączone usługi HDInsight na żądanie, zobacz [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) .
- Użyj Azure PowerShell.  Zobacz [Analizowanie danych dotyczących opóźnień lotów](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Użyj interfejsu wiersza polecenia platformy Azure. Zobacz [Zarządzanie klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md).
- Użyj zestawu .NET SDK usługi HDInsight. Zobacz [przesyłanie Apache Hadoop zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z portalu, zobacz [usuwanie klastrów](#delete-clusters)

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Zobacz [Uaktualnianie klastra usługi HDInsight do nowszej wersji](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci Web Apache Ambari

Ambari zapewnia intuicyjny, łatwy w użyciu interfejs użytkownika sieci Web do zarządzania usługą Hadoop, który jest obsługiwany przez interfejsy API RESTful. Ambari umożliwia administratorom systemu zarządzanie i monitorowanie klastrów usługi Hadoop.

Na [stronie głównej klastra](#homePage):

1. Wybierz **pulpity nawigacyjne klastra**.

    ![Menu klastra Apache Hadoop usługi HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Na nowej stronie wybierz pozycję **Ambari Home** .
1. Wprowadź nazwę użytkownika i hasło do klastra.  Domyślną nazwą użytkownika klastra jest _administrator_.

Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight za pomocą interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Zmień hasła

Klaster HDInsight An może mieć dwa konta użytkowników. Konto użytkownika klastra usługi HDInsight (konto użytkownika HTTP) i konto użytkownika SSH są tworzone podczas procesu tworzenia. Za pomocą portalu można zmienić hasło konta użytkownika klastra i akcje skryptu, aby zmienić konto użytkownika SSH.

### <a name="change-the-cluster-user-password"></a>Zmiana hasła użytkownika klastra

> [!NOTE]  
> Zmiana hasła użytkownika (administratora) może spowodować niepowodzenie akcji skryptu uruchamianych w tym klastrze. Jeśli masz jakiekolwiek akcje utrwalonego skryptu, które są przeznaczone dla węzłów procesu roboczego, te skrypty mogą zakończyć się niepowodzeniem podczas dodawania węzłów do klastra za pomocą operacji zmiany rozmiaru. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Na [stronie głównej klastra](#homePage):
1. Wybierz pozycję **SSH + logowanie do klastra** w obszarze **Ustawienia**.
2. Wybierz pozycję **Zresetuj poświadczenie**.
3. Wprowadź i Potwierdź nowe hasło w polach tekstowych.
4. Wybierz przycisk **OK**.

Hasło zostanie zmienione we wszystkich węzłach w klastrze.

### <a name="change-the-ssh-user-password-or-public-key"></a>Zmień hasło użytkownika SSH lub klucz publiczny

1. Za pomocą edytora tekstów Zapisz następujący tekst jako plik o nazwie **changecredentials.sh**.

    > [!IMPORTANT]  
    > Musisz użyć edytora, który używa LF jako końca wiersza. Jeśli Edytor używa CRLF, skrypt nie działa.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Przekaż plik do lokalizacji przechowywania, do której można uzyskać dostęp z usługi HDInsight przy użyciu adresu HTTP lub HTTPS. Na przykład Publiczny magazyn plików, taki jak OneDrive lub Azure Blob Storage. Zapisz identyfikator URI (adres HTTP lub HTTPS) do pliku, ponieważ ten identyfikator URI jest wymagany w następnym kroku.
3. Na [stronie głównej klastra](#homePage)wybierz pozycję **Akcje skryptu** w obszarze **Ustawienia**.
4. Na stronie **Akcje skryptu** wybierz pozycję **Prześlij nowy**.
5. Na stronie **Prześlij akcję skryptu** wprowadź następujące informacje:

> [!NOTE]
> Hasła protokołu SSH nie mogą zawierać następujących znaków:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Pole | Wartość |
   | --- | --- |
   | Typ skryptu | Z listy rozwijanej wybierz pozycję **— niestandardowa** .|
   | Nazwa |"Zmiana poświadczeń SSH" |
   | Identyfikator URI skryptu bash |Identyfikator URI pliku changecredentials.sh |
   | Typy węzłów: (kierownik, proces roboczy, Nimbus, Nadzorca lub dozorcy). |✓ dla wszystkich typów węzłów na liście |
   | Parametry |Wprowadź nazwę użytkownika SSH, a następnie nowe hasło. Między nazwą użytkownika i hasłem powinna być jedna spacja. |
   | Utrwalaj tę akcję skryptu... |Nie zaznaczaj tego pola. |

6. Wybierz pozycję **Utwórz** , aby zastosować skrypt. Po zakończeniu działania skryptu można nawiązać połączenie z klastrem przy użyciu protokołu SSH z nowymi poświadczeniami.

## <a name="find-the-subscription-id"></a>Znajdowanie identyfikatora subskrypcji

Każdy klaster jest powiązany z subskrypcją platformy Azure.  Identyfikator subskrypcji platformy Azure jest widoczny na [stronie głównej klastra](#homePage).

## <a name="find-the-resource-group"></a>Znajdowanie grupy zasobów

W trybie Azure Resource Manager każdy klaster usługi HDInsight jest tworzony z grupą Azure Resource Manager. Grupa Menedżer zasobów jest widoczna na [stronie głównej klastra](#homePage).

## <a name="find-the-storage-accounts"></a>Znajdowanie kont magazynu

Klastry HDInsight wykorzystują konto usługi Azure Storage lub Azure Data Lake Storage do przechowywania danych. Każdy klaster usługi HDInsight może mieć jedno domyślne konto magazynu i wiele połączonych kont magazynu. Aby wyświetlić listę kont magazynu, na [stronie głównej klastra](#homePage) w obszarze **Ustawienia** wybierz pozycję **konta magazynu**.

## <a name="monitor-jobs"></a>Monitorowanie zadań

Zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Rozmiar klastra

Kafelek **rozmiar klastra** ze [strony głównej klastra](#homePage) zawiera liczbę rdzeni przypisywanych do tego klastra i sposób ich przydzielenia dla węzłów w tym klastrze.

> [!IMPORTANT]  
> Aby monitorować usługi udostępniane przez klaster usługi HDInsight, należy użyć interfejsu API REST Ambari Web lub Ambari. Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu platformy Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

- [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono podstawowe funkcje administracyjne. Więcej informacji można znaleźć w następujących artykułach:

- [Administrowanie usługą HDInsight przy użyciu Azure PowerShell](hdinsight-administer-use-powershell.md)
- [Administrowanie usługą HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)
- [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Szczegółowe informacje na temat korzystania z interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Używanie Apache Hive w usłudze HDInsight](hadoop/hdinsight-use-hive.md)
- [Korzystanie z programu Apache Sqoop w usłudze HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Używanie funkcji języka Python zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache chlewnej w usłudze HDInsight](hadoop/python-udf-hdinsight.md)
- [Która wersja Apache Hadoop znajduje się w usłudze Azure HDInsight?](hdinsight-component-versioning.md)