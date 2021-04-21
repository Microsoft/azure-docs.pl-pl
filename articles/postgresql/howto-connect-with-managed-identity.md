---
title: Nawiązywanie połączenia przy użyciu tożsamości zarządzanej — Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się więcej na temat nawiązywania połączenia i uwierzytelniania przy użyciu tożsamości zarządzanej na potrzeby uwierzytelniania Azure Database for PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: beb5930e98a5c5498349dc3aa773423ee5d281bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792475"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Nawiązywanie połączenia za pomocą tożsamości zarządzanej z usługą Azure Database for PostgreSQL

W tym artykule pokazano, jak używać tożsamości przypisanej przez użytkownika dla maszyny wirtualnej platformy Azure w celu uzyskania dostępu do Azure Database for PostgreSQL wirtualnej. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. 

Omawiane kwestie:
- Udzielanie maszynie wirtualnej dostępu do Azure Database for PostgreSQL wirtualnej
- Tworzenie użytkownika w bazie danych, który reprezentuje tożsamość przypisaną przez użytkownika maszyny wirtualnej
- Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej i używanie go do wykonywania zapytań Azure Database for PostgreSQL serwera
- Implementowanie pobierania tokenu w przykładowej aplikacji w języku C#

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](../../articles/active-directory/managed-identities-azure-resources/overview.md). Jeśli nie masz jeszcze konta platformy Azure, przed kontynuowaniem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aby wykonać wymagane tworzenie zasobów i zarządzanie rolami, Twoje konto musi mieć uprawnienia "Właściciel" w odpowiednim zakresie (subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy w przypisaniu ról, zobacz Przypisywanie ról platformy Azure w [celu zarządzania dostępem do zasobów subskrypcji platformy Azure.](../../articles/role-based-access-control/role-assignments-portal.md)
- Potrzebujesz maszyny wirtualnej platformy Azure (na przykład uruchomionej Ubuntu Linux), która ma być potrzebna do uzyskiwania dostępu do bazy danych przy użyciu tożsamości zarządzanej
- Potrzebny jest serwer Azure Database for PostgreSQL bazy danych ze skonfigurowanym [uwierzytelnianiem usługi Azure AD](howto-configure-sign-in-aad-authentication.md)
- Aby postępować zgodnie z przykładem w języku C#, najpierw ukończ przewodnik Nawiązywanie połączenia [przy użyciu języka C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika dla maszyny wirtualnej

Utwórz tożsamość w subskrypcji za pomocą [polecenia az identity create.](/cli/azure/identity#az_identity_create) Możesz użyć tej samej grupy zasobów, w których działa maszyna wirtualna, lub innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Aby skonfigurować tożsamość w poniższych krokach, użyj [polecenia az identity show](/cli/azure/identity#az_identity_show) do przechowywania identyfikatora zasobu tożsamości i identyfikatora klienta w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Teraz możemy przypisać tożsamość przypisaną przez użytkownika do maszyny wirtualnej za pomocą [polecenia az vm identity assign:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Aby zakończyć instalację, wyświetl wartość identyfikatora klienta, która będzie potrzebna w kilku następnych krokach:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Tworzenie użytkownika postgreSQL dla tożsamości zarządzanej

Teraz połącz się jako administrator usługi Azure AD z bazą danych PostgreSQL i uruchom następujące instrukcje SQL:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Tożsamość zarządzana ma teraz dostęp podczas uwierzytelniania przy użyciu nazwy użytkownika `myuser` (zastąp nazwą).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Pobieranie tokenu dostępu z usługi Azure Instance Metadata Service

Aplikacja może teraz pobrać token dostępu z usługi Azure Instance Metadata Service i używać go do uwierzytelniania w bazie danych.

Pobieranie tokenu odbywa się przez wykonanie żądania HTTP do usługi `http://169.254.169.254/metadata/identity/oauth2/token` i przekazanie następujących parametrów:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (pobrane wcześniej)

Otrzymasz wynik JSON zawierający pole — ta długa wartość tekstowa to token dostępu tożsamości zarządzanej, którego należy użyć jako hasła podczas nawiązywania połączenia z bazą `access_token` danych.

W celach testowych możesz uruchomić następujące polecenia w swojej powłoki. Należy pamiętać, `curl` że potrzebne są , i zainstalowany `jq` `psql` klient.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Masz teraz połączenie z wcześniej skonfigurowaną bazą danych.

## <a name="connecting-using-managed-identity-in-c"></a>Nawiązywanie połączenia przy użyciu tożsamości zarządzanej w języku C #

W tej sekcji pokazano, jak uzyskać token dostępu przy użyciu przypisanej przez użytkownika tożsamości zarządzanej maszyny wirtualnej i użyć go do wywołania Azure Database for PostgreSQL. Azure Database for PostgreSQL natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Podczas tworzenia połączenia z usługą PostgreSQL należy przekazać token dostępu w polu hasło.

Oto przykładowy kod .NET otwierający połączenie z usługą PostgreSQL przy użyciu tokenu dostępu. Ten kod należy uruchomić na maszynie wirtualnej, aby uzyskać dostęp do punktu końcowego tożsamości zarządzanej przypisanej przez użytkownika. .NET Framework tokenu dostępu jest wymagany program .NET Core 2.2 lub wyższy w programie 4.6 lub wyższym. Zastąp wartości HOST, USER, DATABASE i CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Po uruchomieniu to polecenie da dane wyjściowe podobne do tych:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z ogólnymi pojęciami Azure Active Directory [uwierzytelniania przy użyciu Azure Database for PostgreSQL](concepts-aad-authentication.md)
