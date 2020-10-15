---
title: Przykładowe zasady zarządzania interfejsem API — Autoryzuj żądanie przy użyciu zewnętrznego upoważnienia
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak autoryzować żądania przy użyciu zewnętrznego autoryzowanego upoważnienia do hermetyzacji niestandardowej lub starszej logiki uwierzytelniania/autoryzacji.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076236"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzacja żądań za pomocą zewnętrznego obiektu autoryzującego

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który demonstruje sposób zabezpieczania dostępu do interfejsu API przy użyciu zewnętrznego, autoryzowanego do hermetyzacji logiki uwierzytelniania niestandardowego/autoryzacji. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-reference.md)