---
title: Przeglądanie dostępu do grup & aplikacji w ramach przeglądów dostępu — Azure AD
description: Dowiedz się, jak przejrzeć własny dostęp do grup lub aplikacji w Azure Active Directory przeglądy dostępu.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422403"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Przejrzyj dostęp do swoich grup lub aplikacji w przeglądach dostępu usługi Azure AD

Azure Active Directory (Azure AD) upraszczają, w jaki sposób przedsiębiorstwa zarządzają dostępem do grup lub aplikacji w usłudze Azure AD i innych usługach online firmy Microsoft przy użyciu funkcji o nazwie przeglądy dostępu usługi Azure AD.

W tym artykule opisano sposób przeglądania własnego dostępu do grupy lub aplikacji.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Pierwszym krokiem do przeprowadzenia przeglądu dostępu jest znalezienie i otwarcie przeglądu dostępu.

1. Poszukaj wiadomości e-mail od firmy Microsoft, która prosi o sprawdzenie dostępu. Oto przykładowa wiadomość e-mail, aby przejrzeć dostęp do grupy.

    ![Przykładowa wiadomość e-mail od firmy Microsoft w celu przejrzenia dostępu do grupy](./media/review-your-access/access-review-email.png)

1. Kliknij link **Przejrzyj dostęp** , aby otworzyć przegląd dostępu.

Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące przeglądy dostępu, wykonując następujące kroki.

1. Zaloguj się do portalu aplikacji w witrynie [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portal aplikacji z listą aplikacji, do których masz uprawnienia](./media/review-your-access/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. Po prawej stronie kliknij kafelek **przeglądy dostępu** , aby wyświetlić listę oczekujących przeglądów dostępu.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista oczekujących przeglądów dostępu dla aplikacji i grup](./media/review-your-access/access-reviews-list.png)

1. Kliknij link **Rozpocznij przegląd** , aby zapoznać się z przeglądem dostępu, który chcesz wykonać.

## <a name="perform-the-access-review"></a>Wykonaj przegląd dostępu

Po otwarciu przeglądu dostępu zobaczysz Twój dostęp.

1. Przejrzyj dostęp i zdecyduj, czy nadal potrzebujesz dostępu.

    Jeśli żądanie dotyczy przeglądania dostępu dla innych osób, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [Przegląd dostępu do grup lub aplikacji](perform-access-review.md).

    ![Otwórz przegląd dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/perform-access-review.png)

1. Kliknij przycisk **tak** , aby zachować dostęp, lub kliknij przycisk **nie** , aby usunąć dostęp.

1. Jeśli klikniesz przycisk **tak**, w polu **Przyczyna** może być konieczne określenie uzasadnienia.

    ![Zakończono przegląd dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/perform-access-review-submit.png)

1. Kliknij przycisk **Prześlij**.

    Twój wybór zostanie przesłany i wrócisz do portalu aplikacji.

    Jeśli chcesz zmienić odpowiedź, ponownie otwórz stronę przeglądy dostępu i zaktualizuj odpowiedź. Odpowiedź można zmienić w dowolnym momencie do momentu zakończenia przeglądu dostępu.

    > [!NOTE]
    > Jeśli użytkownik wykazał, że nie potrzebujesz już dostępu, nie zostanie natychmiast usunięty. Użytkownik jest usuwany po zakończeniu przeglądu lub gdy administrator zatrzyma przegląd.

## <a name="next-steps"></a>Następne kroki

- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
