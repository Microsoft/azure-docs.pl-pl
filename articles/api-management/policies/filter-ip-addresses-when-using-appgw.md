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
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75942479"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtruj według adresu IP żądania przy użyciu Application Gateway

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak filtrować adres IP żądania, gdy dostęp do wystąpienia API Management jest uzyskiwany za pomocą Application Gateway lub innego pośrednika. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)
