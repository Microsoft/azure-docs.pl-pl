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
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5cd7a5737b2d13e63eabbbddd076cfc7aff83ac
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746800"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Przeglądanie dostępu do grup i aplikacji w przeglądach dostępu do usługi Azure AD

Azure Active Directory (Azure AD) upraszczają, w jaki sposób przedsiębiorstwa zarządzają dostępem do grup i aplikacji w usłudze Azure AD i innych usługach online firmy Microsoft przy użyciu funkcji o nazwie przeglądy dostępu usługi Azure AD. Ten artykuł zawiera informacje o tym, w jaki sposób wskazany recenzent wykonuje przegląd dostępu dla członków grupy lub użytkowników mających dostęp do aplikacji. Jeśli chcesz przejrzeć dostęp do pakietu, przeczytaj informacje [o dostępie do przeglądu pakietu dostępu w usłudze Azure AD uprawnienia do zarządzania](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Przeprowadź przegląd dostępu przy użyciu moich aplikacji

Proces przeglądu dostępu można uruchomić z powiadomienia e-mail lub bezpośrednio do witryny.

- **Adres e-mail**:

>[!IMPORTANT]
> W przypadku otrzymywania wiadomości e-mail mogą wystąpić opóźnienia, a w niektórych przypadkach może to potrwać do 24 godzin. Dodaj azure-noreply@microsoft.com do listy bezpiecznych adresatów, aby upewnić się, że otrzymujesz wszystkie wiadomości e-mail.

1. Poszukaj wiadomości e-mail od firmy Microsoft z prośbą o przegląd dostępu. Oto przykładowa wiadomość e-mail, aby przejrzeć dostęp do grupy.

    ![Zrzut ekranu pokazujący przykładową wiadomość e-mail od firmy Microsoft w celu przejrzenia dostępu do grupy.](./media/perform-access-review/access-review-email.png)

1. Kliknij link **Rozpocznij przegląd** , aby otworzyć przegląd dostępu.

- **Jeśli nie masz wiadomości e-mail**, możesz znaleźć oczekujące przeglądy dostępu, wykonując następujące kroki.

    1. Zaloguj się do portalu Moje aplikacje pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![Portal moje aplikacje zawiera listę aplikacji, do których masz uprawnienia](./media/perform-access-review/myapps-access-panel.png)

    1. W prawym górnym rogu strony kliknij użytkownika obok swojej nazwy i domyślnej organizacji. Jeśli na liście jest więcej niż jedna organizacja, wybierz tę, która zażądała przeglądu dostępu.

    1. Kliknij kafelek **przeglądy dostępu** , aby wyświetlić listę oczekujących przeglądów dostępu.

        > [!NOTE]
        > Jeśli kafelek **przeglądy dostępu** nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji, a w tej chwili nie jest wymagana żadna akcja.

        ![Zrzut ekranu pokazujący listę oczekujących przeglądów dostępu dla aplikacji i grup.](./media/perform-access-review/access-reviews-list.png)

    1. Kliknij link **Rozpocznij przegląd** , aby zapoznać się z przeglądem dostępu, który chcesz wykonać.

Po otwarciu przeglądu dostępu zobaczysz nazwy użytkowników, którzy muszą mieć przejrzane prawa dostępu.

Jeśli żądanie dotyczy sprawdzenia własnego dostępu, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [przeglądanie dostępu dla siebie do grup lub aplikacji](review-your-access.md).

![Otwórz przegląd dostępu, aby wyświetlić listę użytkowników do przejrzenia](./media/perform-access-review/perform-access-review.png)

Istnieją dwa sposoby zatwierdzenia lub odmowy dostępu:

- Możesz zatwierdzić lub odmówić dostępu dla jednego lub kilku użytkowników ręcznie, wybierając odpowiednią akcję dla każdego żądania użytkownika.
- Możesz zaakceptować zalecenia systemowe.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Zatwierdź lub Odmów dostępu dla co najmniej jednego użytkownika

1. Przejrzyj listę użytkowników i zdecyduj, czy chcesz zatwierdzić lub odrzucić dalszy dostęp.

    - Aby zatwierdzić lub odmówić dostępu dla jednego użytkownika, kliknij wiersz, aby otworzyć okno, aby określić akcję do wykonania. 
    - Aby zatwierdzić lub odmówić dostępu dla wielu użytkowników, należy dodać znaczniki wyboru obok użytkowników, a następnie kliknąć przycisk **Przejrzyj użytkownika X** , aby otworzyć okno, w którym można określić akcję do wykonania.

1. Kliknij przycisk **Zatwierdź** lub **Odmów**. 

    ![Okno akcji, które obejmuje opcje Zatwierdź, Odmów i nieznane](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Jeśli nie masz pewności, możesz kliknąć opcję **nieznane**. a użytkownik będzie mieć dostęp, a wybór zostanie zarejestrowany w dziennikach inspekcji.

1. Administrator recenzji dostępu może wymagać podania przyczyny w polu **Przyczyna** decyzji. Nawet wtedy, gdy powód nie jest wymagany. Nadal możesz podać przyczynę swojej decyzji, a dołączone informacje będą dostępne dla innych recenzentów.

1. Po określeniu akcji do wykonania kliknij przycisk **Zapisz**.

    >[!NOTE]
    > Odpowiedź można zmienić w dowolnym momencie przed zakończeniem przeglądu dostępu. Jeśli chcesz zmienić odpowiedź, zaznacz wiersz i zaktualizuj odpowiedź. Na przykład możesz zatwierdzić wcześniej odmówiony użytkownika lub odmówić wcześniej zatwierdzonego użytkownika.

    >[!IMPORTANT]
    > - Jeśli użytkownik odmówi dostępu, nie zostanie natychmiast usunięty. Są one usuwane po zakończeniu okresu przeglądu lub gdy administrator zatrzyma przegląd, jeśli włączono [funkcję autoapply](complete-access-review.md#apply-the-changes) .
    > - Jeśli istnieje wielu recenzentów, zostanie zarejestrowana Ostatnia przesłana odpowiedź. Rozważmy przykład, w którym administrator wyznaczy dwóch recenzentów — Alicja i Robert. Alicja otwiera najpierw przegląd dostępu i zatwierdza żądanie dostępu użytkownika. Przed zakończeniem okresu przeglądu Robert otwiera przegląd dostępu i odmawia dostępu do tego samego żądania, które zostało wcześniej zatwierdzone przez Alicja. Ostatnia decyzja o odrzuceniu dostępu to odpowiedź, która jest rejestrowana.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Zatwierdzanie lub odrzucanie dostępu na podstawie zaleceń

Aby ułatwić Ci przeglądy dostępu, możesz także zatwierdzić zaleceń, które można zaakceptować przy użyciu jednego kliknięcia. Zalecenia są generowane na podstawie działania logowania użytkownika.

1. Na niebieskim pasku w dolnej części strony kliknij pozycję **Zaakceptuj zalecenia**.

    ![Zrzut ekranu pokazujący otwartą listę przeglądów dostępu z wybranym przyciskiem "Akceptuj rekomendacje".](./media/perform-access-review/accept-recommendations.png)

    Zobaczysz podsumowanie zalecanych akcji.

    ![Okno, które wyświetla podsumowanie zalecanych akcji](./media/perform-access-review/accept-recommendations-summary.png)

1. Kliknij przycisk **OK** , aby zaakceptować zalecenia.

## <a name="perform-access-review-using-my-access-new"></a>Przeprowadź przegląd dostępu przy użyciu mojego dostępu (NOWOŚĆ)

Możesz przejść do nowego środowiska recenzenta przy użyciu zaktualizowanego interfejsu użytkownika w moim dostępie do kilku różnych sposobów:

### <a name="my-apps-portal"></a>Portal moje aplikacje

1. Zaloguj się do moich aplikacji pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portal moje aplikacje zawiera listę aplikacji, do których masz uprawnienia](./media/perform-access-review/myapps-access-panel.png)

2. Kliknij kafelek **przeglądy dostępu** , aby wyświetlić listę oczekujących przeglądów dostępu.

    > [!NOTE]
    > Jeśli kafelek **przeglądy dostępu** nie jest widoczny, nie ma żadnych przeglądów dostępu do wykonania dla tej organizacji, a w tej chwili nie jest wymagana żadna akcja.

![Lista oczekujących przeglądów dostępu dla aplikacji i grup z nowym transparentem dostępnym w trakcie korzystania z wersji zapoznawczej](./media/perform-access-review/banner.png)

3. Kliknij przycisk **Wypróbuj!** na banerze u góry strony. Spowoduje to przejście do nowego środowiska dostępu.
  
### <a name="email"></a>E-mail

  >[!IMPORTANT]
> W przypadku otrzymywania wiadomości e-mail mogą wystąpić opóźnienia, a w niektórych przypadkach może to potrwać do 24 godzin. Dodaj azure-noreply@microsoft.com do listy bezpiecznych adresatów, aby upewnić się, że otrzymujesz wszystkie wiadomości e-mail.

   1. Poszukaj wiadomości e-mail od firmy Microsoft z prośbą o przegląd dostępu. Poniżej znajduje się Przykładowa wiadomość e-mail:

   ![Przykładowa wiadomość e-mail od firmy Microsoft w celu przejrzenia dostępu do grupy](./media/perform-access-review/access-review-email-preview.png)

   2. Kliknij link **Rozpocznij przegląd** , aby otworzyć przegląd dostępu.

>[!NOTE]
>Jeśli kliknięcie przycisku Rozpocznij przegląd spowoduje przejście do obszaru **Moje aplikacje** , wykonaj kroki opisane w sekcji poniżej zatytułowanej **Portal moje aplikacje**.

### <a name="navigate-to-my-access-directly"></a>Przejdź bezpośrednio do mojego dostępu

Możesz również wyświetlić oczekujące przeglądy dostępu za pomocą przeglądarki, aby otworzyć stronę mój dostęp.

1. Zaloguj się do mojego dostępu przy użyciu https://myaccess.microsoft.com/

2. Wybierz pozycję **przeglądy dostępu** z menu na pasku po lewej stronie, aby wyświetlić listę oczekujących przeglądów dostępu przypisanych do użytkownika.

   ![przeglądy dostępu w menu](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Zatwierdź lub Odmów dostępu dla co najmniej jednego użytkownika

Po otwarciu dostępu w obszarze grupy i aplikacje można zobaczyć:

- **Nazwa** Nazwa przeglądu dostępu.
- **Z powodu** Data ukończenia przeglądu. Po tym terminie nie można usunąć użytkowników z przeglądanej grupy lub aplikacji.
- **Zasób** Nazwa zasobu w obszarze przegląd.
- **Postęp** Liczba użytkowników zrecenzowanych na łączną liczbę użytkowników w ramach tego przeglądu dostępu.

Kliknij nazwę przeglądu dostępu, aby rozpocząć pracę.

![Lista oczekujących przeglądów dostępu dla aplikacji i grup](./media/perform-access-review/access-reviews-list-preview.png)

Po otwarciu zostanie wyświetlona lista użytkowników w zakresie dla przeglądu dostępu. Jeśli żądanie dotyczy sprawdzenia własnego dostępu, strona będzie wyglądać inaczej. Aby uzyskać więcej informacji, zobacz [przeglądanie dostępu dla siebie do grup lub aplikacji](review-your-access.md).

Istnieją dwa sposoby zatwierdzenia lub odmowy dostępu:

- Można ręcznie zatwierdzać lub odmówić dostępu dla jednego lub wielu użytkowników.
- Możesz zaakceptować zalecenia systemowe.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Ręcznie Zatwierdź lub Odmów dostępu dla co najmniej jednego użytkownika

1. Przejrzyj listę użytkowników i zdecyduj, czy chcesz zatwierdzić lub odrzucić dalszy dostęp.
2. Wybierz co najmniej jednego użytkownika, klikając okrąg obok jego nazw.
3. Na pasku powyżej wybierz pozycję **Zatwierdź** lub **Odmów** .
    - Jeśli nie masz pewności, możesz kliknąć opcję **nieznane**. Użytkownik uzyskuje dostęp, a wybór zostanie zarejestrowany w dziennikach inspekcji. Należy pamiętać, że wszystkie udostępniane informacje będą dostępne dla innych recenzentów. Mogą odczytywać Komentarze i uwzględniać je podczas przeglądania żądania.

    ![Otwórz przegląd dostępu, aby wyświetlić listę użytkowników, którzy potrzebują przeglądu](./media/perform-access-review/user-list-preview.png)

4. Administrator recenzji dostępu może wymagać podania przyczyny w polu **Przyczyna** decyzji. Nawet wtedy, gdy powód nie jest wymagany. Nadal możesz podać przyczynę swojej decyzji, a dołączone informacje będą dostępne dla innych osób zatwierdzających do przeglądu.

5. Kliknij przycisk **Prześlij**.
    - Odpowiedź można zmienić w dowolnym momencie do momentu zakończenia przeglądu dostępu. Jeśli chcesz zmienić odpowiedź, zaznacz wiersz i zaktualizuj odpowiedź. Na przykład możesz zatwierdzić wcześniej odmówiony użytkownika lub odmówić wcześniej zatwierdzonego użytkownika.

 >[!IMPORTANT]
 > - Jeśli użytkownik odmówi dostępu, nie zostanie natychmiast usunięty. Są one usuwane po zakończeniu okresu przeglądu lub gdy administrator zatrzyma przegląd. 
 > - Jeśli istnieje wielu recenzentów, zostanie zarejestrowana Ostatnia przesłana odpowiedź. Rozważmy przykład, w którym administrator wyznaczy dwóch recenzentów — Alicja i Robert. Alicja otwiera najpierw przegląd dostępu i zatwierdza żądanie dostępu użytkownika. Przed zakończeniem okresu przeglądu Robert otwiera przegląd dostępu i odmawia dostępu do tego samego żądania, które zostało wcześniej zatwierdzone przez Alicja. Ostatnia decyzja o odrzuceniu dostępu to odpowiedź, która jest rejestrowana.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Zatwierdzanie lub odrzucanie dostępu na podstawie zaleceń

Aby ułatwić Ci przeglądy dostępu, możesz także zatwierdzić zaleceń, które można zaakceptować przy użyciu jednego kliknięcia. Zalecenia są generowane na podstawie działania logowania użytkownika.

1. Wybierz co najmniej jednego użytkownika, a następnie kliknij przycisk **Zaakceptuj zalecenia**.

    ![Otwórz listę przeglądów dostępu z przyciskiem Zaakceptuj zalecenia](./media/perform-access-review/accept-recommendations-preview.png)

1. Kliknij pozycję **Prześlij** , aby zaakceptować zalecenia.

Aby zaakceptować zalecenia dla wszystkich użytkowników, upewnij się, że nikt nie jest zaznaczony, i kliknij przycisk **Zaakceptuj rekomendacje** na górnym pasku.

>[!NOTE]
>Po zaakceptowaniu zaleceń powyższe decyzje nie zostaną zmienione.

## <a name="next-steps"></a>Następne kroki

- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
