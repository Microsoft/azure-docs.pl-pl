---
title: Używanie MapReduce i programu PowerShell z usługą Apache Hadoop Azure HDInsight
description: Dowiedz się, jak używać programu PowerShell do zdalnego uruchamiania Apache Hadoop zadań MapReduce w usłudze HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939668"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Uruchamianie zadań MapReduce w Apache Hadoop usłudze HDInsight przy użyciu programu PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ten dokument zawiera przykład użycia Azure PowerShell do uruchamiania zadania MapReduce w klastrze usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* Program PowerShell [AZ module](/powershell/azure/) został zainstalowany.

## <a name="run-a-mapreduce-job"></a>Uruchamianie zadania MapReduce

Azure PowerShell udostępnia *polecenia cmdlet* , które umożliwiają zdalne uruchamianie zadań MapReduce w usłudze HDInsight. Wewnętrznie program PowerShell tworzy wywołania REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dawniej Templeton) uruchomionego w klastrze usługi HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zadań MapReduce w zdalnym klastrze usługi HDInsight.

|Polecenie cmdlet | Opis |
|---|---|
|Connect-AzAccount|Uwierzytelnia Azure PowerShell w ramach subskrypcji platformy Azure.|
|New-AzHDInsightMapReduceJobDefinition|Tworzy nową *definicję zadania* przy użyciu określonych informacji MapReduce.|
|Start-AzHDInsightJob|Wysyła definicję zadania do usługi HDInsight i uruchamia zadanie. Obiekt *zadania* jest zwracany.|
|Wait-AzHDInsightJob|Używa obiektu zadanie do sprawdzenia stanu zadania. Czeka na zakończenie zadania lub przekroczenie czasu oczekiwania.|
|Get-AzHDInsightJobOutput|Służy do pobierania danych wyjściowych zadania.|

Poniższe kroki pokazują, jak używać tych poleceń cmdlet do uruchamiania zadania w klastrze usługi HDInsight.

1. Za pomocą edytora Zapisz Poniższy kod jako **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Otwórz nowy wiersz polecenia **Azure PowerShell** . Zmień katalogi na lokalizację pliku **mapreducejob.ps1** , a następnie użyj następującego polecenia, aby uruchomić skrypt:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Po uruchomieniu skryptu zostanie wyświetlony monit o podanie nazwy klastra usługi HDInsight i logowania do klastra. Może być również wyświetlony monit o uwierzytelnienie w ramach subskrypcji platformy Azure.

3. Po zakończeniu zadania otrzymujesz dane wyjściowe podobne do następującego tekstu:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Dane wyjściowe wskazują, że zadanie zostało ukończone pomyślnie.

    > [!NOTE]  
    > Jeśli **ExitCode** jest wartością inną niż 0, zobacz [Rozwiązywanie problemów](#troubleshooting).

    W tym przykładzie przechowywane są również pobrane pliki do pliku **output.txt** w katalogu, w którym uruchamiany jest skrypt.

### <a name="view-output"></a>Wyświetl dane wyjściowe

Aby zobaczyć słowa i liczby wygenerowane przez zadanie, Otwórz plik **output.txt** w edytorze tekstów.

> [!NOTE]  
> Pliki wyjściowe zadania MapReduce są niezmienne. Dlatego w przypadku ponownego uruchomienia tego przykładu należy zmienić nazwę pliku wyjściowego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli po zakończeniu zadania nie zostaną zwrócone żadne informacje, Wyświetl błędy dla tego zadania. Aby wyświetlić informacje o błędzie dla tego zadania, Dodaj następujące polecenie na końcu pliku **mapreducejob.ps1** . Następnie Zapisz plik i ponownie uruchom skrypt.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje, które zostały zapisaną w STDERR, gdy zadanie zostanie uruchomione.

## <a name="next-steps"></a>Następne kroki

Jak widać, Azure PowerShell zapewnia łatwy sposób uruchamiania zadań MapReduce w klastrze usługi HDInsight, monitorowania stanu zadania i pobierania danych wyjściowych. Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Korzystanie z MapReduce w usłudze HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)