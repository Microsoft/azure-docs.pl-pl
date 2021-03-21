---
title: Przewodnik rozwiązywania problemów Eksplorator usługi Azure Storage | Microsoft Docs
description: Omówienie technik debugowania dla Eksplorator usługi Azure Storage
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 15df9b38abe35fe3eefad2fa160e1c1f16fe7aa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439463"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage jest autonomiczną aplikacją, która ułatwia obsługę danych w usłudze Azure Storage w systemach Windows, macOS i Linux. Aplikacja może łączyć się z kontami magazynu hostowanymi na platformie Azure, w chmurach krajowych i usłudze Azure Stack.

Ten przewodnik zawiera podsumowanie rozwiązań dotyczących problemów, które są często spotykane w Eksplorator usługi Storage.

## <a name="azure-rbac-permissions-issues"></a>Problemy z uprawnieniami kontroli RBAC platformy Azure

Kontrola dostępu oparta na rolach na [platformie Azure](../../role-based-access-control/overview.md) pozwala uzyskać wysoce szczegółowe zarządzanie zasobami platformy Azure przez łączenie zestawów uprawnień z _rolami_. Poniżej przedstawiono niektóre strategie, które umożliwiają optymalną pracę z usługą Azure RBAC w Eksplorator usługi Storage.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Jak mogę uzyskać dostęp do moich zasobów w Eksplorator usługi Storage?

Jeśli masz problemy z uzyskaniem dostępu do zasobów magazynu za pomocą usługi Azure RBAC, być może nie masz przypisanych odpowiednich ról. W poniższych sekcjach opisano uprawnienia Eksplorator usługi Storage obecnie wymagane w celu uzyskania dostępu do zasobów magazynu. Jeśli nie masz pewności, czy masz odpowiednie role lub uprawnienia, skontaktuj się z administratorem konta platformy Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Odczyt: Lista/pobieranie kont magazynu" — problem z uprawnieniami

Musisz mieć uprawnienia do wyświetlania listy kont magazynu. Aby uzyskać to uprawnienie, musisz mieć przypisaną rolę _czytelnik_ .

#### <a name="list-storage-account-keys"></a>Wyświetl listę kluczy konta magazynu

Do uwierzytelniania żądań Eksplorator usługi Storage można także użyć kluczy konta. Dostęp do kluczy konta można uzyskać za pomocą bardziej zaawansowanych ról, takich jak rola _współautor_ .

> [!NOTE]
> Klucze dostępu udzielają nieograniczonych uprawnień osobom, które je przechowują. W związku z tym nie zalecamy przekazywania tych kluczy do użytkowników konta. Jeśli musisz odwołać klucze dostępu, możesz je wygenerować ponownie z [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role danych

Musisz mieć przypisaną co najmniej jedną rolę, która przyznaje dostęp do odczytu danych z zasobów. Jeśli na przykład chcesz wyświetlić lub pobrać obiekty blob, musisz mieć co najmniej rolę _czytnika danych obiektów blob magazynu_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Dlaczego do wyświetlania moich zasobów w Eksplorator usługi Storage jest potrzebna rola warstwy zarządzania?

Usługa Azure Storage ma dwie warstwy dostępu: _Zarządzanie_ i _dane_. Do subskrypcji i kont magazynu uzyskuje się dostęp za pomocą warstwy zarządzania. Do kontenerów, obiektów blob i innych zasobów danych uzyskuje się dostęp za pomocą warstwy danych. Jeśli na przykład chcesz uzyskać listę kont magazynu z platformy Azure, Wyślij żądanie do punktu końcowego zarządzania. Jeśli potrzebujesz listy kontenerów obiektów BLOB na koncie, Wyślij żądanie do odpowiedniego punktu końcowego usługi.

Role platformy Azure mogą przyznawać uprawnienia do zarządzania lub dostępu do warstwy danych. Rola czytelnik, na przykład, umożliwia dostęp tylko do odczytu do zasobów warstwy zarządzania.

Dokładnie mówiąc, rola czytelnika nie zapewnia żadnych uprawnień do warstwy danych i nie jest konieczna do uzyskania dostępu do warstwy danych.

Eksplorator usługi Storage ułatwia uzyskiwanie dostępu do zasobów, zbierając informacje niezbędne do nawiązania połączenia z zasobami platformy Azure. Na przykład aby wyświetlić kontenery obiektów blob, Eksplorator usługi Storage wysyła żądanie "list Containers" do punktu końcowego usługi BLOB Service. Aby uzyskać ten punkt końcowy, Eksplorator usługi Storage przeszukuje listę subskrypcji i kont magazynu, do których masz dostęp. Aby znaleźć subskrypcje i konta magazynu, Eksplorator usługi Storage wymaga również dostępu do warstwy zarządzania.

Jeśli nie masz roli przyznającej uprawnienia do warstwy zarządzania, Eksplorator usługi Storage nie może pobrać informacji potrzebnych do nawiązania połączenia z warstwą danych.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co zrobić, jeśli nie mogę uzyskać wymaganych uprawnień do warstwy zarządzania z mojego administratora?

Jeśli chcesz uzyskać dostęp do kontenerów obiektów blob, ADLS Gen2 kontenerów lub katalogów lub kolejek, możesz dołączyć do tych zasobów przy użyciu poświadczeń platformy Azure.

1. Otwórz okno dialogowe Połącz.
1. Wybierz typ zasobu, z którym chcesz nawiązać połączenie.
1. Wybierz pozycję **Zaloguj się przy użyciu Azure Active Directory (Azure AD)**. Wybierz opcję **Dalej**.
1. Wybierz konto użytkownika i dzierżawcę skojarzone z zasobem, do którego jest dołączane. Wybierz opcję **Dalej**.
1. Wprowadź adres URL zasobu, a następnie wprowadź unikatową nazwę wyświetlaną dla połączenia. Wybierz pozycję **dalej** , a następnie **Połącz**.

W przypadku innych typów zasobów nie ma obecnie rozwiązania dotyczącego kontroli RBAC platformy Azure. Jako obejście możesz zażądać adresu URL sygnatury dostępu współdzielonego, a następnie dołączyć do zasobu, wykonując następujące czynności:

1. Otwórz okno dialogowe Połącz.
1. Wybierz typ zasobu, z którym chcesz nawiązać połączenie.
1. Wybierz pozycję **sygnatura dostępu współdzielonego (SAS)**. Wybierz opcję **Dalej**.
1. Wprowadź otrzymany adres URL sygnatury dostępu współdzielonego i wprowadź unikatową nazwę wyświetlaną dla połączenia. Wybierz pozycję **dalej** , a następnie **Połącz**.
 
Aby uzyskać więcej informacji na temat dołączania do zasobów, zobacz [dołączanie do pojedynczego zasobu](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Zalecane role wbudowane platformy Azure

Istnieje kilka wbudowanych ról platformy Azure, które mogą zapewnić uprawnienia, które są konieczne do korzystania z Eksplorator usługi Storage. Niektóre z tych ról są następujące:
- [Właściciel](../../role-based-access-control/built-in-roles.md#owner): Zarządzanie wszystko, w tym dostęp do zasobów.
- [Współautor](../../role-based-access-control/built-in-roles.md#contributor): Zarządzaj wszystkimi elementami, wykluczając dostęp do zasobów.
- [Czytelnik](../../role-based-access-control/built-in-roles.md#reader): Odczytuj i wyświetlaj zasoby.
- [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor): pełne zarządzanie kontami magazynu.
- [Właściciel danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): pełny dostęp do kontenerów obiektów blob i danych usługi Azure Storage.
- [Współautor danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage.
- [Czytnik danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Odczytuj i wyświetlaj kontenery i obiekty blob usługi Azure Storage.

> [!NOTE]
> Role współautor właściciela, współautora i konta magazynu przyznają dostęp do klucza konta.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatów zwykle występują w jednej z następujących sytuacji:

- Aplikacja jest połączona za pomocą _przezroczystego serwera proxy_. Oznacza to, że serwer (na przykład serwer firmy) przechwytuje ruch HTTPS, odszyfrowuje go, a następnie szyfruje przy użyciu certyfikatu z podpisem własnym.
- Korzystasz z aplikacji, która wprowadza certyfikat TLS/SSL z podpisem własnym do odbieranych komunikatów HTTPS. Przykładami aplikacji, które wprowadzają certyfikaty, są oprogramowanie antywirusowe i program inspekcji ruchu sieciowego.

Gdy Eksplorator usługi Storage widzi certyfikat z podpisem własnym lub niezaufany, nie wie, czy odebrany komunikat HTTPS został zmieniony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz poinstruować Eksplorator usługi Storage, aby go ufa, wykonując następujące czynności:

1. Uzyskaj kopię certyfikatu X. 509 z kodowaniem Base-64 (CER).
2. Przejdź do pozycji **Edytuj**  >  **Certyfikaty SSL**  >  **Importuj certyfikaty**, a następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć plik. cer.

Ten problem może również wystąpić, jeśli istnieje wiele certyfikatów (głównych i pośrednich). Aby naprawić ten błąd, należy dodać oba certyfikaty.

Jeśli nie masz pewności, skąd pochodzi certyfikat, wykonaj następujące kroki, aby je znaleźć:

1. Zainstaluj OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): wszystkie wersje oświetlenia powinny być wystarczające.
    * Komputery Mac i Linux: powinny być dołączone do systemu operacyjnego.
2. Uruchom OpenSSL.
    * Windows: Otwórz katalog instalacji, wybierz pozycję **/bin/**, a następnie kliknij dwukrotnie przycisk **openssl.exe**.
    * Komputery Mac i Linux: uruchamianie `openssl` z poziomu terminalu.
3. Uruchom polecenie `s_client -showcerts -connect microsoft.com:443`.
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie masz pewności, które certyfikaty są z podpisem własnym, pamiętaj o tym, gdzie temat `("s:")` i wystawca `("i:")` są takie same.
5. Po znalezieniu certyfikatów z podpisem własnym dla każdej z nich skopiuj i Wklej wszystko z (i włącznie z `-----BEGIN CERTIFICATE-----` ) `-----END CERTIFICATE-----` do nowego pliku. cer.
6. Otwórz Eksplorator usługi Storage i przejdź do pozycji **Edytuj**  >  **Certyfikaty SSL**  >  **Importuj certyfikaty**. Następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć utworzone pliki CER.

Jeśli nie możesz znaleźć żadnych certyfikatów z podpisem własnym, wykonaj następujące kroki, aby skontaktować się z nami za pomocą narzędzia do przesyłania opinii. Możesz również otworzyć Eksplorator usługi Storage z wiersza polecenia przy użyciu `--ignore-certificate-errors` flagi. Po otwarciu z tą flagą Eksplorator usługi Storage ignoruje błędy certyfikatów.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="blank-sign-in-dialog-box"></a>Puste okno dialogowe logowania

Puste okna dialogowe logowania najczęściej występują, gdy Active Directory Federation Services (AD FS) poprosi Eksplorator usługi Storage o przeprowadzenie przekierowania, który jest nieobsługiwany przez elektron. Aby obejść ten problem, możesz spróbować użyć przepływu kodu urządzenia do logowania. W tym celu wykonaj następujące czynności:

1. Na pasku narzędzi po lewej stronie, Otwórz pozycję **Ustawienia**. W panelu Ustawienia przejdź do pozycji Logowanie do **aplikacji**  >  . Włącz **Logowanie za pomocą przepływu kodu urządzenia**.
2. Otwórz okno dialogowe **łączenie** (za pomocą ikony wtyczki na pasku pionowym po lewej stronie lub wybierając pozycję **Dodaj konto** w panelu konta).
3. Wybierz środowisko, do którego chcesz się zalogować.
4. Wybierz polecenie **Zaloguj się**.
5. Postępuj zgodnie z instrukcjami wyświetlanymi na następnym panelu.

Jeśli nie możesz zalogować się do konta, którego chcesz użyć, ponieważ domyślna przeglądarka została już zarejestrowana na innym koncie, wykonaj jedną z następujących czynności:

- Ręcznie skopiuj link i kod do prywatnej sesji przeglądarki.
- Ręcznie skopiuj link i kod do innej przeglądarki.

### <a name="reauthentication-loop-or-upn-change"></a>Pętla ponownego uwierzytelniania lub zmiana nazwy UPN

Jeśli jesteś w pętli ponownego uwierzytelniania lub zmieniono nazwę UPN jednego z kont, wykonaj następujące czynności:

1. Usuń wszystkie konta, a następnie zamknij Eksplorator usługi Storage.
2. Usuń. IdentityService z komputera. W systemie Windows folder znajduje się w folderze `C:\users\<username>\AppData\Local` . W przypadku systemów Mac i Linux można znaleźć folder w katalogu głównym katalogu użytkownika.
3. W przypadku korzystania z systemu Mac lub Linux należy również usunąć wpis Microsoft. developer. IdentityService z magazynu kluczy używanego przez system operacyjny. Na komputerze Mac magazyn kluczy jest aplikacją GNOME z *łańcucha* . W systemie Linux aplikacja jest zazwyczaj nazywana _dzwonkiem_, ale nazwa może się różnić w zależności od dystrybucji.

### <a name="conditional-access"></a>Dostęp warunkowy

Z powodu ograniczenia w bibliotece usługi Azure AD używanej przez Eksplorator usługi Storage dostęp warunkowy nie jest obsługiwany, gdy Eksplorator usługi Storage jest używany w systemie Windows 10, Linux lub macOS.

## <a name="mac-keychain-errors"></a>Błędy łańcucha kluczy Mac

MacOS pęku kluczy może czasami wprowadzić stan, który powoduje problemy z biblioteką uwierzytelniania Eksplorator usługi Storage. Aby uzyskać łańcuch kluczy z tego stanu, wykonaj następujące kroki:

1. Zamknij Eksplorator usługi Storage.
2. Otwórz pęku kluczy (naciśnij klawisze Command + Spacja, typ **pęku kluczy** i naciśnij klawisz ENTER).
3. Wybierz łańcuch kluczy logowania.
4. Wybierz ikonę kłódki, aby zablokować łańcucha kluczy. (Kłódka zostanie wyświetlona po zakończeniu procesu. Może to potrwać kilka sekund, w zależności od tego, jakie aplikacje zostały otwarte).

    ![Ikona kłódki](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Otwórz Eksploratora usługi Storage.
6. Zostanie wyświetlony monit z komunikatem "Usługa Service Hub chce uzyskać dostęp do łańcucha kluczy". Wprowadź hasło konta administratora dla komputerów Mac i wybierz opcję **zawsze Zezwalaj** (lub **Zezwalaj** , jeśli **zawsze Zezwalaj** jest niedostępne).
7. Spróbuj się zalogować.

### <a name="general-sign-in-troubleshooting-steps"></a>Ogólne kroki rozwiązywania problemów z logowaniem

* Jeśli jesteś w macOS, a okno logowania nigdy nie pojawia się w oknie dialogowym **oczekiwanie na uwierzytelnienie** , spróbuj wykonać [poniższe czynności](#mac-keychain-errors).
* Uruchom ponownie Eksplorator usługi Storage.
* Jeśli okno uwierzytelniania jest puste, odczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelnianie.
* Upewnij się, że ustawienia serwera proxy i certyfikatu zostały prawidłowo skonfigurowane dla komputera i Eksplorator usługi Storage.
* Jeśli używasz systemu Windows i masz dostęp do programu Visual Studio 2019 na tym samym komputerze i poświadczenia logowania, spróbuj zalogować się do programu Visual Studio 2019. Po pomyślnym zalogowaniu się do programu Visual Studio 2019 możesz otworzyć Eksplorator usługi Storage i zobaczyć swoje konto w panelu konta.

Jeśli żadna z tych metod nie zostanie zadziałała, [Otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Brakujące subskrypcje i uszkodzone dzierżawy

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu, wypróbuj następujące metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do oczekiwanych subskrypcji. Możesz sprawdzić dostęp, logując się do portalu dla środowiska platformy Azure, którego próbujesz użyć.
* Upewnij się, że zalogowano się za pomocą poprawnego środowiska platformy Azure (platformy Azure, platformy Azure z Chin, platformy Azure (Niemcy), platformy Azure dla instytucji rządowych USA lub środowiska niestandardowego.
* Jeśli jesteś za serwerem proxy, upewnij się, że poprawnie skonfigurowano serwer proxy Eksplorator usługi Storage.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli istnieje łącze "więcej informacji", sprawdź, które komunikaty o błędach są raportowane dla dzierżawców, które kończą się niepowodzeniem. Jeśli nie masz pewności, jak odpowiedzieć na komunikaty o błędach, możesz [otworzyć problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Nie można usunąć dołączonego konta lub zasobu magazynu

Jeśli nie można usunąć dołączonego konta lub zasobu magazynu za pomocą interfejsu użytkownika, można ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows: `%AppData%/StorageExplorer`
* MacOS `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Zamknij Eksplorator usługi Storage przed usunięciem tych folderów.

> [!NOTE]
> Jeśli kiedykolwiek zaimportowano wszystkie certyfikaty SSL, Utwórz kopię zapasową zawartości `certs` katalogu. Później można użyć kopii zapasowej do zaimportowania certyfikatów SSL.

## <a name="proxy-issues"></a>Problemy z serwerem proxy

Eksplorator usługi Storage obsługuje łączenie z zasobami usługi Azure Storage za pośrednictwem serwera proxy. Jeśli występują problemy z połączeniem z platformą Azure za pośrednictwem serwera proxy, poniżej przedstawiono kilka sugestii.

> [!NOTE]
> Eksplorator usługi Storage obsługuje tylko uwierzytelnianie podstawowe z serwerami proxy. Inne metody uwierzytelniania, takie jak NTLM, nie są obsługiwane.

> [!NOTE]
> Eksplorator usługi Storage nie obsługuje plików autokonfiguracji serwera proxy na potrzeby konfigurowania ustawień serwera proxy.

### <a name="verify-storage-explorer-proxy-settings"></a>Weryfikuj Eksplorator usługi Storage ustawienia serwera proxy

Ustawienie konfiguracji serwera proxy programu **Application → → proxy** określa, które źródło Eksplorator usługi Storage Pobiera konfigurację serwera proxy.

W przypadku wybrania opcji "Użyj zmiennych środowiskowych" Upewnij się, że ustawisz `HTTPS_PROXY` `HTTP_PROXY` zmienne środowiskowe lub (zmienne środowiskowe są rozróżniane wielkości liter, więc pamiętaj, aby ustawić prawidłowe zmienne). Jeśli te zmienne są niezdefiniowane lub nieprawidłowe, Eksplorator usługi Storage nie będą używać serwera proxy. Po zmodyfikowaniu zmiennych środowiskowych ponownie uruchom Eksplorator usługi Storage.

W przypadku wybrania opcji "Użyj ustawień serwera proxy aplikacji" Upewnij się, że ustawienia serwera proxy w aplikacji są poprawne.

### <a name="steps-for-diagnosing-issues"></a>Kroki umożliwiające diagnozowanie problemów

Jeśli nadal występują problemy, wypróbuj następujące metody rozwiązywania problemów:

1. Jeśli możesz nawiązać połączenie z Internetem bez użycia serwera proxy, sprawdź, czy Eksplorator usługi Storage działa bez włączonych ustawień serwera proxy. W przypadku pomyślnego nawiązania połączenia Eksplorator usługi Storage może wystąpić problem z serwerem proxy. Skontaktuj się z administratorem, aby zidentyfikować problemy.
2. Sprawdź, czy inne aplikacje korzystające z serwera proxy działają zgodnie z oczekiwaniami.
3. Sprawdź, czy możesz nawiązać połączenie z portalem dla środowiska platformy Azure, którego próbujesz użyć.
4. Sprawdź, czy można odbierać odpowiedzi z punktów końcowych usługi. Wprowadź jeden z adresów URL punktu końcowego do przeglądarki. Jeśli można nawiązać połączenie, należy otrzymać `InvalidQueryParameterValue` lub podobną odpowiedź XML.
5. Sprawdź, czy inna osoba korzystająca z Eksplorator usługi Storage z tym samym serwerem proxy może nawiązać połączenie. Jeśli to możliwe, może być konieczne skontaktowanie się z administratorem serwera proxy.

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Narzędzie sieciowe, takie jak programu Fiddler, może ułatwić diagnozowanie problemów.

1. Skonfiguruj narzędzie sieciowe jako serwer proxy uruchomiony na hoście lokalnym. Aby kontynuować pracę za rzeczywistym serwerem proxy, może być konieczne skonfigurowanie narzędzia sieci do łączenia się z serwerem proxy.
2. Sprawdź numer portu używany przez narzędzie sieciowe.
3. Skonfiguruj Eksplorator usługi Storage ustawienia serwera proxy, aby użyć hosta lokalnego i numeru portu Narzędzia sieciowego (na przykład "localhost: 8888").
 
Po poprawnym skonfigurowaniu narzędzia sieciowe będą rejestrować żądania sieci Eksplorator usługi Storage do punktów końcowych zarządzania i usług.
 
Jeśli narzędzie sieciowe nie wydaje się rejestrować Eksplorator usługi Storage ruchu, spróbuj przetestować narzędzie przy użyciu innej aplikacji. Na przykład wprowadź adres URL punktu końcowego dla jednego z zasobów magazynu (np `https://contoso.blob.core.windows.net/` .) w przeglądarce internetowej i otrzymasz odpowiedź podobną do następujących:

  ![Przykład kodu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Odpowiedź sugeruje, że zasób istnieje, nawet jeśli nie można uzyskać do niego dostępu.

Jeśli narzędzie sieciowe wyświetla tylko ruch z innych aplikacji, może być konieczne dostosowanie ustawień serwera proxy w Eksplorator usługi Storage. W przeciwnym razie konieczne jest dostosowanie ustawień narzędzia.

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, może być konieczne skontaktowanie się z administratorem serwera proxy w celu:

* Upewnij się, że serwer proxy nie blokuje ruchu sieciowego do zarządzania platformą Azure lub punktów końcowych zasobów.
* Sprawdź protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage obsługuje tylko protokoły uwierzytelniania podstawowego. Eksplorator usługi Storage nie obsługuje proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "nie można pobrać elementu podrzędnego"

Jeśli nawiązano połączenie z platformą Azure za pomocą serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne.

Jeśli właściciel subskrypcji lub konta udzielił Ci dostępu do zasobu, sprawdź, czy masz uprawnienia do odczytu lub listy dla tego zasobu.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Parametry połączenia nie mają pełnych ustawień konfiguracji

Jeśli zostanie wyświetlony komunikat o błędzie, istnieje możliwość, że nie masz wystarczających uprawnień, aby uzyskać klucze dla konta magazynu. Aby upewnić się, że jest to przypadek, przejdź do portalu i Znajdź swoje konto magazynu. Aby to zrobić, kliknij prawym przyciskiem myszy węzeł konta magazynu i wybierz polecenie **Otwórz w portalu**. Następnie przejdź do bloku **klucze dostępu** . Jeśli nie masz uprawnień do wyświetlania kluczy, zobaczysz komunikat "nie masz dostępu". Aby obejść ten problem, możesz uzyskać klucz konta od kogoś innego i dołączyć nazwę i klucz lub poprosił kogoś o sygnaturę dostępu współdzielonego z kontem magazynu i użyć go do dołączenia konta magazynu.

Jeśli widzisz klucze konta, w usłudze GitHub prosimy o problem, aby pomóc w rozwiązaniu problemu.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Wystąpił błąd podczas dodawania nowego połączenia: TypeError: nie można odczytać właściwości "wersja" niezdefiniowanej

Jeśli podczas próby dodania połączenia niestandardowego zostanie wyświetlony komunikat o błędzie, dane połączenia przechowywane w lokalnym Menedżerze poświadczeń mogą być uszkodzone. Aby obejść ten problem, spróbuj usunąć uszkodzone połączenia lokalne, a następnie dodaj je ponownie:

1. Rozpocznij Eksplorator usługi Storage. W menu Przejdź do pozycji **Pomoc** i  >  **Przełącz narzędzia deweloperskie**.
2. W otwartym oknie na karcie **aplikacja** przejdź do obszaru **Magazyn lokalny** (po lewej stronie) > **File://**.
3. W zależności od typu połączenia, z którym występuje problem, poszukaj jego klucza, a następnie skopiuj jego wartość do edytora tekstu. Wartość jest tablicą niestandardowych nazw połączeń, takich jak następujące:
    * Konta magazynu
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Kontenery obiektów blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Udziały plików
        * `StorageExplorer_CustomConnections_Files_v1`
    * Kolejki
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabele
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Po zapisaniu bieżących nazw połączeń ustaw wartość w Narzędzia deweloperskie na `[]` .

Aby zachować połączenia, które nie są uszkodzone, można użyć poniższych kroków w celu zlokalizowania uszkodzonych połączeń. Jeśli nie chcesz utracić wszystkich istniejących połączeń, możesz pominąć te kroki i postępować zgodnie z instrukcjami dotyczącymi konkretnej platformy, aby wyczyścić dane połączenia.

1. W edytorze tekstów ponownie Dodaj nazwy poszczególnych połączeń do Narzędzia deweloperskie, a następnie sprawdź, czy połączenie nadal działa.
2. Jeśli połączenie działa prawidłowo, nie jest uszkodzone i można je bezpiecznie pozostawić. Jeśli połączenie nie działa, usuń jego wartość z Narzędzia deweloperskie i Zapisz je tak, aby można było dodać je ponownie później.
3. Powtarzaj do momentu zbadania wszystkich połączeń.

Po przejściu przez wszystkie połączenia w przypadku wszystkich nazw połączeń, które nie zostały dodane z powrotem, należy wyczyścić ich uszkodzone dane (jeśli istnieją) i dodać je z powrotem przy użyciu standardowych kroków w Eksplorator usługi Storage:

# <a name="windows"></a>[Windows](#tab/Windows)

1. W menu **Start** Wyszukaj pozycję **Menedżer poświadczeń** i otwórz ją.
2. Przejdź do **poświadczeń systemu Windows**.
3. W obszarze **poświadczenia ogólne** Wyszukaj wpisy, które mają `<connection_type_key>/<corrupted_connection_name>` klucz (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Usuń te wpisy i Dodaj je jeszcze raz.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Otwórz centrum uwagi (Command + SPACEBAR) i Wyszukaj **dostęp do łańcucha kluczy**.
2. Wyszukaj wpisy, które mają `<connection_type_key>/<corrupted_connection_name>` klucz (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Usuń te wpisy i Dodaj je jeszcze raz.

# <a name="linux"></a>[Linux](#tab/Linux)

Lokalne zarządzanie poświadczeniami różni się w zależności od dystrybucji systemu Linux. Jeśli dystrybucja systemu Linux nie udostępnia wbudowanego interfejsu GUI do lokalnego zarządzania poświadczeniami, możesz zainstalować narzędzie innych firm, aby zarządzać poświadczeniami lokalnymi. Na przykład można użyć [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), narzędzia interfejsu GUI open source do zarządzania poświadczeniami lokalnymi systemu Linux.

1. Otwórz swoje lokalne narzędzie do zarządzania poświadczeniami i Znajdź zapisane poświadczenia.
2. Wyszukaj wpisy, które mają `<connection_type_key>/<corrupted_connection_name>` klucz (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Usuń te wpisy i Dodaj je jeszcze raz.
---

Jeśli ten błąd będzie nadal występował po wykonaniu tych kroków lub jeśli chcesz udostępnić podejrzane informacje, zapoznaj [się z informacjami na naszej](https://github.com/microsoft/AzureStorageExplorer/issues) stronie usługi GitHub.

## <a name="issues-with-sas-url"></a>Problemy z adresem URL SAS

Jeśli łączysz się z usługą za pomocą adresu URL sygnatury dostępu współdzielonego i wystąpił błąd:

* Sprawdź, czy adres URL zawiera niezbędne uprawnienia do odczytu lub wyświetlania zasobów.
* Sprawdź, czy adres URL nie wygasł.
* Jeśli adres URL SAS jest oparty na zasadach dostępu, sprawdź, czy zasady dostępu nie zostały odwołane.

Jeśli przypadkowo dołączono przy użyciu nieprawidłowego adresu URL sygnatury dostępu współdzielonego i teraz nie można odłączyć, wykonaj następujące kroki:

1. Gdy korzystasz z programu Eksplorator usługi Storage, naciśnij klawisz F12, aby otworzyć okno Narzędzia deweloperskie.
2. Na karcie **aplikacja** wybierz pozycję **Magazyn lokalny**  >  **File://** w drzewie po lewej stronie.
3. Znajdź klucz skojarzony z typem usługi problematycznego identyfikatora URI sygnatury dostępu współdzielonego. Na przykład jeśli zły identyfikator URI sygnatury dostępu współdzielonego dotyczy kontenera obiektów blob, poszukaj klucza o nazwie `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony z nieprawidłowym identyfikatorem URI, a następnie usuń go.
5. Naciśnij klawisze CTRL + R, aby ponownie załadować Eksplorator usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

### <a name="snap"></a>Przystawki

Eksplorator usługi Storage 1.10.0 i nowsze są dostępne jako Przyciągaj z magazynu Snap. Przystawka Eksplorator usługi Storage automatycznie instaluje wszystkie zależności i jest aktualizowana, gdy dostępna jest nowa wersja przyciągania. Zalecaną metodą instalacji jest zainstalowanie przystawki Eksplorator usługi Storage.

Eksplorator usługi Storage wymaga użycia Menedżera haseł, który może być konieczne nawiązanie połączenia ręcznie, zanim Eksplorator usługi Storage będzie działał poprawnie. Możesz połączyć Eksplorator usługi Storage z menedżerem haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>Plik. tar. gz

Możesz również pobrać aplikację jako plik tar. gz, ale musisz ręcznie zainstalować zależności.

Eksplorator usługi Storage, zgodnie z opisem w pobraniu plików. tar. gz, jest obsługiwana tylko dla następujących wersji programu Ubuntu. Eksplorator usługi Storage mogą korzystać z innych dystrybucji systemu Linux, ale nie są oficjalnie obsługiwane.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Eksplorator usługi Storage wymaga zainstalowania programu .NET Core w systemie. Zalecamy platformę .NET Core 2,1, ale Eksplorator usługi Storage również będzie działała z 2,2.

> [!NOTE]
> Eksplorator usługi Storage wersja 1.7.0 i wcześniejsze wymagają programu .NET Core 2,0. Jeśli masz zainstalowaną nowszą wersję programu .NET Core, musisz [zastosować poprawkę Eksplorator usługi Storage](#patching-storage-explorer-for-newer-versions-of-net-core). Jeśli korzystasz z programu Eksplorator usługi Storage 1.8.0 lub nowszego, musisz mieć co najmniej platformę .NET Core 2,1.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Pobierz plik Eksplorator usługi Storage. tar. gz.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Pobierz plik Eksplorator usługi Storage. tar. gz.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Pobierz plik Eksplorator usługi Storage. tar. gz.
2. Zainstaluj [środowisko uruchomieniowe programu .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Wiele bibliotek wymaganych przez Eksplorator usługi Storage są wstępnie zainstalowane z kanonicznymi instalacjami standardowymi Ubuntu. W środowiskach niestandardowych może brakować niektórych z tych bibliotek. Jeśli masz problemy z uruchamianiem Eksplorator usługi Storage, zalecamy upewnienie się, że następujące pakiety są zainstalowane w systemie:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg — narzędzia

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Stosowanie poprawek Eksplorator usługi Storage w przypadku nowszych wersji platformy .NET Core

W przypadku Eksplorator usługi Storage 1.7.0 lub starszych może być konieczne zainstalowanie wersji programu .NET Core używanej przez Eksplorator usługi Storage:

1. Pobierz wersję 1.5.43 z StreamJsonRpc [z narzędzia NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Poszukaj linku "Pobierz pakiet" po prawej stronie strony.
2. Po pobraniu pakietu zmień jego rozszerzenie z `.nupkg` na `.zip` .
3. Rozpakuj pakiet.
4. Otwórz folder `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Skopiuj `StreamJsonRpc.dll` do następujących lokalizacji w folderze Eksplorator usługi Storage:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Otwórz w Eksploratorze" z Azure Portal nie działa

Jeśli przycisk **Otwórz w Eksploratorze** na Azure Portal nie działa, upewnij się, że używasz zgodnej przeglądarki. Następujące przeglądarki zostały przetestowane pod kątem zgodności:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Następne kroki

Jeśli żadne z tych rozwiązań nie zadziałało, [Otwórz problem w usłudze GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Możesz to zrobić, wybierając przycisk **Zgłoś problem do serwisu GitHub** w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)