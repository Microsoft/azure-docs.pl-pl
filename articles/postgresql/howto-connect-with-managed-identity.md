---
title: Nawiązywanie połączenia z zarządzaną tożsamością — Azure Database for PostgreSQL — pojedynczy serwer
description: Informacje na temat sposobu nawiązywania połączenia i uwierzytelniania przy użyciu tożsamości zarządzanej w celu uwierzytelniania za pomocą Azure Database for PostgreSQL
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b0d6b083228ddab9d958bf3480b592f7ee0d892d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092633"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Nawiązywanie połączenia za pomocą tożsamości zarządzanej z usługą Azure Database for PostgreSQL

W tym artykule przedstawiono sposób użycia tożsamości przypisanej przez użytkownika dla maszyny wirtualnej platformy Azure (VM) w celu uzyskania dostępu do serwera Azure Database for PostgreSQL. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. 

Omawiane kwestie:
- Przyznaj maszynie wirtualnej dostęp do serwera Azure Database for PostgreSQL
- Utwórz użytkownika w bazie danych, który reprezentuje tożsamość przypisaną przez użytkownika maszyny wirtualnej
- Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej i używanie jej do wysyłania zapytań do serwera Azure Database for PostgreSQL
- Zaimplementuj pobieranie tokenu w przykładowej aplikacji w języku C#

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](../../articles/active-directory/managed-identities-azure-resources/overview.md). Jeśli nie masz jeszcze konta platformy Azure, przed kontynuowaniem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aby móc wykonywać wymagane operacje tworzenia zasobów i zarządzania rolami, Twoje konto musi mieć uprawnienia "właściciel" w odpowiednim zakresie (subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy z przypisaniem roli, zobacz [Przypisywanie ról platformy Azure do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- Wymagana jest maszyna wirtualna platformy Azure (na przykład uruchomienie Ubuntu Linux), która ma być używana do uzyskiwania dostępu do bazy danych przy użyciu tożsamości zarządzanej
- Potrzebujesz serwera bazy danych Azure Database for PostgreSQL, na którym skonfigurowano [uwierzytelnianie usługi Azure AD](howto-configure-sign-in-aad-authentication.md)
- Aby postępować zgodnie z przykładem w języku C#, najpierw Uzupełnij Przewodnik jak [nawiązać połączenie przy użyciu języka c#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika dla maszyny wirtualnej

Utwórz tożsamość w subskrypcji za pomocą polecenia [AZ Identity Create](/cli/azure/identity#az-identity-create) . Możesz użyć tej samej grupy zasobów, w której działa maszyna wirtualna, lub innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Aby skonfigurować tożsamość w poniższych krokach, użyj polecenia [AZ Identity show](/cli/azure/identity#az-identity-show) , aby zapisać identyfikator zasobu tożsamości i identyfikator klienta w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Teraz można przypisać tożsamość przypisaną przez użytkownika do maszyny wirtualnej za pomocą polecenia [AZ VM Identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Aby zakończyć instalację, Pokaż wartość identyfikatora klienta, która będzie potrzebna w następnych kilku krokach:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Tworzenie użytkownika PostgreSQL dla tożsamości zarządzanej

Teraz Połącz się jako użytkownik administratora usługi Azure AD z bazą danych PostgreSQL i uruchom następujące instrukcje SQL:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Zarządzana tożsamość ma teraz dostęp do uwierzytelniania przy użyciu nazwy użytkownika `myuser` (Zastąp wybraną nazwą).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Pobieranie tokenu dostępu z usługi metadanych wystąpienia platformy Azure

Aplikacja może teraz pobrać token dostępu z usługi metadanych wystąpienia platformy Azure i używać jej do uwierzytelniania w bazie danych.

Pobieranie tego tokenu odbywa się przez utworzenie żądania HTTP `http://169.254.169.254/metadata/identity/oauth2/token` i przekazanie następujących parametrów:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (pobrany wcześniej)

Zostanie przywrócony wynik JSON zawierający `access_token` pole — wartość tego długiego tekstu jest tokenem dostępu do tożsamości zarządzanego, który powinien być używany jako hasło podczas łączenia się z bazą danych.

W celach testowych można uruchomić następujące polecenia w powłoce. Należy pamiętać, `curl` że `jq` klient programu jest `psql` zainstalowany.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Nawiązano połączenie z bazą danych, która została wcześniej skonfigurowana.

## <a name="connecting-using-managed-identity-in-c"></a>Nawiązywanie połączenia przy użyciu tożsamości zarządzanej w języku C #

W tej sekcji pokazano, jak uzyskać token dostępu przy użyciu tożsamości zarządzanej przypisanej przez użytkownika przez maszynę wirtualną i używać jej do wywoływania Azure Database for PostgreSQL. Azure Database for PostgreSQL natywnie obsługuje uwierzytelnianie w usłudze Azure AD, dzięki czemu może on bezpośrednio akceptować tokeny dostępu uzyskane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Podczas tworzenia połączenia z usługą PostgreSQL, należy przekazać token dostępu w polu hasło.

Oto przykład kodu platformy .NET służący do otwierania połączenia z usługą PostgreSQL przy użyciu tokenu dostępu. Ten kod musi działać na maszynie wirtualnej w celu uzyskania dostępu do punktu końcowego tożsamości zarządzanej przypisanej przez użytkownika maszyny wirtualnej. Do korzystania z metody tokenu dostępu jest wymagany .NET Framework 4,6 lub nowszy lub .NET Core 2,2 lub nowszy. Zastąp wartości HOST, USER, DATABASE i CLIENT_ID.

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

Po uruchomieniu to polecenie zapewni dane wyjściowe podobne do tego:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z ogólnymi pojęciami dotyczącymi [uwierzytelniania Azure Active Directory przy użyciu Azure Database for PostgreSQL](concepts-aad-authentication.md)
