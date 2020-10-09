---
title: Nawiązywanie połączenia z usługą Outlook.com
description: Automatyzowanie zadań i przepływów pracy, które zarządzają pocztą e-mail, kalendarzami i kontaktami w programie Outlook.com przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75707190"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Zarządzanie pocztą e-mail, kalendarzami i kontaktami w programie Outlook.com przy użyciu Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika Outlook.com](/connectors/outlook/)można tworzyć zautomatyzowane zadania i przepływy pracy, które zarządzają @outlook.com @hotmail.com kontami lub przez tworzenie aplikacji logiki. Na przykład można zautomatyzować następujące zadania:

* Pobieranie, wysyłanie i odpowiadanie na wiadomości e-mail.
* Planowanie spotkań w kalendarzu.
* Dodawanie i edytowanie kontaktów.

Możesz użyć dowolnego wyzwalacza, aby uruchomić przepływ pracy, na przykład po nadejściu nowej wiadomości e-mail, po zaktualizowaniu elementu kalendarza lub gdy zdarzenie występuje w usłudze różnicowej. Możesz użyć akcji, które reagują na zdarzenie wyzwalacza, na przykład Wyślij wiadomość e-mail lub Utwórz nowe wydarzenie w kalendarzu.

> [!NOTE]
> Aby zautomatyzować zadania dla konta służbowego firmy Microsoft, takie jak @fabrikam.onmicrosoft.com , użyj [łącznika programu Outlook pakietu Office 365](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto Outlook.com](https://outlook.live.com/owa/)

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta Outlook.com. Aby uruchomić przepływ pracy przy użyciu wyzwalacza Outlook.com, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby dodać akcję Outlook.com do przepływu pracy, aplikacja logiki musi już mieć wyzwalacz.

## <a name="add-a-trigger"></a>Dodawanie wyzwalacza

[Wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) to zdarzenie, które uruchamia przepływ pracy w aplikacji logiki. Ta przykładowa aplikacja logiki używa wyzwalacza sondowania, który sprawdza nową wiadomość e-mail na koncie e-mail na podstawie określonego interwału i częstotliwości.

1. W [Azure Portal](https://portal.azure.com)Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania wprowadź ciąg "outlook.com" jako filtr. Na potrzeby tego przykładu wybierz **po nadejściu nowej wiadomości e-mail**.

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj poświadczenia Outlook.com, aby umożliwić aplikacji logiki łączenie się z kontem. W przeciwnym razie, jeśli połączenie już istnieje, podaj informacje o właściwościach wyzwalacza:

1. W wyzwalaczu Ustaw wartości **częstotliwości** i **interwałów** .

   Na przykład jeśli wyzwalacz ma sondować co 15 minut, należy ustawić **częstotliwość** na **minutę**i ustawić **Interwał** na **15**.

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**, co spowoduje zapisanie aplikacji logiki.

Aby odpowiedzieć na wyzwalacz, Dodaj kolejną akcję. Można na przykład dodać akcję Twilio **Wyślij wiadomość** , która wysyła tekst po nadejściu wiadomości e-mail.

## <a name="add-an-action"></a>Dodawanie akcji

[Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest operacją uruchomioną przez przepływ pracy w aplikacji logiki. Ta przykładowa aplikacja logiki wysyła wiadomość e-mail z konta usługi Outlook.com. Aby wypełnić akcję, można użyć danych wyjściowych z innego wyzwalacza. Załóżmy na przykład, że aplikacja logiki używa usługi SalesForce, **gdy zostanie utworzony wyzwalacz obiektu** . Można dodać akcję Outlook.com **Wyślij wiadomość e-mail** i użyć danych wyjściowych z wyzwalacza usługi Salesforce w wiadomości e-mail.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Aby dodać akcję jako ostatni krok w przepływie pracy, wybierz pozycję **nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź ciąg "outlook.com" jako filtr. Na potrzeby tego przykładu wybierz opcję **Wyślij wiadomość e-mail**. 

1. Jeśli zostanie wyświetlony monit o zalogowanie się, podaj poświadczenia Outlook.com, aby umożliwić aplikacji logiki łączenie się z kontem. W przeciwnym razie, jeśli połączenie już istnieje, podaj informacje o właściwościach akcji.

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**, co spowoduje zapisanie aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/outlook/). 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
