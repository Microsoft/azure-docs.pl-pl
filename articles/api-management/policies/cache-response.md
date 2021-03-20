---
title: Przykładowe zasady zarządzania interfejsem API — Dodawanie funkcji do usługi zaplecza
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać możliwości do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody.
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
ms.openlocfilehash: 64db4e5451425002eeaac11695ac011a96047d9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076185"
---
# <a name="add-capabilities-to-a-backend-service"></a>Dodawanie funkcji do usługi wewnętrznej bazy danych

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który pokazuje, jak dodać możliwości do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-reference.md)