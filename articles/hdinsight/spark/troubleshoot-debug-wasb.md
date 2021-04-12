---
title: Debugowanie operacji na plikach WASB w usłudze Azure HDInsight
description: Opisuje kroki rozwiązywania problemów i możliwe rozwiązania problemów występujących podczas pracy z klastrami usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938742"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Debugowanie operacji na plikach WASB w usłudze Azure HDInsight

Istnieją przypadki, w których można zrozumieć, jakie operacje został uruchomiony przez sterownik WASB w usłudze Azure Storage. Po stronie klienta sterownik WASB tworzy dzienniki dla każdej operacji systemu plików na poziomie **debugowania** . Sterownik WASB używa Log4J do kontrolowania poziomu rejestrowania i domyślnego poziomu **informacji** . W przypadku dzienników analitycznych po stronie serwera usługi Azure Storage zobacz [Rejestrowanie analizy usługi Azure Storage](../../storage/common/storage-analytics-logging.md).

Wygenerowany dziennik będzie wyglądać podobnie do:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Włącz Dziennik debugowania WASB dla operacji na plikach

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra Spark.

1. Przejdź do **zaawansowanej spark2-Log4J-Properties**.

    1. Modyfikuj `log4j.appender.console.Threshold=INFO` na `log4j.appender.console.Threshold=DEBUG` .

    1. Dodaj `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Przejdź do **zaawansowanej livy2-Log4J-Properties**.

    Dodaj `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Zapisz zmiany.

## <a name="additional-logging"></a>Dodatkowe rejestrowanie

Powyższe dzienniki powinny zapewnić ogólne zrozumienie operacji systemu plików. Jeśli powyższe dzienniki nadal nie udostępniają użytecznych informacji lub jeśli chcesz zbadać wywołania interfejsu API usługi BLOB Storage, Dodaj `fs.azure.storage.client.logging=true` je do programu `core-site` . To ustawienie spowoduje włączenie dzienników zestawu SDK języka Java dla sterownika magazynu wasb i wydrukowanie każdego wywołania do serwera usługi BLOB Storage. Usuń ustawienie po zbadaniu, ponieważ może ono szybko zapełniać dysk i może spowolnić proces.

Jeśli zaplecze jest oparty Azure Data Lake, użyj następującego ustawienia Log4J dla składnika (na przykład Spark/tez/HDFS):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Zapoznaj się z dziennikami dzienników `/var/log/adl/adl.log` .

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).