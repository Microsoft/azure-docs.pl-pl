---
title: Jak wygenerować klucze chronione & HSM — Azure Key Vault
description: Dowiedz się, jak planować, generować i przenosić własne klucze chronione przez moduł HSM do użycia z Azure Key Vault. Znana także jako BYOK lub bring your own key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: 171e0c03dc6f246d0f56d11f793ca711b0082f49
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588296"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importowanie kluczy chronionych przez moduł HSM do usługi Key Vault

Aby uzyskać dodatkową pewność, gdy używasz Azure Key Vault, możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają granic modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). Azure Key Vault ochrony kluczy używa rodziny modułów HSM nCipher nShield (zweryfikowanych w modelu FIPS 140-2 poziom 2).

Ta funkcja nie jest dostępna dla Azure (Chiny) — 21Vianet.

> [!NOTE]
> Aby uzyskać więcej informacji na Azure Key Vault, zobacz [Co to jest Azure Key Vault?](../general/overview.md)  
> Aby uzyskać samouczek z wprowadzeniem, który obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz Co to [jest Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Obsługiwane moduły HSM

Transferowanie kluczy chronionych przez moduł HSM do Key Vault jest obsługiwane za pośrednictwem dwóch różnych metod w zależności od stosowanych modułów HSM. Skorzystaj z poniższej tabeli, aby określić, która metoda ma być używana do generowania modułów HSM, a następnie przenieś własne klucze chronione przez moduł HSM do użycia z modułem Azure Key Vault. 

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele modułów HSM|Obsługiwana metoda transferu klucza HSM|
|---|---|---|---|
|[Szyfrowanie nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Producent<br/>Moduł HSM jako usługa|<ul><li>Rodzina modułów HSM nShield</li><li>nShield jako usługa</ul>|**Metoda 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (przestarzałe). Ta metoda nie będzie obsługiwana po <strong>30 czerwca 2021 r.</strong><br/>**Metoda 2. Użyj** [nowej metody BYOK](hsm-protected-keys-byok.md) (zalecane)|
|Thales|Producent|<ul><li>Rodzina Modułów HSM 7 z oprogramowaniem układowym w wersji 7.3 lub nowszej</li></ul>| [Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Producent<br/>Moduł HSM jako usługa|<ul><li>Self-Defending zarządzania kluczami (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|Marvell|Producent|Wszystkie moduły HSM LiquidSecurity z<ul><li>Oprogramowanie układowe w wersji 2.0.4 lub nowszej</li><li>Oprogramowanie układowe w wersji 3.2 lub nowszej</li></ul>|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|Kryptograficzne|ISV (Enterprise Key Management System)|Wiele marek i modeli modułów HSM, w tym<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Aby uzyskać szczegółowe informacje, zobacz [witrynę kryptograficzną](https://www.cryptomathic.com/azurebyok)|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|Securosys SA|Producent<br/>Moduł HSM jako usługa|Rodzina modułów HSM Primus, securosys clouds HSM|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Enterprise Key Management System)|Wiele marek i modeli modułów HSM, w tym<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Aby uzyskać [szczegółowe informacje, zobacz witrynę StorMagic](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|IBM|Producent|IBM 476x, CryptoExpress|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|Utimaco|Producent<br/>Moduł HSM jako usługa|u.trust Anchor, CryptoServer|[Korzystanie z nowej metody BYOK](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Następne kroki

* Przejrzyj omówienie [Key Vault zabezpieczeń,](../general/security-overview.md) aby zapewnić bezpieczeństwo, trwałość i monitorowanie kluczy.
* Pełny [opis nowej metody BYOK](./byok-specification.md) można znaleźć w specyfikacji BYOK