---
title: Samouczek — używanie usługi Azure Key Vault z maszyną wirtualną systemu Windows w programie .NET | Dokumenty firmy Microsoft
description: W tym samouczku skonfigurujesz ASP.NET podstawową aplikację do odczytu klucza tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 6ba78a44af7beb9b5b79aa1a87e08f5a82589cce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422865"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Samouczek: Używanie usługi Azure Key Vault z maszyną wirtualną systemu Windows w programie .NET

Usługa Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych, które są potrzebne do uzyskania dostępu do aplikacji, usług i zasobów IT.

W tym samouczku dowiesz się, jak uzyskać aplikację konsoli do odczytu informacji z usługi Azure Key Vault. Aby to zrobić, należy użyć tożsamości zarządzanych dla zasobów platformy Azure. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Tworzenie magazynu kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md) maszyny wirtualnej.
> * Przypisywanie uprawnień do tożsamości maszyny Wirtualnej.

Przed rozpoczęciem przeczytaj [podstawowe pojęcia programu Key Vault](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów Windows, Mac i Linux:
  * [Git](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musi być zainstalowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informacje o tożsamości usługi zarządzanej

Usługa Azure Key Vault bezpiecznie przechowuje poświadczenia, więc nie są one wyświetlane w kodzie. Aby jednak pobrać klucze, musisz uwierzytelnić się w usłudze Azure Key Vault. W tym celu potrzebujesz poświadczenia. To klasyczny przykład dylematu dotyczącego uruchamiania. Tożsamość usługi zarządzanej rozwiązuje ten problem poprzez zapewnienie _tożsamości uruchamiania_, która upraszcza ten proces.

Po włączeniu msi dla usługi platformy Azure, takich jak maszyny wirtualne platformy Azure, usługa azure app service lub usługi Azure Functions, platforma Azure tworzy [jednostkę usługi.](basic-concepts.md) MSI robi to dla wystąpienia usługi w usłudze Azure Active Directory (Azure AD) i wstrzykuje poświadczenia jednostki usługi do tego wystąpienia. 

![Tożsamość usługi zarządzanej](../media/MSI.png)

Następnie, aby uzyskać token dostępu, kod wywołuje lokalną usługę metadanych, która jest dostępna w zasobie platformy Azure. Aby uwierzytelnić się w usłudze Azure Key Vault, kod używa tokenu dostępu, który pobiera z lokalnego punktu końcowego MSI. 

## <a name="create-resources-and-assign-permissions"></a>Tworzenie zasobów i przypisywanie uprawnień

Przed rozpoczęciem kodowania należy utworzyć niektóre zasoby, umieścić klucz tajny w magazynie kluczy i przypisać uprawnienia.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

W tym przykładzie utworzy grupę zasobów w lokalizacji zachodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Nowo utworzona grupa zasobów będzie używana w tym samouczku.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Tworzenie magazynu kluczy i wypełnianie go kluczem tajnym

Utwórz magazyn kluczy w grupie zasobów, udostępniając polecenie [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) z następującymi informacjami:

* Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)
* Nazwa grupy zasobów
* Lokalizacja: **Zachodnie stany USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
W tym momencie twoje konto platformy Azure jest jedynym, który jest autoryzowany do wykonywania operacji na tym nowym magazynie kluczy.

Teraz dodaj klucz tajny do magazynu kluczy za pomocą polecenia [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Aby utworzyć klucz tajny w magazynie kluczy o nazwie **AppSecret**, wprowadź następujące polecenie:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz maszynę wirtualną przy użyciu jednej z następujących metod:

* [Narzędzie interfejsu wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
Utwórz tożsamość przypisaną systemowo dla maszyny wirtualnej za pomocą polecenia [przypisywania tożsamości az vm:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj tożsamość przypisaną do systemu, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia to: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny Wirtualnej
Przypisz wcześniej utworzone uprawnienia tożsamości do magazynu kluczy za pomocą polecenia [az keyvault set-policy:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Logowanie się do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami w [obszarze Połącz i zaloguj się na maszynie wirtualnej platformy Azure z systemem Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Konfigurowanie aplikacji konsoli

Utwórz aplikację konsoli i zainstaluj wymagane `dotnet` pakiety za pomocą polecenia.

### <a name="install-net-core"></a>Instalowanie programu .NET Core

Aby zainstalować program .NET Core, przejdź do strony [pobierania .NET.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Otwórz wiersz polecenia.

"Hello World" można wydrukować na konsoli, uruchamiając następujące polecenia:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Zainstaluj pakiety

W oknie konsoli zainstaluj pakiety .NET wymagane dla tego przewodnika Szybki start:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Edytowanie aplikacji konsoli

Otwórz plik *Program.cs* i dodaj następujące pakiety:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edytuj plik klasy, aby zawierał kod w następującym trzyetapowym procesie:

1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej. W ten sposób pobiera również token z usługi Azure AD.
2. Przekaż token do magazynu kluczy, a następnie pobierz swój klucz tajny. 
3. Dodaj nazwę przechowalni i nazwę tajnego do żądania.

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }
        
        static string FetchSecretValueFromKeyVault(string token)
        {
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej z systemem Windows.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń maszynę wirtualną i magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
