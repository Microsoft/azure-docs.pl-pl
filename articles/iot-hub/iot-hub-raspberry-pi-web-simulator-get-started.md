---
title: Połącz Raspberry Pi Web symulator z platformą Azure IoT Hub (Node.js)
description: Połącz Raspberry Pi Web symulator z platformą Azure IoT Hub Raspberry Pi, aby wysyłać dane do chmury platformy Azure.
author: wesmc7777
manager: philmea
keywords: Raspberry Pi symulatora, Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi wysyła dane do chmury, Raspberry Pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: 702dee108577665eded6dd1a92203236d74e866e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91308352"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Połącz symulator online Raspberry Pi z platformą Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku rozpocznie się zapoznanie się z podstawą pracy z symulatorem online Raspberry Pi. Następnie dowiesz się, jak bezproblemowo połączyć symulatora pi z chmurą przy użyciu [usługi Azure IoT Hub](about-iot-hub.md).

<p>
<div id="diag" style="width:100%; text-align:center">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
</p>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</a>
</div>
</p>

Jeśli masz urządzenia fizyczne, odwiedź stronę [Connect Raspberry Pi do platformy IoT Hub Azure](iot-hub-raspberry-pi-kit-node-get-started.md) , aby rozpocząć pracę.

## <a name="what-you-do"></a>Co robisz

* Poznaj podstawy symulatora online Raspberry Pi.

* Utwórz centrum IoT.

* Zarejestruj urządzenie dla platformy Pi w centrum IoT.

* Uruchom przykładową aplikację w języku Pi, aby wysyłać symulowane dane czujników do centrum IoT Hub.

Połącz symulowane Raspberry Pi z tworzonym Centrum IoT. Następnie uruchamiasz przykładową aplikację z symulatorem, aby generować dane czujnika. Na koniec wysyłasz dane czujnika do centrum IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć usługę Azure IoT Hub i uzyskać nowe parametry połączenia z urządzeniem. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.

* Jak korzystać z symulatora online Raspberry Pi.

* Jak wysyłać dane czujnika do centrum IoT Hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Omówienie symulatora sieci Web Raspberry Pi

Kliknij przycisk, aby uruchomić symulator online Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Uruchom symulator Raspberry Pi</a>

W symulatorze sieci Web znajdują się trzy obszary.

1. Obszar montażu — domyślny obwód to urządzenie Pi połączone z czujnikiem BME280 i diodą LED. Obszar ten jest zablokowany w wersji zapoznawczej więc obecnie nie można go dostosowywać.

2. Obszar kodowania — edytor kodu online umożliwiający kodowanie, korzystając z urządzenia Raspberry Pi. Przykładowa aplikacja domyślna ułatwia zbieranie danych z czujnika BME280 i wysyłanie do Twojej usługi Azure IoT Hub. Aplikacja jest w pełni zgodna z rzeczywistymi urządzeniami Pi. 

3. Zintegrowane okno konsoli — pokazuje dane wyjściowe kodu. W górnej części tego okna znajdują się trzy przyciski.

   * **Uruchom** — uruchamia aplikację w obszarze kodowania.

   * **Resetuj** — resetuje obszar kodowania do domyślnej aplikacji przykładowej.

   * **Zwiń/rozwiń** — z prawej strony znajduje się przycisk umożliwiający zwijanie/rozwijanie okna konsoli.

> [!NOTE]
> Symulator sieci Web Raspberry Pi jest teraz dostępny w wersji zapoznawczej. Chcemy słyszeć Twój głos w [warunkom chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Kod źródłowy jest publiczny w serwisie [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Omówienie symulatora online](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji w internetowym symulatorze urządzenia Pi

1. W obszarze kodowania upewnij się, że pracujesz na domyślnej przykładowej aplikacji. W wierszu 15. zastąp symbol zastępczy parametrami połączenia urządzenia centrum IoT na platformie Azure.
1. 
   ![Zastępowanie parametrów połączenia urządzenia](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Wybierz polecenie **Uruchom** lub wpisz, `npm start` Aby uruchomić aplikację.

Powinny pojawić się następujące dane wyjściowe pokazujące dane czujnika i komunikaty wysyłane do ![ danych z czujnika danych wyjściowych usługi IoT Hub wysyłane z Raspberry Pi do centrum IoT Hub](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Odczytywanie komunikatów odebranych przez centrum

Jednym ze sposobów monitorowania komunikatów odebranych przez Centrum IoT Hub z symulowanego urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code w celu wysyłania i odbierania komunikatów między urządzeniem i IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomiono przykładową aplikację, która umożliwia zbieranie danych z czujnika i wysyłanie ich do centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
