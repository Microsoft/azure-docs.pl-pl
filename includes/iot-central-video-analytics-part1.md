---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763441"
---
Przykładowa aplikacja zawiera dwa symulowane urządzenia i jedną bramę IoT Edge. W poniższych samouczkach przedstawiono dwa podejścia do eksperymentowania z i zrozumienia możliwości bramy:

* Utwórz bramę IoT Edge na maszynie wirtualnej platformy Azure i podłącz symulowany aparat.
* Utwórz bramę IoT Edge na rzeczywistym urządzeniu, takim jak Intel NUC, i podłącz rzeczywistą kamerę.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji dla sklepu detalicznego za pomocą szablonu aplikacji Azure IoT Central Video Analytics
> * Dostosowywanie ustawień aplikacji
> * Tworzenie szablonu urządzenia dla urządzenia bramy IoT Edge
> * Dodawanie urządzenia bramy do aplikacji IoT Central

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę serię samouczków, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).
* Jeśli używasz rzeczywistego aparatu fotograficznego, potrzebujesz łączności między urządzeniem IoT Edge i kamerą i potrzebujesz kanału **protokołu przesyłania strumieniowego w czasie rzeczywistym** .

## <a name="initial-setup"></a>Początkowa konfiguracja

W tych samouczkach można aktualizować i używać kilku plików konfiguracji. Początkowe wersje tych plików są dostępne w repozytorium [LVA-Gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub. Repozytorium zawiera plik tekstowy [ScratchPad](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) do pobrania i użycia w celu rejestrowania wartości konfiguracyjnych z wdrażanych usług. Ten plik ułatwia wykonywanie kolejnych kroków samouczków.

Utwórz folder o nazwie *LVA-Configuration* na maszynie lokalnej, aby zapisać kopie tych plików. Następnie kliknij prawym przyciskiem myszy poszczególne poniższe linki i wybierz polecenie **Zapisz jako** , aby zapisać plik w folderze *LVA-Configuration* :
