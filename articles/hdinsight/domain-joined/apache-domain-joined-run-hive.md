---
title: Zasady Apache Hive w usłudze Apache Ranger — Azure HDInsight
description: Dowiedz się, jak skonfigurować zasady Apache Ranger dla usługi Hive w usłudze Azure HDInsight przy użyciu pakiet Enterprise Security.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 15c406576b373577a2a3a50108acad7ccbf36699
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863262"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurowanie zasad technologii Apache Hive w usłudze HDInsight przy użyciu pakietu Enterprise Security

Dowiedz się, jak skonfigurować zasady Apache Ranger dla Apache Hive. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tabeli hivesampletable. Tabela hivesampletable jest dołączana do klastrów usługi HDInsight. Po skonfigurowaniu zasad należy używać programu Excel i sterownika ODBC do łączenia się z tabelami programu Hive w usłudze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi HDInsight z pakiet Enterprise Security. Zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Stacja robocza z Microsoft 365 aplikacjami dla przedsiębiorstw, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone lub Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger
**Aby połączyć się z interfejsem użytkownika administratora platformy Ranger**

1. W przeglądarce przejdź do interfejsu użytkownika administratora Ranger w miejscu, `https://CLUSTERNAME.azurehdinsight.net/Ranger/` gdzie ClusterName jest nazwą klastra.

   > [!NOTE]  
   > Ranger używa innych poświadczeń niż Klaster Apache Hadoop. Aby uniemożliwić przeglądarkom korzystanie z zbuforowanych poświadczeń usługi Hadoop, Użyj nowego okna przeglądarki InPrivate do nawiązania połączenia z interfejsem użytkownika administratora Ranger.

2. Zaloguj się za pomocą nazwy i hasła użytkownika domeny administratora klastra:

    :::image type="content" source="./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png" alt-text="Strona główna usługi HDInsight ESP Ranger" border="true":::

    Obecnie platforma Ranger współpracuje tylko z usługami Yarn i Hive.

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny

Więcej informacji na temat tworzenia hiveruser1 i hiveuser2 można znaleźć w temacie [Tworzenie klastra usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Te dwa konta użytkowników są używane w tym artykule.

## <a name="create-ranger-policies"></a>Tworzenie zasad platformy Ranger

W tej sekcji utworzysz dwie zasady Ranger na potrzeby uzyskiwania dostępu do hivesampletable. Możesz przydzielić uprawnienie select (wybór) do innego zestawu kolumn. Dwóch użytkowników zostało utworzonych przy użyciu polecenia [Utwórz klaster usługi HDInsight z partycją ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). W następnej sekcji przetestujesz dwie zasady w programie Excel.

**Aby utworzyć zasady platformy Ranger**

1. Otwórz interfejs użytkownika administratora platformy Ranger. Zobacz sekcję Łączenie z interfejsem użytkownika administratora platformy Apache Ranger.
2. Wybierz **CLUSTERNAME_Hive**, w obszarze **Hive**. Zostaną wyświetlone dwie wstępnie skonfigurowane zasady.
3. Wybierz pozycję **Dodaj nowe zasady**, a następnie wprowadź następujące wartości:

    |Właściwość |Wartość |
    |---|---|
    |Policy Name (Nazwa zasad)|Read-hivesampletable-All|
    |Baza danych programu Hive|default|
    |tabela|hivesampletable|
    |Kolumna Hive|*|
    |Select User (Wybierz użytkownika)|Użytkownik hiveuser1|
    |Uprawnienia|Wybierz|

    :::image type="content" source="./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png" alt-text="Konfiguracja zasad Hive ESP Ranger usługi HDInsight" border="true":::.

    > [!NOTE]  
    > Jeśli pole Select User (Wybierz użytkownika) nie zostało wypełnione przy użyciu danych użytkownika domeny, zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą AAD.

4. Wybierz pozycję **Dodaj** , aby zapisać zasady.

5. Powtórz dwa ostatnie kroki, aby utworzyć inne zasady o następujących właściwościach:

    |Właściwość |Wartość |
    |---|---|
    |Policy Name (Nazwa zasad)|Read-hivesampletable-devicemake|
    |Baza danych programu Hive|default|
    |tabela|hivesampletable|
    |Kolumna Hive|ClientID, devicemake|
    |Select User (Wybierz użytkownika)|hiveuser2|
    |Uprawnienia|Wybierz|

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive

Instrukcje można znaleźć w sekcji [Create Hive ODBC data source](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (Tworzenie źródła danych ODBC usługi Hive).  

 | Właściwość  |Opis |
 | --- | --- |
 | Data Source Name (Nazwa źródła danych) | Nadaj nazwę źródła danych. |
 | Host | Wprowadź CLUSTERNAME.azurehdinsight.net. np. myHDICluster.azurehdinsight.net. |
 | Port | Użyj portu **443**. (Ten port został zmieniony z 563 na 443). |
 | baza danych | Użyj **domyślnego**. |
 | Hive Server Type (Typ serwera Hive) | Wybierz wartość **Hive Server 2**. |
 | Mechanism (Mechanizm) | Wybierz wartość **Azure HDInsight Service** (Usługa Azure HDInsight). |
 | HTTP Path (Ścieżka HTTP) | Pozostaw to pole puste. |
 | Nazwa użytkownika | Wprowadź hiveuser1@contoso158.onmicrosoft.com. Zaktualizuj nazwę domeny, jeśli jest inna. |
 | Hasło | Wprowadź hasło użytkownika hiveuser1. |

Pamiętaj o kliknięciu przycisku **Test** (Testuj) przed zapisaniem źródła danych.

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight

W ostatniej sekcji zostały skonfigurowane dwie zasady.  Użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach, a użytkownik hiveuser2 ma uprawnienia select w dwóch kolumnach. W tej sekcji będziesz personifikować dwóch użytkowników w celu zaimportowania danych do programu Excel.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.

1. Na karcie **dane** przejdź do **pobierania danych**  >  **z innych źródeł**  >  **z ODBC** , aby uruchomić z okna **ODBC** .

    :::image type="content" source="./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png" alt-text="Kreator otwierania połączenia danych" border="true":::

1. Z listy rozwijanej wybierz nazwę źródła danych utworzoną w ostatniej sekcji, a następnie wybierz przycisk **OK**.

1. Do pierwszego użycia zostanie otwarte okno dialogowe **sterownika ODBC** . Z menu po lewej stronie wybierz pozycję **Windows** . Następnie wybierz pozycję **Połącz** , aby otworzyć okno **Nawigator** .

1. Zaczekaj na otwarcie okna dialogowego **Wybieranie bazy danych i tabeli**. Może to potrwać kilka sekund.

1. Wybierz pozycję **hivesampletable**, a następnie wybierz pozycję **Next (dalej**).

1. Wybierz pozycję **Zakończ**.

1. W oknie dialogowym **Importowanie danych** możesz zmienić lub określić zapytanie. W tym celu wybierz pozycję **Właściwości**. Może to potrwać kilka sekund.

1. Wybierz kartę **Definicja** . Tekst polecenia:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   Zgodnie ze zdefiniowanymi zasadami platformy Ranger użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach.  To zapytanie działa z poświadczeniami hiveuser1's, ale to zapytanie nie działa z poświadczeniami użytkownika hiveuser2.

1. Wybierz **przycisk OK** , aby zamknąć okno dialogowe właściwości połączenia.

1. Wybierz **przycisk OK** , aby zamknąć okno dialogowe **Importowanie danych** .  

1. Wprowadź ponownie hasło użytkownika hiveuser1, a następnie kliknij przycisk **OK**. Importowanie danych do programu Excel może potrwać kilka sekund. Gdy wszystko będzie gotowe, zobaczysz 11 kolumn danych.

Aby przetestować drugie zasady (Read-hivesampletable-devicemake), utworzone w ostatniej sekcji

1. Dodaj nowy arkusz w programie Excel.
2. Wykonaj kroki ostatniej procedury, aby zaimportować dane.  Jedyną zmianą jest użycie poświadczeń użytkownika hiveuser2 zamiast hiveuser1's. To nie powiedzie się, ponieważ tylko hiveuser2 ma uprawnienia do wyświetlania dwóch kolumn. Wystąpi następujący błąd:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Wykonaj kroki tej samej procedury, aby zaimportować dane. Tym razem użyj poświadczeń użytkownika hiveuser2 i zmień instrukcję select z:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    na:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    Gdy wszystko będzie gotowe, zobaczysz dwie kolumny importowanych danych.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować klaster usługi HDInsight z pakiet Enterprise Security, zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Aby zarządzać klastrem usługi HDInsight przy użyciu protokołu ESP, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-manage.md).
* Aby uruchamiać zapytania programu Hive przy użyciu protokołu SSH w klastrach usługi HDInsight z protokołem ESP, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Aby nawiązać połączenie z usługą Hive przy użyciu usługi Hive JDBC, zobacz [nawiązywanie połączenia z Apache Hive w usłudze Azure HDInsight przy użyciu sterownika JDBC Hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Aby połączyć program Excel z usługą Hadoop przy użyciu interfejsu ODBC platformy Hive, zobacz [Connect Excel to Apache Hadoop z dyskiem ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Aby połączyć program Excel z usługą Hadoop przy użyciu Power Query, zobacz [Connect Excel to Apache Hadoop przy użyciu Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)