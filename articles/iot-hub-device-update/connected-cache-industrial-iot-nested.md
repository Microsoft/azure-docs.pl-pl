---
title: Połączona pamięć podręczna firmy Microsoft w Azure IoT Edge na potrzeby konfiguracji przemysłowej usługi IoT | Microsoft Docs
description: Połączona pamięć podręczna firmy Microsoft w ramach Azure IoT Edge dla usługi przemysłowej konfiguracji usługi IoT
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664777"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Przykład scenariusza wdrożenia w podglądzie połączonej pamięci podręcznej firmy Microsoft: połączona pamięć podręczna firmy Microsoft w Azure IoT Edge dla konfiguracji usługi IoT

Sieci produkcyjne są często organizowane w warstwach hierarchicznych po [modelu sieci Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (uwzględnionym w standardach [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) i [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) ). W tych sieciach tylko Górna warstwa ma łączność z chmurą, a niższe warstwy w hierarchii mogą komunikować się tylko z sąsiednimi warstwami Północne i Południowe.

Ten przykład w serwisie GitHub [Azure IoT Edge w przypadku przemysłu IoT](https://github.com/Azure-Samples/iot-edge-for-iiot), wdraża następujące elementy:

* Symulowana sieć Purdue na platformie Azure
* Zasoby przemysłowe 
* Hierarchia Azure IoT Edge bram
  
Te składniki będą używane do pozyskiwania danych przemysłowych i bezpiecznego przekazywania ich do chmury bez naruszenia bezpieczeństwa sieci. Połączona pamięć podręczna firmy Microsoft może zostać wdrożona w celu obsługi pobierania zawartości na wszystkich poziomach w sieci zgodnej z programem ISA 95.

Klucz do konfigurowania wdrożeń połączonej pamięci podręcznej firmy Microsoft w ramach sieci zgodnej z programem ISA 95 konfiguruje zarówno serwer proxy, *jak i* hosta nadrzędnego na bramie IoT Edge L3.

1. Skonfiguruj wdrożenia podłączane pamięci podręcznej firmy Microsoft na poziomach P5 i P4, zgodnie z opisem w przykładowej IoT Edge bramy Two-Level zagnieżdżonych 
2. Wdrożenie w bramie IoT Edge L3 musi określać:
   
   * UPSTREAM_HOST — adres IP/nazwa FQDN bramy P4 IoT Edge, do której będzie zażądać zawartość w połączonej pamięci podręcznej L3 firmy Microsoft.
   * UPSTREAM_PROXY — adres IP/nazwa FQDN: PORT serwera niezwiązanego z serwerem proxy.

3. Niemniej serwer proxy musi dodać nazwę FQDN/adres IP P4 MCC do listy dozwolonych.

Aby sprawdzić, czy połączona pamięć podręczna firmy Microsoft działa prawidłowo, należy wykonać następujące polecenie w terminalu IoT Edge urządzenia, hostować moduł lub dowolne urządzenie w sieci.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```