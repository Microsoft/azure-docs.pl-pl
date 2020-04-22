---
title: Łączenie ruuvitagu w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak podłączyć czujnik środowiska RuuviTag do aplikacji IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 93e4d3d0bed9090573d2b6ee87a29b86ccd72e42
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758943"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Podłączanie czujnika RuuviTag do aplikacji Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym artykule opisano, jak jako konstruktor rozwiązań można podłączyć czujnik RuuviTag do aplikacji Microsoft Azure IoT Central.

Co to jest tag Ruuvi?

RuuviTag to zaawansowana platforma do ostrzegania czujników open source, zaprojektowana z myślą o zaspokajaniu potrzeb klientów biznesowych, programistów, twórców, studentów i hobbystów. Urządzenie jest skonfigurowane do pracy, gdy tylko wyjmiesz je z pudełka i jest gotowe do wdrożenia w miejscu, w którym jest potrzebne. Jest to sygnalizator Bluetooth LE z wbudowanym czujnikiem środowiska i akcelerometrem.

RuuviTag komunikuje się za pośrednictwem ble (Bluetooth Low Energy) i wymaga urządzenia bramy do rozmowy z usługą Azure IoT Central. Upewnij się, że masz urządzenie bramy, takie jak Rigado Cascade 500, konfiguracja, aby umożliwić RuuviTag połączyć się z IoT Central.

Postępuj zgodnie z [instrukcjami tutaj,](./howto-connect-rigado-cascade-500.md) jeśli chcesz skonfigurować urządzenie bramy Rigado Cascade 500.

## <a name="prerequisites"></a>Wymagania wstępne

Aby połączyć czujniki RuuviTag, potrzebne są następujące zasoby:

* Czujnik RuuviTag. Aby uzyskać więcej informacji, odwiedź [RuuviTag](https://ruuvi.com/).
* Urządzenie Rigado Cascade 500 lub inna brama BLE. Aby uzyskać więcej informacji, odwiedź [Stronę Rigado](https://www.rigado.com/).
* Aplikacja Azure IoT Central. Aby uzyskać więcej informacji, zobacz [tworzenie nowej aplikacji](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Dodawanie szablonu urządzenia RuuviTag

Aby wbudowany czujnik RuuviTag do wystąpienia aplikacji usługi Azure IoT Central, należy skonfigurować odpowiedni szablon urządzenia w aplikacji.

Aby dodać szablon urządzenia RuuviTag:

1. Przejdź do karty ***Szablony urządzeń*** w lewym okienku, wybierz](./media/howto-connect-ruuvi/devicetemplate-new.png) + **Nowy**: ![Tworzenie nowego szablonu urządzenia Strona umożliwia utworzenie ***szablonu niestandardowego*** lub ***użycie wstępnie skonfigurowanego szablonu urządzenia***
1. Wybierz szablon urządzenia RuuviTag z listy wstępnie skonfigurowanych szablonów ![urządzeń, jak pokazano poniżej: Wybierz szablon urządzenia RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Wybierz ***dalej: Dostosuj,*** aby przejść do następnego kroku.
1. Na następnym ekranie wybierz pozycję ***Utwórz,*** aby pokłować szablon urządzenia C500 do aplikacji IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Podłączanie czujnika RuuviTag

Jak wspomniano wcześniej, aby połączyć RuuviTag z aplikacją IoT Central, musisz skonfigurować urządzenie bramy. Poniższe kroki zakładają, że masz skonfigurowane urządzenie bramy Rigado Cascade 500.  

1. Włącz urządzenie Rigado Cascade 500 i podłącz go do połączenia sieciowego (przez Ethernet lub bezprzewodową)
1. Wyjmij pokrywę z RuuviTag i pociągnij plastikową zaczepkę, aby zabezpieczyć połączenie z baterią.
1. Umieść RuuviTag w pobliżu bramy Rigado Cascade 500, która jest już skonfigurowana w aplikacji IoT Central.
1. W ciągu zaledwie kilku sekund urządzenie RuuviTag powinno pojawić się na liście urządzeń w umieszczona w UIT Central.  
    ![Lista urządzeń RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Teraz można używać tego RuuviTag w aplikacji IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Tworzenie symulowanego RuuviTag

Jeśli nie masz fizycznego urządzenia RuuviTag, możesz utworzyć symulowany czujnik RuuviTag do testowania w aplikacji Azure IoT Central.

Aby utworzyć symulowany RuuviTag:

1. Wybierz **urządzenia > RuuviTag**.
1. Wybierz **+ Nowy**.
1. Określ **unikatowy identyfikator urządzenia** i przyjazną **nazwę urządzenia**.  
1. Włącz ustawienie **Symulowane.**
1. Wybierz pozycję **Utwórz**.  

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, niektóre z sugerowanych następnych kroków to:

- Przeczytaj o [łączności urządzenia w usłudze Azure IoT Central](./concepts-get-connected.md)
- Dowiedz się, jak [monitorować łączność urządzenia przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
