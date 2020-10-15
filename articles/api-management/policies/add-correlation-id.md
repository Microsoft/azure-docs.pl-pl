---
title: Zasady zarządzania przykładowymi interfejsami API — dodawanie nagłówka zawierającego identyfikator korelacji
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać nagłówek zawierający identyfikator korelacji do żądania przychodzącego.
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
ms.openlocfilehash: 922565d26274aee12c9397c08c19330b4fce00e7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076304"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Dodawanie nagłówka zawierającego identyfikator korelacji

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak dodać nagłówek zawierający identyfikator korelacji do żądania przychodzącego. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-reference.md)