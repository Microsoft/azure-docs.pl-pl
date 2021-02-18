---
title: Wyłącz monitorowanie w Azure Monitor dla maszyn wirtualnych
description: W tym artykule opisano sposób zatrzymania monitorowania maszyn wirtualnych w programie Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619713"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Wyłącz monitorowanie maszyn wirtualnych w Azure Monitor dla maszyn wirtualnych

Po włączeniu monitorowania maszyn wirtualnych można później wyłączyć monitorowanie w programie Azure Monitor dla maszyn wirtualnych. W tym artykule pokazano, jak wyłączyć monitorowanie dla co najmniej jednej maszyny wirtualnej.  

Obecnie Azure Monitor dla maszyn wirtualnych nie obsługuje selektywnego wyłączania monitorowania maszyn wirtualnych. Obszar roboczy Log Analytics może obsługiwać Azure Monitor dla maszyn wirtualnych i inne rozwiązania. Może również zbierać inne dane monitorowania. Jeśli obszar roboczy Log Analytics zawiera te usługi, należy zrozumieć efekt i metody wyłączenia monitorowania przed rozpoczęciem.

Azure Monitor dla maszyn wirtualnych opiera się na następujących składnikach, aby zapewnić ich środowisko pracy:

* Obszar roboczy Log Analytics, w którym są przechowywane dane monitorowania z maszyn wirtualnych i innych źródeł.
* Kolekcja liczników wydajności skonfigurowanych w obszarze roboczym. Kolekcja aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `VMInsights`, czyli rozwiązanie do monitorowania skonfigurowane w obszarze roboczym. To rozwiązanie aktualizuje konfigurację monitorowania na wszystkich maszynach wirtualnych podłączonych do obszaru roboczego.
* `MicrosoftMonitoringAgent` i `DependencyAgent` , które są rozszerzeniami maszyny wirtualnej platformy Azure. Te rozszerzenia zbierają i przesyłają dane do obszaru roboczego.

Przygotowując się do wyłączenia monitorowania maszyn wirtualnych, pamiętaj o następujących kwestiach:

* Jeśli oceniasz pojedynczą maszynę wirtualną i korzystasz z prewybieranego domyślnego obszaru roboczego Log Analytics, możesz wyłączyć monitorowanie przez odinstalowanie agenta zależności z maszyny wirtualnej i odłączenie agenta Log Analytics z tego obszaru roboczego. Ta metoda jest odpowiednia, jeśli zamierzasz użyć maszyny wirtualnej do innych celów i podjąć decyzję później, aby ponownie połączyć ją z innym obszarem roboczym.
* W przypadku wybrania istniejącego obszaru roboczego Log Analytics, który obsługuje inne rozwiązania monitorowania i zbieranie danych z innych źródeł, można usunąć składniki rozwiązania z obszaru roboczego bez przerywania ani wpływu na obszar roboczy.  

>[!NOTE]
> Po usunięciu składników rozwiązania z obszaru roboczego można nadal wyświetlać dane dotyczące wydajności i mapy dla maszyn wirtualnych platformy Azure. Dane zostaną ostatecznie zatrzymane w widokach **wydajność** i **Mapa** . Opcja **Włącz** będzie dostępna na wybranej maszynie wirtualnej platformy Azure, dzięki czemu można ponownie włączyć monitorowanie w przyszłości.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Usuń Azure Monitor dla maszyn wirtualnych całkowicie

Jeśli nadal potrzebujesz obszaru roboczego Log Analytics, wykonaj następujące kroki, aby całkowicie usunąć Azure Monitor dla maszyn wirtualnych. Rozwiązanie zostanie usunięte `VMInsights` z obszaru roboczego.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz pozycję **Log Analytics**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy wybrany podczas włączania Azure Monitor dla maszyn wirtualnych.
4. Po lewej stronie wybierz pozycję **rozwiązania**.  
5. Na liście rozwiązań wybierz pozycję **VMInsights (nazwa obszaru roboczego)**. Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Wyłącz monitorowanie i Zachowaj obszar roboczy  

Jeśli obszar roboczy Log Analytics nadal musi obsługiwać monitorowanie z innych źródeł, wykonaj następujące kroki, aby wyłączyć monitorowanie na maszynie wirtualnej użytej do oszacowania Azure Monitor dla maszyn wirtualnych. W przypadku maszyn wirtualnych platformy Azure należy usunąć rozszerzenie maszyny wirtualnej agenta zależności oraz rozszerzenie maszyny wirtualnej agenta Log Analytics dla systemu Windows lub Linux bezpośrednio z maszyny wirtualnej. 

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
