---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750306"
---
Możesz zarządzać szyfrowaniem na poziomie każdego dysku zarządzanego przy użyciu własnych kluczy. Szyfrowanie po stronie serwera dla dysków zarządzanych z kluczami zarządzanymi przez klienta oferuje zintegrowane środowisko pracy z Azure Key Vault. Możesz zaimportować [klucze RSA](../articles/key-vault/keys/hsm-protected-keys.md) do Key Vault lub wygenerować nowe klucze rsa w Azure Key Vault. 

Usługa Azure Managed disks obsługuje szyfrowanie i odszyfrowywanie w pełni przejrzysty sposób przy użyciu funkcji [szyfrowania kopert](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Szyfruje ona dane przy użyciu klucza szyfrowania danych opartego na protokole [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, który jest z kolei chroniony przy użyciu kluczy. Usługa Storage generuje klucze szyfrowania danych i szyfruje je za pomocą kluczy zarządzanych przez klienta przy użyciu szyfrowania RSA. Szyfrowanie koperty pozwala na okresowe obracanie (zmiana) kluczy zgodnie z zasadami zgodności bez wpływu na maszyny wirtualne. Gdy obracasz klucze, usługa Storage ponownie szyfruje klucze szyfrowania danych przy użyciu nowych kluczy zarządzanych przez klienta. 

#### <a name="full-control-of-your-keys"></a>Pełna kontrola nad kluczami

Musisz udzielić dostępu do dysków zarządzanych w Key Vault, aby użyć kluczy do szyfrowania i odszyfrowywania danych. Pozwala to na pełną kontrolę nad danymi i kluczami. Możesz w dowolnym momencie wyłączyć klucze lub odwołać dostęp do dysków zarządzanych. Możesz również przeprowadzić inspekcję użycia klucza szyfrowania przy użyciu monitorowania Azure Key Vault, aby upewnić się, że tylko zarządzane dyski lub inne zaufane usługi platformy Azure uzyskują dostęp do kluczy.

W przypadku wersji Premium dysków SSD, standard dysków SSD i Standard HDD: po wyłączeniu lub usunięciu klucza wszystkie maszyny wirtualne z dyskami korzystającymi z tego klucza zostaną automatycznie zamknięte. Następnie maszyny wirtualne nie będą używane, chyba że klucz zostanie włączony ponownie lub przypiszesz nowy klucz.    

W przypadku Ultra disks: po wyłączeniu lub usunięciu klucza wszystkie maszyny wirtualne z użyciem klucza nie będą automatycznie zamykane. Po przydzieleniu i ponownym uruchomieniu maszyn wirtualnych dyski przestaną być używane przy użyciu klucza, a następnie maszyny wirtualne nie zostaną przywrócone do trybu online. Aby przywrócić maszyny wirtualne do trybu online, należy przypisać nowy klucz lub włączyć istniejący klucz.    

Na poniższym diagramie pokazano, w jaki sposób dyski zarządzane używają Azure Active Directory i Azure Key Vault do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Przepływ pracy dotyczący dysków zarządzanych i kluczy zarządzanych przez klienta. Administrator tworzy Azure Key Vault, tworzy zestaw szyfrowania dysków i konfiguruje zestaw szyfrowania dysków. Zestaw jest skojarzony z maszyną wirtualną, co pozwala dyskowi używać usługi Azure AD do uwierzytelniania":::

Na poniższej liście wyjaśniono diagram bardziej szczegółowo:

1. Administrator Azure Key Vault tworzy zasoby magazynu kluczy.
1. Administrator magazynu kluczy importuje klucze RSA do Key Vault lub generować nowe klucze RSA w Key Vault.
1. Administrator tworzy wystąpienie zasobu zestawu szyfrowania dysku, określając identyfikator Azure Key Vault i adres URL klucza. Zestaw szyfrowanie dysków jest nowym zasobem wprowadzonym w celu uproszczenia zarządzania kluczami dla dysków zarządzanych. 
1. Po utworzeniu zestawu szyfrowania dysku [zarządzana tożsamość przypisana przez system](../articles/active-directory/managed-identities-azure-resources/overview.md) jest tworzona w Azure Active Directory (AD) i skojarzona z zestawem szyfrowania dysków. 
1. Następnie administrator magazynu kluczy Azure przyznaje uprawnienia zarządzanej tożsamości do wykonywania operacji w magazynie kluczy.
1. Użytkownik maszyny wirtualnej tworzy dyski przez skojarzenie ich z zestawem szyfrowania dysków. Użytkownik maszyny wirtualnej może również włączyć szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta dla istniejących zasobów przez skojarzenie ich z zestawem szyfrowanie dysków. 
1. Dyski zarządzane używają tożsamości zarządzanej do wysyłania żądań do Azure Key Vault.
1. W przypadku odczytywania lub zapisywania danych dyski zarządzane wysyłają żądania do Azure Key Vault, aby zaszyfrować (otoczyć) i odszyfrować (odwinięcie) klucz szyfrowania danych w celu szyfrowania i odszyfrowywania danych. 

Aby odwołać dostęp do kluczy zarządzanych przez klienta, zobacz [Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) i [interfejs wiersza polecenia Azure Key Vault](/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Automatyczne rotacja kluczy zarządzanych przez klienta (wersja zapoznawcza)

Możesz włączyć automatyczne rotację kluczy do najnowszej wersji klucza. Dysk odwołuje się do klucza za pośrednictwem jego zestawu szyfrowania dysków. Po włączeniu automatycznego rotacji dla zestawu szyfrowania dysku system automatycznie zaktualizuje wszystkie dyski zarządzane, migawki i obrazy, do których odwołuje się zestaw szyfrowania dysku, aby użyć nowej wersji klucza w ciągu jednej godziny. Ta funkcja jest obecnie dostępna w ograniczonej liczbie regionów w wersji zapoznawczej. Aby uzyskać dostęp do regionu, zobacz sekcję [Obsługiwane regiony](#supported-regions) .