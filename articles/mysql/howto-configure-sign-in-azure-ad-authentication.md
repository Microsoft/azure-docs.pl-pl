---
title: Użyj Azure Active Directory-Azure Database for MySQL
description: Dowiedz się więcej na temat konfigurowania Azure Active Directory (Azure AD) do uwierzytelniania za pomocą Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 492e56e09129f9d47b863624cd72cd508801c143
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728270"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Używanie Azure Active Directory do uwierzytelniania za pomocą programu MySQL

W tym artykule opisano kroki konfigurowania Azure Active Directory dostępu za pomocą Azure Database for MySQL i sposobu nawiązywania połączenia przy użyciu tokenu usługi Azure AD.

> [!IMPORTANT]
> Uwierzytelnianie Azure Active Directory jest dostępne tylko dla MySQL 5,7 i nowszych.

## <a name="setting-the-azure-ad-admin-user"></a>Ustawianie użytkownika administratora usługi Azure AD

Tylko użytkownik administrator usługi Azure AD może tworzyć/włączać użytkowników w ramach uwierzytelniania opartego na usłudze Azure AD. Aby utworzyć użytkownika administratora usługi Azure AD, wykonaj następujące czynności.

1. W Azure Portal wybierz wystąpienie Azure Database for MySQL, które chcesz włączyć dla usługi Azure AD.
2. W obszarze Ustawienia wybierz pozycję Administrator Active Directory:

![Ustawianie administratora usługi Azure AD][2]

3. Wybierz prawidłowego użytkownika usługi Azure AD w dzierżawie klienta, aby być administratorem usługi Azure AD.

> [!IMPORTANT]
> Podczas konfigurowania administratora nowy użytkownik zostanie dodany do serwera Azure Database for MySQL z uprawnieniami pełnego administratora.

Tylko jeden administrator usługi Azure AD można utworzyć na serwerze MySQL, a wybór innego z nich spowoduje zastąpienie istniejącego administratora usługi Azure AD skonfigurowanego dla serwera.

Po skonfigurowaniu administratora możesz teraz zalogować się:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Nawiązywanie połączenia z Azure Database for MySQL przy użyciu usługi Azure AD

Poniższy diagram wysokiego poziomu podsumowuje przepływ pracy przy użyciu uwierzytelniania usługi Azure AD z Azure Database for MySQL:

![przepływ uwierzytelniania][1]

Zaprojektowano integrację usługi Azure AD w celu współpracy z typowymi narzędziami MySQL, takimi jak interfejs wiersza polecenia MySQL, które nie są oparte na usłudze Azure AD i obsługują Określanie nazwy użytkownika i hasła podczas łączenia się z bazą danych MySQL. Token usługi Azure AD jest przekazywany jako hasło, jak pokazano na powyższym rysunku.

Obecnie przetestowano następujących klientów:

- MySQLWorkbench 
- Interfejs wiersza polecenia MySQL

Przetestowano również najpopularniejsze sterowniki aplikacji, na końcu tej strony można zobaczyć szczegóły.

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

Wywołaj narzędzie interfejsu wiersza polecenia platformy Azure, aby uzyskać token dostępu dla uwierzytelnionego użytkownika usługi Azure AD z kroku 1 w celu uzyskania dostępu do Azure Database for MySQL.

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
Korzystając z programu PowerShell, można uzyskać token dostępu za pomocą następującego polecenia:

```azurepowershell-interactive
$accessToken = Get-AzAccessToken -ResourceUrl https://ossrdbms-aad.database.windows.net
$accessToken.Token | out-file C:\temp\MySQLAccessToken.txt
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

Token jest bazowym ciągiem 64, który koduje wszystkie informacje o uwierzytelnionym użytkowniku i który jest przeznaczony dla usługi Azure Database for MySQL.

Ważność tokenu dostępu jest w dowolnym miejscu od ***5 minut do 60 minut***. Zalecamy uzyskanie tokenu dostępu tuż przed zainicjowaniem logowania do Azure Database for MySQL. Aby wyświetlić ważność tokenu, można użyć następującego polecenia programu PowerShell. 

```azurepowershell-interactive
$accessToken.ExpiresOn.DateTime
```

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Krok 3. Używanie tokenu jako hasła do logowania za pomocą programu MySQL

Podczas łączenia należy użyć tokenu dostępu jako hasła użytkownika programu MySQL. W przypadku korzystania z klientów GUI, takich jak MySQLWorkbench, można użyć opisanej powyżej metody do pobrania tokenu. 

#### <a name="using-mysql-cli"></a>Korzystanie z interfejsu wiersza polecenia MySQL
Korzystając z interfejsu wiersza polecenia, można nawiązać połączenie za pomocą tej krótkiej nazwy: 

**Przykład (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```
#### <a name="using-mysql-workbench"></a>Korzystanie z programu MySQL Workbench
* Uruchom program MySQL Workbench i kliknij opcję baza danych, a następnie kliknij pozycję "Połącz z bazą danych".
* W polu Nazwa hosta wprowadź nazwę FQDN MySQL na przykład. mydb.mysql.database.azure.com
* W polu Nazwa użytkownika wprowadź nazwę administratora Azure Active Directory MySQL i Dołącz ją przy użyciu nazwy serwera MySQL, a nie do nazwy FQDN, np. user@tenant.onmicrosoft.com@mydb
* W polu Hasło kliknij pozycję "przechowuj w magazynie" i wklej w tokenie dostępu z pliku, np. C:\temp\MySQLAccessToken.txt
* Kliknij kartę Zaawansowane i upewnij się, że jest zaznaczone pole wyboru Włącz wtyczkę uwierzytelniania zwykłego tekstu.
* Kliknij przycisk OK, aby nawiązać połączenie z bazą danych

#### <a name="important-considerations-when-connecting"></a>Ważne zagadnienia dotyczące nawiązywania połączenia:

* `user@tenant.onmicrosoft.com` jest nazwą użytkownika lub grupy usługi Azure AD, z którą próbujesz nawiązać połączenie
* Zawsze dołączaj nazwę serwera po nazwie użytkownika/grupy usługi Azure AD (np. `@mydb` )
* Upewnij się, że używasz dokładnego sposobu, w jaki nazwa użytkownika lub grupy usługi Azure AD jest wpisana
* Nazwy użytkowników i grup usługi Azure AD uwzględniają wielkość liter
* Podczas nawiązywania połączenia jako grupy należy użyć tylko nazwy grupy (np. `GroupName@mydb` )
* Jeśli nazwa zawiera spacje, użyj `\` przed każdym miejscem, aby ją wypróbować

Zwróć uwagę na ustawienie "Enable-zwykły tekst-wtyczka" — należy użyć podobnej konfiguracji z innymi klientami, aby upewnić się, że token jest wysyłany do serwera bez mieszania.

Masz teraz uwierzytelnienie na serwerze MySQL przy użyciu uwierzytelniania usługi Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Tworzenie użytkowników usługi Azure AD w Azure Database for MySQL

Aby dodać użytkownika usługi Azure AD do bazy danych Azure Database for MySQL, wykonaj następujące czynności po nawiązaniu połączenia (zobacz sekcję w dalszej części How to Connect):

1. Najpierw upewnij się, że użytkownik usługi Azure AD `<user>@yourtenant.onmicrosoft.com` jest prawidłowym użytkownikiem w dzierżawie usługi Azure AD.
2. Zaloguj się do swojego wystąpienia Azure Database for MySQL jako użytkownik administracyjny usługi Azure AD.
3. Utwórz użytkownika `<user>@yourtenant.onmicrosoft.com` w Azure Database for MySQL.

**Przykład:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

W przypadku nazw użytkowników, które przekraczają 32 znaków, zaleca się użycie aliasu, który ma być używany podczas nawiązywania połączenia: 

Przykład:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Uwierzytelnienie użytkownika za pomocą usługi Azure AD nie daje użytkownikowi żadnych uprawnień dostępu do obiektów w bazie danych Azure Database for MySQL. Musisz przyznać użytkownikowi wymagane uprawnienia ręcznie.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Tworzenie grup usługi Azure AD w Azure Database for MySQL

Aby włączyć grupę usługi Azure AD w celu uzyskania dostępu do bazy danych, należy użyć tego samego mechanizmu co w przypadku użytkowników, ale zamiast tego należy określić nazwę grupy:

**Przykład:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Gdy logujesz się, członkowie grupy będą używać osobistych tokenów dostępu, ale Podpisz z nazwą grupy określoną jako nazwa użytkownika.

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

Uwierzytelnianie usługi Azure AD w Azure Database for MySQL zapewnia, że użytkownik istnieje na serwerze MySQL i sprawdza poprawność tokenu przez zweryfikowanie zawartości tokenu. Wykonywane są następujące kroki walidacji tokenu:

-   Token jest podpisany przez usługę Azure AD i nie został naruszony
-   Token został wystawiony przez usługę Azure AD dla dzierżawy skojarzonej z serwerem
-   Token nie wygasł
-   Token jest przeznaczony dla zasobu Azure Database for MySQL (a nie innego zasobu platformy Azure)

## <a name="compatibility-with-application-drivers"></a>Zgodność ze sterownikami aplikacji

Większość sterowników jest obsługiwanych, jednak Pamiętaj, aby użyć ustawień do wysyłania hasła w postaci zwykłego tekstu, więc token zostanie wysłany bez modyfikacji.

* C/C++
  * libmysqlclient: obsługiwane
  * MySQL-Connector-c + +: obsługiwane
* Java
  * Łącznik/J (MySQL-Connector-Java): obsługiwane, musi korzystać z `useSSL` Ustawienia
* Python
  * Łącznik/Python: obsługiwane
* Ruby
  * mysql2: obsługiwane
* .NET
  * MySQL-Connector-net: obsługiwane, należy dodać wtyczkę dla mysql_clear_password
  * MySQL-NET/MySqlConnector: obsługiwane
* Node.js
  * mysqljs: nieobsługiwane (nie wysyłaj tokenu w postaci zwykłego tekstu bez poprawki)
  * Node-mysql2: obsługiwane
* Perl
  * DBD:: MySQL: obsługiwane
  * NET:: MySQL: nieobsługiwane
* Go
  * go-SQL-Driver: obsługiwane, Dodaj `?tls=true&allowCleartextPasswords=true` do parametrów połączenia

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z ogólnymi pojęciami dotyczącymi [uwierzytelniania Azure Active Directory przy użyciu Azure Database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
