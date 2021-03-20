---
title: Troubleshoot and diagnose workflow failures (Rozwiązywanie problemów i diagnozowanie błędów przepływu pracy)
description: Dowiedz się, jak rozwiązywać problemy, błędy i błędy w przepływach pracy w programie Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995050"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Rozwiązywanie problemów i diagnozowanie błędów przepływów pracy w usłudze Azure Logic Apps

Aplikacja logiki generuje informacje, które mogą pomóc zdiagnozować i debugować problemy w aplikacji. Aplikację logiki można zdiagnozować, przeglądając każdy krok w przepływie pracy za pomocą Azure Portal. Można też dodać kilka kroków do przepływu pracy w celu debugowania środowiska uruchomieniowego.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Sprawdź historię wyzwalacza

Każde uruchomienie aplikacji logiki rozpoczyna się od próby wyzwalacza, więc jeśli wyzwalacz nie zostanie wyzwolony, wykonaj następujące czynności:

1. Sprawdź stan wyzwalacza, [sprawdzając historię wyzwalacza](../logic-apps/monitor-logic-apps.md#review-trigger-history). Aby wyświetlić więcej informacji na temat próby wyzwalacza, wybierz to zdarzenie wyzwalacza, na przykład:

   ![Wyświetl stan i historię wyzwalacza](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Sprawdź dane wejściowe wyzwalacza, aby upewnić się, że są wyświetlane zgodnie z oczekiwaniami. W obszarze **link danych wejściowych** wybierz łącze, które wyświetla okienko **dane wejściowe** .

   Wyzwalacze wejściowe zawierają dane, których wyzwalacz oczekuje i które są wymagane do uruchomienia przepływu pracy. Przeglądanie tych danych wejściowych może pomóc w ustaleniu, czy dane wejściowe wyzwalacza są poprawne i czy warunek został spełniony, aby przepływ pracy mógł kontynuować działanie.

   Na przykład właściwość w `feedUrl` tym miejscu zawiera niepoprawną wartość kanału informacyjnego RSS:

   ![Przejrzyj dane wejściowe wyzwalacza pod kątem błędów](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Sprawdź wyjściowe Wyzwalacze (jeśli istnieją), aby upewnić się, że są wyświetlane zgodnie z oczekiwaniami. W obszarze link do danych **wyjściowych** wybierz link, który pokazuje okienko dane **wyjściowe** .

   Wyjściowe wyzwalacze obejmują dane przekazywane przez wyzwalacz do następnego kroku w przepływie pracy. Przeglądanie tych danych wyjściowych może pomóc w ustaleniu, czy poprawna lub oczekiwana wartość została przeniesiona do następnego kroku w przepływie pracy, na przykład:

   ![Przejrzyj dane wyjściowe wyzwalacza pod kątem błędów](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Jeśli znajdziesz dowolną nierozpoznaną zawartość, Dowiedz się więcej o [różnych typach zawartości](../logic-apps/logic-apps-content-type.md) w Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Sprawdź historię uruchomień

Za każdym razem, gdy wyzwalacz jest wyzwalany dla elementu lub zdarzenia, aparat Logic Apps tworzy i uruchamia oddzielne wystąpienie przepływu pracy dla każdego elementu lub zdarzenia. Jeśli uruchomienie nie powiedzie się, wykonaj następujące kroki, aby sprawdzić, co się stało w tym przebiegu, w tym informacje o stanie dla każdego kroku w przepływie pracy oraz dane wejściowe i wyjściowe dla każdego kroku.

1. Sprawdź stan uruchomienia przepływu pracy, [sprawdzając historię uruchomień](../logic-apps/monitor-logic-apps.md#review-runs-history). Aby wyświetlić więcej informacji na temat niepowodzenia przebiegu, w tym wszystkich kroków w tym działaniu, wybierz przebieg zakończony niepowodzeniem.

   ![Wyświetl historię uruchamiania i wybierz nieudane uruchomienie](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Po wyświetleniu wszystkich kroków w przebiegu rozwiń pierwszy krok zakończony niepowodzeniem.

   ![Rozwiń pierwszy krok zakończony niepowodzeniem](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Sprawdź dane wejściowe kroku zakończonego niepowodzeniem, aby upewnić się, że są wyświetlane zgodnie z oczekiwaniami.

1. Przejrzyj szczegóły każdego kroku w określonym przebiegu. W obszarze **historia uruchamiania** wybierz przebieg, który chcesz przejrzeć.

   ![Przejrzyj historię uruchamiania](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Wyświetlanie szczegółów uruchomienia aplikacji logiki](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Aby przeanalizować dane wejściowe, wyjściowe i wszystkie komunikaty o błędach dla określonego kroku, należy wybrać ten krok, aby kształt rozszerzał i pokazywał szczegóły. Na przykład:

   ![Wyświetlanie szczegółów kroku](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Wykonywanie debugowania środowiska uruchomieniowego

Aby ułatwić debugowanie, można dodać kroki diagnostyczne do przepływu pracy aplikacji logiki, a także przejrzeć wyzwalacz i historię uruchomienia. Można na przykład dodać kroki, które korzystają z usługi [webhook testera](https://webhook.site/) , aby móc sprawdzać żądania HTTP i określać ich dokładny rozmiar, kształt i format.

1. Przejdź do witryny [testera elementu webhook](https://webhook.site/) i skopiuj wygenerowany unikatowy adres URL.

1. W aplikacji logiki Dodaj akcję POST protokołu HTTP i treść, którą chcesz przetestować, na przykład wyrażenie lub inne wyjście krok.

1. Wklej adres URL z testera elementu webhook do akcji POST protokołu HTTP.

1. Aby sprawdzić, w jaki sposób jest tworzone żądanie podczas generowania z aparatu Logic Apps, uruchom aplikację logiki i ponownie odwiedź witrynę testera elementu webhook, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps.md)
