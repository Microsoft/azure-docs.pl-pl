---
title: Przykładowe zasady zarządzania interfejsem API — wysyłaj błędy do rozwiązania Stackify na potrzeby rejestrowania
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać zasady rejestrowania błędów w celu wysłania błędów do rozwiązania Stackify na potrzeby rejestrowania.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072071"
---
# <a name="send-errors-to-stackify-for-logging"></a>Wysyłaj błędy do rozwiązania Stackify na potrzeby rejestrowania

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak dodać zasady rejestrowania błędów w celu wysłania błędów do rozwiązania Stackify na potrzeby rejestrowania. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **on-Error** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-reference.md)