---
title: Konta usług zarządzane przez grupę dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak utworzyć konto usługi zarządzane przez grupę (gMSA) do użycia z domenami zarządzanymi Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619988"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Tworzenie konta usługi zarządzanego przez grupę (gMSA) w Azure Active Directory Domain Services

Aplikacje i usługi często wymagają tożsamości do samodzielnego uwierzytelnienia z innymi zasobami. Na przykład może być konieczne uwierzytelnienie usługi sieci Web za pomocą usługi bazy danych. Jeśli aplikacja lub usługa ma wiele wystąpień, takich jak farma serwerów sieci Web, ręczne tworzenie i Konfigurowanie tożsamości dla tych zasobów jest czasochłonne.

Zamiast tego można utworzyć konto usługi zarządzane przez grupę (gMSA) w domenie zarządzanej Azure Active Directory Domain Services (Azure AD DS). System operacyjny Windows automatycznie zarządza poświadczeniami dla gMSA, co upraszcza zarządzanie dużymi grupami zasobów.

W tym artykule opisano sposób tworzenia gMSA w domenie zarządzanej przy użyciu Azure PowerShell.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest dołączona do domeny zarządzanej AD DS platformy Azure.
    * W razie potrzeby Ukończ samouczek, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Omówienie zarządzanych kont usług

Autonomiczne zarządzane konto usługi (autonomiczne) to konto domeny, którego hasło jest zarządzane automatycznie. Takie podejście upraszcza zarządzanie główną nazwą usługi (SPN) i umożliwia delegowanie zarządzania innym administratorom. Nie musisz ręcznie tworzyć i obracać poświadczeń dla konta.

Konto usługi zarządzane przez grupę (gMSA) zapewnia takie samo Uproszczenie zarządzania, ale dla wielu serwerów w domenie. GMSA umożliwia wszystkim wystąpieniem usługi hostowanej w farmie serwerów używanie tej samej jednostki usługi dla protokołów uwierzytelniania wzajemnego do pracy. Gdy gMSA jest używany jako nazwa główna usługi, system operacyjny Windows ponownie zarządza hasłem konta, zamiast polegać na administratorach.

Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Korzystanie z kont usług w usłudze Azure AD DS

Ponieważ domeny zarządzane są zablokowane i zarządzane przez firmę Microsoft, podczas korzystania z kont usług należy wziąć pod uwagę następujące kwestie:

* Utwórz konta usług w niestandardowych jednostkach organizacyjnych (OU) w domenie zarządzanej.
    * Nie można utworzyć konta usługi w wbudowanych jednostkach *AADDC users* lub *AADDC Computers* .
    * Zamiast tego należy [utworzyć niestandardową jednostkę organizacyjną][create-custom-ou] w domenie zarządzanej, a następnie utworzyć konta usług w tej niestandardowej jednostce organizacyjnej.
* Klucz główny usług dystrybucji kluczy (KDS) jest wstępnie utworzony.
    * Klucz główny KDS służy do generowania i pobierania haseł dla kont gMSA. Na platformie Azure AD DS jest tworzony certyfikat główny KDS.
    * Nie masz uprawnień do utworzenia innego lub wyświetlenie domyślnego klucza głównego KDS.

## <a name="create-a-gmsa"></a>Utwórz gMSA

Najpierw utwórz niestandardową jednostkę organizacyjną przy użyciu polecenia cmdlet [New-ADOrganizationalUnit][New-AdOrganizationalUnit] . Aby uzyskać więcej informacji na temat tworzenia niestandardowych jednostek organizacyjnych i zarządzania nimi, zobacz [niestandardowe jednostki organizacyjne w usłudze Azure AD DS][create-custom-ou].

> [!TIP]
> Aby wykonać te kroki w celu utworzenia gMSA, [Użyj maszyny wirtualnej zarządzania][tutorial-create-management-vm]. Ta maszyna wirtualna zarządzania powinna mieć już wymagane polecenia cmdlet programu PowerShell usługi AD i połączenie z domeną zarządzaną.

Poniższy przykład tworzy niestandardową jednostkę organizacyjną o nazwie *myNewOU* w domenie zarządzanej o nazwie *aaddscontoso.com*. Użyj własnej jednostki organizacyjnej i nazwy domeny zarządzanej:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Teraz Utwórz element gMSA przy użyciu polecenia cmdlet [New-ADServiceAccount][New-ADServiceAccount] . Zdefiniowano następujące przykładowe parametry:

* **-Name** jest ustawiona na *WebFarmSvc*
* **-Path —** parametr określa niestandardową jednostkę organizacyjną dla gMSA utworzonych w poprzednim kroku.
* Wpisy DNS i nazwy główne usług są ustawiane dla *WebFarmSvc.aaddscontoso.com*
* Podmioty zabezpieczeń w *AADDSCONTOSO — serwer $* mogą pobrać hasło i użyć tożsamości.

Określ własne nazwy i nazwy domen.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Teraz można skonfigurować aplikacje i usługi do korzystania z gMSA w razie potrzeby.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kont gMSA, zobacz [wprowadzenie do kont usług zarządzanych przez grupę][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
