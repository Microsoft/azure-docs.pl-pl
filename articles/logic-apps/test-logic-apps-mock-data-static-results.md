---
title: Testowanie aplikacji logiki za pomocą danych testowych
description: Skonfiguruj statyczne wyniki testowania aplikacji logiki przy użyciu danych makiety bez wpływu na środowiska produkcyjne
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74790284"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testowanie aplikacji logiki przy użyciu danych makiety przez skonfigurowanie wyników statycznych

Podczas testowania aplikacji logiki możesz nie być gotowy do rzeczywistego wywoływania lub uzyskiwania dostępu do aplikacji, usług i systemów z różnych powodów. Zwykle w tych scenariuszach może być konieczne uruchomienie różnych ścieżek warunku, wymuszanie błędów, dostarczenie określonych treści odpowiedzi na komunikaty lub nawet próba pominięcia niektórych kroków. Konfigurując wyniki statyczne dla akcji w aplikacji logiki, można zasymulować dane wyjściowe tej akcji. Włączenie wyników statycznych w akcji nie powoduje uruchomienia akcji, ale zamiast tego zwraca dane makiety.

Jeśli na przykład ustawisz statyczne wyniki dla akcji wysyłania poczty w programie Outlook 365, aparat Logic Apps po prostu zwróci dane makiety, które zostały określone jako wyniki statyczne, a nie wywołują programu Outlook i wyśle wiadomość e-mail.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz skonfigurować wyniki statyczne

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Skonfiguruj wyniki statyczne

1. Jeśli jeszcze tego nie zrobiono, w [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w projektancie Logic Apps.

1. Na akcji, w której chcesz skonfigurować wyniki statyczne, wykonaj następujące kroki: 

   1. W prawym górnym rogu akcji wybierz przycisk wielokropka (*...*), a następnie wybierz pozycję **wynik statyczny**, na przykład:

      ![Wybierz "wynik statyczny" > "Włącz statyczny wynik"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Wybierz pozycję **Włącz statyczny wynik**. Dla właściwości wymaganych (*) Określ wartości wyjściowe makiety, które mają zostać zwrócone dla odpowiedzi akcji.

      Na przykład poniżej przedstawiono właściwości wymagane dla akcji HTTP:

      | Właściwość | Opis |
      |----------|-------------|
      | **Stan** | Stan akcji do zwrócenia |
      | **Kod stanu** | Konkretny kod stanu do zwrócenia |
      | **Nagłówka** | Zawartość nagłówka do zwrócenia |
      |||

      ![Wybierz pozycję "Włącz wynik statyczny"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Aby wprowadzić dane makiety w formacie JavaScript Object Notation (JSON), wybierz polecenie **Przełącz do trybu JSON** ( ![ Wybierz polecenie "Przełącz do trybu JSON" ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ).

   1. W przypadku właściwości opcjonalnych Otwórz listę **Wybierz pola opcjonalne** i wybierz właściwości, które chcesz zasymulować.

      ![Wybierz Właściwości opcjonalne](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Gdy wszystko będzie gotowe do zapisu, wybierz pozycję **gotowe**.

   W prawym górnym rogu akcji na pasku tytułu zostanie wyświetlona ikona zlewka testowego ( ![ ikona dla wyników statycznych ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), która wskazuje, że włączono wyniki statyczne.

   ![Ikona z włączonymi wynikami statycznymi](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Aby znaleźć poprzednie przebiegi korzystające z danych makiety, zobacz [Find uruchomienia, które używają wyników statycznych](#find-runs-mock-data) w dalszej części tego tematu.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Ponowne użycie poprzednich danych wyjściowych

Jeśli Twoja aplikacja logiki ma poprzednie uruchomienie z wynikami, których można użyć jako danych wyjściowych, możesz skopiować i wkleić dane wyjściowe z tego przebiegu.

1. Jeśli jeszcze tego nie zrobiono, w [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w projektancie Logic Apps.

1. W menu głównym aplikacji logiki wybierz pozycję **Przegląd**.

1. W sekcji **historia uruchomień** wybierz żądany przebieg aplikacji logiki.

1. W przepływie pracy aplikacji logiki Znajdź i rozwiń akcję, która ma żądane dane wyjściowe.

1. Wybierz łącze **Pokaż nieprzetworzone dane wyjściowe** .

1. Skopiuj kompletny obiekt JavaScript Object Notation (JSON) lub określoną podsekcję, której chcesz użyć, na przykład sekcję Outputs (dane wyjściowe), a nawet sekcję nagłówki.

1. Postępuj zgodnie z instrukcjami dotyczącymi otwierania **statycznego pola wynikowego** dla akcji w temacie [Konfigurowanie wyników statycznych](#set-up-static-results).

1. Po otwarciu **statycznego pola wyniku** wybierz jeden z kroków:

   * Aby wkleić kompletny obiekt JSON, wybierz pozycję **Przełącz do trybu JSON** ( ![ Wybierz polecenie Przełącz do trybu JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ):

     ![Wybierz pozycję "Przełącz do trybu JSON", aby uzyskać pełny obiekt](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Aby wkleić tylko sekcję JSON, obok etykiety tej sekcji Wybierz pozycję **Przełącz do trybu JSON** dla tej sekcji, na przykład:

     ![Wybierz pozycję "Przełącz do trybu JSON" dla danych wyjściowych](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. W edytorze JSON wklej poprzednio skopiowany kod JSON.

   ![Tryb JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Po zakończeniu wybierz pozycję **Gotowe**. Aby powrócić do projektanta, wybierz opcję **Przełącz tryb** edytora ( ![ Wybierz tryb edytora przełącznika ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Znajdź uruchomienia, które używają wyników statycznych

Historia przebiegów aplikacji logiki identyfikuje uruchomienia, w których akcje używają wyników statycznych. Aby znaleźć te przebiegi, wykonaj następujące kroki:

1. W menu głównym aplikacji logiki wybierz pozycję **Przegląd**. 

1. W okienku po prawej stronie w obszarze **historia uruchamiania**Znajdź kolumnę **statyczne wyniki** . 

   Każdy przebieg obejmujący akcje z wynikami ma **statyczną kolumnę wyników** ustawioną na wartość **włączone**, na przykład:

   ![Historia uruchamiania — kolumna wyników statycznych](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Aby wyświetlić akcje, które używają wyników statycznych, wybierz przebieg, w którym w kolumnie **wyniki statyczne** jest ustawiona wartość **włączone**.

   Akcje, które używają wyników statycznych, pokazują ikonę testową ( ![ ikona dla wyników statycznych ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), na przykład:

   ![Historia uruchamiania — akcje używające wyników statycznych](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Wyłącz wyniki statyczne

Wyłączenie wyników statycznych nie powoduje odebrania wartości z ostatniej instalacji. Dlatego po następnym włączeniu wyników statycznych można nadal korzystać z poprzednich wartości.

1. Znajdź akcję, w której chcesz wyłączyć statyczne dane wyjściowe. W prawym górnym rogu akcji wybierz ikonę testera testowego ( ![ ikona dla wyników statycznych ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ).

   ![Wyłącz wyniki statyczne](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Wybierz pozycję **Wyłącz wynik statyczny**  >  **Done**.

   ![Wyłącz wyniki statyczne](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Tematy pomocy

Aby uzyskać więcej informacji na temat tego ustawienia w podstawowych definicjach przepływów pracy, zobacz temat [statyczne wyniki — dokumentacja schematu dla języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md#static-results) i [runtimeConfiguration. staticResult — ustawienia konfiguracji środowiska uruchomieniowego](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)