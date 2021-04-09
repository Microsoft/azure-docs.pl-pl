---
title: Połącz z kontem SFTP (przestarzałe)
description: Automatyzowanie zadań i procesów, które monitorują, tworzą, zarządzają i odbierają pliki dla serwera SFTP przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673813"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>Monitorowanie i tworzenie plików SFTP oraz zarządzanie nimi w Azure Logic Apps

> [!IMPORTANT]
> Użyj [łącznika SFTP-SSH](../connectors/connectors-sftp-ssh.md) , ponieważ łącznik SFTP jest przestarzały. Nie można już wybierać wyzwalaczy i akcji SFTP w Projektancie aplikacji logiki.

Aby zautomatyzować zadania, które monitorują, tworzą, wysyłają i odbierają pliki na serwerze [bezpiecznym protokół transferu plików (SFTP)](https://www.ssh.com/ssh/sftp/) , można tworzyć i automatyzować przepływy pracy integracji przy użyciu Azure Logic Apps i łącznika SFTP. SFTP to protokół sieciowy, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami za pośrednictwem dowolnego niezawodnego strumienia danych. Poniżej przedstawiono kilka przykładowych zadań, które można zautomatyzować:

* Monitoruj, gdy pliki są dodawane lub zmieniane.
* Pobieranie, tworzenie, kopiowanie, aktualizowanie, wyświetlanie i usuwanie plików.
* Pobieranie zawartości i metadanych pliku.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które monitorują zdarzenia na serwerze SFTP i udostępniają dane wyjściowe dla innych akcji. Możesz użyć akcji, które wykonują różne zadania na serwerze SFTP. Możesz również mieć inne akcje w aplikacji logiki, używając danych wyjściowych z akcji SFTP. Na przykład w przypadku regularnego pobierania plików z serwera SFTP można wysyłać alerty e-mail dotyczące tych plików i ich zawartości przy użyciu łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limity

Łącznik SFTP obsługuje tylko pliki z *50 MB lub mniejszych* i nie obsługuje fragmentacji [komunikatów](../logic-apps/logic-apps-handle-large-messages.md). W przypadku większych plików użyj [łącznika SFTP-SSH](../connectors/connectors-sftp-ssh.md). Aby zapoznać się z różnicami między łącznikiem SFTP a łącznikiem SFTP-SSH, zapoznaj się z tematem [porównanie protokołu SFTP-SSH i SFTP](../connectors/connectors-sftp-ssh.md#comparison) w artykule SFTP-SSH.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera SFTP i poświadczenia konta, które umożliwiają aplikacji logiki dostęp do Twojego konta SFTP. Aby można było korzystać z protokołu [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) , wymagany jest również dostęp do prywatnego klucza SSH oraz hasła prywatnego klucza SSH.

  > [!NOTE]
  >
  > Łącznik SFTP obsługuje te formaty kluczy prywatnych: OpenSSH, ssh.com i.
  >
  > Gdy tworzysz aplikację logiki, po dodaniu żądanego wyzwalacza SFTP lub akcji musisz podać informacje o połączeniu dla serwera SFTP. 
  > Jeśli używasz klucza prywatnego SSH, pamiętaj, aby ***skopiować*** klucz z pliku prywatnego klucza SSH i ***wkleić*** go do szczegółów połączenia, ***nie wprowadzaj ręcznie ani nie edytuj klucza***, co może spowodować niepowodzenie połączenia. 
  > Aby uzyskać więcej informacji, zobacz kroki opisane w dalszej części tego artykułu.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Aby rozpocząć pracę z wyzwalaczem SFTP, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="how-sftp-triggers-work"></a>Jak działają wyzwalacze SFTP

Wyzwalacze SFTP działają przez sondowanie systemu plików SFTP i wyszukiwanie wszystkich plików, które zostały zmienione od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy pliki zmienią się. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł funkcjonować. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient SFTP | Akcja |
|-------------|--------|
| WinSCP | Przejdź do pozycji **Opcje**  >  **Preferencje**  >  **transfer**  >  **Edycja**  >  **Zachowaj sygnaturę czasową**  >  **Wyłącz** |
| FileZilla | Przejdź do **transferu**  >  **Zachowaj sygnatury czasowe transferowanych plików**  >  **wyłączone** |
|||

Gdy wyzwalacz odnajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest zakończony i nie jest częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć powrotu częściowo zapisywanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnio wprowadzone zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sonduje serwer. Czasami takie zachowanie może spowodować opóźnienie, który jest maksymalnie dwa razy interwał sondowania wyzwalacza.

## <a name="connect-to-sftp"></a>Nawiązywanie połączenia z SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "SFTP" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz.

   -lub-

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. W polu wyszukiwania wprowadź ciąg "SFTP" jako filtr. Na liście Akcje wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj niezbędne szczegóły dotyczące połączenia.

   > [!IMPORTANT]
   >
   > Po wprowadzeniu klucza prywatnego SSH we właściwości **prywatnego klucza SSH** wykonaj te dodatkowe kroki, aby upewnić się, że podajesz pełną i poprawną wartość dla tej właściwości. 
   > Nieprawidłowy klucz powoduje niepowodzenie połączenia.

   Mimo że można użyć dowolnego edytora tekstu, poniżej przedstawiono przykładowe kroki, które pokazują, jak poprawnie skopiować i wkleić klucz przy użyciu Notepad.exe na przykład.

   1. Otwórz plik klucza prywatnego SSH w edytorze tekstu. W tych krokach użyto Notatnika jako przykładu.

   1. W menu **Edycja** Notatnik wybierz pozycję **Zaznacz wszystko**.

   1. Wybierz pozycję **Edytuj**  >  **kopię**.

   1. W wyzwalaczu SFTP lub akcji, który został dodany, wklej *pełny* klucz skopiowany do właściwości **prywatnego klucza SSH** , który obsługuje wiele wierszy. **_Upewnij się, że wkleisz_*klawisz _. _*_nie wprowadzaj ręcznie ani nie edytuj klucza_**.

1. Po zakończeniu wprowadzania szczegółów połączenia wybierz pozycję **Utwórz**.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz SFTP: po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki, gdy na serwerze SFTP zostanie dodany lub zmieniony plik. Na przykład można dodać warunek, który sprawdza zawartość pliku i pobiera zawartość w zależności od tego, czy zawartość spełnia określony warunek. Następnie można dodać akcję, która pobiera zawartość pliku i umieszcza tę zawartość w folderze na serwerze SFTP.

**Przykład przedsiębiorstwa**: ten wyzwalacz służy do monitorowania folderu SFTP dla nowych plików reprezentujących zamówienia klienta. Następnie można użyć akcji SFTP, takiej jak **pobieranie zawartości pliku** , aby uzyskać zawartość zamówienia do dalszej obróbki i przechowywać ją w bazie danych zamówień.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Akcja SFTP: pobieranie zawartości

Ta akcja pobiera zawartość z pliku na serwerze SFTP. Na przykład można dodać wyzwalacz z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek ma wartość true, Akcja, która pobiera zawartość, może zostać uruchomiona.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](../data-factory/connector-sftp.md)łącznika.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)