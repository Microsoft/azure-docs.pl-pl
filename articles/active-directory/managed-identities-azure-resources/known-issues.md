---
title: Często zadawane pytania i znane problemy związane z tożsamościami zarządzanymi — Azure AD
description: Znane problemy związane z tożsamościami zarządzanymi dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79266535"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Często zadawane pytania i znane problemy związane z tożsamościami zarządzanymi dla zasobów platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Czy zarządzane tożsamości dla zasobów platformy Azure współpracują z usługą Azure Cloud Services?

Nie. nie ma żadnych planów do obsługi tożsamości zarządzanych dla zasobów platformy Azure w usłudze Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Czy zarządzane tożsamości dla zasobów platformy Azure współpracują z Active Directory Authentication Library (ADAL) czy z biblioteką uwierzytelniania firmy Microsoft (MSAL)?

Nie, zarządzane tożsamości dla zasobów platformy Azure nie są jeszcze zintegrowane z biblioteką ADAL lub MSAL. Aby uzyskać szczegółowe informacje na temat uzyskiwania tokenu dla tożsamości zarządzanych dla zasobów platformy Azure przy użyciu punktu końcowego REST, zobacz [jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaka jest granica zabezpieczeń zarządzanych tożsamości dla zasobów platformy Azure?

Granica zabezpieczeń tożsamości jest zasobem, do którego jest dołączony. Na przykład granica zabezpieczeń maszyny wirtualnej z zarządzanymi tożsamościami dla zasobów platformy Azure to maszyna wirtualna. Każdy kod uruchomiony na tej maszynie wirtualnej może wywoływać zarządzane tożsamości dla punktu końcowego zasobów platformy Azure i tokenów żądań. Jest to podobne środowisko z innymi zasobami, które obsługują zarządzane tożsamości dla zasobów platformy Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jaką tożsamość będzie IMDS domyślnie, jeśli nie określisz tożsamości w żądaniu?

- Jeśli jest włączona tożsamość zarządzana przypisana przez system i żadna tożsamość nie została określona w żądaniu, IMDS domyślnie zostanie skojarzona z tożsamością zarządzaną przez system.
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona i istnieje tylko jedna tożsamość zarządzana przypisana przez użytkownika, IMDS zostanie domyślnie przypisana tożsamość zarządzana pojedynczego użytkownika. 
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona, a istnieją wiele tożsamości zarządzanych przypisanych przez użytkownika, a następnie wymagane jest określenie tożsamości zarządzanej w żądaniu.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Czy należy używać zarządzanych tożsamości dla punktu końcowego IMDS zasobów platformy Azure lub punktu końcowego rozszerzenia maszyny wirtualnej?

W przypadku korzystania z tożsamości zarządzanych dla zasobów platformy Azure z maszynami wirtualnymi zalecamy użycie punktu końcowego IMDS. Instance Metadata Service platformy Azure to punkt końcowy REST dostępny dla wszystkich maszyn wirtualnych IaaS utworzonych za pośrednictwem Azure Resource Manager. 

Niektóre korzyści wynikające z używania tożsamości zarządzanych dla zasobów platformy Azure za pośrednictwem IMDS są następujące:
- Wszystkie systemy operacyjne obsługiwane przez usługę Azure IaaS mogą używać tożsamości zarządzanych dla zasobów platformy Azure za pośrednictwem usługi IMDS.
- Nie trzeba już instalować rozszerzenia na maszynie wirtualnej, aby umożliwić zarządzanie tożsamościami dla zasobów platformy Azure. 
- Certyfikaty używane przez zarządzane tożsamości dla zasobów platformy Azure nie są już obecne na maszynie wirtualnej.
- Punkt końcowy IMDS jest dobrze znanym adresem IP bez obsługi routingu, który jest dostępny tylko w ramach maszyny wirtualnej.
- 1000 tożsamości zarządzane przypisane przez użytkownika można przypisać do pojedynczej maszyny wirtualnej. 

Zarządzane tożsamości dla rozszerzenia maszyny wirtualnej zasobów platformy Azure są nadal dostępne; nie opracowujemy jednak już nowych funkcji. Zalecamy przełączenie do korzystania z punktu końcowego IMDS. 

Niektóre ograniczenia związane z korzystaniem z punktu końcowego rozszerzenia maszyny wirtualnej są następujące:
- Ograniczona obsługa dystrybucji systemu Linux: CoreOS stabilna, CentOS 7,1, Red Hat 7,2, Ubuntu 15,04, Ubuntu 16,04
- Do maszyny wirtualnej można przypisać tylko 32 tożsamości zarządzane przypisane przez użytkownika.


Uwaga: w styczniu 2019 nie będzie można obsługiwać zarządzanych tożsamości dla rozszerzenia maszyny wirtualnej zasobów platformy Azure. 

Aby uzyskać więcej informacji na temat usługi Azure Instance Metadata Service, zobacz [dokumentację IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Czy tożsamość zarządzana zostanie automatycznie utworzona, Jeśli przeniesiesz subskrypcję do innego katalogu?

Nie. Jeśli przeniesiesz subskrypcję do innego katalogu, musisz ręcznie je ponownie utworzyć i ponownie udzielić przypisań ról RBAC platformy Azure.
- Dla tożsamości zarządzanych przypisanych przez system: Wyłącz i ponownie włącz. 
- W przypadku tożsamości zarządzanych przypisanych przez użytkownika: Usuń, ponownie utwórz i Dołącz je do niezbędnych zasobów (np. maszyn wirtualnych).

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Czy mogę użyć tożsamości zarządzanej w celu uzyskania dostępu do zasobu w innym katalogu/dzierżawie?

Nie. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jakie uprawnienia RBAC platformy Azure są wymagane do zarządzania tożsamościami w zasobie? 

- Tożsamość zarządzana przypisana przez system: wymagane są uprawnienia do zapisu dla zasobu. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Ta akcja jest uwzględniona w rolach wbudowanych specyficznych dla zasobów, takich jak [współautor maszyny wirtualnej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Tożsamość zarządzana przypisana przez użytkownika: wymagane są uprawnienia do zapisu dla zasobu. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Oprócz przypisywania roli [operatora tożsamości zarządzanej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) przez zarządzaną tożsamość.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Jak ponownie uruchomić zarządzane tożsamości dla rozszerzenia zasobów platformy Azure?
W systemie Windows i niektórych wersjach systemu Linux, jeśli rozszerzenie zostanie zatrzymane, można użyć następującego polecenia cmdlet, aby ręcznie uruchomić ponownie:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Nazwa rozszerzenia i typ dla systemu Windows to: ManagedIdentityExtensionForWindows
- Nazwa rozszerzenia i typ dla systemu Linux to: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Znane problemy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksportowania schematu dla tożsamości zarządzanych dla rozszerzenia zasobów platformy Azure

Gdy zarządzane tożsamości dla zasobów platformy Azure są włączone na maszynie wirtualnej, podczas próby użycia funkcji "skrypt automatyzacji" dla maszyny wirtualnej lub jej grupy zasobów jest wyświetlany następujący błąd:

![Tożsamość zarządzana dla błędu eksportowania skryptu automatyzacji zasobów platformy Azure](./media/msi-known-issues/automation-script-export-error.png)

Zarządzane tożsamości dla rozszerzenia maszyny wirtualnej zasobów platformy Azure (planowane do wycofania w styczniu 2019) nie obsługują obecnie możliwości eksportowania jego schematu do szablonu grupy zasobów. W związku z tym wygenerowany szablon nie pokazuje parametrów konfiguracji, aby umożliwić zarządzanie tożsamościami dla zasobów platformy Azure w ramach zasobu. Te sekcje można dodać ręcznie, postępując zgodnie z przykładami w temacie [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).

Po udostępnieniu funkcji eksportu schematu dla tożsamości zarządzanych dla rozszerzenia maszyny wirtualnej Azure Resources (planowanej do wycofania w styczniu 2019) zostanie ono wyświetlone w obszarze [Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Nie można uruchomić maszyny wirtualnej po przeniesieniu jej z grupy zasobów lub subskrypcji

Jeśli przeniesiesz maszynę wirtualną w stan uruchomienia, będzie ona nadal działać podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie, nie zostanie uruchomiona. Ten problem występuje, ponieważ maszyna wirtualna nie aktualizuje odwołania do zarządzanych tożsamości dla tożsamości zasobów platformy Azure i kontynuuje wskazywanie jej w starej grupie zasobów.

**Poprawkę** 
 
Wyzwól aktualizację na maszynie wirtualnej, aby możliwe było uzyskanie prawidłowych wartości dla zarządzanych tożsamości dla zasobów platformy Azure. Aby zaktualizować odwołanie do zarządzanych tożsamości dla tożsamości zasobów platformy Azure, można zmienić wartość właściwości maszyny wirtualnej. Na przykład można ustawić nową wartość tagu na maszynie wirtualnej przy użyciu następującego polecenia:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy tag "fixVM" o wartości 1 na maszynie wirtualnej. 
 
Ustawiając tę właściwość, maszyna wirtualna ma aktualizacje z poprawnymi tożsamościami zarządzanymi dla identyfikatora URI zasobu zasobów platformy Azure, a następnie powinno być możliwe uruchomienie maszyny wirtualnej. 
 
Po uruchomieniu maszyny wirtualnej tag można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Inicjowanie obsługi rozszerzenia maszyny wirtualnej nie powiodło się

Inicjowanie obsługi rozszerzenia maszyny wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. Uruchom ponownie maszynę wirtualną i spróbuj ponownie.
 
> [!NOTE]
> Rozszerzenie maszyny wirtualnej jest planowane do wycofania do stycznia 2019. Zalecamy przechodzenie do korzystania z punktu końcowego IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Przenoszenie subskrypcji między katalogami usługi Azure AD

Tożsamości zarządzane nie są aktualizowane, gdy subskrypcja jest przenoszona/przenoszona do innego katalogu. W związku z tym wszystkie istniejące tożsamości zarządzane przypisane do systemu lub przypisane przez użytkownika zostaną przerwane. 

Obejście dla zarządzanych tożsamości w ramach subskrypcji, która została przeniesiona do innego katalogu:

 - Dla tożsamości zarządzanych przypisanych przez system: Wyłącz i ponownie włącz. 
 - W przypadku tożsamości zarządzanych przypisanych przez użytkownika: Usuń, ponownie utwórz i Dołącz je do niezbędnych zasobów (np. maszyn wirtualnych).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Przeniesienie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów/subskrypcji

Przeniesienie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów spowoduje, że tożsamość zostanie przerwana. W związku z tym zasoby (np. VM) używające tej tożsamości nie będą mogły żądać tokenów dla niego. 
