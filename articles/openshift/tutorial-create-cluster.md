---
title: Samouczek — Tworzenie klastra usługi Azure Red Hat OpenShift 4
description: Dowiedz się, jak utworzyć Microsoft Azure klaster Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509724"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Samouczek: Tworzenie klastra usługi Azure Red Hat OpenShift 4

W tym samouczku część jednej z trzech zostanie przygotowana do utworzenia klastra usługi Azure Red Hat OpenShift z systemem OpenShift 4 i utworzenia klastra. Poznasz następujące czynności:
> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.75 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="install-the-az-aro-extension"></a>Zainstaluj `az aro` rozszerzenie
`az aro` Rozszerzenie umożliwia tworzenie i usuwanie klastrów usługi Azure Red Hat OpenShift oraz uzyskiwanie do nich dostępu bezpośrednio z wiersza polecenia przy użyciu interfejsu użytkownika platformy Azure.

Uruchom następujące polecenie, aby zainstalować `az aro` rozszerzenie.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Jeśli rozszerzenie jest już zainstalowane, można je zaktualizować, uruchamiając następujące polecenie.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Następnie musisz zarejestrować dostawcę `Microsoft.RedHatOpenShift` zasobów w ramach subskrypcji.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Sprawdź, czy rozszerzenie jest zarejestrowane.

```azurecli-interactive
az -v
```

  Powinno zostać wyświetlone dane wyjściowe podobne do poniższego.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz wpis tajny Red Hat (opcjonalnie)

Klucz tajny w systemie Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat oraz dodatkową zawartość. Ten krok jest opcjonalny, ale zalecany.

Aby uzyskać klucz tajny, przejdź do https://cloud.redhat.com/openshift/install/azure/aro-provisioned węzła i kliknij pozycję *Pobierz klucz tajny*.

Musisz zalogować się do swojego konta Red Hat lub utworzyć nowe konto Red Hat przy użyciu firmowej poczty e-mail i zaakceptować warunki i postanowienia.

Przechowuj zapisany `pull-secret.txt` plik w bezpiecznym miejscu — będzie on używany podczas tworzenia klastra.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Następnie utworzysz sieć wirtualną zawierającą dwie puste podsieci.

1. **Ustaw następujące zmienne.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
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

5. **[Wyłącz zasady prywatnego punktu końcowego podsieci](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) w podsieci głównej.** Jest to wymagane, aby mieć możliwość nawiązywania połączenia z klastrem i zarządzania nim.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Tworzenie klastra

Uruchom następujące polecenie, aby utworzyć klaster. Opcjonalnie można przekazać klucz tajny ściągania, który umożliwia klastrowi dostęp do rejestrów kontenera Red Hat oraz dodatkową zawartość. Uzyskaj dostęp do hasła ściągnięcia, przechodząc do [Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) , a następnie klikając pozycję **Kopiuj ściągnięcie klucza tajnego**.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> Utworzenie klastra zwykle trwa około 35 minut.

>[!IMPORTANT]
> Jeśli zdecydujesz się określić domenę niestandardową, na przykład **foo.example.com**, konsola OpenShift będzie dostępna pod adresem URL, takim jak `https://console-openshift-console.apps.foo.example.com`, a nie wbudowaną domeną. `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> Domyślnie OpenShift używa certyfikatów z podpisem własnym dla wszystkich tras utworzonych w usłudze `*.apps.<random>.<location>.aroapp.io`.  Jeśli zdecydujesz się używać niestandardowej usługi DNS po nawiązaniu połączenia z klastrem, musisz postępować zgodnie z dokumentacją OpenShift w celu [skonfigurowania niestandardowego urzędu certyfikacji dla kontrolera](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) usług przychodzących i [niestandardowego urzędu certyfikacji dla serwera interfejsu API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i utwórz wymaganą sieć wirtualną i podsieci
> * Wdrażanie klastra

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z klastrem usługi Azure Red Hat OpenShift](tutorial-connect-cluster.md)
