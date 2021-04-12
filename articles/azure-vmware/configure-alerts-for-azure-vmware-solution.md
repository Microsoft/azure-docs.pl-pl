---
title: Konfigurowanie alertów i korzystanie z metryk w rozwiązaniu Azure VMware
description: Dowiedz się, jak odbierać powiadomienia przy użyciu alertów. Dowiedz się również, jak korzystać z metryk, aby uzyskać dokładniejszy wgląd w chmurę prywatną rozwiązania VMware platformy Azure.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: f021662658399111187e9963fc5caec434fabf4a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096839"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Konfigurowanie alertów platformy Azure w rozwiązaniu VMware platformy Azure 

W tym artykule dowiesz się, jak skonfigurować [grupy akcji platformy Azure](/azure/azure-monitor/alerts/action-groups) w [Microsoft Azure alertach](/azure/azure-monitor/alerts/alerts-overvie) , aby otrzymywać powiadomienia o zdefiniowanych zdarzeniach wyzwalanych przez użytkownika. Dowiesz się również, jak używać [metryk Azure monitor](/azure/azure-monitor/essentials/data-platform-metrics) , aby uzyskać dokładniejszy wgląd w chmurę prywatną rozwiązania VMware platformy Azure.


## <a name="supported-metrics-and-activities"></a>Obsługiwane metryki i działania

Następujące metryki są widoczne za pomocą metryk Azure Monitor.

| **Nazwa sygnału**                                                         | **Typ sygnału** | **Monitorowanie usługi** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Całkowita pojemność dysku magazynu danych                                           | Metric          | Platforma            |
| Użyty dysk magazynu danych (%)                                          | Metric          | Platforma            |
| Procentowe użycie procesora CPU                                                          | Metric          | Platforma            |
| Średnia efektywna pamięć                                                | Metric          | Platforma            |
| Średnie obciążenie pamięci                                                 | Metric          | Platforma            |
| Średnia całkowita ilość pamięci                                                    | Metric          | Platforma            |
| Średnie użycie pamięci                                                    | Metric          | Platforma            |
| Używany dysk magazynu danych                                                     | Metric          | Platforma            |
| Wszystkie operacje administracyjne                                           | Dziennik aktywności    | Administracyjny      |
| Zarejestruj dostawcę zasobów Microsoft. Automatyczna synchronizacja. (Microsoft. Automatyczna synchronizacja/privateClouds) | Dziennik aktywności    | Administracyjny      |
| Utwórz lub zaktualizuj PrivateCloud. (Microsoft. Automatyczna synchronizacja/privateClouds)          | Dziennik aktywności    | Administracyjny      |
| Usuń element PrivateCloud. (Microsoft. Automatyczna synchronizacja/privateClouds)                    | Dziennik aktywności    | Administracyjny      |

## <a name="configure-an-alert-rule"></a>Konfigurowanie reguły alertu
1. W chmurze prywatnej rozwiązania Azure VMware wybierz kolejno pozycje **monitorowanie**  >  **alertów**, a następnie **Nowa reguła alertu**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Zrzut ekranu pokazujący, gdzie skonfigurować regułę alertu w chmurze prywatnej rozwiązania Azure VMware." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Zostanie otwarty nowy ekran konfiguracji, w którym można:
   - Zdefiniuj zakres
   - Konfigurowanie warunku
   - Konfigurowanie grupy akcji
   - Zdefiniuj szczegóły reguły alertu
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Zrzut ekranu przedstawiający okno Tworzenie reguły alertu." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. W obszarze **zakres** wybierz zasób docelowy, który chcesz monitorować. Domyślnie w chmurze prywatnej rozwiązania VMware platformy Azure, w którym otwarto menu alerty, zostało zdefiniowane.

1. W obszarze **warunek** wybierz pozycję **Dodaj warunek**, a następnie w otwartym oknie Wybierz sygnał, który chcesz utworzyć dla reguły alertu. 

   W naszym przykładzie został wybrany **procent użycia dysku magazynu** danych, który jest istotny w perspektywie [umowy SLA rozwiązania VMware platformy Azure](https://aka.ms/avs/sla) . 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Zrzut ekranu pokazujący okno Konfigurowanie logiki sygnałów ze wstępnie zdefiniowanymi nazwami sygnałów."::: 

1. Zdefiniuj logikę, która będzie wyzwalać alert, a następnie wybierz pozycję **gotowe**. 

   W naszym przykładzie został dostosowany tylko **próg** i **częstotliwość obliczania** . 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Zrzut ekranu pokazujący informacje dla wybranej logiki sygnałów."::: 

1. W obszarze **Akcje** wybierz pozycję **Dodaj grupy akcji**. Grupa akcji definiuje, w *jaki sposób* powiadomienie zostaje odebrane i *kto* go otrzymuje.   Powiadomienia można odbierać za pośrednictwem poczty e-mail, wiadomości SMS, [powiadomienia wypychanego aplikacji mobilnej platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/) lub wiadomości głosowych.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Zrzut ekranu pokazujący istniejące grupy akcji i miejsce, w którym ma zostać utworzona nowa grupa akcji.":::

1. W otwartym oknie wybierz pozycję **Utwórz grupę akcji**.

   >[!TIP]
   > Możesz również użyć istniejącej grupy akcji.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Zrzut ekranu pokazujący grupy akcji do wybrania dla alertu."::: 

 

 
1. W otwartym oknie, na karcie **podstawowe** , nadaj grupie akcji nazwę i nazwę wyświetlaną.

1. Wybierz kartę **powiadomienia** , wybierz **Typ powiadomienia** i **nazwę**. Następnie wybierz przycisk **OK**.

   Nasz przykład opiera się na powiadomień e-mail.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Zrzut ekranu pokazujący ustawienia poczty e-mail, wiadomości SMS, wypychania i głosu dla alertu." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Obowiązkowe Skonfiguruj **Akcje** , jeśli chcesz wykonywać aktywne akcje i otrzymywać powiadomienia o zdarzeniu. Wybierz dostępny **Typ akcji** , a następnie wybierz pozycję **Recenzja + Utwórz**. 
   - Elementy Runbook automatyzacji
   - Azure Functions — dla niestandardowego wykonywania kodu bezserwerowego opartego na zdarzeniach
   - NARZĘDZIA ITSM — aby przeprowadzić integrację z dostawcą usług, takim jak usługi ServiceNow, aby utworzyć bilet
   - Aplikacja logiki — w celu uzyskania bardziej złożonej aranżacji przepływu pracy
   - Elementy webhook — aby wyzwolić proces w innej usłudze

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Zrzut ekranu pokazujący okno Tworzenie grupy akcji z fokusem na liście rozwijanej Typ akcji." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. W obszarze **szczegóły reguły alertu** Podaj nazwę, opis, grupę zasobów, aby zachować regułę alertu, ważność. Następnie wybierz pozycję **Utwórz regułę alertu**.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Zrzut ekranu pokazujący szczegóły reguły alertu."::: 
 
   Reguła alertu jest widoczna i można ją zarządzać z poziomu Azure Portal.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Zrzut ekranu pokazujący nową regułę alertu w oknie reguły." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Gdy tylko Metryka osiągnie próg określony w regule alertu, menu **alerty** jest aktualizowane i udostępniane.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Zrzut ekranu pokazujący alert po osiągnięciu określonego progu." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   W zależności od skonfigurowanej grupy akcji otrzymasz powiadomienie za pomocą skonfigurowanego nośnika. W naszym przykładzie została skonfigurowana poczta e-mail.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Zrzut ekranu przedstawiający alert Azure Monitor z ciągiem błędu oraz zdarzenie daty i godziny zostało wyzwolone."::: 

## <a name="work-with-metrics"></a>Korzystanie z metryk

1. W chmurze prywatnej rozwiązania Azure VMware wybierz pozycję **monitorowanie**  >  **metryk**. Następnie wybierz żądaną metrykę z listy rozwijanej.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Zrzut ekranu przedstawiający okno metryk i fokus na liście rozwijanej Metryka." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Można zmienić parametry diagramu, takie jak **zakres czasu** lub **stopień szczegółowości czasu**. 

   Inne opcje to:
   - **Przechodzenie do szczegółów dzienników** i wykonywanie zapytań dotyczących danych w powiązanym obszarze roboczym log Analytics
   - **Przypnij ten diagram** do pulpitu nawigacyjnego platformy Azure dla wygody.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Zrzut ekranu przedstawiający opcje zakresu czasu i stopnia szczegółowości dla metryki." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu reguły alertu dla chmury prywatnej rozwiązania VMware platformy Azure, warto jeszcze bardziej zapoznaj się z tematem:
- [Metryki usługi Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics)
- [Alerty usługi Azure Monitor](/azure/azure-monitor/alerts/alerts-overview)
- [Grupy akcji platformy Azure](/azure/azure-monitor/alerts/action-groups)

Możesz również kontynuować korzystanie z jednego z pozostałych [rozwiązań VMware platformy Azure](index.yml) .





