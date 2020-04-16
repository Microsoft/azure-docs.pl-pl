---
title: Platforma Iskrowa Apache w usłudze Azure Synapse Analytics — podstawowe pojęcia
description: Ten artykuł zawiera wprowadzenie do platformy Apache Spark w usłudze Azure Synapse Analytics i różnych pojęć.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3cf654e77bf68c5194a0213d4452242b5c44e234
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423670"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark w podstawowe koncepcje analizy usługi Azure Synapse Analytics

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Apache Spark w usłudze Azure Synapse Analytics jest jedną z implementacji platformy Microsoft apache Spark w chmurze. 

Usługa Azure Synapse ułatwia tworzenie i konfigurowanie funkcji platformy Spark na platformie Azure. Usługa Azure Synapse udostępnia inną implementację tych funkcji platformy Spark, które są udokumentowane w tym miejscu.

## <a name="spark-pools-preview"></a>Baseny iskrowe (wersja zapoznawcza)

Pula platformy Spark (wersja zapoznawcza) jest tworzona w witrynie Azure portal. Jest to definicja puli platformy Spark, która po utworzeniu wystąpienia jest używana do tworzenia wystąpienia platformy Spark, które przetwarza dane. Po utworzeniu puli platformy Spark istnieje ona tylko jako metadane; żadne zasoby nie są zużywane, uruchomione ani naliczane. A Spark puli ma szereg właściwości, które kontrolują właściwości Spark wystąpienia; te cechy obejmują, ale nie są ograniczone do nazwy, rozmiar, zachowanie skalowania, czas na żywo.

Ponieważ z tworzeniem pul platformy Spark nie ma żadnych kosztów dolara ani zasobu, można utworzyć dowolną liczbę z dowolną liczbą różnych konfiguracji. Uprawnienia mogą być również stosowane do pul platformy Spark, dzięki czemu użytkownicy mają dostęp tylko do niektórych, a nie innych.

Najlepszym rozwiązaniem jest utworzenie mniejszych pul platformy Spark, które mogą być używane do programowania i debugowania, a następnie większe dla uruchamiania obciążeń produkcyjnych.

Możesz przeczytać, jak utworzyć pulę platformy Spark i zobaczyć wszystkie ich właściwości tutaj [Wprowadzenie do puli platformy Spark w usłudze Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md#create-an-apache-spark-pool)

## <a name="spark-instances"></a>Instancje iskry

Wystąpienia platformy Spark są tworzone podczas łączenia się z pulą platformy Spark, tworzenia sesji i uruchamiania zadania. Ponieważ wielu użytkowników może mieć dostęp do jednej puli platformy Spark, dla każdego użytkownika, który łączy się, tworzone jest nowe wystąpienie platformy Spark. 

Po przesłaniu drugiego zadania, a następnie jeśli istnieje pojemność w puli, istniejące wystąpienie Platformy Spark ma również pojemność, a następnie istniejące wystąpienie będzie przetwarzać zadanie; jeśli nie i istnieje pojemność na poziomie puli, zostanie utworzone nowe wystąpienie platformy Spark.

## <a name="examples"></a>Przykłady

### <a name="example-1"></a>Przykład 1

- Utworzysz pulę platformy Spark o nazwie SP1; ma stały rozmiar klastra 20 węzłów.
- Prześlij zadanie notesu, J1, który używa 10 węzłów, Spark wystąpienie, SI1 jest tworzony do przetwarzania zadania.
- Teraz prześlij inne zadanie, J2, który używa 10 węzłów, ponieważ nadal istnieje pojemność w puli i wystąpienie, J2, jest przetwarzany przez SI1.
- Gdyby J2 poprosił o 11 węzłów, nie byłoby pojemności w dodatku SP1 lub SI1. W takim przypadku, jeśli J2 pochodzi z notesu, zadanie zostanie odrzucone; jeśli J2 pochodzi z zadania wsadowego, a następnie będzie w kolejce.

### <a name="example-2"></a>Przykład 2

- Tworzenie wywołania puli platformy Spark SP2; ma włączoną automatyczną skalę 10 – 20 węzłów
- Prześlij zadanie notesu, J1, który używa 10 węzłów, Spark wystąpienie, SI1, jest tworzony do przetwarzania zadania.
- Teraz prześlij inne zadanie, J2, który używa 10 węzłów, ponieważ nadal istnieje pojemność w puli auto wystąpienia rośnie do 20 węzłów i przetwarza J2.

### <a name="example-3"></a>Przykład 3

- Utworzysz pulę platformy Spark o nazwie SP1; ma stały rozmiar klastra 20 węzłów.
- Prześlij zadanie notesu, J1, który używa 10 węzłów, Spark wystąpienie, SI1 jest tworzony do przetwarzania zadania.
- Inny użytkownik, U2, przesyła zadanie, J3, który używa 10 węzłów, nowe wystąpienie platformy Spark, SI2, jest tworzony do przetwarzania zadania.
- Teraz prześlij inne zadanie, J2, który używa 10 węzłów, ponieważ nadal istnieje pojemność w puli i wystąpienie, J2, jest przetwarzany przez SI1.

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark Dokumentacja](https://spark.apache.org/docs/2.4.4/)
