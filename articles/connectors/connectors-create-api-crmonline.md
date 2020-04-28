---
title: Łączenie z usługą Dynamics 365
description: Utwórz rekordy i zarządzaj nimi za pomocą interfejsów API REST usługi Dynamics 365 (online) i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789890"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Zarządzanie rekordami usługi Dynamics 365 za pomocą usługi Azure Logic Apps

Dzięki usłudze Azure Logic Apps i łącznikowi usługi Dynamics 365 możesz tworzyć automatyczne zadania i przepływy pracy na podstawie rekordów w usłudze Dynamics 365. Przepływy pracy mogą w ramach konta Dynamics 365 tworzyć rekordy, aktualizować elementy, zwracać rekordy i wykonywać wiele innych akcji. Możesz uwzględnić akcje w aplikacjach logiki, które odbierają odpowiedzi z usługi Dynamics 365 i udostępniają dane wyjściowe dla innych akcji. Na przykład po zaktualizowaniu elementu w usłudze Dynamics 365 można wysłać wiadomość e-mail przy użyciu pakietu Office 365.

W tym artykule pokazano, jak utworzyć aplikację logiki, która tworzy zadanie w usłudze Dynamics 365 za każdym razem, gdy w usłudze Dynamics 365 zostanie utworzony nowy rekord potencjalnego klienta.
Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto Dynamics 365](https://dynamics.microsoft.com)

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta Dynamics 365. Do uruchomienia aplikacji logiki przy użyciu wyzwalacza Dynamics 365 potrzebna jest [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Dodaj wyzwalacz Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Najpierw Dodaj wyzwalacz Dynamics 365, który jest uruchamiany po pojawieniu się nowego rekordu potencjalnego klienta w usłudze Dynamics 365.

1. W [Azure Portal](https://portal.azure.com)Otwórz pustą aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W polu wyszukiwania wprowadź ciąg "Dynamics 365" jako filtr. Na potrzeby tego przykładu na liście Wyzwalacze wybierz ten wyzwalacz: **po utworzeniu rekordu**

   ![Wybierz wyzwalacz](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Jeśli zostanie wyświetlony monit o zalogowanie do systemu Dynamics 365, zaloguj się teraz.

1. Podaj następujące szczegóły wyzwalacza:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Tak | Nazwa wystąpienia usługi Dynamics 365 w organizacji do monitorowania, na przykład "contoso" |
   | **Nazwa jednostki** | Tak | Nazwa jednostki do monitorowania, na przykład "potencjalni klienci" | 
   | **Częstotliwość** | Tak | Jednostka czasu, która ma być używana z interwałami podczas sprawdzania aktualizacji związanych z wyzwalaczem |
   | **Dat** | Tak | Liczba sekund, minut, godzin, dni, tygodnie lub miesięcy, które są przekazywane przed następnym sprawdzeniem |
   ||| 

   ![Szczegóły wyzwalacza](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dodaj akcję Dynamics 365

Teraz Dodaj akcję Dynamics 365, która tworzy rekord zadania dla nowego rekordu potencjalnego klienta.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź ciąg "Dynamics 365" jako filtr. Z listy Akcje wybierz tę akcję: **Utwórz nowy rekord**

   ![Wybierz akcję](./media/connectors-create-api-crmonline/select-action.png)

1. Podaj następujące szczegóły akcji:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Tak | Wystąpienie programu Dynamics 365, w którym chcesz utworzyć rekord, który nie musi być tym samym wystąpieniem w wyzwalaczu, ale w tym przykładzie jest to "contoso" |
   | **Nazwa jednostki** | Tak | Jednostka, w której ma zostać utworzony rekord, na przykład "zadania" |
   | | |

   ![Szczegóły akcji](./media/connectors-create-api-crmonline/action-details.png)

1. Gdy w akcji pojawi się pole **temat** , kliknij wewnątrz pola **temat** , aby wyświetlić listę zawartości dynamicznej. Z tej listy wybierz wartości pól do uwzględnienia w rekordzie zadania skojarzonym z nowym rekordem potencjalnego klienta:

   | Pole | Opis |
   |-------|-------------|
   | **Nazwisko** | Nazwisko z potencjalnego klienta jako głównej osoby kontaktowej w rekordzie |
   | **Temat** | Opisowa nazwa potencjalnego klienta w rekordzie |
   | | |

   ![Szczegóły rekordu zadania](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz** dla aplikacji logiki. 

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

   ![Uruchamianie aplikacji logiki](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Teraz Utwórz rekord potencjalnego klienta w usłudze Dynamics 365, aby móc wyzwolić przepływ pracy aplikacji logiki.

## <a name="add-filter-or-query"></a>Dodaj filtr lub zapytanie

Aby określić sposób filtrowania danych w akcji Dynamics 365, wybierz pozycję **Pokaż opcje zaawansowane** w tej akcji. Następnie można dodać filtr lub kwerendę order by.
Na przykład można użyć kwerendy filtru, aby uzyskać tylko aktywne konta i zamówić te rekordy według nazwy konta. W przypadku tego zadania wykonaj następujące kroki:

1. W obszarze **zapytanie filtru**wprowadź następujące zapytanie filtru OData:`statuscode eq 1`

2. Jeśli zostanie wyświetlona lista zawartość dynamiczna, w obszarze **kolejność według**wybierz pozycję **nazwa konta**. 

   ![Określ filtr i kolejność](./media/connectors-create-api-crmonline/advanced-options.png)

Aby uzyskać więcej informacji, zobacz Opcje zapytania o systemowy interfejs API sieci Web w systemie Dynamics 365:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Najlepsze rozwiązania dotyczące opcji zaawansowanych

Gdy określisz wartość dla pola w akcji lub wyzwalaczu, typ danych wartości musi być zgodny z typem pola, niezależnie od tego, czy ręcznie wprowadzisz wartość, lub wybierz wartość z listy zawartości dynamicznej.

W tej tabeli opisano niektóre typy pól i wymagane typy danych dla ich wartości.

| Typ pola | Wymagany typ danych | Opis | 
|------------|--------------------|-------------|
| Pola tekstowe | Pojedynczy wiersz tekstu | Te pola wymagają pojedynczego wiersza tekstu lub zawartości dynamicznej, która ma typ tekstu. <p><p>*Przykładowe pola*: **Description** i **Category** | 
| Pola liczb całkowitych | Liczba całkowita | Niektóre pola wymagają liczby całkowitej lub zawartości dynamicznej, która ma typ Integer. <p><p>*Przykładowe pola*: **procent ukończenia** i **czas trwania** | 
| Pola daty | Data i godzina | Niektóre pola wymagają daty o formacie mm/dd/rrrr lub zawartości dynamicznej, która ma typ daty. <p><p>*Przykładowe pola*: **utworzone**, **Data rozpoczęcia**, **rzeczywiste rozpoczęcie**, **rzeczywiste zakończenie**i **Data ukończenia** | 
| Pola wymagające zarówno identyfikatora rekordu, jak i typu wyszukiwania | Klucz podstawowy | Niektóre pola, które odwołują się do innego rekordu jednostki, wymagają identyfikatora rekordu i typu wyszukiwania. | 
||||

Rozwijanie w tych typach pól, poniżej przedstawiono przykładowe pola w wyzwalaczach i akcje w usłudze Dynamics 365, które wymagają zarówno identyfikatora rekordu, jak i typu wyszukiwania. To wymaganie oznacza, że wartości wybrane z listy dynamicznej nie będą działały.

| Pole | Opis |
|-------|-------------|
| **Właściciel** | Musi to być prawidłowy identyfikator użytkownika lub identyfikator rekordu zespołu. |
| **Typ właściciela** | Musi być albo `systemusers` `teams`. |
| **Dotyczy** | Musi być prawidłowym IDENTYFIKATORem rekordu, np. IDENTYFIKATORem konta lub IDENTYFIKATORem rekordu osoby kontaktowej. |
| **Typ dotyczący** | Musi być typem wyszukiwania, takim jak `accounts` lub. `contacts` |
| **Klient** | Musi być prawidłowym IDENTYFIKATORem rekordu, np. IDENTYFIKATORem konta lub IDENTYFIKATORem rekordu osoby kontaktowej. |
| **Typ klienta** | Musi być typem wyszukiwania, takim jak `accounts` lub. `contacts` |
|||

W tym przykładzie Akcja o nazwie **Utwórz nowy rekord** powoduje utworzenie nowego rekordu zadania:

![Tworzenie rekordu zadania z identyfikatorami rekordów i typami wyszukiwania](./media/connectors-create-api-crmonline/create-record-advanced.png)

Ta akcja przypisuje rekord zadania do określonego identyfikatora użytkownika lub identyfikatora rekordu zespołu na podstawie identyfikatora rekordu w polu **właściciel** i typu wyszukiwania w polu **typ właściciela** :

![Identyfikator rekordu właściciela i typ wyszukiwania](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Ta akcja powoduje również dodanie rekordu konta skojarzonego z IDENTYFIKATORem rekordu dodanym w polu **dotyczy** i typu wyszukiwania w polu **dotyczy typu** :

![Dotyczy identyfikatora rekordu i typu wyszukiwania](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Znajdź identyfikator rekordu

Aby znaleźć identyfikator rekordu, wykonaj następujące kroki:

1. W programie Dynamics 365 Otwórz rekord, taki jak rekord konta.

2. Na pasku narzędzi Akcje wybierz jedną z następujących czynności:

   * Wybierz pozycję **wyskakujące okienko**. ![popout rekord](./media/connectors-create-api-crmonline/popout-record.png) 
   * Wybierz pozycję **Wyślij wiadomość e-mail** , aby można było skopiować pełny adres URL do domyślnego programu poczty e-mail.

   Identyfikator rekordu pojawia się w adresie URL między `%7b` znakami i `%7d` :

   ![Znajdź identyfikator rekordu](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Rozwiązywanie problemów z nieudanymi uruchomieniami

Aby znaleźć i przejrzeć nieudane kroki w aplikacji logiki, można wyświetlić historię uruchomienia, stan, dane wyjściowe i tak dalej.

1. W Azure Portal w menu głównym aplikacji logiki wybierz pozycję **Przegląd**. W sekcji **historia uruchomień** , która zawiera wszystkie Stany uruchamiania aplikacji logiki, wybierz przebieg zakończony niepowodzeniem, aby uzyskać więcej informacji.

   ![Stan uruchomienia aplikacji logiki](./media/connectors-create-api-crmonline/run-history.png)

1. Rozwiń krok zakończony niepowodzeniem, aby wyświetlić więcej szczegółów.

   ![Rozwiń krok zakończony niepowodzeniem](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Przejrzyj szczegóły kroku, takie jak dane wejściowe i wyjściowe, które mogą pomóc w znalezieniu przyczyny wystąpienia błędu.

   ![Nieudane instrukcje wejścia i wyjścia](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Aby uzyskać więcej informacji na temat rozwiązywania problemów z usługą Logic Apps, zobacz [Diagnozowanie błędów aplikacji logiki](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
