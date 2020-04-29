---
title: Tworzenie klastra usługi Azure Red Hat OpenShift 4,3 | Microsoft Docs
description: Tworzenie klastra przy użyciu usługi Azure Red Hat OpenShift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398889"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Tworzenie klastra usługi Azure Red Hat OpenShift 4,3, uzyskiwanie do niego dostępu i zarządzanie nim

> [!IMPORTANT]
> Należy pamiętać, że usługa Azure Red Hat OpenShift 4,3 jest obecnie dostępna tylko w prywatnej wersji zapoznawczej w regionach Wschodnie stany USA i Wschodnie stany USA 2. Zaakceptowanie prywatnej wersji zapoznawczej odbywa się tylko przez zaproszenie. Przed podjęciem próby włączenia tej funkcji upewnij się, że subskrypcja została zarejestrowana: [Azure Red Hat OpenShift — Rejestracja w prywatnej wersji zapoznawczej](https://aka.ms/aro-preview-register)

> [!NOTE]
> Funkcje w wersji zapoznawczej są samoobsługowe i udostępniane jako dostępne i są wykluczone z umowy dotyczącej poziomu usług (SLA) i ograniczonej rękojmi. W związku z tym funkcje te nie są przeznaczone do użytku produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć klaster usługi Azure Red Hat OpenShift 4,3, potrzebne są następujące elementy:

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.72 lub nowszej
  
- Rozszerzenie "AZ ARO"

- Sieć wirtualna zawierająca dwie puste podsieci, z których każda nie jest podłączona do sieciowej grupy zabezpieczeń.  Klaster zostanie wdrożony w tych podsieciach.

- Aplikacja `az aro create` do obsługi klastra (identyfikator klienta i klucz tajny) oraz jednostka usługi lub wystarczających uprawnień w usłudze AAD do automatycznego tworzenia aplikacji usługi AAD i jej nazwy głównej.

- Nazwa główna usługi RP i jednostka usługi klastra muszą mieć rolę współautor w sieci wirtualnej klastra.  Jeśli masz rolę "Administrator dostępu użytkowników" w sieci wirtualnej, `az aro create` program automatycznie skonfiguruje przypisania ról.

### <a name="install-the-az-aro-extension"></a>Zainstaluj rozszerzenie "AZ ARO"
`az aro` Rozszerzenie umożliwia tworzenie i usuwanie klastrów usługi Azure Red Hat OpenShift oraz uzyskiwanie do nich dostępu bezpośrednio z wiersza polecenia przy użyciu interfejsu użytkownika platformy Azure.

> [!Note] 
> `az aro` Rozszerzenie jest w wersji zapoznawczej. Może zostać zmieniona lub usunięta w przyszłej wersji.
> Aby skorzystać z wersji zapoznawczej `az aro` rozszerzenia, należy zarejestrować dostawcę `Microsoft.RedHatOpenShift` zasobów.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Loguje się do platformy Azure.

   ```console
   az login
   ```

2. Uruchom następujące polecenie, aby zainstalować `az aro` rozszerzenie:

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

### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz wpis tajny Red Hat (opcjonalnie)

Klucz tajny z systemem Red Hat umożliwia klastrowi dostęp do rejestrów kontenerów Red Hat i dodatkowej zawartości. Używanie klucza tajnego ściągania jest opcjonalne, ale zalecane.

Aby uzyskać klucz tajny ściągania:

1. Przejdź do witryny https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Zaloguj się do swojego konta Red Hat lub Utwórz nowe konto Red Hat przy użyciu firmowej poczty e-mail. Zaakceptuj warunki i postanowienia.
1. Wybierz pozycję **Pobierz klucz tajny**.

Zapisz plik *pull-Secret. txt* w bezpiecznym miejscu; Ten plik będzie używany podczas tworzenia klastra.

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

5. Wyłączenie zasad sieciowych dla usługi link prywatny w sieci wirtualnej i podsieciach. Jest to wymaganie do uzyskiwania dostępu do klastra i zarządzania nim za pomocą usługi ARO.

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
> Utworzenie klastra zwykle trwa około 35 minut.

## <a name="access-the-cluster-console"></a>Dostęp do konsoli klastra

Adres URL konsoli klastra (formularza `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) można znaleźć w obszarze zasobów klastra usługi Azure Red Hat OpenShift 4,3. Uruchom następujące polecenie, aby wyświetlić zasób:

```console
az aro list -o table
```

Możesz zalogować się do klastra przy użyciu `kubeadmin` użytkownika.  Uruchom następujące polecenie, aby znaleźć hasło dla `kubeadmin` użytkownika:

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
