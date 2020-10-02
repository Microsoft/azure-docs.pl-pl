---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80411ac191d0385ddafd4a3cc490d322ce039689
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91641856"
---
- Nie należy kopiować plików bezpośrednio do żadnego ze współtworzonych udziałów. Należy utworzyć folder w udziale, a następnie skopiować pliki do tego folderu.
- Folder w *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *StorageAccount_BlockBlob/Container* i *StorageAccount_PageBlob/Container*.
- Każdy folder utworzony bezpośrednio w obszarze *StorageAccount_AzureFiles* jest tłumaczony na udział plików platformy Azure.
- Jeśli kopiowany obiekt ma taką samą nazwę jak obiekt platformy Azure, na przykład obiektu BLOB lub pliku, który znajduje się już w chmurze, urządzenie Data Box zastąpi plik w chmurze.
- Każdy plik zapisany w udziałach *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest przekazywany jako blokowy obiekt BLOB i stronicowy obiekt BLOB.
- Usługa Azure Blob Storage nie obsługuje katalogów. Jeśli utworzysz folder w folderze *StorageAccount_BlockBlob* , foldery wirtualne są tworzone w nazwie obiektu BLOB. W przypadku Azure Files zachowywana jest rzeczywista struktura katalogów.
- Nie przekazano żadnych pustych hierarchii katalogów (bez plików) utworzonych w obszarze *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* folderów.
- Jeśli wystąpią błędy podczas przekazywania danych do platformy Azure, na docelowym koncie magazynu zostanie utworzony dziennik błędów. Ścieżka do tego dziennika błędów jest dostępna po zakończeniu przekazywania i można przejrzeć dziennik, aby podjąć działania naprawcze. Nie usuwaj danych ze źródła bez weryfikowania przekazanych danych.
- Metadane plików i uprawnienia NTFS można zachować, gdy dane są przekazywane do Azure Files przy użyciu wskazówek dotyczących [zachowania list ACL plików, atrybutów i sygnatur czasowych z Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).