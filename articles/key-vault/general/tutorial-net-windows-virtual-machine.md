---
title: Samouczek — używanie Azure Key Vault z maszyną wirtualną z systemem Windows w środowisku .NET | Microsoft Docs
description: W tym samouczku skonfigurujesz aplikację ASP.NET Core w celu odczytu wpisu tajnego z magazynu kluczy.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422865"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Samouczek: używanie Azure Key Vault z maszyną wirtualną z systemem Windows w środowisku .NET

Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskiwania dostępu do aplikacji, usług i zasobów IT.

W ramach tego samouczka nauczysz się, jak uzyskać aplikację konsolową do odczytu informacji z Azure Key Vault. W tym celu należy użyć zarządzanych tożsamości dla zasobów platformy Azure. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Tworzenie magazynu kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz [zarządzaną tożsamość](../../active-directory/managed-identities-azure-resources/overview.md) maszyny wirtualnej.
> * Przypisz uprawnienia do tożsamości maszyny wirtualnej.

Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów Windows, Mac i Linux:
  * [Git](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musisz mieć zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informacje o tożsamości usługi zarządzanej

Azure Key Vault przechowuje poświadczenia bezpiecznie, więc nie są wyświetlane w kodzie. Aby jednak pobrać klucze, musisz uwierzytelnić się w usłudze Azure Key Vault. W tym celu potrzebujesz poświadczenia. To klasyczny przykład dylematu dotyczącego uruchamiania. Tożsamość usługi zarządzanej rozwiązuje ten problem poprzez zapewnienie _tożsamości uruchamiania_, która upraszcza ten proces.

Po włączeniu MSI dla usługi platformy Azure, takiej jak Azure Virtual Machines, Azure App Service lub Azure Functions, platforma Azure tworzy jednostkę [usługi](basic-concepts.md). Plik MSI robi to w przypadku wystąpienia usługi w Azure Active Directory (Azure AD) i wprowadza poświadczenia nazwy głównej usługi do tego wystąpienia. 

![Tożsamość usługi zarządzanej](../media/MSI.png)

Następnie w celu uzyskania tokenu dostępu kod wywołuje lokalną usługę metadanych, która jest dostępna w ramach zasobu platformy Azure. Aby uwierzytelnić się w usłudze Azure Key Vault, kod używa tokenu dostępu pobieranego z lokalnego punktu końcowego MSI. 

## <a name="create-resources-and-assign-permissions"></a>Tworzenie zasobów i przypisywanie uprawnień

Przed rozpoczęciem kodowania należy utworzyć pewne zasoby, umieścić wpis tajny w magazynie kluczy i przypisać uprawnienia.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

Ten przykład tworzy grupę zasobów w lokalizacji zachodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Nowo utworzona grupa zasobów zostanie użyta w tym samouczku.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Utwórz magazyn kluczy i wypełnij go wpisem tajnym

Utwórz magazyn kluczy w grupie zasobów, dostarczając polecenie AZ Key [magazynu Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) z następującymi informacjami:

* Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)
* Nazwa grupy zasobów
* Lokalizacja: **zachodnie stany USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
W tym momencie Twoje konto platformy Azure jest jedyną osobą, która ma uprawnienia do wykonywania operacji na tym nowym magazynie kluczy.

Teraz Dodaj wpis tajny do magazynu kluczy za pomocą polecenia AZ Key Key [Secret Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**, wprowadź następujące polecenie:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz maszynę wirtualną za pomocą jednej z następujących metod:

* [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
Utwórz tożsamość przypisaną do systemu dla maszyny wirtualnej za pomocą polecenia [AZ VM Identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj tożsamość przypisaną przez system, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia byłyby następujące: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny wirtualnej
Przypisz wcześniej utworzone uprawnienia tożsamości do magazynu kluczy za pomocą polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Zaloguj się do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami podanymi w temacie [łączenie i logowanie do maszyny wirtualnej platformy Azure z systemem Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Konfigurowanie aplikacji konsolowej

Utwórz aplikację konsolową i zainstaluj wymagane pakiety przy użyciu `dotnet` polecenia.

### <a name="install-net-core"></a>Instalowanie programu .NET Core

Aby zainstalować program .NET Core, przejdź do strony [plików do pobrania platformy .NET](https://www.microsoft.com/net/download) .

### <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Otwórz wiersz polecenia.

"Hello world" można wydrukować do konsoli programu, uruchamiając następujące polecenia:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Zainstaluj pakiety

W oknie konsoli Zainstaluj pakiety .NET wymagane do tego przewodnika Szybki Start:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Edytowanie aplikacji konsolowej

Otwórz plik *program.cs* i Dodaj następujące pakiety:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edytuj plik klasy, aby zawierał kod w następującym procesie trzech kroków:

1. Pobieranie tokenu z lokalnego punktu końcowego tożsamości usługi zarządzanej na maszynie wirtualnej. Spowoduje to również pobranie tokenu z usługi Azure AD.
2. Przekaż token do magazynu kluczy, a następnie Pobierz wpis tajny. 
3. Dodaj nazwę magazynu i nazwę wpisu tajnego do żądania.

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

Gdy nie są już potrzebne, Usuń maszynę wirtualną i Magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
