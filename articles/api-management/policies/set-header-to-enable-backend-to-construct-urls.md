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
ms.openlocfilehash: df72ae5e9a1471e1387539d2c89a1eca0b09d866
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078616"
---
# <a name="add-a-forwarded-header"></a>Dodawanie przekazanego nagłówka

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak dodać przesłany dalej nagłówek do żądania przychodzącego, aby umożliwić interfejsowi API zaplecza konstruowanie prawidłowych adresów URL. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="code"></a>Kod

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-reference.md)