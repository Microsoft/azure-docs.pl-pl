---
title: Szyfrowanie usługi Advisor metryk
titleSuffix: Azure Cognitive Services
description: Usługa klasyfikatora metryk szyfrowania danych magazynowanych.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909747"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Usługa klasyfikatora metryk szyfrowania danych magazynowanych

Usługa klasyfikator metryk automatycznie szyfruje dane po utrwaleniu ich w chmurze. Szyfrowanie usługi Advisor metryk chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i Prześlij informacje o [usłudze Advisor metryk Customer-Managed formularzu żądania klucza](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą Advisor metryk należy utworzyć nowy zasób usługi Advisor metryk i wybrać E0 jako warstwę cenową. Po utworzeniu zasobu klasyfikatora metryk z warstwą cenową E0 można użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza Customer-Managed usługi Advisor metryk](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](../../key-vault/general/overview.md)