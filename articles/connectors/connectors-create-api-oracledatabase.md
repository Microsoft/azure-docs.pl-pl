---
title: Połącz z Oracle Database
description: Wstawianie rekordów i zarządzanie nimi za pomocą Oracle Database interfejsów API REST i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674821"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Wprowadzenie do łącznika Oracle Database

Korzystając z łącznika Oracle Database, tworzysz przepływy pracy organizacyjne, które korzystają z danych w istniejącej bazie danych. Ten łącznik może nawiązać połączenie z lokalną Oracle Database lub maszyną wirtualną platformy Azure z zainstalowaną Oracle Database. Za pomocą tego łącznika możesz:

* Kompiluj swój przepływ pracy, dodając nowego klienta do bazy danych klientów lub aktualizując zamówienie w bazie danych zamówień.
* Użyj akcji, aby uzyskać wiersz danych, Wstaw nowy wiersz, a nawet Usuń. Na przykład po utworzeniu rekordu w usłudze Dynamics CRM Online (wyzwalacz), a następnie wstawieniu wiersza w Oracle Database (Akcja). 

Ten łącznik nie obsługuje następujących elementów:

* Widoki 
* Dowolna tabela z kluczami złożonymi
* Zagnieżdżone typy obiektów w tabelach
* Funkcje bazy danych z wartościami nieskalarnymi

W tym artykule pokazano, jak używać łącznika Oracle Database w aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Obsługiwane wersje oprogramowania Oracle: 
    * Oracle 9 lub nowszy
    * Oprogramowania klienta Oracle 8.1.7 lub nowsze

* Zainstaluj lokalną bramę danych. [Łączenie z danymi lokalnymi z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md) zawiera listę kroków. Brama jest wymagana do nawiązania połączenia z lokalną Oracle Database lub z maszyną wirtualną platformy Azure z zainstalowaną Oracle DB. 

    > [!NOTE]
    > Lokalna Brama danych działa jako most i zapewnia bezpieczny transfer danych między lokalnymi danymi (danymi, które nie znajdują się w chmurze) i aplikacjami logiki. Ta sama Brama może być używana z wieloma usługami i wieloma źródłami danych. W związku z tym wystarczy zainstalować bramę tylko raz.

* Zainstaluj klienta Oracle na komputerze, na którym zainstalowano lokalną bramę danych. Pamiętaj, aby zainstalować 64-bitową Dostawca danych Oracle dla programu .NET z bazy danych Oracle:  

  [64-bitowa wersja programu ODAC 12c w wersji 4 (12.1.0.2.4) dla systemu Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Jeśli klient Oracle nie jest zainstalowany, podczas próby utworzenia lub użycia połączenia wystąpi błąd. Zobacz typowe błędy w tym artykule.


## <a name="add-the-connector"></a>Dodawanie łącznika

> [!IMPORTANT]
> Ten łącznik nie ma żadnych wyzwalaczy. Ma tylko akcje. Dlatego podczas tworzenia aplikacji logiki należy dodać kolejny wyzwalacz, aby uruchomić aplikację logiki, taką jak **harmonogram-cykl** lub **żądanie/odpowiedź-odpowiedź**. 

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki.

2. Na początku aplikacji logiki Wybierz wyzwalacz **żądanie/odpowiedź-żądanie** : 

    ![Okno dialogowe zawiera pole, aby przeszukać wszystkie wyzwalacze. Widoczny jest również pojedynczy wyzwalacz, "żądanie/odpowiedź-żądanie" z przyciskiem wyboru.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Wybierz pozycję **Zapisz**. Podczas zapisywania zostanie automatycznie wygenerowany adres URL żądania. 

4. Wybierz pozycję **Nowy krok**, a następnie pozycję **Dodaj akcję**. Wpisz `oracle` , aby wyświetlić dostępne akcje: 

    ![Pole wyszukiwania zawiera ciąg "Oracle". Wyszukiwanie tworzy jedno trafienie o nazwie "Oracle Database". Istnieje strona z kartami, jedna karta przedstawiająca "WYZWALACZe (0)", a druga — "akcje (6)". Zostanie wyświetlona sześć akcji. Pierwszy z nich to "Pobierz Podgląd wierszy".](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Jest to również najszybszy sposób wyświetlania wyzwalaczy i akcji dostępnych dla każdego łącznika. Wpisz część nazwy łącznika, na przykład `oracle` . Projektant wyświetla wszystkie wyzwalacze i wszystkie akcje. 

5. Wybierz jedną z akcji, na przykład **Oracle Database — Pobierz wiersz**. Wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**. Wprowadź nazwę serwera Oracle, metodę uwierzytelniania, nazwę użytkownika, hasło, a następnie wybierz bramę:

    ![Okno dialogowe zatytułowane "Oracle Database-Get Row". Istnieje pole o nazwie "Połącz za pośrednictwem lokalnej bramy danych". Poniżej znajdują się pięć innych pól tekstowych.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Po nawiązaniu połączenia wybierz tabelę z listy, a następnie wprowadź identyfikator wiersza w tabeli. Musisz znać identyfikator tabeli. Jeśli nie wiesz, skontaktuj się z administratorem usługi Oracle DB i Pobierz dane wyjściowe z programu `select * from yourTableName` . Zapewnia to informacje, które należy wykonać.

    W poniższym przykładzie dane zadania są zwracane z bazy danych kadr: 

    ![Okno dialogowe zatytułowane "Pobierz wiersz (wersja zapoznawcza)" ma dwa pola tekstowe: "Nazwa tabeli", która zawiera "H R JOBS" i ma listę rozwijaną oraz "wiersz i d", który zawiera "S A-REP".](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. W tym następnym kroku można użyć dowolnego z innych łączników do skompilowania przepływu pracy. Jeśli chcesz przetestować pobieranie danych z programu Oracle, Wyślij do siebie wiadomość e-mail z danymi z programu Oracle przy użyciu jednego z łączników wysyłania wiadomości e-mail, np. Office 365 Outlook. Użyj tokenów dynamicznych z tabeli Oracle, aby utworzyć `Subject` i `Body` wysłać wiadomość e-mail:

    ![Istnieją dwa okna dialogowe. Pole "Wyślij wiadomość e-mail" zawiera pola, aby określić adres e-mail "treść", "podmiot" i "do". Okno dialogowe Dodawanie zawartości dynamicznej umożliwia wyszukiwanie zawartości dynamicznej w aplikacjach i usługach przepływu.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Zapisz** aplikację logiki, a następnie wybierz pozycję **Uruchom**. Zamknij projektanta i przejrzyj historię uruchamiania dla stanu. Jeśli to się nie powiedzie, wybierz wiersz komunikatu z błędem. Zostanie otwarty projektant, który pokazuje, który krok nie powiódł się, a także informacje o błędzie. Jeśli się powiedzie, należy odebrać wiadomość e-mail z dodanymi informacjami.


### <a name="workflow-ideas"></a>Pomysły dotyczące przepływu pracy

* Chcesz monitorować #oraclego i umieścić tweety w bazie danych, aby można było wykonywać zapytania i używać ich w innych aplikacjach. W aplikacji logiki Dodaj `Twitter - When a new tweet is posted` wyzwalacz i wprowadź **#oracle** hasztagów. Następnie Dodaj `Oracle Database - Insert row` akcję i wybierz tabelę:

    ![W oknie dialogowym "po opublikowaniu nowego tweetu" zostanie wyświetlona opcja "baza danych programu" Oracle "jako tekst wyszukiwania i można określić częstotliwość sprawdzania. To okno dialogowe prowadzi do okna dialogowego "Oracle Database", które umożliwia wybranie akcji.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Komunikaty są wysyłane do kolejki Service Bus. Chcesz uzyskać te komunikaty i umieścić je w bazie danych. W aplikacji logiki Dodaj `Service Bus - when a message is received in a queue` wyzwalacz i wybierz kolejkę. Następnie Dodaj `Oracle Database - Insert row` akcję i wybierz tabelę:

    !["Po odebraniu komunikatu..." w oknie dialogowym są wyświetlane "zamówienia" jako "Nazwa kolejki" i można określić częstotliwość sprawdzania. To pole prowadzi do okna dialogowego "Wstaw wiersz (wersja zapoznawcza)", które umożliwia wybranie opcji "Nazwa tabeli".](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Typowe błędy

#### <a name="error-cannot-reach-the-gateway"></a>**Błąd**: nie można nawiązać połączenia z bramą

**Przyczyna**: lokalna Brama danych nie może nawiązać połączenia z chmurą. 

Środki **zaradcze**: Upewnij się, że brama jest uruchomiona na komputerze lokalnym, na którym został zainstalowany, i czy może nawiązać połączenie z Internetem.  Nie zaleca się instalowania bramy na komputerze, który może zostać wyłączony lub uśpiony. Możesz również ponownie uruchomić usługę lokalna Brama danych (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Błąd**: używany dostawca jest przestarzały: element "System. Data. OracleClient wymaga oprogramowania klienckiego Oracle w wersji 8.1.7 lub nowszej.". Zobacz, [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) Aby zainstalować oficjalnego dostawcę.

**Przyczyna**: zestaw SDK klienta Oracle nie jest zainstalowany na komputerze, na którym jest uruchomiona lokalna Brama danych.  

**Rozwiązanie**: Pobierz i Zainstaluj zestaw SDK klienta Oracle na tym samym komputerze, na którym znajduje się lokalna Brama danych.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Błąd**: tabela "[TableName]" nie definiuje żadnych kolumn klucza

**Przyczyna**: tabela nie ma klucza podstawowego.  

**Rozwiązanie**: Łącznik Oracle Database wymaga użycia tabeli z kolumną klucza podstawowego.
 
## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/oracle/). 

## <a name="get-some-help"></a>Uzyskaj pomoc

[Strona pytania firmy&Microsoft dla Azure Logic Apps](/answers/topics/azure-logic-apps.html) jest doskonałym miejscem, w którym można zadawać pytania, odpowiadać na nie i dowiedzieć się, co inni użytkownicy Logic Apps robią. 

Możesz pomóc ulepszyć Logic Apps i łączniki, głosując i przesyłając swoje pomysły w witrynie [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) . 


## <a name="next-steps"></a>Następne kroki
[Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)i Eksploruj dostępne łączniki w Logic Apps na [liście interfejsów API](apis-list.md).