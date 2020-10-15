---
title: Akcja poczty e-mail w ramach monitorowania zdalnego — Azure | Microsoft Docs
description: W tym przewodniku opisano sposób dodawania akcji poczty e-mail do nowej lub istniejącej reguły.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 2776b18dc3faded103b49e8ff58a8a0a6dccf144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074468"
---
# <a name="add-an-email-action"></a>Dodaj akcję poczty e-mail

Akcje e-mail pomagają upewnić się, że alerty nie zostały pominięte. Możesz dodać akcję poczty e-mail do istniejącej reguły lub podczas tworzenia nowej reguły.

Aby wykonać kroki opisane w tym przewodniku, musisz wdrożyć wystąpienie akceleratora rozwiązania do monitorowania zdalnego w ramach subskrypcji platformy Azure.

Aby utworzyć lub zmodyfikować regułę, musisz być [ **administratorem**lub mieć odpowiednie uprawnienia](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Edytowanie istniejącej reguły

Wykonaj następujące kroki, aby dodać akcję poczty e-mail do istniejącej reguły:

1. Przejdź do rozwiązania do monitorowania zdalnego.

1. Na **pulpicie nawigacyjnym**przejdź do strony **reguły** :

    ![Strona Reguły](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kliknij pole wyboru obok istniejącej reguły do zmodyfikowania, a następnie kliknij pozycję **Edytuj** u góry. Zostanie wyświetlony panel **reguły** edytowalnej.

1. W sekcji **Akcja** Przełącz opcję **poczta e-mail** na włączone **.**

1. Po pierwszym włączeniu akcji poczty e-mail w akceleratorze rozwiązań należy [zalogować się do programu Outlook](#outlook).

1. Wprowadź adres e-mail w polu odbiorca i naciśnij klawisz **Enter** , aby dodać adres e-mail.

    ![Wpis adresu](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Wprowadź temat wiadomości e-mail.

1. Wprowadź wszelkie dodatkowe uwagi dla adresatów wiadomości e-mail jako zwykły tekst. Możesz użyć formatowania HTML [w przypadku edytowania szablonu wiadomości e-mail](#htmledit).

1. Upewnij się, że **stan reguły** jest ustawiony na **włączone**.

1. Kliknij pozycję **Zastosuj**.

## <a name="create-a-new-rule"></a>Tworzenie nowej reguły

Wykonaj następujące kroki, aby dodać akcję poczty e-mail podczas tworzenia nowej reguły:

1. Przejdź do rozwiązania do monitorowania zdalnego.

1. Na **pulpicie nawigacyjnym**przejdź do strony **reguły** :

    ![Strona Reguły](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Wykonaj kroki opisane w [sekcji Tworzenie reguły](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Wykonaj kroki opisane w sekcji [tworzenie zaawansowanej reguły](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) do punktu, w którym ustawiono **poziom ważności**. Nie klikaj jeszcze przycisku **Zastosuj** .

1. W sekcji **Akcja** Przełącz opcję **poczta e-mail** na włączone **.**

1. Po pierwszym włączeniu akcji poczty e-mail w akceleratorze rozwiązań należy [zalogować się do programu Outlook](#outlook).

1. Wprowadź adres e-mail w polu odbiorca i naciśnij klawisz **Enter** , aby dodać adres e-mail.

    ![Wpis adresu](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Wprowadź temat wiadomości e-mail.

1. Wprowadź wszelkie dodatkowe uwagi dla adresatów wiadomości e-mail jako zwykły tekst. Możesz użyć formatowania HTML [w przypadku edytowania szablonu wiadomości e-mail](#htmledit).

1. Upewnij się, że **stan reguły** jest ustawiony na **włączone**.

1. Kliknij pozycję **Zastosuj**.

Twoja reguła z akcją wiadomości e-mail jest teraz włączona. Za każdym razem, gdy akcja jest wyzwalana, do adresatów jest wysyłana nowa wiadomość e-mail.

## <a name="sign-in-to-outlook"></a>Zaloguj się do programu Outlook <a name="outlook"></a>

Po pierwszym włączeniu akcji poczty e-mail w akceleratorze rozwiązań należy zalogować się do programu Outlook. Ta akcja powoduje skonfigurowanie konta e-mail, które wysyła powiadomienia e-mail.

> [!NOTE]
> Należy utworzyć określone konto programu Outlook tylko dla powiadomień akceleratora rozwiązań i korzystać z tego konta po włączeniu pierwszej akcji poczty e-mail.

### <a name="contributor-role-outlook-setup"></a>Rola współautor — konfiguracja programu Outlook

Jeśli ktoś w roli **współautor** w subskrypcji wdrożony Akcelerator rozwiązania, aplikacja nie ma wystarczających uprawnień do konfigurowania i weryfikowania akcji poczty e-mail za pośrednictwem interfejsu użytkownika sieci Web.

Przed rozpoczęciem Utwórz konto programu Outlook, które będzie używane do wysyłania powiadomień e-mail z akceleratora rozwiązań.

Poniższe kroki pokazują, jak skonfigurować i zweryfikować akcje poczty e-mail ręcznie:

1. Przejdź do [Azure Portal](https://portal.azure.com).

1. Przejdź do grupy zasobów dla akceleratora rozwiązania.

1. Kliknij **Łącznik usługi Office 365**:

    ![Połączenie interfejsu API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Kliknij transparent, aby rozpocząć proces autoryzacji:

    ![autoryzacja](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Kliknij przycisk **Autoryzuj**. Zostanie wyświetlony monit o zalogowanie się. Konto używane do logowania powinno być adresem e-mail używanym przez aplikację do wysyłania powiadomień e-mail:

    ![Przycisk Autoryzuj](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Kliknij przycisk **Zapisz** u dołu. Autoryzacja powiedzie się, jeśli transparent zostanie usunięty.

1. Aby zmienić adres e-mail, z którego są wysyłane powiadomienia, kliknij pozycję **Edytuj połączenie z interfejsem API**.

    ![Zmień adres e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Konfiguracja roli właściciela programu Outlook

Jeśli ktoś w roli **właściciela** w ramach subskrypcji wdrożył Akcelerator rozwiązania, aplikacja może sprawdzić, czy akcje e-mail zostały prawidłowo skonfigurowane za pośrednictwem interfejsu użytkownika sieci Web.

Przed rozpoczęciem Utwórz konto programu Outlook, które będzie używane do wysyłania powiadomień e-mail z akceleratora rozwiązań.

Poniższe kroki ułatwiają logowanie się i Konfigurowanie akcji poczty e-mail:

1. Kliknij, aby zalogować się do programu Outlook. Nastąpi przekierowanie do Azure Portal:

   ![Zaloguj się do programu Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Kliknij przycisk **Autoryzuj**. Zostanie wyświetlony monit o zalogowanie się. Konto używane do logowania powinno być adresem e-mail używanym przez aplikację do wysyłania powiadomień e-mail:

1. Kliknij pozycję **Zapisz**. Wróć do akceleratora rozwiązania i Odśwież stronę.

1. Po pomyślnym skonfigurowaniu powiadomienia e-mail zostanie wyświetlony następujący komunikat:

   ![Pomyślne logowanie do programu Outlook](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Dostosowywanie wiadomości e-mail w formacie HTML <a name="htmledit"></a>

Wbudowany Akcelerator rozwiązania do monitorowania zdalnego udostępnia podstawowy szablon HTML dla wiadomości e-mail z akcją. Szablon wiadomości e-mail używa wartości z ustawień akcji poczty e-mail. Oto przykład wiadomości e-mail:

![przykład wiadomości e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Poniższe kroki pokazują, jak edytować szablon wiadomości e-mail w formacie HTML. Na przykład możesz dołączyć więcej informacji lub dodać obrazy niestandardowe:

1. Sklonowanie repozytorium usługi GitHub do monitorowania zdalnego Java lub .NET:

1. Przejdź do lokalizacji szablonu wiadomości e-mail:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Aby dostosować komunikat, można dodać lub usunąć wszystkie parametry w tym szablonie. W razie konieczności można także dodawać, usuwać lub zamieniać wywołania:

    Na przykład w kodzie .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Na przykład w kodzie Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametry w szablonie mają postać `${...}` . Aby usunąć parametr, Usuń wymagany wiersz. Aby dodać parametr, Dodaj wiersz z wartością do wstawienia.

1. Aby dodać obrazy lub niestandardowy tekst, zaktualizuj bezpośrednio plik EmailTemplate.HTML.

## <a name="throttling"></a>Ograniczanie przepływności

Akcelerator rozwiązania do monitorowania zdalnego używa programu Outlook do wysyłania powiadomień e-mail. Program Outlook ogranicza liczbę wiadomości e-mail wysyłanych do [30 wiadomości e-mail na 1 minutę](/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Klienci poczty e-mail, którzy odbierają wiadomości e-mail, mogą również ograniczyć liczbę otrzymywanych wiadomości e-mail na minutę. Skontaktuj się z konkretnym klientem poczty e-mail w sprawie ograniczeń. Podczas konfigurowania powiadomienia e-mail dla reguły, reguła powinna obliczać wartości średnie w okresie co najmniej jednej minuty, a nie używać wartości błyskawicznych:

![Obliczanie średniej](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku pokazano, jak dodać akcję poczty e-mail do nowej lub istniejącej reguły w ramach rozwiązania do monitorowania zdalnego. Przewodnik ten zawiera również instrukcje i sposób edytowania kodu HTML, który definiuje format wiadomości.

Zalecanym następnym krokiem jest zapoznanie się z [alertami i rozwiązywanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md).