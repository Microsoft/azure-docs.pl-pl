---
title: Usługa Microsoft Connected Cache w ramach Azure IoT Edge konfiguracji przemysłowego IoT | Microsoft Docs
description: Samouczek Connected Cache microsoft Azure IoT Edge konfiguracji przemysłowego IoT
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811818"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Przykład Connected Cache wdrażania w wersji zapoznawczej firmy Microsoft: usługa Microsoft Connected Cache w ramach Azure IoT Edge konfiguracji przemysłowego IoT

Sieci produkcyjne są często zorganizowane w warstwach hierarchicznych zgodnie z modelem [sieci Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (uwzględnionym w standardach [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) i [ISA 99).](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) W tych sieciach tylko górna warstwa ma łączność z chmurą, a niższe warstwy w hierarchii mogą komunikować się tylko z sąsiednimi warstwami północ i południe.

Ten przykład usługi GitHub, [Azure IoT Edge dla przemysłowego IoT,](https://github.com/Azure-Samples/iot-edge-for-iiot)wdraża następujące elementy:

* Symulowana sieć Purdue na platformie Azure
* Zasoby przemysłowe 
* Hierarchia bram Azure IoT Edge sieci
  
Te składniki będą używane do pozyskiwania danych przemysłowych i bezpiecznego przekazywania ich do chmury bez naruszania bezpieczeństwa sieci. Program Microsoft Connected Cache można wdrożyć w celu obsługi pobierania zawartości na wszystkich poziomach w sieci zgodnej z standardem ISA 95.

Kluczem do konfigurowania wdrożeń usługi Microsoft Connected Cache w sieci zgodnej ze standardem ISA 95 jest skonfigurowanie zarówno serwera *proxy* OT, jak i hosta nadrzędnego w bramie L3 IoT Edge wirtualnej.

1. Konfigurowanie wdrożeń Connected Cache Microsoft na poziomach L5 i L4 zgodnie z opisem w przykładzie Two-Level nested IoT Edge gateway 
2. Wdrożenie na bramie IoT Edge L3 musi określać:
   
   * UPSTREAM_HOST — adres IP/FQDN bramy L4 IoT Edge, której zawartość będzie żądać Connected Cache L3 firmy Microsoft.
   * UPSTREAM_PROXY — adres IP/nazwa FQDN:PORT serwera proxy OT.

3. Serwer proxy OT musi dodać do listy zezwalanych adres FQDN/IP L4 MCC.

Aby sprawdzić, czy Connected Cache Microsoft działa prawidłowo, wykonaj następujące polecenie w terminalu urządzenia IoT Edge, hostowanie modułu lub dowolnego urządzenia w sieci. Zastąp wartości adresem IP lub nazwą hosta bramy \<Azure IoT Edge Gateway IP\> IoT Edge wirtualnej. (Aby uzyskać informacje na temat widoczności tego raportu, zobacz szczegóły zmiennych środowiskowych).

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```