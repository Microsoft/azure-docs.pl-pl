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
ms.openlocfilehash: 6ebf23720d1d323b66671c6770ab2121c9091920
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197700"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Kontrola dostępu do konta magazynu dla programu SQL na żądanie (wersja zapoznawcza)

Zapytanie na żądanie SQL odczytuje pliki bezpośrednio z usługi Azure Storage. Uprawnienia dostępu do plików w usłudze Azure Storage są kontrolowane na dwóch poziomach:
- **Poziom magazynu** — użytkownik powinien mieć uprawnienia dostępu do podstawowych plików magazynu. Administrator magazynu powinien zezwolić podmiotowi zabezpieczeń usługi Azure AD na odczyt/zapis plików lub generowanie klucza SAS, który będzie używany do uzyskiwania dostępu do magazynu.
- **Poziom usługi SQL** — użytkownik powinien mieć `ADMINISTER BULK ADMIN` uprawnienia do wykonywania `OPENROWSET` , a także uprawnienia do używania poświadczeń, które będą używane w celu uzyskania dostępu do magazynu.

W tym artykule opisano typy poświadczeń, których można użyć oraz sposób przeszukiwania poświadczeń dla użytkowników usług SQL i Azure AD.

## <a name="supported-storage-authorization-types"></a>Obsługiwane typy autoryzacji magazynu

Użytkownik zalogowany do zasobu na żądanie SQL musi mieć autoryzację, aby uzyskać dostęp do plików w usłudze Azure Storage i wysyłać do nich zapytania. Obsługiwane są trzy typy autoryzacji:

- [Sygnatura dostępu współdzielonego](#shared-access-signature)
- [Tożsamość zarządzana](#managed-identity)
- [Tożsamość użytkownika](#user-identity)

> [!NOTE]
> [Usługa Azure AD Pass-through](#force-azure-ad-pass-through) to domyślne zachowanie podczas tworzenia obszaru roboczego. Jeśli używasz tego programu, nie musisz tworzyć poświadczeń dla każdego konta magazynu, do którego można uzyskać dostęp za pomocą nazw logowania usługi Azure AD. [To zachowanie można wyłączyć](#disable-forcing-azure-ad-pass-through).

W poniższej tabeli znajdują się dostępne typy autoryzacji:

| Typ autoryzacji                    | *Użytkownik SQL*    | *Użytkownik usługi Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [SYGNATUR](#shared-access-signature)       | Obsługiwane     | Obsługiwane      |
| [Tożsamość zarządzana](#managed-identity) | Nieobsługiwane | Obsługiwane      |
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

> [!IMPORTANT]
> Aby móc uzyskiwać dostęp do danych, musisz mieć rolę właściciel danych obiektów BLOB/współautor/czytelnik.
> Nawet jeśli jesteś właścicielem konta magazynu, nadal musisz dodać siebie do jednej z ról danych obiektów blob magazynu.
>
> Aby dowiedzieć się więcej na temat kontroli dostępu w Azure Data Lake Store Gen2, zapoznaj się z informacjami na temat [kontroli dostępu w Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artykule.
>

Należy jawnie włączyć uwierzytelnianie przekazywane przez usługę Azure AD, aby umożliwić użytkownikom usługi Azure AD dostęp do magazynu przy użyciu ich tożsamości.

#### <a name="force-azure-ad-pass-through"></a>Wymuś przekazywanie usługi Azure AD

Wymuszanie przekazywania usługi Azure AD jest domyślnym zachowaniem uzyskanym przez specjalną nazwę poświadczenia, `UserIdentity` która jest tworzona automatycznie podczas aprowizacji obszaru roboczego usługi Azure Synapse. Wymusza użycie usługi Azure AD Pass-through dla każdego zapytania dotyczącego każdego identyfikatora logowania usługi Azure AD, co nastąpi pomimo istnienia innych poświadczeń.

> [!NOTE]
> Usługa Azure AD Pass-through jest zachowaniem domyślnym. Nie musisz tworzyć poświadczeń dla każdego konta magazynu, do którego uzyskuje się dostęp przy użyciu nazw logowania usługi AD.

W przypadku [wyłączenia dla każdej kwerendy usługi Azure AD Pass-through](#disable-forcing-azure-ad-pass-through), a następnie chcesz ją ponownie włączyć, wykonaj następujące kroki:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Aby włączyć przekazywanie usługi Azure AD dla określonego użytkownika, można przyznać uprawnienia odwołania dla danego `UserIdentity` użytkownika. Poniższy przykład umożliwia wymuszenie przekazywania usługi Azure AD dla user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Wyłączenie wymuszania usługi Azure AD Pass-through

Można wyłączyć funkcję [wymuszania przekazywania usługi Azure AD dla każdego zapytania](#force-azure-ad-pass-through). Aby go wyłączyć, Porzuć `Userdentity` poświadczenia przy użyciu:

```sql
DROP CREDENTIAL [UserIdentity];
```

Jeśli chcesz ponownie włączyć tę funkcję, zapoznaj się z sekcją [przekazanie do usługi Azure AD](#force-azure-ad-pass-through) .

### <a name="managed-identity"></a>Tożsamość zarządzana

**Tożsamość zarządzana** jest również znana jako plik msi. Jest to funkcja Azure Active Directory (Azure AD), która udostępnia usługi platformy Azure dla programu SQL na żądanie. Wdraża także automatycznie zarządzaną tożsamość w usłudze Azure AD. Ta tożsamość może służyć do autoryzowania żądania dostępu do danych w usłudze Azure Storage.

Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień do zarządzanej tożsamości na potrzeby uzyskiwania dostępu do danych. Przyznawanie uprawnień do tożsamości zarządzanej odbywa się tak samo jak udzielanie uprawnień innym użytkownikom usługi Azure AD.

## <a name="credentials"></a>Poświadczenia

Aby wysłać zapytanie do pliku znajdującego się w usłudze Azure Storage, punkt końcowy SQL na żądanie musi mieć poświadczenie zawierające informacje o uwierzytelnianiu. Używane są dwa typy poświadczeń:
- POŚWIADCZENIA na poziomie serwera są używane dla zapytań ad hoc wykonywanych za pomocą `OPENROWSET` funkcji. Nazwa poświadczenia musi być zgodna z adresem URL magazynu.
- Poświadczenie o zakresie bazy danych jest używane w przypadku tabel zewnętrznych. Tabela zewnętrzna zawiera odwołania `DATA SOURCE` do poświadczeń, które powinny być używane do uzyskiwania dostępu do magazynu.

Poświadczenie jest dodawane przez uruchomienie [Create Credential](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Musisz podać argument nazwy poświadczenia. Musi być zgodna z każdą częścią ścieżki lub pełną ścieżką do danych w magazynie (patrz poniżej).

> [!NOTE]
> Argument dla dostawcy usług KRYPTOGRAFICZNYch nie jest obsługiwany.

W przypadku wszystkich obsługiwanych typów autoryzacji poświadczenia mogą wskazywać na konto lub kontener.

Nazwa poświadczeń na poziomie serwera musi być zgodna z pełną ścieżką do konta magazynu (i opcjonalnie kontenera) w następującym formacie:`<prefix>://<storage_account_path>/<storage_path>`

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Usługa Azure Data Lake Storage 1. generacji | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Usługa Azure Data Lake Storage 2. generacji | https  | <storage_account>. dfs.core.windows.net              |


> [!NOTE]
> Istnieje specjalne poświadczenie na poziomie serwera `UserIdentity` , które [wymusza przekazywanie usługi Azure AD](#force-azure-ad-pass-through).

Opcjonalnie, aby zezwolić użytkownikowi na tworzenie lub porzucanie poświadczeń, administrator może udzielić/odmówić zmiany uprawnień poświadczeń użytkownikowi:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Obsługiwane magazyny i typy autoryzacji

Można użyć następujących kombinacji typów autoryzacji i usługi Azure Storage:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SYGNATUR*               | Obsługiwane      | Nieobsługiwane   | Obsługiwane     |
| *Tożsamość zarządzana* | Obsługiwane      | Obsługiwane        | Obsługiwane     |
| *Tożsamość użytkownika*    | Obsługiwane      | Obsługiwane        | Obsługiwane     |


### <a name="grant-permissions-to-use-credential"></a>Przyznawanie uprawnień do korzystania z poświadczeń

Aby można było użyć poświadczenie, użytkownik musi mieć uprawnienia odwołuje się do określonego poświadczenia. Aby udzielić uprawnień odwołujących się do storage_credential specific_user, wykonaj następujące polecenie:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Aby zapewnić bezproblemowe środowisko przekazywania usługi Azure AD, wszyscy użytkownicy będą domyślnie mieli prawo do korzystania z `UserIdentity` poświadczeń. Jest to realizowane przez automatyczne wykonanie następującej instrukcji podczas aprowizacji obszaru roboczego usługi Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

### <a name="examples"></a>Przykłady

W zależności od [typu autoryzacji](#supported-storage-authorization-types)można utworzyć poświadczenia przy użyciu składni T-SQL poniżej.
- Poświadczenia z zakresem serwera są używane, gdy logowanie SQL wywołuje `OPENROWSET` funkcję bez `DATA_SOURCE` odczytywania plików na niektórych kontach magazynu. Nazwa poświadczenia z zakresem serwera **musi** być zgodna z adresem URL usługi Azure Storage.
- Poświadczenia w zakresie bazy danych są używane, gdy każda `OPENROWSET` funkcja wywołuje `DATA_SOURCE` lub wybiera dane z tabeli zewnętrznej, która nie uzyskuje dostępu do plików publicznych. Poświadczenia w zakresie bazy danych nie muszą być zgodne z nazwą konta magazynu, ponieważ zostaną jawnie użyte w źródle danych, który definiuje lokalizację magazynu.

**Poświadczenie o zakresie serwera z sygnaturą dostępu współdzielonego dla Blob Storage**

Poniższy skrypt tworzy poświadczenia na poziomie serwera, których można użyć w celu uzyskania dostępu do dowolnego pliku w usłudze Azure Storage przy użyciu tokenu SAS. Utwórz to poświadczenie, aby włączyć podmiot zabezpieczeń SQL, który wykonuje `OPENROWSET` funkcję do odczytu plików chronionych za pomocą klucza SAS w usłudze Azure Storage, które pasują do adresu URL w nazwie poświadczenia.

Program Exchange <*mystorageaccountname*> z rzeczywistą nazwą konta magazynu i <*mystorageaccountcontainername*> z rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Poświadczenia w zakresie serwera, które umożliwiają dostęp do magazynu publicznego**

Poniższy skrypt tworzy poświadczenia na poziomie serwera, których można użyć w celu uzyskania dostępu do dowolnego pliku w publicznie dostępnym magazynie Azure. Utwórz to poświadczenie, aby włączyć podmiot zabezpieczeń SQL, który wykonuje `OPENROWSET` funkcję do odczytu publicznie dostępnych plików w usłudze Azure Storage, które pasują do adresu URL w nazwie poświadczenia.

Należy *mieć <programu* Exchange> z rzeczywistą nazwą konta magazynu i <*mystorageaccountcontainername*> z rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```

**Poświadczenie o zakresie bazy danych z tokenem sygnatury dostępu współdzielonego**

Poniższy skrypt tworzy poświadczenie, które jest używane do uzyskiwania dostępu do plików w magazynie przy użyciu tokenu SAS określonego w poświadczeniu.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Poświadczenia w zakresie bazy danych z tożsamością usługi Azure AD**

Poniższy skrypt tworzy poświadczenie, które jest używane przez zewnętrzne tabele i `OPENROWSET` funkcje, które używają źródła danych z poświadczeniami do uzyskiwania dostępu do plików magazynu przy użyciu własnej tożsamości usługi Azure AD.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

**Poświadczenie o zakresie bazy danych z tożsamością zarządzaną**

Poniższy skrypt tworzy poświadczenie, które może służyć do personifikacji bieżącego użytkownika usługi Azure AD jako tożsamości zarządzanej usługi. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

Poświadczenia w zakresie bazy danych nie muszą być zgodne z nazwą konta magazynu, ponieważ zostaną jawnie użyte w źródle danych, który definiuje lokalizację magazynu.

## <a name="next-steps"></a>Następne kroki

Artykuły wymienione poniżej zawierają informacje o sposobie wykonywania zapytań dotyczących różnych typów folderów, typów plików i tworzenia i używania widoków:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)
- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](query-specific-files.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)
