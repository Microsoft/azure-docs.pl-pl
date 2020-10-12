---
title: Łączenie z serwerem FTP
description: Automatyzowanie zadań i przepływów pracy, które tworzą, monitorują i zarządzają plikami na serwerze FTP za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284017"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Tworzenie i monitorowanie plików FTP oraz zarządzanie nimi za pomocą usługi Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika FTP można tworzyć automatyczne zadania i przepływy pracy, które tworzą, monitorują, wysyłają i odbierają pliki przy użyciu konta na serwerze FTP oraz z innymi akcjami, na przykład:

* Monitoruj, gdy pliki są dodawane lub zmieniane.
* Pobieranie, tworzenie, kopiowanie, aktualizowanie, wyświetlanie i usuwanie plików.
* Pobieranie zawartości i metadanych pliku.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które odbierają odpowiedzi z serwera FTP i udostępniają dane wyjściowe innym akcjom. W aplikacjach logiki można używać akcji Run do zarządzania plikami na serwerze FTP. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji FTP. Na przykład w przypadku regularnego pobierania plików z serwera FTP można wysyłać wiadomości e-mail dotyczące tych plików i ich zawartości przy użyciu łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli dopiero zaczynasz w usłudze Logic Apps, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Ograniczenia

* Łącznik FTP obsługuje tylko jawny protokół FTP over TLS/SSL (FTPS) i nie jest zgodny z niejawnym FTPS.

* Domyślnie akcje FTP mogą odczytywać lub zapisywać pliki, które są *50 MB lub mniejsze*. Aby obsługiwać pliki o rozmiarze większym niż 50 MB, operacje FTP obsługują [fragmenty komunikatów](../logic-apps/logic-apps-handle-large-messages.md). Akcja **Pobierz zawartość pliku** niejawnie używa podziału.

* Wyzwalacze FTP nie obsługują fragmentacji. Podczas żądania zawartości pliku wyzwalane są tylko pliki o rozmiarze 50 MB lub mniejszej. Aby uzyskać pliki o rozmiarze większym niż 50 MB, wykonaj następujące czynności:

  * Użyj wyzwalacza FTP, który zwraca właściwości pliku, na przykład **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

  * Śledź wyzwalacz z akcją **Pobierz zawartość pliku** FTP, która odczytuje kompletny plik i niejawnie używa podziału.

* Jeśli masz lokalny serwer FTP, rozważ utworzenie [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) lub [Azure App Service połączenia hybrydowe](../app-service/app-service-hybrid-connections.md), które umożliwiają dostęp do lokalnych źródeł danych bez korzystania z lokalnej bramy danych.

## <a name="how-ftp-triggers-work"></a>Jak działają wyzwalacze FTP

Wyzwalacze FTP działają przez sondowanie systemu plików FTP i wyszukiwanie wszelkich plików, które zostały zmienione od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy pliki zmienią się. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł funkcjonować. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient SFTP | Akcja |
|-------------|--------|
| WinSCP | Przejdź do pozycji **Opcje**  >  **Preferencje**  >  **transfer**  >  **Edycja**  >  **Zachowaj sygnaturę czasową**  >  **Wyłącz** |
| FileZilla | Przejdź do **transferu**  >  **Zachowaj sygnatury czasowe transferowanych plików**  >  **wyłączone** |
|||

Gdy wyzwalacz odnajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest zakończony i nie jest częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć powrotu częściowo zapisywanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnio wprowadzone zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sonduje serwer. Czasami takie zachowanie może spowodować opóźnienie, który jest maksymalnie dwa razy interwał sondowania wyzwalacza.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera hosta FTP i poświadczenia konta

  Łącznik FTP wymaga, aby serwer FTP był dostępny z Internetu i skonfigurowany do działania w trybie *pasywnym* . Twoje poświadczenia pozwalają aplikacji logiki utworzyć połączenie i uzyskać dostęp do konta FTP.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta FTP. Aby rozpocząć pracę z wyzwalaczem FTP, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji FTP, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-ftp"></a>Nawiązywanie połączenia z serwerem FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź `ftp` jako filtr. Z listy **wyzwalacze** Wybierz wyzwalacz, który chcesz.

   -lub-

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**, a następnie wybierz pozycję **Dodaj akcję**. W polu wyszukiwania wprowadź `ftp` jako filtr. Z listy **Akcje** wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj informacje o połączeniu i wybierz pozycję **Utwórz**.

1. Podaj informacje dotyczące wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Dodaj wyzwalacz FTP

**Po dodaniu lub zmodyfikowaniu pliku (tylko właściwości)** wyzwalacz uruchamia przepływ pracy aplikacji logiki, gdy wyzwalacz wykryje, że plik zostanie dodany lub zmieniony na serwerze FTP. Na przykład można dodać warunek, który sprawdza zawartość pliku i decyduje o tym, czy zawartość jest zgodna z określonym warunkiem. Na koniec można dodać akcję, która pobiera zawartość pliku i umieścić tę zawartość w innym folderze na serwerze SFTP.

Na przykład można użyć tego wyzwalacza do monitorowania folderu FTP dla nowych plików, które opisują zamówienia klientów. Następnie można użyć akcji FTP, takiej jak **Pobierz metadane pliku** , aby pobrać właściwości dla tego nowego pliku, a następnie użyć **Pobierz zawartość pliku** , aby pobrać zawartość z tego pliku do dalszej obróbki i przechować ją w bazie danych zamówień.

Oto przykład, który pokazuje, w jaki sposób używać **po dodaniu lub zmodyfikowaniu pliku (tylko właściwości)** wyzwalacza.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź `ftp` jako filtr. Na liście Wyzwalacze wybierz ten wyzwalacz: **po dodaniu lub zmodyfikowaniu zgłoszenia (tylko właściwości)**

   ![Znajdź i wybierz wyzwalacz FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Podaj niezbędne szczegóły dotyczące połączenia, a następnie wybierz pozycję **Utwórz**.

   Domyślnie ten łącznik przenosi pliki w formacie tekstowym. Aby przenieść pliki w formacie binarnym, na przykład gdzie i kiedy jest używane kodowanie, wybierz opcję **transport binarny**.

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. W polu **folder** wybierz ikonę folderu, aby wyświetlić listę. Aby znaleźć folder, który ma być monitorowany dla nowych lub edytowanych plików, wybierz strzałkę w prawo ( **>** ), przejdź do tego folderu, a następnie wybierz folder.

   ![Znajdź i wybierz folder do monitorowania](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Wybrany folder zostanie wyświetlony w polu **folder** .

   ![Wybrany folder zostanie wyświetlony w właściwości "folder"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Teraz, gdy aplikacja logiki ma wyzwalacz, Dodaj akcje, które chcesz uruchomić, gdy aplikacja logiki odnajdzie nowy lub zmodyfikowany plik. Na potrzeby tego przykładu można dodać akcję FTP, która pobiera nową lub zaktualizowaną zawartość.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Dodaj akcję FTP

Akcja **Pobierz metadane pliku** pobiera właściwości pliku znajdującego się na serwerze FTP, a Akcja **Pobierz zawartość pliku** pobiera zawartość pliku na podstawie informacji o tym pliku na serwerze FTP. Na przykład można dodać wyzwalacz z poprzedniego przykładu i te akcje, aby pobrać zawartość pliku po dodaniu lub zmodyfikowaniu pliku.

1. W obszarze wyzwalacza lub innych akcji wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź `ftp` jako filtr. Na liście Akcje wybierz tę akcję: **Pobierz metadane pliku**

   ![Wybierz akcję "Pobierz metadane pliku"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Jeśli masz już połączenie z serwerem FTP i kontem, przejdź do następnego kroku. W przeciwnym razie podaj niezbędne szczegóły tego połączenia, a następnie wybierz pozycję **Utwórz**.

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Gdy zostanie wyświetlona Akcja **Pobierz metadane pliku** , kliknij wewnątrz pola **plik** , aby wyświetlić listę zawartości dynamicznej. Teraz można wybrać właściwości danych wyjściowych z poprzednich kroków. Z listy zawartość dynamiczna w obszarze **Pobieranie metadanych pliku**wybierz właściwość **identyfikatora plików** , która odwołuje się do kolekcji, w której plik został dodany lub zaktualizowany.

   ![Znajdź i wybierz właściwość "Lista identyfikatorów plików"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   Zostanie wyświetlona **Lista identyfikatorów plików** w polu **plik** .

   ![Wybrana Właściwość "Lista identyfikatorów plików"](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Teraz Dodaj tę akcję FTP: **Pobierz zawartość pliku**

   ![Znajdź i wybierz akcję "Pobierz zawartość pliku"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Gdy zostanie wyświetlona Akcja **Pobierz zawartość pliku** , kliknij wewnątrz pola **plik** , aby wyświetlić listę zawartości dynamicznej. Teraz można wybrać właściwości danych wyjściowych z poprzednich kroków. Z listy zawartość dynamiczna w obszarze **Pobierz metadane pliku**wybierz właściwość **identyfikatora** , która odwołuje się do pliku, który został dodany lub zaktualizowany.

   ![Znajdź i wybierz właściwość "ID"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   W polu **plik** zostanie wyświetlona Właściwość **ID** .

   ![Wybrana Właściwość "ID"](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Zapisz aplikację logiki.

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy przepływ pracy zwraca oczekiwaną zawartość, Dodaj kolejną akcję, która wysyła zawartość z przekazanego lub zaktualizowanego pliku.

1. W obszarze Akcja **Pobierz zawartość pliku** Dodaj akcję, która może wysyłać zawartość pliku. Ten przykład dodaje akcję **Wyślij wiadomość e-mail** dotyczącą pakietu Office 365 Outlook.

   ![Dodawanie akcji wysyłania wiadomości e-mail](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Po wyświetleniu akcji podaj informacje i Uwzględnij właściwości, które chcesz przetestować. Na przykład Uwzględnij Właściwość **zawartości pliku** , która pojawia się na liście zawartości dynamicznej po wybraniu opcji **Zobacz więcej** w sekcji **pobieranie zawartości pliku** .

   ![Podaj informacje o akcji poczty e-mail](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Zapisz aplikację logiki. Aby uruchomić i wyzwolić aplikację logiki, na pasku narzędzi wybierz pozycję **Uruchom**, a następnie Dodaj plik do folderu FTP, który teraz monitoruje aplikację logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/ftpconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

