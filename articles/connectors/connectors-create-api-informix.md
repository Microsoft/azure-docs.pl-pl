---
title: Łączenie z bazą danych programu IBM Informix
description: Automatyzowanie zadań i przepływów pracy służących do zarządzania zasobami przechowywanymi w programie IBM Informix przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761648"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Zarządzanie zasobami baz danych programu IBM Informix przy użyciu Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika programu Informix](/connectors/informix/)można tworzyć zautomatyzowane zadania i przepływy pracy, które zarządzają zasobami w bazie danych programu IBM Informix. Ten łącznik zawiera klienta firmy Microsoft, który komunikuje się ze zdalnym serwerem programu Informix w sieci TCP/IP, w tym z bazami danych opartymi na chmurze, takimi jak IBM Informix dla systemu Windows działającą w ramach wirtualizacji platformy Azure i lokalnych baz danych w przypadku korzystania z [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Można nawiązać połączenie z tymi platformami i wersjami programu Informix, jeśli są one skonfigurowane do obsługi połączeń klienckich opartych na architekturze DRDA (Distributed relacyjna baza danych)

* IBM Informix 12,1
* IBM Informix 11,7

W tym temacie pokazano, jak za pomocą łącznika w aplikacji logiki przetwarzać operacje bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* W przypadku lokalnych baz danych [Pobierz i zainstaluj lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym, a następnie [Utwórz zasób bramy danych platformy Azure w Azure Portal](../logic-apps/logic-apps-gateway-connection.md).

* Aplikacja logiki, do której potrzebny jest dostęp do bazy danych programu Informix. Ten łącznik zawiera tylko akcje, więc aplikacja logiki musi już rozpoczynać się od wyzwalacza, na przykład [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Dodaj akcję programu Informix

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie została jeszcze otwarta.

1. W kroku, w którym chcesz dodać akcję Informix, wybierz pozycję **nowy krok**.

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź `informix` jako filtr. Z listy Akcje wybierz żądaną akcję, na przykład:

   ![Wybierz akcję programu Informix do uruchomienia](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Łącznik zawiera te akcje, które uruchamiają odpowiednie operacje bazy danych:

   * Pobieranie tabel z bazami danych list tabel przy użyciu `CALL` instrukcji
   * Pobieranie wierszy — odczytywanie wszystkich wierszy przy użyciu `SELECT *` instrukcji
   * Pobierz wiersz — odczytaj wiersz przy użyciu `SELECT WHERE` instrukcji
   * Dodawanie wiersza przy użyciu `INSERT` instrukcji
   * Edytowanie wiersza przy użyciu `UPDATE` instrukcji
   * Usuwanie wiersza przy użyciu `DELETE` instrukcji

1. Jeśli zostanie wyświetlony monit o podanie szczegółowych informacji o połączeniu z bazą danych programu Informix, postępuj zgodnie z instrukcjami, [Aby utworzyć połączenie](#create-connection), a następnie przejdź do następnego kroku.

1. Podaj informacje dotyczące wybranej akcji:

   | Akcja | Opis | Właściwości i opisy |
   |--------|-------------|-----------------------------|
   | **Pobierz tabele** | Wyświetl listę tabel bazy danych, uruchamiając instrukcję programu Informix CALL. | Brak |
   | **Pobierz wiersze** | Pobierz wszystkie wiersze w określonej tabeli, uruchamiając `SELECT *` instrukcję Informix. | **Nazwa tabeli**: Nazwa potrzebnej tabeli programu Informix <p><p>Aby dodać inne właściwości do tej akcji, wybierz je z listy **Dodaj nowy parametr** . Aby uzyskać więcej informacji, zobacz [temat informacje o łączniku](/connectors/informix/). |
   | **Pobierz wiersz** | Pobierz wiersz z określonej tabeli, uruchamiając `SELECT WHERE` instrukcję Informix. | - **Nazwa tabeli**: Nazwa potrzebnej tabeli programu Informix <br>- **Identyfikator wiersza**: unikatowy identyfikator wiersza, na przykład `9999` |
   | **Wstaw wiersz** | Dodaj wiersz do określonej tabeli programu Informix, uruchamiając instrukcję Informix `INSERT` . | - **Nazwa tabeli**: Nazwa potrzebnej tabeli programu Informix <br>- **element**: wiersz z wartościami do dodania |
   | **Aktualizuj wiersz** | Zmień wiersz w określonej tabeli programu Informix, uruchamiając instrukcję Informix `UPDATE` . | - **Nazwa tabeli**: Nazwa potrzebnej tabeli programu Informix <br>- **Identyfikator wiersza**: unikatowy identyfikator wiersza do zaktualizowania, na przykład `9999` <br>- **Wiersz**: wiersz z zaktualizowanymi wartościami, na przykład `102` |
   | **Usuń wiersz** | Usuń wiersz z określonej tabeli programu Informix, uruchamiając instrukcję Informix `DELETE` . | - **Nazwa tabeli**: Nazwa potrzebnej tabeli programu Informix <br>- **Identyfikator wiersza**: unikatowy identyfikator wiersza do usunięcia, na przykład `9999` |
   ||||

1. Zapisz aplikację logiki. Teraz [Przetestuj aplikację logiki](#test-logic-app) lub Kontynuuj tworzenie aplikacji logiki.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Nawiązywanie połączenia z programem Informix

1. Jeśli aplikacja logiki nawiązuje połączenie z lokalną bazą danych, wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.

1. Podaj te informacje o połączeniu, a następnie wybierz pozycję **Utwórz**.

   | Właściwość | Właściwość JSON | Wymagane | Przykładowa wartość | Opis |
   |----------|---------------|----------|---------------|-------------|
   | Nazwa połączenia | `name` | Tak | `informix-demo-connection` | Nazwa, która ma być używana w połączeniu z bazą danych programu Informix |
   | Serwer | `server` | Tak | Chmury `informixdemo.cloudapp.net:9089` <br>-On-premises: `informixdemo:9089` | Adres TCP/IP lub alias w formacie IPv4 lub IPv6, po którym następuje dwukropek i numer portu TCP/IP |
   | baza danych | `database` | Tak | `nwind` | Nazwa relacyjnej bazy danych DRDA (RDBNAM) lub nazwa bazy danych programu Informix (dbname). Informix akceptuje 128-bajtowy ciąg. |
   | Uwierzytelnianie | `authentication` | Tylko lokalne | **Basic** lub **Windows** (Kerberos) | Typ uwierzytelniania, który jest wymagany przez bazę danych programu Informix. Ta właściwość jest dostępna tylko po wybraniu opcji **Połącz za pośrednictwem lokalnej bramy danych**. |
   | Nazwa użytkownika | `username` | Nie | <*Baza danych — nazwa użytkownika*> | Nazwa użytkownika bazy danych |
   | Hasło | `password` | Nie | <*Baza danych — hasło*> | Hasło do bazy danych |
   | Brama | `gateway` | Tylko lokalne | -<*platformy Azure — subskrypcja*> <br>-<*Azure-on-premises-Data-Gateway-Resource*> | Subskrypcja platformy Azure i nazwa zasobu platformy Azure dla lokalnej bramy danych utworzonej w Azure Portal. Właściwość **Gateway** i właściwości podrzędne są wyświetlane tylko po wybraniu opcji **Połącz za pośrednictwem lokalnej bramy danych**. |
   ||||||

   Na przykład:

   * **Baza danych w chmurze**

     ![Informacje o połączeniu z bazą danych w chmurze](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Lokalna baza danych**

     ![Informacje o połączeniu z lokalną bazą danych](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Zapisz aplikację logiki.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Uruchom**. Po uruchomieniu aplikacji logiki można wyświetlić dane wyjściowe z tego przebiegu.

1. Z menu aplikacji logiki wybierz pozycję **Przegląd**. W okienku Przegląd w obszarze **Summary**  >  **historia uruchamiania**podsumowania wybierz ostatnie uruchomienie.

1. W obszarze **Uruchom aplikację logiki**wybierz pozycję **Uruchom szczegóły**.

1. Z listy Akcje wybierz akcję z wynikami, które chcesz wyświetlić, na przykład **Get_tables**.

   Jeśli akcja zakończyła się pomyślnie, jej właściwość **status** jest oznaczona jako **zakończona powodzeniem**.

1. Aby wyświetlić dane wejściowe, w obszarze **link danych wejściowych**wybierz link adresu URL. Aby wyświetlić dane wyjściowe, w obszarze link do danych **wyjściowych** wybierz link adresu URL. Oto kilka przykładowych danych wyjściowych:

   * **Get_tables** wyświetla listę tabel:

     ![Wyniki z akcji "Pobierz tabele"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** wyświetla listę wierszy:

     ![Wyniki z akcji "Pobierz wiersze"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** pokazuje określony wiersz:

     ![Dane wyjściowe z akcji "Pobierz wiersz"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** wyświetla nowy wiersz:

     ![Dane wyjściowe z akcji "Wstaw wiersz"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** wyświetla zaktualizowany wiersz:

     ![Dane wyjściowe z akcji "Aktualizuj wiersz"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** pokazuje usunięty wiersz:

     ![Dane wyjściowe z akcji "Usuń wiersz"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie struktury Swagger łącznika, przejrzyj [stronę odwołania łącznika](/connectors/informix/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](apis-list.md)
