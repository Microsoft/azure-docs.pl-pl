---
title: Rozwiązywanie problemów z programem Apache Oozie w usłudze Azure HDInsight
description: Rozwiązywanie niektórych błędów usługi Apache Oozie w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: edbe5274de8576fccb29e1e69d260a6531d4ab05
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287397"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Rozwiązywanie problemów z programem Apache Oozie w usłudze Azure HDInsight

Za pomocą interfejsu użytkownika Apache Oozie można wyświetlać dzienniki Oozie. Interfejs użytkownika Oozie zawiera również linki do dzienników JobTracker dla zadań MapReduce, które zostały uruchomione w ramach przepływu pracy. Wzorzec do rozwiązywania problemów powinien być:

1. Wyświetl zadanie w interfejsie użytkownika sieci Web Oozie.

2. Jeśli wystąpi błąd lub błąd konkretnej akcji, wybierz akcję, aby zobaczyć, czy pole **komunikatu o błędzie** zawiera więcej informacji na temat błędu.

3. Jeśli jest dostępna, użyj adresu URL z akcji, aby wyświetlić więcej szczegółów, takich jak dzienniki JobTracker, dla akcji.

Poniżej znajdują się konkretne błędy, które można napotkać i sposoby ich rozwiązywania.

## <a name="ja009-cant-initialize-cluster"></a>JA009: nie można zainicjować klastra

### <a name="issue"></a>Problem

Stan zadania zostanie zmieniony na **zawieszone**. Szczegóły zadania zawierają `RunHiveScript` stan **START_MANUAL**. Po wybraniu akcji zostanie wyświetlony następujący komunikat o błędzie:

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

Stan zadania zostanie zmieniony na **zawieszone**. Szczegóły zadania zawierają `RunHiveScript` stan **START_MANUAL**. W przypadku wybrania akcji zostanie wyświetlony następujący komunikat o błędzie:

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

Stan zadania zmieni się na **zabity**. Szczegóły zadania przedstawiają `RunSqoopExport` stan jako **błąd**. W przypadku wybrania akcji zostanie wyświetlony następujący komunikat o błędzie:

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

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]