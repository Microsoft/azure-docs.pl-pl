---
title: Instalowanie usługi Azure Blob Storage za pomocą protokołu NFS 3,0 (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak zainstalować kontener w usłudze BLOB Storage z maszyny wirtualnej platformy Azure lub z klienta działającego lokalnie przy użyciu protokołu NFS 3,0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b61ce696c28a2c72a2cd3d0eb2d2fde0022dbb01
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897743"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Instalowanie magazynu obiektów BLOB przy użyciu protokołu NFS (Network File System) 3,0 (wersja zapoznawcza)

Kontener w usłudze BLOB Storage można zainstalować z maszyny wirtualnej platformy Azure lub systemu Windows lub Linux, która jest uruchamiana lokalnie przy użyciu protokołu NFS 3,0. Ten artykuł zawiera wskazówki krok po kroku. Aby dowiedzieć się więcej o obsłudze protokołu NFS 3,0 w usłudze BLOB Storage, zobacz temat [Obsługa protokołu sieciowego systemu plików (NFS) 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza)](network-file-system-protocol-support.md).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Krok 1. rejestrowanie funkcji protokołu NFS 3,0 w ramach subskrypcji

1. Otwórz okno polecenia programu PowerShell. 

2. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

3. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

4. Zarejestruj `AllowNFSV3` funkcję przy użyciu następującego polecenia.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Zarejestruj dostawcę zasobów przy użyciu następującego polecenia.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Krok 2: sprawdzenie, czy funkcja jest zarejestrowana 

Zatwierdzenie rejestracji może potrwać do godziny. Aby sprawdzić, czy rejestracja została zakończona, użyj następujących poleceń.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Krok 3. Tworzenie Virtual Network platformy Azure (Sieć wirtualna)

Konto magazynu musi być zawarte w sieci wirtualnej. Sieć wirtualna umożliwia klientom bezpieczne łączenie się z kontem magazynu. Aby dowiedzieć się więcej o sieci wirtualnej i jak ją utworzyć, zobacz [dokumentację Virtual Network](../../virtual-network/index.yml).

> [!NOTE]
> Klienci w tej samej sieci wirtualnej mogą instalować kontenery na Twoim koncie. Możesz również zainstalować kontener z poziomu klienta działającego w sieci lokalnej, ale musisz najpierw połączyć sieć lokalną z siecią wirtualną. Zobacz [obsługiwane połączenia sieciowe](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Krok 4. Konfigurowanie zabezpieczeń sieci

Jedynym sposobem zabezpieczenia danych na koncie jest użycie sieci wirtualnej i innych ustawień zabezpieczeń sieci. Wszystkie inne narzędzia służące do zabezpieczania danych, w tym autoryzacja klucza konta, zabezpieczenia Azure Active Directory (AD) i listy kontroli dostępu (ACL) nie są jeszcze obsługiwane na kontach, na których włączono obsługę protokołu NFS 3,0. 

Aby zabezpieczyć dane na koncie, zobacz następujące zalecenia: [zalecenia dotyczące zabezpieczeń sieci dla usługi BLOB Storage](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Krok 5. Tworzenie i Konfigurowanie konta magazynu

Aby zainstalować kontener przy użyciu systemu plików NFS 3,0, należy utworzyć konto magazynu **po** zarejestrowaniu funkcji w ramach subskrypcji. Nie można włączyć kont, które istniały przed zarejestrowaniem funkcji. 

W wersji zapoznawczej tej funkcji Protokół NFS 3,0 jest obsługiwany na kontach [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) i [ogólnego przeznaczenia w wersji 2](../common/storage-account-overview.md#general-purpose-v2-accounts) .

Podczas konfigurowania konta wybierz następujące wartości:

|Ustawienie | Wydajność warstwy Premium | Wydajność standardowa  
|----|---|---|
|Lokalizacja|Wszystkie dostępne regiony |Jeden z następujących regionów: Australia Wschodnia, Korea Środkowa i Południowo-środkowe stany USA   
|Wydajność|Premium| Standardowa
|Rodzaj konta|BlockBlobStorage| Ogólnego przeznaczenia w wersji 2
|Replikacja|Magazyn lokalnie nadmiarowy (LRS)| Magazyn lokalnie nadmiarowy (LRS)
|Metoda łączności|Publiczny punkt końcowy (wybrane sieci) lub prywatny punkt końcowy |Publiczny punkt końcowy (wybrane sieci) lub prywatny punkt końcowy
|Wymagany bezpieczny transfer|Disabled|Disabled
|Hierarchiczna przestrzeń nazw|Enabled (Włączony)|Enabled (Włączony)
|SYSTEM PLIKÓW NFS V3|Enabled (Włączony) |Enabled (Włączony) 

Możesz zaakceptować wartości domyślne dla wszystkich innych ustawień. 

## <a name="step-6-create-a-container"></a>Krok 6. Tworzenie kontenera

Utwórz kontener na koncie magazynu przy użyciu dowolnego z tych narzędzi lub zestawów SDK:

|Narzędzia|Zestawy SDK|
|---|---|
|[Witryna Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[Program PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Krok 7. Instalowanie kontenera

Utwórz katalog w systemie Windows lub Linux, a następnie zainstaluj kontener na koncie magazynu.

### <a name="linux"></a>[Linux](#tab/linux)

1. W systemie Linux Utwórz katalog.

   ```
   mkdir -p /mnt/test
   ```

2. Zainstaluj kontener za pomocą następującego polecenia.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Zastąp `<storage-account-name>` symbol zastępczy, który pojawia się w tym poleceniu nazwą konta magazynu.  

   - Zastąp `<container-name>` symbol zastępczy nazwą kontenera.


### <a name="windows"></a>[Windows](#tab/windows)

1. Otwórz okno dialogowe **funkcje systemu Windows** , a następnie włącz opcję **Klient dla systemu plików NFS** . 

   ![Funkcja klienta dla systemu plików NFS](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Otwórz okno **wiersza polecenia** (cmd.exe). Następnie zainstaluj kontener za pomocą polecenia [Mount](/windows-server/administration/windows-commands/mount) .

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - Zastąp `<storage-account-name>` symbol zastępczy, który pojawia się w tym poleceniu nazwą konta magazynu.  

   - Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

3. Jeśli potrzebujesz uprawnień do zapisu, może zajść potrzeba zmiany domyślnego identyfikatora UID i GID, którego system Windows używa do nawiązywania połączenia z udziałem. Aby to zrobić, uruchom następujące polecenia programu PowerShell jako administrator:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Po wprowadzeniu tej zmiany należy ponownie uruchomić usługę klienta NFS lub uruchomić ponownie serwer.

---

## <a name="resolve-common-issues"></a>Rozwiązywanie typowych problemów

|Problem/błąd | Rozwiązanie|
|---|---|
|`Access denied by server while mounting`|Upewnij się, że klient działa w ramach obsługiwanej podsieci. Zobacz [obsługiwane lokalizacje sieciowe](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Upewnij się, że instalowany kontener został utworzony po sprawdzeniu, że funkcja została zarejestrowana. Zobacz [krok 2. Sprawdzanie, czy funkcja jest zarejestrowana](#step-2-verify-that-the-feature-is-registered). Upewnij się również, że wpisz polecenie instalacji i jest ono parametrami bezpośrednio w terminalu. Jeśli skopiujesz i wkleisz dowolny fragment tego polecenia do terminalu z innej aplikacji, ukryte znaki we wklejonej informacji mogą spowodować pojawienie się tego błędu.|

## <a name="see-also"></a>Zobacz też

[Obsługa protokołu sieciowego systemu plików (NFS) 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza)](network-file-system-protocol-support.md)