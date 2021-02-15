---
title: Zarządzaj powiadomieniami o alertach zdarzeń urządzeń dla zasobów usługi Azure Stack EDGE Pro | Microsoft Docs
description: Opisuje, jak używać Azure Portal do zarządzania alertami dla zdarzeń urządzeń w Azure Stack zasobów brzegowych Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 0ab7cdfb3d699a8415739565aae5d4326002bc43
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389148"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Zarządzaj powiadomieniami o alertach zdarzeń urządzeń w witrynie Azure Stack EDGE Pro

W tym artykule opisano sposób tworzenia reguł akcji w Azure Portal w celu wyzwalania lub pomijania powiadomień o alertach dotyczących zdarzeń urządzeń występujących w ramach grupy zasobów, subskrypcji platformy Azure lub pojedynczego zasobu Azure Stack Edge. Ten artykuł ma zastosowanie do wszystkich modeli Azure Stack Edge.  

## <a name="about-action-rules"></a>Reguły akcji — informacje

Reguła akcji może wyzwalać lub pomijać powiadomienia o alertach. Reguła akcji jest dodawana do *grupy akcji* — zestawu preferencji powiadomień, który służy do powiadamiania użytkowników, którzy muszą działać na alertach wyzwalanych w różnych kontekstach dla zasobu lub zestawu zasobów.

Aby uzyskać więcej informacji na temat reguł akcji, zobacz [Konfigurowanie reguły akcji](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule). Aby uzyskać więcej informacji na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](/azure/azure-monitor/platform/action-groups).

> [!NOTE]
> Funkcja reguł akcji jest w wersji zapoznawczej. Niektóre ekrany i kroki mogą ulec zmianie, gdy proces jest rafinowany.


## <a name="create-an-action-rule"></a>Tworzenie reguły akcji

Wykonaj następujące kroki w Azure Portal, aby utworzyć regułę akcji dla Azure Stack urządzeniu brzegowego.

> [!NOTE]
> Te kroki umożliwiają utworzenie reguły akcji, która wysyła powiadomienia do grupy akcji. Aby uzyskać szczegółowe informacje na temat tworzenia reguły akcji w celu pomijania powiadomień, zobacz [Konfigurowanie reguły akcji](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule).

1. Przejdź do Azure Stack urządzenia brzegowego w Azure Portal, a następnie przejdź do pozycji **monitorowanie > alerty**. Wybierz pozycję **Zarządzaj akcjami**.

   ![Monitorowanie alertów, zarządzanie widokiem akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Wybierz pozycję **reguły akcji (wersja zapoznawcza)**, a następnie wybierz pozycję **+ Nowa reguła akcji**.

   ![Zarządzanie akcjami, opcja reguł akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. Na ekranie **Tworzenie reguły akcji** Użyj opcji **zakres** , aby wybrać subskrypcję platformy Azure, grupę zasobów lub zasób docelowy. Reguła działania będzie działać na wszystkich alertach wygenerowanych w tym zakresie.

   1. Wybierz **pozycję Wybierz** według **zakresu**.

      ![Wybierz zakres dla nowej reguły akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Wybierz **subskrypcję** dla reguły akcji, opcjonalnie Filtruj według typu **zasobu** . Aby odfiltrować do Azure Stack zasobów brzegowych, wybierz pozycję **urządzenia Data Box Edge (dataBoxEdge)**.

      Obszar **zasobów** zawiera listę dostępnych zasobów na podstawie wybranych opcji.
  
      ![Dostępne zasoby na ekranie Wybieranie zakresu](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Zaznacz pole wyboru obok każdego zasobu, do którego chcesz zastosować regułę. Można wybrać subskrypcję, grupy zasobów lub poszczególne zasoby. Następnie wybierz pozycję **Done** (Gotowe).

      ![Przykładowe ustawienia dla zakresu reguły akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      Na ekranie **Tworzenie reguły akcji** zostanie wyświetlony wybrany zakres.

      ![Ukończony zakres dla reguły akcji Azure Stack Edge](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Użyj opcji **filtru** , aby zawęzić stosowanie reguły do podzbioru alertów w ramach wybranego zakresu.

   1. Wybierz pozycję **Dodaj** , aby otworzyć okienko **Dodawanie filtrów** .

      ![Opcja dodawania filtrów do reguły akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. W obszarze **filtry** Dodaj każdy filtr, który ma zostać zastosowany. Dla każdego filtru wybierz typ filtru, **operator** i **wartość**.
   
      Aby zapoznać się z listą opcji filtru, zobacz [kryteria filtrowania](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#filter-criteria).

      Poniższe filtry przykładowe dotyczą wszystkich alertów o poziomie ważności 2, 3 i 4, które usługa monitor podnosi do Azure Stack zasobów brzegowych.

      Po zakończeniu dodawania filtrów wybierz pozycję **gotowe**.
   
      ![Przykładowy filtr dla reguły akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Na ekranie **Tworzenie reguły akcji** wybierz pozycję **Grupa akcji** , aby utworzyć regułę, która wysyła powiadomienia. Następnie w obszarze **Akcje** wybierz **pozycję Wybierz**.

   ![Opcja grupy akcji służąca do tworzenia reguły akcji, która wysyła powiadomienia](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Aby utworzyć regułę, która pomija powiadomienia, wybierz pozycję **pomijanie**. Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguły akcji](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule).

6. Wybierz grupę akcji, która ma być używana z tą regułą działania. Następnie wybierz opcję **Wybierz**. Nowa reguła akcji zostanie dodana do preferencji powiadomień wybranej grupy akcji.

   Jeśli musisz utworzyć nową grupę akcji, wybierz pozycję **+ Utwórz grupę** akcji, a następnie wykonaj kroki opisane w temacie [Tworzenie grupy akcji przy użyciu Azure Portal](/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal).

   ![Wybierz grupę akcji, która ma być używana z regułą, a następnie wybierz pozycję Wybierz.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Nadaj **nazwę** i **Opis** regule nowej akcji, a następnie przypisz regułę do grupy zasobów.

9. Nowa reguła jest domyślnie włączona. Jeśli nie chcesz od razu zacząć korzystać z reguły, wybierz pozycję **nie** , aby **włączyć tworzenie aktualizacji reguł**.

10. Po zakończeniu ustawień wybierz pozycję **Utwórz**.

    ![Ustawienia wykonane dla reguły akcji, która będzie wysyłać powiadomienia o alertach](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Zostanie otwarty ekran **reguły akcji (wersja zapoznawcza)** , ale nie będzie można natychmiast zobaczyć nowej reguły akcji. Fokus jest **wszystkich** grup zasobów.

11. Aby wyświetlić nową regułę akcji, wybierz grupę zasobów dla reguły.

    ![Ekran reguł akcji z wyświetlaną nową regułą](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Wyświetlanie powiadomień

Powiadomienia są wysyłane, gdy nowe zdarzenie wyzwala alert dotyczący zasobu, który znajduje się w zakresie reguły akcji.

Grupa akcji dla zestawów reguł, którzy odbierają powiadomienie i typ wysyłanego powiadomienia — wiadomości e-mail, wiadomości SMS lub obu.

Otrzymywanie powiadomień po wyzwoleniu alertu może potrwać kilka minut.

Powiadomienie e-mail będzie wyglądać podobnie do tego.

![Przykładowe powiadomienie e-mail dla reguły akcji](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Następne kroki

<!-- - See [Create and manage action groups in the Azure portal](/azure/azure-monitor/platform/action-groups) for guidance on creating a new action group.
- See [Configure an action rule](/azure/azure-monitor/platform/alerts-action-rules?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Aby uzyskać informacje na temat przeglądania zdarzeń urządzeń, stanu sprzętu i wykresów metryk, zobacz [monitorowanie Azure Stack Edge](azure-stack-edge-monitor.md) . 
- Zobacz [używanie Azure monitor](azure-stack-edge-gpu-enable-azure-monitor.md) , aby uzyskać informacje na temat optymalizowania Azure monitor dla urządzeń z urządzeniami GPU w programie Azure Stack Edge.
- Aby uzyskać informacje na temat zarządzania indywidualnymi alertami [, zobacz Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi za pomocą Azure monitor linku docelowego](/azure/azure-monitor/platform/alerts-metric) .
