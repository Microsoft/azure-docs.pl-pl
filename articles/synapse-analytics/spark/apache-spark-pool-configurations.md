---
title: Koncepcje puli Apache Spark
description: Wprowadzenie do Apache Spark rozmiaru puli i konfiguracji w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: e68b8aff700519b6bef31e5126c91fa8bc4a3593
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202560"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Konfiguracje puli Apache Spark w usłudze Azure Synapse Analytics

Pula platformy Spark to zestaw metadanych, który definiuje wymagania dotyczące zasobów obliczeniowych i powiązane cechy zachowania podczas tworzenia wystąpienia platformy Spark. Te cechy obejmują, ale nie są ograniczone do nazwy, liczby węzłów, rozmiaru węzła, zachowania skalowania i czasu na żywo. Pula Spark nie korzysta z żadnych zasobów. Nie są naliczane żadne koszty związane z tworzeniem pul platformy Spark. Opłaty są naliczane tylko po wykonaniu zadania Spark w docelowej puli Spark i wystąpieniu wystąpienia platformy Spark na żądanie.

Możesz zapoznać się z artykułem jak utworzyć pulę platformy Spark i zobaczyć wszystkie ich właściwości tutaj, aby rozpocząć [pracę z pulami platformy Spark w Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="nodes"></a>Węzły

Wystąpienie puli Apache Spark składa się z jednego węzła głównego i dwóch lub więcej węzłów procesu roboczego z co najmniej trzema węzłami w wystąpieniu platformy Spark.  Węzeł główny uruchamia dodatkowe usługi zarządzania, takie jak usługi Livy, przędze Menedżer zasobów, dozorcy i sterownik Spark.  Wszystkie węzły uruchamiają usługi takie jak Agent węzła i Menedżer węzłów przędzenia. Wszystkie węzły procesu roboczego uruchamiają usługę programu wykonującego Spark.

## <a name="node-sizes"></a>Rozmiary węzłów

Pulę platformy Spark można zdefiniować przy użyciu rozmiarów węzłów, które obejmują zakres od małego węzła obliczeniowego z 8 rdzeń wirtualny i 64 GB pamięci do węzła obliczeniowego XXLarge z 64 Rdzeń wirtualny i 432 GB pamięci na węzeł. Rozmiary węzłów można zmienić po utworzeniu puli, chociaż może być konieczne ponowne uruchomienie tego wystąpienia.

|Rozmiar | Rdzenie wirtualne | Pamięć|
|-----|------|-------|
|Mały|4|32 GB|
|Śred.|8|64 GB|
|Duży|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Automatyczne skalowanie

Pule Apache Spark zapewniają możliwość automatycznego skalowania zasobów obliczeniowych w górę i w dół na podstawie ilości aktywności.  Po włączeniu funkcji automatycznego skalowania można ustawić minimalną i maksymalną liczbę węzłów do skalowania.
Gdy funkcja automatycznego skalowania jest wyłączona, liczba ustawionych węzłów pozostanie stała.  To ustawienie można zmienić po utworzeniu puli, chociaż może być konieczne ponowne uruchomienie wystąpienia.

## <a name="automatic-pause"></a>Automatyczne wstrzymywanie

Funkcja automatycznego wstrzymania zwalnia zasoby po upływie określonego czasu bezczynności, zmniejszając całkowity koszt puli Apache Spark.  Liczba minut czasu bezczynności, które można ustawić po włączeniu tej funkcji.  Funkcja automatycznego wstrzymania jest niezależna od funkcji Skalowanie automatyczne. Zasoby można wstrzymywać niezależnie od tego, czy Skalowanie automatyczne jest włączone, czy wyłączone.  To ustawienie można zmienić po utworzeniu puli, chociaż może być konieczne ponowne uruchomienie wystąpienia.

## <a name="next-steps"></a>Następne kroki

* [Azure Synapse Analytics](/azure/synapse-analytics)
* [Dokumentacja Apache Spark](https://spark.apache.org/docs/2.4.5/)
