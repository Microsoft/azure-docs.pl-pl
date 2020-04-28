---
title: Łączenie się z bazą danych IBM Informix
description: Automatyzacja zadań i przepływów pracy, które zarządzają zasobami przechowywanymi w programie IBM Informix przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757972"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Zarządzanie zasobami bazy danych IBM Informix przy użyciu aplikacji Azure Logic Apps

Za [pomocą usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika Informix](/connectors/informix/)można tworzyć zautomatyzowane zadania i przepływy pracy, które zarządzają zasobami w bazie danych IBM Informix. Ten łącznik zawiera klienta firmy Microsoft, który komunikuje się ze zdalnymi komputerami serwera Informix w sieci TCP/IP, w tym z bazami danych opartymi na chmurze, takimi jak IBM Informix dla systemu Windows działającymi w wirtualizacji platformy Azure i lokalnymi bazami danych podczas korzystania z [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Można połączyć się z tymi platformami i wersjami Informix, jeśli są one skonfigurowane do obsługi połączeń klientów DDA (Distributed Relational Database Architecture):You can connect to these Informix platforms and versions if they are configured to support Distributed Relational Database Architecture (DRDA) client connections:

* IBM Informix 12.1
* IBM Informix 11.7

W tym temacie pokazano, jak używać łącznika w aplikacji logiki do przetwarzania operacji bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* W przypadku lokalnych baz danych [pobierz i zainstaluj lokalną bramę danych na](../logic-apps/logic-apps-gateway-install.md) komputerze lokalnym, a następnie [utwórz zasób bramy danych platformy Azure w witrynie Azure portal](../logic-apps/logic-apps-gateway-connection.md).

* Aplikacja logiki, w której potrzebny jest dostęp do bazy danych Informix. Ten łącznik udostępnia tylko akcje, więc aplikacja logiki musi już rozpocząć się z wyzwalaczem, na przykład [wyzwalacz cyklu](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Dodawanie akcji Informix

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki, jeśli nie jest jeszcze otwarty.

1. W obszarze kroku, w którym chcesz dodać akcję Informix, wybierz pozycję **Nowy krok**.

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania `informix` wprowadź jako filtr. Z listy akcji wybierz odpowiednią akcję, na przykład:

   ![Wybierz akcję Informix do uruchomienia](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Łącznik udostępnia następujące akcje, które uruchamiają odpowiednie operacje bazy danych:

   * Pobierz tabele — lista `CALL` tabel bazy danych przy użyciu instrukcji
   * Pobierz wiersze — odczytuj `SELECT *` wszystkie wiersze przy użyciu instrukcji
   * Pobierz wiersz — odczytywanie `SELECT WHERE` wiersza przy użyciu instrukcji
   * Dodawanie wiersza przy `INSERT` użyciu instrukcji
   * Edytowanie wiersza `UPDATE` przy użyciu instrukcji
   * Usuwanie wiersza przy `DELETE` użyciu instrukcji

1. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia dla bazy danych Informix, wykonaj [kroki, aby utworzyć połączenie,](#create-connection)a następnie przejdź do następnego kroku.

1. Podaj informacje dotyczące wybranej akcji:

   | Akcja | Opis | Właściwości i opisy |
   |--------|-------------|-----------------------------|
   | **Pobierz tabele** | Lista tabel bazy danych przez uruchomienie instrukcji Informix CALL. | Brak |
   | **Pobierz wiersze** | Pobierz wszystkie wiersze w określonej tabeli, `SELECT *` uruchamiając Instrukcję Informix. | **Nazwa tabeli**: Nazwa żądanej tabeli Informix <p><p>Aby dodać inne właściwości do tej akcji, wybierz je z listy **Dodaj nowy parametr.** Aby uzyskać więcej informacji, zobacz [temat odwołania łącznika](/connectors/informix/). |
   | **Pobierz wiersz** | Pobierz wiersz z określonej tabeli, `SELECT WHERE` uruchamiając Instrukcję Informix. | - **Nazwa tabeli**: Nazwa żądanej tabeli Informix <br>- **Identyfikator wiersza:** unikatowy identyfikator wiersza, na przykład`9999` |
   | **Wstaw wiersz** | Dodaj wiersz do określonej tabeli Informix, uruchamiając instrukcję Informix. `INSERT` | - **Nazwa tabeli**: Nazwa żądanej tabeli Informix <br>- **element**: Wiersz z wartościami do dodania |
   | **Aktualizuj wiersz** | Zmień wiersz w określonej tabeli Informix, uruchamiając instrukcję Informix. `UPDATE` | - **Nazwa tabeli**: Nazwa żądanej tabeli Informix <br>- **Identyfikator wiersza:** unikatowy identyfikator wiersza do aktualizacji, na przykład`9999` <br>- **Wiersz:** Wiersz ze zaktualizowanymi wartościami, na przykład`102` |
   | **Usuń wiersz** | Usuń wiersz z określonej tabeli Informix, uruchamiając instrukcję Informix. `DELETE` | - **Nazwa tabeli**: Nazwa żądanej tabeli Informix <br>- **Identyfikator wiersza:** unikatowy identyfikator wiersza do usunięcia, na przykład`9999` |
   ||||

1. Zapisz aplikację logiki. Teraz [przetestuj aplikację logiki](#test-logic-app) lub kontynuuj tworzenie aplikacji logiki.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Połącz się z informixem

1. Jeśli aplikacja logiki łączy się z lokalną bazą danych, wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.

1. Podaj te informacje o połączeniu, a następnie wybierz pozycję **Utwórz**.

   | Właściwość | Właściwość JSON | Wymagany | Przykładowa wartość | Opis |
   |----------|---------------|----------|---------------|-------------|
   | Nazwa połączenia | `name` | Yes | `informix-demo-connection` | Nazwa używana do połączenia z bazą danych Informix |
   | Serwer | `server` | Yes | - Chmura:`informixdemo.cloudapp.net:9089` <br>- Lokalnie:`informixdemo:9089` | Adres lub alias TCP/IP w formacie IPv4 lub IPv6, po którym następuje dwukropek i numer portu TCP/IP |
   | baza danych | `database` | Yes | `nwind` | Nazwa relacyjnej bazy danych DRDA (RDBNAM) lub nazwa bazy danych Informix (nazwa dbname). Informix akceptuje ciąg 128 bajtów. |
   | Authentication | `authentication` | Tylko lokalnie | **Podstawowe** lub **Windows** (kerberos) | Typ uwierzytelniania wymagany przez bazę danych Informix. Ta właściwość jest wyświetlana tylko po wybraniu opcji **Połącz za pośrednictwem lokalnej bramy danych**. |
   | Nazwa użytkownika | `username` | Nie | <*nazwa użytkownika bazy danych*> | Nazwa użytkownika bazy danych |
   | Hasło | `password` | Nie | <*hasło bazy danych*> | Hasło do bazy danych |
   | Brama | `gateway` | Tylko lokalnie | - <*subskrypcja platformy Azure*> <br>- <*zasobu azure-on-premise-data-gateway*> | Subskrypcja platformy Azure i nazwa zasobu platformy Azure dla lokalnej bramy danych utworzonej w witrynie Azure portal. Właściwość **bramy** i właściwości podrzędne są wyświetlane tylko po wybraniu opcji **Połącz za pośrednictwem lokalnej bramy danych**. |
   ||||||

   Przykład:

   * **Baza danych w chmurze**

     ![Informacje o połączeniu z bazą danych w chmurze](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Lokalna baza danych**

     ![Informacje o połączeniu lokalnej bazy danych](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Zapisz aplikację logiki.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Na pasku narzędzi Projektanta aplikacji logiki wybierz pozycję **Uruchom**. Po uruchomieniu aplikacji logiki można wyświetlić dane wyjściowe z tego uruchomienia.

1. Z menu aplikacji logiki wybierz pozycję **Przegląd**. W okienku przeglądu w obszarze**Historia przebiegów** **podsumowania** > wybierz najnowsze uruchomienie.

1. W obszarze **Uruchamianie aplikacji Logika**wybierz pozycję **Uruchom szczegóły**.

1. Z listy akcji wybierz akcję z wyjściami, które chcesz wyświetlić, na przykład **Get_tables**.

   Jeśli akcja zakończyła się pomyślnie, ich **właściwość Status** jest oznaczona jako **Powodem.**

1. Aby wyświetlić dane wejściowe, w obszarze **Inputs Link**wybierz łącze URL. Aby wyświetlić dane wyjściowe, w obszarze **Łącze wyjścia** wybierz łącze URL. Oto kilka przykładowych wyjść:

   * **Get_tables** pokazuje listę tabel:

     ![Wyjścia z akcji "Pobierz tabele"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** pokazuje listę wierszy:

     ![Dane wyjściowe z akcji "Pobierz wiersze"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** pokazuje określony wiersz:

     ![Dane wyjściowe z akcji "Pobierz wiersz"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** pokazuje nowy wiersz:

     ![Wyjścia z akcji "Wstaw wiersz"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** pokazuje zaktualizowany wiersz:

     ![Wyjścia z akcji "Aktualizuj wiersz"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** pokazuje usunięty wiersz:

     ![Dane wyjściowe z akcji "Usuń wiersz"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie Swagger łącznika, przejrzyj [stronę referencyjną łącznika](/connectors/informix/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](apis-list.md)
