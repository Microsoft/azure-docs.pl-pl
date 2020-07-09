---
title: Usługa aparat rozpoznawania formularzy szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Szyfruj aparat rozpoznawania danych w spoczynku.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310720"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Szyfruj aparat rozpoznawania danych w czasie spoczynku

Aparat rozpoznawania formularzy platformy Azure automatycznie szyfruje dane po utrwaleniu ich w chmurze. Szyfrowanie aparatu rozpoznawania formularzy chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko dla zasobów utworzonych po 11 maja, 2020. Aby używać CMK z aparatem rozpoznawania formularzy, należy utworzyć nowy zasób aparatu rozpoznawania formularza. Po utworzeniu zasobu możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* [Aparat rozpoznawania formularzy formularz żądania klucza zarządzanego przez klienta](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


