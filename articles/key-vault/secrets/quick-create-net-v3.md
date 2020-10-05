---
title: Szybki Start — Azure Key Vaulta Biblioteka kliencka dla platformy .NET (SDK v3)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej platformy .NET (wersja 3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078879"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Szybki Start: Azure Key Vaulta Biblioteka kliencka dla platformy .NET (SDK v3)

Wprowadzenie do biblioteki klienta Azure Key Vault dla platformy .NET. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

> [!NOTE]
> Ten przewodnik Szybki Start używa wersji 3.0.4 biblioteki klienta Microsoft. Azure. Aby użyć najnowszej wersji biblioteki klienta Key Vault, zobacz [Azure Key Vault biblioteki klienckiej dla platformy .NET (SDK v4)](quick-create-net.md). 

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta Key Vault dla platformy .NET, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Utwórz i zaimportuj klucze szyfrowania w kilka minut.
- Ogranicz opóźnienia dzięki skali chmury i globalnej nadmiarowości.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

[Dokumentacja](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw .NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/)

W tym przewodniku szybki start założono, że korzystasz z systemu `dotnet` , [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)i poleceń systemu Windows w terminalu z systemem Windows (na przykład [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)lub [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-new-net-console-app"></a>Utwórz nową aplikację konsolową platformy .NET

W oknie konsoli Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową .NET o nazwie `akv-dotnet` .


```console
dotnet new console -n akvdotnet
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli programu Zainstaluj Azure Key Vaultą bibliotekę kliencką dla platformy .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

W tym przewodniku szybki start konieczne będzie zainstalowanie następujących pakietów:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla platformy .NET umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokazują, jak ustawić wpis tajny i pobrać wpis tajny.

Cała Aplikacja konsolowa jest dostępna pod adresem https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet .

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Uwierzytelnianie w magazynie kluczy

Ten przewodnik Szybki Start platformy .NET opiera się na zmiennych środowiskowych do przechowywania poświadczeń, które nie powinny być umieszczane w kodzie. 

Przed rozpoczęciem kompilowania i uruchamiania aplikacji użyj `setx` polecenia, aby ustawić `akvClientId` `akvClientSecret` `akvTenantId` `akvSubscriptionId` zmienne środowiskowe,, i na wartości zanotowane powyżej.

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

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Przypisz te zmienne środowiskowe do ciągów w kodzie, a następnie Uwierzytelnij aplikację, przekazując je do [klasy KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Zapisz klucz tajny

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz tajny w magazynie kluczy przy użyciu [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , która wymaga adresu URL Twojego magazynu, który znajduje się w formularzu `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Wymaga również nazwy wpisu tajnego — używamy hasła ". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Można sprawdzić, czy wpis tajny został ustawiony za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Teraz można pobrać wcześniej ustawioną wartość za pomocą [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Wpis tajny jest teraz zapisywany jako `keyvaultSecret.Value;` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano wpis tajny i pobrano ten klucz tajny. Zobacz [całą aplikację konsolową w](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)serwisie GitHub.

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Implementowanie uwierzytelniania między usługami [w celu Azure Key Vault przy użyciu platformy .NET](../general/service-to-service-authentication.md)
- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
