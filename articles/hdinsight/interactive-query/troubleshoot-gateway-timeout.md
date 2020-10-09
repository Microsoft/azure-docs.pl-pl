---
title: Wyjątek podczas uruchamiania zapytań z widoku Hive programu Apache Ambari w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów podczas uruchamiania zapytań Apache Hive za pomocą widoku Hive programu Apache Ambari w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895052"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Wyjątek podczas uruchamiania zapytań z widoku Hive programu Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

W przypadku uruchamiania zapytania Apache Hive z widoku programu Apache Ambari Hive pojawia się sporadycznie następujący komunikat o błędzie:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Przyczyna

Limit czasu bramy.

Wartość limitu czasu bramy wynosi 2 minuty. Zapytania z widoku programu Hive Ambari są przesyłane do `/hive2` punktu końcowego za pośrednictwem bramy. Po pomyślnym skompilowaniu i zaakceptowaniu zapytania HiveServer zwraca wartość `queryid` . Następnie klienci kontynuują sondowanie stanu zapytania. W trakcie tego procesu, jeśli HiveServer nie zwróci odpowiedzi HTTP w ciągu 2 minut, Brama HDI zgłasza błąd przekroczenia limitu czasu bramy 502,3 do obiektu wywołującego. Błędy mogą wystąpić, gdy zapytanie zostanie przesłane do przetwarzania (bardziej prawdopodobnie), a także w wywołaniu get status (mniej możliwe). Użytkownicy mogą zobaczyć jeden z nich.

Wątek obsługi http powinien być szybki: Przygotuj zadanie i zwróć `queryid` . Jednak ze względu na kilka powodów wszystkie wątki obsługi mogą być zajęte, co powoduje przekroczenie limitu czasu dla nowych zapytań i wywołań stanu pobierania.

### <a name="responsibilities-of-the-http-handler-thread"></a>Obowiązki wątku obsługi HTTP

Gdy klient przesyła zapytanie do HiveServer, wykonuje następujące czynności w wątku pierwszego planu:

* Przeanalizuj żądanie, wykonaj weryfikację semantyczną
* Uzyskaj blokadę
* Wyszukiwanie magazynu metadanych w razie potrzeby
* Kompiluj zapytanie (DDL lub DML)
* Przygotowywanie planu zapytania
* Wykonaj autoryzację (Uruchom wszystkie odpowiednie zasady Ranger w bezpiecznych klastrach)

## <a name="resolution"></a>Rozwiązanie

Niektóre ogólne zalecenia dotyczące poprawy sytuacji:

* W przypadku korzystania z zewnętrznego magazynu metadanych Hive należy sprawdzić metryki bazy danych i upewnić się, że nie jest przeciążona. Rozważ przeskalowanie warstwy bazy danych magazynu metadanych.

* Upewnij się, że równoległe operacje Ops są włączone (umożliwia równoległe uruchamianie wątków obsługi HTTP). Aby sprawdzić wartość, uruchom oprogramowanie [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) i przejdź do **Hive**  >  **Konfiguracja**Hive $  >  **Advanced**  >  **Custom Hive-site**. Wartość parametru for `hive.server2.parallel.ops.in.session` powinna być równa `true` .

* Upewnij się, że jednostka SKU maszyny wirtualnej klastra nie jest zbyt mała dla obciążenia. Rozważ podzielenie pracy między wieloma klastrami. Aby uzyskać więcej informacji, zobacz [Wybieranie typu klastra](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Jeśli Ranger jest zainstalowany w klastrze, sprawdź, czy istnieje zbyt wiele zasad Ranger, które należy ocenić dla każdego zapytania. Poszukaj zduplikowanych lub niepotrzebnych zasad.

* Sprawdź wartość **serwera hiveserver2 rozmiaru sterty** z Ambari. Przejdź do **Hive**  >  **Configs**  >  **optymalizacji ustawień**konfiguracji programu Hive  >  **Optimization**. Upewnij się, że wartość jest większa niż 10 GB. Dostosuj w miarę potrzeby, aby zoptymalizować wydajność.

* Upewnij się, że zapytanie programu Hive jest prawidłowo dopasowane. Aby uzyskać więcej informacji, zobacz [optymalizowanie Apache Hive zapytań w usłudze Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
