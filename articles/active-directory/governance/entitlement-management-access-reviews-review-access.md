---
title: Przeglądanie dostępu do pakietu dostępu w usłudze Azure AD uprawnienia zarządzania
description: Dowiedz się, jak przeprowadzić przegląd dostępu do pakietów dostępu do zarządzania prawami w Azure Active Directory przeglądy dostępu (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798601"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Przeglądanie dostępu do pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Zarządzanie prawami usługi Azure AD upraszcza zarządzanie dostępem do grup, aplikacji i witryn programu SharePoint przez przedsiębiorstwa. W tym artykule opisano, jak przeprowadzać przeglądy dostępu dla innych użytkowników przypisanych do pakietu dostępu jako wyznaczonego recenzenta.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejrzeć przypisania aktywnego pakietu dostępu użytkowników, musisz spełnić wymagania wstępne, aby przeprowadzić przegląd dostępu:
- Usługa Azure AD — wersja Premium P2
- Administrator globalny
- Wyznaczeni administrator, właściciel katalogu lub Menedżer pakietów dostępu

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Aby znaleźć i otworzyć przegląd dostępu, wykonaj następujące kroki:

1. Otrzymasz od firmy Microsoft wiadomość e-mail z prośbą o sprawdzenie dostępu. Znajdź wiadomość e-mail, aby otworzyć przegląd dostępu. Oto przykład wiadomości e-mail do sprawdzenia dostępu:
    
    ![Adres e-mail recenzenta przeglądu dostępu](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Kliknij link **Przejrzyj dostęp użytkownika** , aby otworzyć przegląd dostępu. 

1. Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące przeglądy dostępu, przechodząc bezpośrednio do https://myaccess.microsoft.com .  (W przypadku instytucji rządowych USA Użyj `https://myaccess.microsoft.us` zamiast niej).

1. Kliknij przycisk **przeglądy dostępu** na lewym pasku nawigacyjnym, aby wyświetlić listę oczekujących przeglądów dostępu przypisanych do użytkownika.
    
    ![Wybieranie opcji przeglądy dostępu w ramach mojego dostępu](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Kliknij przegląd, którego chcesz zacząć.
    
    ![Wybierz przegląd dostępu](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Wykonaj przegląd dostępu

Po otwarciu przeglądu dostępu zobaczysz nazwy użytkowników, których należy przejrzeć. Istnieją dwa sposoby zatwierdzenia lub odmowy dostępu:
- Możesz ręcznie zatwierdzać lub odmówić dostępu dla jednego lub kilku użytkowników
- Możesz zaakceptować zalecenia dotyczące systemu

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Ręcznie Zatwierdź lub Odmów dostępu dla co najmniej jednego użytkownika
1. Przejrzyj listę użytkowników i ustal, którzy użytkownicy muszą nadal mieć dostęp.

    ![Lista użytkowników do przejrzenia](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Aby zatwierdzić lub odmówić dostępu, wybierz przycisk radiowy po lewej stronie nazwy użytkownika.

1. Na pasku powyżej nazw użytkowników wybierz pozycję **Zatwierdź** lub **Odmów** .

    ![Wybierz użytkownika](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Jeśli nie masz pewności, możesz kliknąć przycisk **nie zna** .

    Po wybraniu tej opcji użytkownik będzie miał dostęp, a wybór zostanie przełączony do dzienników inspekcji. W dzienniku są wyświetlane inni Recenzenci, którzy nadal ukończyli przegląd.

1. Może być konieczne podanie przyczyny decyzji. Wpisz przyczynę i kliknij pozycję **Prześlij**.

    ![Zatwierdź lub Odmów dostępu](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Decyzję można zmienić w dowolnym momencie przed końcem przeglądu. Aby to zrobić, wybierz użytkownika z listy i Zmień decyzję. Na przykład możesz zatwierdzić dostęp dla użytkownika, który został wcześniej odrzucony.

Jeśli istnieje wielu recenzentów, zostanie zarejestrowana Ostatnia przesłana odpowiedź. Rozważmy przykład, w którym administrator wyznaczy dwóch recenzentów — Alicja i Robert. Alicja otwiera najpierw recenzję i zatwierdza dostęp. Przed zakończeniem przeglądu Robert otwiera przegląd i odmawia dostępu. W takim przypadku Ostatnia decyzja o odmowie dostępu zostanie zarejestrowana.

>[!NOTE]
>Jeśli użytkownik odmówi dostępu, nie zostanie natychmiast usunięty z pakietu dostępu. Użytkownik zostanie usunięty z pakietu dostępu po zakończeniu przeglądu lub administrator zakończy przegląd.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Zatwierdź lub Odmów dostępu przy użyciu zaleceń generowanych przez system

Aby szybciej przeglądać dostęp dla wielu użytkowników, możesz użyć zaleceń generowanych przez system, akceptując zalecenia jednym kliknięciem. Zalecenia są generowane na podstawie działania logowania użytkownika.

1.  Na pasku w górnej części strony kliknij pozycję **Zaakceptuj zalecenia**.
    
    ![Wybierz pozycję Zaakceptuj rekomendacje](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Zobaczysz podsumowanie zalecanych akcji.

1.  Kliknij pozycję **Prześlij** , aby zaakceptować zalecenia.

## <a name="next-steps"></a>Następne kroki

- [Samoprzegląd pakietów dostępu](entitlement-management-access-reviews-self-review.md)