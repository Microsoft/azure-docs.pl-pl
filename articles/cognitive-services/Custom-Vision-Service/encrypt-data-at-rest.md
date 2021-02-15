---
title: Custom Vision szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych przechowywane dla Custom Vision i sposób włączania usługi CMK oraz zarządzania nią.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524540"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision szyfrowanie danych magazynowanych

Usługa Azure Custom Vision automatycznie szyfruje dane po utrwaleniu ich w chmurze. Custom Vision szyfrowanie chroni dane i pomaga spełnić wymagania dotyczące zabezpieczeń i zgodności organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko dla zasobów utworzonych po 11 maja, 2020. Aby użyć CMK z Custom Vision, należy utworzyć nowy zasób Custom Vision. Po utworzeniu zasobu możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

## <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są obecnie dostępne w następujących regionach:

* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* East US
* US Gov Wirginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę usług, które obsługują CMK, zobacz [klucze zarządzane przez klienta dla Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co to jest Azure Key Vault](../../key-vault/general/overview.md)?
* [Formularz żądania klucza Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)