---
title: Limity Azure Data Box | Microsoft Docs
description: Opisuje limity systemu i zalecane rozmiary Microsoft Azure urządzenie Data Box składników i połączeń.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7d699fc47fa0a0cb57d103ff42ff17bdc0f3a82b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202671"
---
# <a name="azure-data-box-limits"></a>Limity Azure Data Box

Te limity należy wziąć pod uwagę podczas wdrażania i obsługi urządzenie Data Box Microsoft Azure. W poniższej tabeli opisano te limity dla urządzenie Data Box.

## <a name="data-box-service-limits"></a>Limity usługi urządzenie Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Limity urządzenie Data Box

- Urządzenie Data Box może przechowywać maksymalnie 500 000 000 plików dla importu i eksportu.
- Urządzenie Data Box obsługuje maksymalnie 512 kontenerów lub udziałów w chmurze. Katalogi najwyższego poziomu w udziale użytkownika stają się kontenerami lub udziałami plików platformy Azure w chmurze. 
- Urządzenie Data Box pojemność użycia może być mniejsza niż 80 TB z powodu użycia przestrzeni metadanych systemu plików ReFS.

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych


### <a name="for-import-order"></a>Dla zamówienia importu

Urządzenie Data Box zastrzeżenia dla zamówienia importu obejmują:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>Dla zamówienia eksportu

Urządzenie Data Box zastrzeżenia dla zamówienia eksportu obejmują:

- Urządzenie Data Box to urządzenie z systemem Windows i nie obsługuje uwzględniania wielkości liter w nazwach plików. Na przykład na platformie Azure mogą znajdować się dwa różne pliki z nazwami, które różnią się tylko wielkością liter. Nie używaj pola danych do eksportowania takich plików, ponieważ zostaną one nadpisywane na urządzeniu.
- Jeśli masz zduplikowane znaczniki w plikach wejściowych lub tagach odnoszących się do tych samych danych, eksport urządzenie Data Box może pominąć lub zastąpić pliki. Liczba plików i rozmiar danych wyświetlanych na Azure Portal mogą różnić się od rzeczywistego rozmiaru danych znajdujących się na urządzeniu. 
- Urządzenie Data Box eksportuje dane do systemu Windows za pośrednictwem protokołu SMB i są ograniczone przez ograniczenia protokołu SMB dla plików i folderów. Pliki i foldery z nieobsługiwanymi nazwami nie są eksportowane.
- Istnieje mapowanie 1:1 z prefiksu do kontenera.
- Maksymalny rozmiar pliku to 1024 znaków, nazwy plików, które przekraczają tę długość, nie są eksportowane.
- Zduplikowane prefiksy w pliku *XML* (przekazane podczas tworzenia zamówienia) są eksportowane. Zduplikowane prefiksy nie są ignorowane.
- W przypadku stronicowych obiektów blob i nazw kontenerów uwzględniana jest wielkość liter, więc jeśli wielkość liter nie jest zgodna, obiekt BLOB i/lub kontener nie zostaną znalezione.
 

## <a name="azure-storage-account-size-limits"></a>Limity rozmiaru konta usługi Azure Storage

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blokowe obiekty blob platformy Azure, stronicowe obiekty blob i konwencje nazewnictwa plików

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
