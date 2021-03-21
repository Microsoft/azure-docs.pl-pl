---
title: Defender-IoT-Micro-Agent dla interfejsu API RTO platformy Azure
description: Reference API dla usługi Defender-IoT-Micro-Agent dla platformy Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 9a285636ba10f7ca0668f597d0e9016cff73494a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494295"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-Micro-Agent dla interfejsu API usługi Azure RTO (wersja zapoznawcza)

Ten interfejs API jest przeznaczony do użytku z usługą Defender-IoT-Micro-Agent tylko dla usługi Azure RTO. Aby uzyskać dodatkowe zasoby, zobacz temat [Defender-IoT-Micro-Agent for Azure RTO GitHub](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Włączanie usługi Defender-IoT-Micro-Agent for Azure RTO

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Opis

Ta procedura włącza podsystem Azure IoT Defender-IoT-Micro-Agent. Wewnętrzny komputer stanu zarządza zbieraniem zdarzeń zabezpieczeń i wysyła je do usługi Azure IoT Hub. Tylko jedno wystąpienie NX_AZURE_IOT_SECURITY_MODULE jest wymagane i jest niezbędne do zarządzania zbieraniem danych.

### <a name="parameters"></a>Parametry

| Nazwa | Opis |
|---------|---------|
| nx_azure_iot_ptr [in]    | Wskaźnik do `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Wartości zwracane

|Wartości zwracane  |Opis |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Pomyślnie włączono moduł zabezpieczeń usługi Azure IoT.     |
|NX_AZURE_IOT_FAILURE   |  Nie można włączyć modułu usługi Azure IoT Security z powodu błędu wewnętrznego.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Moduł zabezpieczeń wymaga prawidłowego wystąpienia #NX_AZURE_IOT.      |

### <a name="allowed-from"></a>Dozwolone z

Wątki

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Wyłączanie usługi Azure IoT Defender-IoT-Micro-Agent

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Opis

Ta procedura powoduje wyłączenie podsystemu usługi Azure IoT Defender-IoT-Micro-Agent.

### <a name="parameters"></a>Parametry

| Nazwa | Opis |
|---------|---------|
| nx_azure_iot_ptr [in]    | Wskaźnik do `NX_AZURE_IOT` . Jeśli wartość jest równa NULL, wystąpienie pojedyncze jest wyłączone. |

### <a name="return-values"></a>Wartości zwracane

|Wartości zwracane  |Opis |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Pomyślnie wyłączono moduł zabezpieczeń usługi Azure IoT.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Wystąpienie usługi Azure IoT Hub jest inne niż pojedyncze wystąpienie złożone.       |
|NX_AZURE_IOT_FAILURE    |  Nie można wyłączyć modułu zabezpieczeń usługi Azure IoT z powodu błędu wewnętrznego.       |

### <a name="allowed-from"></a>Dozwolone z

Wątki


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozpoczynania pracy z usługą Azure RTO Defender-IoT-Micro-Agent, zobacz następujące artykuły:

- Zapoznaj się z [omówieniem](iot-security-azure-rtos.md)usługi Defender for IoT RTO Defender-IoT-Micro-Agent.
