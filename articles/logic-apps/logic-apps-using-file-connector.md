---
title: Połącz z systemami plików lokalnie
description: Automatyzowanie zadań i przepływów pracy łączących się z lokalnymi systemami plików z łącznikiem systemu plików za pomocą lokalnej bramy danych w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4715d7173dd959d12350229e457717c908a83756
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91873238"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Łączenie z lokalnymi systemami plików przy użyciu usługi Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika systemu plików można tworzyć automatyczne zadania i przepływy pracy, które tworzą pliki i zarządzają nimi w lokalnym udziale plików, na przykład:

- Twórz, pobieraj, dołączaj, Aktualizuj i usuwaj pliki.
- Wyświetl listę plików w folderach lub folderach głównych.
- Pobieranie zawartości i metadanych pliku.

  > [!IMPORTANT]
  > Łącznik systemu plików obsługuje obecnie tylko systemy plików Windows w systemach operacyjnych Windows.  

W tym artykule pokazano, jak połączyć się z lokalnym systemem plików zgodnie z opisem w tym przykładowym scenariuszu: Skopiuj plik przekazany do usługi Dropbox do udziału plików, a następnie Wyślij wiadomość e-mail. Aby bezpiecznie połączyć się z systemami lokalnymi i uzyskać do nich dostęp, Aplikacje logiki korzystają z [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Informacje techniczne dotyczące łącznika można znaleźć w [dokumentacji dotyczącej łącznika systemu plików](/connectors/filesystem/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aby można było połączyć Aplikacje logiki z systemami lokalnymi, takimi jak serwer systemu plików, należy [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). Dzięki temu można określić, czy podczas tworzenia połączenia z systemem plików z poziomu aplikacji logiki ma być używana instalacja bramy.

* [Konto usługi Dropbox](https://www.dropbox.com/), którego możesz zarejestrować się bezpłatnie. Poświadczenia konta są niezbędne do utworzenia połączenia między aplikacją logiki a kontem usługi Dropbox.

* Dostęp do komputera z systemem plików, który ma być używany. Na przykład w przypadku zainstalowania bramy danych na tym samym komputerze, na którym znajduje się system plików, potrzebne są poświadczenia konta dla tego komputera.

* Konto e-mail dostawcy, który jest obsługiwany przez Logic Apps, na przykład Office 365 Outlook, Outlook.com lub gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). Ta aplikacja logiki korzysta z konta służbowego. Jeśli korzystasz z innego konta e-mail, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić.

  > [!IMPORTANT]
  > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Na potrzeby tego przykładu potrzebujesz pustej aplikacji logiki.

## <a name="add-trigger"></a>Dodawanie wyzwalacza

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W polu wyszukiwania wprowadź ciąg "Dropbox" jako filtr. Z listy Wyzwalacze wybierz ten wyzwalacz: **po utworzeniu pliku**

   ![Wybierz wyzwalacz usługi Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Zaloguj się przy użyciu poświadczeń konta usługi Dropbox i Autoryzuj dostęp do danych usługi Dropbox w celu Azure Logic Apps.

1. Podaj wymagane informacje dla wyzwalacza.

   ![Wyzwalacz usługi Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Dodaj akcje

1. W obszarze wyzwalacza wybierz pozycję **Następny krok**. W polu wyszukiwania wprowadź "system plików" jako filtr. Z listy Akcje wybierz pozycję Ta akcja: **Utwórz plik**

   ![Znajdź łącznik systemu plików](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Jeśli nie masz jeszcze połączenia z systemem plików, zostanie wyświetlony monit o utworzenie połączenia.

   ![Tworzenie połączenia](media/logic-apps-using-file-connector/file-system-connection.png)

   | Właściwość | Wymagany | Wartość | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Nazwa połączenia** | Tak | <*Nazwa połączenia*> | Nazwa, która ma być używany dla połączenia |
   | **Folder główny** | Tak | <*Nazwa folderu głównego*> | Folder główny systemu plików, na przykład jeśli zainstalowano lokalną bramę danych, taką jak folder lokalny na komputerze, na którym jest zainstalowana lokalna Brama danych lub folder udziału sieciowego, do którego komputer ma dostęp. <p>Na przykład: `\\PublicShare\\DropboxFiles` <p>Folder główny jest głównym folderem nadrzędnym, który jest używany dla ścieżek względnych dla wszystkich akcji związanych z plikami. |
   | **Typ uwierzytelniania** | Nie | <*Typ uwierzytelniania*> | Typ uwierzytelniania używany przez system plików: **Windows** |
   | **Nazwa użytkownika** | Tak | < > \\ domena < *Nazwa użytkownika*> <p>-lub- <p><*komputer* > \\ lokalny < *Nazwa użytkownika*> | Nazwa użytkownika komputera, na którym znajduje się folder systemu plików. <p>Jeśli folder systemu plików znajduje się na tym samym komputerze co lokalna Brama danych, można użyć < > \\ < *nazwy użytkownika* lokalnego komputera>. |
   | **Password** (Hasło) | Tak | <*Twoje hasło*> | Hasło do komputera, na którym znajduje się system plików |
   | **punkt** | Tak | <*Instalacja bramy — nazwa*> | Nazwa zainstalowanej wcześniej bramy |
   |||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Logic Apps konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie. Jeśli połączenie jest prawidłowo skonfigurowane, opcje są wyświetlane dla wybranej wcześniej akcji.

1. W akcji **Utwórz plik** Podaj szczegóły kopiowania plików z usługi Dropbox do folderu głównego w lokalnym udziale plików. Aby dodać dane wyjściowe z poprzednich kroków, kliknij wewnątrz pól i wybierz opcję spośród dostępnych pól, gdy zostanie wyświetlona lista zawartość dynamiczna.

   ![Akcja tworzenia pliku](media/logic-apps-using-file-connector/create-file-filled.png)

1. Teraz Dodaj akcję programu Outlook, która wysyła wiadomość e-mail, aby użytkownicy wiedzieli o nowym pliku. Wprowadź adresatów, tytuł i treść wiadomości e-mail. Do testowania możesz użyć własnego adresu e-mail.

   ![Wyślij akcję poczty e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Zapisz aplikację logiki. Przetestuj aplikację, przekazując plik do usługi Dropbox.

   Aplikacja logiki powinna skopiować plik do lokalnego udziału plików i wysłać adresatom wiadomość e-mail dotyczącą skopiowanego pliku.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/fileconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [nawiązać połączenie z danymi lokalnymi](../logic-apps/logic-apps-gateway-connection.md) 
* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
