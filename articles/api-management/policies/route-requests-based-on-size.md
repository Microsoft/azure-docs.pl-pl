---
title: Przykładowe zasady zarządzania interfejsem API — żądanie trasy na podstawie rozmiaru treści wiadomości
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak kierować żądania na podstawie rozmiaru ich jednostek.
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442425"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Kierowanie żądania na podstawie rozmiaru jego treści

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak kierować żądania na podstawie rozmiaru ich jednostek. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

