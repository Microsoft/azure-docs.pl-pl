---
title: Zainstaluj usługę Azure Blob Storage przy użyciu protokołu NFS 3.0 (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak zainstalować kontener w usłudze Blob Storage z maszyny wirtualnej platformy Azure lub klienta uruchamianego lokalnie przy użyciu protokołu NFS 3.0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: bf6b06ba7cc7f547f752ffa7877fca186ba4465e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713790"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Zainstaluj usługę Blob Storage przy użyciu protokołu NFS (Network File System) 3.0 (wersja zapoznawcza)

Kontener w usłudze Blob Storage można zainstalować z maszyny wirtualnej platformy Azure opartej na systemie Linux lub systemu Linux uruchamianego lokalnie przy użyciu protokołu NFS 3.0. Ten artykuł zawiera szczegółowe wskazówki. Aby dowiedzieć się więcej na temat obsługi protokołu NFS 3.0 w usłudze Blob Storage, zobacz Network [File System (NFS) 3.0 protocol support in Azure Blob Storage (preview) (Obsługa protokołu NFS 3.0](network-file-system-protocol-support.md)w usłudze Azure Blob Storage (wersja zapoznawcza) ).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Krok 1. Rejestrowanie funkcji protokołu NFS 3.0 w subskrypcji

1. Otwórz okno polecenia programu PowerShell. 

2. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

3. Jeśli Tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

4. Zarejestruj funkcję `AllowNFSV3` przy użyciu następującego polecenia.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Zarejestruj dostawcę zasobów przy użyciu następującego polecenia.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Krok 2. Sprawdzenie, czy funkcja została zarejestrowana 

Zatwierdzenie rejestracji może potrwać do godziny. Aby sprawdzić, czy rejestracja jest ukończona, użyj następujących poleceń.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Krok 3. Tworzenie sieci wirtualnej Virtual Network Azure

Konto magazynu musi znajdować się w sieci wirtualnej. Sieć wirtualna umożliwia klientom bezpieczne łączenie się z kontem magazynu. Aby dowiedzieć się więcej na temat sieci wirtualnej i sposobu jej tworzenia, zobacz [dokumentację Virtual Network .](../../virtual-network/index.yml)

> [!NOTE]
> Klienci w tej samej sieci wirtualnej mogą zainstalować kontenery na Twoim koncie. Kontener można również zainstalować z klienta, który działa w sieci lokalnej, ale najpierw należy połączyć sieć lokalną z siecią wirtualną. Zobacz [Obsługiwane połączenia sieciowe.](network-file-system-protocol-support.md#supported-network-connections)

## <a name="step-4-configure-network-security"></a>Krok 4. Konfigurowanie zabezpieczeń sieci

Jedynym sposobem zabezpieczenia danych na koncie jest użycie sieci wirtualnej i innych ustawień zabezpieczeń sieci. Inne narzędzia używane do zabezpieczania danych, w tym autoryzacja klucza konta, zabezpieczenia Azure Active Directory (AD) i listy kontroli dostępu (ACL), nie są jeszcze obsługiwane na kontach, które mają włączoną obsługę protokołu NFS 3.0.

Aby zabezpieczyć dane na koncie, zobacz następujące zalecenia: [Zalecenia dotyczące zabezpieczeń sieci dla usługi Blob Storage.](security-recommendations.md#networking)

## <a name="step-5-create-and-configure-a-storage-account"></a>Krok 5. Tworzenie i konfigurowanie konta magazynu

Aby zainstalować kontener przy użyciu systemu plików NFS 3.0, należy utworzyć konto magazynu po zarejestrowaniu funkcji w subskrypcji.  Nie można włączyć kont, które istniały przed zarejestrowaniem funkcji.

W wersji zapoznawczej tej funkcji protokół NFS 3.0 jest obsługiwany dla kont magazynu ogólnego przeznaczenia ogólnego przeznaczenia w wersji 2 i kont magazynu blokowych obiektów blob w chmurze Premium. Aby uzyskać więcej informacji na temat tych typów kont magazynu, zobacz [Omówienie konta magazynu.](../common/storage-account-overview.md)

Podczas konfigurowania konta wybierz następujące wartości:

|Ustawienie | Wydajność Premium | Standardowa wydajność  
|----|---|---|
|Lokalizacja|Wszystkie dostępne regiony |Jeden z następujących regionów: Australia Wschodnia, Korea Środkowa, Wschodnie usa i Południowo-środkowe usa   
|Wydajność|Premium| Standardowa
|Rodzaj konta|BlockBlobStorage| Ogólnego przeznaczenia, wersja 2
|Replikacja|Magazyn lokalnie nadmiarowy (LRS)| Magazyn lokalnie nadmiarowy (LRS)
|Metoda łączności|Publiczny punkt końcowy (wybrane sieci) lub prywatny punkt końcowy |Publiczny punkt końcowy (wybrane sieci) lub prywatny punkt końcowy
|Wymagany bezpieczny transfer|Disabled|Disabled
|Hierarchiczna przestrzeń nazw|Enabled (Włączony)|Enabled (Włączony)
|NFS V3|Enabled (Włączony) |Enabled (Włączony) 

Możesz zaakceptować wartości domyślne dla wszystkich pozostałych ustawień. 

## <a name="step-6-create-a-container"></a>Krok 6. Tworzenie kontenera

Utwórz kontener na koncie magazynu przy użyciu dowolnego z tych narzędzi lub zestawów SDK:

|Narzędzia|Zestawy SDK|
|---|---|
|[Witryna Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[Program PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Krok 7. Zainstaluj kontener

Utwórz katalog w systemie Linux, a następnie zainstaluj kontener na koncie magazynu.

1. W systemie Linux utwórz katalog.

   ```
   mkdir -p /mnt/test
   ```

2. Zainstaluj kontener przy użyciu następującego polecenia.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Zastąp symbol `<storage-account-name>` zastępczy wyświetlany w tym poleceniu nazwą konta magazynu.  

   - Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

---

## <a name="resolve-common-issues"></a>Rozwiązywanie typowych problemów

|Problem/błąd | Rozwiązanie|
|---|---|
|`Access denied by server while mounting`|Upewnij się, że klient działa w ramach obsługiwanej podsieci. Zobacz [obsługiwane lokalizacje sieciowe.](network-file-system-protocol-support.md#supported-network-connections)|
|`No such file or directory`| Upewnij się, że instalowany kontener został utworzony po sprawdzeniu, że funkcja została zarejestrowana. Zobacz [Krok 2. Sprawdzenie, czy funkcja jest zarejestrowana.](#step-2-verify-that-the-feature-is-registered) Pamiętaj również o wpisaniu polecenia instalacji i jego parametrów bezpośrednio w terminalu. Jeśli skopiujesz i wkleisz dowolny fragment tego polecenia do terminalu z innej aplikacji, ukryte znaki we wklejonej informacji mogą spowodować pojawienie się tego błędu.|

## <a name="see-also"></a>Zobacz też

[Obsługa protokołu sieciowego systemu plików (NFS) 3.0 w usłudze Azure Blob Storage (wersja zapoznawcza)](network-file-system-protocol-support.md)
