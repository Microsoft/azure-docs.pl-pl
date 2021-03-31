---
title: Łączenie RuuviTag na platformie Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak połączyć czujnik środowiska RuuviTag z aplikacją IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 7cf0d31895eaeaa41c23f9f07664b27bed1d3126
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006391"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Łączenie czujnika RuuviTag z aplikacją IoT Central platformy Azure

*Ten artykuł ma zastosowanie do konstruktorów rozwiązań.*

W tym artykule opisano sposób, w jaki Konstruktor rozwiązań umożliwia połączenie czujnika RuuviTag z aplikacją IoT Central Microsoft Azure.

Co to jest tag ruuvi?

RuuviTag to zaawansowana platforma typu "Open Source sensor" służąca do zaspokajania potrzeb klientów, deweloperów, twórców, studentów i hobby. Urządzenie jest skonfigurowane tak, aby działało zaraz po jego użyciu i będzie gotowe do wdrożenia, gdy będzie potrzebne. Jest to sygnał Bluetooth LE z czujnikiem środowiska i funkcją przyspieszeniomierza wbudowaną w.

RuuviTag komunikuje się za pośrednictwem sieci, a urządzenie bramy może komunikować się z usługą Azure IoT Central. Upewnij się, że masz urządzenie bramy, takie jak Rigado Kaskada 500, Instalator, aby umożliwić usłudze RuuviTag łączenie się z IoT Central.

Postępuj zgodnie z [instrukcjami w tym miejscu](./howto-connect-rigado-cascade-500.md) , jeśli chcesz skonfigurować urządzenie bramy Rigado z kaskadą 500.

## <a name="prerequisites"></a>Wymagania wstępne

Aby połączyć czujniki RuuviTag, potrzebne są następujące zasoby:

* Czujnik RuuviTag. Aby uzyskać więcej informacji, odwiedź stronę [RuuviTag](https://ruuvi.com/).
* Urządzenie Rigado kaskadowo 500 lub inną bramę w sieci. Aby uzyskać więcej informacji, odwiedź stronę [Rigado](https://www.rigado.com/).
* Aplikacja IoT Central platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej aplikacji](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Dodawanie szablonu urządzenia RuuviTag

Aby dołączyć czujnik RuuviTag do wystąpienia aplikacji platformy Azure IoT Central, musisz skonfigurować odpowiedni szablon urządzenia w aplikacji.

Aby dodać szablon urządzenia RuuviTag:

1. Przejdź do karty ***Szablony urządzeń** _ w lewym okienku, wybierz pozycję _* + Nowy * *: ![ Utwórz nowy szablon urządzenia ](./media/howto-connect-ruuvi/devicetemplate-new.png) Strona udostępnia opcję ***Utwórz szablon niestandardowy**_ lub _ *_Użyj wstępnie skonfigurowanego szablonu urządzenia_**
1. Wybierz szablon urządzenia RuuviTag z listy wstępnie skonfigurowanych szablonów urządzeń, jak pokazano poniżej:  ![ Wybierz szablon urządzenia RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Wybierz pozycję ***Dalej: Dostosuj*** , aby przejść do następnego kroku.
1. Na następnym ekranie wybierz pozycję ***Utwórz*** , aby dołączyć szablon urządzenia C500 do aplikacji IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Łączenie czujnika RuuviTag

Jak wspomniano wcześniej, aby połączyć RuuviTag z aplikacją IoT Central, należy skonfigurować urządzenie bramy. W poniższych krokach przyjęto założenie, że skonfigurowano urządzenie bramy Rigado z kaskadą 500.  

1. Włącz urządzenie kaskadowe Rigado 500 i połącz je z połączeniem sieciowym (za pośrednictwem sieci Ethernet lub sieci bezprzewodowej)
1. Pokaż pozostałą część RuuviTag i pociągnij kartę plastikową, aby zabezpieczyć połączenie z baterią.
1. Umieść RuuviTag blisko Rigado kaskadowego 500, który jest już skonfigurowany w aplikacji IoT Central.
1. W ciągu zaledwie kilku sekund RuuviTag powinien pojawić się na liście urządzeń w IoT Central.  
    ![Lista urządzeń RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Możesz teraz używać tego RuuviTag w aplikacji IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Tworzenie symulowanych RuuviTag

Jeśli nie masz fizycznego urządzenia RuuviTag, możesz utworzyć symulowaną sensor RuuviTag, który będzie używany do testowania w aplikacji IoT Central platformy Azure.

Aby utworzyć symulowaną RuuviTag:

1. Wybierz pozycję **urządzenia > RuuviTag**.
1. Wybierz pozycję **+ Nowe**.
1. Określ unikatowy **Identyfikator urządzenia** i przyjazną **nazwę urządzenia**.  
1. Włącz ustawienie **symulowane** .
1. Wybierz przycisk **Utwórz**.  

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Przeczytaj o [łączności urządzeń w usłudze Azure IoT Central](./concepts-get-connected.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
