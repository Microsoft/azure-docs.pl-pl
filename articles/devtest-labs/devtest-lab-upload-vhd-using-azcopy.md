---
title: Przekaż plik VHD do Azure DevTest Labs przy użyciu usługi AzCopy | Microsoft Docs
description: Ten artykuł zawiera Przewodnik po użyciu narzędzia wiersza polecenia AzCopy w celu przekazania pliku VHD do konta magazynu laboratorium w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88650778"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Przekaż plik VHD do konta magazynu laboratorium przy użyciu AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W Azure DevTest Labs pliki VHD mogą służyć do tworzenia obrazów niestandardowych, które są używane do obsługi maszyn wirtualnych. Poniższe kroki umożliwiają przekazanie pliku VHD do konta magazynu laboratorium przy użyciu narzędzia wiersza polecenia AzCopy. Po przekazaniu pliku VHD [sekcja następne kroki](#next-steps) zawiera listę artykułów, które ilustrują, jak utworzyć obraz niestandardowy na podstawie przekazanego pliku VHD. Aby uzyskać więcej informacji na temat dysków i dysków VHD na platformie Azure, zobacz [wprowadzenie do usługi Managed disks](../virtual-machines/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy to narzędzie wiersza polecenia tylko dla systemu Windows.

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przeprowadzą Cię przez proces przekazywania pliku VHD do Azure DevTest Labs przy użyciu [AzCopy](https://aka.ms/downloadazcopy). 

1. Pobierz nazwę konta magazynu laboratorium przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

1. Z listy laboratoriów wybierz odpowiednie laboratorium.  

1. W bloku laboratorium wybierz pozycję **Konfiguracja**. 

1. W bloku **Konfiguracja** laboratorium wybierz pozycję **obrazy niestandardowe (VHD)**.

1. W bloku **obrazy niestandardowe** wybierz pozycję **+ Dodaj**. 

1. W bloku **obrazu niestandardowego** wybierz pozycję **wirtualny dysk twardy**.

1. W bloku **wirtualnego dysku twardego** wybierz pozycję **Przekaż dysk VHD przy użyciu programu PowerShell**.

    ![Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Blok **przekazywanie obrazu przy użyciu programu PowerShell** wyświetla wywołanie polecenia cmdlet **Add-AzureVhd** . Pierwszy parametr (*miejsce docelowe*) zawiera identyfikator URI kontenera obiektów BLOB (*przekazywanie*) w następującym formacie:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Zanotuj pełny identyfikator URI, ponieważ jest używany w dalszych krokach.

1. Przekaż plik VHD przy użyciu AzCopy:
 
1. [Pobierz i zainstaluj najnowszą wersję programu AzCopy](https://aka.ms/downloadazcopy).

1. Otwórz okno polecenia i przejdź do katalogu instalacyjnego AzCopy. Opcjonalnie można dodać lokalizację instalacji AzCopy do ścieżki systemowej. Domyślnie AzCopy jest instalowany w następującym katalogu:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Korzystając z klucza konta magazynu i identyfikatora URI kontenera obiektów blob, uruchom następujące polecenie w wierszu polecenia. Wartość *vhdFileName* musi być w cudzysłowie. Proces przekazywania pliku VHD może być długi w zależności od rozmiaru pliku VHD i szybkości połączenia.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu Azure Portal](devtest-lab-create-template.md)
- [Tworzenie niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)