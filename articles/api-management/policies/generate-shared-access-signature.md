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
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078429"
---
# <a name="generate-shared-access-signature"></a>Generowanie sygnatury dostępu współdzielonego

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak wygenerować [sygnaturę dostępu współdzielonego](../../storage/common/storage-sas-overview.md) przy użyciu wyrażeń i przesłać żądanie do usługi Azure Storage przy użyciu zasad ponownego zapisywania identyfikatorów URI. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-reference.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-reference.md)