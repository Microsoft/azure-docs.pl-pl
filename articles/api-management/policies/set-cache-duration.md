---
title: Przykładowe zasady zarządzania interfejsem API — Ustawianie czasu trwania pamięci podręcznej odpowiedzi
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak ustawić czas trwania pamięci podręcznej odpowiedzi przy użyciu wartości maxAge w Cache-Control nagłówku wysłanym przez zaplecze..
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442396"
---
# <a name="set-response-cache-duration"></a>Ustawianie czasu trwania pamięci podręcznej odpowiedzi

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak ustawić czas trwania pamięci podręcznej odpowiedzi przy użyciu wartości maxAge w Cache-Control nagłówku wysłanym przez zaplecze. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

