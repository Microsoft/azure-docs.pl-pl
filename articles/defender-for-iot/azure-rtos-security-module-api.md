---
title: Interfejs API modułu zabezpieczeń dla usługi Azure RTOS
description: Dokumentacja interfejsu API dla modułu zabezpieczeń dla usługi Azure RTO.
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
ms.openlocfilehash: 8191d68f701a3be6a37d8623d2d900f6aa6c1851
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939542"
---
# <a name="security-module-for-azure-rtos-api-preview"></a>Moduł zabezpieczeń dla interfejsu API usługi Azure RTO (wersja zapoznawcza)

Ten interfejs API jest przeznaczony do użytku z modułem zabezpieczeń tylko dla usługi Azure RTO. Aby uzyskać dodatkowe zasoby, zobacz [moduł zabezpieczeń dla zasobu Azure RTO GitHub](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Włączanie modułu zabezpieczeń dla usługi Azure RTO

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Opis

Ta procedura włącza podsystem modułu zabezpieczeń Azure IoT. Wewnętrzny komputer stanu zarządza zbieraniem zdarzeń zabezpieczeń i wysyła je do usługi Azure IoT Hub. Tylko jedno wystąpienie NX_AZURE_IOT_SECURITY_MODULE jest wymagane i jest niezbędne do zarządzania zbieraniem danych.

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

## <a name="disable-azure-iot-security-module"></a>Wyłącz moduł zabezpieczeń usługi Azure IoT

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Opis

Ta procedura powoduje wyłączenie podsystemu modułu zabezpieczeń usługi Azure IoT.

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

Aby dowiedzieć się więcej na temat rozpoczynania pracy z modułem zabezpieczeń Azure RTO, zobacz następujące artykuły:

- Zapoznaj się z [omówieniem](iot-security-azure-rtos.md)modułu zabezpieczeń usługi Defender for IoT RTO.