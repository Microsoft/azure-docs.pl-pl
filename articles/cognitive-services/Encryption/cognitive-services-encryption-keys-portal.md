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
ms.openlocfilehash: fca3fc80411432f2e82a68faa3b6f297b8ca5f9c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522006"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Skonfiguruj klucze zarządzane przez klienta za pomocą Azure Key Vault dla Cognitive Services

Proces włączania kluczy Customer-Managed w Azure Key Vault dla Cognitive Services zależy od produktu. Skorzystaj z tych linków, aby uzyskać instrukcje dotyczące usługi:

## <a name="vision"></a>Obraz

* [Custom Vision szyfrowanie danych magazynowanych](../Custom-Vision-Service/encrypt-data-at-rest.md)
* [Szyfrowanie usług w czasie spoczynku](../Face/encrypt-data-at-rest.md)
* [Szyfruj aparat rozpoznawania danych w czasie spoczynku](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Język

* [Language Understanding szyfrowanie usługi danych w spoczynku](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker szyfrowanie danych magazynowanych](../QnAMaker/encrypt-data-at-rest.md)
* [Szyfrowanie danych w usłudze translator](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>Decyzja

* [Content Moderator szyfrowanie danych magazynowanych](../Content-Moderator/encrypt-data-at-rest.md)
* [Personalizacja szyfrowania danych magazynowanych](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure Key Vault](../../key-vault/general/overview.md)?
* [Formularz żądania klucza Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)