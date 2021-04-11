---
title: Tworzenie i przekazywanie wirtualnego dysku twardego Flatcar kontenera systemu Linux do użycia na platformie Azure
description: Dowiedz się, jak utworzyć i przekazać dysk VHD zawierający system operacyjny Flatcar Container Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565295"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Używanie prekompilowanego obrazu Flatcar dla platformy Azure

Możesz pobrać wstępnie utworzone obrazy wirtualnego dysku twardego platformy Azure z Flatcar kontenera systemu Linux dla każdego z obsługiwanych kanałów Flatcar:

- [stabilne](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [wersja beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alfa](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [brzegu](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Ten obraz jest już w pełni skonfigurowany i zoptymalizowany pod kątem uruchamiania na platformie Azure. Wystarczy tylko zdekompresować.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Tworzenie własnej maszyny wirtualnej opartej na Flatcar na platformie Azure

Alternatywnie możesz utworzyć własny obraz kontenera Flatcar systemu Linux.

Na wszystkich maszynach z systemem Linux postępuj zgodnie z instrukcjami opisanymi w [przewodniku po zestawie SDK Flatcar kontenera systemu Linux](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). Podczas uruchamiania skryptu upewnij się `image_to_vm.sh` , że został przekazany `--format=azure` do tworzenia wirtualnego dysku twardego platformy Azure.

## <a name="next-steps"></a>Następne kroki

Gdy masz plik VHD, możesz użyć utworzonego pliku, aby utworzyć nowe maszyny wirtualne na platformie Azure. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).
