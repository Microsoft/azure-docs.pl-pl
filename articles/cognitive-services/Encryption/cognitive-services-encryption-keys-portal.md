---
title: Klucze Customer-Managed dla Cognitive Services
titleSuffix: Cognitive Services
description: Informacje dotyczące konfigurowania kluczy zarządzanych przez klienta przy użyciu Azure Portal w programie Azure Key Vault. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1d161c82c087fd86a3774f0d121330260b1574e4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366098"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Skonfiguruj klucze zarządzane przez klienta za pomocą Azure Key Vault dla Cognitive Services

Proces włączania kluczy Customer-Managed w Azure Key Vault dla Cognitive Services zależy od produktu. Skorzystaj z tych linków, aby uzyskać instrukcje dotyczące usługi:

## <a name="vision"></a>Obraz

* [Custom Vision szyfrowanie danych magazynowanych](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [Szyfrowanie usług w czasie spoczynku](../Face/face-encryption-of-data-at-rest.md)
* [Szyfruj aparat rozpoznawania danych w czasie spoczynku](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>Język

* [Language Understanding szyfrowanie usługi danych w spoczynku](../LUIS/luis-encryption-of-data-at-rest.md)
* [QnA Maker szyfrowanie danych magazynowanych](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Szyfrowanie danych w usłudze translator](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>Decyzja

* [Content Moderator szyfrowanie danych magazynowanych](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Personalizacja szyfrowania danych magazynowanych](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure Key Vault](../../key-vault/general/overview.md)?
* [Formularz żądania klucza Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)