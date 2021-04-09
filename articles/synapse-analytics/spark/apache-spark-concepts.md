---
title: Podstawowe pojęcia Apache Spark
description: Wprowadzenie do podstawowych pojęć dotyczących Apache Spark w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120312"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark w podstawowych pojęciach dotyczących usługi Azure Synapse Analytics

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Apache Spark w usłudze Azure Synapse Analytics jest jednym z implementacji Apache Spark w chmurze firmy Microsoft. 

Usługa Azure Synapse ułatwia tworzenie i Konfigurowanie funkcji platformy Spark na platformie Azure. Usługa Azure Synapse zapewnia inną implementację tych możliwości platformy Spark, które są udokumentowane w tym miejscu.

## <a name="spark-pools"></a>Pule platformy Spark

W Azure Portal zostanie utworzona Pula Apache Spark bezserwerowa. Jest to definicja puli platformy Spark, która podczas tworzenia wystąpienia jest używana do tworzenia wystąpienia platformy Spark, które przetwarza dane. Po utworzeniu puli platformy Spark istnieje ona tylko jako metadane i żadne zasoby nie są używane, nie są uruchamiane ani obciążane opłatami. Pula platformy Spark ma serię właściwości kontrolujących charakterystykę wystąpienia platformy Spark. Te cechy obejmują, ale nie są ograniczone do nazwy, rozmiaru, zachowania skalowania, czasu wygaśnięcia.

Ponieważ nie ma dolarów ani kosztu zasobów związanych z tworzeniem pul platformy Spark, można utworzyć dowolną liczbę z dowolną liczbą różnych konfiguracji. Uprawnienia mogą być również stosowane do pul platformy Spark, co pozwala użytkownikom tylko na dostęp do niektórych i innych osób.

Najlepszym rozwiązaniem jest utworzenie mniejszych pul platformy Spark, które mogą być używane do programowania i debugowania, a następnie większych do uruchamiania obciążeń produkcyjnych.

Możesz zapoznać się z artykułem jak utworzyć pulę platformy Spark i zobaczyć wszystkie ich właściwości tutaj, aby rozpocząć [pracę z pulami platformy Spark w usłudze Azure Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

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

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Przydziały i ograniczenia zasobów w Apache Spark dla usługi Azure Synapse

### <a name="workspace-level"></a>Poziom obszaru roboczego

Każdy obszar roboczy usługi Azure Synapse ma domyślny limit przydziału rdzeni wirtualnych, który może być używany przez platformę Spark. Przydział jest dzielony między limitem przydziału użytkownika a limitem przydziału przepływu danych, tak aby żaden wzorzec użycia nie korzystał ze wszystkich rdzeni wirtualnych w obszarze roboczym. Przydział jest różny w zależności od typu subskrypcji, ale jest symetryczny między użytkownikiem i przepływu danych. Jeśli jednak zażądasz więcej rdzeni wirtualnych niż pozostało w obszarze roboczym, zostanie wyświetlony następujący błąd:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

Łącze w wiadomości wskazuje ten artykuł.

W tym artykule opisano sposób żądania wzrostu przydziału rdzeń wirtualny obszaru roboczego.

- Wybierz pozycję "Azure Synapse Analytics" jako typ usługi.
- W oknie Szczegóły przydziału wybierz pozycję Apache Spark (rdzeń wirtualny) na obszar roboczy

[Zażądaj zwiększenia pojemności za pośrednictwem Azure Portal](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Poziom puli platformy Spark

Podczas definiowania puli platformy Spark można skutecznie definiować przydziały dla poszczególnych użytkowników dla tej puli, jeśli uruchomisz wiele notesów lub zadań albo mieszany 2, możliwe jest wyczerpanie limitu przydziału puli. W takim przypadku zostanie wygenerowany komunikat o błędzie podobny do następującego:

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Aby rozwiązać ten problem, należy zmniejszyć użycie zasobów puli przed przesłaniem nowego żądania zasobu przez uruchomienie notesu lub zadania.

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../index.yml)
- [Dokumentacja Apache Spark](https://spark.apache.org/docs/2.4.5/)