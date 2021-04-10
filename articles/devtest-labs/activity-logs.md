---
title: Dzienniki aktywności w Azure DevTest Labs | Microsoft Docs
description: W tym artykule przedstawiono kroki umożliwiające wyświetlenie dzienników aktywności Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582769"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Wyświetlanie dzienników aktywności dla laboratoriów w Azure DevTest Labs 
Po utworzeniu co najmniej jednej z laboratoriów prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do laboratoriów, modyfikowania ich i zarządzania nimi oraz przez kogo. Azure DevTest Labs używa Azure Monitor, w tym **dzienników aktywności**, do przekazywania informacji dotyczących tych operacji do laboratoriów. 

W tym artykule opisano sposób wyświetlania dzienników aktywności dla laboratorium w Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Wyświetlanie dziennika aktywności laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **DevTest Labs** w sekcji **DEVOPS** . Jeśli wybierzesz pozycję * (gwiazdka) obok pozycji **DevTest Labs** w sekcji **DEVOPS** . Ta akcja dodaje **DevTest Labs** do menu nawigacji po lewej stronie, aby można było łatwo uzyskać do niego dostęp. Następnie możesz wybrać pozycję **DevTest Labs** w menu nawigacji po lewej stronie.

    ![Wszystkie usługi — wybierz pozycję DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Z listy laboratoriów wybierz laboratorium.
1. Na stronie głównej laboratorium wybierz pozycję **konfiguracje i zasady** w menu po lewej stronie. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Wybierz pozycję Konfiguracja i zasady w menu po lewej stronie":::
1. Na stronie **Konfiguracja i zasady** wybierz pozycję **Dziennik aktywności** w menu po lewej stronie w obszarze **Zarządzaj**. Powinny być widoczne wpisy dotyczące operacji wykonywanych w laboratorium. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Dziennik aktywności":::    
1. Wybierz zdarzenie, aby wyświetlić jego szczegóły. Na stronie **Podsumowanie** są wyświetlane informacje, takie jak nazwa operacji, sygnatura czasowa i osoba, która wykonała operację. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Zatrzymywanie zdarzenia maszyny wirtualnej — podsumowanie":::        
1. Przejdź do karty **JSON** , aby zobaczyć więcej szczegółów. W poniższym przykładzie można zobaczyć nazwę maszyny wirtualnej i operację wykonywaną na maszynie wirtualnej (zatrzymana).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Zatrzymywanie zdarzenia maszyny wirtualnej — JSON":::           
1. Przejdź do karty **historia zmian (wersja zapoznawcza)** , aby zobaczyć historię zmian. W poniższym przykładzie zostanie wyświetlona zmiana wprowadzona na maszynie wirtualnej. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Zatrzymaj historię zmian zdarzeń maszyny wirtualnej":::             
1. Wybierz pozycję Zmień na liście historia zmian, aby wyświetlić więcej szczegółów na temat zmiany. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Zatrzymywanie zdarzenia maszyny wirtualnej — szczegóły zmiany":::             

Aby uzyskać więcej informacji na temat dzienników aktywności, zobacz [Dziennik aktywności platformy Azure](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat ustawiania **alertów** dotyczących dzienników aktywności, zobacz [tworzenie alertów](create-alerts.md).
- Aby dowiedzieć się więcej o dziennikach aktywności, zobacz  [Dziennik aktywności platformy Azure](../azure-monitor/essentials/activity-log.md).

