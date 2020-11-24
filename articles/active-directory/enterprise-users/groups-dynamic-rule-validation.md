---
title: Sprawdzanie poprawności reguł członkostwa w grupie dynamicznej (wersja zapoznawcza) — Azure AD | Microsoft Docs
description: Jak przetestować elementy członkowskie względem reguły członkostwa dla grup dynamicznych w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e811cba1596d0bece4966ee5b02ecd9c2a3180cf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488911"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Sprawdzanie poprawności reguły członkostwa w grupie dynamicznej (wersja zapoznawcza) w Azure Active Directory

Azure Active Directory (Azure AD) zapewnia teraz metodę weryfikacji reguł grupy dynamicznej (w publicznej wersji zapoznawczej). Na karcie **Sprawdzanie poprawności reguł** można sprawdzić poprawność reguły dynamicznej względem przykładowych członków grupy, aby potwierdzić, że reguła działa zgodnie z oczekiwaniami. Podczas tworzenia lub aktualizowania zasad grupy dynamicznej Administratorzy chcą wiedzieć, czy użytkownik lub urządzenie będą członkiem grupy. Pomaga to w ocenie, czy użytkownik lub urządzenie spełnia kryteria reguły i pomoc w rozwiązywaniu problemów, gdy członkostwo nie jest oczekiwane.

## <a name="step-by-step-walk-through"></a>Przewodnik krok po kroku

Aby rozpocząć, przejdź do grup **Azure Active Directory**  >  **Groups**. Wybierz istniejącą grupę dynamiczną lub Utwórz nową grupę dynamiczną i kliknij pozycję reguły członkostwa dynamicznego. Następnie można wyświetlić kartę **reguły sprawdzania poprawności** .

![Znajdowanie karty reguły sprawdzania poprawności i rozpoczynanie pracy z istniejącą regułą](./media/groups-dynamic-rule-validation/validate-tab.png)

Na karcie **Weryfikowanie reguł** można wybrać użytkowników do sprawdzania ich członkostw. 20 użytkowników lub urządzeń można wybrać jednocześnie.

![Dodaj użytkowników, aby zweryfikować istniejącą regułę](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Po wybraniu opcji użytkownicy lub urządzenia z selektora, a **następnie wybraniu opcji** Walidacja zostanie automatycznie uruchomiona, a wyniki walidacji zostaną wyświetlone.

![Wyświetl wyniki weryfikacji reguły](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Wyniki wskazują, czy użytkownik jest członkiem grupy, czy nie. Jeśli reguła jest nieprawidłowa lub występuje problem z siecią, wynik będzie wyświetlany jako **nieznany**. W przypadku **nieznanego** szczegółowego komunikatu o błędzie opisano problem i akcje.

![Wyświetl szczegóły wyników weryfikacji reguły](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Można zmodyfikować regułę i sprawdzanie poprawności członkostw. Aby zobaczyć, dlaczego użytkownik nie jest członkiem grupy, kliknij pozycję "Wyświetl szczegóły" i szczegóły weryfikacji będą wyświetlane wyniki każdego wyrażenia składającego się z reguły. Kliknij przycisk **OK** , aby zakończyć.

## <a name="next-steps"></a>Następne kroki

- [Reguły członkostwa dynamicznego dla grup](groups-dynamic-membership.md)
