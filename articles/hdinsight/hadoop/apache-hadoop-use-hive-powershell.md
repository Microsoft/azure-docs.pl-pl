---
title: Używanie Apache Hive z programem PowerShell w usłudze HDInsight — Azure
description: Uruchamianie zapytań Apache Hive w usłudze Apache Hadoop w usłudze Azure HDInsight przy użyciu programu PowerShell
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 43cac81717027b39210d6cd9cb9ad0baa596f50d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943178"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Uruchamianie zapytań Apache Hive przy użyciu programu PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ten dokument zawiera przykład użycia Azure PowerShell do uruchamiania zapytań Apache Hive w Apache Hadoop w klastrze usługi HDInsight.

> [!NOTE]  
> Ten dokument nie zawiera szczegółowego opisu instrukcji HiveQL, które są używane w przykładach. Aby uzyskać informacje na temat HiveQL, który jest używany w tym przykładzie, zobacz [używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Program PowerShell [AZ module](/powershell/azure/) został zainstalowany.

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

Azure PowerShell udostępnia *polecenia cmdlet* , które umożliwiają zdalne uruchamianie zapytań Hive w usłudze HDInsight. Wewnętrznie polecenia cmdlet sprawiają, że wywołania REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) w klastrze usługi HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zapytań programu Hive w zdalnym klastrze usługi HDInsight:

* `Connect-AzAccount`: Uwierzytelnia Azure PowerShell w ramach subskrypcji platformy Azure.
* `New-AzHDInsightHiveJobDefinition`: Tworzy *definicję zadania* przy użyciu określonych instrukcji HiveQL.
* `Start-AzHDInsightJob`: Wysyła definicję zadania do usługi HDInsight i uruchamia zadanie. Obiekt *zadania* jest zwracany.
* `Wait-AzHDInsightJob`: Używa obiektu zadanie do sprawdzenia stanu zadania. Czeka na zakończenie zadania lub przekroczenie czasu oczekiwania.
* `Get-AzHDInsightJobOutput`: Służy do pobierania danych wyjściowych zadania.
* `Invoke-AzHDInsightHiveJob`: Służy do uruchamiania instrukcji HiveQL. To polecenie cmdlet blokuje wykonywanie zapytania, a następnie zwraca wyniki.
* `Use-AzHDInsightCluster`: Ustawia bieżący klaster do użycia dla `Invoke-AzHDInsightHiveJob` polecenia.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet do uruchamiania zadania w klastrze usługi HDInsight:

1. Za pomocą edytora Zapisz Poniższy kod jako `hivejob.ps1` .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Otwórz nowy wiersz polecenia **Azure PowerShell** . Zmień katalogi na lokalizację `hivejob.ps1` pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

    ```azurepowershell
    .\hivejob.ps1
    ```

    Po uruchomieniu skryptu zostanie wyświetlony monit o wprowadzenie nazwy klastra i poświadczeń konta administratora protokołu HTTPS/klastra. Może również zostać wyświetlony monit o zalogowanie się do subskrypcji platformy Azure.

3. Po zakończeniu zadania zwraca informacje podobne do następującego tekstu:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Jak wspomniano wcześniej, `Invoke-Hive` można użyć do uruchomienia zapytania i poczekać na odpowiedź. Użyj następującego skryptu, aby zobaczyć, jak działa Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Dane wyjściowe wyglądają następująco:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Aby uzyskać dłuższe zapytania HiveQL, można użyć polecenia cmdlet Azure PowerShell **tutaj-String** lub plików skryptów HiveQL. Poniższy fragment kodu przedstawia sposób użycia `Invoke-Hive` polecenia cmdlet do uruchamiania pliku skryptu HiveQL. Plik skryptu HiveQL musi zostać przekazany do wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Aby uzyskać więcej informacji na temat **ciągów**, zobacz [tutaj](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli po zakończeniu zadania nie zostaną zwrócone żadne informacje, wyświetl dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, Dodaj następujący element na końcu `hivejob.ps1` pliku, Zapisz go, a następnie uruchom ponownie.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje zapisywane do STDERR podczas przetwarzania zadania.

## <a name="summary"></a>Podsumowanie

Jak widać, Azure PowerShell zapewnia łatwy sposób uruchamiania zapytań programu Hive w klastrze usługi HDInsight, monitorowania stanu zadania i pobierania danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat usługi Hive w usłudze HDInsight:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
