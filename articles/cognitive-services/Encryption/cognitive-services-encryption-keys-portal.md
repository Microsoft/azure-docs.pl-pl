---
title: Customer-Managed kluczy dla Cognitive Services
titleSuffix: Cognitive Services
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować klucze zarządzane przez klienta przy użyciu Azure Key Vault. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: 7c7476a3ab885e9c127cbd571ad723864bf0d898
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534564"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Konfigurowanie kluczy zarządzanych przez klienta przy użyciu Azure Key Vault dla Cognitive Services

Proces włączania kluczy Customer-Managed z Azure Key Vault dla Cognitive Services zależy od produktu. Użyj tych linków, aby uzyskać instrukcje dotyczące poszczególnych usług:

## <a name="vision"></a>Obraz

* [Custom Vision danych w spoczynku](../custom-vision-service/encrypt-data-at-rest.md)
* [Szyfrowanie danych w spoczynku w usługach rozpoznawania twarzy](../face/encrypt-data-at-rest.md)
* [Rozpoznawanie formularzy danych w spoczynku](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Język

* [Language Understanding danych w spoczynku](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker danych w spoczynku](../QnAMaker/encrypt-data-at-rest.md)
* [Szyfrowanie danych w spoczynku w u usługi Translator](../translator/encrypt-data-at-rest.md)

## <a name="speech"></a>Mowa

* [Szyfrowanie mowy danych w spoczynku](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>Decyzja

* [Content Moderator danych w spoczynku](../Content-Moderator/encrypt-data-at-rest.md)
* [Szyfrowanie danych w spoczynku za pomocą personalizacji](../personalizer/encrypt-data-at-rest.md)
* [Doradca w zakresie metryk danych w spoczynku](../metrics-advisor/encryption.md)

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure Key Vault?](../../key-vault/general/overview.md)
* [Cognitive Services Customer-Managed żądania klucza](https://aka.ms/cogsvc-cmk)