---
title: Przykładowe zasady zarządzania interfejsem API — wysyłanie informacji kontekstu żądania do usługi zaplecza
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak wysyłać informacje kontekstu żądania do usługi zaplecza.
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
ms.openlocfilehash: 7782af3c8a533ceb3a6d2bd3b412c21469f9a021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078820"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Wysyłanie informacji o kontekście żądania do usługi zaplecza

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak wysyłać informacje kontekstu żądania do usługi zaplecza. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-reference.md)