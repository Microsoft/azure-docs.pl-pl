---
title: Szybki Start — Tworzenie obiektu BLOB przy użyciu programu PowerShell
titleSuffix: Azure Storage
description: W tym przewodniku Szybki start program Azure PowerShell jest używany w ramach magazynu obiektów (blob). Następnie przy użyciu programu PowerShell przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz obiekty blob w kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21fc7e6de38a98e4f0853cf513f9f3441c7e2106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735356"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą programu PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi. Tworzenie zasobów platformy Azure lub zarządzanie nimi może odbywać się w wierszu polecenia programu PowerShell lub za pośrednictwem skryptów. Ten przewodnik zawiera opis sposobu użycia programu PowerShell do transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Potrzebna będzie także rola współautor danych obiektów blob magazynu do odczytywania, zapisywania i usuwania kontenerów i obiektów BLOB usługi Azure Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dla tego przewodnika Szybki start wymagany jest moduł Az Azure PowerShell w wersji 0.7 lub nowszej. Uruchom polecenie `Get-InstalledModule -Name Az -AllVersions | select Name,Version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Obiekty blob są zawsze przesyłane do kontenera. Możesz organizować grupy obiektów blob, tak jak organizujesz pliki w folderach na komputerze.

Określ nazwę kontenera, a następnie utwórz kontener przy użyciu polecenia [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Ustaw uprawnienia na `blob`, aby zezwolić na publiczny dostęp do plików. W tym przykładzie nazwą kontenera jest *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Pliki VHD, które służą do tworzenia obsługi maszyn wirtualnych usługi IaaS, to stronicowe obiekty blob. Uzupełnialne obiekty blob mogą służyć do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość plików przechowywanych w usłudze Blob Storage to blokowe obiekty blob. 

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj odwołanie do blokowego obiektu blob w tym kontenerze. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dane przy użyciu polecenia [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent). Ta operacja tworzy obiekt blob, jeśli nie istnieje, lub zastępuje obiekt blob, jeśli istnieje.

Poniższe przykłady umożliwiają przekazanie *Image001.jpg* i *Image002.png* z folderu *D: \\ _TestImages* na dysku lokalnym do utworzonego kontenera.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx 
  -StandardBlobTier Archive
```

Przed kontynuowaniem można przesłać dowolną liczbę plików.

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę obiektów blob w kontenerze za pomocą polecenia [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). W tym przykładzie przedstawiono tylko nazwy przekazanych obiektów blob.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz obiekty blob na swój dysk twardy. Dla każdego obiektu blob, który chcesz pobrać, określ nazwę i wywołaj polecenie [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent), aby pobrać obiekt blob.

Ten przykład umożliwia pobranie obiektów BLOB do *D: \\ _TestImages \downloads* na dysku lokalnym. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Transfer danych przy użyciu narzędzia AzCopy

Narzędzie wiersza polecenia AzCopy oferuje wysoce wydajny, oparty na skryptach transfer danych dla usługi Azure Storage. Można użyć AzCopy do transferowania danych do i z usługi BLOB Storage i Azure Files. Aby uzyskać więcej informacji na temat AzCopy V10, najnowszej wersji programu AzCopy, zobacz [wprowadzenie do AzCopy](../common/storage-use-azcopy-v10.md). Aby dowiedzieć się więcej o korzystaniu z usługi AzCopy V10 z usługą BLOB Storage, zobacz [transfer danych za pomocą AzCopy i BLOB Storage](../common/storage-use-azcopy-v10.md#transfer-data).

Poniższy przykład używa AzCopy do przekazania pliku lokalnego do obiektu BLOB. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń wszystkie utworzone zasoby. Najprostszym sposobem usunięcia elementów zawartości jest usunięcie grupy zasobów. Usunięcie grupy zasobów spowoduje także usunięcie wszystkich zasobów uwzględnionych w tej grupie. W poniższym przykładzie usunięcie grupy zasobów powoduje usunięcie konta magazynu i samej grupy zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przeniesiono pliki między lokalnym systemem plików i magazynem obiektów blob platformy Azure. Aby dowiedzieć się więcej na temat pracy z usługą BLOB Storage za pomocą programu PowerShell, Eksploruj Azure PowerShell przykłady dla usługi BLOB Storage.

> [!div class="nextstepaction"]
> [Przykłady programu Azure PowerShell dla usługi Azure Blob Storage](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Microsoft Azure Storage

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
