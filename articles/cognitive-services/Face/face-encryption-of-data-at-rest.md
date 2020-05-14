---
title: Szyfrowanie usługi do obsługi danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Szyfrowanie usługi z danymi magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201943"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Szyfrowanie usługi do obsługi danych w spoczynku

Usługa twarzy automatycznie szyfruje dane po utrwaleniu jej w chmurze. Szyfrowanie usługi programu Front Data chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta usługi](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą czołową należy utworzyć nowy zasób usługi i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu platformy z użyciem warstwy cenowej E0 można skonfigurować swoją tożsamość zarządzaną za pomocą Azure Key Vault.

### <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są obecnie dostępne w następujących regionach:

* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA
* US Gov Wirginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez klienta usługi kroju](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


