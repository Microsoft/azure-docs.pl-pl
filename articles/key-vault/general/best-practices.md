---
title: Najlepsze rozwiązania dotyczące korzystania z Key Vault — Azure Key Vault | Microsoft Docs
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących Azure Key Vault, w tym o kontrolowaniu dostępu, o tym, kiedy używać oddzielnych magazynów kluczy, kopii zapasowej, rejestrowania i opcji odzyskiwania.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 83fc2639ccfeccb7de974739562e1a212bac78a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749910"
---
# <a name="best-practices-to-use-key-vault"></a>Najlepsze rozwiązania dotyczące korzystania z Key Vault

## <a name="use-separate-key-vaults"></a>Używanie oddzielnych magazynów kluczy

Naszym zaleceniem jest użycie magazynu na aplikację na środowisko (deweloperskie, przedprodukcyjne i produkcyjne). Ułatwia to nieudzielanie wpisów tajnych między środowiskami, a także zmniejsza zagrożenie w przypadku naruszenia zabezpieczeń.

## <a name="control-access-to-your-vault"></a>Kontrola dostępu do magazynu

Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla działalności firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowanym aplikacjom i użytkownikom. Ten [artykuł](security-overview.md) zawiera omówienie modelu Key Vault dostępu. Wyjaśniono w nim uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Sugestie podczas kontrolowania dostępu do magazynu są następujące:
1. Blokowanie dostępu do subskrypcji, grupy zasobów i magazynów kluczy (RBAC platformy Azure)
2. Tworzenie zasad dostępu dla każdego magazynu
3. Użyj jednostki dostępu z najmniejszymi uprawnieniami, aby udzielić dostępu
4. Włączanie punktów końcowych usługi zapory [i sieci wirtualnej](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Backup

Upewnij się, że regularnie tworzysz kopię zapasową magazynu po aktualizacji/usunięciu/utworzeniu obiektów w magazynie.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell polecenia kopii zapasowej

* [Certyfikat kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Klucz kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Klucz tajny kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Polecenia kopii zapasowej interfejsu wiersza polecenia platformy Azure

* [Certyfikat kopii zapasowej](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Klucz kopii zapasowej](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Klucz tajny kopii zapasowej](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Włączanie rejestrowania

[Włącz rejestrowanie dla](logging.md) magazynu. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włączanie opcji odzyskiwania

1. Włącz usuwanie [nie softne.](soft-delete-overview.md)
2. Włącz ochronę przed przeczyszczaniem, jeśli chcesz chronić przed wymuszaniem usunięcia tajnego/magazynu nawet po włączeniu usuwania nie soft-delete.