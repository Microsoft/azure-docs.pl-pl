---
title: Przykład zasad usługi Azure API Management — filtrowanie zawartości odpowiedzi | Microsoft Docs
description: Przykład zasad usługi Azure API Management — demonstruje sposób filtrowania elementów danych z ładunku odpowiedzi na podstawie produktu skojarzonego z żądaniem.
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
ms.openlocfilehash: 462db76b06e5071571ae475d420a627e57dad92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70067756"
---
# <a name="filter-response-content"></a>Filtrowanie zawartości odpowiedzi

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak filtrować elementy danych z ładunku odpowiedzi na podstawie produktu skojarzonego z żądaniem. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **wychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

