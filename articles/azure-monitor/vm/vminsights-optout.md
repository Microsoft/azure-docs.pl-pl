---
title: Wyłącz monitorowanie w usłudze VM Insights
description: W tym artykule opisano sposób zatrzymania monitorowania maszyn wirtualnych w usłudze VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 2de0dcd52745ebadb02ab8dbb563e28abf2822dc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046486"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Wyłączanie monitorowania maszyn wirtualnych w usłudze VM Insights

Po włączeniu monitorowania maszyn wirtualnych można później wyłączyć monitorowanie w usłudze VM Insights. W tym artykule pokazano, jak wyłączyć monitorowanie dla co najmniej jednej maszyny wirtualnej.  

Obecnie w usłudze VM Insights nie jest obsługiwane selektywne wyłączanie monitorowania maszyn wirtualnych. Obszar roboczy Log Analytics może obsługiwać szczegółowe informacje o maszynach wirtualnych i innych rozwiązaniach. Może również zbierać inne dane monitorowania. Jeśli obszar roboczy Log Analytics zawiera te usługi, należy zrozumieć efekt i metody wyłączenia monitorowania przed rozpoczęciem.

Informacje o usłudze VM są oparte na następujących składnikach, aby zapewnić ich środowisko pracy:

* Obszar roboczy Log Analytics, w którym są przechowywane dane monitorowania z maszyn wirtualnych i innych źródeł.
* Kolekcja liczników wydajności skonfigurowanych w obszarze roboczym. Kolekcja aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `VMInsights`, czyli rozwiązanie do monitorowania skonfigurowane w obszarze roboczym. To rozwiązanie aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `MicrosoftMonitoringAgent` i `DependencyAgent` , które są rozszerzeniami maszyny wirtualnej platformy Azure. Te rozszerzenia zbierają i przesyłają dane do obszaru roboczego.

Przygotowując się do wyłączenia monitorowania maszyn wirtualnych, pamiętaj o następujących kwestiach:

* Jeśli oceniasz pojedynczą maszynę wirtualną i korzystasz z prewybieranego domyślnego obszaru roboczego Log Analytics, możesz wyłączyć monitorowanie przez odinstalowanie agenta zależności z maszyny wirtualnej i odłączenie agenta Log Analytics z tego obszaru roboczego. Ta metoda jest odpowiednia, jeśli zamierzasz użyć maszyny wirtualnej do innych celów i podjąć decyzję później, aby ponownie połączyć ją z innym obszarem roboczym.
* W przypadku wybrania istniejącego obszaru roboczego Log Analytics, który obsługuje inne rozwiązania monitorowania i zbieranie danych z innych źródeł, można usunąć składniki rozwiązania z obszaru roboczego bez przerywania ani wpływu na obszar roboczy.  

>[!NOTE]
> Po usunięciu składników rozwiązania z obszaru roboczego można nadal wyświetlać dane dotyczące wydajności i mapy dla maszyn wirtualnych platformy Azure. Dane zostaną ostatecznie zatrzymane w widokach **wydajność** i **Mapa** . Opcja **Włącz** będzie dostępna na wybranej maszynie wirtualnej platformy Azure, dzięki czemu można ponownie włączyć monitorowanie w przyszłości.  

## <a name="remove-vm-insights-completely"></a>Całkowicie Usuń szczegółowe informacje o maszynie wirtualnej

Jeśli nadal potrzebujesz obszaru roboczego Log Analytics, wykonaj następujące kroki, aby całkowicie usunąć informacje o maszynie wirtualnej. Rozwiązanie zostanie usunięte `VMInsights` z obszaru roboczego.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz pozycję **Log Analytics**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy wybrany podczas włączania usługi VM Insights.
4. Po lewej stronie wybierz pozycję **rozwiązania**.  
5. Na liście rozwiązań wybierz pozycję **VMInsights (nazwa obszaru roboczego)**. Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Wyłącz monitorowanie i Zachowaj obszar roboczy  

Jeśli obszar roboczy Log Analytics nadal musi obsługiwać monitorowanie z innych źródeł, wykonaj następujące kroki, aby wyłączyć monitorowanie na maszynie wirtualnej użytej do oszacowania szczegółowych informacji o maszynie wirtualnej. W przypadku maszyn wirtualnych platformy Azure należy usunąć rozszerzenie maszyny wirtualnej agenta zależności oraz rozszerzenie maszyny wirtualnej agenta Log Analytics dla systemu Windows lub Linux bezpośrednio z maszyny wirtualnej. 

>[!NOTE]
>Nie usuwaj agenta Log Analytics, jeśli: 
>
> * Azure Automation zarządza maszyną wirtualną w celu organizowania procesów lub zarządzania konfiguracją lub aktualizacjami. 
> * Azure Security Center zarządza maszyną wirtualną w celu zapewnienia bezpieczeństwa i wykrywania zagrożeń. 
>
> Jeśli usuniesz agenta Log Analytics, uniemożliwisz tym usługom i rozwiązaniom aktywne zarządzanie maszyną wirtualną. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. W Azure Portal wybierz pozycję **Virtual Machines**. 
3. Z listy wybierz maszynę wirtualną. 
4. Po lewej stronie wybierz pozycję **rozszerzenia**. Na stronie **rozszerzenia** wybierz pozycję **DependencyAgent**.
5. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.
6. Na stronie **rozszerzenia** wybierz pozycję **MicrosoftMonitoringAgent**. Na stronie właściwości rozszerzenia wybierz pozycję **Odinstaluj**.  
