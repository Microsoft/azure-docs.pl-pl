---
title: Szybki start — Azure Key Vault klienta certyfikatów dla programu .NET (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z usługi Azure Key Vault przy użyciu biblioteki klienta platformy .NET (wersja 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 9188079a93c9c2688a310413401e90014b278bf8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813420"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Szybki start: Azure Key Vault klienta certyfikatów dla platformy .NET (ZESTAW SDK 4)

Wprowadzenie do biblioteki klienta Azure Key Vault certyfikatów dla programu .NET. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn certyfikatów. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. Z tego przewodnika Szybki start dowiesz się, jak tworzyć, pobierać i usuwać certyfikaty z usługi Azure Key Vault przy użyciu biblioteki klienta platformy .NET

Key Vault zasobów biblioteki klienta:

[Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.security.keyvault.certificates)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Aby uzyskać więcej informacji na Key Vault i certyfikatów, zobacz:
- [Key Vault omówienie](../general/overview.md)
- [Certificates Overview (Omówienie certyfikatów).](about-certificates.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [Zestaw SDK platformy .NET Core 3.1 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Interfejs Key Vault — można go utworzyć przy użyciu interfejsu [Azure Portal,](../general/quick-create-portal.md) [interfejsu wiersza](../general/quick-create-cli.md)polecenia platformy Azure [lub Azure PowerShell.](../general/quick-create-powershell.md)

Ten przewodnik Szybki start korzysta z interfejsu `dotnet` wiersza polecenia platformy Azure

## <a name="setup"></a>Konfiguracja

Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą [biblioteki klienta tożsamości platformy Azure).](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

#### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, które będą udzielać uprawnień do certyfikatu kontu użytkownika

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Tworzenie nowej aplikacji konsolowej .NET

1. W powłoki poleceń uruchom następujące polecenie, aby utworzyć projekt o nazwie `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Zmień katalog na nowo utworzony *katalog key-vault-console-app* i uruchom następujące polecenie, aby skompilować projekt:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Instalowanie pakietów

Z powłoki poleceń zainstaluj bibliotekę klienta Azure Key Vault certyfikatu dla programu .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

W tym przewodniku Szybki start musisz również zainstalować bibliotekę klienta zestawu Azure SDK dla tożsamości platformy Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ta aplikacja używa nazwy magazynu kluczy jako zmiennej środowiskowej o nazwie `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS lub Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Model obiektów

Biblioteka Azure Key Vault klienta certyfikatów dla programu .NET umożliwia zarządzanie certyfikatami. W [sekcji Przykłady](#code-examples) kodu pokazano, jak utworzyć klienta, ustawić certyfikat, pobrać certyfikat i usunąć certyfikat.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy na początku programu *Program.cs:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie jest to klasa ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) z biblioteki tożsamości platformy [Azure,](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania w magazynie kluczy, zobacz [Developer's Guide (Przewodnik dewelopera).](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Zapisywanie certyfikatu

Dla uproszczenia w tym przykładzie można użyć certyfikatu z podpisem własnym z domyślnymi zasadami wystawiania. W tym zadaniu użyj [metody StartCreateCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) Parametry metody akceptują nazwę certyfikatu i [zasady certyfikatu](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy).

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Jeśli nazwa certyfikatu istnieje, powyższy kod utworzy nową wersję tego certyfikatu.

### <a name="retrieve-a-certificate"></a>Pobieranie certyfikatu

Teraz możesz pobrać wcześniej utworzony certyfikat za pomocą [metody GetCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync)

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Na koniec usuńmy i przeczyścimy certyfikat z magazynu kluczy przy użyciu metod [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) i [PurgeDeletedCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Przykładowy kod

Zmodyfikuj aplikację konsoli .NET Core, aby współdziałała z Key Vault, wykonując następujące kroki:

- Zastąp kod w pliku *Program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testowanie i weryfikowanie

Wykonaj następujące polecenie, aby skompilować projekt

```dotnetcli
dotnet build
```

Zostanie wyświetlona odmiana następujących danych wyjściowych:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, przechowywano certyfikat i pobrano ten certyfikat. 

Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, zobacz następujące artykuły:

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zapoznaj się [z omówieniem certyfikatów](about-certificates.md)
- Zobacz [samouczek dotyczący dostępu Key Vault z App Service aplikacji](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [samouczek dotyczący Key Vault dostępu z maszyny wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
