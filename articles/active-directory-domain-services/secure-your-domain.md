---
title: Zabezpiecz Azure AD Domain Services | Microsoft Docs
description: Informacje na temat wyłączania słabych szyfrów, starych protokołów i synchronizacji skrótów haseł NTLM dla Azure Active Directory Domain Services domeny zarządzanej.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 581963c94129c36acbd8761d93e369281797fa9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654718"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Wyłącz słabe szyfry i synchronizację skrótów haseł, aby zabezpieczyć domenę zarządzaną Azure AD Domain Services

Domyślnie Azure Active Directory Domain Services (Azure AD DS) umożliwia korzystanie z szyfrów, takich jak NTLM V1 i TLS v1. Te szyfry mogą być wymagane w przypadku niektórych starszych aplikacji, ale są uznawane za słabe i można je wyłączyć, jeśli nie są potrzebne. Jeśli masz lokalną łączność hybrydową przy użyciu Azure AD Connect, możesz również wyłączyć synchronizację skrótów haseł NTLM.

W tym artykule pokazano, jak wyłączyć szyfrowanie NTLM V1 i TLS V1 oraz wyłączyć synchronizację skrótów haseł NTLM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](/powershell/azure/install-az-ps).
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Zainstaluj i skonfiguruj program Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure AD PowerShell i nawiązać połączenie z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] .

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Wyłącz słabe szyfry i synchronizację skrótu hasła NTLM

Aby wyłączyć słabe mechanizmy szyfrowania i synchronizację skrótów poświadczeń NTLM, zaloguj się do konta platformy Azure, a następnie Pobierz zasób Azure AD DS przy użyciu polecenia cmdlet [Get-AzResource][Get-AzResource] :

> [!TIP]
> Jeśli wystąpi błąd przy użyciu polecenia [Get-AzResource][Get-AzResource] , że zasób *Microsoft. AAD/DomainServices* nie istnieje, [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Następnie zdefiniuj *DomainSecuritySettings* , aby skonfigurować następujące opcje zabezpieczeń:

1. Wyłącz obsługę NTLM v1.
2. Wyłącz synchronizację skrótów haseł NTLM z lokalnej usługi AD.
3. Wyłącz protokół TLS v1.

> [!IMPORTANT]
> Użytkownicy i konta usług nie mogą wykonać prostych powiązań LDAP w przypadku wyłączenia synchronizacji skrótów haseł NTLM w domenie zarządzanej AD DS platformy Azure. Jeśli musisz wykonać proste powiązania LDAP, nie ustawiaj opcji *"SyncNtlmPasswords" = "Disabled"* (Konfiguracja zabezpieczeń) w poniższym poleceniu.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Na koniec zastosuj zdefiniowane ustawienia zabezpieczeń do domeny zarządzanej AD DS platformy Azure przy użyciu polecenia cmdlet [Set-AzResource][Set-AzResource] . Określ zasób AD DS platformy Azure z pierwszego kroku oraz ustawienia zabezpieczeń z poprzedniego kroku.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Zastosowanie ustawień zabezpieczeń do domeny zarządzanej platformy Azure AD DS może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [jak obiekty i poświadczenia są synchronizowane w domenie zarządzanej AD DS platformy Azure][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD