---
title: Szybki start — biblioteka klienta usługi Azure Key Vault dla platformy .NET (SDK v3)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienta platformy .NET (w wersji 3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: bdc686284711fb39e6cedb18aea4d1f5c9d1d318
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425007"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Szybki start: biblioteka klienta usługi Azure Key Vault dla platformy .NET (SDK v3)

Wprowadzenie do biblioteki klienta usługi Azure Key Vault dla platformy .NET. Wykonaj poniższe czynności, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

> [!NOTE]
> Ten przewodnik Szybki start korzysta z wersji 3.0.4 biblioteki klienta microsoft.Azure.KeyVault. Aby użyć najbardziej aktualnej wersji biblioteki klienta usługi Key Vault, zobacz [Biblioteka klienta usługi Azure Key Vault dla platformy .NET (SDK v4).](quick-create-net.md) 

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta usługi .NET usługi Key Vault, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Tworzenie i importowanie kluczy szyfrowania w ciągu kilku minut.
- Zmniejsz opóźnienia dzięki skalowaniu chmury i nadmiarowości globalnej.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj modułów HSM z walidacji FIPS 140-2 Poziomu 2.

[Dokumentacja](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | dokumentacji interfejsu API[Pakiet źródłowy kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Każdy magazyn kluczy musi mieć unikatową nazwę. W poniższych przykładach <> nazwa <nazwa magazynu kluczy.


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [SDK .NET Core 2.1 lub nowszym](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Narzędzie interfejsu wiersza polecenia](/cli/azure/install-azure-cli?view=azure-cli-latest) sieci owych lub [programu Azure PowerShell platformy Azure](/powershell/azure/overview)

Ten przewodnik Szybki start `dotnet`zakłada, że używasz poleceń [interfejsu wiersza polecenia Platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)i systemu Windows w terminalu systemu Windows (takich jak [PowerShell Core,](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6) [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)lub [Azure Cloud Shell).](https://shell.azure.com/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-new-net-console-app"></a>Tworzenie nowej aplikacji konsoli .NET

W oknie konsoli użyj `dotnet new` polecenia, aby utworzyć nową aplikację `akv-dotnet`konsoli .NET o nazwie .


```console
dotnet new console -n akvdotnet
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli zainstaluj bibliotekę klienta usługi Azure Key Vault dla platformy .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

W tym przewodniku Szybki start należy również zainstalować następujące pakiety:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki start korzysta z wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując kroki w [przewodniku Szybki start interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [przewodniku Szybki start usługi Azure PowerShell](quick-create-powershell.md)lub [przewodniku Szybki start w portalu Azure.](quick-create-portal.md) Alternatywnie można po prostu uruchomić polecenia interfejsu wiersza polecenia platformy Azure poniżej.

> [!Important]
> Każdy magazyn kluczy musi mieć unikatową nazwę. W poniższych przykładach <> nazwa <nazwa magazynu kluczy.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji .NET opartej na chmurze jest z tożsamością zarządzaną; Zobacz [Korzystanie z tożsamości zarządzanej usługi App Service, aby uzyskać dostęp do usługi Azure Key Vault, aby](../general/managed-identity.md) uzyskać szczegółowe informacje. Jednak ze względu na prostotę ten przewodnik Szybki start tworzy aplikację konsoli .NET. Uwierzytelnianie aplikacji klasycznej za pomocą platformy Azure wymaga użycia jednostki usługi i zasad kontroli dostępu.

Utwórz zasadę usługi przy użyciu polecenia Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ta operacja zwróci serię par kluczy / wartości. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Zanotuj clientId i clientSecret, ponieważ użyjemy ich w kroku [Uwierzytelnianie do magazynu kluczy](#authenticate-to-your-key-vault) poniżej.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udzielanie podmiotowi usługi dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który udziela uprawnień do jednostki usługi, przekazując clientId do [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) polecenia. Nadaj jednostce usługi get, list i set permissions for both keys and secrets.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Model obiektu

Biblioteka klienta usługi Azure Key Vault dla platformy .NET umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokażą, jak ustawić klucz tajny i pobrać klucz tajny.

Cała aplikacja konsoli jest https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnetdostępna pod adresem .

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy do górnej części kodu:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Uwierzytelnij się w magazynie kluczy

Ten program szybki start platformy .NET opiera się na zmiennych środowiskowych do przechowywania poświadczeń, które nie powinny być umieszczane w kodzie. 

Przed utworzeniem i uruchomieniem `setx` aplikacji użyj `akvClientId`polecenia, `akvTenantId`aby `akvSubscriptionId` ustawić `akvClientSecret`zmienne , , i środowiskowe na wartości, które zostały wymienione powyżej.

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**Macos**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Przypisz te zmienne środowiskowe do ciągów w kodzie, a następnie uwierzytelnij aplikację, przekazując je do [klasy KeyVaultClient:](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Zapisywanie tajemnicy

Teraz, gdy aplikacja jest uwierzytelniona, można umieścić klucz tajny w keyvault przy użyciu [Metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Wymaga `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`to adresu URL magazynu kluczy, który jest w formularzu . Wymaga to również nazwy dla tajemnicy - używamy "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Można sprawdzić, czy klucz tajny został ustawiony za pomocą polecenia [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobieranie klucza tajnego

Teraz można pobrać wcześniej ustawioną wartość za pomocą [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Twój sekret jest `keyvaultSecret.Value;`teraz zapisywany jako .

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, zapis tajny i pobrano ten klucz tajny. Zobacz [całą aplikację konsoli w GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Aby dowiedzieć się więcej o programie Key Vault i integruj go z aplikacjami, przejdź do poniższych artykułów.

- Implementowanie [uwierzytelniania usługi do usługi w usłudze Azure Key Vault przy użyciu platformy .NET](../general/service-to-service-authentication.md)
- Przeczytaj [omówienie usługi Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dla deweloperów usługi Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](../general/best-practices.md)
