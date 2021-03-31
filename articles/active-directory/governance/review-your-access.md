---
title: Przeglądanie dostępu do grup & aplikacji w ramach przeglądów dostępu — Azure AD
description: Dowiedz się, jak przejrzeć własny dostęp do grup lub aplikacji w Azure Active Directory przeglądy dostępu.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746783"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Przejrzyj dostęp do swoich grup lub aplikacji w przeglądach dostępu usługi Azure AD

Azure Active Directory (Azure AD) upraszczają, w jaki sposób przedsiębiorstwa zarządzają dostępem do grup lub aplikacji w usłudze Azure AD i innych usługach online firmy Microsoft przy użyciu funkcji o nazwie przeglądy dostępu usługi Azure AD.

W tym artykule opisano sposób przeglądania własnego dostępu do grupy lub aplikacji.

## <a name="review-your-access-using-my-apps"></a>Przeglądanie dostępu przy użyciu aplikacji Moje aplikacje

Pierwszym krokiem do przeprowadzenia przeglądu dostępu jest znalezienie i otwarcie przeglądu dostępu.

>[!IMPORTANT]
> W przypadku otrzymywania wiadomości e-mail mogą wystąpić opóźnienia, a w niektórych przypadkach może to potrwać do 24 godzin. Dodaj azure-noreply@microsoft.com do listy bezpiecznych adresatów, aby upewnić się, że otrzymujesz wszystkie wiadomości e-mail.

1. Poszukaj wiadomości e-mail od firmy Microsoft, która prosi o sprawdzenie dostępu. Oto przykładowa wiadomość e-mail, aby przejrzeć dostęp do grupy.

    ![Przykładowa wiadomość e-mail od firmy Microsoft w celu przejrzenia dostępu do grupy](./media/review-your-access/access-review-email.png)

1. Kliknij link **Przejrzyj dostęp** , aby otworzyć przegląd dostępu.

Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące przeglądy dostępu, wykonując następujące kroki.

1. Zaloguj się do portalu Moje aplikacje pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portal moje aplikacje zawiera listę aplikacji, do których masz uprawnienia](./media/review-your-access/myapps-access-panel.png)

1. W prawym górnym rogu strony kliknij symbol użytkownika, który wyświetla Twoją nazwę i domyślną organizację. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

1. Po prawej stronie kliknij kafelek **przeglądy dostępu** , aby wyświetlić listę oczekujących przeglądów dostępu.

    Jeśli kafelek nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji i w tej chwili jest wymagana żadna akcja.

    ![Lista oczekujących przeglądów dostępu dla aplikacji i grup](./media/review-your-access/access-reviews-list.png)

1. Kliknij link **Rozpocznij przegląd** , aby zapoznać się z przeglądem dostępu, który chcesz wykonać.

### <a name="perform-the-access-review"></a>Wykonaj przegląd dostępu

Po otwarciu przeglądu dostępu zobaczysz Twój dostęp.

1. Przejrzyj dostęp i zdecyduj, czy nadal potrzebujesz dostępu.

    Jeśli żądanie dotyczy przeglądania dostępu dla innych osób, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [Przegląd dostępu do grup lub aplikacji](perform-access-review.md).

    ![Zrzut ekranu pokazujący otwarty przegląd dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy.](./media/review-your-access/perform-access-review.png)

1. Kliknij przycisk **tak** , aby zachować dostęp, lub kliknij przycisk **nie** , aby usunąć dostęp.

1. Jeśli klikniesz przycisk **tak**, w polu **Przyczyna** może być konieczne określenie uzasadnienia.

    ![Zrzut ekranu pokazujący zakończony przegląd dostępu z pytaniem, czy nadal potrzebny jest dostęp do grupy z wybraną opcją "tak".](./media/review-your-access/perform-access-review-submit.png)

1. Kliknij przycisk **Prześlij**.

    Twój wybór zostanie przesłany i wrócisz do portalu My Apps.

    Jeśli chcesz zmienić odpowiedź, ponownie otwórz stronę przeglądy dostępu i zaktualizuj odpowiedź. Odpowiedź można zmienić w dowolnym momencie do momentu zakończenia przeglądu dostępu.

    > [!NOTE]
    > Jeśli użytkownik wykazał, że nie potrzebujesz już dostępu, nie zostanie natychmiast usunięty. Użytkownik jest usuwany po zakończeniu przeglądu lub gdy administrator zatrzyma przegląd.

## <a name="review-your-own-access-using-my-access-new"></a>Przeglądanie własnego dostępu przy użyciu dostępu (NOWOŚĆ)

Nowe środowisko można wypróbować za pomocą zaktualizowanego interfejsu użytkownika w moim dostępie na kilka różnych sposobów:

### <a name="my-apps-portal"></a>Portal moje aplikacje

1. Zaloguj się do portalu Moje aplikacje pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portal moje aplikacje zawiera listę aplikacji, do których masz uprawnienia](./media/review-your-access/myapps-access-panel.png)

2. Kliknij kafelek **przeglądy dostępu** , aby wyświetlić listę oczekujących przeglądów dostępu.

    > [!NOTE]
    > Jeśli kafelek **przeglądy dostępu** nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji, a w tej chwili nie jest wymagana żadna akcja.

3. Kliknij przycisk **Wypróbuj!** na transparencie w górnej części strony, aby przejść do nowego środowiska dostępu.

    ![Lista oczekujących przeglądów dostępu dla aplikacji i grup z nowym transparentem dostępnym w trakcie korzystania z wersji zapoznawczej](./media/review-your-access/banner-your-access.png)

4. Kontynuuj w sekcji **Przeprowadź przegląd dostępu**

### <a name="email"></a>E-mail

>[!IMPORTANT]
> W przypadku otrzymywania wiadomości e-mail mogą wystąpić opóźnienia, a w niektórych przypadkach może to potrwać do 24 godzin. Dodaj azure-noreply@microsoft.com do listy bezpiecznych adresatów, aby upewnić się, że otrzymujesz wszystkie wiadomości e-mail.

1. Poszukaj wiadomości e-mail od firmy Microsoft z prośbą o przegląd dostępu. Poniżej znajduje się Przykładowa wiadomość e-mail:

 ![Przykładowa wiadomość e-mail od firmy Microsoft w celu przejrzenia dostępu do grupy](./media/review-your-access/access-review-email-preview.png)

2. Kliknij link **Przejrzyj dostęp** , aby otworzyć przegląd dostępu.

3. Kontynuuj w sekcji **Przeprowadź przegląd dostępu**

>[!NOTE]
>Jeśli kliknięcie przycisku Rozpocznij przegląd spowoduje przejście do obszaru **Moje aplikacje** , wykonaj kroki opisane w sekcji poniżej zatytułowanej **Portal moje aplikacje**.

### <a name="directly-at-my-access"></a>Bezpośrednio w moim dostępie

Możesz również wyświetlić oczekujące przeglądy dostępu za pomocą przeglądarki, aby otworzyć stronę mój dostęp.

1. Zaloguj się do mojego dostępu przy użyciu https://myaccess.microsoft.com/

2. Wybierz pozycję **przeglądy dostępu** z menu na pasku po lewej stronie, aby wyświetlić listę oczekujących przeglądów dostępu przypisanych do użytkownika.

   ![przeglądy dostępu w menu](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Wykonaj przegląd dostępu

1. W obszarze grupy i aplikacje możesz zobaczyć:
    
    - **Nazwa** Nazwa przeglądu dostępu.
    - **Z powodu** Data ukończenia przeglądu. Po tym terminie nie można usunąć użytkowników z przeglądanej grupy lub aplikacji.
    - **Zasób** Nazwa zasobu w obszarze przegląd.
    - **Postęp** Liczba użytkowników zrecenzowanych na łączną liczbę użytkowników w ramach tego przeglądu dostępu.
    
2. Kliknij nazwę przeglądu dostępu, aby rozpocząć pracę.

   ![Lista oczekujących przeglądów dostępu dla aplikacji i grup](./media/review-your-access/access-reviews-list-preview.png)

3. Przejrzyj dostęp i zdecyduj, czy nadal potrzebujesz dostępu.

    Jeśli żądanie dotyczy przeglądania dostępu dla innych osób, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [Przegląd dostępu do grup lub aplikacji](perform-access-review.md).

    ![Otwórz przegląd dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/review-access-preview.png)

1. Wybierz pozycję **tak** , aby zachować dostęp, lub wybierz pozycję **nie** , aby usunąć dostęp.

1. Jeśli klikniesz przycisk **tak**, w polu **Przyczyna** może być konieczne określenie uzasadnienia.

    ![Zakończono przegląd dostępu z pytaniem, czy nadal potrzebujesz dostępu do grupy](./media/review-your-access/review-access-yes-preview.png)

1. Kliknij przycisk **Prześlij**.

    Wybór zostanie przesłany i nawrócisz do strony mój dostęp.

    Jeśli chcesz zmienić odpowiedź, ponownie otwórz stronę przeglądy dostępu i zaktualizuj odpowiedź. Odpowiedź można zmienić w dowolnym momencie do momentu zakończenia przeglądu dostępu.

    > [!NOTE]
    > Jeśli użytkownik wykazał, że nie potrzebujesz już dostępu, nie zostanie natychmiast usunięty. Użytkownik jest usuwany po zakończeniu przeglądu lub gdy administrator zatrzyma przegląd.

## <a name="next-steps"></a>Następne kroki

- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
