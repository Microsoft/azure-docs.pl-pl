---
title: Samouczek — tworzenie klastra Azure Red Hat OpenShift 4
description: Dowiedz się, jak utworzyć klaster usługi Microsoft Azure Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: dda4fc6a80bbe07977f8d2a5ffcbea895a4e1fe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771843"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: tworzenie klastra Azure Red Hat OpenShift 4

W tym samouczku (część 1 z trzech) przygotujesz środowisko do utworzenia klastra usługi Azure Red Hat OpenShift z systemem OpenShift 4 i utworzenia klastra. Omawiane tematy:
> [!div class="checklist"]
> * Konfigurowanie wymagań wstępnych 
> * Tworzenie wymaganej sieci wirtualnej i podsieci
> * Wdrażanie klastra

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Azure Red Hat OpenShift do utworzenia i uruchomienia klastra OpenShift wymagane jest co najmniej 40 rdzeni. Domyślny limit przydziału zasobów platformy Azure dla nowej subskrypcji platformy Azure nie spełnia tego wymagania. Aby zażądać zwiększenia limitu zasobów, zobacz [Standard quota: Increase limits by VM series (Standardowy limit przydziału: zwiększanie limitów według serii maszyn wirtualnych).](../azure-portal/supportability/per-vm-quota-requests.md)

Klucz tajny ściągania ARO nie zmienia kosztu licencji RH OpenShift dla ARO.

### <a name="verify-your-permissions"></a>Sprawdzanie uprawnień

W tym samouczku utworzysz grupę zasobów, która będzie zawierać sieć wirtualną klastra. Musisz mieć uprawnienia Współautor i Administrator dostępu użytkowników lub Właściciel bezpośrednio w sieci wirtualnej albo w grupie zasobów lub subskrypcji, która ją zawiera.

Potrzebne będą również wystarczające uprawnienia Azure Active Directory narzędzi do tworzenia aplikacji i jednostki usługi w Twoim imieniu dla klastra.

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

### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz klucz tajny ściągania Red Hat (opcjonalnie)

Wpis tajny ściągania systemu Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat wraz z dodatkową zawartością. Ten krok jest opcjonalny, ale zalecany.

1. [Przejdź do portalu menedżera klastra Red Hat OpenShift i](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) zaloguj się.

   Musisz zalogować się do konta red hat lub utworzyć nowe konto Red Hat przy użyciu służbowego adresu e-mail i zaakceptować warunki i postanowienia.

1. Kliknij **pozycję Pobierz klucz tajny ściągnięcia** i pobierz klucz tajny ściągania, który będzie używany z klastrem ARO.

    Przechowuj zapisany `pull-secret.txt` plik w bezpiecznym miejscu. Plik będzie używany w każdym tworzeniu klastra, jeśli musisz utworzyć klaster, który zawiera przykłady lub operatory dla oprogramowania Red Hat lub certyfikowanych partnerów.

    Podczas uruchamiania polecenia możesz odwoływać się do swojego `az aro create` tajnego ściągnięć przy użyciu `--pull-secret @pull-secret.txt` parametru . Wykonaj `az aro create` polecenie z katalogu, w którym został zapisany `pull-secret.txt` plik. W przeciwnym razie zastąp `@pull-secret.txt` . `@/path/to/my/pull-secret.txt`

    Jeśli kopiujesz klucz tajny ściągania lub odwołujesz się do niego w innych skryptach, klucz tajny ściągania powinien być sformatowany jako prawidłowy ciąg JSON.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Przygotowywanie domeny niestandardowej dla klastra (opcjonalnie)

Podczas uruchamiania `az aro create` polecenia można określić domenę niestandardową dla klastra przy użyciu `--domain foo.example.com` parametru .

Jeśli podano domenę niestandardową dla klastra, zwróć uwagę na następujące kwestie:

* Po utworzeniu klastra należy utworzyć 2 rekordy DNS A na serwerze DNS dla `--domain` określonego:
    * **api** — skierowanie na adres IP serwera interfejsu API
    * **\* .apps** — skierowanie na adres IP dla danych przychodzących
    * Pobierz te wartości, wykonując następujące polecenie po utworzeniu klastra: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* Konsola OpenShift będzie dostępna pod adresem URL, takim jak `https://console-openshift-console.apps.example.com` , zamiast wbudowanej domeny `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Domyślnie system OpenShift używa certyfikatów z podpisem własnym dla wszystkich tras utworzonych w domenach `*.apps.example.com` niestandardowych.  Jeśli zdecydujesz się używać niestandardowego systemu DNS po nałączeniu z klastrem, postępuj zgodnie z dokumentacją platformy OpenShift, aby skonfigurować niestandardowy urząd certyfikacji dla kontrolera ruchu wychodzącego i niestandardowy urząd certyfikacji dla serwera interfejsu [API.](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html) [](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html)

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Następnie utworzysz sieć wirtualną zawierającą dwie puste podsieci. Jeśli masz istniejącą sieć wirtualną, która spełnia Twoje potrzeby, możesz pominąć ten krok.

1. **Ustaw następujące zmienne w środowisku powłoki, w którym będą wykonywane `az` polecenia.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Utwórz grupę zasobów.**

   Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja to miejsce, w którym są przechowywane metadane grupy zasobów, oraz miejsce, w którym zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów przy użyciu polecenia [az group create](/cli/azure/group#az_group_create).
    
   > [!NOTE] 
   > Azure Red Hat OpenShift nie jest dostępna we wszystkich regionach, w których można utworzyć grupę zasobów platformy Azure. Zobacz [Dostępne regiony,](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) aby uzyskać informacje na temat Azure Red Hat OpenShift obsługiwanych regionów.

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

   Azure Red Hat OpenShift z systemem OpenShift 4 wymagają sieci wirtualnej z dwiema pustymi podsieciami dla węzła głównego i węzła roboczego. W tym celu możesz utworzyć nową sieć wirtualną lub użyć istniejącej sieci wirtualnej.

   Utwórz nową sieć wirtualną w tej samej grupie zasobów, która została utworzona wcześniej:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   Następujące przykładowe dane wyjściowe pokazują pomyślnie utworzoną sieć wirtualną:

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

5. **[Wyłącz zasady prywatnego punktu końcowego podsieci](../private-link/disable-private-link-service-network-policy.md) w podsieci głównej.** Jest to wymagane, aby usługa mogła nawiązać połączenie z klastrem i zarządzać tym klastrem.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>Tworzenie klastra

Uruchom następujące polecenie, aby utworzyć klaster. Jeśli zdecydujesz się użyć jednej z następujących opcji, odpowiednio zmodyfikuj polecenie:
* Opcjonalnie możesz przekazać klucz tajny ściągania [systemu Red Hat,](#get-a-red-hat-pull-secret-optional) co umożliwi klastrowi dostęp do rejestrów kontenerów Red Hat wraz z dodatkową zawartością. Dodaj `--pull-secret @pull-secret.txt` argument do polecenia .
* Opcjonalnie możesz użyć [domeny niestandardowej](#prepare-a-custom-domain-for-your-cluster-optional). Dodaj `--domain foo.example.com` argument do polecenia, zastępując `foo.example.com` wartość własną domeną niestandardową.

> [!NOTE]
> Jeśli dodajesz opcjonalne argumenty do polecenia, pamiętaj o zamknięciu argumentu w poprzednim wierszu polecenia za pomocą końcowego ukośnika odwrotnego.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Po wykonaniu polecenia utworzenie klastra zwykle trwa około `az aro create` 35 minut.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie wymagań wstępnych i tworzenie wymaganej sieci wirtualnej i podsieci
> * Wdrażanie klastra

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z klastrem Azure Red Hat OpenShift klastra](tutorial-connect-cluster.md)
