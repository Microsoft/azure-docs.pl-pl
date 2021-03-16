---
title: Znajdź zakup rezerwacji z dzienników Azure Monitor
description: Ten artykuł pomaga znaleźć zakup rezerwacji z informacjami z dzienników Azure Monitor.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: eedb5e7a55b50a353fd16498500b891e289e61e5
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477056"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Znajdź zakup rezerwacji z dzienników platformy Azure

Ten artykuł pomaga znaleźć zakup rezerwacji z informacjami z dzienników katalogów. Dzienniki katalogów z Azure Monitor przedstawiają identyfikatory poczty e-mail użytkowników, którzy zakupili rezerwację.

## <a name="find-the-purchaser"></a>Znajdź kupującego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **monitorowania**  >  **aktywności dziennika aktywności**  >  .  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Zrzut ekranu przedstawiający nawigację do działania dziennika aktywności." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Wybierz pozycję **aktywność katalogu**. Jeśli zostanie wyświetlony komunikat z informacją, *że potrzebujesz uprawnień do wyświetlania dzienników na poziomie katalogu*, wybierz [link](../../role-based-access-control/elevate-access-global-admin.md) , aby dowiedzieć się, jak uzyskać uprawnienia.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Zrzut ekranu przedstawiający aktywność katalogu bez uprawnień do wyświetlania dziennika." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Gdy masz odpowiednie uprawnienia, przefiltruj **dostawcę zasobów dzierżawcy** z **firmą Microsoft. Pojemność**. Wszystkie zdarzenia związane z rezerwacją powinny być widoczne dla wybranego przedziału czasu. W razie konieczności zmień zakres czasu.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Zrzut ekranu przedstawiający użytkownika, który zakupił rezerwację." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    W razie potrzeby może być konieczne **Edytowanie kolumn** w celu wybrania **zdarzenia zainicjowanego przez** program.
   Użytkownik, który dokonał zakupu rezerwacji, jest widoczny w obszarze **zdarzenie zainicjowane przez**.

## <a name="next-steps"></a>Następne kroki

- W razie potrzeby Administratorzy rozliczeń mogą [przejąć na własność rezerwację](view-reservations.md#how-billing-administrators-view-or-manage-reservations).