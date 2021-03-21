---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517002"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Co można osiągnąć przy użyciu klastra?

W przypadku klastra Event Hubs, jak dużo możliwości pozyskiwania i przesyłania strumieniowego zależy od różnych czynników, takich jak producenci, konsumenci, szybkość pozyskiwania i przetwarzania oraz wiele innych. 

W poniższej tabeli przedstawiono wyniki testów porównawczych, które zostały osiągnięte podczas testowania:

| Kształt ładunku | Odbiorników | Przepustowość ruchu przychodzącego| Komunikaty dotyczące transferu danych przychodzących | Przepustowość ruchu wychodzącego | Komunikaty wychodzące | Łącznie TUs | TUs na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | komunikaty 400 000/s | 800 MB/s | komunikaty 800k/s | 400 TUs | 100 TUs | 
| Partie 10x10KB | 2 | 666 MB/s | 66.6 k komunikatów/s | 1,33 GB/s | komunikaty 133k/s | 666 TUs | 166 TUs |
| Partie 6x32KB | 1 | 1,05 GB/s | komunikaty 34k/s | 1,05 GB/s | komunikaty 34k/s | 1000 TUs | 250 TUs |

Podczas testowania użyto następujących kryteriów:

- Użyto dedykowanego klastra Event Hubs z czterema jednostkami pojemności (CUs). 
- Centrum zdarzeń używane na potrzeby pozyskiwania ma 200 partycji. 
- Dane, które zostały odebrane, są odbierane przez dwie aplikacje odbiornika odbierane ze wszystkich partycji.

### <a name="can-i-scale-updown-my-cluster"></a>Czy mogę skalować w górę i w dół mój klaster?

Po utworzeniu opłaty za klastry są naliczane co najmniej 4 godziny użytkowania. W wersji zapoznawczej środowiska samoobsługowego można przesłać [żądanie pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) do zespołu Event Hubs w ramach żądania limitu przydziału **technicznego**, aby skalować klaster w górę lub w dół w celu skalowania  >    >  klastra w górę lub w **dół** . Ukończenie żądania skalowania klastra może potrwać do 7 dni. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Jak działa geograficzna usługa DR z moim klastrem?

Można sparować geograficznie przestrzeń nazw w klastrze dedykowanym z inną przestrzenią nazw w klastrze dedykowanym. Nie zachęcamy do kojarzenia przestrzeni nazw dedykowanej warstwy z przestrzenią nazw w naszej standardowej ofercie, ponieważ ograniczenie przepływności będzie niezgodne i spowoduje błędy. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Czy mogę migrować moje standardowe przestrzenie nazw, które mają należeć do klastra warstwy dedykowanej?
Obecnie nie obsługujemy zautomatyzowanego procesu migracji danych centrów zdarzeń z standardowej przestrzeni nazw do dedykowanej. 
