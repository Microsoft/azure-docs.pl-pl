---
title: Automatyczne skalowanie wystąpień usługi Azure Synapse Apache Spark
description: Automatyczne skalowanie wystąpień Apache Spark przy użyciu funkcji automatycznego skalowania usługi Azure Synapse
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 6a627dfed3886a99a458a67d793c3ebbbdd0fb19
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644867"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Automatyczne skalowanie pul Apache Spark usługi Azure Synapse Analytics

Apache Spark funkcji automatycznego skalowania puli usługi Azure Synapse Analytics automatycznie skaluje liczbę węzłów w wystąpieniu klastra w górę i w dół. Podczas tworzenia nowego Apache Spark dla puli usługi Azure Synapse Analytics można ustawić minimalną i maksymalną liczbę węzłów, gdy jest zaznaczone automatyczne skalowanie. Funkcja automatycznego skalowania monitoruje następnie wymagania dotyczące zasobów obciążenia i skaluje liczbę węzłów w górę lub w dół. Ta funkcja nie ma dodatkowych opłat.

## <a name="metrics-monitoring"></a>Monitorowanie metryk

Automatyczne skalowanie w sposób ciągły monitoruje wystąpienie platformy Spark i zbiera następujące metryki:

|Metric|Opis|
|---|---|
|Łączny czas oczekiwania na procesor|Łączna liczba rdzeni wymaganych do rozpoczęcia wykonywania wszystkich oczekujących węzłów.|
|Całkowita liczba oczekujących pamięci|Całkowita ilość pamięci (w MB) wymagana do uruchomienia wszystkich oczekujących węzłów.|
|Łączny bezpłatny procesor CPU|Suma wszystkich nieużywanych rdzeni na aktywnych węzłach.|
|Całkowita ilość wolnej pamięci|Suma nieużywanej pamięci (w MB) na aktywnych węzłach.|
|Użyta pamięć na węzeł|Obciążenie węzła. Węzeł, na którym jest używana 10 GB pamięci, jest uznawany za większy niż proces roboczy z 2 GB używanej pamięci.|

Powyższe metryki są sprawdzane co 30 sekund. Funkcja automatycznego skalowania umożliwia skalowanie w górę i w dół w oparciu o te metryki.

## <a name="load-based-scale-conditions"></a>Warunki skalowania na podstawie obciążenia

Po wykryciu następujących warunków funkcja automatycznego skalowania wystawia żądanie skalowania:

|Skalowanie w górę|Skalowanie w dół|
|---|---|
|Łączny czas oczekiwania procesora CPU jest większy niż całkowity bezpłatny procesor CPU przez więcej niż 1 minutę.|Całkowita liczba oczekujących procesorów CPU jest mniejsza niż całkowity bezpłatny procesor CPU przez więcej niż 2 minuty.|
|Całkowita liczba oczekujących pamięci jest większa niż całkowita ilość wolnej pamięci przez więcej niż 1 minutę.|Całkowita ilość oczekujących pamięci jest mniejsza niż całkowita ilość wolnej pamięci przez więcej niż 2 minuty.|

W przypadku skalowania w górę usługa Azure Synapse Skalowanie automatyczne oblicza liczbę nowych węzłów potrzebnych do spełnienia bieżących wymagań procesora i pamięci, a następnie wystawia żądanie skalowania w celu dodania wymaganej liczby węzłów.

W przypadku skalowania w dół w oparciu o liczbę modułów wykonujących, wzorce aplikacji na węzeł i bieżące wymagania dotyczące procesora CPU i pamięci, automatyczne skalowanie wystawia żądanie usunięcia pewnej liczby węzłów. Usługa wykrywa również, które węzły są kandydatami do usunięcia na podstawie bieżącego wykonywania zadania. Operacja skalowania w dół najpierw likwidowanie węzłów, a następnie usunięcie ich z klastra.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="create-a-spark-pool-with-autoscaling"></a>Tworzenie puli platformy Spark z funkcją automatycznego skalowania

Aby włączyć funkcję automatycznego skalowania, wykonaj następujące czynności w ramach procesu tworzenia puli normalnej:

1. Na karcie **podstawy** zaznacz pole wyboru **Włącz automatyczne skalowanie** .
1. Wprowadź odpowiednie wartości dla następujących właściwości:  

    * **Minimalna** liczba węzłów.
    * **Maksymalna** liczba węzłów.

Początkowa liczba węzłów będzie minimalną. Ta wartość definiuje początkowy rozmiar wystąpienia podczas jego tworzenia. Minimalna liczba węzłów nie może być mniejsza niż trzy.

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Rozważ opóźnienie operacji skalowania w górę lub w dół

Ukończenie operacji skalowania może potrwać od 1 do 5 minut.

### <a name="preparation-for-scaling-down"></a>Przygotowanie do skalowania w dół

Podczas skalowania w poziomie w dół funkcja automatycznego skalowania umieści węzły w stanie likwidowania, aby nie można było uruchamiać nowych modułów wykonujących w tym węźle.

Uruchomione zadania będą nadal wykonywane i kończone. Oczekujące zadania będą oczekiwać na zaplanowanie jako normalne z mniejszą liczbą dostępnych węzłów.

## <a name="next-steps"></a>Następne kroki

Przewodnik Szybki Start dotyczący konfigurowania nowej puli platformy Spark [Tworzenie puli platformy Spark](../quickstart-create-apache-spark-pool-portal.md)
