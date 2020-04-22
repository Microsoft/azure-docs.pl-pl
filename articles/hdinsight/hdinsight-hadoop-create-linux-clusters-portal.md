---
title: Tworzenie klastrów Apache Hadoop przy użyciu przeglądarki sieci Web Azure HDInsight
description: Dowiedz się, jak tworzyć apache Hadoop, Apache HBase, Apache Storm lub Apache Spark klastrów na HDInsight. Użyj przeglądarki sieci Web i witryny Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: 88392e4e61c28d4fe821c8176242246d10e52b23
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770894"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu witryny Azure portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Portal Azure to internetowe narzędzie do zarządzania usługami i zasobami hostowanymi w chmurze platformy Microsoft Azure. W tym artykule dowiesz się, jak utworzyć klastry usługi Azure HDInsight oparte na systemie Linux przy użyciu portalu. Dodatkowe szczegóły są dostępne w [umień klastrów HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Portal Azure udostępnia większość właściwości klastra. Korzystając z szablonów usługi Azure Resource Manager, można ukryć wiele szczegółów. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżera zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-clusters"></a>Tworzenie klastrów

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z górnego menu wybierz pozycję **+ Utwórz zasób**.

    ![Tworzenie nowego klastra w witrynie Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Tworzenie nowego klastra w witrynie Azure portal")

1. Wybierz **Analytics** > **Azure HDInsight,** aby przejść do strony **Tworzenie klastra HDInsight.**

## <a name="basics"></a>Podstawy

![Usługa HDInsight tworzy podstawowe informacje o klastrze](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Tworzenie nowego klastra w witrynie Azure portal")

Na karcie **Podstawy** podaj następujące informacje:

|Właściwość |Opis |
|---|---|
|Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra.|
|Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**.|
|Nazwa klastra|Podaj globalnie unikatową nazwę.|
|Region|Z listy rozwijanej wybierz region, w którym jest tworzony klaster.|
|Typ klastra|Kliknij **pozycję Wybierz typ klastra,** aby otworzyć listę. Z listy wybierz typ klastra poszukiwanego. Klastry HDInsight są dostępne w różnych typach. Odpowiadają one obciążeniu lub technologii, dla których jest dostrojony klaster. Nie ma żadnej obsługiwanej metody tworzenia klastra, który łączy wiele typów.|
|Wersja|Z listy rozwijanej wybierz **wersję**. Użyj wersji domyślnej, jeśli nie wiesz, co wybrać. Więcej informacji można znaleźć w temacie [HDInsight cluster versions](hdinsight-component-versioning.md) (Wersje klastrów usługi HDInsight).|
|Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika, domyślnie jest **admin**.|
|Hasło logowania klastra|Podaj hasło.|
|Potwierdź hasło logowania klastra|Ponowne wniesienie hasła|
|Nazwa użytkownika protokołu SSH (Secure Shell)|Podaj nazwę użytkownika, domyślnie jest **sshuser**|
|Używanie hasła logowania klastra dla SSH|Jeśli chcesz mieć to samo hasło SSH, co hasło administratora określone wcześniej, zaznacz pole wyboru **Użyj hasła logowania do klastra dla SSH.** Jeśli nie, podaj **hasło** lub **klucz publiczny,** aby uwierzytelnić użytkownika SSH. Kluczem publicznym jest podejście, które zalecamy. Wybierz **pozycję Wybierz** u dołu, aby zapisać konfigurację poświadczeń.  Aby uzyskać więcej informacji, zobacz [Łączenie się z hdinsight (Apache Hadoop) za pomocą SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Wybierz **dalej: >>do przechowywania,** aby przejść do następnej karty.

## <a name="storage"></a>Magazyn

![Usługa HDInsight umożliwia tworzenie magazynu klastra](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Tworzenie nowego klastra w witrynie Azure portal — magazyn")

### <a name="primary-storage"></a>Magazyn podstawowy

Z listy rozwijanej dla **głównego typu magazynu**wybierz domyślny typ magazynu. Późniejsze pola do ukończenia będą się różnić w zależności od wyboru. Dla **usługi Azure Storage:**

1. W obszarze **Wybór metoda**, wybierz opcję Wybierz **z listy**lub **Użyj klawisza dostępu**.
    * W obszarze **Wybierz z listy**wybierz z listy wybierz konto magazynu **podstawowego** z listy rozwijanej lub wybierz pozycję **Utwórz nowe**.
    * W przypadku **korzystania z klucza dostępu**wprowadź nazwę konta **magazynu**. Następnie podaj **klawisz access**.

1. W polu **Kontener**zaakceptuj wartość domyślną lub wprowadź nową.

### <a name="additional-azure-storage"></a>Dodatkowa usługa Azure Storage

Opcjonalnie: wybierz **pozycję Dodaj usługę Azure Storage, aby** uzyskać dodatkowe miejsce do magazynowania klastra. Korzystanie z dodatkowego konta magazynu w innym regionie niż klaster HDInsight nie jest obsługiwane.

### <a name="metastore-settings"></a>Ustawienia magazynu metastore

Opcjonalnie: Określ istniejącą bazę danych SQL, aby zapisać metadane Apache Hive, Apache Oozie i lub Apache Ambari poza klastrem. Usługa Azure SQL Database, która jest używana dla magazynu meta, musi zezwalać na łączność z innymi usługami platformy Azure, w tym usługą Azure HDInsight. Podczas tworzenia magazynu metastore nie należy nazywać bazy danych myślnikami ani myślnikami. Te znaki mogą spowodować niepowodzenie procesu tworzenia klastra.

Wybierz **dalej: Zabezpieczenia + >>sieci,** aby przejść do następnej karty.

## <a name="security--networking"></a>Bezpieczeństwo + sieć

![Usługa HDInsight tworzy sieci zabezpieczeń klastra](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "Usługa HDInsight tworzy sieci zabezpieczeń klastra")

Na karcie **Zabezpieczenia + sieć** podaj następujące informacje:

|Właściwość |Opis |
|---|---|
|Pakiet zabezpieczeń przedsiębiorstwa|Opcjonalnie: Zaznacz to pole wyboru, aby użyć **pakietu zabezpieczeń przedsiębiorstwa**. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastra HDInsight z pakietem zabezpieczeń przedsiębiorstwa przy użyciu usług domenowych Active Directory platformy Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Opcjonalnie: Wybierz wersję TLS z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia warstwy transportu](./transport-layer-security.md).|
|Sieć wirtualna|Opcjonalnie: Wybierz istniejącą sieć wirtualną i podsieć z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Artykuł zawiera określone wymagania konfiguracyjne dla sieci wirtualnej.|
|Ustawienia szyfrowania dysku|Opcjonalnie: Zaznacz to pole wyboru, aby używać szyfrowania. Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysku klucza zarządzanego przez klienta](./disk-encryption.md).|
|Serwer proxy REST Kafka|To ustawienie jest dostępne tylko dla typu klastra Platformy Kafka. Aby uzyskać więcej informacji, zobacz [Korzystanie z serwera proxy REST](./kafka/rest-proxy.md).|
|Tożsamość|Opcjonalnie: Wybierz istniejącą tożsamość usługi przypisaną przez użytkownika z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [Tożsamości zarządzane w usłudze Azure HDInsight](./hdinsight-managed-identities.md).|

Wybierz **dalej: Konfiguracja + >>cenowa,** aby przejść do następnej karty.

## <a name="configuration--pricing"></a>Konfiguracja + ceny

![Usługa HDInsight umożliwia tworzenie konfiguracji klastra](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Karta Konfiguracja i cennik")

Na karcie **Konfiguracja + cennik** podaj następujące informacje:

|Właściwość |Opis |
|---|---|
|+ Dodaj aplikację|Opcjonalnie: wybierz dowolną aplikację. Microsoft, niezależni dostawcy oprogramowania (ISV) lub można tworzyć te aplikacje. Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji podczas tworzenia klastra](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Rozmiar węzła|Opcjonalnie: wybierz węzeł o innym rozmiarze.|
|Liczba węzłów|Opcjonalnie: Wprowadź liczbę węzłów dla określonego typu węzła. Jeśli planujesz więcej niż 32 węzłów procesu roboczego, wybierz rozmiar węzła głównego z co najmniej ośmioma rdzeniami i 14 GB pamięci RAM. Planowanie węzłów podczas tworzenia klastra lub skalowania klastra po utworzeniu.|
|Włączanie skalowania automatycznego|Opcjonalnie: Zaznacz to pole wyboru, aby włączyć tę funkcję. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie klastrów usługi Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Dodaj akcję skryptu|Opcjonalnie: Ta opcja działa, jeśli chcesz użyć skryptu niestandardowego, aby dostosować klaster podczas tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptów, zobacz [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).|

Wybierz **pozycję Przejrzyj + utwórz >>,** aby sprawdzić poprawność konfiguracji klastra i przejść do ostatniej karty.

## <a name="review--create"></a>Recenzja + tworzenie

![Usługa HDInsight tworzy podsumowanie klastra](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Określanie liczby węzłów klastra")

Przejrzyj ustawienia. Wybierz opcję **Utwórz**, aby utworzyć klaster.

Tworzenie klastra zajmuje trochę czasu, zwykle około 20 minut. Monitorowanie **powiadomień** w celu sprawdzenia procesu inicjowania obsługi administracyjnej.

## <a name="post-creation"></a>Tworzenie postów

Po zakończeniu procesu tworzenia wybierz pozycję **Przejdź do zasobu** z powiadomienia **o pomyślnym wdrożeniu.** Okno klastra zawiera następujące informacje.

![Omówienie klastra portalu HDI Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Właściwości klastra")

Niektóre ikony w oknie są wyjaśnione w następujący sposób:

|Właściwość | Opis |
|---|---|
|Omówienie|Zawiera wszystkie istotne informacje o klastrze. Przykładami są nazwa, grupa zasobów, do której należy, lokalizacja, system operacyjny i adres URL pulpitu nawigacyjnego klastra.|
|Pulpity nawigacyjne klastra|Kieruje cię do portalu Ambari skojarzonego z klastrem.|
|SSH + Logowanie klastra|Zawiera informacje potrzebne do uzyskania dostępu do klastra przy użyciu SSH.|
|Usuń|Usuwa klaster HDInsight.|

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* [Dostosowywanie klastrów usługi HDInsight przy użyciu bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Usuwanie klastra

Zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.](./hdinsight-delete-cluster.md)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Pomyślnie utworzono klaster HDInsight. Dowiedz się, jak pracować z klastrem.

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Wprowadzenie do Apache HBase w programie HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Opracowanie topologii Java dla Apache Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Tworzenie aplikacji autonomicznej przy użyciu scalii](spark/apache-spark-create-standalone-application.md)
