---
title: Przykład zasad usługi Azure API Management — Dodawanie przekazanego nagłówka | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać przesłany dalej nagłówek w żądaniu przychodzącym, aby umożliwić interfejsowi API zaplecza konstruowanie odpowiednich adresów URL.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70067500"
---
# <a name="add-a-forwarded-header"></a>Dodawanie przekazanego nagłówka

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak dodać przesłany dalej nagłówek do żądania przychodzącego, aby umożliwić interfejsowi API zaplecza konstruowanie prawidłowych adresów URL. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="code"></a>Kod

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)
