---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183581"
---
### <a name="retrieve-output-files"></a>Pobieranie plików wyjściowych

Przy użyciu witryny Azure Portal można pobrać wyjściowe pliki MP3 wygenerowane przez zadania ffmpeg. 

1. Kliknij pozycję **wszystkie usługi**  >  **konta magazynu**, a następnie kliknij nazwę konta magazynu.
2. Kliknij pozycję **obiekty blob**  >  *dane wyjściowe*.
3. Kliknij prawym przyciskiem myszy jeden z wyjściowych plików MP3, a następnie kliknij polecenie **Pobierz**. Postępuj zgodnie z monitami wyświetlanymi w przeglądarce, aby otworzyć lub zapisać plik.

![Pobieranie pliku wyjściowego](./media/batch-common-tutorial-download/download.png)

Mimo że nie pokazano tego w tym przykładzie, pliki można również pobrać programowo z węzłów obliczeniowych lub z kontenera magazynu.
