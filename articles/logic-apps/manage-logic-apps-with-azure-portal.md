---
title: Zarządzanie aplikacjami logiki w Azure Portal
description: Można edytować, włączać, wyłączać i usuwać aplikacje logiki przy użyciu Azure Portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: d80972cd200b8f85e14d316c4c06a38f88ac81b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82598168"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Zarządzanie aplikacjami logiki w Azure Portal

Aplikacjami logiki można zarządzać przy użyciu [Azure Portal](https://portal.azure.com) lub [programu Visual Studio](manage-logic-apps-with-visual-studio.md). W tym artykule pokazano, jak edytować, wyłączać, włączać lub usuwać aplikacje logiki w Azure Portal. Jeśli jesteś nowym do Azure Logic Apps, zobacz [co to jest Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Istniejąca aplikacja logiki. Aby dowiedzieć się, jak utworzyć aplikację logiki w Azure Portal, zobacz [Szybki Start: Tworzenie pierwszego przepływu pracy przy użyciu Azure Logic Apps Azure Portal](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Znajdowanie aplikacji logiki

Aby znaleźć i otworzyć aplikację logiki, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

1. Na pasku wyszukiwania platformy Azure wprowadź wartość `logic apps` i wybierz pozycję **Logic Apps**.

   ![Znajdź i wybierz pozycję "Logic Apps"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na stronie **Logic Apps** Znajdź i wybierz aplikację logiki, którą chcesz zarządzać.

   Po otwarciu okienka **Przegląd** aplikacji logiki można filtrować listę, która pojawia się na stronie **Logic Apps** w następujący sposób:

   * Wyszukaj Aplikacje logiki według nazwy
   * Filtrowanie aplikacji logiki według subskrypcji, grupy zasobów, lokalizacji i tagów
   * Grupuj Aplikacje logiki według grupy zasobów, typu, subskrypcji i lokalizacji

## <a name="view-logic-app-properties"></a>Wyświetl właściwości aplikacji logiki

1. W Azure Portal [Znajdź i Otwórz aplikację logiki](#find-logic-app).

1. Z menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Właściwości**.

1. W okienku **Właściwości** można wyświetlać i kopiować następujące informacje o aplikacji logiki:

   * **Nazwa**
   * **Identyfikator zasobu**
   * **Grupa zasobów**
   * **Lokalizacja**
   * **Typ** 
   * **Subscription Name**
   * **Identyfikator subskrypcji**
   * **Punkt końcowy dostępu**
   * **Wychodzące adresy IP środowiska uruchomieniowego**
   * **Adresy IP punktów końcowych dostępu**
   * **Wychodzące adresy IP łącznika**

## <a name="disable-or-enable-logic-apps"></a>Wyłączanie lub włączanie aplikacji logiki

W tym samym czasie w Azure Portal można włączyć lub wyłączyć [jedną aplikację logiki](#disable-enable-single-logic-app) lub [wiele aplikacji logiki](#disable-or-enable-multiple-logic-apps) . Możesz również [włączyć lub wyłączyć Aplikacje logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Wyłączenie aplikacji logiki wpływa na wystąpienia przepływu pracy i przebiega w następujący sposób:

* Wszystkie w toku i oczekujące przebiegi są kontynuowane do czasu zakończenia. W zależności od liczby tych przebiegów proces ten może zająć trochę czasu.

* Aparat Logic Apps nie utworzy ani nie uruchomi nowych wystąpień przepływu pracy.

* Wyzwalacz nie zostanie uruchomiony przy następnym spełnieniu jego warunków.

* Stan wyzwalacza odnależy do punktu, w którym aplikacja logiki została zatrzymana. Dlatego po ponownym włączeniu aplikacji logiki wyzwalany jest wyzwalacz dla wszystkich nieprzetworzonych elementów od momentu ostatniego uruchomienia.

  Aby zatrzymać wywoływanie aplikacji logiki dla nieprzetworzonych elementów od momentu ostatniego uruchomienia, wyczyść stan wyzwalacza przed ponownym włączeniem aplikacji logiki:

  1. W Azure Portal [Znajdź i Otwórz aplikację logiki](#find-logic-app).

  1. Edytuj jakąkolwiek część wyzwalacza aplikacji logiki.

  1. Zapisz zmiany. Ten krok resetuje bieżący stan wyzwalacza.

  1. [Włącz ponownie aplikację logiki](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Wyłączanie lub Włączanie pojedynczej aplikacji logiki

1. W Azure Portal [Znajdź i Otwórz aplikację logiki](#find-logic-app).

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Wybierz jedną z następujących opcji:

   * Na pasku narzędzi wybierz pozycję **Wyłącz**.

     ![Wyłącz pojedynczą aplikację logiki w Azure Portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Jeśli aplikacja logiki jest już wyłączona, zobaczysz tylko opcję **Włącz** .

   * Na pasku narzędzi wybierz pozycję **Włącz**.

     ![Włączanie pojedynczej aplikacji logiki w Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Jeśli aplikacja logiki jest już włączona, zobaczysz tylko opcję **Wyłącz** . 

   Azure Portal przedstawia powiadomienie na głównym pasku narzędzi platformy Azure, który potwierdza, czy operacja zakończyła się powodzeniem, czy niepowodzeniem.

   ![Powiadomienie w celu potwierdzenia stanu operacji](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Wyłączanie lub Włączanie wielu aplikacji logiki

1. W Azure Portal [Znajdź aplikacje logiki](#find-logic-app) , które chcesz wyłączyć lub włączyć.

1. Aby sprawdzić, czy aplikacja logiki jest obecnie włączona, czy wyłączona, na stronie **Logic Apps** Sprawdź kolumnę **stan** tej aplikacji logiki. 

   ![Kolumna stanu Logic Apps](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Jeśli kolumna **stan** nie jest widoczna, na pasku narzędzi **Logic Apps** wybierz pozycję **Wypróbuj wersję zapoznawczą**.

   ![Włącz podgląd](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. W kolumnie CheckBox wybierz Aplikacje logiki, które chcesz wyłączyć lub włączyć. Na pasku narzędzi wybierz opcję **Wyłącz** lub **Włącz**.

   ![Włączać lub wyłączać wiele aplikacji logiki w Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Po wyświetleniu pola potwierdzenia wybierz pozycję **tak** , aby kontynuować.

   Azure Portal przedstawia powiadomienie na głównym pasku narzędzi platformy Azure, który potwierdza, czy operacja zakończyła się powodzeniem, czy niepowodzeniem.

## <a name="delete-logic-apps"></a>Usuwanie aplikacji logiki

Można [usunąć pojedynczą aplikację logiki](#delete-single-logic-app) lub [usunąć wiele aplikacji logiki jednocześnie](#delete-multiple-logic-apps) w Azure Portal. Możesz również [usunąć aplikację logiki w programie Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Usunięcie aplikacji logiki wpływa na wystąpienia przepływu pracy w następujący sposób:

* Wszystkie w toku i oczekujące przebiegi są kontynuowane do czasu zakończenia. W zależności od liczby tych przebiegów proces ten może zająć trochę czasu.

* Aparat Logic Apps nie utworzy ani nie uruchomi nowych wystąpień przepływu pracy.

> [!NOTE]
> Jeśli usuniesz i utworzysz ponownie podrzędną aplikację logiki, musisz ponownie zapisać nadrzędną aplikację logiki. Ponownie utworzona aplikacja podrzędna będzie miała inne metadane.
> Jeśli nie zapiszesz ponownie nadrzędnej aplikacji logiki po ponownym utworzeniu elementu podrzędnego, Twoje wywołania podrzędnej aplikacji logiki zakończą się niepowodzeniem z błędem "Brak autoryzacji". To zachowanie dotyczy aplikacji logiki nadrzędny-podrzędny, na przykład tych, które używają artefaktów na kontach integracji lub wywołują usługi Azure Functions.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Usuwanie pojedynczej aplikacji logiki

1. W Azure Portal [Znajdź i Otwórz aplikację logiki](#find-logic-app).

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi aplikacji logiki wybierz pozycję **Usuń**.

   ![Na pasku narzędzi aplikacji logiki wybierz pozycję "Usuń".](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Gdy pojawi się okno potwierdzenia, wprowadź nazwę aplikacji logiki, a następnie wybierz pozycję **Usuń**.

   ![Potwierdzenie usunięcia aplikacji logiki](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure Portal przedstawia powiadomienie na głównym pasku narzędzi platformy Azure, który potwierdza, czy operacja zakończyła się powodzeniem, czy niepowodzeniem.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Usuwanie wielu aplikacji logiki

1. W Azure Portal [Znajdź aplikacje logiki, które chcesz usunąć](#find-logic-app).

1. W kolumnie CheckBox wybierz Aplikacje logiki, które chcesz usunąć. Na pasku narzędzi wybierz pozycję **Usuń**.

   ![Usuwanie wielu aplikacji logiki](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Gdy pojawi się okno potwierdzenia, wprowadź `yes` i wybierz pozycję **Usuń**.

   ![Potwierdzenie usunięcia aplikacji logiki](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure Portal przedstawia powiadomienie na głównym pasku narzędzi platformy Azure, który potwierdza, czy operacja zakończyła się powodzeniem, czy niepowodzeniem.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Zarządzanie wersjami aplikacji logiki

Azure Portal służy do kontroli wersji aplikacji logiki. Możesz znaleźć historię wersji aplikacji logiki i podwyższyć poziom poprzednich wersji.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Znajdź i Wyświetl poprzednie wersje

1. W Azure Portal [Znajdź aplikację logiki, którą chcesz zarządzać](#find-logic-app).

1. W menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **wersje**.

   ![W menu aplikacji logiki wybierz pozycję "wersje" w obszarze "narzędzia programistyczne".](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Wybierz **wersję** aplikacji logiki, która ma być zarządzana z listy. Aby odfiltrować listę, możesz wprowadzić identyfikator **wersji** na pasku wyszukiwania.

1. Na stronie **wersja historii** zobaczysz szczegóły poprzedniej wersji w trybie tylko do odczytu. Można wybrać między trybami **projektant** Logic Apps i **Widok kodu** .

   ![Strona wersji historii dla aplikacji logiki korzystającej z widoku kodu i widoku projektanta aplikacji logiki](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Podwyższ poziom poprzednich wersji

1. W historii wersji aplikacji logiki [Znajdź i wybierz wersję, którą chcesz podwyższyć](#find-version-history).

1. Na stronie **wersja historii** wybierz pozycję **Podwyższ poziom**.

   ![Przycisk promowania w historii wersji aplikacji logiki](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Na stronie **projektanta Logic Apps** , która zostanie otwarta, Edytuj wersję, która jest podwyższana, w razie konieczności. Można przełączać się między trybami **projektanta** i **widoku kodu** . Można także aktualizować **Parametry**, **Szablony**i **Łączniki**.

   ![Strona projektanta Logic Apps na potrzeby promowania poprzedniej wersji](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Aby zapisać wszystkie aktualizacje i zakończenie promowania poprzedniej wersji, wybierz pozycję **Zapisz**. (Lub, aby anulować zmiany, wybierz pozycję **Odrzuć**.) 

   Po ponownym [wyświetleniu historii wersji aplikacji logiki](#find-version-history) zostanie wyświetlona promowana wersja znajdująca się u góry listy i ma nowy identyfikator.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie aplikacji logiki](monitor-logic-apps.md)
