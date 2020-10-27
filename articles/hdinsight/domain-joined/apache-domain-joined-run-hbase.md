---
title: Apache HBase & pakiet Enterprise Security — Azure HDInsight
description: Samouczek — informacje na temat konfigurowania zasad Apache Ranger dla HBase w usłudze Azure HDInsight przy użyciu pakiet Enterprise Security.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 5747de399e7ae0cfe99ba013f8da376be0ba1b2a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544957"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Samouczek: Konfigurowanie zasad Apache HBase w usłudze HDInsight przy użyciu pakiet Enterprise Security

Dowiedz się, jak skonfigurować zasady platformy Apache Ranger na potrzeby klastrów Apache HBase pakietów Enterprise Security Package (ESP). Klastry ESP są łączone z domeną, co pozwala użytkownikom na uwierzytelnianie przy użyciu poświadczeń domeny. W tym samouczku utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do różnych rodzin kolumn w tabeli bazy danych HBase.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie użytkowników domeny
> * Tworzenie zasad platformy Ranger
> * Tworzenie tabel w klastrze bazy danych HBase
> * Testowanie zasad platformy Ranger

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Utwórz [klaster bazy danych HBase usługi HDInsight, używając pakietu Enterprise Security](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger

1. W przeglądarce połącz się z interfejsem użytkownika administratora platformy Ranger przy użyciu adresu URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Pamiętaj, aby zmienić wartość `<ClusterName>` na nazwę klastra bazy danych HBase.

    > [!NOTE]  
    > Poświadczenia platformy Ranger są inne niż poświadczenia klastra Hadoop. Aby zapobiec używaniu w przeglądarce buforowanych poświadczeń usługi Hadoop, należy połączyć się z interfejsem użytkownika administratora platformy Ranger w nowym oknie przeglądarki InPrivate.

2. Zaloguj się przy użyciu poświadczeń administratora usługi Azure Active Directory (AD). Poświadczenia administratora usługi Azure AD są inne niż poświadczenia klastra HDInsight i poświadczenia protokołu SSH węzła usługi HDInsight w systemie Linux.

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny

Zapoznaj się z sekcją dotyczącą [tworzenia klastra usługi HDInsight przy użyciu pakietu Enterprise Security](./apache-domain-joined-configure-using-azure-adds.md), aby dowiedzieć się, jak utworzyć użytkowników domeny **sales_user1** i **marketing_user1** . W scenariuszu produkcyjnym użytkownicy domeny pochodzą z dzierżawy usługi Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Tworzenie tabel bazy danych HBase i importowanie przykładowych danych

Protokół SSH umożliwia połączenie z klastrami HBase, a następnie korzystanie z [powłoki Apache HBase](https://hbase.apache.org/0.94/book/shell.html) w celu tworzenia tabel bazy danych HBase, wstawiania danych i wykonywania zapytań o dane. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Korzystanie z powłoki HBase

1. Z poziomu bezpiecznej powłoki (SSH) uruchom następujące polecenie bazy danych HBase:
   
    ```bash
    hbase shell
    ```

2. Utwórz bazę danych HBase `Customers` z dwiema rodzinami kolumn: `Name` i `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Wstaw dowolne dane:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Wyświetl zawartość tabeli:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![Dane wyjściowe powłoki HBase usługi HDInsight Hadoop](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Tworzenie zasad platformy Ranger

Utwórz zasady platformy Ranger dla użytkowników **sales_user1** i **marketing_user1** .

1. Otwórz **interfejs użytkownika administratora platformy Ranger** . Kliknij **\<ClusterName> _hbase** w obszarze **HBase** .

   ![Interfejs użytkownika administratora usługi HDInsight Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. Na ekranie **Lista zasad** zostaną wyświetlone wszystkie zasady platformy Ranger utworzone dla tego klastra. Lista może zawierać tylko jedne wstępnie skonfigurowane zasady. Kliknij pozycję **Dodaj nowe zasady** .

    ![Lista zasad HBase Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Na ekranie **Tworzenie zasad** wprowadź następujące wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Policy Name (Nazwa zasad)  |  sales_customers_name_contact   |
   |Tabela bazy danych HBase   |  Klienci |
   |Rodzina kolumn bazy danych HBase   |  Nazwa, kontakt |
   |Kolumna bazy danych HBase   |  * |
   |Wybierz grupę  | |
   |Select User (Wybierz użytkownika)  | sales_user1 |
   |Uprawnienia  | Odczyt |

   W nazwie tematu można uwzględnić następujące symbole wieloznaczne:

   * `*` oznacza zero lub więcej wystąpień znaków.
   * `?` oznacza pojedynczy znak.

   ![Tworzenie sprzedaży przez zasady Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą Azure AD, jeśli użytkownik domeny nie zostanie automatycznie wypełniony dla ustawienia **Select User** (Wybierz użytkownika).

4. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.

5. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady), a następnie wprowadź następujące wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Policy Name (Nazwa zasad)  |  marketing_customers_contact   |
   |Tabela bazy danych HBase   |  Klienci |
   |Rodzina kolumn bazy danych HBase   |  Kontakt |
   |Kolumna bazy danych HBase   |  * |
   |Wybierz grupę  | |
   |Select User (Wybierz użytkownika)  | marketing_user1 |
   |Uprawnienia  | Odczyt |

   ![Tworzenie marketingowe zasad w usłudze Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.

## <a name="test-the-ranger-policies"></a>Testowanie zasad platformy Ranger

Zgodnie ze skonfigurowanymi zasadami platformy Ranger użytkownik **sales_user1** może wyświetlić wszystkie dane dla kolumn w obydwu rodzinach kolumn `Name` i `Contact`. Użytkownik **marketing_user1** może wyświetlać dane tylko w rodzinie kolumn `Contact`.

### <a name="access-data-as-sales_user1"></a>Uzyskiwanie dostępu do danych jako sales_user1

1. Otwórz nowe połączenie SSH z klastrem. Użyj następującego polecenia, aby zalogować się do klastra:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Użyj polecenia kinit, aby zmienić kontekst na kontekst naszego żądanego użytkownika.

   ```bash
   kinit sales_user1
   ```

2. Otwórz powłokę HBase i Zeskanuj tabelę `Customers` .

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Zauważ, że użytkownik „sales” może wyświetlać wszystkie kolumny tabeli `Customers`, w tym dwie kolumny z rodziny kolumn `Name`, a także pięć kolumn z rodziny kolumn `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>Uzyskiwanie dostępu do danych jako marketing_user1

1. Otwórz nowe połączenie SSH z klastrem. Użyj następującego polecenia, aby zalogować się jako użytkownik **marketing_user1** :

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Użyj polecenia kinit, aby zmienić kontekst na kontekst naszego żądanego użytkownika.

   ```bash
   kinit marketing_user1
   ```

1. Otwórz powłokę HBase i Zeskanuj tabelę `Customers` :

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Zauważ, że użytkownik „marketing” może wyświetlić tylko pięć kolumn z rodziny kolumn `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. Wyświetl zdarzenia dostępu inspekcji z poziomu interfejsu użytkownika platformy Ranger.

   ![Inspekcja zasad interfejsu użytkownika usługi HDInsight Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń utworzony klaster bazy danych HBase, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W polu **Wyszukaj** w górnej części wpisz **HDInsight** . 
1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi** .
1. Na wyświetlonej liście klastrów usługi HDInsight kliknij symbol **...** obok klastra utworzonego na potrzeby tego samouczka. 
1. Kliknij polecenie **Usuń** . Kliknij przycisk **Yes** (Tak).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozpocznij pracę z bazą danych Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)