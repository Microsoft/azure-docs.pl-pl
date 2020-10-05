---
title: Podstawowe pojęcia Apache Spark
description: Wprowadzenie do Apache Spark usługi Azure Synapse Analytics i różnych koncepcji.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 74e85906742207d6cde0b7c4cc5c021c23ee4c7b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260142"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark w podstawowych pojęciach dotyczących usługi Azure Synapse Analytics

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Apache Spark w usłudze Azure Synapse Analytics jest jednym z implementacji Apache Spark w chmurze firmy Microsoft. 

Usługa Azure Synapse ułatwia tworzenie i Konfigurowanie funkcji platformy Spark na platformie Azure. Usługa Azure Synapse zapewnia inną implementację tych możliwości platformy Spark, które są udokumentowane w tym miejscu.

## <a name="spark-pools-preview"></a>Pule Spark (wersja zapoznawcza)

W Azure Portal zostanie utworzona Pula Spark (wersja zapoznawcza). Jest to definicja puli platformy Spark, która podczas tworzenia wystąpienia jest używana do tworzenia wystąpienia platformy Spark, które przetwarza dane. Po utworzeniu puli platformy Spark istnieje ona tylko jako metadane i żadne zasoby nie są używane, nie są uruchamiane ani obciążane opłatami. Pula platformy Spark ma serię właściwości kontrolujących charakterystykę wystąpienia platformy Spark. Te cechy obejmują, ale nie są ograniczone do nazwy, rozmiaru, zachowania skalowania, czasu wygaśnięcia.

Ponieważ nie ma dolarów ani kosztu zasobów związanych z tworzeniem pul platformy Spark, można utworzyć dowolną liczbę z dowolną liczbą różnych konfiguracji. Uprawnienia mogą być również stosowane do pul platformy Spark, co pozwala użytkownikom tylko na dostęp do niektórych i innych osób.

Najlepszym rozwiązaniem jest utworzenie mniejszych pul platformy Spark, które mogą być używane do programowania i debugowania, a następnie większych do uruchamiania obciążeń produkcyjnych.

Możesz zapoznać się z artykułem jak utworzyć pulę platformy Spark i zobaczyć wszystkie ich właściwości tutaj, aby rozpocząć [pracę z pulami platformy Spark w Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Wystąpienia platformy Spark

Wystąpienia platformy Spark są tworzone podczas łączenia się z pulą platformy Spark, tworzenia sesji i uruchamiania zadania. Ponieważ wielu użytkowników może mieć dostęp do pojedynczej puli platformy Spark, dla każdego z nich zostanie utworzone nowe wystąpienie platformy Spark. 

W przypadku przesłania drugiego zadania, jeśli istnieje pojemność w puli, istniejące wystąpienie platformy Spark ma również pojemność. Następnie istniejące wystąpienie będzie przetwarzać zadanie. W przeciwnym razie, jeśli pojemność jest dostępna na poziomie puli, zostanie utworzone nowe wystąpienie platformy Spark.

## <a name="examples"></a>Przykłady

### <a name="example-1"></a>Przykład 1

- Tworzysz pulę platformy Spark o nazwie SP1; ma stały rozmiar klastra 20 węzłów.
- Przesyłasz zadanie notesu, J1, które używa 10 węzłów, wystąpienie platformy Spark, SI1 jest tworzone w celu przetworzenia zadania.
- Teraz przesyłamy kolejne zadanie, j2, które korzysta z 10 węzłów, ponieważ nadal istnieje pojemność puli i wystąpienia, a J2 jest przetwarzana przez SI1.
- Jeśli J2 zażądali 11 węzłów, nie było możliwości w programie SP1 ani SI1. W takim przypadku, jeśli J2 pochodzi z notesu, zadanie zostanie odrzucone. Jeśli J2 pochodzi z zadania usługi Batch, zostanie on umieszczony w kolejce.

### <a name="example-2"></a>Przykład 2

- Należy utworzyć wywołanie puli Spark z dodatkiem SP2. ma włączoną funkcję automatycznego skalowania o wielkości 10 – 20
- Przesyłasz zadanie notesu, J1, które używa 10 węzłów, wystąpienia Spark, SI1, jest tworzone w celu przetworzenia zadania.
- Teraz przesyłamy kolejne zadanie, j2, które korzysta z 10 węzłów, ponieważ nadal istnieje pojemność w puli, którą wystąpienie jest rozrastane do 20 węzłów i procesów J2.

### <a name="example-3"></a>Przykład 3

- Tworzysz pulę platformy Spark o nazwie SP1; ma stały rozmiar klastra 20 węzłów.
- Przesyłasz zadanie notesu, J1, które używa 10 węzłów, wystąpienie platformy Spark, SI1 jest tworzone w celu przetworzenia zadania.
- Inny użytkownik, U2, przesyła zadanie, J3, które używa 10 węzłów, nowe wystąpienie platformy Spark, SI2, zostało utworzone w celu przetworzenia zadania.
- Teraz przesyłamy kolejne zadanie, j2, które korzysta z 10 węzłów, ponieważ nadal pojemność puli i wystąpienie, j2, jest przetwarzana przez SI1.

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentacja Apache Spark](https://spark.apache.org/docs/2.4.4/)
