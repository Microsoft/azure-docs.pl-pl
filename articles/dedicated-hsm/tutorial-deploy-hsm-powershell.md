---
title: Samouczek dotyczący wdrażania w istniejącej sieci wirtualnej przy użyciu programu PowerShell — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Samouczek, w którym pokazano, jak wdrożyć dedykowany moduł HSM w istniejącej sieci wirtualnej przy użyciu programu PowerShell
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2020
ms.author: johndaw
ms.openlocfilehash: fc67012dff5931fb86452ea95c2ea074a426953c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075679"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Samouczek: wdrażanie modułów HSM w istniejącej sieci wirtualnej przy użyciu programu PowerShell

Usługa dedykowanego modułu HSM platformy Azure udostępnia urządzenie fizyczne do wyłącznego użytku przez klienta. Daje ono pełną kontrolę administracyjną, ale przenosi na klienta pełną odpowiedzialność za zarządzanie. Ze względu na udostępnienie sprzętu fizycznego firma Microsoft musi kontrolować, w jaki sposób te urządzenia są przydzielane w celu zapewnienia efektywnego zarządzania pojemnością. W związku z tym w ramach subskrypcji platformy Azure usługa dedykowanego modułu HSM nie będzie normalnie widoczna na potrzeby aprowizowania zasobów. Każdy klient platformy Azure, który potrzebuje dostępu do usługi dedykowanego modułu HSM, musi najpierw skontaktować się z pracownikiem odpowiedzialnym za kontakty z klientami w firmie Microsoft w celu zażądania rejestracji w ramach usługi dedykowanego modułu HSM. Aprowizacja będzie możliwa tylko po pomyślnym zakończeniu tego procesu.
Celem tego samouczka jest przedstawienie typowego procesu aprowizacji, w którym:

- klient ma już sieć wirtualną,
- klient ma maszynę wirtualną,
- klient potrzebuje dodać zasoby modułu HSM do tego istniejącego środowiska.

Typowa architektura wdrożenia w wielu regionach o wysokiej dostępności może wyglądać następująco:

![wdrażanie w wielu regionach](media/tutorial-deploy-hsm-powershell/high-availability.png)

Ten samouczek koncentruje się na dwóch modułach HSM i wymaganej bramie usługi ExpressRoute (patrz Podsieć 1 powyżej), które są integrowane w ramach istniejącej sieci wirtualnej (patrz Sieć wirtualna 1 powyżej).  Wszystkie inne zasoby to standardowe zasoby platformy Azure. Ten sam proces integracji może zostać użyty dla modułów HSM w podsieci 4 w sieci wirtualnej 3 powyżej.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Dedykowany moduł HSM platformy Azure nie jest obecnie dostępny w witrynie Azure Portal, w związku z tym wszystkie interakcje z usługą będą przeprowadzane za pośrednictwem wiersza polecenia lub przy użyciu programu PowerShell. W tym samouczku zostanie użyty program PowerShell w ramach usługi Azure Cloud Shell. Jeśli jesteś nowym użytkownikiem programu PowerShell, wykonaj instrukcje wprowadzającego znajdujące się w sekcji dotyczącej [rozpoczynania pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).

Założenia:

- Masz za sobą proces rejestracji dedykowanego modułu HSM platformy Azure i masz zezwolenie na korzystanie z usługi. Jeśli tak nie jest, skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje. 
- Masz utworzoną grupę zasobów dla tych zasobów i nowe zasoby wdrożone w ramach tego samouczka zostaną dołączone do tej grupy.
- Masz utworzoną wymaganą sieć wirtualną, podsieć i maszyny wirtualne zgodnie z powyższym diagramem. Teraz chcesz zintegrować z tym wdrożeniem 2 moduły HSM.

We wszystkich poniższych instrukcjach przyjęto założenie, że jesteś już w witrynie Azure Portal i masz otwartą usługę Cloud Shell (pozycja „\>\_” w prawej górnej części portalu).

## <a name="provisioning-a-dedicated-hsm"></a>Aprowizowanie dedykowanego modułu HSM

Aprowizowanie modułów HSM i integrowanie ich w ramach istniejącej sieci wirtualnej za pośrednictwem bramy usługi ExpressRoute zostanie zweryfikowane przy użyciu narzędzia wiersza polecenia protokołu SSH w celu zapewnienia osiągalności i podstawowej dostępności urządzenia HSM dla wszelkich dalszych działań konfiguracyjnych. Następujące polecenia użyją szablonu usługi Resource Manager do tworzenia zasobów modułu HSM i skojarzonych zasobów sieciowych.

### <a name="validating-feature-registration"></a>Weryfikowanie rejestracji funkcji

Jak wspomniano powyżej, wszelkie działania związane z aprowizacją wymagają, aby usługa dedykowanego modułu HSM była zarejestrowana dla Twojej subskrypcji. Aby to zweryfikować, uruchom następujące polecenie programu PowerShell w usłudze Cloud Shell w witrynie Azure Portal. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

Polecenie powinno zwrócić stan "zarejestrowane" (jak pokazano poniżej) przed dalszym przeprowadzeniem dalszych działań.  Jeśli nie zarejestrowano Cię do tej usługi, skontaktuj się z przedstawicielem konto Microsoft.

![stan subskrypcji](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Tworzenie zasobów modułu HSM

Urządzenie HSM jest aprowizowane w sieci wirtualnej klientów. Oznacza to konieczność użycia podsieci. Zależność dla modułu HSM w celu umożliwienia komunikacji między siecią wirtualną i urządzeniem fizycznym stanowi brama usługi ExpressRoute. Wymagana jest również maszyna wirtualna służąca do uzyskania dostępu do urządzenia HSM za pomocą oprogramowania klienckiego firmy Gemalto. W celu ułatwienia użycia te zasoby zostały zebrane w pliku szablonu z odpowiadającym im plikiem parametrów. Te pliki zostaną udostępnione po skontaktowaniu się z firmą Microsoft bezpośrednio pod adresem HSMrequest@Microsoft.com.

Po uzyskaniu tych plików należy zmodyfikować plik parametrów i wstawić preferowane nazwy zasobów. Oznacza to konieczność edytowania wierszy z wartością "value": "".

- `namingInfix` Prefiks nazw zasobów modułu HSM
- `ExistingVirtualNetworkName` Nazwa sieci wirtualnej używanej dla modułów HSM
- `DedicatedHsmResourceName1` Nazwa zasobu modułu HSM w zasobach sprzętowych 1 centrum danych
- `DedicatedHsmResourceName2` Nazwa zasobu modułu HSM w zasobach sprzętowych 2 centrum danych
- `hsmSubnetRange` Zakres adresów IP podsieci dla modułów HSM
- `ERSubnetRange` Zakres adresów IP podsieci dla bramy sieci wirtualnej

Przykład tych zmian jest następujący:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Skojarzony plik szablonu usługi Resource Manager utworzy sześć zasobów z następującymi informacjami:

- Podsieć dla modułów HSM w określonej sieci wirtualnej
- Podsieć dla bramy sieci wirtualnej 
- Brama sieci wirtualnej łącząca sieć wirtualną z urządzeniami HSM
- Publiczny adres IP bramy
- Moduł HSM w zasobach sprzętowych 1
- Moduł HSM w zasobach sprzętowych 2

Po ustawieniu wartości parametrów należy przekazać pliki do udziału plików usługi Cloud Shell w witrynie Azure Portal w celu ich użycia. W prawej górnej części witryny Azure Portal kliknij symbol usługi Cloud Shell „\>\_”, co spowoduje, że dolna część ekranu stanie się środowiskiem poleceń. Opcje są następujące: powłoka BASH i program PowerShell. Należy wybrać powłokę BASH, jeśli nie zostało to wcześniej ustawione.

Powłoka poleceń ma opcję przekazywania/pobierania na pasku narzędzi, którą należy wybrać, aby przekazać pliki szablonów i parametrów do udziału plików:

![udział plików](media/tutorial-deploy-hsm-powershell/file-share.png)

Po przekazaniu plików można przystąpić do tworzenia zasobów.
Przed utworzeniem nowych zasobów modułu HSM należy sprawdzić, czy istnieją pewne wymagane wstępnie zasoby. Należy mieć sieć wirtualną z zakresami adresów na potrzeby obliczeń, modułów HSM i bramy. Następujące polecenia przedstawiają przykładowy sposób tworzenia takiej sieci wirtualnej.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>Najważniejszą czynnością konfiguracyjną, o której należy pamiętać w przypadku sieci wirtualnej, jest konieczność ustawienia delegacji urządzenia HSM na wartość „Microsoft.HardwareSecurityModules/dedicatedHSMs”.  Aprowizacja modułu HSM nie będzie możliwa bez tego ustawienia.

Gdy wszystkie wymagania wstępne zostaną spełnione, uruchom następujące polecenie, aby użyć szablonu usługi Resource Manager zapewniającego, że wartości zostały zaktualizowane za pomocą unikatowych nazw (a przynajmniej nazwy grupy zasobów):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Wykonywanie tego polecenia powinno zająć około 20 minut. Opcja „-Verbose” służy do ciągłego wyświetlania stanu.

![stan aprowizacji](media/tutorial-deploy-hsm-powershell/progress-status.png)

Po pomyślnym wykonaniu tego polecenia, na co wskazywać będzie wyświetlenie komunikatu "provisioningState": "Succeeded", możesz zalogować się do istniejącej maszyny wirtualnej i użyć narzędzia SSH w celu zapewnienia dostępności urządzenia HSM.

## <a name="verifying-the-deployment"></a>Weryfikowanie wdrożenia

Aby zweryfikować, czy urządzenia zostały zaaprowizowane, i wyświetlić atrybuty urządzeń, uruchom następujący zestaw poleceń. Upewnij się, że grupa zasobów została odpowiednio ustawiona, a nazwa zasobu jest dokładnie taka sama jak w pliku parametrów.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![stan aprowizacji](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Teraz będzie można również wyświetlić zasoby za pomocą [eksploratora zasobów platformy Azure](https://resources.azure.com/).   W eksploratorze rozwiń pozycję „subskrypcje” po lewej stronie, rozwiń konkretną subskrypcję dla dedykowanego modułu HSM, rozwiń pozycję „grupy zasobów”, rozwiń grupę zasobów, która została użyta, i na koniec wybierz element „zasoby”.

## <a name="testing-the-deployment"></a>Testowanie wdrożenia

Testowanie wdrożenia polega na nawiązaniu połączenia z maszyną wirtualną, która ma dostęp do modułów HSM, a następnie bezpośrednim nawiązaniu połączenia z urządzeniem HSM. Za pomocą tych akcji można potwierdzić, czy moduł HSM jest osiągalny.
Narzędzie SSH jest używane do nawiązywania połączenia z maszyną wirtualną. Polecenie będzie podobne do poniższego, ale zostanie użyta nazwa administratora i nazwa serwera DNS określona w parametrze.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Używane hasło to hasło z pliku parametrów.
Po zalogowaniu się do maszyny wirtualnej z systemem Linux możesz zalogować się do modułu HSM przy użyciu prywatnego adresu IP znalezionego w portalu dla zasobu \<prefix>hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Gdy będziesz mieć adres IP, uruchom następujące polecenie:

`ssh tenantadmin@<ip address of HSM>`

W przypadku powodzenia zostanie wyświetlony monit o podanie hasła. Domyślne hasło to PASSWORD. Moduł HSM wyświetli monit o zmianę hasła. Ustaw silne hasło i skorzystaj z dowolnego mechanizmu, który jest preferowany w Twojej organizacji do przechowywania hasła i zapobiegania jego utracie.  

>[!IMPORTANT]
>Jeśli hasło zostanie utracone, konieczne będzie zresetowanie modułu HSM, co spowoduje utratę kluczy.

Po nawiązaniu połączenia z urządzeniem HSM przy użyciu protokołu SSH uruchom następujące polecenie, aby upewnić się, że moduł HSM działa.

`hsm show`

Dane wyjściowe powinny wyglądać podobnie do tych na poniższym obrazie:

![stan aprowizacji](media/tutorial-deploy-hsm-powershell/output.png)

Do tego momentu przydzielono wszystkie zasoby na potrzeby wdrożenia o wysokiej dostępności z dwoma modułami HSM oraz zweryfikowano dostęp i stan operacyjny. Dalsze czynności związane z konfiguracją lub testowaniem obejmują dodatkową pracę z samym modułem HSM. W tym celu należy postępować zgodnie z instrukcjami znajdującymi się w rozdziale 7 przewodnika administratora urządzenia Luna Network HSM 7 firmy Gemalto, które dotyczą inicjowania modułu HSM i tworzenia partycji. Całość dokumentacji i oprogramowania jest dostępna bezpośrednio od firmy Gemalto do pobrania po zarejestrowaniu się w portalu pomocy technicznej firmy Gemalto i uzyskaniu identyfikatora klienta. Pobierz oprogramowanie klienckie w wersji 7.2, aby uzyskać wszystkie wymagane składniki.

## <a name="delete-or-clean-up-resources"></a>Usuwanie lub czyszczenie zasobów

Jeśli zakończono pracę z urządzeniem HSM, to można je usunąć jako zasób i zwrócić do dostępnej puli. Poważnym problemem w takim przypadku są jakiekolwiek poufne dane klienta znajdujące się na urządzeniu. Najlepszym sposobem na "zeroize" jest to, że hasło administratora modułu HSM jest niewłaściwe 3 razy (Uwaga: nie jest to administrator urządzenia — jest to rzeczywisty administrator HSM). Ze względów bezpieczeństwa w celu ochrony materiału kluczowego urządzenie nie może zostać usunięte jako zasób platformy Azure, dopóki nie będzie w stanie wyzerować.

> [!NOTE]
> Jeśli masz problemy z jakimikolwiek czynnościami konfiguracyjnymi dotyczącymi urządzeń firmy Gemalto, skontaktuj się [z pomocą techniczną firmy Gemalto](https://safenet.gemalto.com/technical-support/).

Jeśli chcesz usunąć zasób HSM na platformie Azure, możesz użyć następującego polecenia zastępującego zmienne "$" własnymi parametrami:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Następne kroki

Po wykonaniu kroków w tym samouczku zasoby dedykowanego modułu HSM zostaną zaaprowizowane i będą dostępne w Twojej sieci wirtualnej. Jesteś teraz w stanie uzupełnić to wdrożenie dodatkowymi zasobami zgodnie z wymaganiami Twojej preferowanej architektury wdrożenia. Więcej informacji na temat ułatwiania planowania wdrożenia możesz znaleźć w dokumentach z pojęciami. Zalecany jest projekt z dwoma modułami HSM w regionie podstawowym służącym do zapewnienia dostępności na poziomie regału sprzętowego i dwoma modułami HSM w regionie pomocniczym służącym do zapewnienia dostępności regionalnej. Plik szablonu użyty w tym samouczku może zostać w łatwy sposób użyty jako podstawa dla wdrożenia z dwoma modułami HSM, ale konieczne jest zmodyfikowanie jego parametrów w celu spełnienia wymagań.

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Monitorowanie](monitoring.md)
* [Możliwości obsługi](supportability.md)
