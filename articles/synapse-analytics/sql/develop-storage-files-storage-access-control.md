---
title: Sterowanie dostępem do konta magazynu dla języka SQL na żądanie (wersja zapoznawcza)
description: W tym artykule opisano, jak sql na żądanie (wersja zapoznawcza) uzyskuje dostęp do usługi Azure Storage i jak można kontrolować dostęp do magazynu dla sql na żądanie w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424034"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Kontrolowanie dostępu do konta magazynu dla języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

Kwerenda SQL na żądanie (wersja zapoznawcza) odczytuje pliki bezpośrednio z usługi Azure Storage. Ponieważ konto magazynu jest obiektem, który jest zewnętrzny dla zasobu SQL na żądanie, wymagane są odpowiednie poświadczenia. Użytkownik potrzebuje odpowiednich uprawnień przyznanych do korzystania z wymaganych poświadczeń. W tym artykule opisano typy poświadczeń, których można użyć, oraz sposób uchwalania wyszukiwania poświadczeń dla użytkowników usług SQL i usługi Azure AD.

## <a name="supported-storage-authorization-types"></a>Obsługiwane typy autoryzacji magazynu

Użytkownik, który zalogował się do zasobu SQL na żądanie musi być autoryzowany do uzyskiwania dostępu do plików w usłudze Azure Storage i wykonywania zapytań. Obsługiwane są trzy typy autoryzacji:

- [Sygnatura dostępu współdzielonego](#shared-access-signature)
- [Tożsamość zarządzana](#managed-identity)
- [Tożsamość użytkownika](#user-identity)

> [!NOTE]
> [Przekazywanie usługi Azure AD](#force-azure-ad-pass-through) jest zachowaniem domyślnym podczas tworzenia obszaru roboczego. Jeśli go używasz, nie trzeba tworzyć poświadczeń dla każdego konta magazynu dostępne przy użyciu logowania usługi AD. Można [wyłączyć to zachowanie](#disable-forcing-azure-ad-pass-through).

W poniższej tabeli znajdziesz różne typy autoryzacji, które są obsługiwane lub będą obsługiwane wkrótce.

| Typ autoryzacji                    | *Użytkownik SQL*    | *Użytkownik usługi Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Sas](#shared-access-signature)       | Obsługiwane     | Obsługiwane      |
| [Tożsamość zarządzana](#managed-identity) | Nieobsługiwane | Nieobsługiwane  |
| [Tożsamość użytkownika](#user-identity)       | Nieobsługiwane | Obsługiwane      |

### <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego

**Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego klient może udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia szczegółową nad typem dostępu udzielonego klientom, którzy mają sygnaturę dostępu Współdzielonego, w tym interwał ważności, przyznane uprawnienia, dopuszczalny zakres adresów IP i dopuszczalny protokół (https/http).

Token sygnatury dostępu Współdzielonego można uzyskać, przechodząc do **konta usługi Azure portal -> storage account -> Shared access signature -> Configure permissions -> Generate SAS and connection string.**

> [!IMPORTANT]
> Po wygenerowaniu tokenu sygnatury dostępu Współdzielonego zawiera znak zapytania ('?') na początku tokenu. Aby użyć tokenu w języku SQL na żądanie, należy usunąć znak zapytania ('?') podczas tworzenia poświadczenia. Przykład:
>
> Sygnatura SAS: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdsMANd0ef9BrIPJ3VYEIq78%3D

### <a name="user-identity"></a>Tożsamość użytkownika

**Tożsamość użytkownika**, znany również jako "pass-through", to typ autoryzacji, w którym tożsamość użytkownika usługi Azure AD, który zalogował się do sql na żądanie jest używany do autoryzowania dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień użytkownikowi usługi Azure AD. Jak wskazano w powyższej tabeli, nie jest obsługiwany dla typu użytkownika SQL.

> [!NOTE]
> Jeśli używasz [usługi Azure AD pass-through](#force-azure-ad-pass-through) nie trzeba tworzyć poświadczenia dla każdego konta magazynu dostępne przy użyciu logowania usługi AD.

> [!IMPORTANT]
> Aby uzyskać dostęp do danych, musisz mieć rolę Właściciel/Współautor/Czytnik danych obiektu magazynu, aby użyć twojej tożsamości do uzyskania dostępu do danych.
> Nawet jeśli jesteś właścicielem konta magazynu, nadal musisz dodać się do jednej z ról danych obiektów blob magazynu.
>
> Aby dowiedzieć się więcej na temat kontroli dostępu w usłudze Azure Data Lake Store Gen2, przejrzyj [kontrolę dostępu w artykule Usługi Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="managed-identity"></a>Tożsamość zarządzana

**Tożsamość zarządzana** jest również znana jako MSI. Jest to funkcja usługi Azure Active Directory (Azure AD), która zapewnia usługi platformy Azure dla sql na żądanie. Ponadto wdraża automatycznie zarządzaną tożsamość w usłudze Azure AD. Ta tożsamość może służyć do autoryzowania żądania dostępu do danych w usłudze Azure Storage.

Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień do tożsamości zarządzanej w celu uzyskania dostępu do danych. Udzielanie uprawnień do tożsamości zarządzanej odbywa się w taki sam sposób, jak udzielanie uprawnień każdemu innemu użytkownikowi usługi Azure AD.

## <a name="create-credentials"></a>Tworzenie poświadczeń

Aby zbadać plik znajdujący się w usłudze Azure Storage, punkt końcowy sql na żądanie wymaga poświadczenia na poziomie serwera, który zawiera informacje uwierzytelniania. Poświadczenie jest dodawane przez uruchomienie [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Musisz podać argument NAZWA POŚWIADCZENIA. Musi być zgodna z częścią ścieżki lub całą ścieżką do danych w magazynie (patrz poniżej).

> [!NOTE]
> Argument FOR CRYPTOGRAPHIC PROVIDER nie jest obsługiwany.

W przypadku wszystkich obsługiwanych typów autoryzacji poświadczenia mogą wskazywać konto, kontener, dowolny katalog (nieszporowy) lub pojedynczy plik.

NAZWA POŚWIADCZEŃ musi być zgodna z pełną ścieżką do kontenera, folderu lub pliku w następującym formacie:`<prefix>://<storage_account_path>/<storage_path>`

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Usługa Azure Data Lake Storage 1. generacji | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Usługa Azure Data Lake Storage 2. generacji | https  | <storage_account>.dfs.core.windows.net              |

 "<storage_path>" to ścieżka w magazynie, która wskazuje folder lub plik, który chcesz odczytać.

> [!NOTE]
> Istnieje specjalna NAZWA `UserIdentity` POŚWIADCZEŃ, która [wymusza przekazywanie usługi Azure AD](#force-azure-ad-pass-through)przez . Przeczytaj o wpływie, jaki ma na [wyszukiwanie poświadczeń](#credential-lookup) podczas wykonywania zapytań.

Opcjonalnie, aby umożliwić użytkownikowi tworzenie lub upuszczanie poświadczeń, administrator może udzielić/odmówić zmiany wszelkich uprawnień poświadczeń dla użytkownika:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Obsługiwane magazyny i typy autoryzacji

Można użyć następujących kombinacji autoryzacji i typów usługi Azure Storage:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *Sas*               | Obsługiwane      | Nie obsługiwane   | Obsługiwane     |
| *Tożsamość zarządzana* | Nieobsługiwane  | Nieobsługiwane    | Nieobsługiwane |
| *Tożsamość użytkownika*    | Obsługiwane      | Obsługiwane        | Obsługiwane     |

### <a name="examples"></a>Przykłady

W zależności od [typu autoryzacji](#supported-storage-authorization-types)można utworzyć poświadczenia przy użyciu poniższej składni T-SQL.

**Sygnatura dostępu współdzielonego i magazyn obiektów blob**

<> zamiennych <*mystorageaccountname* z rzeczywistą nazwą konta magazynu i <*> mystoragecountcontainername* o rzeczywistej nazwie kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Tożsamość użytkownika i usługa Azure Data Lake Storage Gen1**

<> zamiennych <*mystorageaccountname* z rzeczywistą nazwą konta magazynu i <*> mystoragecountcontainername* o rzeczywistej nazwie kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Tożsamość użytkownika i usługa Azure Data Lake Storage Gen2**

<> zamiennych <*mystorageaccountname* z rzeczywistą nazwą konta magazynu i <*> mystoragecountcontainername* o rzeczywistej nazwie kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Wymuszanie przekazywania usługi Azure AD

Wymuszanie przekazywania usługi Azure AD jest domyślnym zachowaniem osiągniętym przez specjalną nazwę poświadczeń, `UserIdentity`która jest tworzona automatycznie podczas inicjowania obsługi administracyjnej obszaru roboczego Usługi Azure Synapse. Wymusza użycie usługi Azure AD pass-through dla każdej kwerendy każdego logowania usługi Azure AD, który wystąpi pomimo istnienia innych poświadczeń.

> [!NOTE]
> Przekazywanie usługi Azure AD jest zachowaniem domyślnym. Nie trzeba tworzyć poświadczeń dla każdego konta magazynu dostępnego za pomocą logowania usługi AD.

W przypadku, gdy [wyłączysz wymuszanie przekazywania usługi Azure AD dla każdej kwerendy](#disable-forcing-azure-ad-pass-through)i chcesz włączyć ją ponownie, wykonaj:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Aby włączyć wymuszanie przekazywania usługi Azure AD dla określonego `UserIdentity` użytkownika, można udzielić uprawnień REFERENCE na poświadczenia do określonego użytkownika. Poniższy przykład umożliwia wymuszanie przekazywania usługi Azure AD dla user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Aby uzyskać więcej informacji na temat sposobu, w jaki sql na żądanie znajduje poświadczenia do użycia, zobacz [wyszukiwanie poświadczeń](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Wyłącz wymuszanie przekazywania usługi Azure AD

Można wyłączyć [wymuszanie przekazywania usługi Azure AD dla każdej kwerendy.](#force-azure-ad-pass-through) Aby ją wyłączyć, `Userdentity` upuść poświadczenia za pomocą:

```sql
DROP CREDENTIAL [UserIdentity];
```

Jeśli chcesz ponownie włączyć go ponownie, odnoszą się do [sekcji przekazywania usługi Azure AD.](#force-azure-ad-pass-through)

Aby wyłączyć wymuszanie przekazywania usługi Azure AD dla określonego `UserIdentity` użytkownika, można odmówić uprawnienia REFERENCE na poświadczenia dla określonego użytkownika. Poniższy przykład wyłącza wymuszanie przekazywania usługi Azure AD dla user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Aby uzyskać więcej informacji na temat sposobu, w jaki sql na żądanie znajduje poświadczenia do użycia, zobacz [wyszukiwanie poświadczeń](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Udzielanie uprawnień do używania poświadczeń

Aby użyć poświadczeń, użytkownik musi mieć uprawnienia REFERENCES dla określonego poświadczenia. Aby udzielić uprawnienia REFERENCES na storage_credential dla specific_user, wykonaj:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Aby zapewnić płynne środowisko przekazywania usługi Azure AD, wszyscy użytkownicy `UserIdentity` będą domyślnie mieli prawo do używania poświadczeń. Jest to osiągane przez automatyczne wykonanie następującej instrukcji na temat inicjowania obsługi administracyjnej obszaru roboczego usługi Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Wyszukiwanie poświadczeń

Podczas autoryzowania kwerend wyszukiwanie poświadczeń jest używane do uzyskiwania dostępu do konta magazynu i opiera się na następujących regułach:

- Użytkownik jest zalogowany jako login usługi Azure AD

  - Jeśli istnieje poświadczenie UserIdentity, a użytkownik ma uprawnienia referencyjne, zostanie użyty przekaz usługi Azure AD, w przeciwnym razie [poświadczenia wyszukiwania według ścieżki](#lookup-credential-by-path)

- Użytkownik jest zalogowany jako login SQL

  - Używanie [poświadczeń odnośny według ścieżki](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Poświadczenia odnośnika według ścieżki

Jeśli wymuszenie przekazywania usługi Azure AD jest wyłączone, wyszukiwanie poświadczeń będzie oparte na ścieżce magazynu (najpierw głębokość) i istnieniu uprawnienia REFERENCES dla tego określonego poświadczenia. Jeśli istnieje wiele poświadczeń, które mogą służyć do uzyskania dostępu do tego samego pliku, SQL na żądanie użyje najbardziej konkretnego.  

Poniżej znajduje się przykład kwerendy w następującej ścieżce pliku: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

Wyszukiwanie poświadczeń zostanie zakończone w tej kolejności:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Jeśli użytkownik nie ma uprawnień ODWOŁÓW do numeru poświadczeń 5, sql na żądanie sprawdzi, czy użytkownik ma uprawnienia REFERENCES na poświadczeniu, który jest o jeden poziom wyższy, dopóki nie zlokalizuje poświadczenia, na które użytkownik ma uprawnienia REFERENCES. Jeśli takie uprawnienie nie zostanie znalezione, zostanie zwrócony komunikat o błędzie.

### <a name="credential-and-path-level"></a>Poziom poświadczeń i ścieżki

W zależności od odpowiedniego kształtu ścieżki obowiązują następujące wymagania dotyczące uruchamiania kwerend:

- Jeśli kwerenda jest kierowana na wiele plików (foldery, z lub bez symboli wieloznacznych), użytkownik musi mieć dostęp do poświadczeń na co najmniej poziomie katalogu głównego (poziomie kontenera). Ten poziom dostępu jest potrzebny, ponieważ pliki listy są względne z katalogu głównego (ograniczenia usługi Azure Storage)
- Jeśli kwerenda jest kierowana na pojedynczy plik, użytkownik musi mieć dostęp do poświadczeń na dowolnym poziomie, ponieważ SQL na żądanie uzyskuje bezpośredni dostęp do pliku, czyli bez wyświetlania folderów.

|                  | *Konto* | *Katalog główny* | *Każdy inny katalog* | *Plik*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Pojedynczy plik*    | Obsługiwane | Obsługiwane        | Obsługiwane             | Obsługiwane     |
| *Wiele plików* | Obsługiwane | Obsługiwane        | Nieobsługiwane         | Nieobsługiwane |

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły pomogą Ci dowiedzieć się, jak badać różne typy folderów, typy plików oraz tworzyć i używać widoków:

- [Zapytanie o pojedynczy plik CSV](query-single-csv-file.md)
- [Foldery kwerend i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](query-specific-files.md)
- [Pliki parkietu kwerendy](query-parquet-files.md)
- [Tworzenie i używanie widoków](create-use-views.md)
- [Kwerenda plików JSON](query-json-files.md)
- [Kwerenda Parkiet zagnieżdżone typy](query-parquet-nested-types.md)
