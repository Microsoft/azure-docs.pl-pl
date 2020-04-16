---
title: Tworzenie klastra Usługi Azure Red Hat OpenShift 4.3 | Dokumenty firmy Microsoft
description: Tworzenie klastra za pomocą usługi Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, czerwony kapelusz, cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398889"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Tworzenie klastra usługi Azure Red Hat OpenShift 4.3, uzyskiwanie do nich dostępu i zarządzanie nim

> [!IMPORTANT]
> Należy pamiętać, że usługa Azure Red Hat OpenShift 4.3 jest obecnie dostępna tylko w prywatnej wersji zapoznawczej we wschodnich stanach USA i wschodnich stanach USA 2. Akceptacja prywatnej wersji zapoznawczej odbywa się wyłącznie na zaproszenie. Przed podjęciem próby włączenia tej funkcji należy zarejestrować subskrypcję: [rejestracja prywatna w wersji zapoznawczej Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Funkcje w wersji zapoznawczej są samoobsługowe i są dostępne w stanie dostępności i są wyłączone z umowy dotyczącej poziomu usług (SLA) i ograniczonej gwarancji. W związku z tym funkcje nie są przeznaczone do użytku produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć klaster Azure Red Hat OpenShift 4.3, musisz wykonać następujące czynności:

- Interfejsu wiersza polecenia platformy Azure w wersji 2.0.72 lub większej
  
- Rozszerzenie 'az aro'

- Sieć wirtualna zawierająca dwie puste podsieci, z których każda nie jest podłączona do sieciowej grupy zabezpieczeń.  Klaster zostanie wdrożony w tych podsieciach.

- Aplikacja AAD klastra (identyfikator klienta i klucz tajny) i jednostki `az aro create` usługi lub wystarczające uprawnienia usługi AAD do utworzenia aplikacji i jednostki usługi AAD dla Ciebie automatycznie.

- Podmiot zabezpieczeń usługi RP i jednostki usługi klastrowania musi mieć rolę współautora w sieci wirtualnej klastra.  Jeśli w sieci wirtualnej masz rolę "Administrator `az aro create` dostępu użytkownika", automatycznie skonfigurujesz przypisania ról.

### <a name="install-the-az-aro-extension"></a>Zainstaluj rozszerzenie 'az aro'
Rozszerzenie `az aro` umożliwia tworzenie, dostęp i usuwanie klastrów Azure Red Hat OpenShift bezpośrednio z wiersza polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!Note] 
> Rozszerzenie `az aro` jest currenty w wersji zapoznawczej. Może zostać zmieniony lub usunięty w przyszłej wersji.
> Aby wyrazić zgodę na podgląd `az aro` rozszerzenia, musisz zarejestrować dostawcę `Microsoft.RedHatOpenShift` zasobów.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Loguje się do platformy Azure.

   ```console
   az login
   ```

2. Uruchom następujące polecenie, `az aro` aby zainstalować rozszerzenie:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Sprawdź, czy rozszerzenie ARO jest zarejestrowane.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Uzyskaj sekret ciągnięcia Czerwonego Kapelusza (opcjonalnie)

Tajny klucz ściągania Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat i dodatkowej zawartości. Użycie klucza tajnego ściągania jest opcjonalne, ale zalecane.

Aby uzyskać sekret ciągnięcia:

1. Przejdź do pozycji https://cloud.redhat.com/openshift/install/azure/aro-provisioned (Plik > Nowy > Inny).
1. Zaloguj się na swoje konto Red Hat lub utwórz nowe konto Red Hat za pomocą firmowego adresu e-mail; zaakceptować warunki.
1. Wybierz **pobierz klucz tajny**.

Zapisz plik *pull-secret.txt* w bezpiecznym miejscu; będzie używany plik przy każdym utworzeniu klastra.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Tworzenie sieci wirtualnej zawierającej dwie puste podsieci

Wykonaj następujące kroki, aby utworzyć sieć wirtualną zawierającą dwie puste podsieci.

1. Ustaw następujące zmienne.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Utwórz grupę zasobów dla klastra.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Utwórz sieć wirtualną.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Dodaj dwie puste podsieci do sieci wirtualnej.

   ```console
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Wyłącz zasady sieci dla usługi private link w sieci wirtualnej i podsieciach. Jest to wymagane dla usługi ARO dostępu i zarządzania klastrem.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Tworzenie klastra

Uruchom następujące polecenie, aby utworzyć klaster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> Zwykle trwa około 35 minut, aby utworzyć klaster.

## <a name="access-the-cluster-console"></a>Uzyskiwanie dostępu do konsoli klastra

Adres URL konsoli klastra (formularza) `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`można znaleźć w obszarze zasobów klastra Azure Red Hat OpenShift 4.3. Uruchom następujące polecenie, aby wyświetlić zasób:

```console
az aro list -o table
```

Można zalogować się do `kubeadmin` klastra przy użyciu użytkownika.  Uruchom następujące polecenie, aby znaleźć `kubeadmin` hasło dla użytkownika:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Usuwanie klastra

Uruchom następujące polecenie, aby usunąć klaster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
