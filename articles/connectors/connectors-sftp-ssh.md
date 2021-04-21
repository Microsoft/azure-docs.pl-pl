---
title: Nawiązywanie połączenia z serwerem SFTP przy użyciu protokołu SSH
description: Automatyzowanie zadań, które monitorują, tworzą, wysyłają i odbierają pliki dla serwera SFTP oraz zarządzają nimi przy użyciu protokołu SSH i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781563"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>Tworzenie plików SFTP i zarządzanie nimi przy użyciu protokołu SSH i Azure Logic Apps

Aby zautomatyzować zadania, które tworzą pliki i zarządzają nimi na serwerze [Secure protokół transferu plików (SFTP)](https://www.ssh.com/ssh/sftp/) przy użyciu protokołu [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) można utworzyć zautomatyzowane przepływy pracy integracji przy użyciu programu Azure Logic Apps i łącznika SFTP-SSH. SFTP to protokół sieciowy, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami za pośrednictwem dowolnego niezawodnego strumienia danych.

Oto kilka przykładowych zadań, które można zautomatyzować:

* Monitoruj, kiedy pliki są dodawane lub zmieniane.
* Pobierz, utwórz, skopiuj, zmień nazwę, zaktualizuj, utwórz listę i usuń pliki.
* Tworzenie folderów.
* Pobierz zawartość pliku i metadane.
* Wyodrębnij archiwa do folderów.

W przepływie pracy można użyć wyzwalacza, który monitoruje zdarzenia na serwerze SFTP i udostępnia dane wyjściowe innym akcji. Następnie można użyć akcji do wykonywania różnych zadań na serwerze SFTP. Możesz również uwzględnić inne akcje, które używają danych wyjściowych akcji SFTP-SSH. Jeśli na przykład regularnie pobierasz pliki z serwera SFTP, możesz wysyłać alerty e-mail dotyczące tych plików i ich zawartości przy użyciu łącznika usługi Office 365 Outlook lub łącznika Outlook.com usługi. Jeśli jesteś nowym użytkownikom aplikacji logiki, zapoznaj się z [tematem Co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Różnice między łącznikiem SFTP-SSH a łącznikiem SFTP można uzyskać w sekcji Porównanie protokołu [SFTP-SSH](#comparison) i SFTP w dalszej części tego tematu.

## <a name="limits"></a>Limity

* Łącznik SFTP-SSH obecnie nie obsługuje tych serwerów SFTP:

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* Akcje SFTP-SSH, [](../logic-apps/logic-apps-handle-large-messages.md) które obsługują fragmentowanie, mogą obsługiwać pliki o rozmiarze do 1 GB, natomiast akcje SFTP-SSH, które nie obsługują fragmentowania, mogą obsługiwać pliki o rozmiarze do 50 MB. Domyślny rozmiar fragmentu to 15 MB. Jednak ten rozmiar może zmieniać się dynamicznie, począwszy od 5 MB i stopniowo zwiększając do maksymalnego rozmiaru 50 MB. Dynamiczne zmiany rozmiaru są oparte na czynnikach, takich jak opóźnienie sieci, czas odpowiedzi serwera itp.

  > [!NOTE]
  > W przypadku aplikacji logiki w środowisku usługi integracji [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika wymaga fragmentowania, aby zamiast tego użyć limitów komunikatów [ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  To zachowanie adaptacyjne można zastąpić, określając zamiast tego stały [rozmiar](#change-chunk-size) fragmentu. Ten rozmiar może mieć zakres od 5 MB do 50 MB. Załóżmy na przykład, że masz plik o rozmiarze 45 MB i sieć, która może obsługiwać ten rozmiar pliku bez opóźnień. Adaptacyjne fragmentowanie powoduje kilka wywołań, a nie jedno wywołanie. Aby zmniejszyć liczbę wywołań, możesz spróbować ustawić rozmiar fragmentu 50 MB. W innym scenariuszu, jeśli aplikacja logiki ma limit czasu, na przykład w przypadku korzystania z fragmentów o rozmiarze 15 MB możesz spróbować zmniejszyć rozmiar do 5 MB.

  Rozmiar fragmentu jest skojarzony z połączeniem. Ten atrybut oznacza, że można użyć tego samego połączenia dla obu akcji, które obsługują fragmentowanie, i akcji, które nie obsługują fragmentowania. W tym przypadku rozmiar fragmentu dla akcji, które nie obsługują fragmentowania, wynosi od 5 MB do 50 MB. W poniższej tabeli przedstawiono, które akcje SFTP-SSH obsługują fragmentowanie:

  | Akcja | Obsługa fragmentowania | Obsługa zastępowania rozmiaru fragmentu |
  |--------|------------------|-----------------------------|
  | **Kopiowanie pliku** | Nie | Nie dotyczy |
  | **Tworzenie pliku** | Tak | Tak |
  | **Tworzenie folderu** | Nie dotyczy | Nie dotyczy |
  | **Usuwanie pliku** | Nie dotyczy | Nie dotyczy |
  | **Wyodrębnij archiwum do folderu** | Nie dotyczy | Nie dotyczy |
  | **Uzyskiwanie zawartości pliku** | Tak | Tak |
  | **Uzyskiwanie zawartości pliku przy użyciu ścieżki** | Tak | Tak |
  | **Uzyskiwanie metadanych pliku** | Nie dotyczy | Nie dotyczy |
  | **Uzyskiwanie metadanych pliku przy użyciu ścieżki** | Nie dotyczy | Nie dotyczy |
  | **Lista plików w folderze** | Nie dotyczy | Nie dotyczy |
  | **Zmienianie nazwy pliku** | Nie dotyczy | Nie dotyczy |
  | **Aktualizowanie pliku** | Nie | Nie dotyczy |
  ||||

* Wyzwalacze SFTP-SSH nie obsługują fragmentowania komunikatów. Podczas żądania zawartości pliku wyzwalacze wybieraj tylko pliki o rozmiarze 15 MB lub mniejszym. Aby uzyskać pliki większe niż 15 MB, zamiast tego postępuj zgodnie z tym wzorcem:

  1. Użyj wyzwalacza SFTP-SSH, który zwraca tylko właściwości pliku. Te wyzwalacze mają nazwy, które zawierają opis **(tylko właściwości).**

  1. Postępuj zgodnie z wyzwalaczem za pomocą akcji SFTP-SSH **Pobierz zawartość** pliku. Ta akcja odczytuje kompletny plik i niejawnie używa fragmentowania komunikatów.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porównanie protokołu SFTP-SSH i SFTP

Na poniższej liście opisano kluczowe możliwości protokołu SFTP-SSH, które różnią się od łącznika SFTP:

* Używa [biblioteki SSH.NET](https://github.com/sshnet/SSH.NET), która jest biblioteką open source Secure Shell (SSH), która obsługuje program .NET.

* Udostępnia **akcję Utwórz folder,** która tworzy folder w określonej ścieżce na serwerze SFTP.

* Udostępnia **akcję Zmień nazwę** pliku, która zmienia nazwę pliku na serwerze SFTP.

* Buforuje połączenie z serwerem SFTP *przez maksymalnie 1 godzinę.* Ta funkcja zwiększa wydajność i zmniejsza, jak często łącznik próbuje połączyć się z serwerem. Aby ustawić czas trwania tego zachowania buforowania, edytuj właściwość [ **ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) w konfiguracji protokołu SSH na serwerze SFTP.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera SFTP i poświadczenia konta, dzięki czemu przepływ pracy może uzyskać dostęp do konta SFTP. Potrzebny jest również dostęp do klucza prywatnego SSH i hasła klucza prywatnego SSH. Do przekazywania dużych plików przy użyciu fragmentowania potrzebny jest zarówno dostęp do odczytu, jak i zapisu dla folderu głównego na serwerze SFTP. W przeciwnym razie zostanie wyświetlany błąd "401 Brak autoryzacji".

  Łącznik SFTP-SSH obsługuje zarówno uwierzytelnianie za pomocą klucza prywatnego, jak i uwierzytelnianie hasłem. Jednak łącznik SFTP-SSH obsługuje *tylko* te formaty kluczy prywatnych, algorytmy i odciski palców:

  * **Formaty kluczy prywatnych:** RSA (Rivest Shamir Adleman) i DSA (Digital Signature Algorithm) w formatach OpenSSH i ssh.com formatach. Jeśli klucz prywatny jest w formacie pliku PuTTY (ppk), najpierw przekonwertuj klucz na format pliku [OpenSSH (pem).](#convert-to-openssh)
  * **Algorytmy szyfrowania:** DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC i AES-256-CBC
  * **Odcisk palca:** MD5

  Po dodaniu wyzwalacza lub akcji SFTP-SSH do przepływu pracy należy podać informacje o połączeniu dla serwera SFTP. Po powiązaniu klucza prywatnego SSH dla tego połączenia ***** nie należy ręcznie wprowadzać ani edytować klucza _, co może spowodować niepowodzenie połączenia. Zamiast tego upewnij się, _*_że_*_ klucz został skopiowany z pliku klucza prywatnego SSH, a element _ *_wklej_** ten klucz do szczegółów połączenia. Aby uzyskać więcej informacji, zobacz sekcję [Nawiązywanie połączenia z sftp za](#connect) pomocą protokołu SSH w dalszej części tego artykułu.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Przepływ pracy aplikacji logiki, w którym chcesz uzyskać dostęp do konta SFTP. Aby rozpocząć od wyzwalacza SFTP-SSH, [utwórz pusty przepływ pracy aplikacji logiki.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Aby użyć akcji SFTP-SSH, uruchom przepływ pracy z innym wyzwalaczem, na przykład **wyzwalaczem Cykl.**

## <a name="how-sftp-ssh-triggers-work"></a>Jak działają wyzwalacze SFTP-SSH

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Zachowanie sondowania

Wyzwalacze SFTP-SSH sonduje system plików SFTP i szukają plików, które uległy zmianie od czasu ostatniego sondowania. Niektóre narzędzia umożliwiają zachowanie znacznika czasu po zmianie plików. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz działał. Oto kilka typowych ustawień:

| Klient SFTP | Akcja |
|-------------|--------|
| Winscp | Przejdź do opcji  >    >  **Preferencje Transfer**  >  **Edytuj**  >  **Zachowaj znacznik czasu**  >  **Wyłącz** |
| Filezilla | Przejdź do **opcji Transfer** Zachowaj znaczniki  >  **czasu transferowanych plików**  >  **Wyłącz** |
|||

Gdy wyzwalacz znajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest ukończony i nie został częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć zwracania częściowo zapisanego pliku, wyzwalacz zaobejmuje znacznik czasu dla pliku, który ma ostatnie zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko podczas sondowania serwera ponownie. Czasami takie zachowanie może spowodować opóźnienie, które wynosi maksymalnie dwa razy więcej niż interwał sondowania wyzwalacza.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Wyzwalanie przesunięcia i dryfu cyklu

Wyzwalacze oparte na połączeniach, w przypadku których należy najpierw utworzyć połączenie, takie jak wyzwalacz SFTP-SSH, różnią się od wbudowanych wyzwalaczy uruchamianych natywnie w programie Azure Logic Apps, takich jak wyzwalacz [Cykl.](../connectors/connectors-native-recurrence.md) W przypadku wyzwalaczy cyklicznych opartych na połączeniu harmonogram cyklu nie jest jedynym sterownikem sterującym wykonywaniem, a strefa czasowa określa tylko początkowy czas rozpoczęcia. Kolejne przebiegi zależą od harmonogramu cyklu,  ostatniego wykonania wyzwalacza i innych czynników, które mogą powodować dryfowanie czasu wykonywania lub spowodować nieoczekiwane zachowanie. Na przykład nieoczekiwane zachowanie może obejmować niepowodzenie utrzymania określonego harmonogramu podczas uruchamiania i zakończenia czasu letniego (DST). Aby upewnić się, że czas cyklu nie zmienia się, gdy czas DST zajmuje się zmianą, ręcznie dostosuj cykl. Dzięki temu przepływ pracy będzie nadal działać w oczekiwanym czasie. W przeciwnym razie czas rozpoczęcia przesuwa się o godzinę do przodu, gdy rozpoczyna się czas DST, i o godzinę do tyłu po zakończeniu czasu DST. Aby uzyskać więcej informacji, zobacz [Cykl dla wyzwalaczy opartych na połączeniu.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konwertowanie klucza opartego na programie PuTTY na openSSH

Format PuTTY i OpenSSH używają różnych rozszerzeń nazw plików. Format PuTTY używa rozszerzenia nazwy pliku ppk lub klucza prywatnego PuTTY. Format OpenSSH używa rozszerzenia nazwy Privacy Enhanced Mail .pem. Jeśli klucz prywatny jest w formacie PuTTY i musisz użyć formatu OpenSSH, najpierw przekonwertuj klucz na format OpenSSH, wykonać następujące czynności:

### <a name="unix-based-os"></a>System operacyjny unix

1. Jeśli w systemie nie masz zainstalowanych narzędzi PuTTY, zrób to teraz, na przykład:

   `sudo apt-get install -y putty`

1. Uruchom to polecenie, aby utworzyć plik, którego można używać z łącznikiem SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Na przykład:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>System operacyjny Windows

1. Jeśli jeszcze tego nie zrobiono, pobierz najnowsze narzędzie [PuTTY Generator (puttygen.exe),](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)a następnie uruchom narzędzie.

1. Na tym ekranie wybierz pozycję **Załaduj**.

   ![Wybierz pozycję "Załaduj"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Przejdź do pliku klucza prywatnego w formacie PuTTY, a następnie wybierz **pozycję Otwórz**.

1. W menu **Konwersje** wybierz pozycję **Eksportuj klucz OpenSSH.**

   ![Wybierz pozycję "Eksportuj klucz OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Zapisz plik klucza prywatnego z `.pem` rozszerzeniem nazwy pliku.

## <a name="considerations"></a>Zagadnienia do rozważenia

W tej sekcji opisano zagadnienia, które należy wziąć pod uwagę podczas korzystania z wyzwalaczy i akcji tego łącznika.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Przekazywanie i przetwarzanie plików przy użyciu różnych folderów SFTP

Na serwerze SFTP użyj oddzielnych folderów do przechowywania przekazanych plików i do monitorowania tych plików w celu ich przetwarzania przez wyzwalacz. W przeciwnym razie wyzwalacz nie zostanie uruchomiony i będzie zachowywać się w sposób nieprzewidywalny, na przykład pomijając losową liczbę plików przetwarzanych przez wyzwalacz. Jednak to wymaganie oznacza, że potrzebny jest sposób przenoszenia plików między tymi folderami. 

Jeśli ten problem z wyzwalaczem występuje, usuń pliki z folderu monitorowanego przez wyzwalacz i użyj innego folderu do przechowywania przekazanych plików.

<a name="create-file"></a>

### <a name="create-file"></a>Tworzenie pliku

Aby utworzyć plik na serwerze SFTP, możesz użyć akcji SFTP-SSH **Utwórz** plik. Gdy ta akcja powoduje utworzenie pliku, Logic Apps automatycznie wywołuje serwer SFTP w celu uzyskania metadanych pliku. Jeśli jednak przeniesiesz nowo utworzony plik przed wywołaniem usługi Logic Apps w celu uzyskania metadanych, zostanie wyświetlony `404` komunikat o błędzie `'A reference was made to a file or folder which does not exist'` . Aby pominąć odczytywanie metadanych pliku po utworzeniu pliku, wykonaj kroki, aby dodać i ustawić właściwość Pobierz wszystkie [ **metadane** pliku na **wartość Nie.**](#file-does-not-exist)

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Nawiązywanie połączenia z sfTP przy użyciu protokołu SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do aplikacji [Azure Portal](https://portal.azure.com)i otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie została jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź `sftp ssh` wartość jako filtr. Z listy wyzwalaczy wybierz wyzwalacz, którego potrzebujesz.

   -lub-

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz **pozycję Nowy krok**. W polu wyszukiwania wprowadź `sftp ssh` wartość jako filtr. Na liście akcji wybierz akcję, której chcesz użyć.

   Aby dodać akcję między krokami, przesuń wskaźnik na strzałkę między krokami. Wybierz wyświetlony znak plus **+** (), a następnie wybierz **pozycję Dodaj akcję**.

1. Podaj szczegóły niezbędne do nawiązaniu połączenia.

   > [!IMPORTANT]
   >
   > Po wprowadzeniu klucza prywatnego SSH we właściwości klucza prywatnego **SSH** wykonaj dodatkowe kroki, które ułatwiają zapewnienie pełnej i poprawnej wartości tej właściwości. Nieprawidłowy klucz powoduje niepowodzenie połączenia.

   Chociaż można użyć dowolnego edytora tekstów, poniżej znajdują się przykładowe kroki, które pokazują, jak poprawnie skopiować i wkleić klucz przy użyciu Notepad.exe jako przykładu.

   1. Otwórz plik klucza prywatnego SSH w edytorze tekstów. W tych krokach jako przykładu jest używać Notatnika.

   1. W menu Edycja **Notatnika** wybierz pozycję **Zaznacz wszystko.**

   1. Wybierz **pozycję Edytuj**  >  **kopię**.

   1. W wyzwalaczu lub akcji SFTP-SSH wklej pełny skopiowany klucz we właściwości klucza prywatnego **SSH,** która obsługuje wiele wierszy.  **_Nie należy ręcznie wprowadzać ani edytować klucza_**.

1. Po zakończeniu wprowadzania szczegółów połączenia wybierz pozycję **Utwórz**.

1. Teraz podaj niezbędne szczegóły dla wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Zastąp rozmiar fragmentu

Aby zastąpić domyślne zachowanie adaptacyjne używane przez fragmentowanie, można określić stały rozmiar fragmentu od 5 MB do 50 MB.

1. W prawym górnym rogu akcji wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

   ![Otwieranie ustawień PROTOKOŁU SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. W **obszarze Content Transfer**(Transfer zawartości) we właściwości **Chunk size (Rozmiar** fragmentu) wprowadź wartość całkowitą z wartości `5` do , na `50` przykład: 

   ![Określanie rozmiaru fragmentu do użycia](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Po zakończeniu wybierz pozycję **Gotowe.**

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP — wyzwalacz SSH: po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz uruchamia przepływ pracy po dodaniu lub zmianie pliku na serwerze SFTP. Jako przykładowe kolejne akcje przepływ pracy może użyć warunku, aby sprawdzić, czy zawartość pliku spełnia określone kryteria. Jeśli zawartość spełnia warunek, akcja **Pobierz** zawartość SFTP-SSH może pobrać zawartość, a następnie inna akcja SFTP-SSH może umieścić ten plik w innym folderze na serwerze SFTP.

**Przykład przedsiębiorstwa:** ten wyzwalacz umożliwia monitorowanie folderu SFTP pod uwagę nowych plików reprezentujących zamówienia klientów. Następnie możesz użyć akcji SFTP-SSH, takiej jak Pobierz zawartość pliku, aby uzyskać zawartość zamówienia do dalszego przetwarzania i zapisać to zamówienie w bazie danych zamówień. 

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP — akcja SSH: uzyskiwanie zawartości pliku przy użyciu ścieżki

Ta akcja pobiera zawartość z pliku na serwerze SFTP, określając ścieżkę pliku. Możesz na przykład dodać wyzwalacz z poprzedniego przykładu i warunek, który zawartość pliku musi spełniać. Jeśli warunek ma wartość true, można uruchomić akcję, która pobiera zawartość.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

W tej sekcji opisano możliwe rozwiązania typowych błędów lub problemów.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>Błąd 504: "Próba połączenia nie powiodła się, ponieważ połączona strona nie odpowiedziała prawidłowo po upływie czasu lub połączenie nie powiodło się, ponieważ połączony host nie odpowiedział" lub "Żądanie do serwera SFTP zajęło więcej niż "00:00:30" sekund"

Ten błąd może wystąpić, gdy aplikacja logiki nie może pomyślnie nawiązać połączenia z serwerem SFTP. Przyczyny tego problemu mogą być różne, dlatego wypróbuj następujące opcje rozwiązywania problemów:

* Limit czasu połączenia wynosi 20 sekund. Sprawdź, czy serwer SFTP ma dobrą wydajność, a urządzenia pośrednie, takie jak zapory, nie dodają narzutu. 

* Jeśli masz ustawioną zaporę, upewnij się, że do listy zatwierdzonych dodasz adresy **IP** łącznika zarządzanego. Aby znaleźć adresy IP dla regionu aplikacji logiki, zobacz [Limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses)dla Azure Logic Apps .

* Jeśli ten błąd występuje sporadycznie, zmień ustawienie zasad Ponawianie w akcji SFTP-SSH na liczbę ponownych prób większą niż domyślna liczba ponownych prób. 

* Sprawdź, czy serwer SFTP ogranicza liczbę połączeń z każdego adresu IP. Jeśli limit istnieje, może być konieczne ograniczenie liczby współbieżnych wystąpień aplikacji logiki.

* Aby zmniejszyć koszt ustanowienia połączenia, w konfiguracji SSH serwera SFTP zwiększ właściwość [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) do około godziny.

* Przejrzyj dziennik serwera SFTP, aby sprawdzić, czy żądanie z aplikacji logiki dotarło do serwera SFTP. Aby uzyskać więcej informacji na temat problemu z łącznością, możesz również uruchomić śledzenie sieci na zaporze i serwerze SFTP.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>Błąd 404: "Zostało wykonane odwołanie do pliku lub folderu, który nie istnieje"

Ten błąd może wystąpić, gdy przepływ pracy tworzy plik na serwerze  SFTP za pomocą akcji Utwórz plik SFTP-SSH, ale natychmiast przenosi ten plik, zanim usługa Logic Apps będzie w stanie pobrać metadane pliku. Gdy przepływ pracy uruchamia **akcję Utwórz** plik, usługa Logic Apps automatycznie wywołuje serwer SFTP w celu uzyskania metadanych pliku. Jeśli jednak aplikacja logiki przenosi plik, usługa Logic Apps nie może już znaleźć pliku, więc zostanie wyświetlony `404` komunikat o błędzie.

Jeśli nie możesz uniknąć lub opóźnić przenoszenia pliku, możesz pominąć odczytywanie metadanych pliku po utworzeniu pliku, zamiast tego wykonać następujące kroki:

1. W akcji **Utwórz plik** otwórz listę Dodaj **nowy** parametr, wybierz właściwość Pobierz wszystkie **metadane** pliku i ustaw wartość **Nie.**

1. Jeśli te metadane będą potrzebne później, możesz użyć akcji **Pobierz metadane** pliku.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku swagger łącznika, zobacz stronę referencyjną [łącznika](/connectors/sftpwithssh/).

> [!NOTE]
> W przypadku aplikacji logiki w środowisku usługi integracji [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja tego łącznika oznaczona etykietą ISE wymaga fragmentowania, aby zamiast tego użyć limitów komunikatów [ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [innych Logic Apps łączników](../connectors/apis-list.md)
