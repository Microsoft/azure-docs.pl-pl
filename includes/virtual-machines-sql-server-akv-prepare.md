---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95564237"
---
## <a name="prepare-for-akv-integration"></a>Przygotowanie do integracji z programem AKV
Aby skonfigurować SQL Serverą maszynę wirtualną za pomocą integracji Azure Key Vault, istnieje kilka wymagań wstępnych: 

1. [Instalowanie programu Azure PowerShell](#install)
2. [Tworzenie Azure Active Directory](#register)
3. [Tworzenie magazynu kluczy](#createkeyvault)

W poniższych sekcjach opisano te wymagania wstępne i informacje, które należy zebrać w celu późniejszego uruchomienia poleceń cmdlet programu PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Zainstaluj Azure PowerShell
Upewnij się, że zainstalowano najnowszy moduł Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Zarejestruj aplikację w Azure Active Directory

Najpierw musisz mieć [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) w Twojej subskrypcji. Dzięki temu można udzielić uprawnienia do magazynu kluczy dla określonych użytkowników i aplikacji.

Następnie zarejestruj aplikację przy użyciu usługi AAD. Spowoduje to nadanie kontu nazwy głównej usługi dostępu do magazynu kluczy, którego będzie potrzebowała Twoja maszyna wirtualna. W Azure Key Vault artykule można znaleźć następujące kroki w sekcji [Rejestrowanie aplikacji za pomocą Azure Active Directory](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) lub można zobaczyć kroki z zrzutami ekranu w **sekcji Pobieranie tożsamości dla aplikacji** w [tym wpisie w blogu](/archive/blogs/kv/azure-key-vault-step-by-step). Przed wykonaniem tych kroków należy zebrać następujące informacje podczas tej rejestracji, które są potrzebne później po włączeniu integracji Azure Key Vault na maszynie wirtualnej SQL.

* Po dodaniu aplikacji Znajdź **Identyfikator aplikacji** (znany również jako AAD ClientID lub AppID) w bloku **zarejestrowanej aplikacji** .
    Identyfikator aplikacji jest przypisywany później do parametru **$spName** (główna nazwa usługi) w skrypcie programu PowerShell w celu włączenia Azure Key Vault integracji.

   ![Identyfikator aplikacji](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Podczas wykonywania tych czynności podczas tworzenia klucza skopiuj klucz tajny dla klucza, jak pokazano na poniższym zrzucie ekranu. Ten klucz tajny jest przypisywany później do parametru **$spSecret** (klucz tajny usługi) w skrypcie programu PowerShell.

   ![Wpis tajny usługi AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Identyfikator aplikacji i klucz tajny będą również używane do tworzenia poświadczeń w SQL Server.

* Musisz autoryzować ten nowy identyfikator aplikacji (lub identyfikator klienta), aby mieć następujące uprawnienia dostępu: **Get**, **wrapKey**, **unwrapKey**. Jest to realizowane za pomocą polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) . Aby uzyskać więcej informacji, zobacz [Azure Key Vault przegląd](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Tworzenie magazynu kluczy
Aby można było używać Azure Key Vault do przechowywania kluczy używanych do szyfrowania na maszynie wirtualnej, wymagany jest dostęp do magazynu kluczy. Jeśli magazyn kluczy nie został jeszcze skonfigurowany, utwórz go, wykonując kroki opisane w [wprowadzenie z Azure Key Vault](../articles/key-vault/general/overview.md) artykule. Przed wykonaniem tych kroków należy zebrać pewne informacje, które są potrzebne w dalszej części tego ustawienia, gdy włączysz Azure Key Vault integrację na maszynie wirtualnej SQL.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Po przepoczęciu pracy z etapem tworzenia magazynu kluczy Zwróć uwagę na zwróconą Właściwość **vaultUri** , która jest adresem URL magazynu kluczy. W przykładzie podanym w tym kroku pokazano poniżej, nazwa magazynu kluczy to ContosoKeyVault, w związku z czym adres URL magazynu kluczy https://contosokeyvault.vault.azure.net/ .

Adres URL magazynu kluczy zostanie przypisany później do parametru **$akvURL** w skrypcie programu PowerShell w celu włączenia Azure Key Vault integracji.

Po utworzeniu magazynu kluczy musimy dodać klucz do magazynu kluczy. ten klucz będzie określany podczas tworzenia klucza asymetrycznego Utwórz w SQL Server później.