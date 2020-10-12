---
title: Dodawanie Azure Automation elementów Runbook do planów Site Recovery Recovery
description: Dowiedz się, jak rozłożyć plany odzyskiwania z Azure Automation na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424875"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Dodawanie elementów Runbook usługi Azure Automation do planów odzyskiwania

W tym artykule opisano sposób integrowania Azure Automation elementów Runbook w celu rozbudowy [Azure Site Recovery](site-recovery-overview.md) planów odzyskiwania. Pokazujemy, jak zautomatyzować podstawowe zadania, w przypadku których w przeciwnym razie będzie potrzebna ręczna interwencja, i jak przekonwertować wieloetapowe odzyskiwanie na akcję pojedynczego kliknięcia.

## <a name="recovery-plans"></a>Plany odzyskiwania 

Planów odzyskiwania można użyć po przełączeniu w tryb failover maszyn lokalnych lub maszyn wirtualnych platformy Azure. Plany odzyskiwania ułatwiają zdefiniowanie procesu odzyskiwania systematyczne, który definiuje sposób przełączenia maszyn w tryb failover oraz sposób ich uruchamiania i odzyskiwania po przejściu w tryb pracy awaryjnej. 

Duże aplikacje odzyskiwania mogą być złożone. Plany odzyskiwania ułatwiają zmianę kolejności, dzięki czemu odzyskiwanie jest stale dokładne, powtarzalne i zautomatyzowane. Można zautomatyzować zadania w ramach planu odzyskiwania przy użyciu skryptów, a także Azure Automation elementów Runbook. Typowymi przykładami mogą być Konfigurowanie ustawień na maszynie wirtualnej platformy Azure po przejściu w tryb failover lub ponowne skonfigurowanie aplikacji uruchomionej na maszynie wirtualnej.

- [Dowiedz się więcej](recovery-plan-overview.md) o planach odzyskiwania.
- [Dowiedz się więcej](../automation/automation-runbook-types.md) na temat Azure Automation elementów Runbook.



## <a name="runbooks-in-recovery-plans"></a>Elementy Runbook w planach odzyskiwania

Dodawanie konta Azure Automation i elementów Runbook do planu odzyskiwania. Element Runbook jest wywoływany po uruchomieniu planu odzyskiwania.

- Konto usługi Automation może znajdować się w dowolnym regionie świadczenia usługi Azure i musi znajdować się w tej samej subskrypcji co magazyn Site Recovery. 
- Element Runbook można uruchomić w planie odzyskiwania podczas pracy w trybie failover z lokalizacji podstawowej do pomocniczej lub podczas powrotu po awarii z lokalizacji dodatkowej do lokacji głównej.
- Elementy Runbook w planie odzyskiwania są uruchamiane sekwencyjnie, jeden po drugim w kolejności.
- Jeśli elementy Runbook w planie odzyskiwania skonfigurują maszyny wirtualne do uruchamiania w różnych grupach, plan odzyskiwania będzie kontynuowany tylko wtedy, gdy platforma Azure zgłosi wszystkie maszyny wirtualne jako uruchomione.
- Plany odzyskiwania nadal są uruchamiane, nawet jeśli wystąpi błąd skryptu.

### <a name="recovery-plan-context"></a>Kontekst planu odzyskiwania

Po uruchomieniu skryptu wprowadza kontekst planu odzyskiwania do elementu Runbook. Kontekst zawiera zmienne podsumowane w tabeli.

| **Nazwa zmiennej** | **Opis** |
| --- | --- |
| RecoveryPlanName |Nazwa planu odzyskiwania. Używane w akcjach na podstawie nazwy. |
| Tryb failovertype |Określa, czy jest to testowe, czy produkcyjne przejście w tryb failover. 
| Element failoverdirection | Określa, czy odzyskiwanie ma być lokalizacją główną czy dodatkową. |
| GroupID |Identyfikuje numer grupy w planie odzyskiwania, gdy plan jest uruchomiony. |
| VmMap |Tablica wszystkich maszyn wirtualnych w grupie. |
| Klucz VMMap |Unikatowy klucz (GUID) dla każdej maszyny wirtualnej. |
| SubscriptionId |Identyfikator subskrypcji platformy Azure, w której utworzono maszynę wirtualną. |
| ResourceGroupName | Nazwa grupy zasobów, w której znajduje się maszyna wirtualna.
| CloudServiceName |Nazwa usługi w chmurze platformy Azure, w której została utworzona maszyna wirtualna. |
| RoleName |Nazwa maszyny wirtualnej platformy Azure. |
| RecoveryPointId|Sygnatura czasowa odzyskiwania maszyny wirtualnej. |

>[!Note]
>Wartość zmiennej "element failoverdirection" będzie równa "PrimaryToSecondary" w przypadku przejścia w tryb failover i "SecondaryToPrimary" w przypadku powrotu po awarii.

W poniższym przykładzie przedstawiono zmienną kontekstową:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Jeśli chcesz uzyskać dostęp do wszystkich maszyn wirtualnych w VMMap w pętli, możesz użyć następującego kodu:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Blog Aman Sharma w usłudze w [chmurze zbierających](http://harvestingclouds.com) ma przydatny przykład [skryptu kontekstowego planu odzyskiwania](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Przed rozpoczęciem

- Jeśli dopiero zaczynasz Azure Automation, możesz [zarejestrować się](https://azure.microsoft.com/services/automation/) i [pobrać przykładowe skrypty](https://azure.microsoft.com/documentation/scripts/).
- Upewnij się, że konto usługi Automation ma następujące moduły:
    - AzureRM. profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Wszystkie moduły powinny być zgodne z wersjami. Najprostszym sposobem jest zawsze korzystanie z najnowszych wersji wszystkich modułów.



## <a name="customize-the-recovery-plan"></a>Dostosowywanie planu odzyskiwania

1. W magazynie wybierz pozycję **plany odzyskiwania (Site Recovery)**
2. Aby utworzyć plan odzyskiwania, kliknij pozycję **+ plan odzyskiwania**. [Dowiedz się więcej](site-recovery-create-recovery-plans.md). Jeśli masz już plan odzyskiwania, wybierz go, aby go otworzyć.
3. Na stronie plan odzyskiwania kliknij przycisk **Dostosuj**.

    ![Kliknij przycisk Dostosuj](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Kliknij przycisk wielokropka (...) obok pozycji **Grupa 1: Rozpocznij**  >  **Dodawanie wpisu**.
3. W obszarze **Wstaw akcję**Sprawdź, czy jest wybrany **skrypt** , a następnie określ nazwę skryptu (**Hello World**).
4. Określ konto usługi Automation i wybierz element Runbook. Aby zapisać skrypt, kliknij przycisk **OK**. Skrypt zostanie dodany do **grupy 1: kroki po kroku**.


## <a name="reuse-a-runbook-script"></a>Ponowne używanie skryptu elementu Runbook

Możesz użyć pojedynczego skryptu elementu Runbook w wielu planach odzyskiwania, używając zmiennych zewnętrznych. 

- [Zmienne Azure Automation](../automation/shared-resources/variables.md) są używane do przechowywania parametrów do uruchamiania planu odzyskiwania.
- Dodając nazwę planu odzyskiwania jako prefiks do zmiennej, można utworzyć poszczególne zmienne dla każdego planu odzyskiwania. Następnie użyj zmiennych jako parametrów.
- Można zmienić parametr bez zmiany skryptu, ale nadal zmienia sposób działania skryptu.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Używanie prostej zmiennej ciągu w skrypcie elementu Runbook

W tym przykładzie skrypt pobiera dane wejściowe sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i stosuje je do maszyn wirtualnych w planie odzyskiwania. 

1. Aby skrypt mógł wykryć, który plan odzyskiwania jest uruchomiony, użyj tego kontekstu planu odzyskiwania:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Zanotuj nazwę sieciowej grupy zabezpieczeń i grupę zasobów. Te zmienne są używane jako dane wejściowe skryptów planu odzyskiwania. 
1. W obszarze zasoby konta usługi Automation. Utwórz zmienną do przechowywania nazwy sieciowej grupy zabezpieczeń. Dodaj prefiks do nazwy zmiennej z nazwą planu odzyskiwania.

    ![Utwórz zmienną nazwy sieciowej grupy zabezpieczeń](media/site-recovery-runbook-automation-new/var1.png)

2. Utwórz zmienną do przechowywania nazwy grupy zasobów dla zasobu sieciowej grupy zabezpieczeń. Dodaj prefiks do nazwy zmiennej z nazwą planu odzyskiwania.

    ![Utwórz nazwę grupy zasobów sieciowej grupy zabezpieczeń](media/site-recovery-runbook-automation-new/var2.png)


3.  W skrypcie Użyj tego kodu referencyjnego, aby uzyskać wartości zmiennych:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Użyj zmiennych w elemencie Runbook, aby zastosować sieciowej grupy zabezpieczeń do interfejsu sieciowego maszyny wirtualnej w trybie failover:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Dla każdego planu odzyskiwania Utwórz zmienne niezależne, aby można było ponownie użyć skryptu. Dodaj prefiks przy użyciu nazwy planu odzyskiwania. 

Aby uzyskać kompletny, kompleksowy skrypt w tym scenariuszu, przejrzyj [ten skrypt](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Przechowywanie większej ilości informacji za pomocą złożonej zmiennej

W niektórych scenariuszach może nie być możliwe utworzenie oddzielnych zmiennych dla każdego planu odzyskiwania. Rozważmy scenariusz, w którym jeden skrypt ma przypisywać publiczny adres IP na określonych maszynach wirtualnych. W innym scenariuszu możesz chcieć zastosować różne sieciowych grup zabezpieczeń na różnych maszynach wirtualnych (poza wszystkimi maszynami wirtualnymi). Należy pamiętać, że:

- Można utworzyć skrypt do ponownego użycia dla dowolnego planu odzyskiwania.
- Każdy plan odzyskiwania może mieć zmienną liczbę maszyn wirtualnych.
- Na przykład odzyskiwanie programu SharePoint ma dwa Frontony. Podstawowa aplikacja biznesowa (LOB) ma tylko jeden fronton.
- W tym scenariuszu nie można utworzyć oddzielnych zmiennych dla każdego planu odzyskiwania.

W poniższym przykładzie utworzysz [zmienną złożoną](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) na koncie Azure Automation.

W tym celu należy określić wiele wartości przy użyciu Azure PowerShell.

1. W programie PowerShell Zaloguj się do subskrypcji platformy Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Aby przechowywać parametry, Utwórz zmienną złożoną przy użyciu nazwy planu odzyskiwania:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. W tej złożonej zmiennej **VMDetails** jest identyfikatorem maszyny wirtualnej chronionej maszyny wirtualnej. Aby uzyskać identyfikator maszyny wirtualnej, w Azure Portal wyświetlić właściwości maszyny wirtualnej. Poniższy zrzut ekranu przedstawia zmienną, która przechowuje szczegóły dwóch maszyn wirtualnych:

    ![Użyj identyfikatora maszyny wirtualnej jako identyfikatora GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Użyj tej zmiennej w elemencie Runbook. Jeśli wskazany identyfikator GUID maszyny wirtualnej zostanie znaleziony w kontekście planu odzyskiwania, Zastosuj sieciowej grupy zabezpieczeń na maszynie wirtualnej:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. W elemencie Runbook pętla za pośrednictwem maszyn wirtualnych kontekstu planu odzyskiwania. Sprawdź, czy maszyna wirtualna istnieje w **$VMDetailsObj**. Jeśli istnieje, uzyskaj dostęp do właściwości zmiennej, aby zastosować sieciowej grupy zabezpieczeń:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Możesz użyć tego samego skryptu dla różnych planów odzyskiwania. Wprowadź inne parametry, przechowując wartość odpowiadającą planowi odzyskiwania w różnych zmiennych.

## <a name="sample-scripts"></a>Przykładowe skrypty

Aby wdrożyć przykładowe skrypty na koncie usługi Automation, kliknij przycisk **Wdróż na platformie Azure** .

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ten film wideo zapewnia inny przykład. Przedstawiono w nim sposób odzyskiwania dwuwarstwowej aplikacji WordPress na platformę Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Automation konta Uruchom jako](../automation/manage-runas-account.md)
- Przejrzyj [Azure Automation przykładowe skrypty](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Dowiedz się więcej](site-recovery-failover.md) o uruchamianiu trybu failover.
