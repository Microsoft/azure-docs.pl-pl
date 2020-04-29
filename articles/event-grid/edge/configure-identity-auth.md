---
title: Konfigurowanie IoT Edge tożsamości Azure Event Grid | Microsoft Docs
description: Skonfiguruj tożsamość modułu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841769"
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
