---
title: Trwa powolne działanie usługi Azure HDInsight
description: Zmniejszenie ilości danych w usłudze Azure HDInsight odbywa się powoli
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930815"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]