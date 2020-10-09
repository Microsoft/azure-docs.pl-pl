---
title: Przykładowy skrypt programu Azure PowerShell — obliczanie łącznego rozmiaru rozliczeniowego kontenera obiektów blob | Microsoft Docs
description: Oblicz łączny rozmiar kontenera w usłudze Azure Blob Storage na potrzeby rozliczeń.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: f8bd47a8fde700382f9789ab1c77ff35e9b1f4f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999534"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Obliczanie łącznego rozmiaru kontenera obiektów blob

Ten skrypt oblicza rozmiar kontenera w usłudze Azure Blob Storage na potrzeby szacowania kosztów rozliczeń. Skrypt sumuje rozmiar obiektów blob w kontenerze.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ten skrypt programu PowerShell oblicza rozmiar kontenera na potrzeby rozliczeń. Jeśli obliczasz rozmiar kontenera na inne potrzeby, zobacz [Obliczanie łącznego rozmiaru kontenera w usłudze Blob Storage](../scripts/storage-blobs-container-calculate-size-powershell.md), aby uzyskać prostszy skrypt, obliczający przybliżony rozmiar.

## <a name="determine-the-size-of-the-blob-container"></a>Określanie rozmiaru kontenera obiektów blob

Całkowity rozmiar kontenera obiektów blob obejmuje rozmiar samego kontenera i rozmiar wszystkich obiektów blob w kontenerze.

W poniższej sekcji opisano sposób obliczania pojemności magazynu dla kontenerów obiektów blob i obiektów blob.W poniższej sekcji Len(X) oznacza liczbę znaków w ciągu.

### <a name="blob-containers"></a>Kontenery obiektów blob

Następujące obliczenie opisuje sposób szacowania pojemności magazynu używanej przez kontener obiektów blob:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Podział jest następujący:
* 48 bajtów narzutu dla każdego kontenera obejmuje godzinę ostatniej modyfikacji, uprawnienia, ustawienia publiczne i niektóre metadane systemu.

* Nazwa kontenera jest przechowywana w formacie Unicode, więc należy pomnożyć liczbę znaków przez 2.

* Dla każdego bloku przechowywanych metadanych kontenera obiektów blob przechowujemy długość nazwy (ASCII) plus długość wartości ciągu.

* 512 bajtów na każdy podpisany identyfikator obejmuje nazwę podpisanego identyfikatora, godzinę rozpoczęcia, czas wygaśnięcia i uprawnienia.

### <a name="blobs"></a>Obiekty blob

Następujące obliczenia pokazują sposób szacowania pojemności magazynu używanej przez obiekt blob.

* Blokowy obiekt blob (podstawowy obiekt blob lub migawka):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Stronicowy obiekt blob (podstawowy obiekt blob lub migawka):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Podział jest następujący:

* 124 bajty narzutu dla obiektu blob, który obejmuje następujące elementy:
    - Godzina ostatniej modyfikacji
    - Rozmiar
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - Uprawnienia
    - Informacje migawki
    - Dzierżawa
    - Niektóre metadane systemu

* Nazwa obiektu blob jest przechowywana w formacie Unicode, więc należy pomnożyć liczbę znaków przez 2.

* Dla każdego bloku przechowywanych metadanych przechowujemy długość nazwy (ASCII) plus długość wartości ciągu.

* Dla blokowych obiektów blob:
  * 8 bajtów dla listy bloków.
  * Liczba bloków razy rozmiar identyfikatora bloku w bajtach.
  * Rozmiar danych we wszystkich zatwierdzonych i niezatwierdzonych blokach.

    >[!NOTE]
    >Gdy są używane migawki, rozmiar obejmuje tylko unikatowe dane dla tej bazy lub obiektu blob migawki. Jeśli niezatwierdzone bloki nie będą używane przez tydzień, zostaną usunięte. Nie będą wtedy uwzględniane w rozliczeniu.

* Dla stronicowych obiektów blob:
  * Liczba niesąsiadujących zakresów stron z danymi razy 12-bajtów. Jest to liczba unikatowych zakresów stron wyświetlanych podczas wywoływania interfejsu API **GetPageRanges**.

  * Rozmiar danych w bajtach wszystkich przechowywanych stron.

    >[!NOTE]
    >Gdy są używane migawki, ten rozmiar uwzględnia tylko unikatowe strony dla podstawowego obiektu blob lub obiektu blob migawki, który jest liczony.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Następne kroki

- Zobacz [Obliczanie łącznego rozmiaru kontenera w usłudze Blob Storage](../scripts/storage-blobs-container-calculate-size-powershell.md), aby uzyskać prostszy skrypt, obliczający przybliżony rozmiar kontenera.

- Aby uzyskać więcej informacji na temat rozliczeń za usługę Azure Storage, zobacz [Informacje o rozliczeniach usługi Windows Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

- Więcej przykładowych skryptów programu PowerShell dla usługi Storage można znaleźć w artykule [Przykładowe skrypty programu PowerShell dla usługi Azure Storage](../blobs/storage-samples-blobs-powershell.md).
