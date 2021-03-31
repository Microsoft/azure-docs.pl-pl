---
title: Określ metryki i ustawienia umieszczania
description: Dowiedz się, jak opisać usługę Service Fabric, określając metryki, ograniczenia umieszczania i inne zasady umieszczania.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610101"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurowanie ustawień Menedżera zasobów klastra dla usług Service Fabric Services
Klaster Service Fabric Menedżer zasobów umożliwia precyzyjne sterowanie regułami, które regulują każdą konkretną nazwę usługi. Każda nazwana usługa może określać reguły, które powinny być przydzieloną w klastrze. Każda nazwana usługa może również definiować zestaw metryk, które chce zgłosić, w tym ich znaczenie dla tej usługi. Konfigurowanie usług dzieli się na trzy różne zadania:

1. Konfigurowanie ograniczeń umieszczania
2. Konfigurowanie metryk
3. Konfigurowanie zaawansowanych zasad umieszczania i innych reguł (mniej typowe)

## <a name="placement-constraints"></a>Ograniczenia umieszczania
Ograniczenia umieszczania są używane do kontrolowania, które węzły w klastrze mogą być w rzeczywistości uruchomione. Zwykle określone wystąpienie usługi o nazwie lub wszystkie usługi danego typu są ograniczone do uruchamiania w określonym typie węzła. Ograniczenia umieszczania są rozszerzalne. Można zdefiniować dowolny zestaw właściwości dla każdego typu węzła, a następnie wybrać dla nich z ograniczeniami podczas tworzenia usług. Możesz również zmienić ograniczenia umieszczania usługi podczas działania. Pozwala to na reagowanie na zmiany w klastrze lub wymagania usługi. Właściwości danego węzła można także aktualizować dynamicznie w klastrze. Więcej informacji o ograniczeniach umieszczania i sposobach ich konfigurowania można znaleźć w [tym artykule](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) .

## <a name="metrics"></a>Metryki
Metryki to zbiór zasobów, których nazwa jest wymagana przez daną nazwę usługi. Konfiguracja metryki usługi zawiera informacje o tym, ile zasobów każda replika stanowa lub bezstanowa ta usługa domyślnie korzysta. Metryki obejmują również wagę wskazującą, jak ważna jest usługa równoważenia obciążenia dla tej usługi, w przypadku gdy wymagane są kompromisy.

## <a name="advanced-placement-rules"></a>Zaawansowane reguły umieszczania
Istnieją inne typy reguł umieszczania, które są przydatne w mniej typowych scenariuszach. Przykłady to:
- Ograniczenia dotyczące klastrów rozproszonych geograficznie
- Niektóre architektury aplikacji

Inne reguły umieszczania są konfigurowane za pośrednictwem obu korelacji lub zasad.

## <a name="next-steps"></a>Następne kroki
- Metryki to sposób, w jaki Menedżer zasobów klastra Service Fabric zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o metrykach i sposobach ich konfigurowania, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)
- Koligacja to jeden tryb, który można skonfigurować dla usług. Nie jest ona powszechna, ale jeśli jest potrzebna, możesz dowiedzieć się [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Istnieje wiele różnych reguł umieszczania, które można skonfigurować w usłudze w celu obsługi dodatkowych scenariuszy. Informacje o tych różnych zasadach umieszczania można znaleźć [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Zacznij od początku i [zapoznaj się z wprowadzeniem do klastra Service Fabric Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md)
- Aby dowiedzieć się, w jaki sposób klaster Menedżer zasobów zarządza i równoważenia obciążenia w klastrze, zapoznaj się z artykułem dotyczącym [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Klaster Menedżer zasobów ma wiele opcji opisywania klastra. Aby dowiedzieć się więcej na ten temat, zapoznaj się z tym artykułem na temat [opisywania Service Fabric klastra](service-fabric-cluster-resource-manager-cluster-description.md)
