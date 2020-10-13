---
title: Przetwarzanie wsadowe komunikatów jako grupy
description: Wysyłanie i odbieranie komunikatów w grupach między przepływami pracy przy użyciu przetwarzania wsadowego w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87458251"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Wysyłanie, odbieranie i przetwarzanie wsadowe komunikatów w Azure Logic Apps

Aby wysyłać i przetwarzać komunikaty w określony sposób jako grupy, można utworzyć rozwiązanie do przetwarzania wsadowego. To rozwiązanie zbiera komunikaty do *partii* i czeka, aż zostaną spełnione określone kryteria przed zwolnieniem i przetworzeniem wsadowych komunikatów. Przetwarzanie wsadowe może ograniczyć częstotliwość przetwarzania komunikatów przez aplikację logiki.

W tym artykule pokazano, jak utworzyć rozwiązanie wsadowe przez utworzenie dwóch aplikacji logiki w ramach tej samej subskrypcji platformy Azure, regionu platformy Azure i w następującej kolejności:

1. Aplikacja logiki ["odbiorca wsadowa"](#batch-receiver) , która akceptuje i zbiera komunikaty do partii, dopóki nie zostaną spełnione określone kryteria dotyczące zwalniania i przetwarzania tych komunikatów. Upewnij się, że najpierw utworzysz ten odbiornik wsadowy, aby później można było wybrać miejsce docelowe partii podczas tworzenia nadawcy wsadowego.

1. Co najmniej jedna aplikacja logiki ["nadawcy partii"](#batch-sender) , która wysyła komunikaty do wcześniej utworzonego odbiorcy partii.

   Można również określić unikatowy klucz, taki jak numer klienta, *partycji* lub podzielić docelową partię na podzbiory logiczne na podstawie tego klucza. Dzięki temu aplikacja odbiorcy może zbierać wszystkie elementy z tym samym kluczem i przetwarzać je razem.

Odbiorca usługi Batch i nadawca usługi Batch muszą korzystać z tej samej subskrypcji platformy Azure *i* regionu platformy Azure. Jeśli nie, nie możesz wybrać odbiorcy partii podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Możesz też [zarejestrować się, aby uzyskać subskrypcję z opcją płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/purchase-options/)użyciem.

* Konto e-mail z dowolnym [dostawcą poczty e-mail obsługiwanego przez Azure Logic Apps](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aby użyć programu Visual Studio, a nie Azure Portal, upewnij się, że [skonfigurowano program Visual Studio do pracy z Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Utwórz odbiorcę partii

Przed wysłaniem wiadomości do partii, ta partia musi znajdować się jako miejsce docelowe, w którym są wysyłane te wiadomości. Najpierw należy utworzyć aplikację logiki "odbiorca wsadowa", która rozpoczyna się od wyzwalacza **wsadowego** . Dzięki temu podczas tworzenia aplikacji logiki "nadawca partii" można wybrać aplikację logiki odbiorcy partii. Odbiornik usługi Batch kontynuuje zbieranie komunikatów do momentu spełnienia określonych kryteriów dotyczących zwalniania i przetwarzania tych komunikatów. Odbiorcy partii nie muszą wiedzieć niczego o nadawców wsadowych, dlatego nadawcy wsadowy muszą znać miejsce docelowe, do którego są wysyłane wiadomości.

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Utwórz aplikację logiki o tej nazwie: `BatchReceiver`

1. W Projektancie aplikacji logiki Dodaj wyzwalacz **wsadowy** , który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź `batch` i wybierz następujący wyzwalacz: **wiadomości wsadowe**

   ![Dodaj wyzwalacz "komunikaty wsadowe"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Ustaw następujące właściwości dla odbiorcy usługi Batch:

   | Właściwość | Opis |
   |----------|-------------|
   | **Tryb wsadowy** | - **Wbudowane**: w celu zdefiniowania kryteriów wydania wewnątrz wyzwalacza usługi Batch <br>- **Konto integracji**: w celu zdefiniowania wielu konfiguracji kryteriów wydania przy użyciu [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Przy użyciu konta integracji można zachować te konfiguracje w jednym miejscu, a nie w oddzielnych aplikacjach logiki. |
   | **Nazwa partii** | Nazwa partii, która jest w tym przykładzie "TestBatch" i ma zastosowanie tylko do **wbudowanego** trybu wsadowego |
   | **Kryteria wydania** | Stosuje się tylko do **wbudowanego** trybu wsadowego i wybiera kryteria do spełnienia przed przetworzeniem każdej partii: <p>- **Liczba komunikatów na podstawie**: Zwolnij partię na podstawie liczby komunikatów zebranych przez partię. <br>- **Na podstawie rozmiaru**: Zwolnij partię na podstawie łącznego rozmiaru w bajtach dla wszystkich komunikatów zbieranych przez tę partię. <br>- **Harmonogram**: Zwolnij partię na podstawie harmonogramu cyklu, który określa interwał i częstotliwość. W opcjach zaawansowanych można także wybrać strefę czasową i podać datę i godzinę rozpoczęcia. <br>- **Zaznacz wszystko**: Użyj wszystkich określonych kryteriów. |
   | **Liczba komunikatów** | Liczba komunikatów do zebrania w partii, na przykład 10 komunikatów. Limit partii to 8 000 komunikatów. |
   | **Rozmiar wsadu** | Łączny rozmiar w bajtach do zebrania w partii, na przykład 10 MB. Limit rozmiaru partii wynosi 80 MB. |
   | **Zaplanuj** | Interwał i częstotliwość między wersjami usługi Batch, na przykład 10 minut. Cykl minimalny wynosi 60 sekund lub 1 minutę. Minuty ułamkowe są efektywnie zaokrąglane do 1 minuty. Aby określić strefę czasową lub datę i godzinę rozpoczęcia, Otwórz listę **Dodaj nowy parametr** i wybierz odpowiednie właściwości. |
   |||

   > [!NOTE]
   >
   > W przypadku zmiany kryteriów wydania, gdy wyzwalacz nadal zawiera wsadowe, ale niewysłane komunikaty, wyzwalacz korzysta z zaktualizowanych kryteriów wydania do obsługi niewysłanych wiadomości.

   Ten przykład pokazuje wszystkie kryteria, ale dla własnego testowania Wypróbuj tylko jedno kryterium:

   ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Teraz Dodaj co najmniej jedną akcję, która przetwarza każdą partię.

   W tym przykładzie Dodaj akcję, która wysyła wiadomość e-mail po wyzwoleniu wyzwalacza usługi Batch. Wyzwalacz uruchamia i wysyła wiadomość e-mail, gdy partia zawiera 10 komunikatów, osiągnie 10 MB lub po upływie 10 minut.

   1. W obszarze wyzwalacza wsadowego wybierz pozycję **nowy krok**.

   1. W polu wyszukiwania wprowadź `send email` jako filtr. Na podstawie dostawcy poczty e-mail wybierz łącznik poczty e-mail.

      Jeśli na przykład masz konto służbowe, np @fabrikam.com @fabrikam.onmicrosoft.com . lub, wybierz łącznik **Microsoft 365 Outlook** . Jeśli masz konto osobiste, np @outlook.com @hotmail.com . lub, wybierz łącznik **Outlook.com** . W tym przykładzie używa łącznika programu Outlook Microsoft 365.

   1. Wybierz akcję "Wyślij wiadomość e-mail" dla dostawcy, na przykład:

      ![Wybierz akcję "Wyślij wiadomość e-mail" dla dostawcy poczty e-mail](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. W razie potrzeby zaloguj się do swojego konta e-mail.

1. Ustaw właściwości dodanej akcji.

   * W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail.

   * W polu **temat** , gdy zostanie wyświetlona lista zawartości dynamicznej, wybierz pole **Nazwa partycji** .

     ![Z listy zawartość dynamiczna wybierz pozycję "Nazwa partycji".](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Później w nadawcy wsadowym można określić unikatowy klucz partycji, który dzieli docelową partię na podzestawy logiczne, w których można wysyłać wiadomości. Każdy zestaw ma unikatowy numer, który jest generowany przez aplikację logiki nadawcy partii. Ta funkcja umożliwia użycie pojedynczej partii z wieloma podzestawami i zdefiniowanie każdego podzestawu z podaną nazwą.

     > [!IMPORTANT]
     > Partycja ma limit 5 000 komunikatów lub 80 MB. Jeśli spełniony jest dowolny warunek, Logic Apps może zwolnić partię, nawet jeśli zdefiniowany warunek wydania nie zostanie spełniony.

   * W polu **treść** , gdy zostanie wyświetlona lista zawartości dynamicznej, zaznacz pole **Identyfikator wiadomości** .

     Projektant aplikacji logiki automatycznie dodaje pętlę **for each** wokół akcji Wyślij wiadomość e-mail, ponieważ ta akcja traktuje dane wyjściowe z poprzedniej akcji jako kolekcji, a nie do partii.

     ![Dla opcji "treść" Wybierz pozycję "identyfikator wiadomości"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Zapisz aplikację logiki. Odbiorca wsadowy został utworzony.

    ![Zapisywanie aplikacji logiki](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Jeśli używasz programu Visual Studio, przed przejściem do następnej sekcji upewnij się, że najpierw [ *wdrożono* aplikację logiki odbiorcy usługi Batch na platformie Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). W przeciwnym razie nie można wybrać odbiorcy partii podczas tworzenia nadawcy wsadowego.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Utwórz nadawcę partii

Teraz należy utworzyć co najmniej jedną aplikację logiki nadawcy partii, która wysyła komunikaty do aplikacji logiki odbiorcy partii. W każdym nadawcy partii należy określić odbiorcę i nazwę partii wiadomości oraz inne ustawienia. Opcjonalnie możesz podać unikatowy klucz partycji, aby podzielić partię na logiczne podzbiory dla zbierania komunikatów z tym kluczem.

* Upewnij się, że wcześniej [utworzono i wdrożono odbiorcę w usłudze](#batch-receiver) Batch, dlatego podczas tworzenia nadawcy w usłudze Batch można wybrać istniejącego odbiorcę w usłudze Batch jako wsad docelowy. Chociaż odbiorcy partii nie muszą wiedzieć niczego o nadawców wsadowych, nadawcy wsadowy muszą wiedzieć, gdzie mają być wysyłane wiadomości.

* Upewnij się, że odbiorca usługi Batch i nadawca usługi Batch współużytkują ten sam region platformy Azure *i* subskrypcję platformy Azure. Jeśli nie, nie możesz wybrać odbiorcy partii podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

1. Utwórz kolejną aplikację logiki o tej nazwie: `BatchSender`

   1. W polu wyszukiwania wprowadź `recurrence` jako filtr. Z listy Wyzwalacze wybierz ten wyzwalacz: **cykl**

      ![Dodaj wyzwalacz cykliczny](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Ustaw interwał i częstotliwość uruchamiania aplikacji logiki nadawcy co minutę.

      ![Ustawianie częstotliwości i interwału dla wyzwalacza cyklicznego](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Dodaj nową akcję do wysyłania komunikatów do usługi Batch.

   1. W obszarze wyzwalacz **cyklu** wybierz pozycję **nowy krok**.

   1. W polu wyszukiwania wprowadź `batch` jako filtr, a następnie wybierz tę akcję: **Wybierz przepływ pracy Logic Apps z wyzwalaczem wsadowym**

      ![Wybierz pozycję "Wybierz Logic Apps przepływ pracy z wyzwalaczem wsadowym"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      Zostanie wyświetlona lista, która zawiera tylko te aplikacje logiki, które mają wyzwalacze usługi Batch i istnieją w tym samym regionie platformy Azure *i* subskrypcji platformy Azure jako aplikację logiki nadawcy usługi Batch.

   1. Z listy Aplikacje logiki Wybierz utworzoną wcześniej aplikację logiki odbiorcy usługi Batch.

      ![Wybierz aplikację logiki odbiorcy partii](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Jeśli używasz programu Visual Studio i nie widzisz żadnych odbiorników usługi Batch do wybrania, sprawdź, czy wcześniej utworzono i wdrożono odbiorcę usługi Batch na platformie Azure. Jeśli nie, Dowiedz się, [jak wdrożyć aplikację logiki odbiorcy usługi Batch na platformie Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure).

   1. Z listy Akcje wybierz tę akcję: **Batch_messages-<*Twojej-Logic-App-Name* > **

      ![Wybierz tę akcję: "Batch_messages-<> aplikacji logiki](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. Ustaw właściwości nadawcy partii:

   | Właściwość | Opis |
   |----------|-------------|
   | **Nazwa partii** | Nazwa wsadu zdefiniowana przez aplikację logiki odbiornika, która jest `TestBatch` w tym przykładzie <p>**Ważne**: Nazwa wsadu jest sprawdzana w czasie wykonywania i musi być zgodna z nazwą określoną przez aplikację logiki odbiornika. Zmiana nazwy wsadu powoduje niepowodzenie nadawcy wsadu. |
   | **Zawartość komunikatu** | Zawartość wiadomości, która ma zostać wysłana. |
   |||

   > [!NOTE]
   > Wartości **nazwy wyzwalacza** i właściwości **przepływu pracy** są automatycznie wypełniane w wybranej aplikacji logiki.

   Na potrzeby tego przykładu Dodaj wyrażenie, które Wstawia bieżącą datę i godzinę do zawartości wiadomości wysyłanej do partii:

   1. Kliknij wewnątrz pola **zawartość wiadomości** .

   1. Gdy zostanie wyświetlona lista zawartości dynamicznej, wybierz pozycję **wyrażenie**.

   1. Wprowadź wyrażenie `utcnow()` , a następnie wybierz **przycisk OK**.

      ![W obszarze "zawartość wiadomości" Wybierz pozycję "wyrażenie", wprowadź "UtcNow ()", a następnie wybierz pozycję "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Teraz Skonfiguruj partycję dla partii. W `BatchReceiver` akcji Otwórz listę **Dodaj nowy parametr** i wybierz następujące właściwości:

   | Właściwość | Opis |
   |----------|-------------|
   | **Nazwa partycji** | Opcjonalny unikatowy klucz partycji służący do dzielenia docelowej partii na podzbiory logiczne i zbierania komunikatów na podstawie tego klucza |
   | **Identyfikator komunikatu** | Opcjonalny identyfikator wiadomości, który jest wygenerowany globalnie unikatowy identyfikator (GUID), gdy jest pusty |
   |||

   Na potrzeby tego przykładu w polu **Nazwa partycji** Dodaj wyrażenie, które generuje liczbę losową z zakresu od 1 do 5. Pozostaw puste pole **Identyfikator wiadomości** .

   1. Kliknij wewnątrz pola **Nazwa partycji** , aby wyświetlić listę zawartości dynamicznej.

   1. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**.

   1. Wprowadź wyrażenie `rand(1,6)` , a następnie wybierz przycisk **OK**.

      ![Konfigurowanie partycji dla docelowej partii](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Ta funkcja **Rand** generuje liczbę z przedziału od 1 do 5. Więc podział tej partii na pięć numerowanych partycji, które to wyrażenie ustawia dynamicznie.

1. Zapisz aplikację logiki. Aplikacja logiki nadawcy będzie teraz wyglądać podobnie do tego przykładu:

   ![Zapisywanie aplikacji logiki nadawcy](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązanie do przetwarzania wsadowego, pozostaw Aplikacje logiki uruchomione przez kilka minut. Wkrótce zaczniesz otrzymywać wiadomości e-mail w grupach z pięcioma, z tym samym kluczem partycji.

Aplikacja logiki nadawcy usługi Batch jest uruchamiana co minutę, generuje liczbę losową z zakresu od 1 do 5 i używa tej wygenerowanej liczby jako klucza partycji docelowej partii, w której wysyłane są komunikaty. Za każdym razem, gdy partia ma pięć elementów z tym samym kluczem partycji, aplikacja logiki odbiornika usługi Batch wyzwala i wysyła pocztę dla każdego komunikatu.

> [!IMPORTANT]
> Po zakończeniu testowania upewnij się, że aplikacja logiki zostanie wyłączona, `BatchSender` Aby zatrzymać wysyłanie komunikatów i uniknąć przeładowania skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partii i wysyłanie komunikatów EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Kompilowanie w ramach definicji aplikacji logiki przy użyciu formatu JSON](../logic-apps/logic-apps-author-definitions.md)
* [Tworzenie aplikacji bezserwerowej w programie Visual Studio przy użyciu Azure Logic Apps i funkcji](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Obsługa wyjątków i rejestrowanie błędów dla aplikacji logiki](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
