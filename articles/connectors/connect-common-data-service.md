---
title: Nawiązywanie połączenia z Common Data Service (Microsoft datavers)
description: Tworzenie rekordów Common Data Service (Microsoft datavers) i zarządzanie nimi za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 02/11/2021
tags: connectors
ms.openlocfilehash: bec3416195358121b85eb61679ab39647e664a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382359"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Tworzenie rekordów i zarządzanie nimi w Common Data Service (Microsoft datavers) za pomocą Azure Logic Apps

> [!NOTE]
> W listopadzie 2020 zmieniono nazwę Common Data Service na Microsoft datavers.

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika Common Data Service](/connectors/commondataservice/)można tworzyć zautomatyzowane przepływy pracy, które zarządzają rekordami w [Common Data Service, teraz baza danych Microsoft datavers](/powerapps/maker/common-data-service/data-platform-intro) . Te przepływy pracy umożliwiają tworzenie rekordów, aktualizowanie rekordów i wykonywanie innych operacji. Możesz również uzyskać informacje z bazy danych dataverse i udostępnić dane wyjściowe dla innych akcji do użycia w aplikacji logiki. Na przykład, gdy rekord zostanie zaktualizowany w bazie danych dataversa, możesz wysłać wiadomość e-mail przy użyciu łącznika Office 365 Outlook Connector.

W tym artykule pokazano, jak utworzyć aplikację logiki, która tworzy rekord zadania za każdym razem, gdy zostanie utworzony nowy rekord potencjalnego klienta.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Środowisko Common Data Service](/power-platform/admin/environments-overview), czyli miejsce, w którym organizacja przechowuje i udostępnia dane biznesowe oraz Common Data Service bazę danych. Aby uzyskać więcej informacji, zobacz następujące zasoby:<p>

  * [Dowiedz się: Rozpoczynanie pracy z usługą Common Data Service](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Platforma energetyczna — Omówienie środowisk](/power-platform/admin/environments-overview)

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) i aplikacji logiki z lokalizacji, w której chcesz uzyskać dostęp do rekordów w bazie danych dataverse. Aby uruchomić aplikację logiki z wyzwalaczem Common Data Service, musisz mieć pustą aplikację logiki. Jeśli dopiero zaczynasz Azure Logic Apps, zobacz [Szybki Start: Tworzenie pierwszego przepływu pracy przy użyciu Azure Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Dodaj wyzwalacz Common Data Service

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Na potrzeby tego przykładu Dodaj wyzwalacz Common Data Service, który jest uruchamiany, gdy zostanie utworzony nowy rekord.

1. W [Azure Portal](https://portal.azure.com)Otwórz pustą aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W polu wyszukiwania wpisz `common data service`. Na potrzeby tego przykładu na liście Wyzwalacze wybierz ten wyzwalacz: **po utworzeniu rekordu**

   ![Wybierz wyzwalacz "po utworzeniu rekordu"](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Jeśli zostanie wyświetlony monit, zaloguj się do Common Data Service.

1. W wyzwalaczu podaj informacje o środowisku, w którym mają być monitorowane nowe rekordy "potencjalni klienci", na przykład:

   ![Wyzwalaj informacje dla środowiska do monitorowania](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Środowisko** | Tak | Środowisko do monitorowania, na przykład "produkcja Fabrikam Sales". Aby uzyskać więcej informacji, zobacz [platformę PowerShell — Omówienie](/power-platform/admin/environments-overview). |
   | **Nazwa jednostki** | Tak | Jednostka do monitorowania, na przykład "potencjalni klienci" |
   | **Zakres** | Tak | Źródło, które utworzyło nowy rekord, na przykład użytkownik w jednostce biznesowej lub dowolny użytkownik w organizacji. W tym przykładzie zastosowano "jednostkę biznesową". |
   ||||

## <a name="add-common-data-service-action"></a>Dodaj akcję Common Data Service

Teraz Dodaj akcję Common Data Service, która tworzy rekord zadania dla nowego rekordu "potencjalni klienci".

1. W obszarze wyzwalacz **po utworzeniu rekordu** wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wpisz `common data service`. Z listy Akcje wybierz tę akcję: **Utwórz nowy rekord**

   ![Wybierz akcję "Utwórz nowy rekord"](./media/connect-common-data-service/select-create-new-record-action.png)

1. W akcji podaj informacje o środowisku, w którym chcesz utworzyć nowy rekord zadania. Jeśli są dostępne, inne właściwości są również wyświetlane na podstawie jednostki wybranej dla tej akcji, na przykład:

   ![Informacje o akcji dla środowiska, w którym ma zostać utworzony rekord](./media/connect-common-data-service/create-new-record-action-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa organizacji** | Tak | Środowisko, w którym chcesz utworzyć rekord, który nie musi być tym samym środowiskiem w wyzwalaczu, ale jest "produkcja Fabrikam Sales" w tym przykładzie |
   | **Nazwa jednostki** | Tak | Jednostka, w której ma zostać utworzony rekord, na przykład "zadania" |
   | **Temat** | Tak, na podstawie jednostki zaznaczonej w tym przykładzie | Krótki opis celu tego zadania |
   ||||

   1. Dla właściwości **podmiot** wprowadź ten tekst z końcowym miejscem:

      `Follow up with new lead:`

   1. Zachowaj wskaźnik wewnątrz pola **tematu** , aby lista zawartości dynamicznej pozostawała widoczna.
   
   1. Z listy, w sekcji **gdy rekord zostanie utworzony** , wybierz wyjściowe wyzwalacze, które chcesz dołączyć do rekordu zadania, na przykład:

      ![Wybierz wyzwalacze wyjściowe do użycia w rekordzie zadania](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Wyzwalanie danych wyjściowych | Opis |
      |----------------|-------------|
      | **Imię** | Imię i nazwisko z rekordu potencjalnego klienta, które ma być używane jako kontakt podstawowy w rekordzie zadania |
      | **Nazwisko** | Nazwisko z rekordu potencjalnego klienta, które ma być używane jako kontakt podstawowy w rekordzie zadania |
      | **Opis** | Inne dane wyjściowe do uwzględnienia w rekordzie zadania, takie jak adres e-mail i numer telefonu służbowego |
      |||

   Gdy wszystko będzie gotowe, akcja może wyglądać podobnie do tego przykładu:

   ![Zakończono akcję "Utwórz nowy rekord"](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1. Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Aby przetestować aplikację logiki, Utwórz nowy rekord "potencjalni klienci".

## <a name="trigger-only-on-updated-attributes"></a>Wyzwalaj tylko dla zaktualizowanych atrybutów

W przypadku wyzwalaczy, które są uruchamiane podczas aktualizowania rekordów, takich jak Akcja **gdy rekord jest aktualizowany** , można użyć atrybutów filtru, aby aplikacja logiki działała tylko wtedy, gdy określone atrybuty zostaną zaktualizowane. Ta funkcja pomaga uniknąć niepotrzebnych uruchomień aplikacji logiki.

1. W wyzwalaczu na liście **Dodaj nowy parametr** wybierz pozycję **filtry atrybutów**.

   ![Zrzut ekranu pokazujący akcję "po zaktualizowaniu rekordu" i otwartej listy "Dodaj nowy parametr" z wybraną właściwością "filtry atrybutów".](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Dla każdego **elementu filtry atrybutów** Wybierz atrybut, który ma być monitorowany dla aktualizacji, na przykład:

   ![Dodaj właściwość "filtry atrybutów"](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Wyświetlanie listy rekordów na podstawie filtru

W przypadku akcji, które zwracają rekordy, takie jak Akcja **listy rekordów** , można użyć zapytania OData, które zwraca rekordy na podstawie określonego filtru. Na przykład akcja zawiera listę tylko rekordów dla kont aktywnych.

1. W akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz właściwość **Filter Query** .

   ![Dodaj właściwość "Filter Query"](./media/connect-common-data-service/list-records-action-filter-query.png)

1. We właściwości **Filtruj zapytanie** , która jest teraz wyświetlana w akcji, wprowadź zapytanie filtru OData: `statuscode eq 1`

   ![Wprowadź zapytanie filtru ODATA dla rekordów filtrowania](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Aby uzyskać więcej informacji na temat `$filter` opcji zapytania systemowego, zobacz [Common Data Service-Filter Results](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Wyświetlanie listy rekordów na podstawie zamówienia

W przypadku akcji, które zwracają rekordy, takie jak działanie **list Records** , można użyć zapytania OData zwracającego rekordy w określonej kolejności, które różni się w zależności od rekordów zwracanych przez tę akcję. Na przykład akcja może zawierać listę rekordów na podstawie nazwy konta.

1. W akcji Otwórz listę **Dodaj nowy parametr** , a następnie wybierz właściwość **order by** .

   ![Dodaj właściwość "Order by"](./media/connect-common-data-service/list-records-action-order-by.png)

1. W właściwości **order by** , która jest teraz wyświetlana w akcji, wprowadź zapytanie filtru OData: `name`

   ![Wprowadzanie zapytania filtru ODATA w celu uporządkowania rekordów](./media/connect-common-data-service/list-records-action-order-by-value.png)

Aby uzyskać więcej informacji na temat `$orderby` opcji zapytania systemowego, zobacz [wyniki Common Data Service kolejności](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Typy danych pola

Niezależnie od tego, czy ręcznie wprowadzisz wartość, czy wybierzesz wartość z listy zawartości dynamicznej dla pola w wyzwalaczu lub akcji, typ danych wartości musi być zgodny z wymaganym typem danych pola.

W tej tabeli opisano niektóre typy pól i typy danych, których te pola wymagają dla ich wartości.

| Pole | Typ danych | Opis |
|-------|-----------|-------------|
| Pole tekstowe | Pojedynczy wiersz tekstu | Wymaga pojedynczego wiersza tekstu lub zawartości dynamicznej, która ma typ danych tekstowych, na przykład następujące właściwości: <p><p>- **Zharmonizowan** <br>- **Kategorii** |
| Pole liczb całkowitych | Liczba całkowita | Wymaga wartości całkowitej lub dynamicznej, która ma typ danych Integer, na przykład następujące właściwości: <p><p>- **Procent ukończenia** <br>- **Trwania** |
| Pole daty | Data i godzina | Wymaga daty w formacie MM/DD/YYY lub zawartości dynamicznej, która ma typ danych Data, na przykład następujące właściwości: <p><p>- **Data utworzenia** <br>- **Data rozpoczęcia** <br>- **Rzeczywiste rozpoczęcie** <br>- **Rzeczywiste zakończenie** <br>- **Data ukończenia** |
| Pole, które odwołuje się do innego rekordu jednostki | Klucz podstawowy | Wymaga zarówno identyfikatora rekordu, takiego jak identyfikator GUID, jak i typu wyszukiwania, co oznacza, że wartości z listy zawartości dynamicznej nie będą działały, na przykład następujące właściwości: <p><p>- **Właściciel**: musi być PRAWIDŁOWYm identyfikatorem użytkownika lub identyfikatorem rekordu zespołu. <br>- **Typ właściciela**: musi być typem wyszukiwania, takim jak `systemusers` lub `teams` , odpowiednio. <p><p>- **Dotyczy**: musi być PRAWIDŁOWYm identyfikatorem rekordu, np. identyfikatorem konta lub identyfikatorem rekordu osoby kontaktowej. <br>- **Typ dotyczący**: musi być typem wyszukiwania, na `accounts` przykład `contacts` , lub, odpowiednio. <p><p>- **Klient**: musi być PRAWIDŁOWYm identyfikatorem rekordu, np. identyfikatorem konta lub identyfikatorem rekordu osoby kontaktowej. <br>- **Typ klienta**: musi być typem wyszukiwania, takim jak `accounts` lub `contacts` , odpowiednio. |
||||

Ten przykład pokazuje, jak Akcja **Utwórz nowy rekord** powoduje utworzenie nowego rekordu "Tasks" skojarzonego z innymi rekordami jednostki, w tym rekordu użytkownika i rekordu konta. Akcja określa identyfikatory i typy wyszukiwania dla tych rekordów jednostek przy użyciu wartości, które pasują do oczekiwanych typów danych dla odpowiednich właściwości.

* Na podstawie właściwości **Owner** , która określa identyfikator użytkownika i Właściwość **typu właściciela** , która określa `systemusers` Typ wyszukiwania, Akcja kojarzy nowy rekord "Tasks" z określonym użytkownikiem.

* Na podstawie właściwości " **dotyczy** ", która określa identyfikator rekordu i Właściwość **dotyczy typu** , która określa `accounts` Typ wyszukiwania, Akcja kojarzy nowy rekord "Tasks" z określonym kontem.

![Tworzenie rekordu "Tasks" skojarzonego z identyfikatorami i typami wyszukiwania](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Informacje techniczne na podstawie opisu struktury Swagger łącznika, takie jak wyzwalacze, akcje, limity i inne szczegóły, znajdują się na [stronie odniesienia łącznika](/connectors/commondataservice/).

## <a name="troubleshooting-problems"></a>Rozwiązywanie problemów

### <a name="calls-from-multiple-environments"></a>Wywołania z wielu środowisk

Oba łączniki, Common Data Service i Common Data Service (bieżące środowisko), przechowują informacje o przepływach pracy aplikacji logiki, które są potrzebne, i otrzymują powiadomienia o zmianach jednostek przy użyciu `callbackregistrations` jednostki w DataMicrosoft datavers. Jeśli skopiujesz organizację datavers, wszystkie elementy webhook zostaną skopiowane. Jeśli skopiujesz organizację przed wyłączeniem przepływów pracy, które są mapowane do organizacji, wszystkie skopiowane elementy webhook będą również wskazywały te same aplikacje logiki, a następnie otrzymywać powiadomienia z wielu organizacji.

Aby zatrzymać niechciane powiadomienia, usuń rejestrację wywołania zwrotnego z organizacji, która wysyła te powiadomienia, wykonując następujące czynności:

1. Określ organizację dataverse, z której chcesz usunąć powiadomienia, i zaloguj się do tej organizacji.

1. W przeglądarce Chrome Znajdź rejestrację wywołania zwrotnego, która ma zostać usunięta, wykonując następujące czynności:

   1. Przejrzyj listę ogólna dla wszystkich rejestracji wywołania zwrotnego na poniższym identyfikatorze URI OData, aby można było wyświetlić dane wewnątrz `callbackregistrations` jednostki:

      `https://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations`:

      > [!NOTE]
      > Jeśli nie zostaną zwrócone żadne wartości, możesz nie mieć uprawnień do wyświetlania tego typu jednostki lub być może nie zarejestrowano się w odpowiedniej organizacji.

   1. Odfiltruj nazwę logiczną jednostki wyzwalającej `entityname` i zdarzenie powiadomienia pasujące do przepływu pracy aplikacji logiki (komunikatu). Każdy typ zdarzenia jest mapowany na liczbę całkowitą wiadomości w następujący sposób:

      | Typ zdarzenia | Liczba całkowita komunikatu |
      |------------|-----------------|
      | Utwórz | 1 |
      | Usuń | 2 |
      | Aktualizacja | 3 |
      | Metodę createorupdate | 4 |
      | CreateOrDelete | 5 |
      | UpdateOrDelete | 6 |
      | CreateOrUpdateOrDelete | 7 |
      |||

      Ten przykład pokazuje, jak można odfiltrować `Create` powiadomienia w jednostce o nazwie przy `nov_validation` użyciu następującego identyfikatora URI OData dla przykładowej organizacji:

      `https://fabrikam-preprod.crm1.dynamics.com/api/data/v9.0/callbackregistrations?$filter=entityname eq 'nov_validation' and message eq 1`

      ![Zrzut ekranu przedstawiający okno przeglądarki i identyfikator URI protokołu OData na pasku adresu.](./media/connect-common-data-service/find-callback-registrations.png)

      > [!TIP]
      > Jeśli istnieje wiele wyzwalaczy dla tej samej jednostki lub zdarzenia, można filtrować listę przy użyciu dodatkowych filtrów, takich jak `createdon` `_owninguser_value` atrybuty i. Nazwa użytkownika będącego właścicielem jest wyświetlana w sekcji `/api/data/v9.0/systemusers({id})` .

   1. Po znalezieniu identyfikatora dla rejestracji wywołania zwrotnego, która ma zostać usunięta, wykonaj następujące czynności:
   
      1. W przeglądarce Chrome Otwórz Narzędzia deweloperskie programu Chrome (klawiatura: F12).

      1. W oknie w górnej części Wybierz kartę **konsola** .

      1. W wierszu polecenia wprowadź następujące polecenie, które wysyła żądanie usunięcia określonej rejestracji wywołania zwrotnego:

         `fetch('http://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations({ID-to-delete})', { method: 'DELETE'})`

         > [!IMPORTANT]
         > Upewnij się, że żądanie zostało wysłane z nieujednoliconego interfejsu klienta (UCI), na przykład ze strony usługi OData lub interfejsu API odpowiedzi. W przeciwnym razie logika w pliku app.js może kolidować z tą operacją.

   1. Aby upewnić się, że rejestracja wywołania zwrotnego już nie istnieje, sprawdź listę rejestracji wywołania zwrotnego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników dla Azure Logic Apps](../connectors/apis-list.md)
