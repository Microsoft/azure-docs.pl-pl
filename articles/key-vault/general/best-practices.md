---
title: Najlepsze rozwiązania dotyczące korzystania z Key Vault Azure Key Vault | Microsoft Docs
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi Azure Key Vault, w tym kontroli dostępu, kiedy należy używać oddzielnych magazynów kluczy, tworzenia kopii zapasowych, rejestrowania i opcji odzyskiwania.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: e81cbd7e6584f4a280ab9507a989b52d3b188f2d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566612"
---
# <a name="best-practices-to-use-key-vault"></a>Najlepsze rozwiązania w zakresie używania Key Vault

## <a name="use-separate-key-vaults"></a>Korzystanie z oddzielnych magazynów kluczy

Nasze zalecenie polega na użyciu magazynu dla każdej aplikacji na środowisko (projektowanie, przedprodukcyjne i produkcyjne). Ułatwia to nie udostępnianie wpisów tajnych w różnych środowiskach i zmniejsza zagrożenie w przypadku naruszenia.

## <a name="control-access-to-your-vault"></a>Kontrola dostępu do magazynu

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko na autoryzowane aplikacje i użytkowników. Ten [artykuł](secure-your-key-vault.md) zawiera omówienie modelu dostępu Key Vault. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Sugestie dotyczące kontroli dostępu do magazynu są następujące:
1. Zablokuj dostęp do subskrypcji, grup zasobów i magazynów kluczy (RBAC systemu Azure)
2. Utwórz zasady dostępu dla każdego magazynu
3. Użyj najmniejszej nazwy podmiotu zabezpieczeń dostępu, aby udzielić dostępu
4. Włącz funkcję Zapora i [punkty końcowe usługi sieci wirtualnej](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Backup

Zadbaj o to, aby regularnie korzystać z magazynu w celu aktualizowania/usuwania/tworzenia obiektów w magazynie.

### <a name="azure-powershell-backup-commands"></a>Polecenia Azure PowerShell kopii zapasowej

* [Certyfikat kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Klucz kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Wpis tajny kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Polecenia tworzenia kopii zapasowej interfejsu CLI platformy Azure

* [Certyfikat kopii zapasowej](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Klucz kopii zapasowej](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Wpis tajny kopii zapasowej](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Włącz rejestrowanie

[Włącz rejestrowanie](logging.md) dla swojego magazynu. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włącz opcje odzyskiwania

1. Włącz [usuwanie nietrwałe](soft-delete-overview.md).
2. Włącz ochronę przed przeczyszczeniem, jeśli chcesz chronić przed usunięciem wpisu tajnego lub magazynu, nawet po włączeniu usuwania nietrwałego.