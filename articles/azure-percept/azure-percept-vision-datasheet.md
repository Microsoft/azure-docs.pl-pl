---
title: Arkusz danych wizji usługi Azure Percept
description: Zapoznaj się z arkuszem danych wizji usługi Azure Percept, aby uzyskać szczegółowe specyfikacje urządzeń
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662886"
---
# <a name="azure-percept-vision-datasheet"></a>Arkusz danych wizji usługi Azure Percept

Wymienione poniżej specyfikacje dotyczą urządzenia z obsługą platformy Azure [Percept.](./azure-percept-dk-datasheet.md)

|Specyfikacja produktu           |Wartość     |
|--------------------------------|---------------------|
|Docelowe branże               |Produkcja <br> Budynki inteligentne <br> Auto <br> Retail |
|Scenariusze Hero                  |Analiza klientów <br> Dostępność na półce <br> Zmniejszenie zmniejszenia <br> Zabezpieczenia/nadzór <br> Bezpieczeństwo w miejscu pracy|
|Wymiary                      |Watch 42 mm x Watch 42 mm x 40mm (zestaw modelu SoM "Azure Percept Vision" z obudową) <br> Watch 42 mm x Watch 42 mm x 6mm (mikroukładem "Vision SoM")|
|Płaszczyzna kontroli zarządzania        |Aktualizacja urządzenia Azure (ADU)          |
|Obsługiwane oprogramowanie i usługi |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [Środowisko uruchomieniowe ONNX](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Aktualizacja urządzenia platformy Azure |
|Przyspieszenie AI                 |Procesor Intel Movidius wyposażono X (MA2085) Visioning Unit (VPU) z technologią Intel Camera z integracją, 0,7 |
|Czujniki i wskaźniki wizualizacji   |Aparat obsługi rozproszonej 5670 <br> GSO GS8882AA Lens (rozdzielczość: 5MP na 30FPS, Distance: 50cm-nieskończoność, FoV: 120 stopni, Color: Wide Dynamic Range, stała ostra krocząca          |
|Obsługa aparatu                  |Aparaty RGB, IR i głębokości <br> 2 aparaty mogą być uruchamiane jednocześnie |
|Crypto-Controller zabezpieczeń      |ST-Micro STM32L462CE      |
|Składnik obsługi wersji/identyfikatora       |PAMIĘĆ 64 KB |
|Pamięć                          |LPDDR4 2 GB     |
|Zasilanie                           |3,5 W     |
|Porty                           |1x USB 3,0 typ C <br> 2. MIPI 4 (do 1,5 GB/s na tor)     |
|Interfejsy kontroli              |2x I2C <br> 2. SPI <br> 6X PWM (system GPIO: 2. zegar, dwuzakresowa synchronizacja ramek, 2 nieużywane) <br> 2x zapasowy GPIO |
|Certyfikacja                   |CE <br> FCC      |
|Temperatura operacyjna           |od 0 do 27 stopni C (zestaw "Azure Percept Vision SoM" z obudową) <br> -10 do 70 stopni C ("mikroukład SoM") |
|Temperatura dotykowa               |<= 48 stopni C |
|Wilgotność względna               |8% do 90%    |