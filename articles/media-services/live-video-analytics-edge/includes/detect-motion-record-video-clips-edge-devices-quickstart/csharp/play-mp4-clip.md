---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682250"
---
Pliki MP4 są zapisywane w katalogu na urządzeniu brzegowym skonfigurowanym w pliku *ENV* przy użyciu klucza OUTPUT_VIDEO_FOLDER_ON_DEVICE. Jeśli użyto wartości domyślnej, wyniki powinny znajdować się w folderze */var/media/* .

Aby odtworzyć klip MP4:

1. Przejdź do grupy zasobów, Znajdź maszynę wirtualną, a następnie połącz się za pomocą usługi Azure bastionu.

    ![Grupa zasobów](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Zaloguj się przy użyciu poświadczeń, które zostały wygenerowane podczas [konfigurowania zasobów platformy Azure](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. W wierszu polecenia przejdź do odpowiedniego katalogu. Domyślna lokalizacja to */var/media*. Pliki MP4 powinny być widoczne w katalogu.

    ![Dane wyjściowe](../../../media/quickstarts/samples-output.png) 

1. Aby skopiować pliki na komputer lokalny, użyj [bezpiecznego kopiowania (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) . 
1. Odtwórz pliki przy użyciu programu [VLC Media Player](https://www.videolan.org/vlc/) lub dowolnego innego odtwarzacza MP4.
