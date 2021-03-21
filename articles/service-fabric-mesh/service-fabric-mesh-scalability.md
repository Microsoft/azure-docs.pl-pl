---
title: Skalowalność aplikacji usługi Azure Service Fabric w sieci
description: Jedną z zalet wdrażania aplikacji do Service Fabric siatki jest możliwość łatwego skalowania usług — ręcznie lub za pomocą zasad skalowania automatycznego.
author: georgewallace
ms.author: gwallace
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3f0e115e596925878bf9fdd43b7074cefdba47b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626862"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalowanie aplikacji Service Fabric siatki

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Jedną z głównych zalet wdrażania aplikacji do Service Fabric siatki jest możliwość łatwego skalowania usług do lub wyprowadzenia. Ta usługa powinna być używana do obsługi różnych ilości obciążeń w usługach lub do zwiększenia dostępności. Możesz ręcznie skalować usługi w lub na zewnątrz lub skonfigurować zasady skalowania automatycznego.

## <a name="manual-scaling-instances"></a>Ręczne skalowanie wystąpień

W szablonie wdrażania dla zasobu aplikacji każda usługa ma właściwość *replicaCount*, która może służyć do ustawiania liczby wdrożeń usługi. Aplikacja może składać się z wielu usług, z których każda ma unikatową liczbę właściwości *replicaCount* oraz które są razem wdrażane i zarządzane. Aby skalować liczbę replik usług, zmodyfikuj wartość *replicaCount* dla każdej usługi, którą chcesz skalować, w szablonie wdrożenia lub pliku parametrów. Następnie uaktualnij aplikację.

Przykłady ręcznego skalowania wystąpień usług można znaleźć [w temacie ręczne skalowanie usług w programie lub na zewnątrz](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatyczne skalowanie wystąpień usługi
Skalowanie automatyczne jest dodatkową możliwością Service Fabric do dynamicznego skalowania liczby wystąpień usługi (skalowanie w poziomie). Skalowanie automatyczne zapewnia doskonałą elastyczność i umożliwia inicjowanie obsługi lub usuwanie wystąpień usługi na podstawie użycia procesora lub pamięci.  Funkcja automatycznego skalowania umożliwia uruchamianie odpowiedniej liczby wystąpień usługi dla obciążenia i optymalizację pod kątem kosztów.

Zasady automatycznego skalowania są definiowane dla każdej usługi w pliku zasobów usługi. Każda zasada skalowania składa się z dwóch części:

- Wyzwalacz skalowania, który opisuje, kiedy zostanie wykonane skalowanie usługi. Istnieją trzy czynniki, które określają, kiedy usługa będzie skalowana. *Dolny próg obciążenia* jest wartością, która określa, kiedy usługa będzie skalowana w programie. Jeśli średnie obciążenie wszystkich wystąpień partycji jest mniejsze niż ta wartość, usługa zostanie przeskalowana w poziomie. *Górny próg obciążenia* jest wartością, która określa, kiedy usługa będzie skalowana w poziomie. Jeśli średnie obciążenie wszystkich wystąpień partycji jest wyższe niż ta wartość, usługa zostanie przeskalowana w poziomie. *Interwał skalowania* określa, jak często (w sekundach) zostanie sprawdzony wyzwalacz. Po sprawdzeniu wyzwalacza, jeśli jest wymagany, mechanizm zostanie zastosowany. Jeśli skalowanie nie jest wymagane, nie zostanie podjęta żadna akcja. W obu przypadkach wyzwalacz nie zostanie ponownie sprawdzony przed upływem interwału skalowania.

- Mechanizm skalowania, który opisuje, jak skalowanie zostanie wykonane po jego wyzwoleniu. *Przyrost skali* określa liczbę wystąpień, które zostaną dodane lub usunięte, gdy mechanizm zostanie wyzwolony. *Maksymalna liczba wystąpień* definiuje górny limit skalowania. Jeśli liczba wystąpień osiągnie ten limit, usługa nie zostanie skalowana w poziomie niezależnie od obciążenia. *Minimalna liczba wystąpień* definiuje dolny limit skalowania. Jeśli liczba wystąpień partycji osiągnie ten limit, usługa nie będzie skalowana w przypadku, gdy jest to możliwe.

Aby dowiedzieć się, jak ustawić zasady automatycznego skalowania dla usługi, Przeczytaj [usługi skalowania automatycznego](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Następne kroki

Informacje o modelu aplikacji można znaleźć w temacie [Service Fabric Resources](service-fabric-mesh-service-fabric-resources.md)
