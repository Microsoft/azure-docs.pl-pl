---
title: Zarządzanie stanem w usłudze Azure Service Fabric Services
description: Poznaj informacje o stanie na platformie Azure Service Fabric, w tym o sposobie definiowania stanu usługi i zarządzania nim w usłudze Service Fabric Services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614574"
---
# <a name="service-state"></a>Stan usługi
**Stan usługi** odnosi się do danych znajdujących się w pamięci lub na dysku, których usługa wymaga do działania. Obejmuje to na przykład struktury danych i zmienne składowe, które Usługa odczytuje i zapisuje do pracy. W zależności od tego, jak usługa jest poddana analizie, może również zawierać pliki lub inne zasoby, które są przechowywane na dysku. Na przykład pliki, których baza danych będzie używać do przechowywania danych i dzienników transakcji.

Jako Przykładowa usługa Rozważmy Kalkulator. Podstawowa usługa kalkulatora przyjmuje dwie liczby i zwraca ich sumę. Wykonanie tego obliczenia nie wiąże się z żadną zmienną członkowską ani innymi informacjami.

Teraz Rozważmy ten sam kalkulator, ale z dodatkową metodą przechowywania i zwracania ostatniej obliczonej sumy. Ta usługa jest teraz stanowa. Stanowy oznacza, że zawiera on jakiś stan, w którym jest on zapisywany, gdy oblicza nową sumę i odczytuje z pytania o zwrócenie ostatniej obliczonej sumy.

W usłudze Azure Service Fabric pierwsza usługa jest nazywana usługą bezstanową. Druga usługa jest nazywana usługą stanową.

## <a name="storing-service-state"></a>Przechowywanie stanu usługi
Stan może być zewnętrzny lub wspólnie zlokalizowany z kodem, który operuje na stanie. Externalization stanu zwykle odbywa się przy użyciu zewnętrznej bazy danych lub innego magazynu danych, który działa na różnych komputerach za pośrednictwem sieci lub poza procesami na tym samym komputerze. W naszym przykładzie kalkulatora magazyn danych może być bazą danych SQL lub wystąpieniem magazynu tabel platformy Azure. Każde żądanie obliczenia sumy wykonuje aktualizację tych danych, a żądania kierowane do usługi zwracają wartość wyniku bieżącej wartości pobieranej ze sklepu. 

Stan może również znajdować się w tym samym miejscu przy użyciu kodu, który operuje na stanie. Usługi stanowe w Service Fabric są zwykle tworzone przy użyciu tego modelu. Service Fabric zapewnia infrastrukturę, aby zapewnić wysoką dostępność, spójność i trwałość oraz zapewnić, że usługi skompilowane w ten sposób mogą łatwo skalować.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pojęć Service Fabric, zobacz następujące artykuły:

* [Dostępność usług Service Fabric Services](service-fabric-availability-services.md)
* [Skalowalność usług Service Fabric Services](service-fabric-concepts-scalability.md)
* [Partycjonowanie Service Fabric usług](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
