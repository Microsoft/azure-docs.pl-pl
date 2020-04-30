---
title: Kontrola dostępu do konta magazynu dla programu SQL na żądanie (wersja zapoznawcza)
description: Opisuje, w jaki sposób usługa SQL na żądanie (wersja zapoznawcza) uzyskuje dostęp do usługi Azure Storage oraz jak kontrolować dostęp do magazynu na żądanie SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424034"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Kontrolowanie dostępu do konta magazynu dla SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

Zapytanie SQL na żądanie (wersja zapoznawcza) odczytuje pliki bezpośrednio z usługi Azure Storage. Ponieważ konto magazynu jest obiektem spoza zasobu SQL na żądanie, wymagane są odpowiednie poświadczenia. Użytkownik musi mieć odpowiednie uprawnienia przyznane do korzystania z poświadczeń wymaganych. W tym artykule opisano typy poświadczeń, których można użyć oraz sposób przeszukiwania poświadczeń dla użytkowników usług SQL i Azure AD.

## <a name="supported-storage-authorization-types"></a>Obsługiwane typy autoryzacji magazynu

Użytkownik zalogowany do zasobu na żądanie SQL musi mieć autoryzację, aby uzyskać dostęp do plików w usłudze Azure Storage i wysyłać do nich zapytania. Obsługiwane są trzy typy autoryzacji:

- [Sygnatura dostępu współdzielonego](#shared-access-signature)
- [Tożsamość zarządzana](#managed-identity)
- [Tożsamość użytkownika](#user-identity)

> [!NOTE]
> [Usługa Azure AD Pass-through](#force-azure-ad-pass-through) to domyślne zachowanie podczas tworzenia obszaru roboczego. Jeśli używasz tego programu, nie musisz tworzyć poświadczeń dla każdego konta magazynu, do którego uzyskuje się dostęp przy użyciu nazw logowania usługi AD. [To zachowanie można wyłączyć](#disable-forcing-azure-ad-pass-through).

W poniższej tabeli znajdziesz różne typy autoryzacji, które są obsługiwane lub wkrótce będą obsługiwane.

| Typ autoryzacji                    | *Użytkownik SQL*    | *Użytkownik usługi Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [SYGNATUR](#shared-access-signature)       | Obsługiwane     | Obsługiwane      |
| [Tożsamość zarządzana](#managed-identity) | Nieobsługiwane | Nieobsługiwane  |
| [Tożsamość użytkownika](#user-identity)       | Nieobsługiwane | Obsługiwane      |

### <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

**Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego klient może udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia szczegółową kontrolę nad dostępem udzielonym klientom, którzy mają SYGNATURę czasową, w tym interwałem ważności, udzielonymi uprawnieniami, akceptowalnym zakresem adresów IP i dozwolonym protokołem (https/http).

Token SYGNATURy dostępu współdzielonego można uzyskać, przechodząc do **konta magazynu Azure Portal-> Storage-> sygnatura dostęp współdzielony-> skonfigurować uprawnienia-> generować sygnatury SAS i parametry połączenia.**

> [!IMPORTANT]
> Po wygenerowaniu tokenu SAS zawiera znak zapytania ("?") na początku tokenu. Aby użyć tokenu w SQL na żądanie, należy usunąć znak zapytania ("?") podczas tworzenia poświadczenia. Przykład:
>
> Token sygnatury dostępu współdzielonego:? SV = 2018 r-03-28&SS = bfqt&narzędzia SRT = SCO&Sp = rwdlacup&SE = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

### <a name="user-identity"></a>Tożsamość użytkownika

**Tożsamość użytkownika**, znana również jako "pass-through", jest typem autoryzacji, w którym tożsamość użytkownika usługi Azure AD, która jest zalogowana na żądanie SQL, jest używana do autoryzowania dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień użytkownikowi usługi Azure AD. Jak wskazano w powyższej tabeli, nie jest to obsługiwane w przypadku typu użytkownika SQL.

> [!NOTE]
> Jeśli używasz usługi [Azure AD Pass-through](#force-azure-ad-pass-through) , nie musisz tworzyć poświadczeń dla każdego konta magazynu, do którego uzyskuje się dostęp przy użyciu nazw logowania usługi AD.

> [!IMPORTANT]
> Aby móc uzyskiwać dostęp do danych, musisz mieć rolę właściciel danych obiektów BLOB/współautor/czytelnik.
> Nawet jeśli jesteś właścicielem konta magazynu, nadal musisz dodać siebie do jednej z ról danych obiektów blob magazynu.
>
> Aby dowiedzieć się więcej na temat kontroli dostępu w Azure Data Lake Store Gen2, zapoznaj się z informacjami na temat [kontroli dostępu w Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artykule.
>

### <a name="managed-identity"></a>Tożsamość zarządzana

**Tożsamość zarządzana** jest również znana jako plik msi. Jest to funkcja Azure Active Directory (Azure AD), która udostępnia usługi platformy Azure dla programu SQL na żądanie. Wdraża także automatycznie zarządzaną tożsamość w usłudze Azure AD. Ta tożsamość może służyć do autoryzowania żądania dostępu do danych w usłudze Azure Storage.

Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień do zarządzanej tożsamości na potrzeby uzyskiwania dostępu do danych. Przyznawanie uprawnień do tożsamości zarządzanej odbywa się tak samo jak udzielanie uprawnień innym użytkownikom usługi Azure AD.

## <a name="create-credentials"></a>Utwórz poświadczenia

Aby wysłać zapytanie do pliku znajdującego się w usłudze Azure Storage, punkt końcowy SQL na żądanie wymaga poświadczeń na poziomie serwera, które zawierają informacje o uwierzytelnianiu. Poświadczenie jest dodawane przez uruchomienie [Create Credential](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Musisz podać argument nazwy poświadczenia. Musi być zgodna z każdą częścią ścieżki lub pełną ścieżką do danych w magazynie (patrz poniżej).

> [!NOTE]
> Argument dla dostawcy usług KRYPTOGRAFICZNYch nie jest obsługiwany.

W przypadku wszystkich obsługiwanych typów autoryzacji poświadczenia mogą wskazywać na konto, kontener, dowolny katalog (inny niż główny) lub jeden plik.

Nazwa poświadczenia musi być zgodna z pełną ścieżką do kontenera, folderu lub pliku, w następującym formacie:`<prefix>://<storage_account_path>/<storage_path>`

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Usługa Azure Data Lake Storage 1. generacji | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Usługa Azure Data Lake Storage 2. generacji | https  | <storage_account>. dfs.core.windows.net              |

 "<storage_path>" jest ścieżką w magazynie, która wskazuje folder lub plik, który ma zostać odczytany.

> [!NOTE]
> Istnieje specjalna nazwa `UserIdentity` poświadczeń, która [wymusza przekazywanie usługi Azure AD](#force-azure-ad-pass-through). Zapoznaj się z efektem [wyszukiwania poświadczeń](#credential-lookup) podczas wykonywania zapytań.

Opcjonalnie, aby zezwolić użytkownikowi na tworzenie lub porzucanie poświadczeń, administrator może udzielić/odmówić zmiany uprawnień poświadczeń użytkownikowi:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Obsługiwane magazyny i typy autoryzacji

Można użyć następujących kombinacji typów autoryzacji i usługi Azure Storage:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SYGNATUR*               | Obsługiwane      | Nieobsługiwane   | Obsługiwane     |
| *Tożsamość zarządzana* | Nieobsługiwane  | Nieobsługiwane    | Nieobsługiwane |
| *Tożsamość użytkownika*    | Obsługiwane      | Obsługiwane        | Obsługiwane     |

### <a name="examples"></a>Przykłady

W zależności od [typu autoryzacji](#supported-storage-authorization-types)można utworzyć poświadczenia przy użyciu składni T-SQL poniżej.

**Sygnatura dostępu współdzielonego i Blob Storage**

Program Exchange <*mystorageaccountname*> z rzeczywistą nazwą konta magazynu i <*mystorageaccountcontainername*> z rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Tożsamość użytkownika i Azure Data Lake Storage Gen1**

Program Exchange <*mystorageaccountname*> z rzeczywistą nazwą konta magazynu i <*mystorageaccountcontainername*> z rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Tożsamość użytkownika i Azure Data Lake Storage Gen2**

Program Exchange <*mystorageaccountname*> z rzeczywistą nazwą konta magazynu i <*mystorageaccountcontainername*> z rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Wymuś przekazywanie usługi Azure AD

Wymuszanie przekazywania usługi Azure AD jest domyślnym zachowaniem uzyskanym przez specjalną nazwę poświadczenia `UserIdentity`, która jest tworzona automatycznie podczas aprowizacji obszaru roboczego usługi Azure Synapse. Wymusza użycie usługi Azure AD Pass-through dla każdego zapytania dotyczącego każdego identyfikatora logowania usługi Azure AD, co nastąpi pomimo istnienia innych poświadczeń.

> [!NOTE]
> Usługa Azure AD Pass-through jest zachowaniem domyślnym. Nie musisz tworzyć poświadczeń dla każdego konta magazynu, do którego uzyskuje się dostęp przy użyciu nazw logowania usługi AD.

W przypadku [wyłączenia dla każdej kwerendy usługi Azure AD Pass-through](#disable-forcing-azure-ad-pass-through), a następnie chcesz ją ponownie włączyć, wykonaj następujące kroki:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Aby włączyć przekazywanie usługi Azure AD dla określonego użytkownika, można przyznać uprawnienia `UserIdentity` odwołania dla danego użytkownika. Poniższy przykład umożliwia wymuszenie przekazywania usługi Azure AD dla user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Aby uzyskać więcej informacji na temat sposobu wyszukiwania poświadczeń do użycia przez SQL na żądanie, zobacz [Wyszukiwanie poświadczeń](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Wyłączenie wymuszania usługi Azure AD Pass-through

Można wyłączyć funkcję [wymuszania przekazywania usługi Azure AD dla każdego zapytania](#force-azure-ad-pass-through). Aby go wyłączyć, Porzuć `Userdentity` poświadczenia przy użyciu:

```sql
DROP CREDENTIAL [UserIdentity];
```

Jeśli chcesz ponownie włączyć tę funkcję, zapoznaj się z sekcją [przekazanie do usługi Azure AD](#force-azure-ad-pass-through) .

Aby wyłączyć funkcję wymuszania usługi Azure AD przez określonego użytkownika, możesz odmówić uprawnień do odwołania `UserIdentity` dla określonego użytkownika. Poniższy przykład wyłącza przekazywanie usługi Azure AD dla user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Aby uzyskać więcej informacji na temat sposobu wyszukiwania poświadczeń przez SQL na żądanie, zobacz [Wyszukiwanie poświadczeń](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Przyznawanie uprawnień do korzystania z poświadczeń

Aby można było użyć poświadczenie, użytkownik musi mieć uprawnienia odwołuje się do określonego poświadczenia. Aby udzielić uprawnień odwołujących się do storage_credential specific_user, wykonaj następujące polecenie:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Aby zapewnić bezproblemowe środowisko przekazywania usługi Azure AD, wszyscy użytkownicy będą domyślnie mieli prawo do korzystania z `UserIdentity` poświadczeń. Jest to realizowane przez automatyczne wykonanie następującej instrukcji podczas aprowizacji obszaru roboczego usługi Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Wyszukiwanie poświadczeń

Podczas autoryzowania zapytań wyszukiwanie poświadczeń służy do uzyskiwania dostępu do konta magazynu i opiera się na następujących regułach:

- Użytkownik jest zalogowany jako identyfikator logowania usługi Azure AD

  - Jeśli istnieje poświadczenie tożsamość użytkownika, a użytkownik ma uprawnienia do odwołania, zostanie użyte przekazanie do usługi Azure AD. w przeciwnym razie [poszukiwanie poświadczeń według ścieżki](#lookup-credential-by-path)

- Użytkownik jest zalogowany jako logowanie SQL

  - Użyj [poświadczeń wyszukiwania według ścieżki](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Wyszukaj poświadczenie według ścieżki

W przypadku wymuszenia wyłączenia usługi Azure AD funkcja wyszukiwania poświadczeń będzie oparta na ścieżce magazynu (pierwszej głębokości) i istnieniu uprawnień odwołania dla danego poświadczenia. Jeśli istnieje wiele poświadczeń, których można użyć w celu uzyskania dostępu do tego samego pliku, SQL na żądanie będzie używać najbardziej konkretnej.  

Poniżej znajduje się przykład zapytania w następującej ścieżce do pliku: *account.DFS.Core.Windows.NET/FileSystem/Folder1/.../folderN/fileX.ext*

Wyszukiwanie poświadczeń zostanie wykonane w następującej kolejności:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Jeśli użytkownik nie ma uprawnień do ODWOŁYWANia się do poświadczeń o numerze 5, SQL na żądanie sprawdzi, czy użytkownik ma uprawnienia do poświadczeń o wyższym poziomie, dopóki nie odnajdzie poświadczeń, na które użytkownik ma uprawnienia. Jeśli takie uprawnienie nie zostanie znalezione, zostanie zwrócony komunikat o błędzie.

### <a name="credential-and-path-level"></a>Poświadczenie i poziom ścieżki

W zależności od żądanego kształtu ścieżki są stosowane następujące wymagania dotyczące uruchamiania zapytań:

- Jeśli zapytanie dotyczy wielu plików (folderów, z lub bez symboli wieloznacznych), użytkownik musi mieć dostęp do poświadczenia na co najmniej poziomie katalogu głównego (poziom kontenera). Ten poziom dostępu jest wymagany, ponieważ pliki list są względne dla katalogu głównego (ograniczenia dotyczące usługi Azure Storage)
- Jeśli zapytanie dotyczy pojedynczego pliku, użytkownik musi mieć dostęp do poświadczeń na dowolnym poziomie, ponieważ SQL na żądanie uzyskuje dostęp do pliku bezpośrednio, czyli bez wyświetlania listy folderów.

|                  | *Konto* | *Katalog główny* | *Dowolny inny katalog* | *Plik*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Pojedynczy plik*    | Obsługiwane | Obsługiwane        | Obsługiwane             | Obsługiwane     |
| *Wiele plików* | Obsługiwane | Obsługiwane        | Nieobsługiwane         | Nieobsługiwane |

## <a name="next-steps"></a>Następne kroki

Artykuły wymienione poniżej zawierają informacje o sposobie wykonywania zapytań dotyczących różnych typów folderów, typów plików i tworzenia i używania widoków:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)
- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](query-specific-files.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)
