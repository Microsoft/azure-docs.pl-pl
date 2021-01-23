---
title: OpenShift kontenerów platformy 3,11 w wymaganiach wstępnych platformy Azure
description: Wymagania wstępne dotyczące wdrażania OpenShift kontenera platform 3,11 na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 51f6a2ac4f524ac2a504fb8e0c3dd90ec25c9f93
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734734"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Wspólne wymagania wstępne dotyczące wdrażania OpenShift Container platform 3,11 na platformie Azure

W tym artykule opisano typowe wymagania wstępne dotyczące wdrażania platformy kontenerów OpenShift lub OKD na platformie Azure.

Instalacja programu OpenShift powoduje użycie rozwiązania ansible elementy PlayBook. Rozwiązania ansible używa Secure Shell (SSH) do łączenia się ze wszystkimi hostami klastra w celu ukończenia kroków instalacji.

Gdy rozwiązania ansible nawiązuje połączenie SSH z hostami zdalnymi, nie może wprowadzić hasła. Z tego powodu klucz prywatny nie może być skojarzony z hasłem (hasło) lub wdrożenie nie powiedzie się.

Ponieważ maszyny wirtualne są wdrażane za pomocą szablonów Azure Resource Manager, ten sam klucz publiczny jest używany do uzyskiwania dostępu do wszystkich maszyn wirtualnych. Odpowiedni klucz prywatny musi znajdować się na maszynie wirtualnej, która również wykonuje wszystkie elementy PlayBook. Aby bezpiecznie wykonać tę akcję, Magazyn kluczy platformy Azure jest używany do przekazywania klucza prywatnego do maszyny wirtualnej.

Jeśli istnieje potrzeba trwałego magazynowania dla kontenerów, wymagane są woluminy trwałe. Program OpenShift obsługuje wirtualne dyski twarde (VHD) platformy Azure dla woluminów trwałych, ale najpierw należy skonfigurować platformę Azure jako dostawcę chmury.

W tym modelu OpenShift:

- Tworzy obiekt VHD na koncie usługi Azure Storage lub dysku zarządzanym.
- Instaluje wirtualny dysk twardy na maszynie wirtualnej i sformatuje wolumin.
- Instaluje wolumin pod.

Aby ta konfiguracja działała, OpenShift musi mieć uprawnienia do wykonywania tych zadań na platformie Azure. W tym celu jest używana jednostka usługi. Nazwa główna usługi jest kontem zabezpieczeń w Azure Active Directory przyznano uprawnienia do zasobów.

Nazwa główna usługi musi mieć dostęp do kont magazynu i maszyn wirtualnych tworzących klaster. Jeśli wszystkie zasoby klastra OpenShift są wdrażane w pojedynczej grupie zasobów, można przyznać jednostce usługi uprawnienia do tej grupy zasobów.

W tym przewodniku opisano sposób tworzenia artefaktów skojarzonych z wymaganiami wstępnymi.

> [!div class="checklist"]
> * Utwórz magazyn kluczy, aby zarządzać kluczami SSH dla klastra OpenShift.
> * Utwórz nazwę główną usługi do użycia przez dostawcę chmury platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie lub kliknij przycisk **Wypróbuj** , aby użyć Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Należy użyć dedykowanej grupy zasobów, aby hostować Magazyn kluczy. Ta grupa jest oddzielona od grupy zasobów, w której wdrożone są zasoby klastra OpenShift.

Poniższy przykład tworzy grupę zasobów o nazwie *keyvaultrg* w lokalizacji *Wschodnie* :

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Utwórz magazyn kluczy, w którym mają zostać zapisane klucze SSH dla klastra za pomocą polecenia [AZ Key magazynu Create](/cli/azure/keyvault) . Nazwa magazynu kluczy musi być globalnie unikatowa i musi być włączona dla wdrożenia szablonu lub wdrożenie zakończy się niepowodzeniem z powodu błędu "KeyVaultParameterReferenceSecretRetrieveFailed".

W poniższym przykładzie tworzony jest magazyn kluczy o nazwie *Magazyn* klucza w grupie zasobów *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Tworzenie klucza SSH 
Do zabezpieczenia dostępu do klastra OpenShift jest wymagany klucz SSH. Utwórz parę kluczy SSH przy użyciu `ssh-keygen` polecenia (w systemie Linux lub macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Para kluczy SSH nie może mieć hasła lub hasło.

Aby uzyskać więcej informacji na temat kluczy SSH w systemie Windows, zobacz [jak utworzyć klucze SSH w systemie Windows](./ssh-from-windows.md). Pamiętaj, aby wyeksportować klucz prywatny w formacie OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Przechowuj klucz prywatny SSH w Azure Key Vault
Wdrożenie OpenShift korzysta z utworzonego klucza SSH w celu zabezpieczenia dostępu do głównego serwera OpenShift. Aby umożliwić wdrożenie w celu bezpiecznego pobrania klucza SSH, Zapisz klucz w Key Vault przy użyciu następującego polecenia:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 
OpenShift komunikuje się z platformą Azure przy użyciu nazwy użytkownika i hasła lub nazwy głównej usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń, której można używać z aplikacjami, usługami i narzędziami automatyzacji, takimi jak OpenShift. Użytkownik kontroluje i definiuje uprawnienia do działania, które może wykonywać jednostka usługi na platformie Azure. Najlepszym rozwiązaniem jest zakres uprawnień nazwy głównej usługi do określonych grup zasobów, a nie całej subskrypcji.

Utwórz nazwę główną usługi za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp) i Wyprowadź wymagane przez OpenShift poświadczenia.

Poniższy przykład tworzy nazwę główną usługi i przypisuje uprawnienia współautora do grupy zasobów o nazwie *openshiftrg*.

Najpierw utwórz grupę zasobów o nazwie *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Utwórz nazwę główną usługi:

```azurecli
az group show --name openshiftrg --query id
```

Zapisz dane wyjściowe polecenia i Użyj zamiast $scope w następnym poleceniu

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Zanotuj Właściwość appId i hasło zwrócone przez polecenie:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Pamiętaj o zapisaniu bezpiecznego hasła, ponieważ nie będzie można ponownie pobrać tego hasła.

Aby uzyskać więcej informacji o jednostkach usługi, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Wymagania wstępne dotyczące Menedżer zasobów szablonu

Należy utworzyć wpisy tajne dla prywatnego klucza SSH (**sshPrivateKey**), klucza tajnego klienta usługi Azure AD (**aadClientSecret**), hasła administratora OpenShift (**OpenshiftPassword**) oraz hasła Menedżera subskrypcji Red Hat lub klucza aktywacji (**rhsmPasswordOrActivationKey**).  Ponadto jeśli są używane niestandardowe certyfikaty TLS/SSL, należy utworzyć sześć dodatkowych kluczy tajnych — **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile** i **masterkeyfile**.  Te parametry zostaną omówione bardziej szczegółowo.

Szablon odwołuje się do określonych nazw wpisów tajnych, więc **należy** użyć pogrubionych nazw wymienionych powyżej (z uwzględnieniem wielkości liter).

### <a name="custom-certificates"></a>Certyfikaty niestandardowe

Domyślnie szablon wdraża klaster OpenShift przy użyciu certyfikatów z podpisem własnym dla konsoli sieci Web OpenShift i domeny routingu. Jeśli chcesz użyć niestandardowych certyfikatów TLS/SSL, ustaw wartość "routingCertType" na "Custom" i "masterCertType" na "Custom".  W przypadku certyfikatów będziesz potrzebować plików urzędu certyfikacji, certyfikatu i kluczy w formacie PEM.  Istnieje możliwość użycia certyfikatów niestandardowych dla jednego, ale nie drugiego.

Te pliki muszą być przechowywane w Key Vault Secret.  Użyj tego samego Key Vault jak używany dla klucza prywatnego.  Zamiast wymagać 6 dodatkowych danych wejściowych dla nazw wpisów tajnych, szablon jest zakodowany do używania określonych nazw wpisów tajnych dla każdego z plików certyfikatu TLS/SSL.  Przechowuj dane certyfikatu, korzystając z informacji podanych w poniższej tabeli.

| Nazwa wpisu tajnego      | Plik certyfikatu   |
|------------------|--------------------|
| mastercafile     | główny plik urzędu certyfikacji     |
| mastercertfile   | główny plik certyfikatu   |
| masterkeyfile    | plik klucza głównego    |
| routingcafile    | Routing pliku urzędu certyfikacji    |
| routingcertfile  | plik certyfikatu routingu  |
| routingkeyfile   | plik klucza routingu   |

Utwórz wpisy tajne przy użyciu interfejsu wiersza polecenia platformy Azure. Poniżej znajduje się przykład.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono następujące tematy:
> [!div class="checklist"]
> * Utwórz magazyn kluczy, aby zarządzać kluczami SSH dla klastra OpenShift.
> * Utwórz nazwę główną usługi do użycia przez dostawcę rozwiązań w chmurze platformy Azure.

Następnie wdróż klaster OpenShift:

- [Wdróż platformę kontenera OpenShift](./openshift-container-platform-3x.md)
- [Wdróż platformę kontenera OpenShift Self-Managed ofertę w witrynie Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
