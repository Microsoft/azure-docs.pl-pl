---
title: Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub | Microsoft Docs
description: Wytyczne dotyczące wycofania protokołu TLS 1,0 i 1,1 oraz obsługiwane szyfry w IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90006084"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub

Aby zapewnić najlepszą w swojej klasie szyfrowanie, IoT Hub jest przenoszona do Transport Layer Security (TLS) 1,2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. 

## <a name="timeline"></a>Oś czasu

IoT Hub będzie nadal obsługiwał protokół TLS 1.0/1.1 do momentu dalszej uwagi. Zaleca się jednak, aby wszyscy klienci migrowani do protokołu TLS 1,2 jak najszybciej.

## <a name="deprecating-tls-11-ciphers"></a>Przestarzałe szyfry protokołu TLS 1,1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Przestarzałe szyfry protokołu TLS 1,0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>Mechanizmy szyfrowania TLS 1,2

Zobacz [IoT Hub szyfrowania TLS 1,2](iot-hub-tls-support.md#cipher-suites).
 
## <a name="customer-feedback"></a>Opinie klientów

Chociaż Wymuszanie protokołu TLS 1,2 to Najlepsza w swojej klasie funkcja szyfrowania, która zostanie włączona zgodnie z harmonogramem, nadal będziemy chcieli poznać klientów odnośnie do ich określonych wdrożeń i problemów, które przyjmą protokół TLS 1,2. W tym celu można wysłać komentarze do programu [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
