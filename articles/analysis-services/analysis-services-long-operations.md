---
title: Najważniejsze wskazówki dotyczące długotrwałych operacji w usługach Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano najlepsze rozwiązania dotyczące długotrwałych operacji.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428110"
---
# <a name="best-practices-for-long-running-operations"></a>Najważniejsze wskazówki dotyczące długotrwałych operacji

W usłudze Azure Analysis Services *węzeł* reprezentuje maszynę wirtualną hosta, na której jest uruchomiony zasób serwera. Niektóre operacje, takie jak długotrwałe kwerendy, operacje odświeżania i synchronizacja skalowania kwerend, mogą zakończyć się niepowodzeniem, jeśli zasób serwera zostanie przeniesiony do innego węzła. Typowe komunikaty o błędach w tym scenariuszu obejmują:

- "Wystąpił błąd podczas próby zlokalizowania długotrwałego żądania XMLA. Żądanie mogło zostać przerwane przez uaktualnienie usługi lub ponowne uruchomienie serwera."
- "Zadanie z identyfikatorem<guid>'<database>dla modelu ' ' został anulowany z powodu błędu usługi (brak aktywności) z komunikatem "Anulowanie żądania odświeżania, ponieważ został zatrzymany bez żadnych aktualizacji. Jest to problem z usługą wewnętrzną. Prześlij ponownie zadanie lub złóż zgłoszenie, aby uzyskać pomoc, jeśli ten problem występuje wielokrotnie."

Istnieje wiele powodów, dla których długotrwałe operacje mogą zostać zakłócone. Na przykład aktualizacje na platformie Azure, takie jak: 
- Poprawki systemu operacyjnego 
- Aktualizacje zabezpieczeń
- Aktualizacje usług Azure Analysis Services
- Aktualizacje sieci szkieletowej usług. Sieci szkieletowej usług jest składnikiem platformy używanym przez wiele usług w chmurze firmy Microsoft, w tym usługi Azure Analysis Services.

Oprócz aktualizacji, które występują w usłudze, istnieje naturalny ruch usług między węzłami ze względu na równoważenie obciążenia. Ruchy węzłów są oczekiwaną częścią usługi w chmurze. Usługi Azure Analysis Services próbują zminimalizować wpływ ruchów węzłów, ale nie można ich całkowicie wyeliminować. 

Oprócz ruchów węzłów, istnieją inne błędy, które mogą wystąpić. Na przykład system bazy danych źródła danych może być w trybie offline lub łączność sieciowa zostanie utracona. Jeśli podczas odświeżania partycja ma 10 milionów wierszy i występuje błąd w 9-milionowym wierszu, nie ma możliwości ponownego uruchomienia odświeżania w punkcie awarii. Usługa musi rozpocząć się ponownie od początku. 

## <a name="refresh-rest-api"></a>Odśwież interfejs API REST

Przerwy w działaniu usługi mogą być trudne w przypadku długotrwałych operacji, takich jak odświeżanie danych. Usługi Azure Analysis Services zawierają interfejs API REST, który pomaga w łagodzeniu negatywnych skutków przerw w świadczeniu usług. Aby dowiedzieć się więcej, zobacz [Odświeżanie asynchroniczne za pomocą interfejsu API REST](analysis-services-async-refresh.md).
 
Oprócz interfejsu API REST istnieją inne podejścia, których można użyć, aby zminimalizować potencjalne problemy podczas długotrwałych operacji odświeżania. Głównym celem jest uniknięcie konieczności ponownego uruchamiania operacji odświeżania od początku i zamiast tego wykonywać odświeżania w mniejszych partiach, które mogą być zatwierdzane etapami. 
 
Interfejs API REST umożliwia takie ponowne uruchomienie, ale nie pozwala na pełną elastyczność tworzenia i usuwania partycji. Jeśli scenariusz wymaga złożonych operacji zarządzania danymi, rozwiązanie powinno zawierać jakąś formę przetwarzania wsadowego w swojej logice. Na przykład za pomocą transakcji do przetwarzania danych w wielu oddzielnych partii pozwala na niepowodzenie nie wymagają ponownego uruchomienia od początku, ale zamiast tego z pośredniego punktu kontrolnego. 
 
## <a name="scale-out-query-replicas"></a>Repliki kwerend skalowanych w poziomie

Niezależnie od tego, czy używasz REST, czy logiki niestandardowej, kwerendy aplikacji klienckiej mogą nadal zwracać niespójne lub pośrednie wyniki podczas przetwarzania partii. Jeśli spójne dane zwracane przez kwerendy aplikacji klienckich jest wymagane podczas przetwarzania, a dane modelu jest w stanie pośrednim, można użyć [skalowania](analysis-services-scale-out.md) w poziomie z replik kwerendy tylko do odczytu.

Za pomocą replik kwerendy tylko do odczytu, podczas gdy odświeżania są wykonywane w partiach, użytkownicy aplikacji klienckiej można kontynuować kwerendy starej migawki danych w replikach tylko do odczytu. Po zakończeniu odświeżania można wykonać operację synchronizacji, aby aktualizować repliki tylko do odczytu.


## <a name="next-steps"></a>Następne kroki

[Odświeżanie asynchroniczne za pomocą interfejsu API REST](analysis-services-async-refresh.md)  
[Skalowanie w poziomie usług Azure Analysis Services](analysis-services-scale-out.md)  
[Wysoka dostępność usług Analysis Services](analysis-services-bcdr.md)  
[Wskazówki dotyczące ponawiania prób dla usług platformy Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

