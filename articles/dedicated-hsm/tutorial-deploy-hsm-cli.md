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
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 63cdb27663cb1a2d8de1a97a2f352b05ff57a3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89489888"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Samouczek: wdrażanie sprzętowych modułów zabezpieczeń w istniejącej sieci wirtualnej przy użyciu interfejsu wiersza polecenia

Dedykowany moduł HSM platformy Azure udostępnia urządzenie fizyczne do wyłącznego użytku przez klienta. Daje on pełną kontrolę administracyjną, ale przenosi na klienta pełną odpowiedzialność za zarządzanie. Używanie urządzeń fizycznych powoduje konieczność kontrolowania przez firmę Microsoft przydzielania urządzeń w celu zapewnienia efektywnego zarządzania pojemnością. W związku z tym w ramach subskrypcji platformy Azure usługa dedykowanego modułu HSM nie będzie normalnie widoczna na potrzeby aprowizowania zasobów. Każdy klient platformy Azure, który potrzebuje dostępu do usługi dedykowanego modułu HSM, musi najpierw skontaktować się z pracownikiem odpowiedzialnym za kontakty z klientami w firmie Microsoft w celu zażądania rejestracji w ramach usługi dedykowanego modułu HSM. Aprowizacja będzie możliwa tylko po pomyślnym zakończeniu tego procesu. 

W tym samouczku przedstawiono typowy proces aprowizacji, w którym:

- klient ma już sieć wirtualną,
- klient ma maszynę wirtualną,
- klient potrzebuje dodać zasoby modułu HSM do tego istniejącego środowiska.

Typowa architektura wdrożenia w wielu regionach o wysokiej dostępności może wyglądać następująco:

![wdrażanie w wielu regionach](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Ten samouczek koncentruje się na dwóch modułach HSM i wymaganej bramie usługi ExpressRoute (patrz Podsieć 1 powyżej), które są integrowane w ramach istniejącej sieci wirtualnej (patrz Sieć wirtualna 1 powyżej).  Wszystkie inne zasoby to standardowe zasoby platformy Azure. Ten sam proces integracji może zostać użyty dla modułów HSM w podsieci 4 w sieci wirtualnej 3 powyżej.

## <a name="prerequisites"></a>Wymagania wstępne

Dedykowany moduł HSM platformy Azure nie jest obecnie dostępny w witrynie Azure Portal. Wszystkie interakcje z usługą będą miały miejsce za pośrednictwem wiersza polecenia lub programu PowerShell. W tym samouczku będzie używany interfejs wiersza polecenia w usłudze Azure Cloud Shell. Jeśli jesteś nowym użytkownikiem interfejsu wiersza polecenia platformy Azure, wykonaj instrukcje wprowadzające znajdujące się w sekcji dotyczącej [rozpoczynania pracy z interfejsem wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Założenia:

- Masz za sobą proces rejestracji dedykowanego modułu HSM platformy Azure.
- Masz zezwolenie na korzystanie z usługi. Jeśli tak nie jest, skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać szczegółowe informacje.
- Masz utworzoną grupę zasobów dla tych zasobów i nowe zasoby wdrożone w ramach tego samouczka zostaną dołączone do tej grupy.
- Masz utworzoną wymaganą sieć wirtualną, podsieć i maszyny wirtualne zgodnie z powyższym diagramem. Teraz chcesz zintegrować z tym wdrożeniem 2 moduły HSM.

W poniższych instrukcjach przyjęto założenie, że użytkownik już przeszedł do Azure Portal i otwarto Cloud Shell (wybierz pozycję " \> \_ " w prawym górnym rogu portalu).

## <a name="provisioning-a-dedicated-hsm"></a>Aprowizowanie dedykowanego modułu HSM

Aprowizowanie modułów HSM i integrowanie ich w ramach istniejącej sieci wirtualnej za pośrednictwem bramy usługi ExpressRoute zostanie zweryfikowane przy użyciu narzędzia SSH. Taka weryfikacja pomaga zapewnić osiągalność i podstawową dostępność urządzenia HSM dla wszelkich dalszych działań konfiguracyjnych. Następujące polecenia użyją szablonu usługi Azure Resource Manager do tworzenia zasobów modułu HSM i skojarzonych zasobów sieciowych.

### <a name="validating-feature-registration"></a>Weryfikowanie rejestracji funkcji

Jak wspomniano powyżej, wszelkie działania związane z aprowizacją wymagają, aby usługa dedykowanego modułu HSM była zarejestrowana dla Twojej subskrypcji. Aby to zweryfikować, uruchom następujące polecenia w usłudze Cloud Shell w witrynie Azure Portal.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Polecenia powinny zwracać stan "zarejestrowane" (jak pokazano poniżej). Jeśli polecenia nie zwracają "zarejestrowanych", musisz zarejestrować się w tej usłudze, kontaktując się z przedstawicielem konto Microsoft.

![stan subskrypcji](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Tworzenie zasobów modułu HSM

Moduł HSM jest inicjowany w sieci wirtualnej klientów, więc wymagana jest sieć wirtualna i podsieć. Zależność dla modułu HSM w celu umożliwienia komunikacji między siecią wirtualną i urządzeniem fizycznym stanowi brama usługi ExpressRoute. Wymagana jest również maszyna wirtualna służąca do uzyskania dostępu do urządzenia HSM za pomocą oprogramowania klienckiego firmy Gemalto. W celu ułatwienia użycia te zasoby zostały zebrane w pliku szablonu z odpowiadającym im plikiem parametrów. Te pliki zostaną udostępnione po skontaktowaniu się z firmą Microsoft bezpośrednio pod adresem HSMrequest@Microsoft.com.

Po uzyskaniu tych plików należy zmodyfikować plik parametrów i wstawić preferowane nazwy zasobów. Edytuj wiersze o wartości "value": "".

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

Skojarzony plik szablonu usługi Azure Resource Manager utworzy sześć zasobów z następującymi informacjami:

- Podsieć dla modułów HSM w określonej sieci wirtualnej
- Podsieć dla bramy sieci wirtualnej
- Brama sieci wirtualnej łącząca sieć wirtualną z urządzeniami HSM
- Publiczny adres IP bramy
- Moduł HSM w zasobach sprzętowych 1
- Moduł HSM w zasobach sprzętowych 2

Po ustawieniu wartości parametrów należy przekazać pliki do udziału plików usługi Cloud Shell w witrynie Azure Portal w celu ich użycia. W Azure Portal kliknij " \> \_ " symbol powłoki chmurowej w prawym górnym rogu, co spowoduje, że Dolna część ekranu jest środowiskiem poleceń. Opcje są następujące: powłoka BASH i program PowerShell. Należy wybrać powłokę BASH, jeśli nie zostało to wcześniej ustawione.

Powłoka poleceń ma opcję przekazywania/pobierania na pasku narzędzi, którą należy wybrać, aby przekazać pliki szablonów i parametrów do udziału plików:

![udział plików](media/tutorial-deploy-hsm-cli/file-share.png)

Po przekazaniu plików można przystąpić do tworzenia zasobów. Przed utworzeniem nowych zasobów modułu HSM należy sprawdzić, czy istnieją pewne wymagane wstępnie zasoby. Należy mieć sieć wirtualną z zakresami adresów na potrzeby obliczeń, modułów HSM i bramy. Następujące polecenia przedstawiają przykładowy sposób tworzenia takiej sieci wirtualnej.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
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

Gdy wszystkie wymagania wstępne zostaną spełnione, uruchom następujące polecenie, aby użyć szablonu usługi Azure Resource Manager zapewniającego, że wartości zostały zaktualizowane za pomocą unikatowych nazw (a przynajmniej nazwy grupy zasobów):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Wykonanie tego wdrożenia powinno zająć od 25 do 30 minut, przy czym większość tego czasu jest przeznaczona na urządzenia HSM

![stan aprowizacji](media/tutorial-deploy-hsm-cli/progress-status.png)

Po pomyślnym zakończeniu wdrożenia zostanie wyświetlona wartość "provisioningState" ("powodzenie"). Możesz nawiązać połączenie z istniejącą maszyną wirtualną i skorzystać z narzędzia SSH w celu zapewnienia dostępności urządzenia HSM.

## <a name="verifying-the-deployment"></a>Weryfikowanie wdrożenia

Aby zweryfikować, czy urządzenia zostały zaaprowizowane, i wyświetlić atrybuty urządzeń, uruchom następujący zestaw poleceń. Upewnij się, że grupa zasobów została odpowiednio ustawiona, a nazwa zasobu jest dokładnie taka sama jak w pliku parametrów.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![dane wyjściowe aprowizacji](media/tutorial-deploy-hsm-cli/progress-status2.png)

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

![lista składników](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Do tego momentu przydzielono wszystkie zasoby na potrzeby wdrożenia o wysokiej dostępności z dwoma modułami HSM oraz zweryfikowano dostęp i stan operacyjny. Dalsze czynności związane z konfiguracją lub testowaniem obejmują dodatkową pracę z samym modułem HSM. W tym celu należy postępować zgodnie z instrukcjami znajdującymi się w rozdziale 7 przewodnika administratora urządzenia Luna Network HSM 7 firmy Gemalto, które dotyczą inicjowania modułu HSM i tworzenia partycji. Całość dokumentacji i oprogramowania jest dostępna bezpośrednio od firmy Gemalto do pobrania po zarejestrowaniu się w portalu pomocy technicznej firmy Gemalto i uzyskaniu identyfikatora klienta. Pobierz oprogramowanie klienckie w wersji 7.2, aby uzyskać wszystkie wymagane składniki.

## <a name="delete-or-clean-up-resources"></a>Usuwanie lub czyszczenie zasobów

Jeśli zakończono pracę z urządzeniem HSM, to można je usunąć jako zasób i zwrócić do dostępnej puli. Poważnym problemem w takim przypadku są jakiekolwiek poufne dane klienta znajdujące się na urządzeniu. Najlepszym sposobem na "zeroize" jest to, że hasło administratora modułu HSM jest niewłaściwe 3 razy (Uwaga: nie jest to administrator urządzenia — jest to rzeczywisty administrator HSM). Ze względów bezpieczeństwa w celu ochrony materiału kluczowego urządzenie nie może zostać usunięte jako zasób platformy Azure, dopóki nie będzie w stanie wyzerować.

> [!NOTE]
> Jeśli masz problemy z jakimikolwiek czynnościami konfiguracyjnymi dotyczącymi urządzeń firmy Gemalto, skontaktuj się [z pomocą techniczną firmy Gemalto](https://safenet.gemalto.com/technical-support/).


Jeśli wszystkie zasoby w tej grupie zasobów zostały zakończone, można je usunąć za pomocą następującego polecenia:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Następne kroki

Po wykonaniu kroków w tym samouczku zasoby dedykowanego modułu HSM będą zaaprowizowane i dostępna będzie sieć wirtualną z wymaganymi modułami HSM i innymi składnikami sieciowymi umożliwiającymi komunikację z modułem HSM.  Jesteś teraz w stanie uzupełnić to wdrożenie dodatkowymi zasobami zgodnie z wymaganiami Twojej preferowanej architektury wdrożenia. Więcej informacji na temat ułatwiania planowania wdrożenia możesz znaleźć w dokumentach z pojęciami.
Zalecany jest projekt z dwoma modułami HSM w regionie podstawowym służącym do zapewnienia dostępności na poziomie regału sprzętowego i dwoma modułami HSM w regionie pomocniczym służącym do zapewnienia dostępności regionalnej. Plik szablonu użyty w tym samouczku może zostać w łatwy sposób użyty jako podstawa dla wdrożenia z dwoma modułami HSM, ale konieczne jest zmodyfikowanie jego parametrów w celu spełnienia wymagań.

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
