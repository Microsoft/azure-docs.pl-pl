---
title: Monitorowanie i zarządzanie sesją usługi Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak wybrać trwającą sesję i wymusić jej odłączenie lub usunięcie.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980734"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorowanie sesji i zarządzanie nim w usłudze Azure bastionu

Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, można jej używać do bezproblemowego łączenia się z dowolną MASZYNą wirtualną w tej sieci wirtualnej. Gdy użytkownicy łączą się z obciążeniami, usługa Azure bastionu może służyć do monitorowania sesji zdalnych i wykonywania akcji szybkiego zarządzania. Monitorowanie sesji usługi Azure bastionu umożliwia przeglądanie użytkowników połączonych z maszynami wirtualnymi. Pokazuje on adres IP, z którego użytkownik nawiązał połączenie, jak długo zostały połączone i po nawiązaniu połączenia. Środowisko zarządzania sesjami umożliwia wybranie trwającej sesji i wymuszenie rozłączenia lub usunięcie sesji w celu rozłączenia użytkownika z trwającej sesji.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitoruj sesje zdalne

1. W [Azure Portal](https://portal.azure.com)przejdź do zasobu platformy Azure bastionu i wybierz pozycję **sesje** ze strony bastionu platformy Azure.

   ![Zrzut ekranu przedstawia ustawienia menu Azure Portal z wybranymi sesjami.](./media/session-monitoring/sessions.png)
2. Na stronie **sesje** można zobaczyć bieżące sesje zdalne po prawej stronie.

   ![Wyświetl sesję](./media/session-monitoring/view-session.png)
3. Wybierz pozycję **Odśwież** , aby wyświetlić zaktualizowaną listę sesji zdalnych. Po wybraniu opcji Odśwież usługa Azure bastionu będzie pobierać najnowsze informacje o monitorowaniu i odświeżyć je w portalu.

   ![Zrzut ekranu przedstawia sesję, która używa platformy Azure bastionu z wyróżnioną opcją odświeżenia.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Usuń lub Wymuś rozłączenie trwającej sesji zdalnej

Można wybrać zestaw sesji i wymusić ich rozłączenie. Poniższe kroki pokazują, jak usunąć sesje zdalne:

1. Przejdź do zasobu usługi Azure bastionu i wybierz pozycję **sesje** ze strony bastionu platformy Azure.

   ![Zrzut ekranu przedstawia Azure Portal z sesjami wybranymi w obszarze Ustawienia.](./media/session-monitoring/navigate.png)
2. Po wybraniu sesji zostanie wyświetlona lista sesji zdalnych.

   ![Wyświetl sesje](./media/session-monitoring/list.png)
3. Wybierz konkretną sesję zdalną, a następnie wybierz trzy wielokropek po prawej stronie wiersza sesji, a następnie wybierz pozycję **Usuń**.

   ![Zrzut ekranu przedstawia Azure Portal wyświetlania sesji z wybraną ikoną usuwania.](./media/session-monitoring/delete.png)
4. Po wybraniu opcji Usuń Sesja zdalna zostanie rozłączona, a użytkownik zobaczy komunikat "nastąpiło odłączenie" w sesji zdalnej.

   ![Zrzut ekranu przedstawia komunikat z weryfikacją, że został odłączony.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).
