---
title: Samouczek — korzystanie z maszyny wirtualnej z systemem Linux i aplikacji konsolowej ASP.NET do przechowywania wpisów tajnych w Azure Key Vault | Microsoft Docs
description: W tym samouczku dowiesz się, jak skonfigurować aplikację ASP.NET Core do odczytywania wpisu tajnego z magazynu kluczy platformy Azure.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81423152"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Samouczek: korzystanie z maszyny wirtualnej z systemem Linux i aplikacji platformy .NET do przechowywania wpisów tajnych w programie Azure Key Vault

Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API i parametry połączenia bazy danych, które są potrzebne do uzyskiwania dostępu do aplikacji, usług i zasobów IT.

W tym samouczku skonfigurujesz aplikację konsolową .NET do odczytywania informacji z Azure Key Vault przy użyciu zarządzanych tożsamości dla zasobów platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy
> * Przechowywanie wpisu tajnego w Key Vault
> * Tworzenie maszyny wirtualnej platformy Azure z systemem Linux
> * Włączanie [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej
> * Przyznaj uprawnienia wymagane do odczytania danych z Key Vault przez aplikację konsolową
> * Pobierz klucz tajny z Key Vault

Zanim przejdziemy do dalszych informacji, przeczytaj temat [podstawowe pojęcia związane z magazynem kluczy](../general/basic-concepts.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Usługi [git](https://git-scm.com/downloads).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Interfejs wiersza polecenia platformy Azure 2,0 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lub Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Zapoznanie się z tożsamością usługi zarządzanej

Azure Key Vault mogą bezpiecznie przechowywać poświadczenia, aby nie znajdowały się w kodzie, ale w celu ich pobrania należy uwierzytelnić się w Azure Key Vault. Jednak aby uwierzytelniać się w usłudze Key Vault, potrzebne są poświadczenia. Jest to klasyczny problem dotyczący uruchamiania. Dzięki platformie Azure i Azure Active Directory (Azure AD) tożsamość usługi zarządzanej (MSI) może zapewnić tożsamość ładowania początkowego, która znacznie ułatwia rozpoczęcie pracy.

Po włączeniu MSI dla usługi platformy Azure, takiej jak Virtual Machines, App Service lub funkcje, platforma Azure tworzy jednostkę usługi dla wystąpienia usługi w Azure Active Directory. Wprowadza ona poświadczenia dla jednostki usługi do wystąpienia usługi.

![Tożsamość usługi zarządzanej](../media/MSI.png)

Następnie Twój kod wywołuje lokalną usługę metadanych dostępną w zasobie platformy Azure, aby uzyskać token dostępu.
W celu uwierzytelniania w usłudze Azure Key Vault kod używa tokenu dostępu otrzymanego z lokalnego elementu MSI_ENDPOINT.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą `az group create` polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów w lokalizacji zachodnie stany USA. Wybierz nazwę grupy zasobów i Zastąp `YourResourceGroupName` ją w następującym przykładzie:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Możesz używać tej grupy zasobów w całym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utwórz magazyn kluczy w grupie zasobów. Podaj następujące informacje:

* Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry, litery i łączniki (0-9, a-z, A-Z i \- ).
* Nazwa grupy zasobów
* Lokalizacja: **zachodnie stany USA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

W tym momencie tylko Twoje konto platformy Azure ma autoryzację do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Teraz można dodać wpis tajny. W rzeczywistym scenariuszu można przechowywać parametry połączenia SQL lub inne informacje, które należy zachować bezpiecznie, ale udostępnić aplikację.

W tym samouczku wpisz następujące polecenia, aby utworzyć wpis tajny w magazynie kluczy. Wpis tajny ma nazwę **AppSecret** , a jego wartość to **hasło**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz maszynę wirtualną za `az vm create` pomocą polecenia.

W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie **myVM** i dodane konto użytkownika o nazwie **azureuser**. `--generate-ssh-keys` Parametr US używany do automatycznego generowania klucza SSH i umieszczania go w lokalizacji domyślnej (**~/.SSH**). Aby zamiast niego użyć określonego zestawu kluczy, skorzystaj z opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja tworzenia maszyny wirtualnej zakończyła się pomyślnie.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj `publicIpAddress` dane wyjściowe z maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w późniejszych krokach.

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej

Utwórz tożsamość przypisaną przez system do maszyny wirtualnej, uruchamiając następujące polecenie:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Dane wyjściowe polecenia powinny wyglądać następująco:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Zanotuj wartość parametru `systemAssignedIdentity`. Używasz go w następnym kroku.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Przyznawanie tożsamości maszyny wirtualnej uprawnienia do usługi Key Vault

Teraz możesz przyznać utworzonej tożsamości uprawnienia do usługi Key Vault. Uruchom następujące polecenie:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logowanie się do maszyny wirtualnej

Zaloguj się do maszyny wirtualnej za pomocą terminalu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Instalowanie programu .NET Core w systemie Linux

Na maszynie wirtualnej z systemem Linux:

Zarejestruj klucz produktu firmy Microsoft jako zaufany, uruchamiając następujące polecenia:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Skonfiguruj żądaną wersję źródła danych hosta na podstawie systemu operacyjnego:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Zainstaluj platformę .NET i sprawdź wersję:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Uruchom następujące polecenia. W konsoli programu powinna zostać wyświetlona wartość "Hello world".

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Edytowanie aplikacji konsolowej w celu pobrania klucza tajnego

Otwórz plik Program.cs i Dodaj następujące pakiety:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Jest to dwuetapowy proces służący do zmiany pliku klasy, aby umożliwić aplikacji dostęp do wpisu tajnego w magazynie kluczy.

1. Pobierz token z lokalnego punktu końcowego MSI na maszynie wirtualnej, która z kolei pobiera token z Azure Active Directory.
1. Przekaż token, aby Key Vault i pobrać wpis tajny.

   Edytuj plik klasy, aby zawierał następujący kod:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

Teraz wiesz już, jak wykonywać operacje przy użyciu Azure Key Vault w aplikacji .NET działającej na maszynie wirtualnej platformy Azure z systemem Linux.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie powiązane zasoby nie będą już potrzebne, można je usunąć. W tym celu wybierz grupę zasobów maszyny wirtualnej i kliknij pozycję **Usuń**.

Usuń magazyn kluczy przy użyciu polecenia `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
