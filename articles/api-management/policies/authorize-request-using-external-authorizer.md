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
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442495"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzacja żądań za pomocą zewnętrznego obiektu autoryzującego

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który demonstruje sposób zabezpieczania dostępu do interfejsu API przy użyciu zewnętrznego, autoryzowanego do hermetyzacji logiki uwierzytelniania niestandardowego/autoryzacji. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)
