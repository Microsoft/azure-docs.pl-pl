---
title: Integracja z pakietem Office 365 Outlook
description: Automatyzowanie zadań i przepływów pracy, które zarządzają pocztą e-mail, kontaktami i kalendarzami w programie Office 365 Outlook przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 9caf69a7f78c7872f0a5f8a2ed07bdc749a29023
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682999"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Zarządzanie pocztą e-mail, kontaktami i kalendarzami w programie Outlook usługi Office 365 przy użyciu usługi Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika pakietu Office 365 Outlook](/connectors/office365connector/)można tworzyć automatyczne zadania i przepływy pracy służące do zarządzania kontem służbowym przez tworzenie aplikacji logiki. Na przykład możesz zautomatyzować poniższe zadania:

* Pobieranie, wysyłanie i odpowiadanie na wiadomości e-mail.
* Planowanie spotkań w kalendarzu.
* Dodawanie i edytowanie kontaktów.

Możesz użyć dowolnego wyzwalacza, aby uruchomić przepływ pracy, na przykład po nadejściu nowej wiadomości e-mail, po zaktualizowaniu elementu kalendarza lub gdy zdarzenie występuje w usłudze różnicowej, na przykład w usłudze Salesforce. Możesz użyć akcji, które reagują na zdarzenie wyzwalacza, na przykład Wyślij wiadomość e-mail lub Utwórz nowe wydarzenie w kalendarzu.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto programu Outlook umożliwiające zalogowanie się przy użyciu [konta służbowego](https://www.office.com/). Jeśli masz @outlook.com @hotmail.com konto lub, zamiast tego użyj [łącznika Outlook.com](../connectors/connectors-create-api-outlook.md) . Aby nawiązać połączenie z programem Outlook przy użyciu innego konta użytkownika, takiego jak konto usługi, zobacz [nawiązywanie połączenia przy użyciu innych kont](#connect-using-other-accounts).

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta programu Outlook. Aby uruchomić przepływ pracy przy użyciu wyzwalacza programu Outlook pakietu Office 365, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby dodać akcję programu Outlook pakietu Office 365 do przepływu pracy, aplikacja logiki musi już mieć wyzwalacz.

## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

[Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) to zdarzenie, które uruchamia przepływ pracy w aplikacji logiki. Ta przykładowa aplikacja logiki używa wyzwalacza sondowania, który sprawdza każde zaktualizowane zdarzenie w kalendarzu na koncie e-mail na podstawie określonego interwału i częstotliwości.

1. W [Azure Portal](https://portal.azure.com)Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania wprowadź `office 365 outlook` jako filtr. Ten przykład wybiera **, gdy wkrótce zostanie uruchomione nadchodzące wydarzenie**.
   
   ![Wybierz wyzwalacz, aby uruchomić aplikację logiki](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Jeśli nie masz aktywnego połączenia z kontem programu Outlook, zostanie wyświetlony monit o zalogowanie się i utworzenie tego połączenia. Aby nawiązać połączenie z programem Outlook przy użyciu innego konta użytkownika, takiego jak konto usługi, zobacz [nawiązywanie połączenia przy użyciu innych kont](#connect-using-other-accounts). W przeciwnym razie podaj informacje o właściwościach wyzwalacza.

   > [!NOTE]
   > Twoje połączenie nie wygasa, dopóki nie zostanie odwołane, nawet jeśli zmienisz poświadczenia logowania. Aby uzyskać więcej informacji, zobacz [konfigurowalne okresy istnienia tokenu w Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Ten przykład wybiera kalendarz sprawdzany przez wyzwalacz, na przykład:

   ![Skonfiguruj właściwości wyzwalacza](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. W wyzwalaczu Ustaw wartości **częstotliwości** i **interwałów** . Aby dodać inne dostępne właściwości wyzwalacza, takie jak **strefa czasowa**, wybierz te właściwości z listy **Dodaj nowy parametr** .

   Na przykład, jeśli chcesz, aby wyzwalacz sprawdzał kalendarz co 15 minut, ustaw **częstotliwość** na **minutę** i ustaw **Interwał** na `15` . 

   ![Ustawianie częstotliwości i interwału dla wyzwalacza](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Teraz Dodaj akcję, która jest uruchamiana po uruchomieniu wyzwalacza. Na przykład można dodać akcję Twilio **Wyślij wiadomość** , która wysyła tekst, gdy zdarzenie kalendarza zostanie uruchomione w ciągu 15 minut.

## <a name="add-an-action"></a>Dodawanie akcji

[Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest operacją uruchomioną przez przepływ pracy w aplikacji logiki. Ta przykładowa aplikacja logiki tworzy nowy kontakt w pakiecie Office 365 Outlook. Aby utworzyć kontakt, można użyć danych wyjściowych z innego wyzwalacza lub akcji. Na przykład załóżmy, że aplikacja logiki używa wyzwalacza Dynamics 365, **gdy rekord zostanie utworzony**. Można dodać akcję **Utwórz kontakt** programu Outlook dla pakietu Office 365 i użyć danych wyjściowych z wyzwalacza usługi Salesforce, aby utworzyć nowy kontakt.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Aby dodać akcję jako ostatni krok w przepływie pracy, wybierz pozycję **nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź `office 365 outlook` jako filtr. W tym przykładzie wybrano opcję **Utwórz kontakt**.

   ![Wybierz akcję do uruchomienia w aplikacji logiki](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Jeśli nie masz aktywnego połączenia z kontem programu Outlook, zostanie wyświetlony monit o zalogowanie się i utworzenie tego połączenia. Aby nawiązać połączenie z programem Outlook przy użyciu innego konta użytkownika, takiego jak konto usługi, zobacz [nawiązywanie połączenia przy użyciu innych kont](#connect-using-other-accounts). W przeciwnym razie podaj informacje o właściwościach akcji.

   > [!NOTE]
   > Twoje połączenie nie wygasa, dopóki nie zostanie odwołane, nawet jeśli zmienisz poświadczenia logowania. Aby uzyskać więcej informacji, zobacz [konfigurowalne okresy istnienia tokenu w Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Ten przykład wybiera folder Kontakty, w którym akcja tworzy nowy kontakt, na przykład:

   ![Konfigurowanie właściwości akcji](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Aby dodać inne dostępne właściwości akcji, wybierz te właściwości z listy **Dodaj nowy parametr** .

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Nawiązywanie połączenia przy użyciu innych kont

Jeśli spróbujesz nawiązać połączenie z programem Outlook przy użyciu innego konta niż to, które jest obecnie zalogowane na platformie Azure, możesz uzyskać błędy logowania jednokrotnego [(SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) . Ten problem występuje, gdy zalogujesz się do Azure Portal przy użyciu jednego konta, ale Użyj innego konta, aby utworzyć połączenie. Projektant aplikacji logiki oczekuje na użycie konta, które jest zalogowane na platformie Azure. Aby rozwiązać ten problem, możesz korzystać z następujących opcji:

* Skonfiguruj inne konto jako **współautor** grupy zasobów aplikacji logiki.

  1. W menu Grupa zasobów aplikacji logiki wybierz pozycję **Kontrola dostępu (IAM)**. Skonfiguruj inne konto z rolą **współautor** . Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../role-based-access-control/role-assignments-portal.md).

  1. Jeśli logujesz się do Azure Portal przy użyciu konta służbowego, Wyloguj się i zaloguj się ponownie przy użyciu innego konta. Możesz teraz utworzyć połączenie z programem Outlook przy użyciu innego konta.

* Skonfiguruj inne konto, aby konto służbowe miało uprawnienia "Wyślij jako".

   Jeśli masz uprawnienia administratora, w skrzynce pocztowej konta usługi Skonfiguruj Twoje konto służbowe za pomocą opcji **Wyślij jako** lub **Wyślij w imieniu** uprawnień. Aby uzyskać więcej informacji, zobacz [nadawanie uprawnień skrzynek pocztowych innemu użytkownikowi](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Następnie możesz utworzyć połączenie przy użyciu konta służbowego. Teraz w wyzwalaczach lub akcjach, w których można określić nadawcę, możesz użyć adresu e-mail konta usługi.

   Na przykład akcja **Wyślij wiadomość e-mail** ma opcjonalny parametr **od (Wyślij jako)**, który można dodać do akcji i użyć adresu e-mail konta usługi jako nadawcy. Aby dodać ten parametr, wykonaj następujące kroki:

   1. W akcji **Wyślij wiadomość e-mail** Otwórz listę **Dodaj parametr** , a następnie wybierz parametr **od (Send AS)** .

   1. Po pojawieniu się parametru w akcji wprowadź adres e-mail konta usługi.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne dotyczące tego łącznika, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/office365/). 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)