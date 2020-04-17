---
title: Samouczek — szyfrowanie i odszyfrowywanie obiektów blob przy użyciu usługi Azure Key Vault
titleSuffix: Azure Storage
description: Dowiedz się, jak szyfrować i odszyfrowywać obiekt blob przy użyciu szyfrowania po stronie klienta za pomocą usługi Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c1d26fda57d665cc8d83f594f4efeebebc7bf139
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456893"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Samouczek — szyfrowanie i odszyfrowywanie obiektów blob przy użyciu usługi Azure Key Vault

W tym samouczku opisano, jak korzystać z szyfrowania magazynu po stronie klienta za pomocą usługi Azure Key Vault. Przeprowadzi Cię przez sposób szyfrowania i odszyfrowywania obiektu blob w aplikacji konsoli przy użyciu tych technologii.

**Szacowany czas trwania:** 20 minut

Aby uzyskać omówienie informacji o usłudze Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).

Aby uzyskać omówienie informacji na temat szyfrowania po stronie klienta dla usługi Azure Storage, zobacz [Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto usługi Azure Storage
* Visual Studio 2013 lub nowsze
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Omówienie szyfrowania po stronie klienta

Aby zapoznać się z omówieniem szyfrowania po stronie klienta dla usługi Azure Storage, zobacz [Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Oto krótki opis działania szyfrowania po stronie klienta:

1. Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest kluczem symetrycznym jednorazowego użycia.
2. Dane klienta są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie zawijany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być asymetryczną parą kluczy lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w usłudze Azure Key Vault. Sam klient magazynu nigdy nie ma dostępu do KEK. Po prostu wywołuje algorytm zawijania kluczy, który jest dostarczany przez magazyn klucza. Klienci mogą wybrać użycie niestandardowych dostawców do zawijania/rozpakowywania kluczy, jeśli chcą.
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Aby kontynuować ten samouczek, należy wykonać następujące kroki, które zostały opisane w samouczku [Szybki start: Ustaw i pobierz klucz tajny z usługi Azure Key Vault przy użyciu aplikacji sieci web .NET:](../../key-vault/secrets/quick-create-net.md)

* Tworzenie magazynu kluczy.
* Dodaj klucz lub klucz tajny do magazynu kluczy.
* Zarejestruj aplikację w usłudze Azure Active Directory.
* Autoryzuj aplikację do używania klucza lub klucza tajnego.

Zanotuj identyfikator klienta i program ClientSecret, które zostały wygenerowane podczas rejestrowania aplikacji w usłudze Azure Active Directory.

Utwórz oba klucze w magazynie kluczy. Zakładamy, że w pozostałej części samouczka użyto następujących nazw: ContosoKeyVault i TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Tworzenie aplikacji konsoli z pakietami i ustawieniami aplikacji

W programie Visual Studio utwórz nową aplikację konsoli.

Dodaj niezbędne pakiety nuget w konsoli Menedżera pakietów.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Dodaj appsettings do App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Dodaj następujące `using` dyrektywy i upewnij się, aby dodać odwołanie do System.Configuration do projektu.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Dodawanie metody, aby uzyskać token do aplikacji konsoli

Poniższa metoda jest używana przez klasy usługi Key Vault, które muszą uwierzytelnić się w celu uzyskania dostępu do magazynu kluczy.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Dostęp do usługi Azure Storage i usługi Key Vault w programie

W Main() metody dodaj następujący kod.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modele obiektów magazynu kluczy
> 
> Ważne jest, aby zrozumieć, że w rzeczywistości istnieją dwa modele obiektów usługi Key Vault, o których należy pamiętać: jeden jest oparty na interfejsie API REST (obszar nazw KeyVault), a drugi jest rozszerzeniem szyfrowania po stronie klienta.
> 
> Klient usługi Key Vault współdziała z interfejsem API REST i rozumie klucze sieci Web JSON i wpisy tajne dla dwóch rodzajów rzeczy, które są zawarte w usłudze Key Vault.
> 
> Rozszerzenia usługi Key Vault to klasy, które wydają się specjalnie utworzone dla szyfrowania po stronie klienta w usłudze Azure Storage. Zawierają one interfejs dla kluczy (IKey) i klasy oparte na koncepcji programu rozpoznawania kluczy. Istnieją dwie implementacje IKey, które musisz wiedzieć: RSAKey i SymmetricKey. Teraz zbiegają się one z rzeczami, które są zawarte w magazynie kluczy, ale w tym momencie są niezależnymi klasami (więc klucz i klucz tajny pobrane przez klienta magazynu kluczy nie implementują IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Szyfruj obiekt blob i przesyłanie

Dodaj następujący kod, aby zaszyfrować obiekt blob i przekaż go do konta magazynu platformy Azure. **ResolveKeyAsync** metoda, która jest używana zwraca IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Jeśli spojrzeć na Konstruktora Polityki BlobEncryption, zobaczysz, że można zaakceptować klucz i/lub program rozpoznawania nazw. Należy pamiętać, że w tej chwili nie można użyć programu rozpoznawania nazw do szyfrowania, ponieważ obecnie nie obsługuje klucza domyślnego.

## <a name="decrypt-blob-and-download"></a>Odszyfrowywanie obiektu blob i pobieranie

Odszyfrowywanie jest naprawdę podczas korzystania z rozpoznawania klas sensu. Identyfikator klucza używanego do szyfrowania jest skojarzony z obiektem blob w jego metadanych, więc nie ma powodu, aby pobrać klucz i zapamiętać skojarzenie między kluczem i obiektem blob. Musisz tylko upewnić się, że klucz pozostaje w przechowalni kluczy.   

Klucz prywatny klucza RSA pozostaje w magazynie kluczy, więc w celu odszyfrowania zaszyfrowany klucz z metadanych obiektu blob zawierający CEK jest wysyłany do magazynu kluczy w celu odszyfrowania.

Dodaj następujące elementy, aby odszyfrować obiekt blob, który właśnie został przekazany.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Istnieje kilka innych rodzajów programów rozpoznawania nazw, aby ułatwić zarządzanie kluczami, w tym: AggregateKeyResolver i CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Używanie wpisów tajnych magazynu kluczy

Sposób użycia klucza tajnego z szyfrowaniem po stronie klienta jest za pośrednictwem klasy SymmetricKey, ponieważ klucz tajny jest zasadniczo kluczem symetrycznym. Ale, jak wspomniano powyżej, klucz tajny w przechowalni kluczy nie jest mapowana dokładnie na Symetryczny Klucz. Jest kilka rzeczy do zrozumienia:

* Klucz w SymmetricKey musi być stałą długość: 128, 192, 256, 384 lub 512 bitów.
* Klucz w SymmetricKey powinny być zakodowane Base64.
* Klucz tajny magazynu kluczy, który będzie używany jako SymmetricKey musi mieć typ zawartości "application/octet-stream" w przechowalni kluczy.

Oto przykład w programie PowerShell tworzenia klucza tajnego w magazynie kluczy, który może służyć jako SymmetricKey.
Należy pamiętać, że wartość zakodowane na twardo, $key, służy wyłącznie do celów demonstracyjnych. W swoim własnym kodzie będziesz chciał wygenerować ten klucz.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

W aplikacji konsoli można użyć tego samego wywołania, jak poprzednio, aby pobrać ten klucz tajny jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Gotowe. Owocnej pracy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z usługi Microsoft Azure Storage w języku C#, zobacz [Biblioteka klienta magazynu Microsoft Azure dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Aby uzyskać więcej informacji na temat interfejsu API REST obiektów blob, zobacz [Interfejs API REST usługi obiektów Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Aby uzyskać najnowsze informacje na temat usługi Microsoft Azure Storage, przejdź do [bloga zespołu magazynu microsoft azure](https://blogs.msdn.com/b/windowsazurestorage/).
