---
title: 'Samouczek: wdrażanie dedykowanych modułów HSM platformy Azure w istniejącej sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs'
description: Samouczek, w którym pokazano, jak wdrożyć dedykowany moduł HSM w istniejącej sieci wirtualnej przy użyciu interfejsu wiersza polecenia
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 1ca8bc5c82540b0dc02959d26b452554ef294368
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200533"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Samouczek: wdrażanie sprzętowych modułów zabezpieczeń w istniejącej sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Dedykowany moduł HSM platformy Azure udostępnia urządzenie fizyczne do wyłącznego użytku przez klienta. Daje on pełną kontrolę administracyjną, ale przenosi na klienta pełną odpowiedzialność za zarządzanie. Używanie urządzeń fizycznych powoduje konieczność kontrolowania przez firmę Microsoft przydzielania urządzeń w celu zapewnienia efektywnego zarządzania pojemnością. W związku z tym w ramach subskrypcji platformy Azure usługa dedykowanego modułu HSM nie będzie normalnie widoczna na potrzeby aprowizowania zasobów. Każdy klient platformy Azure, który potrzebuje dostępu do usługi dedykowanego modułu HSM, musi najpierw skontaktować się z pracownikiem odpowiedzialnym za kontakty z klientami w firmie Microsoft w celu zażądania rejestracji w ramach usługi dedykowanego modułu HSM. Aprowizacja będzie możliwa tylko po pomyślnym zakończeniu tego procesu. 

W tym samouczku przedstawiono typowy proces aprowizacji, w którym:

- klient ma już sieć wirtualną,
- klient ma maszynę wirtualną,
- klient potrzebuje dodać zasoby modułu HSM do tego istniejącego środowiska.

Typowa architektura wdrożenia w wielu regionach o wysokiej dostępności może wyglądać następująco:

![wdrażanie w wielu regionach](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Ten samouczek koncentruje się na dwóch modułach HSM i wymaganej bramie usługi ExpressRoute (patrz Podsieć 1 powyżej), które są integrowane w ramach istniejącej sieci wirtualnej (patrz Sieć wirtualna 1 powyżej).  Wszystkie inne zasoby to standardowe zasoby platformy Azure. Ten sam proces integracji może zostać użyty dla modułów HSM w podsieci 4 w sieci wirtualnej 3 powyżej.

## <a name="prerequisites"></a>Wymagania wstępne

Dedykowany moduł HSM platformy Azure nie jest obecnie dostępny w witrynie Azure Portal. Wszystkie interakcje z usługą będą miały miejsce za pośrednictwem wiersza polecenia lub programu PowerShell. W tym samouczku będzie używany interfejs wiersza polecenia w usłudze Azure Cloud Shell. Jeśli jesteś nowym użytkownikiem interfejsu wiersza polecenia platformy Azure, wykonaj instrukcje wprowadzające znajdujące się w sekcji dotyczącej [rozpoczynania pracy z interfejsem wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/get-started-with-azure-cli).

Założenia:

- Masz za sobą proces rejestracji dedykowanego modułu HSM platformy Azure.
- Masz zezwolenie na korzystanie z usługi. Jeśli tak nie jest, skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje.
- Masz utworzoną grupę zasobów dla tych zasobów i nowe zasoby wdrożone w ramach tego samouczka zostaną dołączone do tej grupy.
- Masz utworzoną wymaganą sieć wirtualną, podsieć i maszyny wirtualne zgodnie z powyższym diagramem. Teraz chcesz zintegrować z tym wdrożeniem 2 moduły HSM.

W poniższych instrukcjach przyjęto założenie, że użytkownik już przeszedł do Azure Portal i otwarto Cloud Shell (wybierz pozycję " \> \_ " w prawym górnym rogu portalu).

## <a name="provisioning-a-dedicated-hsm"></a>Aprowizowanie dedykowanego modułu HSM

Aprowizowanie modułów HSM i integrowanie ich w ramach istniejącej sieci wirtualnej za pośrednictwem bramy usługi ExpressRoute zostanie zweryfikowane przy użyciu narzędzia SSH. Taka weryfikacja pomaga zapewnić osiągalność i podstawową dostępność urządzenia HSM dla wszelkich dalszych działań konfiguracyjnych.

### <a name="validating-feature-registration"></a>Weryfikowanie rejestracji funkcji

Jak wspomniano powyżej, wszelkie działania związane z aprowizacją wymagają, aby usługa dedykowanego modułu HSM była zarejestrowana dla Twojej subskrypcji. Aby to sprawdzić, uruchom następujące polecenia w Cloud Shell Azure Portal.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Polecenia powinny zwracać stan "zarejestrowane" (jak pokazano poniżej). Jeśli polecenia nie zwracają "zarejestrowanych", musisz zarejestrować się w tej usłudze, kontaktując się z przedstawicielem konto Microsoft.

![stan subskrypcji](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Tworzenie zasobów modułu HSM

Przed utworzeniem zasobów modułu HSM wymagane są pewne wymagane zasoby. Należy mieć sieć wirtualną z zakresami adresów na potrzeby obliczeń, modułów HSM i bramy. Następujące polecenia przedstawiają przykładowy sposób tworzenia takiej sieci wirtualnej.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>Najważniejszymi konfiguracjami, które należy zwrócić do sieci wirtualnej, jest to, że podsieć dla urządzenia HSM musi mieć ustawioną delegację "Microsoft. HardwareSecurityModules/modułów dedicatedhsms".  Aprowizacja modułu HSM nie będzie możliwa bez ustawienia tej opcji.

Po skonfigurowaniu sieci Użyj tych poleceń interfejsu wiersza polecenia platformy Azure, aby udostępnić sprzętowych modułów zabezpieczeń.

1. Użyj polecenia [AZ dedykowanego modułu HSM Create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) , aby zainicjować obsługę pierwszego modułu HSM. Moduł HSM ma nazwę hsm1. Zastąp subskrypcję:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   To wdrożenie powinno potrwać około 25 do 30 minut, a tym samym czasie są urządzenia HSM.

1. Aby wyświetlić bieżący moduł HSM, uruchom polecenie [AZ dedykowanego modułu HSM show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) :

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. Zainicjuj obsługę drugiego modułu HSM przy użyciu tego polecenia:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Uruchom polecenie [AZ dedykowanego modułu HSM](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) , aby wyświetlić szczegółowe informacje o bieżącym sprzętowych modułów zabezpieczeń:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Istnieją inne polecenia, które mogą być przydatne. Użyj polecenia [AZ dedykowanego-HSM Update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) , aby zaktualizować moduł HSM:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

Aby usunąć moduł HSM, użyj polecenia [AZ dedykowanego modułu HSM Delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) :

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Weryfikowanie wdrożenia

Aby zweryfikować, czy urządzenia zostały zaaprowizowane, i wyświetlić atrybuty urządzeń, uruchom następujący zestaw poleceń. Upewnij się, że grupa zasobów została odpowiednio ustawiona, a nazwa zasobu jest dokładnie taka sama jak w pliku parametrów.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

Dane wyjściowe wyglądają podobnie jak następujące dane wyjściowe:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

Teraz będzie można również wyświetlić zasoby za pomocą [eksploratora zasobów platformy Azure](https://resources.azure.com/).   W Eksploratorze rozwiń pozycję "subskrypcje" po lewej stronie, rozwiń swoją konkretną subskrypcję dla dedykowanego modułu HSM, rozwiń pozycję "grupy zasobów", rozwiń grupę zasobów, której użyto, i wybierz pozycję "zasoby".

## <a name="testing-the-deployment"></a>Testowanie wdrożenia

Testowanie wdrożenia polega na nawiązaniu połączenia z maszyną wirtualną, która ma dostęp do modułów HSM, a następnie bezpośrednim nawiązaniu połączenia z urządzeniem HSM. Za pomocą tych akcji można potwierdzić, czy moduł HSM jest osiągalny.
Narzędzie SSH jest używane do nawiązywania połączenia z maszyną wirtualną. Polecenie będzie podobne do poniższego, ale zostanie użyta nazwa administratora i nazwa serwera DNS określona w parametrze.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

W powyższym poleceniu adres IP maszyny wirtualnej może również zostać użyty zamiast nazwy DNS. Jeśli polecenie zakończy się pomyślnie, zostanie wyświetlony monit o wprowadzenie hasła. Wprowadź je. Po zalogowaniu się do maszyny wirtualnej możesz zalogować się do modułu HSM przy użyciu prywatnego adresu IP znalezionego w portalu dla zasobu interfejsu sieciowego skojarzonego z modułem HSM.

![lista składników](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Zwróć uwagę, że pole wyboru "Pokaż ukryte typy" spowoduje wyświetlenie zasobów modułu HSM.

Na poniższym zrzucie ekranu kliknięcie "HSM1_HSMnic" lub "HSM2_HSMnic" spowoduje wyświetlenie odpowiedniego prywatnego adresu IP. W innym przypadku użyte powyżej polecenie `az resource show` to sposób identyfikacji poprawnego adresu IP. 

Jeśli masz poprawny adres IP, uruchom następujące polecenie, zastępując poniższy adres:

`ssh tenantadmin@10.0.2.4`

W przypadku powodzenia zostanie wyświetlony monit o podanie hasła. Domyślne hasło to PASSWORD. Moduł HSM najpierw wyświetli monit o zmianę hasła. Ustaw silne hasło i skorzystaj z dowolnego mechanizmu, który jest preferowany w Twojej organizacji do przechowywania hasła i zapobiegania jego utracie.

>[!IMPORTANT]
>Jeśli hasło zostanie utracone, konieczne będzie zresetowanie modułu HSM, co spowoduje utratę kluczy.

Po nawiązaniu połączenia z modułem HSM przy użyciu protokołu SSH uruchom następujące polecenie, aby upewnić się, że moduł HSM działa.

`hsm show`

Dane wyjściowe powinny wyglądać jak te na poniższym obrazie:

![Zrzut ekranu przedstawia dane wyjściowe w oknie programu PowerShell.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Do tego momentu przydzielono wszystkie zasoby na potrzeby wdrożenia o wysokiej dostępności z dwoma modułami HSM oraz zweryfikowano dostęp i stan operacyjny. Dalsze czynności związane z konfiguracją lub testowaniem obejmują dodatkową pracę z samym modułem HSM. W tym celu należy postępować zgodnie z instrukcjami w przewodniku administracji firmy Thales Luna Network HSM 7 Rozdział 7, aby zainicjować moduł HSM i utworzyć partycje. Cała dokumentacja i oprogramowanie są dostępne bezpośrednio z programu firmy Thales do pobrania po zarejestrowaniu się w portalu obsługi klienta firmy Thales i o IDENTYFIKATORze klienta. Pobierz oprogramowanie klienckie w wersji 7.2, aby uzyskać wszystkie wymagane składniki.

## <a name="delete-or-clean-up-resources"></a>Usuwanie lub czyszczenie zasobów

Jeśli zakończono pracę z urządzeniem HSM, to można je usunąć jako zasób i zwrócić do dostępnej puli. Poważnym problemem w takim przypadku są jakiekolwiek poufne dane klienta znajdujące się na urządzeniu. Najlepszym sposobem na "zeroize" jest to, że hasło administratora modułu HSM jest niewłaściwe 3 razy (Uwaga: nie jest to administrator urządzenia — jest to rzeczywisty administrator HSM). Ze względów bezpieczeństwa w celu ochrony materiału kluczowego urządzenie nie może zostać usunięte jako zasób platformy Azure, dopóki nie będzie w stanie wyzerować.

> [!NOTE]
> Jeśli masz problem z dowolną konfiguracją urządzeń firmy Thales, skontaktuj się z działem [obsługi klienta firmy Thales](https://safenet.gemalto.com/technical-support/).

Jeśli wszystkie zasoby w tej grupie zasobów zostały zakończone, można je usunąć za pomocą następującego polecenia:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Następne kroki

Po wykonaniu kroków w tym samouczku zasoby dedykowanego modułu HSM będą zaaprowizowane i dostępna będzie sieć wirtualną z wymaganymi modułami HSM i innymi składnikami sieciowymi umożliwiającymi komunikację z modułem HSM.  Jesteś teraz w stanie uzupełnić to wdrożenie dodatkowymi zasobami zgodnie z wymaganiami Twojej preferowanej architektury wdrożenia. Więcej informacji na temat ułatwiania planowania wdrożenia możesz znaleźć w dokumentach z pojęciami.
Zalecany jest projekt z dwoma modułami HSM w regionie podstawowym służącym do zapewnienia dostępności na poziomie regału sprzętowego i dwoma modułami HSM w regionie pomocniczym służącym do zapewnienia dostępności regionalnej. 

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
