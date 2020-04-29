---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Ten artykuł ułatwia planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. Znany również jako BYOK lub własny klucz.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429696"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importowanie kluczy chronionych przez moduł HSM do usługi Key Vault

Aby uzyskać gwarancję, w przypadku korzystania z Azure Key Vault można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). W celu ochrony kluczy Azure Key Vault używa rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń (zweryfikowany poziom 2 trybu FIPS 140-2).

Ta funkcja jest niedostępna dla platformy Azure w Chinach.

> [!NOTE]
> Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../general/overview.md)  
> Aby zapoznać się z samouczkiem wprowadzającym, które obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz [co to jest Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Obsługiwane sprzętowych modułów zabezpieczeń

Transferowanie kluczy chronionych przez moduł HSM do Key Vault jest obsługiwane za pośrednictwem dwóch różnych metod, w zależności od używanej sprzętowych modułów zabezpieczeń. Skorzystaj z poniższej tabeli, aby określić, która metoda powinna zostać użyta do generowania sprzętowych modułów zabezpieczeń, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z Azure Key Vault. 

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Obsługiwana metoda modułu HSM — transfer klucza|
|---|---|---|---|
|Oprogramowanie wspomagające nCipher|Producent|<ul><li>Rodzina sprzętowego nshield sprzętowych modułów zabezpieczeń</li></ul>|[Użyj starszej metody BYOK](hsm-protected-keys-legacy.md)|
|Firmy Thales|Producent|<ul><li>Rodzina SafeNet Luna modułu HSM 7 z oprogramowaniem układowym w wersji 7,3 lub nowszej</li></ul>| [Użyj nowej metody BYOK (wersja zapoznawcza)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|Moduł HSM jako usługa|<ul><li>Samoobsługowa usługa zarządzania kluczami (SDKMS)</li></ul>|[Użyj nowej metody BYOK (wersja zapoznawcza)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [Key Vault najlepszych](../general/best-practices.md) rozwiązań, aby zapewnić bezpieczeństwo, trwałość i monitorowanie kluczy.
