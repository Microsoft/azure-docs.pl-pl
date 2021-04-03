---
title: Kończenie przeglądu dostępu ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak wykonać przegląd dostępu ról usługi Azure AD w Azure AD Privileged Identity Management (PIM) i wyświetlić wyniki
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742220"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Dokończ przegląd dostępu ról usługi Azure AD w Privileged Identity Management

Administratorzy ról uprzywilejowanych mogą przeglądać dostęp uprzywilejowany po [rozpoczęciu przeglądu dostępu](pim-how-to-start-security-review.md).  Privileged Identity Management (PIM) automatycznie wyśle wiadomość e-mail do użytkowników w organizacji Azure Active Directory (Azure AD) z monitem o sprawdzenie dostępu. Jeśli użytkownik nie otrzymał wiadomości e-mail, można wysłać im instrukcje w temacie [Jak przeprowadzić przegląd dostępu](pim-how-to-perform-security-review.md).

Po przekroczeniu okresu przeglądu dostępu lub zakończeniu samooceny przez wszystkich użytkowników wykonaj kroki opisane w tym artykule, aby zarządzać przeglądem i zobaczyć wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

1. Przejdź do [Azure Portal](https://portal.azure.com/) i wybierz usługę **Azure AD Privileged Identity Management** na pulpicie nawigacyjnym.
1. Wybierz sekcję **przeglądy dostępu** na pulpicie nawigacyjnym.
1. Wybierz przegląd dostępu, który chcesz zarządzać.

W bloku szczegóły przeglądu dostępu dostępne są różne opcje zarządzania tym przeglądem.

![Przyciski przeglądu dostępu Privileged Identity Management — zrzut ekranu](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Monitujące

Jeśli przegląd dostępu zostanie skonfigurowany tak, aby użytkownicy sami przeglądali, przycisk **Przypomnij** wyśle powiadomienie.

### <a name="stop"></a>Stop

Wszystkie przeglądy dostępu mają datę końcową, ale możesz użyć przycisku **Zatrzymaj** , aby zakończyć go na początku. Jeśli żaden użytkownik nie został przejrzany w tym czasie, nie będzie mógł po zatrzymywać przeglądu. Po zatrzymaniu nie można ponownie uruchomić recenzji.

### <a name="apply"></a>Zastosuj

Po zakończeniu przeglądu dostępu, ponieważ osiągnięto datę końcową lub została zatrzymana ręcznie, przycisk **Zastosuj** implementuje wynik przeglądu. W przypadku odmowy dostępu użytkownika do przeglądu jest to krok, który spowoduje usunięcie przypisania roli.  

### <a name="export"></a>Eksportowanie

Jeśli chcesz zastosować wyniki przeglądu dostępu ręcznie, możesz wyeksportować przegląd. Przycisk **Eksportuj** rozpocznie pobieranie pliku CSV. Można zarządzać wynikami w programie Excel lub innych programach, które otwierają pliki CSV.

### <a name="delete"></a>Usuń

Jeśli nie interesuje Cię przegląd, usuń go. Przycisk **Usuń** usuwa przegląd z usługi Privileged Identity Management.

> [!IMPORTANT]
> Nie będzie wymagane potwierdzenie tej szkodliwej zmiany, dlatego sprawdź, czy chcesz usunąć ten przegląd.

## <a name="next-steps"></a>Następne kroki

- [Rozpocznij przegląd dostępu dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-start-security-review.md)
- [Przeprowadź przegląd dostępu do ról usługi Azure AD w Privileged Identity Management](pim-how-to-perform-security-review.md)
