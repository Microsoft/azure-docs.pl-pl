---
title: Komunikaty RosettaNet dla integracji B2B
description: Komunikaty programu Exchange RosettaNet w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91565109"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Komunikaty programu Exchange RosettaNet dla integracji z usługą B2B Enterprise w Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) to konsorcjum niedochodowe, które ma standardowe procesy do udostępniania informacji służbowych. Te standardy są często używane na potrzeby procesów łańcucha dostaw i są szeroko rozpowszechnione w branżach półprzewodnikowych, elektronicznych i logistycznych. Konsorcjum RosettaNet tworzy i utrzymuje procesy interfejsu partnera (pestek), które zapewniają wspólne definicje procesów firmy dla wszystkich wymian komunikatów RosettaNet. RosettaNet opiera się na języku XML i definiuje wskazówki dotyczące komunikatów, interfejsy dla procesów biznesowych i struktury implementacji komunikacji między firmami.

W [Azure Logic Apps](../logic-apps/logic-apps-overview.md)łącznik RosettaNet pomaga tworzyć rozwiązania integracji obsługujące standardy RosettaNet. Łącznik jest oparty na programie RosettaNet implementation Framework (RNIF) w wersji 2.0.01. RNIF to struktura aplikacji otwartej sieci, która umożliwia partnerom biznesowym współdziałanie z RosettaNet pestek. Ta struktura definiuje strukturę wiadomości, wymaganie potwierdzeń, kodowanie MIME (Multipurpose Internet Mail Extensions) i podpis cyfrowy.

W tym celu łącznik zapewnia następujące możliwości:

* Koduj lub odbieraj komunikaty RosettaNet.
* Dekodowanie lub wysyłanie komunikatów RosettaNet.
* Poczekaj na odpowiedź i wygenerowanie powiadomienia o niepowodzeniu.

Dla tych możliwości łącznik obsługuje wszystkie pestek, które są zdefiniowane przez RNIF 2.0.01. Komunikacja z partnerem może być synchroniczna lub asynchroniczna.

## <a name="rosettanet-concepts"></a>Pojęcia RosettaNet

Poniżej przedstawiono kilka pojęć i terminów, które są unikatowe dla specyfikacji RosettaNet i są ważne podczas tworzenia integracji opartych na RosettaNet:

* **GRACZY**

  Organizacja RosettaNet tworzy i utrzymuje procesy interfejsu partnera (pestek), które zapewniają wspólne definicje procesów firmy dla wszystkich wymian komunikatów RosettaNet. Każda specyfikacja PIP zawiera plik definicji typu dokumentu (DTD) i dokument zawierający wytyczne dotyczące komunikatów. Plik DTD definiuje strukturę wiadomości zawartości usługi. Dokument dotyczący komunikatów, który jest plikiem HTML, który jest czytelny dla człowieka, określa ograniczenia na poziomie elementu. Razem te pliki zapewniają kompletną definicję procesu biznesowego.

   Wartości pestek są kategoryzowane przez funkcję biznesową wysokiego poziomu lub klaster oraz podfunkcję lub segment. Na przykład "3A4" to PIP dla zamówienia zakupu, natomiast "3" jest funkcją zarządzania kolejnością, a "3A" to funkcja podrzędnego wejścia kolejności &. Aby uzyskać więcej informacji, zobacz [witrynę RosettaNet](https://resources.gs1us.org).

* **Akcja**

  Częścią PIP, komunikaty akcji to wiadomości biznesowe wymieniane między partnerami.

* **Wysłać**

   Część PIP, komunikaty sygnalizujące są potwierdzeniami, które są wysyłane w odpowiedzi na komunikaty akcji.

* **Pojedyncza Akcja i podwójna akcja**

  Jedyną odpowiedzią dla PIP jednego z nich jest komunikat z sygnałem potwierdzającym. W przypadku PIP podwójnej akcji inicjator odbiera komunikat odpowiedzi i odpowiedzi z potwierdzeniem oprócz przepływu komunikatu o pojedynczej akcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania umowy i innych artefaktów B2B. To konto integracji musi być skojarzone z subskrypcją platformy Azure.

* Co najmniej dwóch [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) zdefiniowanych w ramach konta integracji i skonfigurowanych przy użyciu kwalifikatora "Duns" w obszarze **tożsamości biznesowe**

* Konfiguracja procesu PIP, która jest wymagana do wysyłania i odbierania komunikatów RosettaNet na koncie integracji. W konfiguracji procesu są przechowywane wszystkie charakterystyki konfiguracji PIP. Następnie można odwołać się do tej konfiguracji podczas tworzenia umowy z partnerem. Aby utworzyć konfigurację procesu PIP na koncie integracji, zobacz [Add PIP Process Configuration](#add-pip).

* Opcjonalne [Certyfikaty](../logic-apps/logic-apps-enterprise-integration-certificates.md) służące do szyfrowania, odszyfrowywania lub podpisywania komunikatów przekazywanych na konto integracji. Certyfikaty są wymagane tylko w przypadku korzystania z podpisywania lub szyfrowania.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Dodaj konfigurację procesu PIP

Aby dodać konfigurację procesu PIP do konta integracji, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz konto integracji.

1. W okienku **Przegląd** wybierz KAFELEK **RosettaNet PIP** .

   ![Wybierz kafelek RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. W obszarze **ROSETTANET PIP** wybierz pozycję **Dodaj**. Podaj szczegóły dotyczące PIP.

   ![Dodaj szczegóły RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa PIP |
   | **Kod PIP** | Tak | Kod trzycyfrowy PIP. Aby uzyskać więcej informacji, zobacz [RosettaNet pestek](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Wersja PIP** | Tak | Numer wersji PIP, który jest dostępny w oparciu o wybrany kod PIP |
   ||||

   Więcej informacji o tych właściwościach PIP można znaleźć w [witrynie sieci Web RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK**, aby utworzyć konfigurację PIP.

1. Aby wyświetlić lub edytować konfigurację procesu, wybierz pozycję PIP, a następnie wybierz pozycję **Edytuj jako plik JSON**.

   Wszystkie ustawienia konfiguracji procesu pochodzą ze specyfikacji PIP. Logic Apps wypełnia większość ustawień wartościami domyślnymi, które są najczęściej używanymi wartościami dla tych właściwości.

   ![Edytuj konfigurację RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Upewnij się, że ustawienia odpowiadają wartościom w odpowiedniej specyfikacji PIP i spełniają potrzeby biznesowe. W razie potrzeby zaktualizuj wartości w formacie JSON i Zapisz te zmiany.

## <a name="create-rosettanet-agreement"></a>Utwórz umowę RosettaNet

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz konto integracji, jeśli nie jest jeszcze otwarte.

1. W okienku **Przegląd** wybierz kafelek **umowy** .

   ![Wybierz kafelek umowy](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. W obszarze **umowy** wybierz pozycję **Dodaj**. Podaj szczegóły umowy.

   ![Dodaj szczegóły umowy](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa umowy |
   | **Typ umowy** | Tak | Wybierz pozycję **RosettaNet**. |
   | **Partner hosta** | Tak | Umowa wymaga zarówno hosta, jak i partnera gościa. Partner hosta reprezentuje organizację, która konfiguruje umowę. |
   | **Tożsamość hosta** | Tak | Identyfikator dla partnera hosta |
   | **Partner gościa** | Tak | Umowa wymaga zarówno hosta, jak i partnera gościa. Partner gościa reprezentuje organizację, która wykonuje działalność z partnerem hosta. |
   | **Tożsamość gościa** | Tak | Identyfikator partnera gościa |
   | **Ustawienia odbierania** | Różnie | Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez partnera hosta |
   | **Ustawienia wysyłania** | Różnie | Te właściwości mają zastosowanie do wszystkich komunikatów wysyłanych przez partnera hosta |  
   | **Odwołania do RosettaNet PIP** | Tak | Odwołanie PIP dla umowy. Wszystkie komunikaty RosettaNet wymagają konfiguracji PIP. |
   ||||

1. Aby skonfigurować umowę do odbierania wiadomości przychodzących z partnera gościa, wybierz pozycję **Odbierz ustawienia**.

   ![Ustawienia odbierania](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Aby włączyć podpisywanie lub szyfrowanie dla wiadomości przychodzących, w obszarze **komunikaty** wybierz opcję **komunikat powinien być podpisany** lub **komunikat powinien być szyfrowany** odpowiednio.

      | Właściwość | Wymagane | Opis |
      |----------|----------|-------------|
      | **Komunikat powinien być podpisany** | Nie | Podpisz wiadomości przychodzące z wybranym certyfikatem. |
      | **Certyfikat** | Tak, jeśli podpisywanie jest włączone | Certyfikat używany do podpisywania |
      | **Włącz szyfrowanie komunikatów** | Nie | Szyfruj wiadomości przychodzące z wybranym certyfikatem. |
      | **Certyfikat** | Tak, jeśli szyfrowanie jest włączone | Certyfikat do użycia na potrzeby szyfrowania |
      ||||

   1. W każdym z wybranych opcji wybierz odpowiedni [certyfikat](./logic-apps-enterprise-integration-certificates.md), który został wcześniej dodany do konta integracji, aby użyć do podpisywania lub szyfrowania.

1. Aby skonfigurować umowę do wysyłania wiadomości do partnera gościa, wybierz pozycję **Wyślij ustawienia**.

   ![Ustawienia wysyłania](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Aby włączyć podpisywanie lub szyfrowanie dla wiadomości wychodzących, w obszarze **komunikaty** wybierz opcję **Włącz podpisywanie wiadomości** lub **Włącz szyfrowanie wiadomości** odpowiednio. W każdym z wybranych opcji wybierz odpowiedni algorytm i [certyfikat](./logic-apps-enterprise-integration-certificates.md), które zostały wcześniej dodane do konta integracji, aby użyć do podpisywania lub szyfrowania.

      | Właściwość | Wymagane | Opis |
      |----------|----------|-------------|
      | **Włącz podpisywanie komunikatów** | Nie | Podpisz wiadomości wychodzące z wybranym algorytmem podpisywania i certyfikatem. |
      | **Algorytm podpisywania** | Tak, jeśli podpisywanie jest włączone | Algorytm podpisywania, który ma być używany, na podstawie wybranego certyfikatu |
      | **Certyfikat** | Tak, jeśli podpisywanie jest włączone | Certyfikat używany do podpisywania |
      | **Włącz szyfrowanie komunikatów** | Nie | Szyfruj wychodzące z wybranym algorytmem szyfrowania i certyfikatem. |
      | **Algorytm szyfrowania** | Tak, jeśli szyfrowanie jest włączone | Algorytm szyfrowania, który ma być używany na podstawie wybranego certyfikatu |
      | **Certyfikat** | Tak, jeśli szyfrowanie jest włączone | Certyfikat do użycia na potrzeby szyfrowania |
      ||||

   1. W obszarze **punkty końcowe** Określ wymagane adresy URL, które mają być używane do wysyłania komunikatów akcji i potwierdzeń.

      | Właściwość | Wymagane | Opis |
      |----------|----------|-------------|
      | **Adres URL akcji** |  Tak | Adres URL służący do wysyłania komunikatów akcji. Adres URL jest polem wymaganym dla wiadomości synchronicznych i asynchronicznych. |
      | **Adres URL potwierdzenia** | Tak | Adres URL służący do wysyłania wiadomości potwierdzających. Adres URL jest polem wymaganym dla komunikatów asynchronicznych. |
      ||||

1. Aby skonfigurować umowę za pomocą RosettaNet PIP References for Partners, wybierz pozycję **ROSETTANET PIP References**. W obszarze **Nazwa PIP** wybierz nazwę utworzonego wcześniej PIP.

   ![Odwołania PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Wybór wypełnia pozostałe właściwości, które są oparte na urządzeniu PIP skonfigurowanym na koncie integracji. W razie potrzeby można zmienić **rolę PIP**.

   ![Wybrany PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Po wykonaniu tych kroków możesz wysyłać i odbierać komunikaty RosettaNet.

## <a name="rosettanet-templates"></a>Szablony RosettaNet

Aby przyspieszyć opracowywanie i zalecać wzorce integracji, można używać szablonów aplikacji logiki do dekodowania i kodowania komunikatów RosettaNet. Podczas tworzenia aplikacji logiki można wybrać ją z galerii szablonów w Projektancie aplikacji logiki. Szablony te można również znaleźć w [repozytorium GitHub, aby uzyskać Azure Logic Apps](https://github.com/Azure/logicapps).

![Szablony RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Odbierz lub Dekoduj RosettaNet komunikatów

1. [Utwórz pustą aplikację logiki](quickstart-create-first-logic-app-workflow.md).

1. [Połącz swoje konto integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account) z aplikacją logiki.

1. Aby można było dodać akcję dekodowanie komunikatu RosettaNet, należy dodać wyzwalacz do uruchamiania aplikacji logiki, takiej jak wyzwalacz żądania.

1. Po dodaniu wyzwalacza wybierz pozycję **nowy krok**.

   ![Dodaj wyzwalacz żądania](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. W polu wyszukiwania wprowadź ciąg "RosettaNet", a następnie wybierz tę akcję: **dekodowanie RosettaNet**

   ![Znajdź i wybierz akcję "decodeing" RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Podaj informacje o właściwościach akcji:

   ![Zrzut ekranu pokazujący, gdzie są podane informacje o właściwościach akcji.](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Wiadomość** | Tak | Komunikat RosettaNet do zdekodowania  |
   | **Nagłówki** | Tak | Nagłówki HTTP, które dostarczają wartości dla wersji, która jest wersją RNIF, oraz typ odpowiedzi, który wskazuje typ komunikacji między partnerami i może być synchroniczny lub asynchroniczny |
   | **Role** | Tak | Rola partnera hosta w PIP |
   ||||

   Z poziomu akcji dekodowania RosettaNet, dane wyjściowe oraz inne właściwości, obejmują **sygnał wychodzący**, który można wybrać do kodowania i powrotu do partnera, lub wykonać wszelkie inne działania w tych danych wyjściowych.

## <a name="send-or-encode-rosettanet-messages"></a>Wysyłanie lub kodowanie komunikatów RosettaNet

1. [Utwórz pustą aplikację logiki](quickstart-create-first-logic-app-workflow.md).

1. [Połącz swoje konto integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account) z aplikacją logiki.

1. Aby można było dodać akcję w celu zakodowania komunikatu RosettaNet, należy dodać wyzwalacz do uruchamiania aplikacji logiki, takiej jak wyzwalacz żądania.

1. Po dodaniu wyzwalacza wybierz pozycję **nowy krok**.

   ![Dodaj wyzwalacz żądania](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. W polu wyszukiwania wprowadź ciąg "RosettaNet", a następnie wybierz tę akcję: **RosettaNet Encode**

   ![Znajdź i wybierz akcję "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Podaj informacje o właściwościach akcji:

   ![Podaj szczegóły akcji](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Wiadomość** | Tak | Komunikat RosettaNet do zakodowania  |
   | **Partner hosta** | Tak | Nazwa partnera hosta |
   | **Partner gościa** | Tak | Nazwa partnera gościa |
   | **Kod PIP** | Tak | Kod PIP |
   | **Wersja PIP** | Tak | Wersja PIP |  
   | **Tożsamość wystąpienia PIP** | Tak | Unikatowy identyfikator tego komunikatu PIP |  
   | **Typ wiadomości** | Tak | Typ wiadomości do zakodowania |  
   | **Role** | Tak | Rola partnera hosta |
   ||||

   Zakodowany komunikat jest teraz gotowy do wysłania do partnera.

1. Aby wysłać zakodowany komunikat, w tym przykładzie zostanie użyta akcja **http** , której nazwa została zmieniona na "Wyślij komunikat szyfrowany przez protokół HTTP do partnera".

   ![Akcja HTTP do wysyłania komunikatu RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Zgodnie ze standardami RosettaNet transakcje biznesowe są uważane za ukończone tylko wtedy, gdy wszystkie kroki zdefiniowane przez PIP zostały ukończone.

1. Gdy host wyśle zakodowany komunikat do partnera, Host czeka na sygnał i potwierdzenie. Aby wykonać to zadanie, Dodaj akcję **RosettaNet poczekaj na odpowiedź** .

   ![Dodaj akcję "Oczekiwanie na odpowiedź" RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Czas trwania oczekiwania i liczba ponownych prób jest oparty na konfiguracji PIP na koncie integracji. Jeśli odpowiedź nie zostanie odebrana, ta akcja spowoduje wygenerowanie powiadomienia o niepowodzeniu. Aby obsłużyć ponawianie prób, zawsze umieszczaj **kodowanie** i **czekaj na akcje odpowiedzi** w pętli **until** .

   ![Do pętli z akcjami RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Następne kroki

* Informacje dotyczące sprawdzania poprawności, przekształcania i innych operacji na komunikatach przy użyciu [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
