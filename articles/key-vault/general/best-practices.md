---
title: Najlepsze rozwiązania dotyczące używania Key Vault — Azure Key Vault | Microsoft Docs
description: Dowiedz się więcej na temat najlepszych rozwiązań dotyczących Azure Key Vault, w tym kontrolowania dostępu, używania oddzielnych magazynów kluczy, opcji kopii zapasowych, rejestrowania i odzyskiwania.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 5e4ec2749cec8444382e2b6e7c856e74c57ebb59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777171"
---
# <a name="best-practices-to-use-key-vault"></a>Najlepsze rozwiązania dotyczące korzystania z Key Vault

## <a name="use-separate-key-vaults"></a>Używanie oddzielnych magazynów kluczy

Naszym zaleceniem jest użycie magazynu na aplikację na środowisko (deweloperskie, przedprodukcyjne i produkcyjne). Pomaga to nie udostępniać wpisów tajnych w różnych środowiskach, a także zmniejsza zagrożenie w przypadku naruszenia zabezpieczeń.

## <a name="control-access-to-your-vault"></a>Kontrola dostępu do magazynu

Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla działalności firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowanym aplikacjom i użytkownikom. Ten [artykuł](security-overview.md) zawiera omówienie modelu Key Vault dostępu. Wyjaśniono w nim uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Sugestie podczas kontrolowania dostępu do magazynu są następujące:
1. Blokowanie dostępu do subskrypcji, grupy zasobów i magazynów kluczy (RBAC) platformy Azure
2. Tworzenie zasad dostępu dla każdego magazynu
3. Udzielanie dostępu przy użyciu jednostki dostępu z najmniejszymi uprawnieniami
4. Włączanie zapory i [punktów końcowych usługi sieci wirtualnej](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Backup

Upewnij się, że regularnie tworzysz kopię zapasową magazynu podczas aktualizowania/usuwania/tworzenia obiektów w magazynie.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell kopii zapasowej

* [Certyfikat kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Klucz kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Klucz tajny kopii zapasowej](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Polecenia kopii zapasowej interfejsu wiersza polecenia platformy Azure

* [Certyfikat kopii zapasowej](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Klucz kopii zapasowej](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Klucz tajny kopii zapasowej](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Włączanie rejestrowania

[Włącz rejestrowanie dla](logging.md) magazynu. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włączanie opcji odzyskiwania

1. Włącz usuwanie [niealkońcowe.](soft-delete-overview.md)
2. Włącz ochronę przed przeczyszczaniem, jeśli chcesz chronić przed wymuszaniem usunięcia tajnego/magazynu nawet po włączeniu usuwania nie soft-delete.