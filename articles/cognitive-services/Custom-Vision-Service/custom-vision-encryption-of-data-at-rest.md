---
title: Custom Vision szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Custom Vision szyfrowanie danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202272"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision szyfrowanie danych magazynowanych

Usługa Azure Custom Vision automatycznie szyfruje dane po utrwaleniu ich w chmurze. Custom Vision szyfrowanie chroni dane i pomaga spełnić wymagania dotyczące zabezpieczeń i zgodności organizacji.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko dla zasobów utworzonych po 11 maja, 2020. Aby użyć CMK z Custom Vision, należy utworzyć nowy zasób Custom Vision. Po utworzeniu zasobu możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

### <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są obecnie dostępne w następujących regionach:

* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA
* US Gov Wirginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez klienta Custom Vision](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


