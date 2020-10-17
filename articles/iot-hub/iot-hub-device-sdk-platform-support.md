---
title: Obsługa platformy zestawów SDK urządzeń Azure IoT | Microsoft Docs
description: Zestawy SDK urządzeń Open Source są dostępne w witrynie GitHub w językach C, .NET (C#), Java, Node.js i Python, aby połączyć urządzenia z usługą Azure IoT Hub i usługi Device Provisioning (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 5df36cdb3c02fd4c813625c4869c21581f009b02
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152213"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Obsługa platformy zestawów SDK urządzeń Azure IoT

Firma Microsoft dokłada starań, aby w sposób ciągły rozwijać platformę Azure IoT Hub. Firma Microsoft publikuje zestawy SDK urządzeń Open Source w usłudze GitHub, aby ułatwić Łączenie urządzeń z platformą Azure IoT Hub i usługą Device Provisioning. Zestawy SDK urządzeń są dostępne dla języków C, .NET (C#), Java, Node.js i Python. Firma Microsoft testuje każdy zestaw SDK, aby upewnić się, że jest on uruchamiany zgodnie z obsługiwanymi konfiguracjami opisanymi w sekcji [zestawy SDK firmy Microsoft i obsługa platformy urządzeń](#microsoft-sdks-and-device-platform-support) .

Oprócz zestawów SDK urządzeń firma Microsoft udostępnia kilka innych ścieżek, które umożliwiają klientom i deweloperom łączenie ich urządzeń z usługą Azure IoT:

* Firma Microsoft współpracuje z kilkoma firmami partnerskimi, aby ułatwić im publikowanie zestawów deweloperskich, w oparciu o zestaw SDK języka C usługi Azure IoT dla swoich platform sprzętowych.

* Firma Microsoft współpracuje z zaufanymi partnerami firmy Microsoft, aby zapewnić kiedykolwiek rozbudowany zestaw urządzeń, które zostały przetestowane i certyfikowane dla usługi Azure IoT. Bieżącą listę tych urządzeń można znaleźć w temacie [certyfikat platformy Azure dla usługi IoT](https://catalog.azureiotsolutions.com/).

* Firma Microsoft zapewnia warstwę abstrakcji platformy (PAL) w zestawie SDK usługi Azure IoT Hub Device, która ułatwia deweloperom łatwe przenoszenie zestawu SDK na swoją platformę. Aby dowiedzieć się więcej, zobacz [wskazówki dotyczące przenoszenia zestawu SDK języka C](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Ten temat zawiera informacje o zestawach SDK firmy Microsoft i obsługiwanych przez nie konfiguracjach platformy, a także o innych opcjach wymienionych powyżej.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK i obsługa platform urządzeń

Firma Microsoft publikuje zestawy SDK open source w usłudze GitHub dla następujących języków: C, .NET (C#), Node.js, Java i Python. Zestawy SDK i ich zależności są wymienione w tej sekcji. Zestawy SDK są obsługiwane na dowolnej platformie urządzeń, która spełnia te zależności.

Dla każdego z wymienionych zestawów SDK firma Microsoft:

* Ciągle kompiluje i uruchamia kompleksowe testy względem głównej gałęzi odpowiedniego zestawu SDK w usłudze GitHub na kilku popularnych platformach.  Aby zapewnić pokrycie testów w różnych wersjach kompilatora, zazwyczaj testuje się w oparciu o najnowszą wersję LTS i najbardziej popularną wersję.

* W razie potrzeby udostępnia wskazówki dotyczące instalacji lub pakiety instalacyjne.

* W pełni obsługuje zestawy SDK w witrynie GitHub z kodem typu open source, ścieżką dla wkładów klientów i pracą zespołu produktów z problemami z usługą GitHub.

### <a name="c-sdk"></a>ZESTAW SDK JĘZYKA C

[Zestaw SDK urządzeń platformy Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c) jest testowany z programem i obsługuje następujące konfiguracje.

| System operacyjny                  | Biblioteka TLS                  | Wymagania dodatkowe                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL lub BearSSL | Berkeley Sockets</br></br>Interfejs Portable Operating System (POSIX)                       |
| iOS 12,2            | OpenSSL                      | XCode emulowane w OSX 10.13.4                                                               |
| Rodzina systemów Windows 10   | SChannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [Zestawu deweloperskiego IoT dev Kit](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere systemu operacyjnego     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 lub ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

[Zestaw SDK urządzeń w języku Python platformy Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-python) jest testowany z programem i obsługuje następujące konfiguracje.

| System operacyjny                  | Compiler                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,5 lub nowszy |
| macOS High Sierra   | Python 2,7. *, 3,5 lub nowszy |
| Rodzina systemów Windows 10   | Python 2,7. *, 3,5 lub nowszy |

Tylko w wersji 3.5.3 lub nowszej obsługują asynchroniczne interfejsy API, zalecamy użycie wersji 3,7 lub nowszej.

### <a name="net-sdk"></a>Zestaw SDK .NET

[Zestaw SDK urządzeń platformy Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp) jest testowany z programem i obsługuje następujące konfiguracje.

| System operacyjny                                   | Standardowa                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Jednostki SKU systemu Windows 10 dla komputerów stacjonarnych i serwerów   | .NET Core 2,1, .NET Framework 4.5.1 lub .NET Framework 4,7 |

Zestawu .NET SDK można także użyć w połączeniu z usługą Windows IoT Core z [agentem urządzenia platformy Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) lub z [niestandardowym NTService, który może używać usługi RPC do komunikowania się z aplikacjami platformy UWP](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Zestaw SDK dla platformy Node.js

[Zestaw SDK urządzeń usługi Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) jest testowany z usługą i obsługuje następujące konfiguracje.

| System operacyjny                  | Wersja węzła    |
|---------------------|-----------------|
| Linux               | LTS i bieżące |
| Rodzina systemów Windows 10   | LTS i bieżące |

### <a name="java-sdk"></a>Zestaw SDK Java

[Zestaw SDK urządzeń Java IoT Hub platformy Azure](https://github.com/Azure/azure-iot-sdk-java) jest testowany z programem i obsługuje następujące konfiguracje.

| System operacyjny                     | Wersja środowiska Java |
|------------------------|--------------|
| Interfejs API systemu Android 28         | Java 8       |
| Linux x64             | Java 8       |
| Rodzina x64 systemu Windows 10  | Java 8       |

## <a name="partner-supported-development-kits"></a>Obsługiwane przez partnerów zestawy deweloperskie

Firma Microsoft współpracuje z różnymi partnerami, aby udostępnić zestawy deweloperskie dla kilku architektur mikroprocesorów. Ci partnerzy mogli przenieść zestaw SDK usługi Azure IoT C na swoją platformę. Partnerzy tworzą i obsługują warstwę abstrakcji platformy (PAL) zestawu SDK. Firma Microsoft współpracuje z tymi partnerami w celu zapewnienia rozszerzonej pomocy technicznej.

| Partner             | Urządzenia                            | Łącze                     | Pomoc techniczna |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP — Azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem Qualcomm MDM9206 LTE IoT     | [Zestaw SDK usługi Qualcomm LTE for IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST MICROELECTRONICS | Seria STM32L4 <br/> Seria STM32F4 <br/>  Seria STM32F7 <br/>  STM32L4 Discovery Kit dla węzła IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO — AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Pomoc techniczna](https://www.st.com/content/st_com/en/support/support-home.html)
| Narzędzia Texas Instruments   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [Wtyczka usługi Azure IoT dla SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum E2E TI](https://e2e.ti.com) <br/> [Forum usługi TI E2E dla CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Forum usługi TI E2E dla MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Przenoszenie zestawu SDK języka C Microsoft Azure IoT

Jeśli platforma urządzenia nie jest objęta jedną z poprzednich sekcji, można rozważyć przeprowadzenie przenoszenia zestawu SDK języka C usługi Azure IoT. Przenoszenie zestawu SDK języka C obejmuje głównie implementację warstwy abstrakcji platformy (PAL) zestawu SDK. PAL definiuje elementy pierwotne, które zapewniają klej między urządzeniem a funkcjami wyższego poziomu w zestawie SDK. Aby uzyskać więcej informacji, zobacz sekcję [wskazówki dotyczące przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Partnerzy firmy Microsoft i certyfikowane urządzenia IoT na platformie Azure

Firma Microsoft współpracuje z partnerami, aby ciągle rozszerzać usługę Azure IoT Universe za pomocą przetestowanych i certyfikowanych urządzeń Azure IoT.

* Aby przeglądać urządzenia z certyfikatem usługi Azure IoT, zobacz [certyfikat platformy Microsoft Azure dla IoT wykaz urządzeń](https://catalog.azureiotsolutions.com/).

* Aby dowiedzieć się więcej o ekosystemie certyfikatu platformy Azure dla IoT, zobacz [dołączanie do ekosystemu Certified for IoT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Nawiązywanie połączenia z IoT Hub bez zestawu SDK

Jeśli nie można użyć jednego z zestawów SDK urządzeń IoT Hub, można połączyć się bezpośrednio z IoT Hub przy użyciu obsługiwanego protokołu komunikacyjnego. Aby dowiedzieć się więcej, zobacz [programowanie bez zestawu Azure IoT SDK](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Pomoc techniczna i inne zasoby

Jeśli wystąpią problemy podczas korzystania z zestawów SDK urządzeń Azure IoT, istnieje kilka sposobów wyszukiwania pomocy technicznej poniżej. Aby uzyskać pełne informacje na temat wszystkich opcji pomocy technicznej, zobacz [Pomoc techniczna i opcje pomocy usługi Azure IoT](../iot-fundamentals/iot-support-help.md). 

**Raportowanie błędów** — usterki w zestawach SDK urządzeń można zgłaszać na stronie problemy odpowiedniego projektu GitHub. Poprawki są szybko wprowadzane do projektu w programie do aktualizacji produktów.

* [Problemy z zestawem SDK platformy Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemy z zestawem SDK platformy Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemy z zestawem SDK platformy Azure IoT Hub Java](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemy z zestawem SDK usługi Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemy z zestawem SDK platformy Azure IoT Hub Python](https://github.com/Azure/azure-iot-sdk-python/issues)

**Pytania techniczne** — możesz zadawać pytania techniczne na platformie [Microsoft Q&](/answers/topics/azure-iot-sdk.html) a i [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) za pomocą tagu *Azure-IoT-SDK*.

**Zespół pomocy technicznej firmy Microsoft** — użytkownicy z [planem pomocy](https://azure.microsoft.com/support/plans/) technicznej mogą skontaktować się z zespołem pomocy technicznej firmy Microsoft, tworząc nowe żądanie pomocy technicznej bezpośrednio z [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Żądania funkcji** — żądania funkcji usługi Azure IoT są śledzone za pośrednictwem [strony użytkownika](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="next-steps"></a>Następne kroki

* [Zestawy SDK urządzeń i usługi](iot-hub-devguide-sdks.md)
* [Wskazówki dotyczące przenoszenia](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)