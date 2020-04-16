---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia planowanie, generowanie, a następnie przenoszenie własnych kluczy chronionych przez moduł HSM do użycia w usłudze Azure Key Vault. Znany również jako BYOK lub przynieść własny klucz.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429696"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importowanie kluczy chronionych przez moduł HSM do magazynu kluczy

Aby uzyskać dodatkową pewność, korzystając z usługi Azure Key Vault, można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). Usługa Azure Key Vault używa nCipher nShield rodziny modułów HSM (FIPS 140-2 Poziom 2 zatwierdzone) do ochrony kluczy.

Ta funkcja nie jest dostępna dla platformy Azure China 21Vianet.

> [!NOTE]
> Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../general/overview.md)  
> Aby zapoznać się z samouczkiem wprowadzenie, który obejmuje tworzenie magazynu kluczy chronionych przez moduł HSM, zobacz [Co to jest usługa Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Obsługiwane moduły HSM

Przenoszenie kluczy chronionych przez moduł HSM do usługi Key Vault jest obsługiwane za pomocą dwóch różnych metod w zależności od używanych modułów HSM. Skorzystaj z poniższej tabeli, aby określić, która metoda powinna być używana do generowania modułów HSM, a następnie przenieś własne klucze chronione przez moduł HSM do użycia z usługą Azure Key Vault. 

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Obsługiwana metoda transferu klucza HSM|
|---|---|---|---|
|nCipher (własy)|Producent|<ul><li>Rodzina hsmów nShield</li></ul>|[Użyj starszej metody BYOK](hsm-protected-keys-legacy.md)|
|Thales|Producent|<ul><li>Rodzina SafeNet Luna HSM 7 z oprogramowaniem układowym w wersji 7.3 lub nowszej</li></ul>| [Użyj nowej metody BYOK (wersja zapoznawcza)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix ( Fortanix )|HSM jako usługa|<ul><li>Samookadująca się usługa zarządzania kluczami (SDKMS)</li></ul>|[Użyj nowej metody BYOK (wersja zapoznawcza)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [najlepszymi praktykami usługi Key Vault,](../general/best-practices.md) aby zapewnić bezpieczeństwo, trwałość i monitorowanie kluczy.
