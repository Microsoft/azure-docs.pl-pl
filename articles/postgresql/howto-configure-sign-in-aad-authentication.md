---
title: Użyj Azure Active Directory-Azure Database for PostgreSQL-pojedynczego serwera
description: Informacje na temat sposobu konfigurowania Azure Active Directory (AAD) na potrzeby uwierzytelniania przy użyciu jednego serwera Azure Database for PostgreSQL
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 178c339f6f47569160a9a748794678c610f35734
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87171643"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Używanie Azure Active Directory do uwierzytelniania za pomocą PostgreSQL

W tym artykule opisano kroki konfigurowania Azure Active Directory dostępu za pomocą Azure Database for PostgreSQL i sposobu nawiązywania połączenia przy użyciu tokenu usługi Azure AD.

## <a name="setting-the-azure-ad-admin-user"></a>Ustawianie użytkownika administratora usługi Azure AD

Tylko użytkownicy administratorów usługi Azure AD mogą tworzyć/włączać uwierzytelnianie oparte na usłudze Azure AD. Nie zaleca się korzystania z administratora usługi Azure AD na potrzeby zwykłych operacji baz danych, ponieważ ma podwyższone uprawnienia użytkowników (np. CREATEDB).

Aby ustawić administratora usługi Azure AD (można użyć użytkownika lub grupy), wykonaj następujące czynności.

1. W Azure Portal wybierz wystąpienie Azure Database for PostgreSQL, które chcesz włączyć dla usługi Azure AD.
2. W obszarze Ustawienia wybierz pozycję Administrator Active Directory:

![Ustawianie administratora usługi Azure AD][2]

3. Wybierz prawidłowego użytkownika usługi Azure AD w dzierżawie klienta, aby być administratorem usługi Azure AD.

> [!IMPORTANT]
> Podczas konfigurowania administratora nowy użytkownik zostanie dodany do serwera Azure Database for PostgreSQL z uprawnieniami pełnego administratora. Użytkownik administracyjny usługi Azure AD w Azure Database for PostgreSQL będzie miał rolę `azure_ad_admin` .

Tylko jeden administrator usługi Azure AD można utworzyć na serwerze PostgreSQL, a wybranie innego z nich spowoduje zastąpienie istniejącego administratora usługi Azure AD skonfigurowanego dla serwera. Możesz określić grupę usługi Azure AD zamiast pojedynczego użytkownika, aby mieć wielu administratorów. Pamiętaj, że następnie zaloguj się przy użyciu nazwy grupy dla celów administracyjnych.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Nawiązywanie połączenia z Azure Database for PostgreSQL przy użyciu usługi Azure AD

Poniższy diagram wysokiego poziomu podsumowuje przepływ pracy przy użyciu uwierzytelniania usługi Azure AD z Azure Database for PostgreSQL:

![przepływ uwierzytelniania][1]

Zaprojektowano integrację usługi Azure AD w celu współpracy z typowymi narzędziami PostgreSQL, takimi jak PSQL, które nie są oparte na usłudze Azure AD i obsługują Określanie nazwy użytkownika i hasła podczas nawiązywania połączenia z usługą PostgreSQL. Token usługi Azure AD jest przekazywany jako hasło, jak pokazano na powyższym rysunku.

Obecnie przetestowano następujących klientów:

- PSQL CommandLine (Użyj zmiennej PGPASSWORD, aby przekazać token, zobacz poniżej)
- Azure Data Studio (przy użyciu rozszerzenia PostgreSQL)
- Inni klienci korzystający z libpq (np. typowe struktury aplikacji i ORMs)

> [!NOTE]
> Należy pamiętać, że użycie tokenu usługi Azure AD z usługą pgAdmin nie jest obecnie obsługiwane, ponieważ ma ustalone ograniczenie 256 znaków dla haseł (do których token przekracza).

Poniżej przedstawiono kroki, które należy wykonać, aby użytkownik/aplikacja wymagały uwierzytelniania za pomocą usługi Azure AD opisanej poniżej:

### <a name="prerequisites"></a>Wymagania wstępne

Możesz wykonać czynności opisane w Azure Cloud Shell, na maszynie wirtualnej platformy Azure lub na komputerze lokalnym. Upewnij się, że masz [zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Krok 1. uwierzytelnianie przy użyciu usługi Azure AD

Zacznij od uwierzytelnienia w usłudze Azure AD przy użyciu narzędzia interfejsu wiersza polecenia platformy Azure. Ten krok nie jest wymagany w Azure Cloud Shell.

```
az login
```

Polecenie spowoduje uruchomienie okna przeglądarki na stronie uwierzytelniania usługi Azure AD. Wymaga to podania identyfikatora użytkownika i hasła usługi Azure AD.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Krok 2. Pobieranie tokenu dostępu usługi Azure AD

Wywołaj narzędzie interfejsu wiersza polecenia platformy Azure, aby uzyskać token dostępu dla uwierzytelnionego użytkownika usługi Azure AD z kroku 1 w celu uzyskania dostępu do Azure Database for PostgreSQL.

Przykład (dla chmury publicznej):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Powyższa wartość zasobu musi być określona dokładnie tak, jak pokazano. W przypadku innych chmur wartość zasobu może być wyszukiwana przy użyciu:

```azurecli-interactive
az cloud show
```

W przypadku interfejsu wiersza polecenia platformy Azure w wersji 2.0.71 lub nowszej polecenie można określić w następującej bardziej wygodnej wersji dla wszystkich chmur:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Po pomyślnym uwierzytelnieniu usługa Azure AD zwróci token dostępu:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Token jest bazowym ciągiem 64, który koduje wszystkie informacje o uwierzytelnionym użytkowniku i który jest przeznaczony dla usługi Azure Database for PostgreSQL.

> [!NOTE]
> Ważność tokenu dostępu jest w dowolnym miejscu od 5 minut do 60 minut. Zalecamy uzyskanie tokenu dostępu tuż przed zainicjowaniem logowania do Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Krok 3. Używanie tokenu jako hasła do logowania za pomocą usługi PostgreSQL

Podczas łączenia należy użyć tokenu dostępu jako hasła użytkownika PostgreSQL.

W przypadku korzystania z `psql` klienta wiersza polecenia token dostępu musi być przekazywać przez `PGPASSWORD` zmienną środowiskową, ponieważ token dostępu przekracza długość hasła, która `psql` może akceptować bezpośrednio:

Przykład systemu Windows:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Przykład Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Teraz możesz inicjować połączenie z Azure Database for PostgreSQL, podobnie jak zwykle:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Ważne zagadnienia dotyczące nawiązywania połączenia:

* `user@tenant.onmicrosoft.com` jest nazwą użytkownika lub grupy usługi Azure AD, z którą próbujesz nawiązać połączenie
* Zawsze dołączaj nazwę serwera po nazwie użytkownika/grupy usługi Azure AD (np. `@mydb` )
* Upewnij się, że używasz dokładnego sposobu, w jaki nazwa użytkownika lub grupy usługi Azure AD jest wpisana
* Nazwy użytkowników i grup usługi Azure AD uwzględniają wielkość liter
* Podczas nawiązywania połączenia jako grupy należy użyć tylko nazwy grupy (np. `GroupName@mydb` )
* Jeśli nazwa zawiera spacje, użyj `\` przed każdym miejscem, aby ją wypróbować

Masz teraz uwierzytelnienie na serwerze PostgreSQL przy użyciu uwierzytelniania usługi Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Tworzenie użytkowników usługi Azure AD w Azure Database for PostgreSQL

Aby dodać użytkownika usługi Azure AD do bazy danych Azure Database for PostgreSQL, wykonaj następujące czynności po nawiązaniu połączenia (zobacz sekcję w dalszej części How to Connect):

1. Najpierw upewnij się, że użytkownik usługi Azure AD `<user>@yourtenant.onmicrosoft.com` jest prawidłowym użytkownikiem w dzierżawie usługi Azure AD.
2. Zaloguj się do swojego wystąpienia Azure Database for PostgreSQL jako użytkownik administracyjny usługi Azure AD.
3. Utwórz rolę `<user>@yourtenant.onmicrosoft.com` w Azure Database for PostgreSQL.
4. Utwórz `<user>@yourtenant.onmicrosoft.com` członka roli azure_ad_user. Ta usługa musi być określona tylko dla użytkowników usługi Azure AD.

**Przykład:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Uwierzytelnienie użytkownika za pomocą usługi Azure AD nie daje użytkownikowi żadnych uprawnień dostępu do obiektów w bazie danych Azure Database for PostgreSQL. Musisz przyznać użytkownikowi wymagane uprawnienia ręcznie.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Tworzenie grup usługi Azure AD w Azure Database for PostgreSQL

Aby włączyć grupę usługi Azure AD w celu uzyskania dostępu do bazy danych, należy użyć tego samego mechanizmu co w przypadku użytkowników, ale zamiast tego należy określić nazwę grupy:

**Przykład:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Gdy logujesz się, członkowie grupy będą używać osobistych tokenów dostępu, ale Podpisz z nazwą grupy określoną jako nazwa użytkownika.

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

Uwierzytelnianie usługi Azure AD w Azure Database for PostgreSQL zapewnia, że użytkownik istnieje na serwerze PostgreSQL i sprawdza poprawność tokenu przez zweryfikowanie zawartości tokenu. Wykonywane są następujące kroki walidacji tokenu:

- Token jest podpisany przez usługę Azure AD i nie został naruszony
- Token został wystawiony przez usługę Azure AD dla dzierżawy skojarzonej z serwerem
- Token nie wygasł
- Token jest przeznaczony dla zasobu Azure Database for PostgreSQL (a nie innego zasobu platformy Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrowanie istniejących użytkowników PostgreSQL do uwierzytelniania opartego na usłudze Azure AD

Możesz włączyć uwierzytelnianie usługi Azure AD dla istniejących użytkowników. Istnieją dwa przypadki, które należy wziąć pod uwagę:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Przypadek 1: PostgreSQL nazwa użytkownika jest zgodna z nazwą główną użytkownika usługi Azure AD

W przypadku nieprawdopodobnej sytuacji, w której istniejący użytkownicy już pasują do nazw użytkowników usługi Azure AD, można udzielić `azure_ad_user` im roli w celu umożliwienia im uwierzytelniania w usłudze Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Teraz będzie można zalogować się przy użyciu poświadczeń usługi Azure AD zamiast korzystać z wcześniej skonfigurowanego hasła użytkownika PostgreSQL.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Przypadek 2: PostgreSQL nazwa użytkownika różni się od nazwy głównej użytkownika usługi Azure AD

Jeśli użytkownik PostgreSQL nie istnieje w usłudze Azure AD lub ma inną nazwę użytkownika, możesz użyć grup usługi Azure AD do uwierzytelniania jako ten użytkownik PostgreSQL. Istniejące Azure Database for PostgreSQL użytkowników można migrować do usługi Azure AD, tworząc grupę usługi Azure AD o nazwie odpowiadającej użytkownikowi PostgreSQL, a następnie przyznając azure_ad_user rolę istniejącym użytkownikowi PostgreSQL:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Przyjęto założenie, że utworzono grupę "DBReadUser" w usłudze Azure AD. Użytkownicy należący do tej grupy będą teraz mogli logować się do bazy danych jako ten użytkownik.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z ogólnymi koncepcjami [uwierzytelniania Azure Active Directory za pomocą jednego serwera Azure Database for PostgreSQL](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
