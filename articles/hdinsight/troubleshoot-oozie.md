---
title: Rozwiązywanie problemów z programem Apache Oozie w usłudze Azure HDInsight
description: Rozwiązywanie niektórych błędów usługi Apache Oozie w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: f30c66839228e43f9a6fbdce9914f1ca1572c0ec
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538939"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Rozwiązywanie problemów z programem Apache Oozie w usłudze Azure HDInsight

Za pomocą interfejsu użytkownika Apache Oozie można wyświetlać dzienniki Oozie. Interfejs użytkownika Oozie zawiera również linki do dzienników JobTracker dla zadań MapReduce, które zostały uruchomione w ramach przepływu pracy. Wzorzec do rozwiązywania problemów powinien być:

1. Wyświetl zadanie w interfejsie użytkownika sieci Web Oozie.

2. Jeśli wystąpi błąd lub błąd konkretnej akcji, wybierz akcję, aby zobaczyć, czy pole **komunikatu o błędzie** zawiera więcej informacji na temat błędu.

3. Jeśli jest dostępna, użyj adresu URL z akcji, aby wyświetlić więcej szczegółów, takich jak dzienniki JobTracker, dla akcji.

Poniżej znajdują się konkretne błędy, które można napotkać i sposoby ich rozwiązywania.

## <a name="ja009-cant-initialize-cluster"></a>JA009: nie można zainicjować klastra

### <a name="issue"></a>Problem

Stan zadania zostanie zmieniony na **zawieszone** . Szczegóły zadania zawierają `RunHiveScript` stan **START_MANUAL** . Po wybraniu akcji zostanie wyświetlony następujący komunikat o błędzie:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Przyczyna

Adresy magazynu obiektów blob platformy Azure używane w pliku **job.xml** nie zawierają kontenera magazynu ani nazwy konta magazynu. Wymagany format adresu magazynu obiektów BLOB `wasbs://containername@storageaccountname.blob.core.windows.net` .

### <a name="resolution"></a>Rozwiązanie

Zmień adresy magazynu obiektów BLOB używane przez zadanie.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie nie może personifikować &lt; użytkownika&gt;

### <a name="issue"></a>Problem

Stan zadania zostanie zmieniony na **zawieszone** . Szczegóły zadania zawierają `RunHiveScript` stan **START_MANUAL** . W przypadku wybrania akcji zostanie wyświetlony następujący komunikat o błędzie:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Przyczyna

Bieżące ustawienia uprawnień nie zezwalają Oozie na personifikowanie określonego konta użytkownika.

### <a name="resolution"></a>Rozwiązanie

Oozie może personifikować użytkowników w **`users`** grupie. Użyj, `groups USERNAME` Aby wyświetlić grupy, do których należy konto użytkownika. Jeśli użytkownik nie jest członkiem **`users`** grupy, użyj następującego polecenia, aby dodać użytkownika do grupy:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Usługa HDInsight może potrwać kilka minut, ponieważ użytkownik dodaliśmy do grupy.

---

## <a name="launcher-error-sqoop"></a>BŁĄD uruchamiania (Sqoop)

### <a name="issue"></a>Problem

Stan zadania zmieni się na **zabity** . Szczegóły zadania przedstawiają `RunSqoopExport` stan jako **błąd** . W przypadku wybrania akcji zostanie wyświetlony następujący komunikat o błędzie:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Przyczyna

Sqoop nie może załadować sterownika bazy danych wymaganego w celu uzyskania dostępu do bazy danych.

### <a name="resolution"></a>Rozwiązanie

W przypadku korzystania z Sqoop z zadania Oozie należy uwzględnić sterownik bazy danych z innymi zasobami, takimi jak workflow.xml, zadanie używa. Odwołuje się również do archiwum zawierającego sterownik bazy danych z `<sqoop>...</sqoop>` sekcji workflow.xml.

Na przykład dla przykładowego zadania [korzystającego z przepływów pracy usługi Hadoop Oozie](hdinsight-use-oozie-linux-mac.md)należy wykonać następujące czynności:

1. Skopiuj `mssql-jdbc-7.0.0.jre8.jar` plik do katalogu **/Tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Zmodyfikuj, `workflow.xml` Aby dodać następujący kod XML w nowym wierszu powyżej `</sqoop>` :

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).