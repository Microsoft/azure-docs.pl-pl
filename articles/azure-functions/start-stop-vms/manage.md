---
title: Zarządzanie uruchamianiem/zatrzymywaniem maszyn wirtualnych v2 (wersja zapoznawcza)
description: W tym artykule opisano sposób monitorowania stanu maszyn wirtualnych platformy Azure zarządzanych przez funkcję uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza) i wykonywania innych zadań zarządzania.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111957"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Jak zarządzać uruchamianiem/zatrzymywaniem maszyn wirtualnych v2 (wersja zapoznawcza)

## <a name="azure-dashboard"></a>Pulpit nawigacyjny platformy Azure

Uruchamianie/zatrzymywanie maszyn wirtualnych v2 (wersja zapoznawcza) zawiera [pulpit nawigacyjny](../../azure-monitor/visualizations.md#azure-dashboards) , który ułatwia zrozumienie zakresu zarządzania i ostatnich operacji na maszynach wirtualnych. Jest to szybki i łatwy sposób sprawdzenia stanu każdej operacji wykonanej na maszynach wirtualnych platformy Azure. Wizualizacja w każdym kafelku jest oparta na zapytaniu dziennika i wyświetleniu zapytania, zaznacz opcję **Otwórz w dziennikach** w prawym górnym rogu kafelka. Spowoduje to otwarcie narzędzia [log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) w Azure Portal, a w tym miejscu możesz oszacować zapytanie i zmodyfikować je, aby obsługiwały Twoje potrzeby, takie jak niestandardowe [alerty dzienników](../../azure-monitor/alerts/alerts-log.md), [skoroszyt](../../azure-monitor/visualize/workbooks-overview.md)niestandardowy itp.

Dane dziennika wyświetlane każdego kafelka na pulpicie nawigacyjnym są odświeżane co godzinę, z ręczną opcją odświeżania na żądanie, klikając ikonę **odświeżania** w danej wizualizacji lub odświeżając pełny pulpit nawigacyjny.

Aby dowiedzieć się więcej o pracy z pulpitem nawigacyjnym opartym na dzienniku, zobacz poniższy [samouczek](../../azure-monitor/visualize/tutorial-logs-dashboards.md).

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail po wdrożeniu uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza), można zmodyfikować grupę akcji utworzoną podczas wdrażania.

1. W Azure Portal przejdź do pozycję **monitorowanie**, a następnie pozycję **alerty**. Wybierz pozycję **Zarządzaj akcjami**.

1. Na stronie **Zarządzanie akcjami** wybierz grupę akcji o nazwie **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Zrzut ekranu strony grupy akcji.":::

1. Na stronie **StartStopV2_VM_Notification** można modyfikować opcje powiadomień dotyczące poczty e-mail/wiadomości SMS/wypychania/głosu. Dodaj inne akcje lub zaktualizuj istniejącą konfigurację do tej grupy akcji, a następnie kliknij przycisk **OK** , aby zapisać zmiany.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Zrzut ekranu strony wiadomości E-mail/SMS/wypychania/głosu z informacją o zaktualizowanym adresie e-mail.":::

    Aby dowiedzieć się więcej na temat grup akcji, zobacz [grupy akcji](../../azure-monitor/alerts/action-groups.md)

Poniższy zrzut ekranu przedstawia przykładową wiadomość e-mail, która jest wysyłana, gdy ta funkcja zamyka maszyny wirtualne.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Zrzut ekranu przykładowej wiadomości e-mail wysyłanej, gdy ta funkcja zamyka maszyny wirtualne.":::

## <a name="next-steps"></a>Następne kroki

Aby obsługiwać problemy podczas zarządzania maszyną wirtualną, zobacz [Rozwiązywanie problemów z uruchamianiem/zatrzymywaniem maszyn wirtualnych v2](troubleshoot.md) (wersja zapoznawcza)