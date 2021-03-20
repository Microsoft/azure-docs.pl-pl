---
ms.openlocfilehash: dfb887004cd29b5bd9f1d9886b7dfa5f43c83dbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531158"
---
Pliki MP4 są zapisywane w katalogu na urządzeniu brzegowym skonfigurowanym w pliku *ENV* przy użyciu klucza VIDEO_OUTPUT_FOLDER_ON_DEVICE. Jeśli użyto wartości domyślnej, wyniki powinny znajdować się w folderze */var/media/* .

Aby odtworzyć klip MP4:

1. Przejdź do grupy zasobów, Znajdź maszynę wirtualną, a następnie połącz się za pomocą usługi Azure bastionu.

    ![Grupa zasobów](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Zaloguj się przy użyciu poświadczeń, które zostały wygenerowane podczas [konfigurowania zasobów platformy Azure](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. W wierszu polecenia przejdź do odpowiedniego katalogu. Domyślna lokalizacja to */var/media*. Pliki MP4 powinny być widoczne w katalogu.

    ![Dane wyjściowe](../../../media/quickstarts/samples-output.png) 

1. Aby skopiować pliki na komputer lokalny, użyj [bezpiecznego kopiowania (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) . 
1. Odtwórz pliki przy użyciu programu [VLC Media Player](https://www.videolan.org/vlc/) lub dowolnego innego odtwarzacza MP4.
