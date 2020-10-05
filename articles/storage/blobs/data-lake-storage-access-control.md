---
title: Omówienie kontroli dostępu w Azure Data Lake Storage Gen2 | Microsoft Docs
description: Zapoznaj się ze sposobem działania kontroli dostępu w Azure Data Lake Storage Gen2. Obsługiwane są listy kontroli dostępu opartej na rolach (Azure RBAC) i oparte na systemie POSIX.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 31d67daebf2e15fb11b5ebe30c4f7741a09eed2d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716102"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Kontrola dostępu w usłudze Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementuje model kontroli dostępu, który obsługuje zarówno kontrolę dostępu opartą na rolach (Azure RBAC), jak i oparte na systemie POSIX listy kontroli dostępu (ACL). W tym artykule zestawiono podstawowe informacje o modelu kontroli dostępu dla Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac"></a>

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Funkcja RBAC platformy Azure używa przypisań ról do efektywnego stosowania zestawów uprawnień do *podmiotów zabezpieczeń*. *Podmiot zabezpieczeń* to obiekt, który reprezentuje użytkownika, grupę, jednostkę usługi lub tożsamość zarządzaną zdefiniowaną w Azure Active Directory (AD), która żąda dostępu do zasobów platformy Azure.

Zazwyczaj te zasoby platformy Azure są ograniczone do zasobów najwyższego poziomu (na przykład: konta usługi Azure Storage). W przypadku usługi Azure Storage, w Azure Data Lake Storage Gen2 związku z czym ten mechanizm został rozszerzony do zasobu kontenera (systemu plików).

Aby dowiedzieć się, jak przypisać role do podmiotów zabezpieczeń w zakresie konta magazynu, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Użytkownik-Gość nie może utworzyć przypisania roli.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Wpływ przypisań ról na listy kontroli dostępu na poziomie plików i katalogów

Podczas korzystania z przypisań ról platformy Azure jest zaawansowanym mechanizmem kontrolowania uprawnień dostępu, jest to bardzo gruby mechanizm względem list ACL. Najmniejszy poziom szczegółowości dla usługi Azure RBAC jest na poziomie kontenera i zostanie on oceniony z wyższym priorytetem niż listy kontroli dostępu. W związku z tym, Jeśli rola jest przypisywana do podmiotu zabezpieczeń w zakresie kontenera, ten podmiot zabezpieczeń ma poziom autoryzacji skojarzony z tą rolą dla wszystkich katalogów i plików w tym kontenerze, niezależnie od przypisań listy ACL.

Gdy podmiot zabezpieczeń otrzymuje uprawnienia do danych RBAC platformy Azure za pomocą [wbudowanej roli](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)lub przez rolę niestandardową, te uprawnienia są oceniane w pierwszej kolejności przy autoryzacji żądania. Jeśli żądana operacja jest autoryzowana przez przydziały roli platformy Azure podmiotu zabezpieczeń, autoryzacja jest natychmiast rozwiązywana i nie są wykonywane żadne dodatkowe sprawdzenia listy ACL. Alternatywnie, jeśli podmiot zabezpieczeń nie ma przypisania roli platformy Azure lub operacja żądania nie jest zgodna z przypisanym uprawnieniem, sprawdzenia listy ACL są wykonywane w celu ustalenia, czy podmiot zabezpieczeń jest autoryzowany do wykonywania żądanych operacji.

> [!NOTE]
> Jeśli podmiotowi zabezpieczeń przypisano przypisanie wbudowanej roli właściciela danych obiektu blob magazynu, podmiot zabezpieczeń jest traktowany jako *administrator* i uzyskuje pełny dostęp do wszystkich operacji związanych z operacjami, takich jak Ustawianie właściciela katalogu lub pliku oraz list ACL dla katalogów i plików, dla których nie są właścicielami. Dostęp administratora jest jedynym autoryzowanym sposobem zmiany właściciela zasobu.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Uwierzytelnianie klucza wspólnego i sygnatury dostępu współdzielonego (SAS)

Azure Data Lake Storage Gen2 obsługuje metody uwierzytelniania klucza współużytkowanego i SYGNATURy dostępu współdzielonego. Charakterystyka tych metod uwierzytelniania polega na tym, że żadna tożsamość nie jest skojarzona z obiektem wywołującym i dlatego nie można wykonać autoryzacji podmiotu zabezpieczeń na podstawie uprawnień.

W przypadku klucza współużytkowanego, obiekt wywołujący skutecznie uzyskuje dostęp "administratora", co oznacza pełny dostęp do wszystkich operacji na wszystkich zasobach, w tym Ustawianie właściciela i zmienianie list ACL.

Tokeny sygnatury dostępu współdzielonego zawierają dozwolone uprawnienia jako część tokenu. Uprawnienia zawarte w tokenie SAS są skutecznie stosowane do wszystkich decyzji dotyczących autoryzacji, ale nie są wykonywane żadne dodatkowe sprawdzenia listy ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Listy kontroli dostępu do plików i katalogów

Podmiot zabezpieczeń można skojarzyć z poziomem dostępu dla plików i katalogów. Te skojarzenia są przechwytywane z *listy kontroli dostępu (ACL)*. Każdy plik i katalog na koncie magazynu ma listę kontroli dostępu.

> [!NOTE]
> Listy ACL dotyczą tylko podmiotów zabezpieczeń w tej samej dzierżawie. 

Jeśli przypisano rolę do podmiotu zabezpieczeń na poziomie konta magazynu, można użyć list kontroli dostępu, aby przyznać temu podmiotowi zabezpieczeń podwyższony poziom dostępu do określonych plików i katalogów.

Nie można użyć list kontroli dostępu w celu zapewnienia poziomu dostępu, który jest niższy niż poziom przyznany przez przypisanie roli. Na przykład, Jeśli przypiszesz rolę [współautor danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) do podmiotu zabezpieczeń, nie można używać list kontroli dostępu, aby zapobiec zapisywaniu tego podmiotu zabezpieczeń w katalogu.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Ustawianie uprawnień na poziomie plików i katalogów przy użyciu list kontroli dostępu

Aby ustawić uprawnienia na poziomie plików i katalogów, zobacz dowolny z następujących artykułów:

| Środowisko | Artykuł |
|--------|-----------|
|Eksplorator usługi Azure Storage |[Zarządzanie katalogami, plikami i listami ACL w usłudze Azure Data Lake Storage Gen2 za pomocą Eksploratora usługi Azure Storage](data-lake-storage-explorer.md#managing-access)|
|.NET |[Użyj programu .NET do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Używanie języka Java do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Używanie języka Python do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Użyj programu PowerShell do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Interfejs wiersza polecenia platformy Azure|[Korzystanie z interfejsu wiersza polecenia platformy Azure do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|Interfejs API REST |[Ścieżka — aktualizacja](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Jeśli podmiot zabezpieczeń jest jednostką *usługi* , ważne jest użycie identyfikatora obiektu nazwy głównej usługi, a nie identyfikatora obiektu powiązanej rejestracji aplikacji. Aby uzyskać identyfikator obiektu jednostki usługi, Otwórz interfejs wiersza polecenia platformy Azure, a następnie użyj następujące polecenie: `az ad sp show --id <Your App ID> --query objectId` . Pamiętaj, aby zastąpić `<Your App ID>` symbol zastępczy identyfikatorem aplikacji rejestracji aplikacji.

### <a name="types-of-access-control-lists"></a>Typy list kontroli dostępu

Istnieją dwa rodzaje list kontroli dostępu: *listy ACL dostępu* i *domyślne listy ACL*.

Listy ACL dostępu kontrolują dostęp do obiektu. Pliki i katalogi mają osobne listy ACL dostępu.

Domyślne listy ACL są szablonami list ACL skojarzonych z katalogiem, który określa listy ACL dostępu dla wszelkich elementów podrzędnych, które są tworzone w tym katalogu. Pliki nie mają domyślnych list ACL.

Listy ACL dostępu i domyślne listy ACL mają tę samą strukturę.

> [!NOTE]
> Zmiana domyślnej listy ACL w obiekcie nadrzędnym nie ma wpływu na listę ACL dostępu ani domyślną listę ACL elementów podrzędnych, które już istnieją.

### <a name="levels-of-permission"></a>Poziomy uprawnień

Uprawnienia do obiektu kontenera są **odczytywane**, **zapisywane**i **wykonywane**oraz mogą być używane dla plików i katalogów, jak pokazano w poniższej tabeli:

|            |    Plik     |   Katalog |
|------------|-------------|----------|
| **Odczyt (R)** | Może odczytywać zawartości pliku | Wymaga **odczytu** i **wykonania** w celu wyświetlenia listy zawartości katalogu |
| **Zapis (W)** | Może zapisywać w pliku lub dołączać do pliku | Wymaga funkcji **Write** i **Execute** do tworzenia elementów podrzędnych w katalogu |
| **Wykonanie (X)** | Nie oznacza wszystkiego w kontekście Data Lake Storage Gen2 | Wymagane do przechodzenia między elementami podrzędnymi katalogu |

> [!NOTE]
> Jeśli udzielasz uprawnień przy użyciu tylko list ACL (bez kontroli dostępu Azure), a następnie chcesz udzielić podmiotu zabezpieczeń uprawnienia do odczytu lub zapisu do pliku, musisz udzielić podmiotu zabezpieczeń uprawnień do **wykonania** dla kontenera oraz do każdego folderu w hierarchii folderów, które prowadzą do pliku.

#### <a name="short-forms-for-permissions"></a>Krótkie formy uprawnień

Skrót **RWX** służy do wskazywania uprawnień do **odczytu, zapisu i wykonania**. Istnieje bardziej skondensowana postać liczbowa, w której uprawnienia do **odczytu = 4**, **zapisu = 2** i **wykonania = 1**, a ich suma reprezentuje uprawnienia. Poniżej przedstawiono kilka przykładów.

| Forma liczbowa | Forma krótka |      Co to oznacza     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Odczyt (R) + zapis (W) + wykonanie (X) |
| 5            | `R-X`        | Odczyt (R) + wykonanie (X)         |
| 4            | `R--`        | Odczyt                   |
| 0            | `---`        | Brak uprawnień         |

#### <a name="permissions-inheritance"></a>Dziedziczenie uprawnień

W modelu w stylu POSIX, który jest używany przez Data Lake Storage Gen2, uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy, uprawnienia dla elementu nie mogą być dziedziczone z elementów nadrzędnych, jeśli uprawnienia są ustawione po utworzeniu elementu podrzędnego. Uprawnienia są dziedziczone tylko wtedy, gdy dla elementów nadrzędnych zostały ustawione uprawnienia domyślne przed utworzeniem elementów podrzędnych.

### <a name="common-scenarios-related-to-permissions"></a>Typowe scenariusze dotyczące uprawnień

W poniższej tabeli przedstawiono niektóre typowe scenariusze, które ułatwiają zrozumienie, które uprawnienia są potrzebne do wykonania niektórych operacji na koncie magazynu.

|    Operacja             |    /    | Oregon | Biura | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Odczytaj Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Dołącz do Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Usuń Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Utwórz Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Staw                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| /Oregon/listy           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| /Oregon/Portland/listy  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Uprawnienia do zapisu w pliku nie są wymagane do usunięcia go, o ile poprzednie dwa warunki są spełnione.

### <a name="users-and-identities"></a>Użytkownicy i tożsamości

Każdy plik i katalog ma odrębne uprawnienia dla tych tożsamości:

- Użytkownik będący właścicielem
- Grupa będąca właścicielem
- Użytkownicy nazwani
- Grupy nazwane
- Nazwy główne usługi
- Nazwane tożsamości zarządzane
- Wszyscy pozostali użytkownicy

Tożsamości użytkowników i grup są tożsamościami usługi Azure Active Directory (Azure AD). O ile nie zaznaczono inaczej, *użytkownik*, w kontekście Data Lake Storage Gen2, może odwoływać się do użytkownika usługi Azure AD, nazwy głównej usługi, tożsamości zarządzanej lub grupy zabezpieczeń.

#### <a name="the-owning-user"></a>Użytkownik będący właścicielem

Użytkownik, który utworzył element, jest automatycznie właścicielem elementu. Użytkownik będący właścicielem może:

* zmieniać uprawnienia dla pliku, którego jest właścicielem;
* zmieniać grupę będącą właścicielem dla pliku, którego jest właścicielem, jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej.

> [!NOTE]
> Użytkownik będący właścicielem *nie może* zmienić użytkownika będącego właścicielem pliku lub katalogu. Tylko Administratorzy mogą zmienić użytkownika będącego właścicielem pliku lub katalogu.

#### <a name="the-owning-group"></a>Grupa będąca właścicielem

Na listach kontroli dostępu POSIX każdy użytkownik jest skojarzony z *grupą podstawową*. Przykładowo użytkownik "Alicja" może należeć do grupy "Finanse". Alicja może także należeć do wielu grup, ale jedna grupa jest zawsze wyznaczono jako grupę podstawową. W modelu POSIX, gdy Alicja tworzy plik, na grupę będącą właścicielem tego pliku zostaje ustawiona jej grupa główna. W tym przypadku jest to grupa „Finanse”. Grupa będąca właścicielem w przeciwnym razie działa podobnie do przypisanych uprawnień dla innych użytkowników/grup.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Przypisywanie grupy będącej właścicielem dla nowego pliku lub katalogu

* **Przypadek 1**: katalog główny "/". Ten katalog jest tworzony podczas tworzenia kontenera Data Lake Storage Gen2. W takim przypadku grupa będąca właścicielem jest ustawiana na użytkownika, który utworzył kontener, jeśli został on wykonany przy użyciu protokołu OAuth. Jeśli kontener jest tworzony przy użyciu klucza współużytkowanego, SYGNATURy dostępu współdzielonego konta lub SYGNATURy dostępu współdzielonego usługi, właściciel i grupa będąca właścicielem są ustawiani na **$superuser**.
* **Przypadek 2** (każdy inny przypadek): gdy tworzony jest nowy element, grupa będąca właścicielem jest kopiowana z katalogu nadrzędnego.

##### <a name="changing-the-owning-group"></a>Zmiana grupy będącej właścicielem

Grupę będącą właścicielem może zmienić:
* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

> [!NOTE]
> Grupa będąca właścicielem nie może zmienić list kontroli dostępu do pliku lub katalogu.  Grupa będąca właścicielem jest ustawiana na użytkownika, który utworzył konto w przypadku katalogu głównego, w **przypadku 1** powyżej, jedno konto użytkownika jest nieprawidłowe w przypadku podawania uprawnień za pośrednictwem grupy będącej właścicielem. Możesz przypisać te uprawnienia do prawidłowej grupy użytkowników, jeśli ma to zastosowanie.

### <a name="access-check-algorithm"></a>Algorytm kontroli dostępu

Następujący pseudokodzie reprezentuje algorytm kontroli dostępu dla kont magazynu.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
mask = get_mask( path )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    if ((desired_perms & entry.permissions & mask) == desired_perms)
        return True 
        
# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Maska

Jak pokazano w algorytmie kontroli dostępu, maska ogranicza dostęp dla użytkowników nazwanych, grupy będącej właścicielem i nazwanych grup.  

> [!NOTE]
> W przypadku nowego kontenera Data Lake Storage Gen2 maska listy ACL dostępu katalogu głównego ("/") domyślnie 750 dla katalogów i 640 dla plików. Pliki nie otrzymują bitów X, ponieważ nie są one istotne dla plików w systemie tylko do magazynowania.
>
> Maskę można określić dla każdego wywołania. Dzięki temu różne systemy zużywające, takie jak klastry, mają różne efektywne maski dla operacji na plikach. Jeśli Maska jest określona dla danego żądania, całkowicie zastępuje domyślną maskę.

#### <a name="the-sticky-bit"></a>Atrybut sticky bit

Bit programu Sticky to bardziej zaawansowaną funkcję kontenera POSIX. W kontekście Data Lake Storage Gen2 jest mało prawdopodobne, że będzie wymagany bit. W podsumowaniu, jeśli w katalogu jest włączony bit programu Sticky, element podrzędny może zostać usunięty lub zmieniony tylko przez użytkownika będącego właścicielem elementu podrzędnego.

Bit programu Sticky nie jest widoczny w Azure Portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Domyślne uprawnienia do nowych plików i katalogów

Po utworzeniu nowego pliku lub katalogu w istniejącym katalogu, domyślna lista ACL w katalogu nadrzędnym określa:

- Domyślna lista ACL i dostęp do listy ACL katalogu podrzędnego.
- Lista ACL dostępu pliku podrzędnego (pliki nie mają domyślnej listy ACL).

#### <a name="umask"></a>umask

Podczas tworzenia pliku lub katalogu maska umask jest używany do modyfikowania sposobu ustawiania domyślnych list ACL dla elementu podrzędnego. Maska umask jest wartością 9-bitową w katalogach nadrzędnych, które zawierają wartość RWX dla **użytkownika będącego właścicielem**, **grupy będącej właścicielem**i **innych**.

Maska umask dla Azure Data Lake Storage Gen2 stałą wartość ustawioną na 007. Ta wartość tłumaczy na:

| składnik maska umask     | Forma liczbowa | Forma krótka | Znaczenie |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | W przypadku użytkownika będącego właścicielem Skopiuj domyślną listę ACL elementu nadrzędnego do listy ACL dostępu elementu podrzędnego. | 
| umask.owning_group  |    0         |   `---`      | W przypadku grupy będącej właścicielem Skopiuj domyślną listę ACL elementu nadrzędnego do listy ACL dostępu do elementu podrzędnego. | 
| Maska umask. other         |    7         |   `RWX`      | W przypadku innych Usuń wszystkie uprawnienia na liście ACL dostępu dziecka |

Wartość maska umask używana Azure Data Lake Storage Gen2 efektywnie oznacza, że wartość dla **innych** nigdy nie jest domyślnie przekazywana w nowych elementach podrzędnych, chyba że domyślna lista ACL nie jest zdefiniowana w katalogu nadrzędnym. W takim przypadku maska umask jest skutecznie ignorowana, a uprawnienia zdefiniowane przez domyślną listę ACL są stosowane do elementu podrzędnego. 

Poniższy pseudokodzie pokazuje, jak maska umask jest stosowany podczas tworzenia list ACL dla elementu podrzędnego.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Często zadawane pytania dotyczące list ACL w Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Czy muszę włączyć obsługę list ACL?

Nie. Kontrola dostępu za pośrednictwem list ACL jest włączona dla konta magazynu, o ile jest włączona funkcja hierarchicznej przestrzeni nazw (SNS).

Jeśli usługa HNS jest wyłączona, nadal obowiązują reguły autoryzacji usługi Azure RBAC.

### <a name="what-is-the-best-way-to-apply-acls"></a>Jaki jest najlepszy sposób stosowania list ACL?

Zawsze używaj grup zabezpieczeń usługi Azure AD jako przypisanego podmiotu zabezpieczeń na listach ACL. Odporne na możliwość bezpośredniego przypisywania poszczególnych użytkowników lub podmiotów usługi. Użycie tej struktury umożliwi Dodawanie i usuwanie użytkowników lub jednostek usług bez konieczności ponownego stosowania list kontroli dostępu do całej struktury katalogów. Zamiast tego wystarczy dodać lub usunąć je z odpowiedniej grupy zabezpieczeń usługi Azure AD. Należy pamiętać, że listy ACL nie są dziedziczone i dlatego ponowne zastosowanie list kontroli dostępu wymaga aktualizacji listy ACL dla każdego pliku i podkatalogu. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Które uprawnienia są wymagane do rekursywnego usunięcia katalogu i jego zawartości?

- Obiekt wywołujący ma uprawnienia administratora.

Lub

- Katalog nadrzędny musi mieć uprawnienia do zapisu i wykonywania.
- Katalog, który ma zostać usunięty, i każdy znajdujący się w nim katalog, wymaga uprawnień do odczytu i zapisu i wykonywania.

> [!NOTE]
> Nie potrzebujesz uprawnień do zapisu w celu usuwania plików w katalogach. Ponadto nie można usunąć katalogu głównego "/".

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Kto jest właścicielem pliku lub katalogu?

Twórca pliku lub katalogu stał się właścicielem. W przypadku katalogu głównego jest to tożsamość użytkownika, który utworzył kontener.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Która grupa jest ustawiona jako grupa będąca właścicielem pliku lub katalogu podczas tworzenia?

Grupa będąca właścicielem jest kopiowana z grupy będącej właścicielem katalogu nadrzędnego, w którym tworzony jest nowy plik lub katalog.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jestem użytkownikiem będącym właścicielem pliku, ale nie mam wymaganych uprawnień RWX. Co mam zrobić?

Użytkownik będący właścicielem może zmienić uprawnienia do pliku, aby przydzielić sobie wymagane uprawnienia RWX.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Dlaczego w listach kontroli dostępu czasami są wyświetlane identyfikatory GUID?

Identyfikator GUID jest wyświetlany, Jeśli wpis reprezentuje użytkownika, a użytkownik nie istnieje już w usłudze Azure AD. Zwykle dzieje się tak, gdy użytkownik opuścił firmę lub jego konto w usłudze Azure AD zostało usunięte. Ponadto jednostki usługi i grupy zabezpieczeń nie mają nazwy głównej użytkownika (UPN), aby je identyfikować i dlatego są reprezentowane przez ich atrybut OID (GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Jak mogę ustawić listy ACL poprawnie dla nazwy głównej usługi?

Podczas definiowania list ACL dla nazw głównych usługi należy użyć identyfikatora obiektu (OID) jednostki *usługi* dla utworzonej rejestracji aplikacji. Należy pamiętać, że zarejestrowane aplikacje mają oddzielną jednostkę usługi w określonej dzierżawie usługi Azure AD. Zarejestrowane aplikacje mają identyfikator OID widoczny w Azure Portal, ale jednostka *usługi* ma inny identyfikator OID (różny).

Aby uzyskać identyfikator OID dla jednostki usługi, która odnosi się do rejestracji aplikacji, możesz użyć `az ad sp show` polecenia. Określ identyfikator aplikacji jako parametr. Oto przykład uzyskiwania identyfikatora OID dla jednostki usługi, która odpowiada rejestracji aplikacji z IDENTYFIKATORem aplikacji = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

Zostanie wyświetlony identyfikator OID.

Jeśli masz prawidłowy identyfikator OID dla jednostki usługi, przejdź do strony Eksplorator usługi Storage **Zarządzanie dostępem** , aby dodać identyfikator OID i przypisać odpowiednie uprawnienia dla identyfikatora OID. Upewnij się, że wybrano pozycję **Zapisz**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Czy Data Lake Storage Gen2 obsługuje dziedziczenie list ACL?

Przypisań ról platformy Azure dziedziczą. Przepływy zadań z subskrypcji, grupy zasobów i zasobów konta magazynu w dół do zasobu kontenera.

Listy ACL nie są dziedziczone. Jednak domyślne listy ACL mogą być używane do ustawiania list ACL dla podrzędnych podkatalogów i plików utworzonych w katalogu nadrzędnym. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Gdzie można dowiedzieć się więcej na temat modelu kontroli dostępu POSIX?

* [Listy kontroli dostępu w modelu POSIX w systemie Linux](https://www.linux.com/news/posix-acls-linux)
* [Przewodnik po uprawnieniach systemu HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX — często zadawane pytania](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Listy ACL modelu POSIX w systemie Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Listy ACL korzystające z list kontroli dostępu w systemie Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zobacz też

* [Omówienie Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
