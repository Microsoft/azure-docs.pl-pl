---
title: Przetwarzanie wsadowe komunikatów EDI jako grupy
description: Wysyłanie i odbieranie komunikatów EDI jako partii, grup lub kolekcji przy użyciu przetwarzania wsadowego w Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000406"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Komunikaty programu Exchange EDI jako partie lub grupy między partnerami handlowymi w Azure Logic Apps

W scenariuszach Business-Business (B2B) partnerzy często wymieniają komunikaty w grupach lub *partiach*. Podczas tworzenia rozwiązania wsadowego za pomocą Logic Apps można wysyłać komunikaty do partnerów handlowych i przetwarzać je razem w partiach. W tym artykule pokazano, jak można wsadowo przetwarzać komunikaty EDI przy użyciu X12 na przykład przez utworzenie aplikacji logiki "nadawca usługi Batch" i aplikacji logiki "odbiorca usługi Batch". 

Przetwarzanie wsadowe komunikatów X12 działa jak przetwarzanie wsadowe innych komunikatów; używasz wyzwalacza wsadowego, który zbiera komunikaty do partii i akcję wsadową, która wysyła komunikaty do zadania wsadowego. Ponadto X12 Batch zawiera krok kodowania X12, zanim komunikaty przejdą do partnera handlowego lub innego miejsca docelowego. Aby dowiedzieć się więcej na temat wyzwalacza i akcji wsadowej, zobacz [Batch Process messages](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

W tym artykule utworzysz rozwiązanie wsadowe, tworząc dwie aplikacje logiki w ramach tej samej subskrypcji platformy Azure, regionu platformy Azure i wykonując tę konkretną kolejność:

* Aplikacja logiki ["odbiorca wsadowa"](#receiver) , która akceptuje i zbiera komunikaty do partii, dopóki nie zostaną spełnione określone kryteria dotyczące zwalniania i przetwarzania tych komunikatów. W tym scenariuszu odbiorca usługi Batch koduje również komunikaty w partii przy użyciu określonej umowy X12 lub tożsamości partnerów.

  Upewnij się, że najpierw utworzysz odbiornik wsadowy, aby później można było wybrać miejsce docelowe partii podczas tworzenia nadawcy wsadowego.

* Aplikacja logiki ["nadawcy partii"](#sender) , która wysyła komunikaty do wcześniej utworzonego odbiorcy partii. 

Upewnij się, że odbiorca usługi Batch i nadawca usługi Batch korzystają z tej samej subskrypcji platformy Azure *i* regionu platformy Azure. Jeśli nie, nie możesz wybrać odbiorcy partii podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przykładem, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). Możesz też [zarejestrować się, aby uzyskać subskrypcję z opcją płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/purchase-options/)użyciem.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Istniejące [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i jest połączone z aplikacjami logiki

* Co najmniej dwóch istniejących [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) na koncie integracji. Każdy partner musi używać kwalifikatora X12 (kod alfa operatora standardowego) jako tożsamości biznesowej we właściwościach partnera.

* Istniejąca [Umowa X12](../logic-apps/logic-apps-enterprise-integration-x12.md) na koncie integracji

* Aby użyć programu Visual Studio, a nie Azure Portal, upewnij się, że [skonfigurowano program Visual Studio do pracy z Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Utwórz odbiorcę partii X12

Przed wysłaniem wiadomości do partii, ta partia musi znajdować się jako miejsce docelowe, w którym są wysyłane te wiadomości. Najpierw należy utworzyć aplikację logiki "odbiorca wsadowa", która rozpoczyna się od wyzwalacza **wsadowego** . Dzięki temu podczas tworzenia aplikacji logiki "nadawca partii" można wybrać aplikację logiki odbiorcy partii. Odbiornik usługi Batch kontynuuje zbieranie komunikatów do momentu spełnienia określonych kryteriów dotyczących zwalniania i przetwarzania tych komunikatów. Odbiorcy partii nie muszą wiedzieć niczego o nadawców wsadowych, dlatego nadawcy wsadowy muszą znać miejsce docelowe, do którego są wysyłane wiadomości. 

W przypadku tego odbiorcy usługi Batch należy określić tryb wsadowy, nazwę, kryteria wydania, umowę X12 oraz inne ustawienia. 

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Utwórz aplikację logiki o tej nazwie: "BatchX12Messages"

2. [Połącz aplikację logiki z kontem integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. W projektancie Logic Apps, Dodaj wyzwalacz **wsadowy** , który uruchamia przepływ pracy aplikacji logiki. W polu wyszukiwania wprowadź ciąg "Batch" jako filtr. Wybierz ten wyzwalacz: **wiadomości wsadowe**

   ![Dodaj wyzwalacz partii](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Ustaw właściwości odbiorcy partii: 

   | Właściwość | Wartość | Uwagi | 
   |----------|-------|-------|
   | **Tryb wsadowy** | Śródwierszowo |  |  
   | **Nazwa partii** | TestBatch | Dostępne tylko z **wbudowanym** trybem wsadowym | 
   | **Kryteria wydania** | Na podstawie liczby komunikatów na podstawie harmonogramu | Dostępne tylko z **wbudowanym** trybem wsadowym | 
   | **Liczba komunikatów** | 10 | Dostępne tylko w przypadku kryteriów wydania **opartych na liczbie komunikatów** | 
   | **Interwał** | 10 | Dostępne tylko w przypadku kryteriów wydania **opartych na harmonogramie** | 
   | **Częstotliwość** | minuta | Dostępne tylko w przypadku kryteriów wydania **opartych na harmonogramie** | 
   ||| 

   ![Podaj szczegóły wyzwalacza partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > W tym przykładzie nie skonfigurowano partycji dla partii, więc każda partia używa tego samego klucza partycji. Aby dowiedzieć się więcej o partycjach, zobacz [Batch Process messages](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Teraz Dodaj akcję, która koduje każdą partię: 

   1. W obszarze wyzwalacza wsadowego wybierz pozycję **nowy krok**.

   2. W polu wyszukiwania wprowadź ciąg "X12 Batch" jako filtr, a następnie wybierz tę akcję (dowolna wersja): **kodowanie wsadowe <*wersji*>-X12** 

      ![Wybierz akcję kodowania partii X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Jeśli nie masz wcześniej połączenia z kontem integracji, Utwórz połączenie teraz. Podaj nazwę połączenia, wybierz odpowiednie konto integracji, a następnie wybierz pozycję **Utwórz**.

      ![Utwórz połączenie między koderem usługi Batch i kontem integracji](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Ustaw następujące właściwości dla akcji kodera usługi Batch:

      | Właściwość | Opis |
      |----------|-------------|
      | **Nazwa umowy X12** | Otwórz listę i wybierz istniejącą umowę. <p>Jeśli lista jest pusta, upewnij się, że [Twoja aplikacja logiki jest połączona z kontem integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) , dla którego chcesz uzyskać umowę. | 
      | **Partia zadań** | Kliknij wewnątrz tego pola i po wyświetleniu listy zawartości dynamicznej wybierz token **nazwy wsadu** . | 
      | **PartitionName** | Kliknij wewnątrz tego pola i po wyświetleniu listy zawartości dynamicznej wybierz token **nazwy partycji** . | 
      | **Elementy** | Zamknij pole szczegóły elementu, a następnie kliknij wewnątrz tego pola. Po wyświetleniu listy zawartości dynamicznej wybierz token **elementy wsadowe** . | 
      ||| 

      ![Szczegóły akcji kodowania partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Dla pola **Items (elementy** ):

      ![Elementy akcji kodowania partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Zapisz aplikację logiki. 

7. Jeśli używasz programu Visual Studio, upewnij się, że [aplikacja logiki odbiornika usługi Batch została wdrożona na platformie Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). W przeciwnym razie nie można wybrać odbiorcy partii podczas tworzenia nadawcy wsadowego.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby upewnić się, że odbiornik usługi Batch działa zgodnie z oczekiwaniami, można dodać akcję HTTP na potrzeby testowania i wysłać wsadowy komunikat do [usługi Request bin](https://requestbin.com/). 

1. W akcji kodowania X12 wybierz pozycję **nowy krok**. 

2. W polu wyszukiwania wprowadź ciąg "http" jako filtr. Wybierz tę akcję: **http-http**
    
   ![Wybieranie akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Ustaw właściwości akcji HTTP:

   | Właściwość | Opis | 
   |----------|-------------|
   | **Metoda** | Z tej listy wybierz pozycję **post**. | 
   | **Adresu** | Wygeneruj identyfikator URI dla pojemnika żądania, a następnie wprowadź ten identyfikator URI w tym polu. | 
   | **Treść** | Kliknij wewnątrz tego pola i po otwarciu listy zawartości dynamicznej wybierz token **treści** , który jest wyświetlany w sekcji, **zakodować zadanie wsadowe według nazwy umowy**. <p>Jeśli nie widzisz tokenu **treści** , obok pozycji **Koduj zadanie wsadowe według nazwy umowy** wybierz pozycję **Zobacz więcej**. | 
   ||| 

   ![Podaj szczegóły akcji HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Zapisz aplikację logiki. 

   Twoja aplikacja logiki odbiornika wsadu wygląda następująco: 

   ![Zapisywanie aplikacji logiki odbiorcy partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Tworzenie nadawcy partii X12

Teraz należy utworzyć co najmniej jedną aplikację logiki, która wysyła komunikaty do aplikacji logiki odbiorcy partii. W każdym nadawcy partii należy określić aplikację logiki odbiorcy wsadu i nazwę partii, zawartość wiadomości oraz inne ustawienia. Opcjonalnie możesz podać unikatowy klucz partycji, aby podzielić partię na podzbiory w celu zbierania komunikatów z tym kluczem. 

* Upewnij się, że [Twój odbiornik wsadowy](#receiver) został już utworzony, więc podczas tworzenia nadawcy wsadu możesz wybrać istniejącego odbiorcę w usłudze Batch jako partię docelową. Chociaż odbiorcy partii nie muszą wiedzieć niczego o nadawców wsadowych, nadawcy wsadowy muszą wiedzieć, gdzie mają być wysyłane wiadomości. 

* Upewnij się, że odbiorca usługi Batch i nadawca usługi Batch współużytkują ten sam region platformy Azure *i* subskrypcję platformy Azure. Jeśli nie, nie możesz wybrać odbiorcy partii podczas tworzenia nadawcy partii, ponieważ nie są one widoczne dla siebie nawzajem.

1. Utwórz kolejną aplikację logiki o tej nazwie: "SendX12MessagesToBatch" 

2. W polu wyszukiwania wprowadź ciąg "When a żądanie HTTP" jako filtr. Wybierz ten wyzwalacz: **po odebraniu żądania HTTP** 
   
   ![Dodawanie wyzwalacza żądania](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Dodaj akcję wysyłania komunikatów do usługi Batch.

   1. W obszarze Akcja żądania HTTP wybierz pozycję **nowy krok**.

   2. W polu wyszukiwania wprowadź ciąg "Batch" jako filtr. 
   Wybierz listę **Akcje** , a następnie wybierz tę akcję: **wybierz przepływ pracy Logic Apps z wyzwalaczem wsadowym — Wyślij komunikaty do partii**

      ![Wybierz pozycję "Wybierz Logic Apps przepływ pracy z wyzwalaczem wsadowym"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Teraz wybierz utworzoną wcześniej aplikację logiki "BatchX12Messages".

      ![Wybierz aplikację logiki "odbiorca wsadowa"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Wybierz tę akcję: **Batch_messages-<*użytkownika-Batch-Receiver* >**

      ![Wybierz akcję "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Ustaw właściwości nadawcy partii.

   | Właściwość | Opis | 
   |----------|-------------| 
   | **Nazwa partii** | Nazwa wsadu zdefiniowana przez aplikację logiki odbiornika, która jest w tym przykładzie "TestBatch" <p>**Ważne**: Nazwa wsadu jest sprawdzana w czasie wykonywania i musi być zgodna z nazwą określoną przez aplikację logiki odbiornika. Zmiana nazwy wsadu powoduje niepowodzenie nadawcy wsadu. | 
   | **Zawartość komunikatu** | Zawartość wiadomości, która ma zostać wysłana, która jest tokenem **treści** w tym przykładzie. | 
   ||| 
   
   ![Ustawianie właściwości partii](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Zapisz aplikację logiki. 

   Twoja aplikacja logiki nadawcy w usłudze Batch wygląda podobnie do tego przykładu:

   ![Zapisz aplikację logiki nadawcy w usłudze Batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testowanie aplikacji logiki

Aby przetestować rozwiązanie do przetwarzania wsadowego, Opublikuj komunikaty X12 w aplikacji logiki nadawcy usługi Batch z poziomu programu [Poster](https://www.getpostman.com/postman) lub podobnego narzędzia. Wkrótce rozpocznie się pobieranie komunikatów X12 w koszu do żądania, co 10 minut lub w partiach 10, z tym samym kluczem partycji.

## <a name="next-steps"></a>Następne kroki

* [Przetwarzaj komunikaty jako partie](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
