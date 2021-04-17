---
title: Eksplorator usługi Azure Storage rozwiązywania problemów z | Microsoft Docs
description: Omówienie technik debugowania dla Eksplorator usługi Azure Storage
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568543"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z Eksploratorem usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage jest autonomiczną aplikacją, która ułatwia obsługę danych w usłudze Azure Storage w systemach Windows, macOS i Linux. Aplikacja może łączyć się z kontami magazynu hostowanymi na platformie Azure, w chmurach krajowych i usłudze Azure Stack.

Ten przewodnik zawiera podsumowanie rozwiązań problemów, które często występują w Eksplorator usługi Storage.

## <a name="azure-rbac-permissions-issues"></a>Problemy z uprawnieniami RBAC platformy Azure

Kontrola dostępu oparta na rolach na platformie [Azure](../../role-based-access-control/overview.md) umożliwia bardzo szczegółowe zarządzanie dostępem do zasobów platformy Azure przez połączenie zestawów uprawnień w _role._ Oto kilka strategii zapewnienia optymalnego działania kontroli RBAC na platformie Azure w Eksplorator usługi Storage.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Jak mogę uzyskać dostęp do zasobów w Eksplorator usługi Storage?

Jeśli masz problemy z dostępem do zasobów magazynu za pośrednictwem kontroli dostępu na podstawie ról platformy Azure, być może nie masz przypisanych odpowiednich ról. W poniższych sekcjach opisano uprawnienia Eksplorator usługi Storage obecnie wymagane do uzyskania dostępu do zasobów magazynu. Jeśli nie masz pewności, czy masz odpowiednie role lub uprawnienia, skontaktuj się z administratorem konta platformy Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problem z uprawnieniami "Odczyt: lista/uzyskiwanie kont magazynu"

Musisz mieć uprawnienia do listy kont magazynu. Aby uzyskać to uprawnienie, musisz mieć przypisaną rolę _Czytelnik._

#### <a name="list-storage-account-keys"></a>Lista kluczy kont magazynu

Eksplorator usługi Storage można również używać kluczy kont do uwierzytelniania żądań. Dostęp do kluczy kont można uzyskać za pośrednictwem bardziej zaawansowanych ról, takich jak _rola Współautor._

> [!NOTE]
> Klucze dostępu przyznają nieograniczone uprawnienia wszystkim osobom, które je przechowuje. W związku z tym nie zaleca się, aby te klucze były dla użytkowników kont. Jeśli musisz odwołać klucze dostępu, możesz je ponownie wygenerować z Azure Portal [.](https://portal.azure.com/)

#### <a name="data-roles"></a>Role danych

Musisz mieć przypisaną co najmniej jedną rolę, która udziela dostępu do odczytu danych z zasobów. Jeśli na przykład chcesz wyświetlić lub pobrać obiekty blob, musisz mieć co najmniej rolę Czytelnik danych obiektu blob usługi _Storage._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Dlaczego potrzebuję roli warstwy zarządzania, aby wyświetlić moje zasoby w Eksplorator usługi Storage?

Usługa Azure Storage ma dwie warstwy dostępu: _zarządzanie_ i _dane._ Dostęp do subskrypcji i kont magazynu jest uzyskiwany za pośrednictwem warstwy zarządzania. Dostęp do kontenerów, obiektów blob i innych zasobów danych uzyskuje się za pośrednictwem warstwy danych. Jeśli na przykład chcesz uzyskać listę kont magazynu z platformy Azure, wyślij żądanie do punktu końcowego zarządzania. Jeśli chcesz wyświetlić listę kontenerów obiektów blob na koncie, wyślij żądanie do odpowiedniego punktu końcowego usługi.

Role platformy Azure mogą przyznać Ci uprawnienia do zarządzania lub dostępu do warstwy danych. Na przykład rola Czytelnik udziela dostępu tylko do odczytu do zasobów warstwy zarządzania.

Ściśle rzecz biorąc, rola Czytelnik nie zapewnia uprawnień warstwy danych i nie jest konieczna do uzyskania dostępu do warstwy danych.

Eksplorator usługi Storage ułatwia dostęp do zasobów przez zebranie informacji niezbędnych do nawiązania połączenia z zasobami platformy Azure. Aby na przykład wyświetlić kontenery obiektów blob, Eksplorator usługi Storage wysyła żądanie "kontenerów listy" do punktu końcowego usługi blob. Aby uzyskać ten punkt końcowy, Eksplorator usługi Storage przeszuka listę subskrypcji i kont magazynu, do których masz dostęp. Aby znaleźć subskrypcje i konta magazynu, Eksplorator usługi Storage również dostęp do warstwy zarządzania.

Jeśli nie masz roli, która przyznaje jakiekolwiek uprawnienia warstwy zarządzania, Eksplorator usługi Storage nie może uzyskać informacji wymaganych do nawiązania połączenia z warstwą danych.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co zrobić, jeśli nie mogę uzyskać wymaganych uprawnień warstwy zarządzania od administratora?

Jeśli chcesz uzyskać dostęp do kontenerów obiektów blob, ADLS Gen2, katalogów lub kolejek, możesz dołączyć do tych zasobów przy użyciu poświadczeń platformy Azure.

1. Otwórz okno dialogowe Łączenie.
1. Wybierz typ zasobu, z którym chcesz nawiązać połączenie.
1. Wybierz **pozycję Zaloguj się przy użyciu Azure Active Directory (Azure AD).** Wybierz opcję **Dalej**.
1. Wybierz konto użytkownika i dzierżawę skojarzone z zasobem, do których dołączasz. Wybierz opcję **Dalej**.
1. Wprowadź adres URL zasobu, a następnie wprowadź unikatową nazwę wyświetlaną połączenia. Wybierz **pozycję Dalej,** a następnie **pozycję Połącz.**

W przypadku innych typów zasobów nie mamy obecnie rozwiązania związanego z RBAC platformy Azure. Aby obejść ten problem, możesz zażądać adresu URL sygnatury dostępu współdzielonego, a następnie dołączyć go do zasobu, wykonać następujące kroki:

1. Otwórz okno dialogowe Łączenie.
1. Wybierz typ zasobu, z którym chcesz nawiązać połączenie.
1. Wybierz **pozycję Sygnatura dostępu współdzielonego (SAS).** Wybierz opcję **Dalej**.
1. Wprowadź otrzymany adres URL sygnatury dostępu współdzielonego i wprowadź unikatową nazwę wyświetlaną połączenia. Wybierz **pozycję Dalej,** a następnie **pozycję Połącz.**
 
Aby uzyskać więcej informacji na temat dołączania do zasobów, [zobacz Dołączanie do pojedynczego zasobu](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Zalecane wbudowane role platformy Azure

Istnieje kilka wbudowanych ról platformy Azure, które zapewniają uprawnienia wymagane do korzystania z Eksplorator usługi Storage. Niektóre z tych ról to:
- [Właściciel:](../../role-based-access-control/built-in-roles.md#owner)Zarządzaj wszystkim, w tym dostępem do zasobów.
- [Współautor:](../../role-based-access-control/built-in-roles.md#contributor)Zarządzaj wszystkim, z wyłączeniem dostępu do zasobów.
- [Czytelnik:](../../role-based-access-control/built-in-roles.md#reader)odczyt i lista zasobów.
- [Współautor konta magazynu:](../../role-based-access-control/built-in-roles.md#storage-account-contributor)pełne zarządzanie kontami magazynu.
- [Właściciel danych obiektu blob usługi Storage:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)pełny dostęp do kontenerów i danych obiektów blob usługi Azure Storage.
- [Współautor danych obiektu blob usługi Storage:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)odczyt, zapis i usuwanie kontenerów i obiektów blob usługi Azure Storage.
- [Czytelnik danych obiektów blob usługi Storage:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)odczyt i lista kontenerów i obiektów blob usługi Azure Storage.

> [!NOTE]
> Role Właściciel, Współautor i Współautor konta magazynu przyznają dostęp do klucza konta.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Błąd: Certyfikat z podpisem własnym w łańcuchu certyfikatów (i podobne błędy)

Błędy certyfikatu występują zwykle w jednej z następujących sytuacji:

- Aplikacja jest połączona za pośrednictwem _przezroczystego serwera proxy_. Oznacza to, że serwer (taki jak serwer firmowy) przechwytuje ruch HTTPS, odszyfrowuje go, a następnie szyfruje przy użyciu certyfikatu z podpisem własnym.
- Uruchamiasz aplikację, która wprowadza certyfikat TLS/SSL z podpisem własnym do odbieranych komunikatów HTTPS. Przykłady aplikacji, które wstrzykają certyfikaty, to oprogramowanie antywirusowe i oprogramowanie do inspekcji ruchu sieciowego.

Gdy Eksplorator usługi Storage certyfikat z podpisem własnym lub niezaufany, nie wie już, czy odebrany komunikat HTTPS został zmieniony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz poinstruować użytkowników, aby Eksplorator usługi Storage mu ufać, wykonać następujące kroki:

1. Uzyskaj kopię certyfikatu X.509 (cer) zakodowaną w formacie Base-64.
2. Przejdź do **tematu Edytowanie**  >  **certyfikatów SSL**  >  **Importuj certyfikaty,** a następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć plik cer.

Ten problem może również wystąpić, jeśli istnieje wiele certyfikatów (główny i pośredni). Aby naprawić ten błąd, należy dodać oba certyfikaty.

Jeśli nie masz pewności, skąd pochodzi certyfikat, wykonaj następujące kroki, aby go znaleźć:

1. Zainstaluj program OpenSSL.
    * [Windows:](https://slproweb.com/products/Win32OpenSSL.html)każda z wersji jasnych powinna być wystarczająca.
    * Systemy Mac i Linux: powinny być dołączone do systemu operacyjnego.
2. Uruchom program OpenSSL.
    * Windows: otwórz katalog instalacyjny, wybierz **pozycję /bin/**, a następnie kliknij dwukrotnie **openssl.exe**.
    * Komputery Mac i system Linux: `openssl` uruchom z terminalu.
3. Uruchom polecenie `s_client -showcerts -connect microsoft.com:443`.
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie masz pewności, które certyfikaty mają podpis własny, zanotuj, gdzie podmiot i wystawca `("s:")` `("i:")` są takie same.
5. Po odnalezieniu certyfikatów z podpisem własnym skopiuj i wklej wszystko z (i włącznie) do nowego `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` pliku cer.
6. Otwórz Eksplorator usługi Storage i przejdź do menu **Edytuj**  >  **certyfikaty SSL**  >  **Importuj certyfikaty.** Następnie użyj selektora plików, aby znaleźć, wybrać i otworzyć utworzone pliki cer.

Jeśli nie możesz znaleźć żadnych certyfikatów z podpisem własnym, korzystając z tych kroków, skontaktuj się z nami za pomocą narzędzia do opinii. Możesz również otworzyć Eksplorator usługi Storage wierszu polecenia przy użyciu `--ignore-certificate-errors` flagi . Po otwarciu przy użyciu tej flagi Eksplorator usługi Storage ignoruje błędy certyfikatu.

## <a name="sign-in-issues"></a>Problemy dotyczące logowania

### <a name="understanding-sign-in"></a>Opis logowania

Upewnij się, że przeczytasz [dokumentację Logowanie Eksplorator usługi Storage](./storage-explorer-sign-in.md) danych.

### <a name="frequently-having-to-reenter-credentials"></a>Często trzeba ponownie poświadczeń

Ponowne posiadanie poświadczeń jest najprawdopodobniej wynikiem zasad dostępu warunkowego ustawionych przez administratora usługi AAD. Gdy Eksplorator usługi Storage prośbę o ponowne wdjęcie poświadczeń z panelu konta, powinien zostać wyświetlony link **Szczegóły błędu.** Kliknij ten przycisk, aby zobaczyć, Eksplorator usługi Storage użytkownik prosi o ponowne wejśanie poświadczeń. Błędy zasad dostępu warunkowego, które wymagają ponownego wjechania poświadczeń, mogą wyglądać podobnie do tych:
- Token odświeżania wygasł...
- Aby uzyskać dostęp do...
- Z powodu zmiany konfiguracji wprowadzonej przez administratora...

Aby zmniejszyć częstotliwość ponownego logowania poświadczeń z powodu błędów, takich jak powyższe, musisz porozmawiać z administratorem usługi AAD.

### <a name="conditional-access-policies"></a>Zasady dostępu warunkowego

Jeśli masz zasady dostępu warunkowego, które muszą być spełnione dla Twojego konta, upewnij się, że używasz wartości Domyślna przeglądarka sieci **Web** dla ustawienia Zaloguj się **za** pomocą. Aby uzyskać informacje na temat tego ustawienia, zobacz [Zmienianie miejsca logowania.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Nie można uzyskać tokenu, dzierżawa jest odfiltrowana

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że nie można uzyskać tokenu, ponieważ dzierżawa jest odfiltrowana, oznacza to, że próbujesz uzyskać dostęp do zasobu, który znajduje się w dzierżawie, która została odfiltrowana. Aby odfiltrować dzierżawę, przejdź do **panelu** konta i upewnij się, że pole wyboru dzierżawy określonej w błędzie jest zaznaczone. Zapoznaj się z [tematem Zarządzanie kontami,](./storage-explorer-sign-in.md#managing-accounts) aby uzyskać więcej informacji na temat filtrowania dzierżaw w Eksplorator usługi Storage.

## <a name="authentication-library-failed-to-start-properly"></a>Poprawne uruchomienie biblioteki uwierzytelniania nie powiodło się

Jeśli podczas uruchamiania zostanie wyświetlony komunikat o błędzie informujący, że Eksplorator usługi Storage nie można prawidłowo uruchomić biblioteki uwierzytelniania, upewnij się, że środowisko instalacji spełnia [wszystkie wymagania wstępne.](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites) Nie spełnianie wymagań wstępnych jest najbardziej prawdopodobną przyczyną tego komunikatu o błędzie.

Jeśli uważasz, że środowisko instalacji spełnia wszystkie wymagania wstępne, otwórz [problem w witrynie GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues/new) Po otwarciu problemu upewnij się, że uwzględnij następujące informacje:
- Twój system operacyjny.
- Jakiej wersji Eksplorator usługi Storage, której próbujesz użyć.
- Jeśli sprawdzono wymagania wstępne.
- [Dzienniki uwierzytelniania](#authentication-logs) po nieudanym uruchomieniu Eksplorator usługi Storage. Pełne rejestrowanie uwierzytelniania jest automatycznie włączane po wystąpienia tego typu błędu.

### <a name="blank-window-when-using-integrated-sign-in"></a>Puste okno w przypadku korzystania ze zintegrowanego logowania

Jeśli wybrano użycie  zintegrowanego logowania i widzisz puste okno logowania, prawdopodobnie konieczne będzie przełączenie się na inną metodę logowania. Puste okna dialogowe logowania najczęściej występują, gdy serwer Active Directory Federation Services (ADFS) monituje Eksplorator usługi Storage o wykonanie przekierowania nieobsługiwanego przez firmę Electron.

Aby zmienić metodę logowania na inną, zmieniając ustawienie Zaloguj się **przy** użyciu w obszarze **Ustawienia**  >  **Logowanie**  >  **aplikacji.** Aby uzyskać informacje na temat różnych typów metod logowania, zobacz Zmienianie miejsca [logowania.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="reauthentication-loop-or-upn-change"></a>Pętla ponownego uwierzytelniania lub zmiana upn

Jeśli jesteś w pętli ponownego uwierzytelniania lub zmieniono nazwę UPN jednego z kont, spróbuj wykonać następujące kroki:

1. Otwórz Eksplorator usługi Storage
2. Przejdź do pomocy > resetowania
3. Upewnij się, że zaznaczono przynajmniej pole Uwierzytelnianie. Możesz usunąć zaznaczenie innych elementów, których nie chcesz resetować.
4. Kliknij przycisk Resetuj
5. Uruchom Eksplorator usługi Storage i spróbuj zalogować się ponownie.

Jeśli po zresetowaniu nadal masz problemy, spróbuj wykonać następujące czynności:

1. Otwórz Eksplorator usługi Storage
2. Usuń wszystkie konta, a następnie zamknij Eksplorator usługi Storage.
3. Usuń `.IdentityService` folder z komputera. W systemie Windows folder znajduje się w folderze `C:\users\<username>\AppData\Local` . W przypadku komputerów Mac i systemu Linux folder znajduje się w katalogu głównym katalogu użytkownika.
4. Jeśli używasz systemu Mac lub Linux, musisz również usunąć wpis Microsoft.Developer.IdentityService z magazynu kluczy systemu operacyjnego. Na komputerze Mac magazyn kluczy to *aplikacja DoB keychain.* W systemie Linux aplikacja jest zwykle nazywana _keyringiem,_ ale nazwa może się różnić w zależności od dystrybucji.
6. Uruchom Eksplorator usługi Storage i spróbuj zalogować się ponownie.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: błędy klucza lub brak okna logowania

W systemie macOS Keychain można czasami wprowadzić stan, który powoduje problemy z biblioteką Eksplorator usługi Storage uwierzytelniania. Aby wywrzeć ten stan, wykonaj następujące kroki:

1. Zamknij Eksplorator usługi Storage.
2. Otwórz keychain (naciśnij klawisze Command+Spacja, wpisz **keychain** i naciśnij klawisz Enter).
3. Wybierz klucz "login" (zaloguj się).
4. Wybierz ikonę kłódki, aby zablokować klucz. (Kłódka zostanie zablokowana po zakończeniu procesu. Może to potrwać kilka sekund w zależności od otwartych aplikacji.

    ![Ikona kłódki](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Otwórz Eksploratora usługi Storage.
6. Zostanie wyświetlony monit z komunikatem, taki jak "Centrum usług chce uzyskać dostęp do klucza". Wprowadź hasło konta administratora komputera Mac i wybierz pozycję **Zawsze zezwalaj** (lub **Zezwalaj,** **jeśli** zawsze zezwalaj nie jest dostępna).
7. Spróbuj się zalogować.

### <a name="default-browser-doesnt-open"></a>Domyślna przeglądarka nie jest otwierana

Jeśli przeglądarka domyślna nie zostanie otwarta podczas próby zalogowania się, wypróbuj wszystkie następujące techniki:
- Ponowne Eksplorator usługi Storage
- Otwórz przeglądarkę ręcznie przed rozpoczęciem logowania
- Spróbuj użyć **zintegrowanego logowania**, zobacz [Zmienianie](./storage-explorer-sign-in.md#changing-where-sign-in-happens) miejsca logowania, aby uzyskać instrukcje dotyczące tego, jak to zrobić.

### <a name="other-sign-in-issues"></a>Inne problemy z logowaniem

Jeśli żadne z powyższych nie dotyczy Twojego problemu z logowaniem lub jeśli nie rozwiążą one problemu z logowaniem, otwórz [problem w usłudze GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>Brakujące subskrypcje i uszkodzone dzierżawy

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu się, wypróbuj następujące metody rozwiązywania problemów:

* Sprawdź, czy Twoje konto ma dostęp do spodziewanych subskrypcji. Możesz zweryfikować swój dostęp, logując się do portalu dla środowiska platformy Azure, z którym próbujesz korzystać.
* Upewnij się, że zalogowano się za pośrednictwem poprawnego środowiska platformy Azure (Azure, Azure (Chiny) — 21Vianet, Azure Germany, Azure US Government lub Środowisko niestandardowe).
* Jeśli jesteś za serwerem proxy, upewnij się, że serwer proxy został poprawnie Eksplorator usługi Storage proxy.
* Spróbuj usunąć i ponownie dodać konto.
* Jeśli istnieje link "Więcej informacji" lub "Szczegóły błędu", sprawdź, które komunikaty o błędach są zgłaszane dla dzierżaw, które się nie ponoszą. Jeśli nie masz pewności, jak reagować na komunikaty o błędach, możesz otworzyć [problem w usłudze GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Nie można usunąć dołączonego konta magazynu lub zasobu

Jeśli nie możesz usunąć dołączonego konta lub zasobu magazynu za pomocą interfejsu użytkownika, możesz ręcznie usunąć wszystkie dołączone zasoby, usuwając następujące foldery:

* Windows: `%AppData%/StorageExplorer`
* Macos: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Zamknij Eksplorator usługi Storage przed usunięciem tych folderów.

> [!NOTE]
> Jeśli kiedykolwiek zaimportowano jakiekolwiek certyfikaty SSL, należy wrócić do kopii zapasowej zawartości `certs` katalogu. Później można użyć kopii zapasowej do ponownego zaimportowania certyfikatów SSL.

## <a name="proxy-issues"></a>Problemy z serwerem proxy

Eksplorator usługi Storage obsługuje łączenie się z zasobami usługi Azure Storage za pośrednictwem serwera proxy. Jeśli wystąpią problemy podczas nawiązywania połączenia z platformą Azure za pośrednictwem serwera proxy, poniżej znajdują się sugestie.

> [!NOTE]
> Eksplorator usługi Storage obsługuje tylko uwierzytelnianie podstawowe z serwerami proxy. Inne metody uwierzytelniania, takie jak NTLM, nie są obsługiwane.

> [!NOTE]
> Eksplorator usługi Storage nie obsługuje plików automatycznej konfiguracji serwera proxy do konfigurowania ustawień serwera proxy.

### <a name="verify-storage-explorer-proxy-settings"></a>Weryfikowanie Eksplorator usługi Storage serwera proxy

Ustawienie **konfiguracji serwera → proxy →** określa, z którego źródła Eksplorator usługi Storage pobiera konfigurację serwera proxy.

W przypadku wybrania opcji "Użyj zmiennych środowiskowych" upewnij się, że ustawiono zmienne środowiskowe lub (w zmiennych środowiskowych jest zróżnicowa wielkość liter, dlatego należy ustawić `HTTPS_PROXY` `HTTP_PROXY` poprawne zmienne). Jeśli te zmienne są niezdefiniowane lub nieprawidłowe Eksplorator usługi Storage nie będą używać serwera proxy. Uruchom Eksplorator usługi Storage po zmodyfikowaniu zmiennych środowiskowych.

Jeśli wybierzesz pozycję "Użyj ustawień serwera proxy aplikacji", upewnij się, że ustawienia serwera proxy w aplikacji są poprawne.

### <a name="steps-for-diagnosing-issues"></a>Kroki diagnozowania problemów

Jeśli nadal występują problemy, wypróbuj następujące metody rozwiązywania problemów:

1. Jeśli możesz nawiązać połączenie z Internetem bez użycia serwera proxy, sprawdź, czy Eksplorator usługi Storage działa bez włączonych ustawień serwera proxy. Jeśli Eksplorator usługi Storage pomyślnie nawiąże połączenie, może wystąpić problem z serwerem proxy. Aby zidentyfikować problemy, należy współpracować z administratorem.
2. Sprawdź, czy inne aplikacje, które korzystają z serwera proxy, działają zgodnie z oczekiwaniami.
3. Sprawdź, czy możesz nawiązać połączenie z portalem dla środowiska platformy Azure, z którym próbujesz korzystać.
4. Sprawdź, czy możesz otrzymywać odpowiedzi z punktów końcowych usługi. Wprowadź jeden z adresów URL punktu końcowego w przeglądarce. Jeśli możesz nawiązać połączenie, powinna zostać wyświetlony komunikat `InvalidQueryParameterValue` lub podobna odpowiedź XML.
5. Sprawdź, czy inna osoba korzystająca Eksplorator usługi Storage z tym samym serwerem proxy może nawiązać połączenie. Jeśli tak, być może trzeba będzie skontaktować się z administratorem serwera proxy.

### <a name="tools-for-diagnosing-issues"></a>Narzędzia do diagnozowania problemów

Narzędzie sieciowe, takie jak Fiddler, może pomóc w diagnozowaniu problemów.

1. Skonfiguruj narzędzie sieciowe jako serwer proxy uruchomiony na hoście lokalnym. Jeśli musisz kontynuować pracę za rzeczywistym serwerem proxy, może być trzeba skonfigurować narzędzie sieciowe do nawiązywania połączenia za pośrednictwem serwera proxy.
2. Sprawdź numer portu używany przez narzędzie sieciowe.
3. Skonfiguruj Eksplorator usługi Storage serwera proxy, aby używać hosta lokalnego i numeru portu narzędzia sieciowego (na przykład "localhost:8888").
 
Po prawidłowym skonfigurowaniu narzędzie sieciowe będzie rejestrować żądania sieciowe Eksplorator usługi Storage do punktów końcowych zarządzania i usługi.
 
Jeśli wydaje się, że narzędzie sieciowe nie jest Eksplorator usługi Storage ruchu sieciowego, spróbuj przetestować narzędzie za pomocą innej aplikacji. Na przykład wprowadź adres URL punktu końcowego dla jednego z zasobów magazynu (na przykład ) w przeglądarce internetowej, a otrzymasz `https://contoso.blob.core.windows.net/` odpowiedź podobną do:

  ![Przykład kodu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Odpowiedź sugeruje, że zasób istnieje, mimo że nie można uzyskać do niego dostępu.

Jeśli w narzędziu sieciowym jest tylko ruch z innych aplikacji, może być konieczne dostosowanie ustawień serwera proxy w Eksplorator usługi Storage. W przeciwnym razie trzeba było dostosować ustawienia narzędzia.

### <a name="contact-proxy-server-admin"></a>Skontaktuj się z administratorem serwera proxy

Jeśli ustawienia serwera proxy są poprawne, może być trzeba skontaktować się z administratorem serwera proxy w celu:

* Upewnij się, że serwer proxy nie blokuje ruchu do punktów końcowych zasobów lub zarządzania platformą Azure.
* Sprawdź protokół uwierzytelniania używany przez serwer proxy. Eksplorator usługi Storage obsługuje tylko podstawowe protokoły uwierzytelniania. Eksplorator usługi Storage nie obsługuje serwerów proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Komunikat o błędzie "Nie można pobrać dzieci"

Jeśli masz połączenie z platformą Azure za pośrednictwem serwera proxy, sprawdź, czy ustawienia serwera proxy są poprawne.

Jeśli właściciel subskrypcji lub konta przyznał Ci dostęp do zasobu, sprawdź, czy masz uprawnienia do odczytu lub listy dla tego zasobu.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Parametrów połączenia nie ma pełnych ustawień konfiguracji

Jeśli zostanie wyświetlony ten komunikat o błędzie, możliwe, że nie masz niezbędnych uprawnień do uzyskania kluczy dla konta magazynu. Aby potwierdzić, że tak jest, przejdź do portalu i znajdź swoje konto magazynu. W tym celu kliknij prawym przyciskiem myszy węzeł konta magazynu i wybierz polecenie **Otwórz w portalu.** Następnie przejdź do bloku **Klucze** dostępu. Jeśli nie masz uprawnień do wyświetlania kluczy, zostanie wyświetlony komunikat "Nie masz dostępu". Aby ominąć ten problem, możesz uzyskać klucz konta od innej osoby i dołączyć go za pomocą nazwy i klucza albo poprosić kogoś o sygnaturę dostępu współdzielonego do konta magazynu i użyć go do dołączenia konta magazynu.

Jeśli widzisz klucze kont, w usłudze GitHub możesz utworzyć plik problemu, abyśmy pomogli Rozwiązać ten problem.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Wystąpił błąd podczas dodawania nowego połączenia: TypeError: Nie można odczytać właściwości "version" niezdefiniowanych

Jeśli ten komunikat o błędzie zostanie wyświetlony podczas próby dodania połączenia niestandardowego, dane połączenia przechowywane w lokalnym menedżerze poświadczeń mogą być uszkodzone. Aby usunąć ten problem, spróbuj usunąć uszkodzone połączenia lokalne, a następnie dodaj je ponownie:

1. Uruchom Eksplorator usługi Storage. Z menu przejdź do **pozycji** Przełącz pomoc  >  **Narzędzia deweloperskie**.
2. W otwartej oknie na karcie **Aplikacja** przejdź do magazynu lokalnego **(po** lewej stronie) i > **file://**.
3. W zależności od typu połączenia, którego dotyczy problem, poszukaj jego klucza, a następnie skopiuj jego wartość do edytora tekstów. Wartość jest tablicą niestandardowych nazw połączeń, jak poniżej:
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

Jeśli chcesz zachować połączenia, które nie są uszkodzone, możesz użyć poniższych kroków, aby zlokalizować uszkodzone połączenia. Jeśli nie masz nic przeciwko utracie wszystkich istniejących połączeń, możesz pominąć te kroki i postępować zgodnie z instrukcjami dla danej platformy, aby wyczyścić dane połączenia.

1. W edytorze tekstów dodaj ponownie każdą nazwę połączenia do Narzędzia deweloperskie, a następnie sprawdź, czy połączenie nadal działa.
2. Jeśli połączenie działa prawidłowo, nie jest uszkodzone i można je bezpiecznie tam opuścić. Jeśli połączenie nie działa, usuń jego wartość z Narzędzia deweloperskie i zanotuj ją, aby można było dodać ją z powrotem później.
3. Powtarzaj te czynności do momentu zbadania wszystkich połączeń.

Po zakończeniu wszystkich połączeń dla wszystkich nazw połączeń, które nie zostały dodane ponownie, należy wyczyścić uszkodzone dane (jeśli istnieją) i dodać je ponownie, korzystając ze standardowych kroków w Eksplorator usługi Storage:

### <a name="windows"></a>[Windows](#tab/Windows)

1. W menu **Start** wyszukaj pozycję **Menedżer poświadczeń** i otwórz ją.
2. Przejdź do **witryny Poświadczenia systemu Windows.**
3. W **obszarze Poświadczenia ogólne** poszukaj wpisów, które mają klucz `<connection_type_key>/<corrupted_connection_name>` (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Usuń te wpisy i ponownie dodaj połączenia.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Otwórz usługę Spotlight (Command+Spacja) i wyszukaj dostęp **do klucza .**
2. Poszukaj wpisów, które mają `<connection_type_key>/<corrupted_connection_name>` klucz (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Usuń te wpisy i ponownie dodaj połączenia.

### <a name="linux"></a>[Linux](#tab/Linux)

Lokalne zarządzanie poświadczeniami różni się w zależności od dystrybucji systemu Linux. Jeśli dystrybucja systemu Linux nie zapewnia wbudowanego narzędzia graficznego interfejsu użytkownika do lokalnego zarządzania poświadczeniami, możesz zainstalować narzędzie innej firmy do zarządzania poświadczeniami lokalnymi. Na przykład można użyć [seahorse](https://wiki.gnome.org/Apps/Seahorse/), narzędzia graficznego interfejsu użytkownika typu open source do zarządzania poświadczeniami lokalnymi systemu Linux.

1. Otwórz lokalne narzędzie do zarządzania poświadczeniami i znajdź zapisane poświadczenia.
2. Poszukaj wpisów, które mają `<connection_type_key>/<corrupted_connection_name>` klucz (na przykład `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Usuń te wpisy i ponownie dodaj połączenia.
---

Jeśli ten błąd nadal występuje po uruchomieniu tych kroków lub jeśli chcesz podzielić się swoimi podejrzeniem, że połączenia zostały uszkodzone, otwórz [problem](https://github.com/microsoft/AzureStorageExplorer/issues) na naszej stronie usługi GitHub.

## <a name="issues-with-sas-url"></a>Problemy z adresem URL sygnatury dostępu współdzielonego

Jeśli łączysz się z usługą za pośrednictwem adresu URL sygnatury dostępu współdzielonego i występuje błąd:

* Sprawdź, czy adres URL zapewnia uprawnienia niezbędne do odczytu lub listy zasobów.
* Sprawdź, czy adres URL nie wygasł.
* Jeśli adres URL sygnatury dostępu współdzielonego jest oparty na zasadach dostępu, sprawdź, czy zasady dostępu nie zostały odwołane.

Jeśli adres URL sygnatury dostępu współdzielonego został przypadkowo dołączony przy użyciu nieprawidłowego adresu URL sygnatury dostępu współdzielonego, wykonaj następujące kroki:

1. Po uruchomieniu polecenia Eksplorator usługi Storage naciśnij klawisz F12, aby otworzyć Narzędzia deweloperskie okno.
2. Na karcie **Aplikacja** wybierz pozycję **Magazyn lokalny**  >  **file://** w drzewie po lewej stronie.
3. Znajdź klucz skojarzony z typem usługi problematycznego URI sygnatury dostępu współdzielonego. Jeśli na przykład zły interfejs URI sygnatury dostępu współdzielonego jest dla kontenera obiektów blob, poszukaj klucza o nazwie `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. Wartość klucza powinna być tablicą JSON. Znajdź obiekt skojarzony z złym identyfikatorem URI, a następnie usuń go.
5. Naciśnij klawisze Ctrl+R, aby ponownie załadować Eksplorator usługi Storage.

## <a name="linux-dependencies"></a>Zależności systemu Linux

### <a name="snap"></a>Przyciągaj

Eksplorator usługi Storage wersji 1.10.0 lub nowszej jest dostępna jako przystawka z magazynu przyciągania. Przystawka Eksplorator usługi Storage automatycznie instaluje wszystkie jej zależności i jest aktualizowana, gdy jest dostępna nowa wersja przystawki. Zalecaną metodą instalacji jest Eksplorator usługi Storage przystawki instalacji.

Eksplorator usługi Storage wymaga użycia menedżera haseł, co może wymagać ręcznego nawiązania połączenia, Eksplorator usługi Storage będzie działać prawidłowo. Możesz połączyć Eksplorator usługi Storage z menedżerem haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>Plik tar.gz

Aplikację możesz również pobrać jako plik tar.gz, ale musisz zainstalować zależności ręcznie.

Eksplorator usługi Storage jak podano w pliku do pobrania tar.gz, jest obsługiwany tylko w następujących wersjach systemu Ubuntu. Eksplorator usługi Storage mogą działać w innych dystrybucjach systemu Linux, ale nie są oficjalnie obsługiwane.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Eksplorator usługi Storage wymaga zainstalowania w systemie oprogramowania .NET Core. Zalecamy program .NET Core 2.1, ale Eksplorator usługi Storage również z 2.2.

> [!NOTE]
> Eksplorator usługi Storage wersji 1.7.0 i starszych wymagają programu .NET Core 2.0. Jeśli masz zainstalowaną nowszą wersję programu .NET Core, musisz zainstalować [poprawki](#patching-storage-explorer-for-newer-versions-of-net-core)Eksplorator usługi Storage . Jeśli używasz systemu Eksplorator usługi Storage 1.8.0 lub nowszego, musisz mieć co najmniej program .NET Core 2.1.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Pobierz plik Eksplorator usługi Storage tar.gz.
2. Zainstaluj środowisko [uruchomieniowe .NET Core:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Pobierz plik Eksplorator usługi Storage tar.gz.
2. Zainstaluj środowisko [uruchomieniowe .NET Core:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Pobierz plik Eksplorator usługi Storage tar.gz.
2. Zainstaluj środowisko [uruchomieniowe .NET Core:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Wiele bibliotek wymaganych przez Eksplorator usługi Storage jest wstępnie zainstalowanych ze standardowymi instalacjami systemu Ubuntu firmy Canonical. Niektóre z tych bibliotek mogą nie być w środowiskach niestandardowych. Jeśli masz problemy z uruchamianiem Eksplorator usługi Storage, zalecamy, aby w systemie zostały zainstalowane następujące pakiety:

- iproute2
- liba 2
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
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Stosowanie poprawek Eksplorator usługi Storage dla nowszej wersji programu .NET Core

W Eksplorator usługi Storage wersji 1.7.0 lub starszej może być konieczne stosowanie poprawek do wersji .NET Core używanej przez program Eksplorator usługi Storage:

1. Pobierz wersję 1.5.43 pakietu StreamJsonRpc [z pakietu NuGet.](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) Poszukaj linku "Pobierz pakiet" w prawej części strony.
2. Po pobraniu pakietu zmień jego rozszerzenie pliku z `.nupkg` na `.zip` .
3. Rozpakować pakiet.
4. Otwórz folder `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Skopiuj `StreamJsonRpc.dll` do następujących lokalizacji w Eksplorator usługi Storage folderze:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Otwórz w Eksploratorze" z Azure Portal nie działa

Jeśli przycisk **Otwórz w Eksploratorze** na Azure Portal nie działa, upewnij się, że używasz zgodnej przeglądarki. Następujące przeglądarki zostały przetestowane pod kątem zgodności:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Zbieranie dzienników

Gdy zgłaszasz problem w usłudze GitHub, możesz zostać poproszony o zebranie niektórych dzienników w celu zdiagnozowania problemu.

### <a name="storage-explorer-logs"></a>Eksplorator usługi Storage dzienników

Począwszy od wersji 1.16.0, Eksplorator usługi Storage rejestruje różne rzeczy w swoich dziennikach aplikacji. Możesz łatwo uzyskać dostęp do tych dzienników, klikając pozycję Help > Open Logs Directory (Otwórz katalog dzienników). Domyślnie program Eksplorator usługi Storage dzienniki z niskim poziomem szczegółowości. Aby zmienić poziom szczegółowości, dodaj zmienną środowiskową o nazwie `STG_EX_LOG_LEVEL` i dowolną z następujących wartości:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (poziom domyślny)
- `verbose`
- `debug`

Dzienniki są podzielone na foldery dla każdej Eksplorator usługi Storage, które uruchamiasz. W przypadku plików dziennika, które należy udostępnić, zaleca się umieścić je w archiwum zip z plikami z różnych sesji w różnych folderach.

### <a name="authentication-logs"></a>Dzienniki uwierzytelniania

W przypadku problemów związanych z logowaniem lub Eksplorator usługi Storage biblioteki uwierzytelniania najprawdopodobniej konieczne będzie zebranie dzienników uwierzytelniania. Dzienniki uwierzytelniania są przechowywane w:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- systemy macOS i Linux `~/.ServiceHub/logs`

Ogólnie rzecz biorąc, możesz wykonać następujące kroki, aby zebrać dzienniki:

1. Przejdź do ustawień > logowania i > pełne rejestrowanie uwierzytelniania. Jeśli Eksplorator usługi Storage nie można uruchomić z powodu problemu z biblioteką uwierzytelniania, zostanie to zrobione za Ciebie.
2. Zamknij Eksplorator usługi Storage.
1. Opcjonalne/zalecane: wyczyść istniejące dzienniki z `logs` folderu. Spowoduje to zmniejszenie ilości informacji, które należy do nas wysłać.
4. Otwórz Eksplorator usługi Storage i odtprodukuj problem
5. Zamknij Eksplorator usługi Storage
6. Spakuj zawartość `log` folderu.

### <a name="azcopy-logs"></a>Dzienniki programu AzCopy

Jeśli masz problemy z przesyłaniem danych, może być konieczne uzyskiwanie dzienników programu AzCopy. Dzienniki programu AzCopy można łatwo znaleźć za pomocą dwóch różnych metod:
- W przypadku nieudanych transferów nadal w dzienniku aktywności kliknij pozycję "Przejdź do pliku dziennika AzCopy"
- W przypadku transferów, które nie powiodły się w przeszłości, przejdź do folderu dzienników programu AzCopy. Ten folder można znaleźć w:
  - Windows: `C:\Users\<your username>\.azcopy`
  - MacOS i Linux '~/.azcopy

### <a name="network-logs"></a>Dzienniki sieciowe

W przypadku niektórych problemów należy podać dzienniki wywołań sieciowych wykonanych przez Eksplorator usługi Storage. W systemie Windows można to zrobić za pomocą programu Fiddler.

> [!NOTE]
> Ślady w programie Fiddler mogą zawierać hasła wprowadzone/wysłane w przeglądarce podczas zbierania danych śledzenia. Pamiętaj, aby przeczytać instrukcje dotyczące sposobu czyszczenia śledzenia w programie Fiddler. Nie należy przekazywać śladów narzędzia Fiddler do usługi GitHub. Zostanie ci poinformowana, gdzie można bezpiecznie wysłać ślad fiddlera.

Część 1. Instalowanie i konfigurowanie programu Fiddler

1. Instalowanie programu Fiddler
2. Uruchamianie programu Fiddler
3. Przejdź do opcji narzędzia > narzędzia
4. Kliknij kartę HTTPS
5. Upewnij się, że zaznaczono pole przechwyć połączenia CONNECTs i odszyfruj ruch HTTPS
6. Kliknij przycisk Akcje
7. Wybierz pozycję "Ufaj certyfikatowi głównemu", a następnie pozycję "Tak" w następnym oknie dialogowym
8. Ponownie kliknij przycisk Akcje
9. Wybierz pozycję "Eksportuj certyfikat główny na pulpit"
10. Przejdź do pulpitu
11. Znajdź plik FiddlerRoot.cer
12. Kliknij dwukrotnie, aby otworzyć
13. Przejdź do karty "Szczegóły"
14. Kliknij pozycję "Kopiuj do pliku..."
15. W kreatorze eksportu wybierz następujące opcje
    - Kodowany algorytm Base-64 X.509
    - W przypadku nazwy pliku wybierz pozycję Przeglądaj... do C:\Users \<your user dir> \AppData\Roaming\StorageExplorer\certs, a następnie możesz zapisać go jako dowolną nazwę pliku
16. Zamykanie okna certyfikatu
17. Uruchom Eksplorator usługi Storage
18. Przejdź do menu Edytuj > konfigurowanie serwera proxy
19. W oknie dialogowym wybierz pozycję "Użyj ustawień serwera proxy aplikacji", a następnie ustaw adres URL na i http://localhost port na 8888
20. Kliknij przycisk OK
21. Ponowne Eksplorator usługi Storage
22. W programie Fiddler powinny zacząć być `storageexplorer:` wyświetlane wywołania sieciowe z procesu

Część 2. Odtworzenie problemu
1. Zamknij wszystkie aplikacje inne niż Fiddler
2. Wyczyść dziennik programu Fiddler (ikona X w lewym górnym rogu w pobliżu menu Widok)
3. Opcjonalne/zalecane: pozwól, aby zestaw fiddler był ustawiony na kilka minut. Jeśli zostaną wyświetlone wywołania sieciowe, kliknij je prawym przyciskiem myszy i wybierz pozycję "Filtruj teraz" > <process name> "Ukryj"
4. Uruchom Eksplorator usługi Storage
5. Odtwórz problem
6. Kliknij pozycję > Zapisz > wszystkie sesje... zapisz w miejscu, o których nie zapomnisz
7. Zamknij centrum fiddler i Eksplorator usługi Storage

Część 3. Czyszczenie śledzenia fiddlera
1. Kliknij dwukrotnie ślad programu fiddler (plik .saz)
2. Naciśnij `ctrl`+`f`
3. W wyświetlonym oknie dialogowym upewnij się, że ustawiono następujące opcje: Wyszukaj = Żądania i odpowiedzi, Zbadaj = Nagłówki i treści
4. Wyszukaj hasła użyte podczas zbierania śledzenia fiddler, wszystkie wyróżnione wpisy, kliknij prawym przyciskiem myszy i wybierz polecenie Usuń > wybrane sesje
5. Jeśli na pewno wprowadzono hasła w przeglądarce podczas zbierania śladu, ale nie znajdziesz żadnych wpisów przy użyciu klawiszy ctrl+f i nie chcesz zmieniać haseł/haseł, które zostały użyte do innych kont, możesz pominąć wysyłanie do nas pliku .saz. Lepiej być bezpiecznym niż niestety. :)
6. Zapisz ślad ponownie pod nową nazwą
7. Opcjonalnie: usuń oryginalny ślad

## <a name="next-steps"></a>Następne kroki

Jeśli żadne z tych rozwiązań nie działa za Ciebie, możesz:
- Tworzenie biletu pomocy technicznej
- [Otwórz problem w usłudze GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues) Możesz to również zrobić, wybierając przycisk Zgłoś problem w usłudze **GitHub** w lewym dolnym rogu.

![Opinia](./media/storage-explorer-troubleshooting/feedback-button.PNG)