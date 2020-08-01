---
title: Samouczek — Tworzenie klastra usługi Azure Red Hat OpenShift 4
description: Dowiedz się, jak utworzyć Microsoft Azure klaster Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: be04b690add70468335ac694e3be54fa55a94249
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475655"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: Tworzenie klastra usługi Azure Red Hat OpenShift 4

W tym samouczku część jednej z trzech zostanie przygotowana do utworzenia klastra usługi Azure Red Hat OpenShift z systemem OpenShift 4 i utworzenia klastra. Poznasz następujące czynności:
> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.75 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Sprawdzanie uprawnień

Aby utworzyć klaster usługi Azure Red Hat OpenShift, sprawdź następujące uprawnienia dotyczące subskrypcji platformy Azure, użytkownika Azure Active Directory lub nazwy głównej usługi:

|Uprawnienia|Grupa zasobów zawierająca sieć wirtualną|Wykonywane przez użytkownika`az aro create`|Nazwa główna usługi przebiegła jako`–client-id`|
|----|:----:|:----:|:----:|
|**Administrator dostępu użytkowników**|X|X| |
|**Współautor**|X|X|X|

### <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Następnie musisz zarejestrować `Microsoft.RedHatOpenShift` dostawcę zasobów w ramach subskrypcji.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz wpis tajny Red Hat (opcjonalnie)

Klucz tajny w systemie Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat oraz dodatkową zawartość. Ten krok jest opcjonalny, ale zalecany.

1. **[Przejdź do portalu Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) i zaloguj się.**

   Musisz zalogować się do swojego konta Red Hat lub utworzyć nowe konto Red Hat przy użyciu firmowej poczty e-mail i zaakceptować warunki i postanowienia.

2. **Kliknij pozycję Pobierz klucz tajny ściągania.**

Przechowuj zapisany `pull-secret.txt` plik w bezpiecznym miejscu — będzie on używany podczas tworzenia klastra.

Po uruchomieniu `az aro create` polecenia można odwoływać się do klucza tajnego ściągnięcia przy użyciu `--pull-secret @pull-secret.txt` parametru. Wykonaj `az aro create` z katalogu, w którym zapisano `pull-secret.txt` plik. W przeciwnym razie Zamień `@pull-secret.txt` na `@<path-to-my-pull-secret-file>` .

Jeśli kopiujesz klucz tajny ściągania lub odwołujesz się do niego w innych skryptach, klucz tajny ściągania powinien być sformatowany jako prawidłowy ciąg JSON.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Przygotuj domenę niestandardową dla klastra (opcjonalnie)

Podczas uruchamiania `az aro create` polecenia można określić domenę niestandardową dla klastra przy użyciu `--domain foo.example.com` parametru.

Jeśli podano domenę niestandardową dla klastra, należy zwrócić uwagę na następujące kwestie:

* Po utworzeniu klastra należy utworzyć 2 rekordy A systemu DNS na serwerze DNS dla `--domain` określonych:
    * **interfejs API** — wskazanie serwera interfejsu API
    * ** \* aplikacje** — wskazuje na ruch przychodzący
    * Pobierz te wartości, wykonując następujące polecenie: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* Konsola OpenShift będzie dostępna pod adresem URL, takim jak `https://console-openshift-console.apps.foo.example.com` , a nie wbudowaną domeną `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Domyślnie OpenShift używa certyfikatów z podpisem własnym dla wszystkich tras utworzonych w usłudze `*.apps.<random>.<location>.aroapp.io` .  Jeśli zdecydujesz się używać niestandardowej usługi DNS po nawiązaniu połączenia z klastrem, musisz postępować zgodnie z dokumentacją OpenShift w celu [skonfigurowania niestandardowego urzędu certyfikacji dla kontrolera](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) usług przychodzących i [niestandardowego urzędu certyfikacji dla serwera interfejsu API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Następnie utworzysz sieć wirtualną zawierającą dwie puste podsieci.

1. **Ustaw następujące zmienne w środowisku powłoki, w którym będą wykonywane `az` polecenia.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Utwórz grupę zasobów.**

    Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja wskazuje, gdzie są przechowywane metadane grupy zasobów, a także czy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów przy użyciu polecenia [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Utwórz sieć wirtualną.**

    Klastry usługi Azure Red Hat OpenShift z systemem OpenShift 4 wymagają sieci wirtualnej z dwiema pustymi podsieciami dla węzłów głównych i procesów roboczych.

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

5. **[Wyłącz zasady prywatnego punktu końcowego podsieci](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) w podsieci głównej.** Jest to wymagane, aby mieć możliwość nawiązywania połączenia z klastrem i zarządzania nim.

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
