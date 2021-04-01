---
title: Przykładowe zasady zarządzania interfejsem API — filtr na adres IP przy użyciu Application Gateway
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — demonstruje sposób filtrowania adresu IP żądania przy użyciu Application Gateway.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076117"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtruj według adresu IP żądania przy użyciu Application Gateway

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak filtrować adres IP żądania, gdy dostęp do wystąpienia API Management jest uzyskiwany za pomocą Application Gateway lub innego pośrednika. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-reference.md)