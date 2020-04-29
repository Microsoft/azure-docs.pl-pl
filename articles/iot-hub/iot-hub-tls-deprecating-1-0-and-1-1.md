---
title: Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub | Microsoft Docs
description: Wytyczne dotyczące wycofania protokołu TLS 1,0 i 1,1 oraz obsługiwane szyfry w IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381295"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub

Aby zapewnić najlepszą w swojej klasie szyfrowanie, IoT Hub jest przenoszona do Transport Layer Security (TLS) 1,2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. 

## <a name="timeline"></a>Oś czasu

IoT Hub będzie nadal obsługiwał protokół TLS 1.0/1.1 do momentu dalszej uwagi. Zaleca się jednak, aby wszyscy klienci migrowani do protokołu TLS 1,2 jak najszybciej.

## <a name="supported-ciphers"></a>Obsługiwane szyfry

Oś czasu dostępności różnych szyfrów używanych w uzgadnianiu protokołu TLS jest następująca:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (obecnie obsługiwane)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (będzie obsługiwana w drugiej połowie 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (będzie obsługiwana w drugiej połowie 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (będzie obsługiwana w drugiej połowie 2020)

## <a name="customer-feedback"></a>Opinie klientów

Chociaż Wymuszanie protokołu TLS 1,2 to Najlepsza w swojej klasie funkcja szyfrowania, która zostanie włączona zgodnie z harmonogramem, nadal będziemy chcieli poznać klientów odnośnie do ich określonych wdrożeń i problemów, które przyjmą protokół TLS 1,2. W tym celu można wysłać komentarze do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)programu.
