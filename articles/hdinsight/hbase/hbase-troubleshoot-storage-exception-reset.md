---
title: Wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight
description: Wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936750"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenariusz: wyjątek magazynu po zresetowaniu połączenia w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można utworzyć nowej tabeli Apache HBase.

## <a name="cause"></a>Przyczyna

Podczas procesu obcięcia tabeli wystąpił problem z połączeniem magazynu. Wpis tabeli został usunięty w tabeli metadanych HBase. Wszystkie pliki obiektów blob, ale zostały usunięte.

Mimo że w magazynie nie znaleziono obiektu BLOB `/hbase/data/default/ThatTable` w folderze. Sterownik WASB znalazł obecność powyżej pliku obiektu BLOB i nie zezwolił na utworzenie obiektu BLOB o nazwie `/hbase/data/default/ThatTable` , ponieważ założono, że foldery nadrzędne istniały, w związku z czym Tworzenie tabeli zakończy się niepowodzeniem.

## <a name="resolution"></a>Rozwiązanie

1. W interfejsie użytkownika Apache Ambari Uruchom ponownie aktywne serwera hmaster. Dzięki temu jedna z dwóch serwera hmaster stanu wstrzymania stanie się aktywna, a nowa aktywna serwera hmaster będzie ponownie ładować informacje tabeli metadanych. Oznacza to, że tabela nie zostanie wyświetlona `already-deleted` w interfejsie użytkownika serwera hmaster.

1. Oddzielony plik obiektów BLOB można znaleźć w narzędziach interfejsu użytkownika, takich jak Eksplorator chmury lub uruchomione polecenie takie jak `hdfs dfs -ls /xxxxxx/yyyyy` . Uruchom `hdfs dfs -rmr /xxxxx/yyyy` , aby usunąć ten obiekt BLOB. Na przykład `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Teraz można utworzyć nową tabelę o tej samej nazwie w HBase.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).