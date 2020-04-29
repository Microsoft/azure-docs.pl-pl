---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372173"
---
## <a name="about-cognitive-services-encryption"></a>Informacje o szyfrowaniu Cognitive Services

Dane są szyfrowane i odszyfrowywane przy użyciu zgodności ze standardem [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitowego szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) . Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane przez Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Domyślnie Twoja subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. W przypadku korzystania z warstwy cenowej obsługującej klucze zarządzane przez klienta można wyświetlić ustawienia szyfrowania dla zasobu w sekcji **szyfrowanie** [Azure Portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetl ustawienia szyfrowania](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

W przypadku subskrypcji, które obsługują tylko zarządzane przez firmę Microsoft klucze szyfrowania, nie będziesz mieć sekcji **szyfrowanie** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Istnieje również możliwość zarządzania subskrypcją przy użyciu własnych kluczy. Klucze zarządzane przez klienta (CMK), znane także jako dające własny klucz (BYOK), zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.
