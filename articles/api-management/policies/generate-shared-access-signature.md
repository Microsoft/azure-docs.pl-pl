---
title: Przykładowe zasady zarządzania interfejsem API — generowanie sygnatury dostępu współdzielonego
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak wygenerować sygnaturę dostępu współdzielonego przy użyciu wyrażeń i przesłać żądanie do usługi Azure Storage za pomocą zasad ponownego zapisywania identyfikatorów URI.
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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243005"
---
# <a name="generate-shared-access-signature"></a>Generowanie sygnatury dostępu współdzielonego

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak wygenerować [sygnaturę dostępu współdzielonego](../../storage/common/storage-sas-overview.md) przy użyciu wyrażeń i przesłać żądanie do usługi Azure Storage przy użyciu zasad ponownego zapisywania identyfikatorów URI. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)
