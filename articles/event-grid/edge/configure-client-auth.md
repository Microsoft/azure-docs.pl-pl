---
title: Konfigurowanie uwierzytelniania klientów wywołań przychodzących — Azure Event Grid IoT Edge | Microsoft Docs
description: Zapoznaj się z możliwymi konfiguracjami uwierzytelniania klienta dla modułu Event Grid.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290776"
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
