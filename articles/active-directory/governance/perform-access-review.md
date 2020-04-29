---
title: Przeglądanie dostępu do grup & aplikacji w ramach przeglądów dostępu — Azure AD
description: Dowiedz się, jak przeglądać dostęp do członków grupy lub dostępu do aplikacji w Azure Active Directory przeglądy dostępu.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128447"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Przeglądanie dostępu do grup i aplikacji w przeglądach dostępu do usługi Azure AD

Azure Active Directory (Azure AD) upraszczają, w jaki sposób przedsiębiorstwa zarządzają dostępem do grup i aplikacji w usłudze Azure AD i innych usługach online firmy Microsoft przy użyciu funkcji o nazwie przeglądy dostępu usługi Azure AD.

W tym artykule opisano, w jaki sposób wskazany recenzent wykonuje przegląd dostępu dla członków grupy lub użytkowników mających dostęp do aplikacji.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do przeprowadzenia przeglądu dostępu jest znalezienie i otwarcie przeglądu dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft, która prosi o sprawdzenie dostępu. Oto przykładowa wiadomość e-mail, aby przejrzeć dostęp do grupy.

    ![Przykładowa wiadomość e-mail od firmy Microsoft w celu przejrzenia dostępu do grupy](./media/perform-access-review/access-review-email.png)

1. Kliknij link **Rozpocznij przegląd** , aby otworzyć przegląd dostępu.

Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące przeglądy dostępu, wykonując następujące kroki.

1. Zaloguj się do portalu aplikacji w witrynie [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portal aplikacji z listą aplikacji, do których masz uprawnienia](./media/perform-access-review/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. Kliknij kafelek **przeglądy dostępu** , aby wyświetlić listę oczekujących przeglądów dostępu.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista oczekujących przeglądów dostępu dla aplikacji i grup](./media/perform-access-review/access-reviews-list.png)

1. Kliknij link **Rozpocznij przegląd** , aby zapoznać się z przeglądem dostępu, który chcesz wykonać.

## <a name="perform-the-access-review"></a>Wykonaj przegląd dostępu

Po otwarciu przeglądu dostępu zobaczysz nazwy użytkowników, którzy muszą być zrecenzowani.

Jeśli żądanie dotyczy sprawdzenia własnego dostępu, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [przeglądanie dostępu dla siebie do grup lub aplikacji](review-your-access.md).

![Otwórz przegląd dostępu, aby wyświetlić listę użytkowników, którzy muszą zostać zrecenzowani](./media/perform-access-review/perform-access-review.png)

Istnieją dwa sposoby zatwierdzenia lub odmowy dostępu:

- Możesz zatwierdzić lub odmówić dostępu dla jednego lub kilku użytkowników lub
- Możesz zaakceptować zalecenia dotyczące systemu, które jest najłatwiejszym i najszybszym sposobem.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Zatwierdź lub Odmów dostępu dla co najmniej jednego użytkownika

1. Przejrzyj listę użytkowników, aby zdecydować, czy chcesz zatwierdzić lub odrzucić dalszy dostęp.

1. Aby zatwierdzić lub odmówić dostępu dla jednego użytkownika, kliknij wiersz, aby otworzyć okno, aby określić akcję do wykonania. Aby zatwierdzić lub odmówić dostępu dla wielu użytkowników, należy dodać znaczniki wyboru obok użytkowników, a następnie kliknąć przycisk **Przejrzyj użytkownika X** , aby otworzyć okno, w którym można określić akcję do wykonania.

1. Kliknij przycisk **Zatwierdź** lub **Odmów**. Jeśli nie masz pewności, możesz kliknąć opcję **nieznane**. Spowoduje to, że użytkownik będzie utrzymywać dostęp, ale wybór zostanie odzwierciedlony w dziennikach inspekcji.

    ![Okno akcji, które obejmuje opcje Zatwierdź, Odmów i nieznane](./media/perform-access-review/approve-deny.png)

1. W razie potrzeby wprowadź przyczynę w polu **Przyczyna** .

    Administrator recenzji dostępu może wymagać podania przyczyny zatwierdzenia ciągłego dostępu lub członkostwa w grupie.

1. Po określeniu akcji do wykonania kliknij przycisk **Zapisz**.

    Jeśli chcesz zmienić odpowiedź, zaznacz wiersz i zaktualizuj odpowiedź. Na przykład możesz zatwierdzić wcześniej odmówiony użytkownika lub odmówić wcześniej zatwierdzonego użytkownika. Odpowiedź można zmienić w dowolnym momencie do momentu zakończenia przeglądu dostępu.

    Jeśli istnieje wielu recenzentów, zostanie zarejestrowana Ostatnia przesłana odpowiedź. Rozważmy przykład, w którym administrator wyznaczy dwóch recenzentów — Alicja i Robert. Alicja otwiera najpierw przegląd dostępu i zatwierdza dostęp. Przed zakończeniem przeglądu Robert otwiera przegląd dostępu i odmówi dostępu. Ostatnia odpowiedź odmowy to to, co jest rejestrowane.

    > [!NOTE]
    > Jeśli użytkownik odmówi dostępu, nie zostanie natychmiast usunięty. Są one usuwane po zakończeniu przeglądu lub gdy administrator zatrzyma przegląd.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Zatwierdzanie lub odrzucanie dostępu na podstawie zaleceń

Aby ułatwić Ci przeglądy dostępu, możesz także zatwierdzić zaleceń, które można zaakceptować przy użyciu jednego kliknięcia. Zalecenia są generowane na podstawie działania logowania użytkownika.

1. Na niebieskim pasku w dolnej części strony kliknij pozycję **Zaakceptuj zalecenia**.

    ![Otwórz listę przeglądów dostępu z przyciskiem Zaakceptuj zalecenia](./media/perform-access-review/accept-recommendations.png)

    Zobaczysz podsumowanie zalecanych akcji.

    ![Okno, które wyświetla podsumowanie zalecanych akcji](./media/perform-access-review/accept-recommendations-summary.png)

1. Kliknij przycisk **OK** , aby zaakceptować zalecenia.

## <a name="next-steps"></a>Następne kroki

- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
