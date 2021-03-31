---
title: Konfigurowanie IoT Edge tożsamości Azure Event Grid | Microsoft Docs
description: Skonfiguruj tożsamość modułu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171690"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Skonfiguruj tożsamość modułu Event Grid

W tym artykule pokazano, jak skonfigurować tożsamość siatki na krawędzi. Domyślnie moduł Event Grid przedstawia swój certyfikat tożsamości zgodnie z konfiguracją w ramach demona zabezpieczeń IoT. Event Grid na brzegu prezentuje swój certyfikat tożsamości z wychodzącymi wywołaniami, gdy dostarcza zdarzenia. Subskrybenci mogą następnie sprawdzić, czy jest to moduł Event Grid, który wysłał zdarzenie przed zaakceptowaniem.

Wszystkie możliwe konfiguracje można znaleźć w przewodniku dotyczącym [zabezpieczeń i uwierzytelniania](security-authentication.md) .

## <a name="always-present-identity-certificate"></a>Zawsze obecny certyfikat tożsamości
Oto Przykładowa konfiguracja, która zawsze przedstawia certyfikat tożsamości na wywołaniach wychodzących. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Nie podano certyfikatu tożsamości
Oto Przykładowa konfiguracja nieprzedstawiania certyfikatu tożsamości dla wywołań wychodzących. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
