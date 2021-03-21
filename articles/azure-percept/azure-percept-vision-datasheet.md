---
title: Arkusz danych wizji usługi Azure Percept
description: Zapoznaj się z arkuszem danych wizji usługi Azure Percept, aby uzyskać szczegółowe specyfikacje urządzeń
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 7bbb3a88bbc3011ec5dd917cdb0c1e49f7556aab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177168"
---
# <a name="azure-percept-vision-datasheet"></a>Arkusz danych wizji usługi Azure Percept

Wymienione poniżej specyfikacje dotyczą urządzenia z obsługą platformy Azure [Percept.](./azure-percept-dk-datasheet.md)

|Specyfikacja produktu           |Wartość     |
|--------------------------------|---------------------|
|Docelowe branże               |Produkcja <br> Budynki inteligentne <br> Auto <br> Retail |
|Scenariusze Hero                  |Analiza klientów <br> Dostępność na półce <br> Zmniejszenie zmniejszenia <br> Monitorowanie w miejscu pracy|
|Wymiary                      |Watch 42 mm x Watch 42 mm x 40mm (zestaw modelu SoM "Azure Percept Vision" z obudową) <br> Watch 42 mm x Watch 42 mm x 6mm (mikroukładem "Vision SoM")|
|Płaszczyzna kontroli zarządzania        |Aktualizacja urządzenia Azure (ADU)          |
|Obsługiwane oprogramowanie i usługi |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [Środowisko uruchomieniowe ONNX](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Aktualizacja urządzenia platformy Azure |
|Przyspieszenie AI                 |Procesor Intel Movidius wyposażono X (MA2085) Visioning Unit (VPU) z technologią Intel Camera z integracją, 0,7 |
|Czujniki i wskaźniki wizualizacji   |Czujnik aparatu Sony IMX219 z soczewką 6P<br>Rozwiązanie: 8MP o 30FPS, Distance: 50cm — nieskończoność<br>FoV: 120 stopni przekątnych, kolor: szeroki zakres dynamiczny, stała ostra krocząca|
|Obsługa aparatu                  |RGB <br> 2 aparaty mogą być uruchamiane jednocześnie |
|Crypto-Controller zabezpieczeń      |ST-Micro STM32L462CE      |
|Składnik obsługi wersji/identyfikatora       |PAMIĘĆ 64 KB |
|Pamięć                          |LPDDR4 2 GB     |
|Zasilanie                           |3,5 W     |
|Porty                           |1x USB 3,0 typ C <br> 2. MIPI 4 (do 1,5 GB/s na tor)     |
|Interfejsy kontroli              |2x I2C <br> 2. SPI <br> 6X PWM (system GPIO: 2. zegar, dwuzakresowa synchronizacja ramek, 2 nieużywane) <br> 2x zapasowy GPIO |
|Certyfikacja                   |FCC <br> IC <br> RoHS <br> ŚWIETLON <br> UL   |
|Temperatura operacyjna           |od 0 do 27 stopni C (zestaw "Azure Percept Vision SoM" z obudową) <br> -10 do 70 stopni C ("mikroukład SoM") |
|Temperatura dotykowa               |<= 48 stopni C |
|Wilgotność względna               |8% do 90%    |
