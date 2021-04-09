---
title: Zarządzanie klastrami pakiet Enterprise Security — usługa Azure HDInsight
description: Dowiedz się, jak zarządzać klastrami usługi Azure HDInsight za pomocą pakiet Enterprise Security.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: bc31c3d71590a6b8c0b324ffcb8c10129a9f8699
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863245"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Zarządzanie klastrami usługi HDInsight przy użyciu pakiet Enterprise Security

Informacje o użytkownikach i rolach w usłudze HDInsight pakiet Enterprise Security (ESP) i sposobach zarządzania klastrami ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu VSCode

Można połączyć normalny klaster przy użyciu nazwy użytkownika zarządzanego przez Apache Ambari, a także połączyć Klaster Apache Hadoop zabezpieczeń przy użyciu nazwy użytkownika domeny (na przykład: `user1@contoso.com` ).

1. Otwórz [Visual Studio Code](https://code.visualstudio.com/). Upewnij się, że zainstalowano rozszerzenie [narzędzi Hive & platformy Spark](../hdinsight-for-vscode.md) .

1. Wykonaj kroki opisane w sekcji [łączenie klastra](../hdinsight-for-vscode.md#link-a-cluster) w celu Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą środowiska IntelliJ

Normalny klaster można połączyć przy użyciu nazwy użytkownika Ambari Managed, a także połączyć klaster usługi Hadoop z zabezpieczeniami przy użyciu nazwy użytkownika domeny (na przykład: `user1@contoso.com` ).

1. Otwórz środowisko IntelliJ IDEA. Upewnij się, że spełniono wszystkie [wymagania wstępne](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) .

1. Wykonaj kroki opisane w sekcji [łączenie klastra](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) z IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu Eclipse

Normalny klaster można połączyć przy użyciu nazwy użytkownika Ambari Managed, a także połączyć klaster usługi Hadoop z zabezpieczeniami przy użyciu nazwy użytkownika domeny (na przykład: `user1@contoso.com` ).

1. Otwórz program Eclipse. Upewnij się, że spełniono wszystkie [wymagania wstępne](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) .

1. Wykonaj kroki opisane w sekcji [łączenie klastra](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) w celu przeszukania.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Dostęp do klastrów przy użyciu pakiet Enterprise Security

Pakiet Enterprise Security (wcześniej znany jako HDInsight Premium) zapewnia dostęp do klastra przez wiele użytkowników, w którym uwierzytelnianie jest wykonywane przez Active Directory i autoryzację przez usługi Apache Ranger i listy ACL magazynu (ADLS list ACL). Autoryzacja zapewnia bezpieczne granice między wieloma użytkownikami i zezwala na dostęp do danych tylko użytkownikom uprzywilejowanym na podstawie zasad autoryzacji.

Zabezpieczenia i izolacja użytkowników są ważne dla klastra usługi HDInsight z pakiet Enterprise Security. Aby spełnić te wymagania, dostęp SSH do klastra z pakiet Enterprise Security jest obsługiwany przez użytkownika lokalnego wybranego podczas tworzenia klastra, a także użytkownikom dostępnym w usłudze AAD-DS (tj. Kerberos). W poniższej tabeli przedstawiono zalecane metody dostępu dla każdego typu klastra:

|Obciążenie|Scenariusz|Metoda dostępu|
|--------|--------|-------------|
|Apache Hadoop|Hive — zadania interaktywne/zapytania    |<ul><li>[Z usługi Beeline](#beeline)</li><li>[widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Zadania interaktywne/zapytania, PySpark Interactive|<ul><li>[Z usługi Beeline](#beeline)</li><li>[Zeppelin z usługi Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scenariusze przetwarzania wsadowego — przesyłanie na platformie Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Zapytanie interaktywne (LLAP)|Interaktywne|<ul><li>[Z usługi Beeline](#beeline)</li><li>[widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Dowolne|Zainstaluj aplikację niestandardową|<ul><li>[Akcje skryptu](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter nie jest zainstalowana/obsługiwana w pakiet Enterprise Security.

Korzystanie z standardowych interfejsów API pomaga z perspektywą zabezpieczeń. Uzyskasz również następujące korzyści:

- **Zarządzanie** — umożliwia zarządzanie kodem i Automatyzowanie zadań przy użyciu standardowych interfejsów API — usługi LIVY, HS2 itp.
- **Inspekcja** — za pomocą protokołu SSH nie ma możliwości przeprowadzania inspekcji, którzy użytkownicy są SSH do klastra. Nie będzie to miało miejsca w przypadku konstruowania zadań za pośrednictwem standardowych punktów końcowych, ponieważ będą one wykonywane w kontekście użytkownika.

### <a name="use-beeline"></a><a name="beeline"></a>Korzystanie z usługi Beeline

Zainstaluj program Z usługi Beeline na maszynie i nawiąż połączenie za pośrednictwem publicznej sieci Internet, korzystając z następujących parametrów:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Jeśli zainstalowano program Z usługi Beeline lokalnie i nawiążesz połączenie za pośrednictwem Virtual Network platformy Azure, użyj następujących parametrów:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Aby znaleźć w pełni kwalifikowaną nazwę domeny węzła głównego, użyj informacji znajdujących się w dokumencie Zarządzanie usługą HDInsight przy użyciu Ambari interfejsu API REST.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Użytkownicy klastrów usługi HDInsight z partycją ESP

Klaster usługi HDInsight inny niż ESP ma dwa konta użytkowników, które są tworzone podczas tworzenia klastra:

- **Ambari administrator**: to konto jest również znane jako *użytkownik usługi Hadoop* lub *użytkownik http*. Tego konta można użyć do zalogowania się do Ambari o `https://CLUSTERNAME.azurehdinsight.net` . Można go również użyć do uruchamiania zapytań w widokach Ambari, wykonywania zadań za pośrednictwem zewnętrznych narzędzi (na przykład PowerShell, Templeton, Visual Studio) i uwierzytelniania za pomocą sterownika programu Hive ODBC oraz narzędzi analizy biznesowej (na przykład Excel, Power BI lub Tableau).

Klaster usługi HDInsight z protokołem ESP ma trzech nowych użytkowników oprócz administratora Ambari.

- **Ranger administrator**: to konto jest lokalnym kontem administratora Apache Ranger. Nie jest to użytkownik domeny usługi Active Directory. Tego konta można użyć do skonfigurowania zasad i nawiązać innych administratorów lub administratorów delegowanych (tak, aby użytkownicy mogli zarządzać zasadami). Domyślnie nazwa użytkownika to *admin* , a hasło jest takie samo jak hasło administratora Ambari. Hasło można zaktualizować na stronie Ustawienia w Ranger.

- **Domena administratora klastra**: to konto jest użytkownikiem domeny usługi Active Directory wyznaczono jako administrator klastra Hadoop, w tym Ambari i Ranger. Należy podać poświadczenia tego użytkownika podczas tworzenia klastra. Ten użytkownik ma następujące uprawnienia:
    - Przyłączanie maszyn do domeny i umieszczanie ich w jednostce organizacyjnej określonej podczas tworzenia klastra.
    - Utwórz jednostki usługi w jednostce organizacyjnej, która została określona podczas tworzenia klastra.
    - Utwórz odwrotne wpisy DNS.

    Zwróć uwagę, że inni użytkownicy usługi AD mają również te uprawnienia.

    Istnieje kilka punktów końcowych w klastrze (na przykład Templeton), które nie są zarządzane przez Ranger i dlatego nie są bezpieczne. Te punkty końcowe są zablokowane dla wszystkich użytkowników z wyjątkiem użytkownika domeny administratora klastra.

- **Regularna**: podczas tworzenia klastra można podać wiele grup usługi Active Directory. Użytkownicy w tych grupach są synchronizowani z Ranger i Ambari. Ci użytkownicy są użytkownikami domeny i mają dostęp tylko do Ranger punktów końcowych zarządzanych przez program (na przykład serwera hiveserver2). Wszystkie zasady i inspekcja RBAC będą stosowane dla tych użytkowników.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role klastrów usługi HDInsight z partycją ESP

Usługa HDInsight pakiet Enterprise Security ma następujące role:

- Administrator klastra
- Operator klastra
- Administrator usługi
- Operator usługi
- Użytkownik klastra

**Aby wyświetlić uprawnienia tych ról**

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie wybierz pozycję **role**.
3. Wybierz niebieski znak zapytania, aby wyświetlić uprawnienia:

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png" alt-text="Uprawnienia ról usługi HDInsight ESP" border="true":::

## <a name="open-the-ambari-management-ui"></a>Otwórz interfejs użytkownika zarządzania Ambari

1. Przejdź do `https://CLUSTERNAME.azurehdinsight.net/` lokalizacji, gdzie ClusterName jest nazwą klastra.
1. Zaloguj się do Ambari przy użyciu nazwy użytkownika i hasła administratora klastra.
1. Wybierz menu rozwijane **administratora** w prawym górnym rogu, a następnie wybierz pozycję **Zarządzaj Ambari**.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png" alt-text="Usługa ESP HDInsight — zarządzanie Apache Ambari" border="true":::

    Interfejs użytkownika wygląda następująco:

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png" alt-text="Interfejs użytkownika funkcji ESP HDInsight Apache Ambari Management" border="true":::

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Wyświetl listę użytkowników domeny synchronizowanych z Active Directory

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie wybierz pozycję **Użytkownicy**. Zobaczysz wszystkich użytkowników synchronizowanych z Active Directory do klastra usługi HDInsight.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png" alt-text="Lista użytkowników listy UI usługi HDInsight Ambari Management" border="true":::

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Wyświetl listę grup domen synchronizowanych z Active Directory

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie wybierz pozycję **grupy**. Zostaną wyświetlone wszystkie grupy synchronizowane z Active Directory do klastra usługi HDInsight.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png" alt-text="Grupy list UI usługi HDInsight Ambari Management" border="true":::

## <a name="configure-hive-views-permissions"></a>Konfigurowanie uprawnień do widoków Hive

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie wybierz pozycję **widoki**.
3. Wybierz pozycję **Hive** , aby wyświetlić szczegóły.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png" alt-text="Widoki programu Hive interfejsu użytkownika usługi HDInsight Ambari Management" border="true":::

4. Wybierz łącze **Widok Hive** , aby skonfigurować widoki programu Hive.
5. Przewiń w dół do sekcji **uprawnienia** .

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png" alt-text="Widoki programu Hive dla interfejsu użytkownika usługi HDInsight Ambari Management Konfigurowanie uprawnień" border="true":::

6. Wybierz pozycję **Dodaj użytkownika** lub **Dodaj grupę**, a następnie określ użytkowników lub grupy, które mogą używać widoków Hive.

## <a name="configure-users-for-the-roles"></a>Konfigurowanie użytkowników dla ról

 Aby wyświetlić listę ról i ich uprawnień, zobacz Role klastrów usługi HDInsight z ESP.

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie wybierz pozycję **role**.
3. Wybierz pozycję **Dodaj użytkownika** lub **Dodaj grupę** , aby przypisać użytkowników i grupy do różnych ról.

## <a name="next-steps"></a>Następne kroki

- Aby skonfigurować klaster usługi HDInsight z pakiet Enterprise Security, zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](./apache-domain-joined-configure-using-azure-adds.md).
- Aby skonfigurować zasady Hive i uruchamiać zapytania programu Hive, zobacz [Konfigurowanie zasad Apache Hive dla klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-run-hive.md).