---
title: Dostęp do zasobów IBM DB2 i zarządzanie nimi
description: Odczytuj, Edytuj, Aktualizuj i Zarządzaj zasobami programu IBM DB2 przez Tworzenie zautomatyzowanych przepływów pracy przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 6c9c54450788a89a7b1aadbb0b4682a60619c061
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334603"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Dostęp do zasobów IBM DB2 i zarządzanie nimi przy użyciu Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika programu IBM DB2](/connectors/db2/)można tworzyć automatyczne zadania i przepływy pracy oparte na zasobach przechowywanych w bazie danych programu DB2. Przepływy pracy mogą łączyć się z zasobami w bazie danych, czytać i wyświetlać tabele baz danych, dodawać wiersze, zmieniać wiersze, usuwać wiersze i nie tylko. Możesz uwzględnić akcje w aplikacjach logiki, które uzyskują odpowiedzi z bazy danych, i udostępnienia danych wyjściowych dla innych akcji.

W tym artykule pokazano, jak utworzyć aplikację logiki, która wykonuje różne operacje bazy danych. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Obsługiwane platformy i wersje

Łącznik DB2 zawiera klienta firmy Microsoft, który komunikuje się ze zdalnymi serwerami DB2 w sieci TCP/IP. Tego łącznika można używać do uzyskiwania dostępu do baz danych w chmurze, takich jak IBM DB2 dla systemu Windows działających w ramach wirtualizacji platformy Azure. Możesz również uzyskać dostęp do lokalnych baz danych DB2 po [zainstalowaniu i skonfigurowaniu lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md).

Łącznik IBM DB2 obsługuje te platformy i wersje programu IBM DB2 wraz z produktami zgodnymi z programem IBM DB2, które obsługują architekturę DRDA (Distributed relacyjnej bazy danych) (SQLAM) w wersji 10 i 11:

| Platforma | Wersja | 
|----------|---------|
| IBM DB2 dla systemu z/OS | 11,1, 10,1 |
| IBM DB2 for i | 7,3, 7,2, 7,1 |
| IBM DB2 for LUW | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Obsługiwane operacje bazy danych

Łącznik IBM DB2 obsługuje te operacje bazy danych, które mapują do odpowiednich akcji w łączniku:

| Operacja bazy danych | Akcja łącznika |
|--------------------|------------------|
| Wyświetl listę tabel bazy danych | Pobierz tabele |
| Odczytaj jeden wiersz przy użyciu polecenia SELECT | Pobierz wiersz |
| Czytaj wszystkie wiersze przy użyciu polecenia SELECT | Pobierz wiersze |
| Dodaj jeden wiersz przy użyciu instrukcji INSERT | Wstaw wiersz |
| Edytuj jeden wiersz przy użyciu aktualizacji | Aktualizuj wiersz |
| Usuń jeden wiersz przy użyciu polecenia DELETE | Usuń wiersz |
|||

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Baza danych IBM DB2, oparta na chmurze lub lokalna

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do bazy danych DB2. Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, wybierz oddzielny wyzwalacz, na przykład wyzwalacz **cykliczny** .
W przykładach w tym artykule użyto wyzwalacza **cyklu** .

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Dodaj akcję bazy danych DB2 — Pobierz tabele

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie została jeszcze otwarta.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź ciąg "DB2" jako filtr. Na potrzeby tego przykładu na liście Akcje wybierz tę akcję: **Pobierz tabele (wersja zapoznawcza)**

   ![Wybierz akcję](./media/connectors-create-api-db2/select-db2-action.png)

   Teraz zostanie wyświetlony monit o podanie szczegółowych informacji o połączeniu z bazą danych programu DB2.

1. Postępuj zgodnie z instrukcjami dotyczącymi tworzenia połączeń dla [baz danych w chmurze](#cloud-connection) lub [lokalnych baz danych](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Nawiązywanie połączenia z usługą Cloud DB2

Aby skonfigurować połączenie, podaj te szczegóły połączenia po wyświetleniu monitu, wybierz pozycję **Utwórz**, a następnie Zapisz aplikację logiki:

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Łączenie za pośrednictwem bramy lokalnej** | Nie | Dotyczy tylko połączeń lokalnych. |
| **Nazwa połączenia** | Tak | Nazwa połączenia, na przykład "MyLogicApp-DB2-Connection" |
| **Server** (Serwer) | Tak | Numer portu lub dwukropek aliasu dla serwera bazy danych DB2, na przykład "myDB2server.cloudapp.net:50000" <p><p>**Uwaga**: Ta wartość to ciąg, który reprezentuje adres TCP/IP lub alias w formacie IPv4 lub IPv6, po którym następuje dwukropek i numer portu TCP/IP. |
| **Database** (Baza danych) | Tak | Nazwa bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, który reprezentuje nazwę DRDA relacyjnej bazy danych (RDBNAM): <p>-DB2 dla systemu z/OS akceptuje 16-bajtowy ciąg, w którym baza danych jest znana jako lokalizacja "IBM DB2 dla systemu z/OS". <br>-DB2 dla Akceptuję ciąg 18-bajtowy, w którym baza danych jest znana jako relacyjna baza danych "IBM DB2 for i". <br>-DB2 for LUW akceptuje ciąg 8-bajtowy. |
| **Nazwa użytkownika** | Tak | Nazwa użytkownika bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, którego długość jest określana na podstawie określonej bazy danych: <p><p>-DB2 dla systemu z/OS akceptuje ciąg 8-bajtowy. <br>-DB2 dla i akceptuje 10-bajtowy ciąg. <br>-DB2 dla systemu Linux lub UNIX akceptuje 8-bajtowy ciąg. <br>-DB2 dla systemu Windows akceptuje 30-bajtowy ciąg. |
| **Hasło** | Tak | Hasło do bazy danych |
||||

Na przykład:

![Szczegóły połączenia dla baz danych opartych na chmurze](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Nawiązywanie połączenia z lokalnym programem DB2

Przed utworzeniem połączenia należy zainstalować lokalną bramę danych. W przeciwnym razie nie można zakończyć konfigurowania połączenia. Jeśli masz instalację bramy, Kontynuuj, podając te szczegóły połączenia, a następnie wybierz pozycję **Utwórz**.

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Łączenie za pośrednictwem bramy lokalnej** | Tak | Ma zastosowanie, gdy chcesz połączyć lokalne i wyświetlić właściwości połączenia lokalnego. |
| **Nazwa połączenia** | Tak | Nazwa połączenia, na przykład "MyLogicApp-DB2-Connection" | 
| **Server** (Serwer) | Tak | Numer portu lub dwukropek aliasu dla serwera bazy danych DB2, na przykład "myDB2server: 50000" <p><p>**Uwaga**: Ta wartość to ciąg, który reprezentuje adres TCP/IP lub alias w formacie IPv4 lub IPv6, po którym następuje dwukropek i numer portu TCP/IP. |
| **Database** (Baza danych) | Tak | Nazwa bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, który reprezentuje nazwę DRDA relacyjnej bazy danych (RDBNAM): <p>-DB2 dla systemu z/OS akceptuje 16-bajtowy ciąg, w którym baza danych jest znana jako lokalizacja "IBM DB2 dla systemu z/OS". <br>-DB2 dla Akceptuję ciąg 18-bajtowy, w którym baza danych jest znana jako relacyjna baza danych "IBM DB2 for i". <br>-DB2 for LUW akceptuje ciąg 8-bajtowy. |
| **Authentication** | Tak | Typ uwierzytelniania dla połączenia, na przykład "podstawowa" <p><p>**Uwaga**: Wybierz tę wartość z listy, która zawiera podstawowe lub Windows (Kerberos). |
| **Nazwa użytkownika** | Tak | Nazwa użytkownika bazy danych <p><p>**Uwaga**: Ta wartość jest ciągiem, którego długość jest określana na podstawie określonej bazy danych: <p><p>-DB2 dla systemu z/OS akceptuje ciąg 8-bajtowy. <br>-DB2 dla i akceptuje 10-bajtowy ciąg. <br>-DB2 dla systemu Linux lub UNIX akceptuje 8-bajtowy ciąg. <br>-DB2 dla systemu Windows akceptuje 30-bajtowy ciąg. |
| **Hasło** | Tak | Hasło do bazy danych |
| **Punkt** | Tak | Nazwa zainstalowanej lokalnej bramy danych <p><p>**Uwaga**: Wybierz tę wartość z listy, która obejmuje wszystkie zainstalowane bramy danych w ramach subskrypcji i grupy zasobów platformy Azure. |
||||

Na przykład:

![Szczegóły połączenia dla lokalnych baz danych](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Wyświetlanie tabel wyjściowych

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z przebiegu.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**, w sekcji **historia uruchomień** wybierz ostatni przebieg, który jest pierwszym elementem na liście.

   ![Widok historii uruchamiania](./media/connectors-create-api-db2/run-history.png)

1. W obszarze **uruchomienie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Pobierz tabele** .

   ![Rozwiń akcję](./media/connectors-create-api-db2/expand-action-step.png)

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują listę tabel.

   ![Wyświetlanie tabel wyjściowych](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Pobierz wiersz

Aby pobrać jeden rekord z tabeli bazy danych DB2, użyj akcji **Pobierz wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2 `SELECT WHERE` , na przykład `SELECT FROM AREA WHERE AREAID = '99999'` .

1. Jeśli w aplikacji logiki nigdy nie były używane akcje programu DB2, przejrzyj kroki w sekcji [Dodawanie akcji programu DB2 — Pobierz tabele](#add-db2-action) , ale zamiast tego Dodaj akcję **Pobierz wiersz** , a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Pobierz wiersz** poniżej przedstawiono sposób wyświetlania przykładowej aplikacji logiki:

   ![Akcja pobierania wiersza](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Określ wartości wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela zawierająca żądany rekord, taka jak "obszar" w tym przykładzie |
   | **Identyfikator obszaru** | Tak | Identyfikator żądanego rekordu, na przykład "99999" w tym przykładzie |
   ||||

   ![Zrzut ekranu pokazujący akcję "Pobierz wiersz (wersja zapoznawcza)" z otwartą listą "Nazwa tabeli" i wybraną wartością "obszar".](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-output-row"></a>Wyświetl wiersz danych wyjściowych

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z przebiegu.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**, w sekcji **historia uruchomień** wybierz ostatni przebieg, który jest pierwszym elementem na liście.

1. W obszarze **uruchomienie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Pobierz wiersz** .

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują określony wiersz.

   ![Wyświetl wiersz danych wyjściowych](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Pobierz wiersze

Aby pobrać wszystkie rekordy z tabeli bazy danych DB2, użyj akcji **Pobierz wiersze** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2 `SELECT` , na przykład `SELECT * FROM AREA` .

1. Jeśli w aplikacji logiki nigdy nie były używane akcje programu DB2, przejrzyj kroki w sekcji [Dodawanie akcji programu DB2 — Pobierz tabele](#add-db2-action) , ale zamiast tego Dodaj akcję **Pobierz wiersze** , a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Pobierz wiersze** poniżej przedstawiono sposób wyświetlania przykładowej aplikacji logiki:

   ![Akcja pobierania wierszy](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Otwórz listę **Nazwa tabeli** , a następnie wybierz żądaną tabelę, czyli "obszar" w tym przykładzie:

   ![Zrzut ekranu pokazujący akcję "Pobierz wiersz (wersja zapoznawcza)" z wartością "obszar" wybraną na liście "Nazwa tabeli".](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Aby określić filtr lub zapytanie dla wyników, wybierz pozycję **Pokaż opcje zaawansowane**.

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-output-rows"></a>Wyświetl wiersze wyjściowe

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z przebiegu.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**, w sekcji **historia uruchomień** wybierz ostatni przebieg, który jest pierwszym elementem na liście.

1. W obszarze **uruchomienie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Pobierz wiersze** .

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują wszystkie rekordy w określonej tabeli.

   ![Wyświetl wiersze wyjściowe](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Wstaw wiersz

Aby dodać pojedynczy rekord do tabeli bazy danych DB2, użyj akcji **Wstaw wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2 `INSERT` , na przykład `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` .

1. Jeśli w aplikacji logiki nigdy nie były używane akcje programu DB2, przejrzyj kroki w sekcji [Dodawanie akcji programu DB2 — Pobierz tabele](#add-db2-action) , ale zamiast tego Dodaj akcję **Wstaw wiersz** , a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Wstaw wiersz** poniżej przedstawiono sposób wyświetlania przykładowej aplikacji logiki:

   ![Akcja wstawiania wiersza](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Określ wartości wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   Na potrzeby tego przykładu są następujące właściwości:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela, w której ma zostać dodany rekord, taki jak "obszar" |
   | **Identyfikator obszaru** | Tak | Identyfikator obszaru do dodania, na przykład "99999" |
   | **Opis obszaru** | Tak | Opis obszaru do dodania, na przykład "obszar 99999" |
   | **Identyfikator regionu** | Tak | Identyfikator regionu do dodania, na przykład "102" |
   |||| 

   Na przykład:

   ![Zrzut ekranu, który pokazuje projektanta Logic Apps z akcją "Wstaw wiersz (wersja zapoznawcza)" i przykładowymi wartościami właściwości.](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-insert-row-outputs"></a>Wyświetlanie danych wyjściowych wstawiania wierszy

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z przebiegu.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**, w sekcji **historia uruchomień** wybierz ostatni przebieg, który jest pierwszym elementem na liście.

1. W obszarze **uruchomienie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Wstaw wiersz** .

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują rekord dodany do określonej tabeli.

   ![Wyświetl dane wyjściowe z wstawionym wierszem](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aktualizuj wiersz

Aby zaktualizować pojedynczy rekord w tabeli bazy danych DB2, użyj akcji **Aktualizuj wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2 `UPDATE` , na przykład `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)` .

1. Jeśli w aplikacji logiki nigdy nie były używane akcje programu DB2, przejrzyj kroki w sekcji [Dodawanie akcji programu DB2 — Pobierz tabele](#add-db2-action) , ale zamiast tego Dodaj akcję **Aktualizuj wiersz** , a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Aktualizuj wiersz** poniżej przedstawiono sposób wyświetlania przykładowej aplikacji logiki:

   ![Akcja aktualizacji wiersza](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Określ wartości wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   Na potrzeby tego przykładu są następujące właściwości:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela, w której ma zostać zaktualizowany rekord, na przykład "obszar" |
   | **Identyfikator wiersza** | Tak | Identyfikator rekordu do zaktualizowania, na przykład "99999" |
   | **Identyfikator obszaru** | Tak | Nowy identyfikator obszaru, taki jak "99999" |
   | **Opis obszaru** | Tak | Nowy opis obszaru, taki jak "zaktualizowany 99999" |
   | **Identyfikator regionu** | Tak | Nowy identyfikator regionu, taki jak "102" |
   ||||

   Na przykład:

   ![Zrzut ekranu, który pokazuje projektanta Logic Apps z akcją "Aktualizuj wiersz (wersja zapoznawcza)", w której wybiera się tabelę.](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-update-row-outputs"></a>Wyświetl dane wyjściowe aktualizacji wiersza

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z przebiegu.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**, w sekcji **historia uruchomień** wybierz ostatni przebieg, który jest pierwszym elementem na liście.

1. W obszarze **uruchomienie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Aktualizuj wiersz** .

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe obejmują rekord, który został zaktualizowany w określonej tabeli.

   ![Wyświetl dane wyjściowe ze zaktualizowanym wierszem](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Usuń wiersz

Aby usunąć pojedynczy rekord z tabeli bazy danych DB2, użyj akcji **Usuń wiersz** w aplikacji logiki. Ta akcja uruchamia instrukcję DB2 `DELETE` , na przykład `DELETE FROM AREA WHERE AREAID = '99999'` .

1. Jeśli w aplikacji logiki nigdy nie były używane akcje programu DB2, przejrzyj kroki w sekcji [Dodawanie akcji programu DB2 — Pobierz tabele](#add-db2-action) , ale zamiast tego Dodaj akcję **Usuń wiersz** , a następnie wróć tutaj, aby kontynuować.

   Po dodaniu akcji **Usuń wiersz** poniżej przedstawiono sposób wyświetlania przykładowej aplikacji logiki:

   ![Akcja usuwania wiersza](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Określ wartości wszystkich wymaganych właściwości (*). Po wybraniu tabeli akcja pokazuje odpowiednie właściwości, które są specyficzne dla rekordów w tej tabeli.

   Na potrzeby tego przykładu są następujące właściwości:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa tabeli** | Tak | Tabela, w której ma zostać usunięty rekord, na przykład "obszar" |
   | **Identyfikator wiersza** | Tak | Identyfikator rekordu do usunięcia, na przykład "99999" |
   ||||

   Na przykład:

   ![Zrzut ekranu przedstawiający projektanta Logic Apps z akcją "Usuń wiersz (wersja zapoznawcza)", w której jest wybierana tabela do usunięcia.](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="view-delete-row-outputs"></a>Wyświetlanie danych wyjściowych usuwania wierszy

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Po zakończeniu działania aplikacji logiki można wyświetlić dane wyjściowe z przebiegu.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W obszarze **Podsumowanie**, w sekcji **historia uruchomień** wybierz ostatni przebieg, który jest pierwszym elementem na liście.

1. W obszarze **uruchomienie aplikacji logiki**można teraz przeglądać stan, dane wejściowe i wyjściowe dla każdego kroku w aplikacji logiki.
Rozwiń akcję **Usuń wiersz** .

1. Aby wyświetlić dane wejściowe, wybierz pozycję **Pokaż nieprzetworzone dane wejściowe**.

1. Aby wyświetlić dane wyjściowe, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   Dane wyjściowe nie zawierają już rekordu usuniętego z określonej tabeli.

   ![Wyświetl dane wyjściowe bez usuniętego wiersza](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/db2/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

