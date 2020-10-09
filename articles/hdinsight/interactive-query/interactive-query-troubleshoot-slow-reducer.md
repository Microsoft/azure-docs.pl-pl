---
title: Trwa powolne działanie usługi Azure HDInsight
description: Zmniejszenie ilości danych w usłudze Azure HDInsight odbywa się powoli
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895163"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenariusz: skrócenie w usłudze Azure HDInsight jest powolne

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas wykonywania zapytania, takiego jak `insert into table1 partition(a,b) select a,b,c from table2` Plan zapytania, uruchamia wiele elementów ograniczających, ale dane z poszczególnych partycji trafiają do pojedynczego ograniczenia. Powoduje to, że zapytanie działa tak długo, jak długo przez największe zmniejszenie partycji.

## <a name="cause"></a>Przyczyna

Otwórz [z usługi Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) i sprawdź wartość ustawienia `hive.optimize.sort.dynamic.partition` .

Wartość tej zmiennej ma być ustawiona na wartość true/false na podstawie charakteru danych.

Jeśli partycje w tabeli wejściowej są mniejsze (wymawiają mniej niż 10), więc jest to liczba partycji wyjściowych, a zmienna jest ustawiona na `true` , spowoduje to, że dane mają być sortowane globalnie i napisane przy użyciu jednego ograniczenia na partycję. Nawet jeśli liczba dostępnych elementów zmniejszających jest większa, niektóre ograniczenia mogą być opóźnione z powodu pochylenia danych i nie można osiągnąć maksymalnego równoległości. Po zmianie na `false` , więcej niż jedno ograniczenie może obsłużyć jedną partycję, a wiele mniejszych plików zostanie wypisanych, co spowoduje szybsze Wstawianie. Może to mieć wpływ na dalsze zapytania z powodu obecności mniejszych plików.

Wartość ma `true` sens, gdy liczba partycji jest większa, a dane nie są skośne. W takich przypadkach wynik fazy mapy zostanie zapisany w taki sposób, że każda partycja będzie obsługiwana przez pojedyncze zmniejszenie wydajności, co zwiększa wydajność kolejnych zapytań.

## <a name="resolution"></a>Rozwiązanie

1. Spróbuj ponownie podzielić dane na partycje, aby znormalizować je na wiele partycji.

1. Jeśli #1 nie jest możliwe, ustaw wartość konfiguracji na false w sesji z usługi Beeline i ponów próbę wykonania zapytania. `set hive.optimize.sort.dynamic.partition=false`. Ustawienie wartości false na poziomie klastra nie jest zalecane. Wartość `true` jest optymalna i ustawia parametr jako wymagany na podstawie charakteru danych i zapytania.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
