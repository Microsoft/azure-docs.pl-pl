---
title: Powiadomienia o zaawansowaniu (wersja zapoznawcza) dla planowanych zdarzeń konserwacji
description: Pobierz powiadomienie przed planowaną konserwacją Azure SQL Database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 895b9081ba7eb6d7e8b5d3304d37168e4064ed39
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560050"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Powiadomienia z wyprzedzeniem dotyczące planowanych zdarzeń konserwacji (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Powiadomienia z wyprzedzeniem (wersja zapoznawcza) są dostępne dla baz danych skonfigurowanych pod kątem [okna obsługi (wersja zapoznawcza)](maintenance-window.md). Powiadomienia z wyprzedzeniem umożliwiają klientom Konfigurowanie powiadomień do wysłania do 24 godzin przed zaplanowanym wydarzeniem.

Powiadomienia można skonfigurować w taki sposób, aby można było otrzymywać teksty, wiadomości e-mail, powiadomienia wypychane platformy Azure oraz pocztę głosową, gdy planowana konserwacja będzie zaczynać się w ciągu najbliższych 24 godzin. Dodatkowe powiadomienia są wysyłane po rozpoczęciu konserwacji i po zakończeniu konserwacji.

> [!Note]
> Gdy możliwość wyboru okna obsługi jest dostępna dla wystąpień zarządzanych usługi Azure SQL, powiadomienia z wyprzedzeniem nie są obecnie dostępne dla wystąpień zarządzanych usługi Azure SQL.

## <a name="create-an-advance-notification"></a>Utwórz powiadomienie z wyprzedzeniem

W przypadku baz danych usługi Azure SQL, dla których skonfigurowano okno obsługi, są dostępne zaawansowane powiadomienia. 

Wykonaj następujące kroki, aby włączyć powiadomienie.  

1. Przejdź do strony [Planowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) , wybierz pozycję **alerty dotyczące kondycji**, a następnie **Dodaj alert kondycji usługi**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="opcja menu Utwórz nowy Alert kondycji":::

2. W sekcji **Akcje** wybierz pozycję **Dodaj grupy akcji**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="Dodaj grupę akcji — opcja menu":::

3. Wypełnij formularz **Tworzenie grupy akcji** , a następnie wybierz kolejno pozycje **Dalej: powiadomienia**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="Utwórz formularz grupy akcji":::

1. Na karcie **powiadomienia** wybierz **Typ powiadomienia**. Opcja **wiadomości e-mail/komunikatów SMS/wypychania/głosowej** zapewnia największą elastyczność i jest zalecaną opcją. Wybierz pióro, aby skonfigurować powiadomienie.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="Konfigurowanie powiadomień":::



   1. Wypełnij formularz *Dodaj lub Edytuj powiadomienie* , który zostanie otwarty, i wybierz pozycję **OK**: 

   2. Akcje i Tagi są opcjonalne. W tym miejscu możesz skonfigurować dodatkowe akcje, które mają być wyzwalane, lub używać tagów do kategoryzowania i organizowania zasobów platformy Azure. 

   4. Sprawdź szczegóły na karcie **Recenzja + tworzenie** i wybierz pozycję **Utwórz**. 

7. Po wybraniu pozycji Utwórz zostanie otwarty ekran Konfiguracja reguły alertu z wybraną grupą akcji. Nadaj nazwę nowej regule alertu, a następnie wybierz dla niej grupę zasobów i wybierz pozycję **Utwórz regułę alertu**. 

8. Kliknij ponownie element menu **alerty kondycji** , a lista alertów zawiera teraz nowy Alert. 


Wszystko jest gotowe. Następnym razem, gdy jest planowane zdarzenie konserwacji Azure SQL, otrzymasz powiadomienie z góry.

## <a name="receiving-notifications"></a>Odbieranie powiadomień

W poniższej tabeli przedstawiono powiadomienia ogólne-informacje, które mogą zostać wyświetlone: 

|Stan|Opis|
|:---|:---|
|**Planowane wdrożenie**| Odebrano 24 godziny przed zdarzeniem konserwacji. Konserwacja jest planowana w dniu między 17:00-8:00 (czas lokalny) dla bazy danych xyz.|
|**W toku** | Trwa uruchamianie obsługi bazy danych *XYZ*   .| 
|**Ukończ** | Konserwacja bazy danych *XYZ* została ukończona. |

W poniższej tabeli przedstawiono dodatkowe powiadomienia, które mogą być wysyłane w trakcie konserwacji: 

|Stan|Opis|
|:---|:---|
|**Dodatkowa pomoc techniczna** | Konserwacja jest w toku, ale nie została ukończona dla bazy danych *XYZ*. Konserwacja będzie kontynuowana w następnym oknie obsługi.| 
|**Anulowane**| Konserwacja bazy danych *XYZ* została anulowana i zostanie ponownie zaplanowana później. |
|**Zablokowany**|Wystąpił problem podczas konserwacji bazy danych *XYZ*. Powiadomimy Cię o tym po wznowieniu.| 
|**Wznowione**|Problem został rozwiązany, a konserwacja będzie kontynuowana w następnym oknie obsługi.|


## <a name="next-steps"></a>Następne kroki

- [Okno obsługi](maintenance-window.md)
- [Okno obsługi — często zadawane pytania](maintenance-window-faq.yml)
- [Przegląd alertów na platformie Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [Wyślij wiadomość e-mail do roli usługi Azure Resource Manager](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)