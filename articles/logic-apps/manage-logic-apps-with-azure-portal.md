---
title: Zarządzanie aplikacjami logiki w witrynie Azure portal
description: Edytowanie, włączanie, wyłączanie lub usuwanie aplikacji logiki przy użyciu witryny Azure Portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415968"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Zarządzanie aplikacjami logiki w witrynie Azure portal

Aplikacje logiki można zarządzać za pomocą [portalu Azure](https://portal.azure.com) lub programu [Visual Studio.](manage-logic-apps-with-visual-studio.md) W tym artykule pokazano, jak edytować, wyłączać, włączać lub usuwać aplikacje logiki w witrynie Azure portal. Jeśli jesteś nowym użytkownikem usługi Azure Logic Apps, zobacz [Co to jest usługa Azure Logic Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Istniejąca aplikacja logiki. Aby dowiedzieć się, jak utworzyć aplikację logiki w witrynie Azure portal, zobacz [Szybki start: Tworzenie pierwszego przepływu pracy przy użyciu usługi Azure Logic Apps — Azure portal](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Znajdowanie aplikacji logiki

Aby znaleźć i otworzyć aplikację logiki, wykonaj następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

1. Na pasku wyszukiwania `logic apps`platformy Azure wprowadź i wybierz pozycję **Aplikacje logiki**.

   ![Znajdź i wybierz "Aplikacje logiki"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na stronie **Aplikacje logiki** znajdź i wybierz aplikację logiki, którą chcesz zarządzać.

   Po otwarciu okienka **Przegląd** aplikacji logiki można filtrować listę wyświetlaną na stronie **Aplikacje logiki** w następujący sposób:

   * Wyszukiwanie aplikacji logiki według nazwy
   * Filtrowanie aplikacji logiki według subskrypcji, grupy zasobów, lokalizacji i tagów
   * Grupowanie aplikacji logiki według grupy zasobów, typu, subskrypcji i lokalizacji

## <a name="view-logic-app-properties"></a>Wyświetlanie właściwości aplikacji logiki

1. W witrynie Azure portal [znajdź i otwórz aplikację logiki](#find-logic-app).

1. Z menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Właściwości**.

1. W okienku **Właściwości** można wyświetlać i kopiować następujące informacje dotyczące aplikacji logiki:

   * **Nazwa**
   * **Identyfikator zasobu**
   * **Grupa zasobów**
   * **Lokalizacja**
   * **Typ** 
   * **Nazwa subskrypcji**
   * **Subscription ID (Identyfikator subskrypcji)**
   * **Punkt końcowy programu Access**
   * **Wychodzące adresy IP środowiska uruchomieniowego**
   * **Adresy IP punktu końcowego dostępu**
   * **Łącznik wychodzących adresów IP**

## <a name="disable-or-enable-logic-apps"></a>Wyłączanie lub włączanie aplikacji logiki

Można włączyć lub wyłączyć [pojedynczą aplikację logiki](#disable-enable-single-logic-app) lub [wiele aplikacji logiki w tym samym czasie](#disable-or-enable-multiple-logic-apps) w witrynie Azure portal. Można również [włączyć lub wyłączyć aplikacje logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Wyłączenie aplikacji logiki wpływa na wystąpienia przepływu pracy i działa w następujących sposób:

* Wszystkie przebiegi w toku i oczekujące są kontynuowane do czasu ich zakończenia. W zależności od liczby tych uruchomień ten proces może zająć trochę czasu.

* Aparat aplikacji logiki nie tworzy ani nie uruchamia nowych wystąpień przepływu pracy.

* Wyzwalacz nie wystrzeli następnym razem, gdy jego warunki zostaną spełnione.

* Stan wyzwalacza zapamiętuje punkt, w którym aplikacja logiki została zatrzymana. Tak po ponownym włączeniu aplikacji logiki wyzwalacza uruchamia dla wszystkich nieprzetwochanych elementów od ostatniego uruchomienia.

  Aby zatrzymać aplikacji logiki od wypalania na nieprzetwochanych elementów od ostatniego uruchomienia, wyczyść stan wyzwalacza przed ponownym włączeniem aplikacji logiki:

  1. W witrynie Azure portal [znajdź i otwórz aplikację logiki](#find-logic-app).

  1. Edytuj dowolną część wyzwalacza aplikacji logiki.

  1. Zapisz zmiany. Ten krok resetuje bieżący stan wyzwalacza.

  1. [Ponownie włącz aplikację logiki](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Wyłączanie lub włączanie aplikacji z pojedynczą logiką

1. W witrynie Azure portal [znajdź i otwórz aplikację logiki](#find-logic-app).

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Wybierz jedną z następujących opcji:

   * Na pasku narzędzi wybierz pozycję **Wyłącz**.

     ![Wyłączanie pojedynczej aplikacji logiki w witrynie Azure portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Jeśli aplikacja logiki jest już wyłączona, zobaczysz tylko **włącz** opcję.

   * Na pasku narzędzi wybierz pozycję **Włącz**.

     ![Włączanie aplikacji z pojedynczą logiką w witrynie Azure portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Jeśli aplikacja logiki jest już włączona, zobaczysz tylko **wyłącz** opcję. 

   Portal platformy Azure zawiera powiadomienie na głównym pasku narzędzi platformy Azure, które potwierdza, czy operacja zakończyła się pomyślnie lub nie powiodło się.

   ![Powiadomienie o potwierdzeniu stanu operacji](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Wyłączanie lub włączanie wielu aplikacji logiki

1. W witrynie Azure portal [znajdź aplikacje logiki,](#find-logic-app) które chcesz wyłączyć lub włączyć.

1. Aby sprawdzić, czy aplikacja logiki jest aktualnie włączona lub wyłączona, na stronie **Aplikacje logiki** przejrzyj kolumnę **Stan** dla tej aplikacji logiki. 

   ![Kolumna Stan aplikacji logiki](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Jeśli kolumna **Stan** nie jest widoczna, na pasku narzędzi **Aplikacje logiki** wybierz pozycję **Wypróbuj podgląd**.

   ![Włączanie podglądu](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. W kolumnie pola wyboru zaznacz aplikacje logiki, które chcesz wyłączyć lub włączyć. Na pasku narzędzi wybierz pozycję **Wyłącz** lub **Włącz**.

   ![Włączanie lub wyłączanie wielu aplikacji logiki w witrynie Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Po wyświetleniu pola potwierdzenia wybierz pozycję **Tak,** aby kontynuować.

   Portal platformy Azure zawiera powiadomienie na głównym pasku narzędzi platformy Azure, które potwierdza, czy operacja zakończyła się pomyślnie lub nie powiodło się.

## <a name="delete-logic-apps"></a>Usuwanie aplikacji logiki

Można [usunąć pojedynczą aplikację logiki](#delete-single-logic-app) lub [usunąć wiele aplikacji logiki w tym samym czasie](#delete-multiple-logic-apps) w witrynie Azure portal. Można również [usunąć aplikację logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Usunięcie aplikacji logiki wpływa na wystąpienia przepływu pracy w następujących sposób:

* Wszystkie przebiegi w toku i oczekujące są kontynuowane do czasu ich zakończenia. W zależności od liczby tych uruchomień ten proces może zająć trochę czasu.

* Aparat aplikacji logiki nie tworzy ani nie uruchamia nowych wystąpień przepływu pracy.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Usuwanie pojedynczej aplikacji logiki

1. W witrynie Azure portal [znajdź i otwórz aplikację logiki](#find-logic-app).

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi aplikacji logiki wybierz pozycję **Usuń**.

   ![Na pasku narzędzi aplikacji logiki wybierz "Usuń"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Po wyświetleniu pola potwierdzenia wprowadź nazwę aplikacji logiki i wybierz pozycję **Usuń**.

   ![Potwierdź, aby usunąć aplikację logiki](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Portal platformy Azure zawiera powiadomienie na głównym pasku narzędzi platformy Azure, które potwierdza, czy operacja zakończyła się pomyślnie lub nie powiodło się.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Usuwanie wielu aplikacji logiki

1. W witrynie Azure portal [znajdź aplikacje logiki, które chcesz usunąć](#find-logic-app).

1. W kolumnie pola wyboru zaznacz aplikacje logiki, które chcesz usunąć. Na pasku narzędzi wybierz pozycję **Usuń**.

   ![Usuwanie wielu aplikacji logiki](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Po wyświetleniu pola `yes`potwierdzenia wprowadź i wybierz pozycję **Usuń**.

   ![Potwierdź, aby usunąć aplikacje logiki](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Portal platformy Azure zawiera powiadomienie na głównym pasku narzędzi platformy Azure, które potwierdza, czy operacja zakończyła się pomyślnie lub nie powiodło się.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Zarządzanie wersjami aplikacji logiki

Można użyć witryny Azure portal do kontroli wersji aplikacji logiki. Możesz znaleźć historię wersji aplikacji logiki i promować poprzednie wersje.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Znajdowanie i wyświetlanie poprzednich wersji

1. W witrynie Azure portal [znajdź aplikację logiki, którą chcesz zarządzać](#find-logic-app).

1. W menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **Wersje**.

   ![W menu aplikacji logiki wybierz "Wersje" w obszarze "Narzędzia programistyczne"](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Wybierz **wersję** aplikacji logiki do zarządzania z listy. Identyfikator **wersji** można wprowadzić na pasku wyszukiwania, aby filtrować listę.

1. Na stronie **Wersja historii** zobaczysz szczegóły poprzedniej wersji w trybie tylko do odczytu. Można wybrać między trybami **widoku Projektant** aplikacji logiki i **Widok kodu.**

   ![Strona wersji historii dla aplikacji logiki z widokiem kodu i widokiem projektanta aplikacji logiki](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Podwyższyj poprzednie wersje

1. W historii wersji aplikacji logiki [znajdź i wybierz wersję, którą chcesz promować](#find-version-history).

1. Na stronie **Wersja historii** wybierz pozycję **Promuj**.

   ![Przycisk Promuj w historii wersji aplikacji logiki](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Na stronie **Projektant aplikacji logiki,** która zostanie otwarta, edytuj wersję, którą promujesz w razie potrzeby. Można przełączać się między trybami widoku **projektanta** i **kodu.** Można również aktualizować **parametry,** **szablony**i **łączniki**.

   ![Strona Projektanta aplikacji logiki do promowania poprzedniej wersji](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Aby zapisać aktualizacje i zakończyć promowanie poprzedniej wersji, wybierz pozycję **Zapisz**. (Lub, aby anulować zmiany, wybierz **opcję Odrzuć).** 

   Po [ponownym wyświetleniu historii wersji aplikacji logiki promowana](#find-version-history) wersja pojawia się u góry listy i ma nowy identyfikator.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie aplikacji logiki](monitor-logic-apps.md)
