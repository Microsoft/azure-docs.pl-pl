---
title: Zaprzestanie korzystania z rozszerzenia maszyny wirtualnej do zarządzania tożsamościami — Azure AD
description: Instrukcje krok po kroku, aby zatrzymać korzystanie z rozszerzenia maszyny wirtualnej i zacząć korzystać z usługi Azure Instance Metadata Service (IMDS) do uwierzytelniania.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: barclayn
ROBOTS: NOINDEX
ms.openlocfilehash: dca5f9ed2911ae3042fb9871f849212ec18b1b58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539387"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Jak zatrzymać korzystanie z rozszerzenia tożsamości zarządzanej maszyny wirtualnej i zacząć korzystać z usługi Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Rozszerzenie maszyny wirtualnej dla tożsamości zarządzanych

Rozszerzenie maszyny wirtualnej dla tożsamości zarządzanych służy do żądania tokenów dla tożsamości zarządzanej w ramach maszyny wirtualnej. Przepływ pracy składa się z następujących kroków:

1. Najpierw obciążenie w ramach zasobu wywołuje lokalny punkt końcowy, `http://localhost/oauth2/token` Aby zażądać tokenu dostępu.
2. Rozszerzenie maszyny wirtualnej używa poświadczeń dla tożsamości zarządzanej, aby zażądać tokenu dostępu z usługi Azure AD. 
3. Token dostępu jest zwracany do obiektu wywołującego i może służyć do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, takie jak Azure Key Vault lub Azure Storage.

Ze względu na kilka ograniczeń przedstawionych w następnej sekcji rozszerzenie maszyny wirtualnej do zarządzania tożsamościami zostało zaniechane na korzyść używania równoważnego punktu końcowego w usłudze Azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Inicjowanie obsługi rozszerzenia 

Podczas konfigurowania maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych w celu posiadania tożsamości zarządzanej można opcjonalnie zainicjować obsługę administracyjną tożsamości dla rozszerzenia maszyny wirtualnej zasobów platformy Azure za pomocą `-Type` parametru polecenia cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) . Można przekazać albo `ManagedIdentityExtensionForWindows` , w `ManagedIdentityExtensionForLinux` zależności od typu maszyny wirtualnej, i nazwać ją przy użyciu `-Name` parametru. `-Settings`Parametr określa port używany przez punkt końcowy tokenu OAuth do pozyskiwania tokenu:

```azurepowershell-interactive
$settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Można również użyć szablonu wdrażania Azure Resource Manager, aby zainicjować rozszerzenie maszyny wirtualnej, dodając następujący kod JSON do `resources` sekcji szablonu (Użyj `ManagedIdentityExtensionForLinux` dla elementów nazwa i typ dla wersji systemu Linux).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
Jeśli pracujesz z zestawami skalowania maszyn wirtualnych, możesz również udostępnić zarządzane tożsamości dla rozszerzenia zestawu skalowania maszyn wirtualnych zasobów platformy Azure za pomocą polecenia cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) . Można przekazać albo `ManagedIdentityExtensionForWindows` , w `ManagedIdentityExtensionForLinux` zależności od typu zestawu skalowania maszyn wirtualnych, i nazwać go przy użyciu `-Name` parametru. `-Settings`Parametr określa port używany przez punkt końcowy tokenu OAuth do pozyskiwania tokenu:

   ```azurepowershell-interactive
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Aby zainicjować obsługę rozszerzenia zestawu skalowania maszyn wirtualnych przy użyciu szablonu wdrażania Azure Resource Manager, Dodaj następujący kod JSON do `extensionpProfile` sekcji szablonu (Użyj `ManagedIdentityExtensionForLinux` dla elementów nazwa i typ dla wersji systemu Linux).

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

Inicjowanie obsługi rozszerzenia maszyny wirtualnej może zakończyć się niepowodzeniem z powodu błędów wyszukiwania DNS. Jeśli tak się stanie, uruchom ponownie maszynę wirtualną, a następnie spróbuj ponownie. 

### <a name="remove-the-extension"></a>Usuwanie rozszerzenia 
Aby usunąć rozszerzenie, użyj polecenia `-n ManagedIdentityExtensionForWindows` lub `-n ManagedIdentityExtensionForLinux` przełączaj (w zależności od typu maszyny wirtualnej) za pomocą polecenia [AZ VM Extension Delete](/cli/azure/vm/)lub [AZ VMSS Extension Delete](/cli/azure/vmss) dla zestawów skalowania maszyn wirtualnych przy użyciu platformy Azure lub `Remove-AzVMExtension` dla programu PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```azurepowershell-interactive
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Pozyskiwanie tokenu przy użyciu rozszerzenia maszyny wirtualnej

Poniżej przedstawiono przykładowe żądanie przy użyciu tożsamości zarządzanych dla punktu końcowego rozszerzenia maszyny wirtualnej dla zasobów platformy Azure:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Opis |
| ------- | ----------- |
| `GET` | Czasownik HTTP wskazujący, że chcesz pobrać dane z punktu końcowego. W tym przypadku token dostępu OAuth. | 
| `http://localhost:50342/oauth2/token` | Zarządzane tożsamości dla punktu końcowego zasobów platformy Azure, gdzie 50342 jest portem domyślnym i można skonfigurować. |
| `resource` | Parametr ciągu zapytania, wskazujący identyfikator URI aplikacji dla zasobu docelowego. Pojawia się również w `aud` porozumieniu (odbiorcy) wystawionego tokenu. Ten przykład żąda tokenu w celu uzyskania dostępu do Azure Resource Manager, który ma identyfikator URI aplikacji `https://management.azure.com/` . |
| `Metadata` | Pole nagłówka żądania HTTP wymagane przez zarządzane tożsamości dla zasobów platformy Azure jako środki zaradcze związane z atakiem z fałszerstwem żądania po stronie serwera (SSRF). Ta wartość musi być ustawiona na wartość "true" w przypadku małych liter.|
| `object_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący object_id tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika.|
| `client_id` | Obowiązkowe Parametr ciągu zapytania, wskazujący client_id tożsamości zarządzanej, dla której ma być token. Wymagane, jeśli maszyna wirtualna ma wiele zarządzanych tożsamości przypisanych przez użytkownika.|


Przykładowa odpowiedź:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Opis |
| ------- | ----------- |
| `access_token` | Żądany token dostępu. Podczas wywoływania bezpiecznego interfejsu API REST token jest osadzony w `Authorization` polu nagłówka żądania jako token "Bearer", dzięki czemu interfejs API może uwierzytelniać obiekt wywołujący. | 
| `refresh_token` | Nieużywane przez zarządzane tożsamości dla zasobów platformy Azure. |
| `expires_in` | Liczba sekund, przez jaką token dostępu pozostaje prawidłowy, przed wygaśnięciem, od momentu wystawienia. Czas wystawienia można znaleźć w ramach `iat` żądania tokenu. |
| `expires_on` | Wartość TimeSpan w przypadku wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0: 0Z UTC" (odpowiada na `exp` żądania tokenu). |
| `not_before` | Przedział czasu, gdy token dostępu zaczyna obowiązywać i można go zaakceptować. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0: 0Z UTC" (odpowiada na `nbf` żądania tokenu). |
| `resource` | Zasób, dla którego zażądano tokenu dostępu, który jest zgodny z `resource` parametrem ciągu zapytania żądania. |
| `token_type` | Typ tokenu, który jest tokenem dostępu "Bearer", co oznacza, że zasób może zapewnić dostęp do okaziciela tego tokenu. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Uruchom ponownie rozszerzenie maszyny wirtualnej po awarii

W systemie Windows i niektórych wersjach systemu Linux, jeśli rozszerzenie zostanie zatrzymane, można użyć następującego polecenia cmdlet, aby ręcznie uruchomić ponownie:

```azurepowershell-interactive
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Gdzie: 
- Nazwa rozszerzenia i typ dla systemu Windows to: `ManagedIdentityExtensionForWindows`
- Nazwa rozszerzenia i typ dla systemu Linux to: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksportowania schematu dla tożsamości zarządzanych dla rozszerzenia zasobów platformy Azure

Gdy zarządzane tożsamości dla zasobów platformy Azure są włączone na maszynie wirtualnej, podczas próby użycia funkcji "skrypt automatyzacji" dla maszyny wirtualnej lub jej grupy zasobów jest wyświetlany następujący błąd:

![Tożsamość zarządzana dla błędu eksportowania skryptu automatyzacji zasobów platformy Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Zarządzane tożsamości dla rozszerzenia maszyny wirtualnej zasobów platformy Azure nie obsługują obecnie możliwości eksportowania jego schematu do szablonu grupy zasobów. W związku z tym wygenerowany szablon nie pokazuje parametrów konfiguracji, aby umożliwić zarządzanie tożsamościami dla zasobów platformy Azure w ramach zasobu. Te sekcje można dodać ręcznie, postępując zgodnie z przykładami w temacie [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).

Po udostępnieniu funkcji eksportu schematu dla tożsamości zarządzanych dla rozszerzenia maszyny wirtualnej Azure Resources (planowanego do wycofania w styczniu 2019) zostanie ono wyświetlone w obszarze [Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Ograniczenia rozszerzenia maszyny wirtualnej 

Istnieje kilka istotnych ograniczeń dotyczących używania rozszerzenia maszyny wirtualnej. 

 * Najważniejszym ograniczeniem jest fakt, że poświadczenia używane do żądania tokenów są przechowywane na maszynie wirtualnej. Osoba atakująca, która pomyślnie naruszyła maszynę wirtualną, może wyprowadzać poświadczenia. 
 * Ponadto rozszerzenie maszyny wirtualnej nadal jest nieobsługiwane przez kilka dystrybucji systemu Linux, dzięki czemu można modyfikować, kompilować i testować rozszerzenie dla każdej z tych dystrybucji. Obecnie obsługiwane są tylko następujące dystrybucje systemu Linux: 
    * CoreOS Stable
    * CentOS 7,1 
    * Red Hat 7,2 
    * Ubuntu 15,04 
    * Ubuntu 16.04
 * Istnieje wpływ na wydajność wdrażania maszyn wirtualnych z tożsamościami zarządzanymi, ponieważ także należy zastanowić się nad rozszerzeniem maszyny wirtualnej. 
 * Na koniec rozszerzenie maszyny wirtualnej może obsługiwać tylko 32 tożsamości zarządzane przypisane przez użytkownika na maszynę wirtualną. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

[Azure instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) to punkt końcowy REST, który zawiera informacje o uruchomionych wystąpieniach maszyn wirtualnych, które mogą służyć do zarządzania maszynami wirtualnymi i ich konfigurowania. Punkt końcowy jest dostępny w dobrze znanym adresie IP bez obsługi routingu ( `169.254.169.254` ), do którego można uzyskać dostęp tylko z poziomu maszyny wirtualnej.

Korzystanie z usługi Azure IMDS do żądania tokenów ma kilka zalet. 

1. Usługa jest zewnętrzna dla maszyny wirtualnej, dlatego poświadczenia używane przez zarządzane tożsamości nie są już obecne na maszynie wirtualnej. Zamiast tego są one hostowane i zabezpieczone na komputerze hosta maszyny wirtualnej platformy Azure.   
2. Wszystkie systemy operacyjne Windows i Linux obsługiwane przez usługę Azure IaaS mogą używać tożsamości zarządzanych.
3. Wdrożenie jest szybsze i łatwiejsze, ponieważ rozszerzenie maszyny wirtualnej nie musi już być obsługiwane.
4. Za pomocą punktu końcowego IMDS do jednej maszyny wirtualnej można przypisać do 1000 tożsamości zarządzane przypisane przez użytkownika.
5. Nie ma żadnej znaczącej zmiany w żądaniach korzystających z IMDS, w przeciwieństwie do tych korzystających z rozszerzenia maszyny wirtualnej, dlatego jest stosunkowo proste dla portów istniejących wdrożeń, które aktualnie korzystają z rozszerzenia maszyny wirtualnej.

Z tego względu usługa Azure IMDS będzie niedrogim sposobem żądania tokenów, gdy rozszerzenie maszyny wirtualnej jest przestarzałe. 


## <a name="next-steps"></a>Następne kroki

* [Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](../../virtual-machines/windows/instance-metadata-service.md)
