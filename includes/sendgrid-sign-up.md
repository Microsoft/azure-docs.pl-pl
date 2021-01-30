---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214932"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>Aby utworzyć konto usługi SendGrid
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W menu Azure Portal lub stronie głównej wybierz pozycję **Utwórz zasób**.

    ![Zrzut ekranu przedstawiający menu Azure Portal z wybraną opcją Utwórz zasób.][command-bar-new]
3. Wyszukaj i wybierz pozycję **SendGrid**.

    ![Zrzut ekranu przedstawiający ekran Azure Portal Marketplace z informacjami o wartości "SendGr" w polu wyszukiwania i SendGrid wybranym w wynikach wyszukiwania.][sendgrid-store]
4. Wypełnij formularz rejestracji i wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowego konta SendGrid z wypełnionymi polami nazwy, hasła, subskrypcji i grupy zasobów.][sendgrid-create]
5. Wprowadź **nazwę** identyfikującą usługę SendGrid w ustawieniach platformy Azure. Nazwa musi mieć długość od 1 do 100 znaków i może zawierać tylko znaki alfanumeryczne, łączniki, kropki i znaki podkreślenia. Nazwa musi być unikatowa w obrębie subskrybowanych elementów sklepu Azure Store.
6. Wprowadź i potwierdź **hasło**.
7. Wybierz **subskrypcję**.
8. Utwórz nową **grupę zasobów** lub wybierz istniejącą.
9. W sekcji **Warstwa cenowa** wybierz plan usługi SendGrid, w ramach którego chcesz utworzyć konto.

    ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowego konta SendGrid z otwartą sekcją Wybieranie warstwy cenowej][sendgrid-pricing]
10. Wprowadź **kod promocyjny**, jeśli go masz.
11. Wprowadź **informacje kontaktowe**.
12. Zapoznaj się z **postanowieniami prawnymi** i zaakceptuj je.
13. Po potwierdzeniu zakupu zobaczysz okno podręczne **wdrożenia** , które zostanie wyświetlone i zobaczysz Twoje konto.

    ![Zrzut ekranu przedstawiający stronę kont SendGrid z wyświetlonym nowym kontem ContosoSendGrid.][all-resources]

    Po zakończeniu zakupu i kliknięciu przycisku **Zarządzaj** w celu rozpoczęcia procesu weryfikacji adresu e-mail otrzymasz wiadomość e-mail od zespołu SendGrid z monitem o potwierdzenie konta. Jeśli nie otrzymasz tej wiadomości e-mail lub masz problemy z weryfikacją konta, zapoznaj się z naszymi często zadawanymi pytaniami.

    ![Zrzut ekranu strony konta ContosoSendGrid z wyróżnionym przyciskiem Zarządzaj.][manage]

    **Do momentu zweryfikowania konta możesz wysyłać maksymalnie 100 wiadomości e-mail dziennie.**

    Aby zmodyfikować plan subskrypcji lub wyświetlić ustawienia kontaktowe usługi SendGrid, kliknij nazwę usługi SendGrid w celu otwarcia pulpitu nawigacyjnego witryny SendGrid Marketplace.

    ![Zrzut ekranu przedstawiający, że strona ustawienia dla konta ContosoSendGrid jest otwierana, wybierając pozycję Wszystkie ustawienia na stronie konta ContosoSendGrid.][settings]

    Aby wysłać wiadomość e-mail przy użyciu usługi SendGrid, musisz podać klucz interfejsu API.

### <a name="to-find-your-sendgrid-api-key"></a>Aby znaleźć klucz interfejsu API usługi SendGrid
1. Kliknij pozycję **Zarządzaj**.

    ![Zrzut ekranu strony konta ContosoSendGrid z wyróżnionym przyciskiem Zarządzaj.][manage]
2. W pulpicie nawigacyjnym usługi SendGrid wybierz pozycję **Ustawienia**, a następnie pozycję **Klucze interfejsu API** w menu po lewej stronie.

    ![Zrzut ekranu pulpitu nawigacyjnego SendGrid z otwartym listą rozwijaną i wybranymi kluczami interfejsu API.][api-keys]

3. Kliknij przycisk **Utwórz klucz interfejsu API**.

    ![Zrzut ekranu przedstawiający ekran klucze interfejsu API z wybranym przyciskiem Utwórz klucz interfejsu API.][general-api-key]
4. Musisz podać **nazwę klucza** i przyznać pełen dostęp w wierszu **Wysyłanie poczty**. Wybierz przycisk **Zapisz**.

    ![Zrzut ekranu przedstawiający ekran Dodaj nowy ogólny klucz interfejsu API z opcją Wyślij do pełnego dostępu, zaplanowane wysłanie wartości brak dostępu i wyróżniony przycisk Zapisz.][access]
5. Po wykonaniu tych czynności zostanie wyświetlony klucz interfejsu API. Zapisz go w bezpiecznym miejscu.

### <a name="to-find-your-sendgrid-credentials"></a>Aby znaleźć poświadczenia usługi SendGrid
1. Kliknij ikonę klucza, aby wyświetlić swoją **nazwę użytkownika**.

    ![Zrzut ekranu przedstawiający stronę konta ContosoSendGrid z wyróżnioną ikoną klucza.][key]
2. Hasło zostało wcześniej ustawione podczas konfiguracji. Aby wprowadzić zmiany, możesz wybrać opcję **Zmień hasło** lub **Resetuj hasło**.

Aby zarządzać ustawieniami dostarczania poczty e-mail, kliknij przycisk **Zarządzaj**. Spowoduje to przekierowanie do pulpitu nawigacyjnego SendGrid.

![Zrzut ekranu strony konta ContosoSendGrid z wyróżnionym przyciskiem Zarządzaj.][manage]

Aby uzyskać więcej informacji na temat wysyłania wiadomości e-mail za pośrednictwem usługi SendGrid, przejdź do [omówienia interfejsu API poczty e-mail][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
