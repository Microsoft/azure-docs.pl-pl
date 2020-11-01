---
title: Architektura Menedżer zasobów
description: Omówienie i informacje o architekturze dotyczące usługi Menedżer zasobów klastra Service Fabric platformy Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8e7d5d4b730ef1669bd9bb7d74e35924061f5580
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146215"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Omówienie architektury usługi Resource Manager dla klastra
Menedżer zasobów klastra Service Fabric jest usługą centralną, która działa w klastrze. Zarządza żądanym stanem usług w klastrze, szczególnie w odniesieniu do zużycia zasobów i wszelkich reguł umieszczania. 

Aby zarządzać zasobami w klastrze, Menedżer zasobów klaster Service Fabric musi zawierać kilka informacji:

- Które usługi obecnie istnieją
- Bieżące (lub domyślne) użycie zasobów przez poszczególne usługi 
- Pozostała pojemność klastra 
- Pojemność węzłów w klastrze 
- Ilość zasobów zużywanych w każdym węźle

Użycie zasobów przez daną usługę może ulec zmianie z upływem czasu, a usługi zwykle zadbają o więcej niż jeden typ zasobu. W różnych usługach mogą być mierzone rzeczywiste i fizyczne zasoby. Usługi mogą śledzić metryki fizyczne, takie jak użycie pamięci i dysku. Zazwyczaj usługi mogą zadbać o metryki logiczne — podobnie jak "WorkQueueDepth" lub "TotalRequests". Metryki logiczne i fizyczne mogą być używane w tym samym klastrze. Metryki mogą być współużytkowane przez wiele usług lub być specyficzne dla określonej usługi.

## <a name="other-considerations"></a>Inne zagadnienia
Właściciele i operatorzy klastra mogą różnić się od autorów usługi i aplikacji albo co najmniej te same osoby, które mają różne systemy. Podczas tworzenia aplikacji wiadomo, co jest potrzebne. Masz szacunkowy zakres zasobów, które zostaną wykorzystane i w jaki sposób należy wdrożyć różne usługi. Na przykład warstwa sieci Web musi być uruchamiana na węzłach uwidocznionych w Internecie, a usługi bazy danych nie powinny. Inne przykładowe usługi sieci Web są prawdopodobnie ograniczone przez procesor i sieć, a usługi warstwy danych zwiększają ilość pamięci i użycie dysku. Jednak osoba obsługująca zdarzenia na żywo dla tej usługi w środowisku produkcyjnym lub zarządzający uaktualnieniem do usługi ma inne zadanie do wykonania i wymaga różnych narzędzi. 

Klaster i usługi są dynamiczne:

- Liczba węzłów w klastrze może się zwiększać i zmniejszać
- Węzły o różnych rozmiarach i typach mogą pochodzić i przejść
- Usługi można tworzyć, usuwać i zmieniać odpowiednie alokacje zasobów i reguły umieszczania
- Uaktualnienia lub inne operacje zarządzania mogą przechodzić przez klaster w aplikacji na poziomach infrastruktury
- Błędy mogą wystąpić w dowolnym momencie.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Składniki i przepływ danych Menedżera zasobów klastra
Klaster Menedżer zasobów musi śledzić wymagania każdej usługi i zużywać zasoby przez każdy obiekt usługi w ramach tych usług. Klaster Menedżer zasobów ma dwie części koncepcyjne: agenci uruchamiani w każdym węźle i usługi odporne na uszkodzenia. Agenci w każdym węźle śledzą raporty obciążenia z usług, agregują je i okresowo raportują. Usługa Menedżer zasobów klastra agreguje wszystkie informacje z agentów lokalnych i reaguje na jego bieżącą konfigurację.

Przyjrzyjmy się na poniższym diagramie:

<center>

![Diagram przedstawiający Thow usługi Menedżer zasobów klastra agreguje wszystkie informacje z agentów lokalnych i reaguje na podstawie bieżącej konfiguracji.][Image1]
</center>

W czasie wykonywania istnieje wiele zmian, które mogą wystąpić. Załóżmy na przykład, że ilość zasobów niektórych usług zużywa zmiany, niektóre usługi kończą się niepowodzeniem, a niektóre węzły przyłączają się i opuszczają klaster. Wszystkie zmiany w węźle są agregowane i okresowo wysyłane do usługi Menedżer zasobów klastra (1, 2), gdzie są agregowane, analizowane i przechowywane. Co kilka sekund usługa sprawdza zmiany i określa, czy wymagane są jakieś akcje (3). Na przykład może to zauważyć, że niektóre puste węzły zostały dodane do klastra. W związku z tym decyduje się przenieść niektóre usługi do tych węzłów. Menedżer zasobów klastra może również zauważyć, że określony węzeł jest przeciążony lub że pewne usługi nie uległy awarii lub zostały usunięte, zwalniając zasoby w innym miejscu.

Przyjrzyjmy się na poniższym diagramie i zobacz, co się stanie dalej. Załóżmy, że klaster Menedżer zasobów określa, że zmiany są konieczne. Jest ona Współrzędna z innymi usługami systemowymi (w szczególności Menedżer trybu failover), aby wprowadzić niezbędne zmiany. Następnie niezbędne polecenia są wysyłane do odpowiednich węzłów (4). Załóżmy na przykład, że w Menedżer zasobów zauważono, że komputerze Węzeł5 został przeciążony, i dlatego zadecydował o przeniesieniu usługi B z komputerze Węzeł5 do Węzeł4. Po zakończeniu ponownej konfiguracji (5) klaster będzie wyglądać następująco:

<center>

![Architektura modułu równoważenia zasobów][Image2]
</center>

## <a name="next-steps"></a>Następne kroki
- Klaster Menedżer zasobów ma wiele opcji opisywania klastra. Aby dowiedzieć się więcej na ten temat, zapoznaj się z tym artykułem na temat [opisywania Service Fabric klastra](./service-fabric-cluster-resource-manager-cluster-description.md)
- Podstawowe obowiązki Menedżer zasobów klastra to ponowne zrównoważenie klastra i Wymuszanie reguł umieszczania. Aby uzyskać więcej informacji na temat konfigurowania tych zachowań, zobacz [równoważenie klastra Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
