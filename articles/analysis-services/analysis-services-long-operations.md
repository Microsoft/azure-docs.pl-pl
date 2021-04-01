---
title: Najlepsze rozwiązania dotyczące długotrwałych operacji w Azure Analysis Services | Microsoft Docs
description: W tym artykule opisano najlepsze rozwiązania dotyczące długotrwałych operacji.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92014838"
---
# <a name="best-practices-for-long-running-operations"></a>Najlepsze rozwiązania dotyczące długotrwałych operacji

W Azure Analysis Services *węzeł* reprezentuje maszynę wirtualną hosta, w której jest uruchomiony zasób serwera. Niektóre operacje, takie jak długotrwałe wykonywanie zapytań, operacje odświeżania i synchronizacja skalowalna w poziomie zapytań mogą zakończyć się niepowodzeniem, jeśli zasób serwera przejdzie do innego węzła. Typowe komunikaty o błędach w tym scenariuszu obejmują:

- "Wystąpił błąd podczas próby zlokalizowania długotrwałego żądania XMLA. Żądanie mogło zostać przerwane przez uaktualnienie usługi lub ponowne uruchomienie serwera ".
- "Zadanie o IDENTYFIKATORze" <guid> dla modelu " <database> " zostało anulowane z powodu błędu usługi (nieaktywności) z komunikatem "Anulowanie żądania odświeżania, ponieważ było ono zablokowane bez żadnych aktualizacji. Jest to wewnętrzny problem z usługą. Prześlij ponownie zadanie lub zapoznaj się z biletem, aby uzyskać pomoc, jeśli ten problem wystąpi wielokrotnie. "

Istnieje wiele powodów, dla których długotrwałe operacje mogą zostać zakłócone. Na przykład aktualizacje na platformie Azure, takie jak: 
- Poprawki systemu operacyjnego 
- Aktualizacje zabezpieczeń
- Aktualizacje usługi Azure Analysis Services
- Service Fabric aktualizacje. Service Fabric to składnik platformy używany przez wiele usług firmy Microsoft w chmurze, w tym Azure Analysis Services.

Poza aktualizacjami występującymi w usłudze istnieje naturalne przenoszenie usług między węzłami z powodu równoważenia obciążenia. Przenoszenie węzłów jest oczekiwaną częścią usługi w chmurze. Azure Analysis Services próbuje zminimalizować wpływ na ruch węzłów, ale nie można ich całkowicie wyeliminować. 

Oprócz przenoszenia węzłów istnieją inne błędy, które mogą wystąpić. Na przykład system bazy danych źródła danych może być w trybie offline lub połączenie sieciowe zostało utracone. Jeśli podczas odświeżania partycja zawiera 10 000 000 wierszy i wystąpi błąd w wierszu 9 milionowego, nie ma możliwości ponownego uruchomienia odświeżania w punkcie awarii. Usługa musi zacząć od początku. 

## <a name="refresh-rest-api"></a>Odśwież interfejs API REST

Przerwy w działaniu usługi mogą być trudne dla długotrwałych operacji, takich jak odświeżanie danych. Azure Analysis Services obejmuje interfejs API REST, który pomaga ograniczyć negatywne wpływ na przerwy w świadczeniu usług. Aby dowiedzieć się więcej, zobacz [odświeżanie asynchroniczne za pomocą interfejsu API REST](analysis-services-async-refresh.md).
 
Oprócz interfejsu API REST istnieją inne podejścia, których można użyć do zminimalizowania potencjalnych problemów podczas długotrwałych operacji odświeżania. Głównym celem jest uniknięcie konieczności ponownego uruchomienia operacji odświeżania od początku, a zamiast tego przeprowadzenie odświeżenia w mniejszych partiach, które mogą być zatwierdzone na etapach. 
 
Interfejs API REST umożliwia ponowne uruchomienie systemu, ale nie pozwala na pełną elastyczność tworzenia i usuwania partycji. Jeśli scenariusz wymaga złożonych operacji zarządzania danymi, rozwiązanie powinno uwzględniać w swojej logice pewną formę przetwarzania wsadowego. Na przykład za pomocą transakcji do przetwarzania danych w wielu oddzielnych partiach może nie wymagać ponownego uruchomienia od początku, ale zamiast pośredniego punktu kontrolnego. 
 
## <a name="scale-out-query-replicas"></a>Repliki zapytań skalowalnych w poziomie

Bez względu na to, czy jest używana logika REST, czy niestandardowa, zapytania aplikacji klienckiej nadal mogą zwracać niespójne lub pośrednie wyniki podczas przetwarzania partii. Jeśli podczas przetwarzania są wymagane spójne dane zwracane przez zapytania aplikacji klienta, a dane modelu są w stanie pośrednim, można użyć [skalowania](analysis-services-scale-out.md) w poziomie przy użyciu replik zapytań tylko do odczytu.

Przy użyciu replik zapytań tylko do odczytu, podczas gdy odświeża są wykonywane w partiach, użytkownicy aplikacji klienckiej mogą kontynuować wykonywanie zapytania dotyczącego starej migawki danych w replikach tylko do odczytu. Po zakończeniu odświeżania można wykonać operację synchronizacji, aby zapewnić aktualność replik tylko do odczytu.


## <a name="next-steps"></a>Następne kroki

[Odświeżanie asynchroniczne za pomocą interfejsu API REST](analysis-services-async-refresh.md)  
[Skalowanie w poziomie usług Azure Analysis Services](analysis-services-scale-out.md)  
[Wysoka dostępność usług Analysis Services](analysis-services-bcdr.md)  
[Wskazówki dotyczące ponawiania prób dla usług platformy Azure](/azure/architecture/best-practices/retry-service-specific)