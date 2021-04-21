---
title: Znajdowanie nabywcy rezerwacji na Azure Monitor dzienników
description: Ten artykuł pomaga znaleźć nabywcę rezerwacji z informacjami z Azure Monitor dzienników.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773376"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Znajdowanie nabywcy rezerwacji w dziennikach platformy Azure

Ten artykuł ułatwia znalezienie nabywcy rezerwacji z informacjami z dzienników katalogu. Dzienniki katalogu z usługi Azure Monitor e-mail z identyfikatorami użytkowników, którzy dokonano zakupów rezerwacji.

## <a name="find-the-purchaser"></a>Znajdź nabywcę

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **monitora**  >  **aktywności dziennika**  >  **aktywności.**  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Zrzut ekranu przedstawiający nawigację do dziennika aktywności — aktywność." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Wybierz **pozycję Działanie katalogu**. Jeśli zostanie wyświetlony komunikat Potrzebujesz uprawnień do wyświetlania dzienników *na* poziomie katalogu, wybierz [link,](../../role-based-access-control/elevate-access-global-admin.md) aby dowiedzieć się, jak uzyskać uprawnienia.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Zrzut ekranu przedstawiający działanie katalogu bez uprawnień do wyświetlania dziennika." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Po masz uprawnienia, **odfiltruj dostawcę zasobów dzierżawy** za **pomocą microsoft.capacity.** Powinny zostać wyświetlony wszystkie zdarzenia związane z rezerwacją dla wybranego zakresu czasu. W razie potrzeby zmień zakres czasu.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Zrzut ekranu przedstawiający użytkownika, który kupił rezerwację." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    W razie potrzeby może być konieczne wybranie opcji **Edytuj kolumny** w celu wybrania **zdarzenia zainicjowanego przez polecenie**.
   Użytkownik, który dokonał zakupu rezerwacji, jest wyświetlany w obszarze **Zdarzenie zainicjowane przez .**

## <a name="next-steps"></a>Następne kroki

- W razie potrzeby administratorzy rozliczeń mogą [przejąć własność rezerwacji.](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations)
