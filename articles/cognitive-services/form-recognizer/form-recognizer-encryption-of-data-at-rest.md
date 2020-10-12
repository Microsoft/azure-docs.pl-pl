---
title: Usługa aparat rozpoznawania formularzy szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych przechowywane przez aparat rozpoznawania formularzy oraz sposób włączania CMK i zarządzania nim.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: c959231826125349ecd6a62afe529248f7ac2eec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326800"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Szyfruj aparat rozpoznawania danych w czasie spoczynku

Aparat rozpoznawania formularzy platformy Azure automatycznie szyfruje dane podczas ich utrwalania w chmurze. Szyfrowanie aparatu rozpoznawania formularzy chroni dane, aby pomóc spełnić wymagania dotyczące zabezpieczeń i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko dla zasobów utworzonych po 11 maja, 2020. Aby używać CMK z aparatem rozpoznawania formularzy, należy utworzyć nowy zasób aparatu rozpoznawania formularza. Po utworzeniu zasobu możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza Customer-Managed rozpoznawania formularzy](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)