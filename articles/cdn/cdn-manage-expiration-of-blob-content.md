---
title: Zarządzanie wygaśnięciem magazynu obiektów blob platformy Azure
titleSuffix: Azure Content Delivery Network
description: Dowiedz się więcej na temat opcji kontrolowania czasu wygaśnięcia dla obiektów BLOB w pamięci podręcznej Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778716"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Zarządzanie wygaśnięciem usługi Azure Blob Storage w Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Usługa BLOB Storage](../storage/common/storage-introduction.md#blob-storage) w usłudze Azure Storage to jeden z kilku źródeł opartych na platformie Azure zintegrowanych z usługą Azure Content Delivery Network (CDN). Wszystkie publicznie dostępne zawartość obiektów BLOB mogą być buforowane w Azure CDN do momentu, gdy upłynie czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany przez `Cache-Control` nagłówek w odpowiedzi HTTP z serwera pochodzenia. W tym artykule opisano kilka sposobów ustawiania `Cache-Control` nagłówka obiektu BLOB w usłudze Azure Storage.

Ustawienia pamięci podręcznej można również kontrolować przy użyciu Azure Portal przez ustawienie reguł buforowania usługi CDN. Jeśli utworzysz regułę buforowania i ustawisz jej zachowanie buforowania w celu **przesłonięcia** lub **obejścia pamięci podręcznej**, ustawienia pamięci podręcznej podane w tym artykule zostaną zignorowane. Aby uzyskać informacje na temat ogólnych pojęć dotyczących buforowania, zobacz [jak działa buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Można ustawić wartość brak czasu wygaśnięcia dla obiektu BLOB. W takim przypadku Azure CDN automatycznie stosuje domyślny czas wygaśnięcia o wartości siedem dni, chyba że w Azure Portal skonfigurowano reguły buforowania. Ten domyślny czas TTL dotyczy tylko optymalizacji ogólnego dostarczania w sieci Web. W przypadku optymalizacji dużych plików domyślny czas wygaśnięcia wynosi jeden dzień, a w przypadku optymalizacji przesyłania strumieniowego multimediów domyślny czas wygaśnięcia wynosi jeden rok.
> 
> Aby uzyskać więcej informacji o tym, jak działa Azure CDN, aby przyspieszyć dostęp do obiektów blob i innych plików, zobacz [Omówienie usługi Azure Content Delivery Network](cdn-overview.md).
> 
> Aby uzyskać więcej informacji o usłudze Azure Blob Storage, zobacz [wprowadzenie do usługi BLOB Storage](../storage/blobs/storage-blobs-introduction.md).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawianie nagłówków Cache-Control przy użyciu reguł buforowania usługi CDN
Preferowaną metodą ustawiania nagłówka obiektu BLOB `Cache-Control` jest użycie reguł buforowania w Azure Portal. Aby uzyskać więcej informacji na temat reguł buforowania usługi CDN, zobacz [kontrola Azure CDN buforowania przy użyciu reguł buforowania](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profile. W przypadku **Azure CDN Premium z profilów Verizon** należy użyć [aparatu reguł Azure CDN](./cdn-verizon-premium-rules-engine.md) w portalu **zarządzania** , aby korzystać z podobnych funkcji.

**Aby przejść do strony reguły buforowania usługi CDN**:

1. W Azure Portal wybierz profil CDN, a następnie wybierz punkt końcowy dla obiektu BLOB.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk reguły buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Aby ustawić nagłówki Cache-Control usługi BLOB Storage przy użyciu globalnych reguł buforowania:**

1. W obszarze **globalne reguły buforowania** Ustaw **zachowanie buforowania ciągu zapytania** , aby **zignorować ciągi zapytań** i ustawić **zachowanie buforowania** w celu **przesłonięcia**.
      
2. W przypadku **czasu wygaśnięcia pamięci podręcznej** wprowadź 3600 w polu **sekundy** lub 1 w polu **godziny** . 

   ![Przykład globalnych zasad buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Ta Globalna reguła buforowania ustawia czas trwania pamięci podręcznej o jedną godzinę i ma wpływ na wszystkie żądania do punktu końcowego. Zastępuje wszystkie `Cache-Control` nagłówki lub `Expires` http, które są wysyłane przez serwer pochodzenia określony przez punkt końcowy.   

3. Wybierz pozycję **Zapisz**.
 
**Aby ustawić nagłówki Cache-Control pliku obiektu BLOB przy użyciu niestandardowych reguł buforowania:**

1. W obszarze **niestandardowe reguły buforowania** Utwórz dwa warunki dopasowywania:

     A. W przypadku pierwszego warunku dopasowywania Ustaw **warunek dopasowania** na wartość **Path** , a następnie wprowadź `/blobcontainer1/*` dla **wartości dopasowywania**. Ustaw **zachowanie buforowania** w celu **przesłonięcia** i wprowadź 4 w polu **godziny** .

    B. W przypadku drugiego warunku dopasowywania Ustaw **warunek dopasowania** na wartość **Path** i wprowadź `/blobcontainer1/blob1.txt` dla **wartości dopasowywania**. Ustaw **zachowanie buforowania** w celu **przesłonięcia** i wprowadź wartość 2 w polu **godziny** .

    ![Przykład niestandardowych reguł buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Pierwsza Niestandardowa reguła buforowania ustawia czas trwania pamięci podręcznej wynoszący cztery godziny dla wszystkich plików obiektów BLOB w `/blobcontainer1` folderze na serwerze źródłowym określonym przez punkt końcowy. Druga reguła zastępuje pierwszą regułę `blob1.txt` tylko dla pliku obiektu BLOB i ustawia dla niej okres istnienia pamięci podręcznej wynoszący 2 godziny.

2. Wybierz pozycję **Zapisz**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Ustawianie nagłówków Cache-Control przy użyciu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) to jeden z najszybszych i najbardziej zaawansowanych sposobów administrowania usługami platformy Azure. Użyj `Get-AzStorageBlob` polecenia cmdlet, aby uzyskać odwołanie do obiektu BLOB, a następnie ustaw `.ICloudBlob.Properties.CacheControl` Właściwość. 

Na przykład:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Za pomocą programu PowerShell można także [zarządzać profilami i punktami końcowymi usługi CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Ustawianie nagłówków Cache-Control przy użyciu platformy .NET
Aby określić nagłówek obiektu BLOB przy `Cache-Control` użyciu kodu platformy .NET, użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) , aby ustawić właściwość [polecenia cloudblob. Properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) .

Na przykład:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Istnieją więcej przykładów kodu platformy .NET dostępnych w [przykładach BLOB Storage platformy Azure dla platformy .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Ustawianie nagłówków Cache-Control przy użyciu innych metod

### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Za pomocą [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)można wyświetlać i edytować zasoby magazynu obiektów blob, w tym takie właściwości, jak Właściwość *CacheControl* . 

Aby zaktualizować właściwość *CacheControl* obiektu BLOB za pomocą Eksplorator usługi Azure Storage:
   1. Wybierz obiekt BLOB, a następnie wybierz pozycję **Właściwości** z menu kontekstowego. 
   2. Przewiń w dół do właściwości *CacheControl* .
   3. Wprowadź wartość, a następnie wybierz pozycję **Zapisz**.


![Właściwości Eksplorator usługi Azure Storage](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interfejs wiersza polecenia platformy Azure
Za pomocą [interfejsu Azure Command-Line Interface](/cli/azure) (CLI) można zarządzać zasobami obiektów blob platformy Azure z poziomu wiersza polecenia. Aby ustawić nagłówek kontroli pamięci podręcznej podczas przekazywania obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure, ustaw właściwość *cacheControl* przy użyciu `-p` przełącznika. Poniższy przykład pokazuje, jak ustawić czas wygaśnięcia na godzinę (3600 s):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Interfejs API REST usług Azure Storage
Korzystając z [interfejsu API REST usług Azure Storage](/rest/api/storageservices/) , można jawnie ustawić właściwość *x-MS-BLOB-Cache-Control* , wykonując następujące operacje na żądanie:
  
   - [Wstawianie obiektu blob](/rest/api/storageservices/Put-Blob)
   - [Umieść listę zablokowanych](/rest/api/storageservices/Put-Block-List)
   - [Ustawianie właściwości obiektu BLOB](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówka Cache-Control
Można łatwo zweryfikować ustawienia czasu wygaśnięcia obiektów BLOB. Za pomocą [narzędzi deweloperskich](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)przeglądarki Sprawdź, czy obiekt BLOB zawiera `Cache-Control` Nagłówek odpowiedzi. Do sprawdzenia nagłówków odpowiedzi można także użyć narzędzia, takiego jak [Wget](https://www.gnu.org/software/wget/), [Poster](https://www.getpostman.com/)lub [programu Fiddler](https://www.telerik.com/fiddler) .

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak zarządzać wygasaniem zawartości usługi w chmurze w Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Informacje o pojęciach dotyczących buforowania](cdn-how-caching-works.md)