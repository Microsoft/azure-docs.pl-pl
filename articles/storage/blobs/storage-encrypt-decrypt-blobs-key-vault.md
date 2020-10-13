---
title: Samouczek — szyfrowanie i odszyfrowywanie obiektów BLOB za pomocą Azure Key Vault
titleSuffix: Azure Storage
description: Dowiedz się, jak szyfrować i odszyfrowywać obiekt BLOB przy użyciu szyfrowania po stronie klienta za pomocą Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ac69b442b06864fd45561b26909e1247674611a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89458386"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Samouczek — szyfrowanie i odszyfrowywanie obiektów BLOB za pomocą Azure Key Vault

W tym samouczku opisano sposób korzystania z szyfrowania magazynu po stronie klienta z Azure Key Vault. Przeprowadzi Cię przez proces szyfrowania i odszyfrowywania obiektu BLOB w aplikacji konsolowej przy użyciu tych technologii.

**Szacowany czas trwania:** 20 minut

Aby uzyskać przegląd informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md).

Aby uzyskać przegląd informacji o szyfrowaniu po stronie klienta dla usługi Azure Storage, zapoznaj się z tematem [szyfrowanie po stronie klienta i Azure Key Vault Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto usługi Azure Storage
* Visual Studio 2013 lub nowszy
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Omówienie szyfrowania po stronie klienta

Aby zapoznać się z omówieniem szyfrowania po stronie klienta dla usługi Azure Storage, zapoznaj się z tematem [szyfrowanie po stronie klienta i Azure Key Vault dla Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Poniżej znajduje się krótki opis sposobu działania szyfrowania po stronie klienta:

1. Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest jednorazowym użyciem klucza symetrycznego.
2. Dane klienta są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być parę kluczy asymetrycznych lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w Azure Key Vault. Klient magazynu nigdy nie ma dostępu do KEK. Po prostu wywołuje algorytm zawijania kluczy, który jest dostarczany przez Key Vault. Klienci mogą zdecydować się na użycie niestandardowych dostawców na potrzeby zawijania kluczy/rozpakowywania w razie potrzeby.
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Skonfiguruj Azure Key Vault

Aby kontynuować pracę z tym samouczkiem, należy wykonać następujące kroki opisane w samouczku [Szybki Start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault przy użyciu aplikacji sieci Web platformy .NET](../../key-vault/secrets/quick-create-net.md):

* Utwórz magazyn kluczy.
* Dodawanie klucza lub wpisu tajnego do magazynu kluczy.
* Zarejestruj aplikację przy użyciu Azure Active Directory.
* Autoryzuj aplikację do korzystania z klucza lub wpisu tajnego.

Zanotuj ClientID i ClientSecret, które zostały wygenerowane podczas rejestrowania aplikacji przy użyciu Azure Active Directory.

Utwórz oba klucze w magazynie kluczy. Przyjęto założenie, że w pozostałej części samouczka użyto następujących nazw: ContosoKeyVault i TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Tworzenie aplikacji konsolowej przy użyciu pakietów i AppSettings

W programie Visual Studio Utwórz nową aplikację konsolową.

Dodaj wymagane pakiety NuGet w konsoli Menedżera pakietów.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Dodaj AppSettings do App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Dodaj następujące `using` dyrektywy i pamiętaj, aby dodać odwołanie do System.Configwersja do projektu.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Dodawanie metody w celu uzyskania tokenu do aplikacji konsolowej

Następująca metoda jest używana przez klasy Key Vault, które muszą uwierzytelniać się w celu uzyskania dostępu do magazynu kluczy.

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

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Dostęp do usługi Azure Storage i Key Vault w programie

W metodzie Main () Dodaj następujący kod.

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
> Key Vault modele obiektów
> 
> Ważne jest, aby zrozumieć, że istnieją dwa Key Vault modele obiektów, które mają być świadome: jeden jest oparty na interfejsie API REST (przestrzeni nazw magazynu kluczy), a drugi to rozszerzenie dla szyfrowania po stronie klienta.
> 
> Klient Key Vault współdziała z interfejsem API REST i rozpoznaje klucze sieci Web JSON oraz wpisy tajne dla dwóch rodzajów elementów zawartych w Key Vault.
> 
> Rozszerzenia Key Vault to klasy, które są wydające się głównie do szyfrowania po stronie klienta w usłudze Azure Storage. Zawierają one interfejs dla kluczy (IKey) i klasy oparte na koncepcji programu rozpoznawania kluczy. Istnieją dwie implementacje IKey, które należy znać: RSAKey i SymmetricKey. Teraz nastąpiły zbieżność z elementami zawartymi w Key Vault, ale w tym momencie są one niezależnymi klasami (więc klucz i wpis tajny pobierany przez klienta Key Vault nie implementują IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Szyfrowanie obiektów blob i przekazywanie

Dodaj następujący kod, aby zaszyfrować obiekt BLOB i przekazać go do konta usługi Azure Storage. Używana metoda **ResolveKeyAsync** zwraca wartość iKey.

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
> Jeśli zobaczysz Konstruktor BlobEncryptionPolicy, zobaczysz, że może on akceptować klucz i/lub program rozpoznawania nazw. Należy pamiętać, że obecnie nie można użyć programu rozpoznawania nazw do szyfrowania, ponieważ obecnie nie obsługuje on klucza domyślnego.

## <a name="decrypt-blob-and-download"></a>Odszyfruj obiekt BLOB i Pobierz

Odszyfrowywanie jest naprawdę przydatne w przypadku korzystania z klas rozpoznawania nazw. Identyfikator klucza używany do szyfrowania jest skojarzony z obiektem BLOB w swoich metadanych, dlatego nie istnieje powód pobrania klucza i zapamiętania skojarzenia między kluczem a obiektem BLOB. Wystarczy upewnić się, że klucz pozostanie w Key Vault.   

Klucz prywatny klucza RSA pozostaje w Key Vault, więc w celu odszyfrowania wystąpił zaszyfrowany klucz z metadanych obiektu BLOB, który zawiera CEK, jest wysyłany do Key Vault w celu odszyfrowania.

Dodaj następujące elementy do odszyfrowania obiektu BLOB, który właśnie został przekazany.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Istnieje kilka innych rodzajów resolverów, które ułatwiają zarządzanie kluczami, w tym: AggregateKeyResolver i CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Użyj Key Vault wpisów tajnych

Sposób użycia klucza tajnego z szyfrowaniem po stronie klienta polega na użyciu klasy SymmetricKey, ponieważ klucz tajny jest zasadniczo kluczem symetrycznym. Jednak jak wspomniano powyżej, wpis tajny w Key Vault nie jest mapowany do SymmetricKey. Istnieje kilka kwestii, które należy zrozumieć:

* Klucz w SymmetricKey musi mieć stałą długość: 128, 192, 256, 384 lub 512 bity.
* Klucz w SymmetricKey powinien być zakodowany w formacie base64.
* Wpis tajny Key Vault, który będzie używany jako SymmetricKey, musi mieć typ zawartości "application/octet-stream" w Key Vault.

Oto przykład w programie PowerShell tworzenia wpisu tajnego w Key Vault, który może być używany jako SymmetricKey.
Należy pamiętać, że twarda wartość, $key, jest tylko do celów demonstracyjnych. W swoim własnym kodzie chcesz wygenerować ten klucz.

```powershell
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

W aplikacji konsolowej można użyć tego samego wywołania jak wcześniej, aby pobrać ten klucz tajny jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

To wszystko. Owocnej pracy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z Microsoft Azure Storage w języku C#, zobacz [Microsoft Azure Storage biblioteki klienckiej dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Aby uzyskać więcej informacji na temat interfejsu API REST obiektów blob, zobacz [interfejs API REST usługi BLOB Service](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Aby uzyskać najnowsze informacje na temat Microsoft Azure Storage, przejdź do [blogu zespołu Microsoft Azure Storage](https://docs.microsoft.com/archive/blogs/windowsazurestorage/).
