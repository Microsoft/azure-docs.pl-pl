---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: e38cecfe206f21f9189493e7ed6e8f0cadda9cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "75463546"
---
W każdym miesiącu klienci platformy Azure mogą odblokować 25 000 bezpłatnych wiadomości e-mail. Te 25 000 bezpłatne miesięczne wiadomości e-mail zapewniają dostęp do zaawansowanych raportów i analiz oraz [wszystkich interfejsów API][all APIs] (sieci Web, SMTP, Event, Parse itd.). Informacje o dodatkowych usługach SendGrid można znaleźć na stronie z [rozwiązaniami usługi SendGrid][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Aby utworzyć konto usługi SendGrid
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W menu Azure Portal lub stronie głównej wybierz pozycję **Utwórz zasób**.

    ![command-bar-new][command-bar-new]
3. Wyszukaj i wybierz pozycję **SendGrid**.

    ![sendgrid-store][sendgrid-store]
4. Wypełnij formularz rejestracji i wybierz pozycję **Utwórz**.

    ![sendgrid-create][sendgrid-create]
5. Wprowadź **nazwę** identyfikującą usługę SendGrid w ustawieniach platformy Azure. Nazwa musi mieć długość od 1 do 100 znaków i może zawierać tylko znaki alfanumeryczne, łączniki, kropki i znaki podkreślenia. Nazwa musi być unikatowa w obrębie subskrybowanych elementów sklepu Azure Store.
6. Wprowadź i potwierdź **hasło**.
7. Wybierz **subskrypcję**.
8. Utwórz nową **grupę zasobów** lub wybierz istniejącą.
9. W sekcji **Warstwa cenowa** wybierz plan usługi SendGrid, w ramach którego chcesz utworzyć konto.

    ![sendgrid-pricing][sendgrid-pricing]
10. Wprowadź **kod promocyjny**, jeśli go masz.
11. Wprowadź **informacje kontaktowe**.
12. Zapoznaj się z **postanowieniami prawnymi** i zaakceptuj je.
13. Po potwierdzeniu zakupu zobaczysz okno podręczne **wdrożenia** , które zostanie wyświetlone i zobaczysz Twoje konto.

    ![all-resources][all-resources]

    Po zakończeniu zakupu i kliknięciu przycisku **Zarządzaj** w celu rozpoczęcia procesu weryfikacji adresu e-mail otrzymasz wiadomość e-mail od zespołu SendGrid z monitem o potwierdzenie konta. Jeśli nie otrzymasz tej wiadomości e-mail lub masz problemy z weryfikacją konta, zapoznaj się z naszymi często zadawanymi pytaniami.

    ![manage][manage]

    **Do momentu zweryfikowania konta możesz wysyłać maksymalnie 100 wiadomości e-mail dziennie.**

    Aby zmodyfikować plan subskrypcji lub wyświetlić ustawienia kontaktowe usługi SendGrid, kliknij nazwę usługi SendGrid w celu otwarcia pulpitu nawigacyjnego witryny SendGrid Marketplace.

    ![settings][settings]

    Aby wysłać wiadomość e-mail przy użyciu usługi SendGrid, musisz podać klucz interfejsu API.

### <a name="to-find-your-sendgrid-api-key"></a>Aby znaleźć klucz interfejsu API usługi SendGrid
1. Kliknij pozycję **Zarządzaj**.

    ![manage][manage]
2. W pulpicie nawigacyjnym usługi SendGrid wybierz pozycję **Ustawienia**, a następnie pozycję **Klucze interfejsu API** w menu po lewej stronie.

    ![api-keys][api-keys]

3. Kliknij przycisk **Utwórz klucz interfejsu API**.

    ![general-api-key][general-api-key]
4. Musisz podać **nazwę klucza** i przyznać pełen dostęp w wierszu **Wysyłanie poczty**. Wybierz przycisk **Zapisz**.

    ![access][access]
5. Po wykonaniu tych czynności zostanie wyświetlony klucz interfejsu API. Zapisz go w bezpiecznym miejscu.

### <a name="to-find-your-sendgrid-credentials"></a>Aby znaleźć poświadczenia usługi SendGrid
1. Kliknij ikonę klucza, aby wyświetlić swoją **nazwę użytkownika**.

    ![key][key]
2. Hasło zostało wcześniej ustawione podczas konfiguracji. Aby wprowadzić zmiany, możesz wybrać opcję **Zmień hasło** lub **Resetuj hasło**.

Aby zarządzać ustawieniami dostarczania poczty e-mail, kliknij przycisk **Zarządzaj**. Spowoduje to przekierowanie do pulpitu nawigacyjnego SendGrid.

![manage][manage]

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
