---
title: Konfigurowanie uwierzytelniania klientów wywołań przychodzących — Azure Event Grid IoT Edge | Microsoft Docs
description: Skonfiguruj protokoły interfejsu API udostępniane przez Event Grid w IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841795"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurowanie uwierzytelniania klienta dla wywołań przychodzących

Ten przewodnik zawiera przykłady możliwych konfiguracji uwierzytelniania klienta dla modułu Event Grid. Moduł Event Grid obsługuje dwa typy uwierzytelniania klientów:

* Oparty na kluczu sygnatury dostępu współdzielonego (SAS)
* Oparte na certyfikatach

Wszystkie możliwe konfiguracje można znaleźć w przewodniku dotyczącym [zabezpieczeń i uwierzytelniania](security-authentication.md) .

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Włączanie uwierzytelniania klientów opartych na certyfikatach, bez certyfikatów z podpisem własnym

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Włączanie uwierzytelniania klientów opartych na certyfikatach, Zezwalaj na certyfikaty z podpisem własnym

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Ustaw właściwość **inbound__clientAuth__clientCert__allowUnknownCA** na **wartość true** tylko w środowiskach testowych, ponieważ zazwyczaj można używać certyfikatów z podpisem własnym. W przypadku obciążeń produkcyjnych zaleca się ustawienie dla tej właściwości **wartości false** i Certificates urzędu certyfikacji.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Włączanie uwierzytelniania klientów opartych na certyfikatach i sygnaturach dostępu współdzielonego

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Uwierzytelnianie klienta oparte na kluczach SAS umożliwia modułowi nieiot Edge zarządzanie i wykonywanie operacji w czasie wykonywania przy założeniu, że porty interfejsu API są dostępne poza siecią IoT Edge.
