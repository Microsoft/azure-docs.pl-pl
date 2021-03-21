---
title: Samouczek — Tworzenie klastra usługi Azure Red Hat OpenShift 4
description: Dowiedz się, jak utworzyć Microsoft Azure klaster Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: 55c1b6f6c6690f0c8f00a8a2469834781f35fb3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449802"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: Tworzenie klastra usługi Azure Red Hat OpenShift 4

W tym samouczku część jednej z trzech zostanie przygotowana do utworzenia klastra usługi Azure Red Hat OpenShift z systemem OpenShift 4 i utworzenia klastra. Omawiane tematy:
> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne 
> * Utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Do utworzenia i uruchomienia klastra OpenShift na platformie Azure Red Hat OpenShift jest wymagany co najmniej 40 rdzeni. Domyślny przydział zasobów platformy Azure dla nowej subskrypcji platformy Azure nie spełnia tego wymagania. Aby zażądać zwiększenia limitu zasobów, zobacz [przydział standardowy: zwiększanie limitów przez serię maszyn wirtualnych](../azure-portal/supportability/per-vm-quota-requests.md).

Klucz tajny ściągania nie zmienia kosztu OpenShift licencji RH na potrzeby ARO.

### <a name="verify-your-permissions"></a>Sprawdzanie uprawnień

W tym samouczku utworzysz grupę zasobów, która będzie zawierać sieć wirtualną dla klastra. Użytkownik musi mieć uprawnienia administratora i dostępu współautora albo uprawnienia właściciela, bezpośrednio w sieci wirtualnej lub do grupy zasobów lub subskrypcji zawierającej tę opcję.

Wymagane są również wystarczające uprawnienia Azure Active Directory dla narzędzi, aby utworzyć aplikację i jednostkę usługi w Twoim imieniu dla klastra.

### <a name="register-the-resource-providers"></a>Rejestrowanie dostawców zasobów

1. Jeśli masz wiele subskrypcji platformy Azure, określ odpowiedni identyfikator subskrypcji:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zarejestruj `Microsoft.RedHatOpenShift` dostawcę zasobów:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. Zarejestruj `Microsoft.Compute` dostawcę zasobów:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. Zarejestruj `Microsoft.Storage` dostawcę zasobów:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Zarejestruj `Microsoft.Authorization` dostawcę zasobów:

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz wpis tajny Red Hat (opcjonalnie)

Klucz tajny w systemie Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat oraz dodatkową zawartość. Ten krok jest opcjonalny, ale zalecany.

1. [Przejdź do portalu Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) i zaloguj się.

   Musisz zalogować się do swojego konta Red Hat lub utworzyć nowe konto Red Hat przy użyciu firmowej poczty e-mail i zaakceptować warunki i postanowienia.

1. Kliknij pozycję **Pobierz klucz tajny** i Pobierz klucz tajny ściągania, który ma być używany z klastrem ARO.

    Przechowuj zapisany `pull-secret.txt` plik w bezpiecznym miejscu. Plik będzie używany podczas tworzenia klastra, jeśli trzeba utworzyć klaster zawierający przykłady lub operatorów dla systemów Red Hat lub certyfikowanych partnerów.

    Po uruchomieniu `az aro create` polecenia można odwoływać się do klucza tajnego ściągnięcia przy użyciu `--pull-secret @pull-secret.txt` parametru. Wykonaj `az aro create` z katalogu, w którym zapisano `pull-secret.txt` plik. W przeciwnym razie Zamień `@pull-secret.txt` na `@/path/to/my/pull-secret.txt` .

    Jeśli kopiujesz klucz tajny ściągania lub odwołujesz się do niego w innych skryptach, klucz tajny ściągania powinien być sformatowany jako prawidłowy ciąg JSON.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Przygotuj domenę niestandardową dla klastra (opcjonalnie)

Podczas uruchamiania `az aro create` polecenia można określić domenę niestandardową dla klastra przy użyciu `--domain foo.example.com` parametru.

Jeśli podano domenę niestandardową dla klastra, należy zwrócić uwagę na następujące kwestie:

* Po utworzeniu klastra należy utworzyć 2 rekordy A systemu DNS na serwerze DNS dla `--domain` określonych:
    * **interfejs API** — wskazuje adres IP serwera interfejsu API
    * **\* . Apps** — wskazuje adres IP protokołu przychodzącego
    * Pobierz te wartości, wykonując następujące polecenie po utworzeniu klastra: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* Konsola OpenShift będzie dostępna pod adresem URL, takim jak `https://console-openshift-console.apps.example.com` , a nie wbudowaną domeną `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Domyślnie OpenShift używa certyfikatów z podpisem własnym dla wszystkich tras tworzonych w domenach niestandardowych `*.apps.example.com` .  Jeśli zdecydujesz się używać niestandardowej usługi DNS po nawiązaniu połączenia z klastrem, musisz postępować zgodnie z dokumentacją OpenShift w celu [skonfigurowania niestandardowego urzędu certyfikacji dla kontrolera](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) usług przychodzących i [niestandardowego urzędu certyfikacji dla serwera interfejsu API](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Następnie utworzysz sieć wirtualną zawierającą dwie puste podsieci. Jeśli masz istniejącą sieć wirtualną, która spełnia Twoje wymagania, możesz pominąć ten krok.

1. **Ustaw następujące zmienne w środowisku powłoki, w którym będą wykonywane `az` polecenia.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Utwórz grupę zasobów.**

   Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja wskazuje, gdzie są przechowywane metadane grupy zasobów, a także w przypadku, gdy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów przy użyciu polecenia [az group create](/cli/azure/group#az-group-create).
    
   > [!NOTE] 
   > Usługa Azure Red Hat OpenShift nie jest dostępna we wszystkich regionach, w których można utworzyć grupę zasobów platformy Azure. Zobacz [dostępne regiony](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) , aby uzyskać informacje na temat tego, gdzie jest obsługiwana usługa Azure Red Hat OpenShift.

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Utwórz sieć wirtualną.**

   Klastry usługi Azure Red Hat OpenShift z systemem OpenShift 4 wymagają sieci wirtualnej z dwiema pustymi podsieciami dla węzłów głównych i procesów roboczych. Można utworzyć nową sieć wirtualną dla tego lub użyć istniejącej sieci wirtualnej.

   Utwórz nową sieć wirtualną w tej samej grupie zasobów, która została utworzona wcześniej:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   Następujące przykładowe dane wyjściowe pokazują, że sieć wirtualna została utworzona pomyślnie:

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. **Dodaj pustą podsieć dla węzłów głównych.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. **Dodaj pustą podsieć dla węzłów procesu roboczego.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. **[Wyłącz zasady prywatnego punktu końcowego podsieci](../private-link/disable-private-link-service-network-policy.md) w podsieci głównej.** Jest to wymagane, aby usługa mogła nawiązać połączenie z klastrem i zarządzać nim.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>Tworzenie klastra

Uruchom następujące polecenie, aby utworzyć klaster. Jeśli zdecydujesz się użyć jednej z następujących opcji, zmodyfikuj polecenie odpowiednio:
* Opcjonalnie możesz [przekazać swoje hasło w systemie Red Hat](#get-a-red-hat-pull-secret-optional) , które umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat oraz dodatkowej zawartości. Dodaj `--pull-secret @pull-secret.txt` argument do polecenia.
* Opcjonalnie możesz [użyć domeny niestandardowej](#prepare-a-custom-domain-for-your-cluster-optional). Dodaj `--domain foo.example.com` argument do polecenia, zastępując `foo.example.com` go własną domeną niestandardową.

> [!NOTE]
> Jeśli dodajesz opcjonalne argumenty do polecenia, pamiętaj o zamknięciu argumentu w poprzednim wierszu polecenia z końcowym ukośnikiem odwrotnym.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Po wykonaniu `az aro create` polecenia zwykle trwa około 35 minut na utworzenie klastra.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z klastrem usługi Azure Red Hat OpenShift](tutorial-connect-cluster.md)
