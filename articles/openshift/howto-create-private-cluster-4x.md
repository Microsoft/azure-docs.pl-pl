---
title: Tworzenie klastra prywatnego usługi Azure Red Hat OpenShift 4
description: Dowiedz się, jak utworzyć prywatny klaster usługi Azure Red Hat OpenShift z systemem OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: georgewallace
ms.author: gwallace
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: eedd796e3223c6cbcd0844cc4e814a35172d6c7e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078531"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Tworzenie klastra prywatnego usługi Azure Red Hat OpenShift 4

W tym artykule opisano przygotowanie środowiska do tworzenia prywatnych klastrów usługi Azure Red Hat OpenShift z systemem OpenShift 4. Omawiane tematy:

> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra z punktem końcowym prywatnego serwera interfejsu API i prywatnym kontrolerem danych przychodzących

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Przed rozpoczęciem

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

### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz wpis tajny Red Hat (opcjonalnie)

Klucz tajny w systemie Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat oraz dodatkową zawartość. Ten krok jest opcjonalny, ale zalecany.

1. **[Przejdź do portalu Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) i zaloguj się.**

   Musisz zalogować się do swojego konta Red Hat lub utworzyć nowe konto Red Hat przy użyciu firmowej poczty e-mail i zaakceptować warunki i postanowienia.

2. **Kliknij pozycję Pobierz klucz tajny ściągania.**

Przechowuj zapisany `pull-secret.txt` plik w bezpiecznym miejscu — będzie on używany podczas tworzenia klastra.

Po uruchomieniu `az aro create` polecenia można odwoływać się do klucza tajnego ściągnięcia przy użyciu `--pull-secret @pull-secret.txt` parametru. Wykonaj `az aro create` z katalogu, w którym zapisano `pull-secret.txt` plik. W przeciwnym razie Zamień `@pull-secret.txt` na `@<path-to-my-pull-secret-file` .

Jeśli kopiujesz klucz tajny ściągania lub odwołujesz się do niego w innych skryptach, klucz tajny ściągania powinien być sformatowany jako prawidłowy ciąg JSON.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Następnie utworzysz sieć wirtualną zawierającą dwie puste podsieci.

1. **Ustaw następujące zmienne.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Tworzenie grupy zasobów**

    Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja wskazuje, gdzie są przechowywane metadane grupy zasobów, a także czy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów za pomocą polecenia [AZ Group Create] [AZ-Group-Create].

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

    Utwórz nową sieć wirtualną w tej samej grupie zasobów, która została utworzona wcześniej.

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

5. **[Wyłącz zasady prywatnego punktu końcowego podsieci](../private-link/disable-private-link-service-network-policy.md) w podsieci głównej.** Jest to wymagane, aby mieć możliwość nawiązywania połączenia z klastrem i zarządzania nim.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Tworzenie klastra

Uruchom następujące polecenie, aby utworzyć klaster. Opcjonalnie możesz [przekazać swoje hasło w systemie Red Hat](#get-a-red-hat-pull-secret-optional) , które umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat oraz dodatkowej zawartości.

>[!NOTE]
> Jeśli kopiujesz/wklejasz polecenia i używasz jednego z parametrów opcjonalnych, pamiętaj o usunięciu początkowych hasztagów i końcowym tekście komentarza. Ponadto Zamknij argument w poprzednim wierszu polecenia z końcowym ukośnikiem odwrotnym.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Po wykonaniu `az aro create` polecenia zwykle trwa około 35 minut na utworzenie klastra.

>[!IMPORTANT]
> Jeśli zdecydujesz się określić domenę niestandardową, na przykład **foo.example.com**, konsola OpenShift będzie dostępna pod adresem URL, takim jak `https://console-openshift-console.apps.foo.example.com` , a nie wbudowaną domeną `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .
>
> Domyślnie OpenShift używa certyfikatów z podpisem własnym dla wszystkich tras utworzonych w usłudze `*.apps.<random>.<location>.aroapp.io` .  W przypadku wybrania opcji niestandardowy serwer DNS po nawiązaniu połączenia z klastrem należy skorzystać z dokumentacji OpenShift w celu [skonfigurowania niestandardowego urzędu certyfikacji dla kontrolera](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) usług przychodzących i [niestandardowego urzędu certyfikacji dla serwera interfejsu API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Nawiązywanie połączenia z klastrem prywatnym

Możesz zalogować się do klastra przy użyciu `kubeadmin` użytkownika.  Uruchom następujące polecenie, aby znaleźć hasło `kubeadmin` użytkownika.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Następujące przykładowe dane wyjściowe pokazują, że hasło będzie w `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Adres URL konsoli klastra można znaleźć, uruchamiając następujące polecenie, które będzie wyglądać następująco: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Aby można było nawiązać połączenie z prywatnym klastrem Red Hat OpenShift platformy Azure, należy wykonać następujący krok z hosta, który znajduje się w utworzonym Virtual Network lub w Virtual Network połączonym z Virtual Networkm, w [którym został](../virtual-network/virtual-network-peering-overview.md) wdrożony klaster.

Uruchom w przeglądarce adres URL konsoli i zaloguj się przy użyciu `kubeadmin` poświadczeń.

![Ekran logowania OpenShift na platformie Azure Red Hat](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instalowanie interfejsu wiersza polecenia OpenShift

Po zalogowaniu się do konsoli sieci Web OpenShift kliknij pozycję **?** w prawym górnym rogu, a następnie w **narzędziach wiersza polecenia**. Pobierz wydanie odpowiednie dla Twojej maszyny.

![Ekran logowania OpenShift na platformie Azure Red Hat](media/aro4-download-cli.png)

Możesz również pobrać najnowszą wersję interfejsu wiersza polecenia odpowiednią dla komputera z programu <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

## <a name="connect-using-the-openshift-cli"></a>Nawiązywanie połączenia przy użyciu interfejsu wiersza polecenia OpenShift

Pobierz adres serwera interfejsu API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Aby można było nawiązać połączenie z prywatnym klastrem Red Hat OpenShift platformy Azure, należy wykonać następujący krok z hosta, który znajduje się w utworzonym Virtual Network lub w Virtual Network połączonym z Virtual Networkm, w [którym został](../virtual-network/virtual-network-peering-overview.md) wdrożony klaster.

Zaloguj się do serwera interfejsu API klastra OpenShift przy użyciu następującego polecenia. Zamień na **\<kubeadmin password>** hasło, które zostało właśnie pobrane.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule został wdrożony klaster Red Hat OpenShift systemu Azure z systemem OpenShift 4. W tym samouczku omówiono:

> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra
> * Nawiązywanie połączenia z klastrem przy użyciu `kubeadmin` użytkownika

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować klaster pod kątem uwierzytelniania przy użyciu Azure Active Directory.


* [Konfigurowanie uwierzytelniania za pomocą Azure Active Directory przy użyciu wiersza polecenia](configure-azure-ad-cli.md)


* [Konfigurowanie uwierzytelniania za pomocą Azure Active Directory przy użyciu konsoli sieci Web Azure Portal i OpenShift](configure-azure-ad-cli.md)