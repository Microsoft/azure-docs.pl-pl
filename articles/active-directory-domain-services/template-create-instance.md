---
title: Włączanie usług Azure DS Domain Services przy użyciu szablonu | Microsoft Docs
description: Dowiedz się, jak skonfigurować i włączyć Azure Active Directory Domain Services przy użyciu szablonu Azure Resource Manager
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: f257a186f05dc94923d1d39829b5ed68b518f20c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967634"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Tworzenie Azure Active Directory Domain Services domeny zarządzanej przy użyciu szablonu Azure Resource Manager

Azure Active Directory Domain Services (AD DS platformy Azure) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP, uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory. Te usługi domenowe są używane bez konieczności samodzielnego wdrażania kontrolerów domeny i zarządzania nimi. Platforma Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta Integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych, a także umożliwia korzystanie z istniejących grup i kont użytkowników w celu zabezpieczenia dostępu do zasobów.

W tym artykule przedstawiono sposób tworzenia domeny zarządzanej przy użyciu szablonu Azure Resource Manager. Obsługa zasobów jest tworzona przy użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebne są następujące zasoby:

* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](/powershell/azure/install-az-ps).
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Zainstaluj i skonfiguruj program Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure AD PowerShell i nawiązać połączenie z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] .
* Aby włączyć usługę Azure AD DS, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usługi Azure AD DS, musisz mieć uprawnienia *współautora* w ramach subskrypcji platformy Azure.

## <a name="dns-naming-requirements"></a>Wymagania dotyczące nazewnictwa DNS

Podczas tworzenia domeny zarządzanej AD DS platformy Azure należy określić nazwę DNS. Po wybraniu tej nazwy DNS należy wziąć pod uwagę następujące kwestie:

* **Nazwa wbudowanej domeny:** Domyślnie używana jest wbudowana nazwa domeny katalogu (sufiks *. onmicrosoft.com* ). Jeśli chcesz włączyć bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu, nie możesz utworzyć certyfikatu cyfrowego, aby zabezpieczyć połączenie z tą domeną domyślną. Firma Microsoft jest właścicielem domeny *. onmicrosoft.com* , więc urząd certyfikacji (CA) nie wystawia certyfikatu.
* **Niestandardowe nazwy domen:** Najbardziej typowym podejściem jest określenie niestandardowej nazwy domeny, która zwykle jest już posiadana i ma Routing. W przypadku używania routingu, domeny niestandardowej, ruch może prawidłowo przepływać w miarę potrzeb do obsługi aplikacji.
* **Sufiksy domen bez routingu:** Zwykle zalecamy uniknięcie sufiksu nazwy domeny bez obsługi routingu, takiego jak *contoso. Local*. Sufiks *. Local* nie jest w stanie routingu i może powodować problemy z ROZPOZNAWANIEM nazw DNS.

> [!TIP]
> Jeśli utworzysz niestandardową nazwę domeny, weź pod uwagę istniejące przestrzenie nazw DNS. Zalecane jest użycie nazwy domeny innej niż istniejąca lub lokalna przestrzeń nazw DNS.
>
> Na przykład jeśli masz istniejącą przestrzeń nazw DNS *contoso.com*, Utwórz domenę zarządzaną z niestandardową nazwą domeny *aaddscontoso.com*. Jeśli musisz użyć bezpiecznego protokołu LDAP, musisz zarejestrować i utworzyć niestandardową nazwę domeny w celu wygenerowania wymaganych certyfikatów.
>
> Może być konieczne utworzenie dodatkowych rekordów DNS dla innych usług w środowisku lub warunkowych usług przesyłania dalej DNS między istniejącymi spacjami nazw DNS w danym środowisku. Jeśli na przykład uruchomisz serwer sieci Web, który hostuje lokację przy użyciu nazwy głównej DNS, mogą wystąpić konflikty nazw, które wymagają dodatkowych wpisów DNS.
>
> W tym przykładzie oraz z artykułami z artykułu, niestandardowa domena *aaddscontoso.com* jest używana jako krótki przykład. We wszystkich poleceniach należy określić własną nazwę domeny.

Obowiązują również następujące ograniczenia nazw DNS:

* **Ograniczenia prefiksu domeny:** Nie można utworzyć domeny zarządzanej z prefiksem dłuższym niż 15 znaków. Prefiks określonej nazwy domeny (na przykład *aaddscontoso* w nazwie domeny *aaddscontoso.com* ) musi zawierać co najwyżej 15 znaków.
* **Konflikty nazw sieciowych:** Nazwa domeny DNS dla domeny zarządzanej nie powinna już istnieć w sieci wirtualnej. W celu sprawdzenia następujących scenariuszy, które mogłyby prowadzić do konfliktu nazw:
    * Jeśli masz już domenę Active Directory z tą samą nazwą domeny DNS w sieci wirtualnej platformy Azure.
    * Jeśli sieć wirtualna, w której planujesz włączyć domenę zarządzaną, ma połączenie sieci VPN z siecią lokalną. W tym scenariuszu upewnij się, że nie masz domeny z tą samą nazwą domeny DNS w sieci lokalnej.
    * Jeśli masz istniejącą usługę w chmurze platformy Azure o tej nazwie w sieci wirtualnej platformy Azure.

## <a name="create-required-azure-ad-resources"></a>Tworzenie wymaganych zasobów usługi Azure AD

Usługa Azure AD DS wymaga nazwy głównej usługi i grupy usługi Azure AD. Te zasoby umożliwiają synchronizowanie danych przez domenę zarządzaną i definiowanie użytkowników, którzy mają uprawnienia administracyjne w domenie zarządzanej.

Najpierw Zarejestruj dostawcę zasobów Azure AD Domain Services za pomocą polecenia cmdlet [register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Utwórz nazwę główną usługi Azure AD za pomocą polecenia cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] dla platformy AD DS Azure, aby komunikować się i uwierzytelniać samodzielnie. Określony identyfikator aplikacji jest używany o nazwie *usługi kontrolera domeny* o identyfikatorze *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Nie zmieniaj tego identyfikatora aplikacji.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Teraz Utwórz grupę usługi Azure AD o nazwie *administratorzy platformy AAD DC* przy użyciu polecenia cmdlet [New-AzureADGroup][New-AzureADGroup] . Użytkownicy dodani do tej grupy uzyskują uprawnienia do wykonywania zadań administracyjnych w domenie zarządzanej.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Po utworzeniu grupy *administratorów kontrolera domeny usługi AAD* Dodaj użytkownika do grupy przy użyciu polecenia cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember] . Najpierw uzyskasz identyfikator obiektu grupy *administratorów kontrolera domeny usługi AAD* za pomocą polecenia cmdlet [Get-AzureADGroup][Get-AzureADGroup] , a następnie identyfikator obiektu żądanego użytkownika przy użyciu polecenia cmdlet [Get-AzureADUser][Get-AzureADUser] .

W poniższym przykładzie identyfikator obiektu użytkownika dla konta z nazwą UPN `admin@contoso.onmicrosoft.com` . Zastąp to konto użytkownika nazwą UPN użytkownika, który chcesz dodać do grupy *administratorów kontrolera domeny usługi AAD* :

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Na koniec Utwórz grupę zasobów za pomocą polecenia cmdlet [New-AzResourceGroup][New-AzResourceGroup] . W poniższym przykładzie grupa zasobów ma nazwę moja *zasobów* i jest tworzona w regionie *zachodnim* . Użyj własnej nazwy i żądanego regionu:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

W przypadku wybrania regionu, który obsługuje Strefy dostępności, zasoby AD DS platformy Azure są dystrybuowane między strefami w celu zapewnienia dodatkowej nadmiarowości. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach.

Nie ma niczego do skonfigurowania na potrzeby dystrybuowania AD DS platformy Azure między strefami. Platforma Azure automatycznie obsługuje dystrybucję zasobów. Aby uzyskać więcej informacji i sprawdzić dostępność regionów, zobacz [co to są strefy dostępności na platformie Azure?][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Definicja zasobu dla AD DS platformy Azure

W ramach definicji zasobu Menedżer zasobów wymagane są następujące parametry konfiguracji:

| Parametr               | Wartość |
|-------------------------|---------|
| domainName              | Nazwa domeny DNS dla domeny zarządzanej, biorąc pod uwagę poprzednie kwestie dotyczące prefiksów nazw i konfliktów. |
| filteredSync            | Usługa Azure AD DS pozwala synchronizować *wszystkich* użytkowników i grupy dostępne w usłudze Azure AD albo synchronizację z *zakresem* tylko określonych grup.<br /><br /> Aby uzyskać więcej informacji na temat synchronizacji z zakresem, zobacz [Azure AD Domain Services synchronizacji w zakresie][scoped-sync].|
| notificationSettings    | W przypadku wygenerowania alertów w domenie zarządzanej powiadomienia e-mail mogą być wysyłane. <br /><br />*Administratorzy globalni* dzierżawy platformy Azure i członkowie grupy *Administratorzy usługi AAD* mogą *włączyć* te powiadomienia.<br /><br /> W razie potrzeby można dodać kolejnych adresatów dla powiadomień, gdy istnieją alerty wymagające uwagi.|
| domainConfigurationType | Domyślnie domena zarządzana jest tworzona jako Las *użytkownika* . Ten typ lasu służy do synchronizowania wszystkich obiektów z usługi Azure AD, w tym wszystkich kont użytkowników utworzonych w środowisku lokalnym AD DS. Nie musisz określać wartości *atrybutem domainconfiguration* , aby utworzyć Las użytkownika.<br /><br /> Las *zasobów* synchronizuje tylko użytkowników i grupy utworzone bezpośrednio w usłudze Azure AD. Ustaw wartość *ResourceTrusting* , aby utworzyć Las zasobów.<br /><br />Aby uzyskać więcej informacji o lasach *zasobów* , w tym o tym, dlaczego można korzystać z jednej z nich i jak utworzyć relacje zaufania lasów z lokalnymi domenami AD DS, zobacz [Omówienie lasów zasobów platformy Azure AD DS][resource-forests].|

Następująca skrócona Definicja parametrów pokazuje, jak te wartości są zadeklarowane. Las użytkownika o nazwie *aaddscontoso.com* jest tworzony dla wszystkich użytkowników z usługi Azure AD zsynchronizowanych z domeną zarządzaną:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Następujący skrócony typ zasobu szablonu Menedżer zasobów jest następnie używany do definiowania i tworzenia domeny zarządzanej. Sieć wirtualna platformy Azure i podsieć muszą już istnieć lub być utworzone jako część szablonu Menedżer zasobów. Domena zarządzana jest połączona z tą podsiecią.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Te parametry i typ zasobu mogą służyć jako część szerszego szablonu Menedżer zasobów, aby wdrożyć domenę zarządzaną, jak pokazano w poniższej sekcji.

## <a name="create-a-managed-domain-using-sample-template"></a>Tworzenie domeny zarządzanej przy użyciu przykładowego szablonu

Poniższy kompletny szablon Menedżer zasobów służy do tworzenia domeny zarządzanej oraz reguł obsługi sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. Reguły sieciowej grupy zabezpieczeń są wymagane do zabezpieczenia domeny zarządzanej i upewnienia się, że ruch może prawidłowo przepływać. Zostanie utworzony Las użytkownika z nazwą DNS *aaddscontoso.com* , ze wszystkimi użytkownikami synchronizowanymi z usługi Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Ten szablon można wdrożyć przy użyciu preferowanej metody wdrażania, takiej jak [Azure Portal][portal-deploy], [Azure PowerShell][powershell-deploy]lub potok ciągłej integracji/ciągłego dostarczania. Poniższy przykład używa polecenia cmdlet [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] . Określ nazwę własnej grupy zasobów i nazwa pliku szablonu:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Utworzenie zasobu i zwrócenie kontroli do wiersza polecenia programu PowerShell trwa kilka minut. Domena zarządzana jest w dalszym ciągu obsługiwana w tle, a ukończenie wdrożenia może potrwać do godziny. W Azure Portal strona **Przegląd** dla domeny zarządzanej pokazuje bieżący stan w tym etapie wdrażania.

Gdy Azure Portal pokazuje, że domena zarządzana zakończyła Inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania.
    * Aby skonfigurować serwer DNS, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* [Włącz synchronizację haseł w usłudze Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić domenę zarządzaną w działaniu, można [przyłączyć do domeny maszynę wirtualną z systemem Windows][windows-join], [skonfigurować bezpieczny protokół LDAP][tutorial-ldaps]i [skonfigurować synchronizację skrótów haseł][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment