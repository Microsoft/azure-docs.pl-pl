---
title: OpenShift Container Platform 3.11 w wymaganiach wstępnych platformy Azure
description: Wymagania wstępne dotyczące wdrażania platformy kontenerów OpenShift 3.11 na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 26b190515819378309c2b0705efdbc349ecccbe2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759512"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Typowe wymagania wstępne dotyczące wdrażania platformy kontenerowej OpenShift 3.11 na platformie Azure

W tym artykule opisano typowe wymagania wstępne dotyczące wdrażania platformy kontenerów OpenShift lub narzędzia OKD na platformie Azure.

Instalacja OpenShift wykorzystuje czytelne podręczniki. Ansible używa secure shell (SSH) do łączenia się ze wszystkimi hostami klastra w celu ukończenia kroków instalacji.

Gdy ansible nawiązuje połączenie SSH z hostami zdalnymi, nie można wprowadzić hasła. Z tego powodu klucz prywatny nie może mieć skojarzonego z nim hasła (hasła) lub wdrożenia nie powiedzie się.

Ponieważ maszyny wirtualne (maszyny wirtualne) wdrażają za pomocą szablonów usługi Azure Resource Manager, ten sam klucz publiczny jest używany do dostępu do wszystkich maszyn wirtualnych. Odpowiedni klucz prywatny musi znajdować się na maszynie Wirtualnej, która wykonuje wszystkie podręczniki, jak również. Aby bezpiecznie wykonać tę akcję, magazyn kluczy platformy Azure jest używany do przekazywania klucza prywatnego do maszyny Wirtualnej.

Jeśli istnieje potrzeba trwałego przechowywania kontenerów, wymagane są woluminy trwałe. OpenShift obsługuje wirtualne dyski twarde platformy Azure (VHD) dla woluminów trwałych, ale platforma Azure musi najpierw zostać skonfigurowana jako dostawca chmury.

W tym modelu OpenShift:

- Tworzy obiekt VHD na koncie magazynu platformy Azure lub dysku zarządzanym.
- Montuje dysk VHD na maszynie wirtualnej i formatuje wolumin.
- Montuje głośność do zasobnika.

Aby ta konfiguracja działała, program OpenShift potrzebuje uprawnień do wykonywania tych zadań na platformie Azure. W tym celu używany jest podmiot usługi. Podmiot usługi jest kontem zabezpieczeń w usłudze Azure Active Directory, któremu przyznano uprawnienia do zasobów.

Podmiot zabezpieczeń usługi musi mieć dostęp do kont magazynu i maszyn wirtualnych, które tworzą klaster. Jeśli wszystkie zasoby klastra OpenShift zostaną wdrożone w jednej grupie zasobów, podmiotowi usługi można przyznać uprawnienia do tej grupy zasobów.

W tym przewodniku opisano sposób tworzenia artefaktów skojarzonych z wymaganiami wstępnymi.

> [!div class="checklist"]
> * Utwórz magazyn kluczy do zarządzania kluczami SSH dla klastra OpenShift.
> * Utwórz jednostkę usługi do użytku przez dostawcę chmury Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie lub kliknij przycisk **Wypróbuj,** aby użyć usługi Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Do hosta magazynu kluczy należy użyć dedykowanej grupy zasobów. Ta grupa jest oddzielona od grupy zasobów, do której wdrażają zasoby klastra OpenShift.

Poniższy przykład tworzy grupę zasobów o nazwie *keyvaultrg* w lokalizacji *eastus:*

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Utwórz magazyn kluczy do przechowywania kluczy SSH dla klastra za pomocą polecenia [az keyvault create.](/cli/azure/keyvault) Nazwa magazynu kluczy musi być unikatowa globalnie i musi być włączona dla wdrożenia szablonu lub wdrożenie zakończy się niepowodzeniem z "KeyVaultParameterReferenceSecretretretrieveFailed" błąd.

Poniższy przykład tworzy magazyn kluczy o nazwie *keyvault* w grupie zasobów *keyvaultrg:*

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Tworzenie klucza SSH 
Klucz SSH jest potrzebny do zabezpieczenia dostępu do klastra OpenShift. Utwórz parę kluczy SSH za pomocą `ssh-keygen` polecenia (w systemie Linux lub macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Twoja para kluczy SSH nie może mieć hasła / hasła.

Aby uzyskać więcej informacji na temat kluczy SSH w systemie Windows, zobacz [Jak tworzyć klucze SSH w systemie Windows](/azure/virtual-machines/linux/ssh-from-windows). Pamiętaj, aby wyeksportować klucz prywatny w formacie OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Przechowywanie klucza prywatnego SSH w usłudze Azure Key Vault
Wdrożenie OpenShift używa klucza SSH utworzonego w celu zabezpieczenia dostępu do wzorca OpenShift. Aby umożliwić wdrożenie w celu bezpiecznego pobrania klucza SSH, zapisz klucz w magazynie kluczy za pomocą następującego polecenia:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 
OpenShift komunikuje się z platformą Azure przy użyciu nazwy użytkownika i hasła lub jednostki usługi. Podmiot usługi platformy Azure to tożsamość zabezpieczeń, której można używać z aplikacjami, usługami i narzędziami automatyzacji, takimi jak OpenShift. Można kontrolować i zdefiniować uprawnienia, do których operacji podmiot usługi można wykonać na platformie Azure. Najlepiej jest zakres uprawnień jednostki usługi do określonych grup zasobów, a nie całej subskrypcji.

Utwórz jednostkę usługi z [az ad sp create-for-rbac](/cli/azure/ad/sp) i wyśmiaj poświadczenia, których potrzebuje OpenShift.

Poniższy przykład tworzy jednostkę usługi i przypisuje jej uprawnienia współautora do grupy zasobów o nazwie *openshiftrg*.

Najpierw utwórz grupę zasobów o nazwie *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Utwórz jednostkę usługi:

```azurecli
az group show --name openshiftrg --query id
```

Zapisz dane wyjściowe polecenia i użyj zamiast $scope w następnym poleceniu

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Zanotuj właściwość appId i hasło zwrócone z polecenia:

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
 > Pamiętaj, aby zapisać bezpieczne hasło, ponieważ nie będzie możliwe ponowne pobranie tego hasła.

Aby uzyskać więcej informacji na temat podmiotów świadczących usługi, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Wymagania wstępne mające zastosowanie tylko do szablonu Menedżera zasobów

Wpisy tajne będą musiały zostać utworzone dla klucza prywatnego SSH (**sshPrivateKey**), klucz tajny klienta usługi Azure AD **(aadClientSecret),** hasło administratora OpenShift **(openshiftPassword)** i hasło lub klucz aktywacyjny Red Hat Subscription Manager (**rhsmPasswordOrActivationKey**).  Ponadto, jeśli używane są niestandardowe certyfikaty TLS/SSL, konieczne będzie utworzenie sześciu dodatkowych wpisów tajnych - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**i **masterkeyfile**.  Parametry te zostaną wyjaśnione bardziej szczegółowo.

Szablon odwołuje się do określonych nazw tajnych, więc **należy** użyć pogrubionych nazw wymienionych powyżej (z uwzględnieniem wielkości liter).

### <a name="custom-certificates"></a>Certyfikaty niestandardowe

Domyślnie szablon wdraża klaster OpenShift przy użyciu certyfikatów z podpisem własnym dla konsoli internetowej OpenShift i domeny routingu. Jeśli chcesz używać niestandardowych certyfikatów TLS/SSL, ustaw "routingCertType" na "custom" i "masterCertType" na "custom".  Dla certyfikatów potrzebne będą pliki urzędu certyfikacji, certyfikatów i kluczy w formacie pem.  Istnieje możliwość użycia certyfikatów niestandardowych dla jednego, ale nie dla drugiego.

Musisz przechowywać te pliki w wpisach tajnych usługi Key Vault.  Użyj tego samego magazynu kluczy, co używany dla klucza prywatnego.  Zamiast wymagać 6 dodatkowych danych wejściowych dla tajnych nazw, szablon jest zakodowany na czas, aby używać określonych tajnych nazw dla każdego z plików certyfikatów TLS/SSL.  Przechowuj dane certyfikatu przy użyciu informacji z poniższej tabeli.

| Tajna nazwa      | Plik certyfikatu   |
|------------------|--------------------|
| mastercafile (ruszt mastercafile)     | główny plik urzędu certyfikacji     |
| mastercertfile   | główny plik CERT   |
| masterkeyfile (plik masterkeyfile)    | główny plik klucza    |
| routingucafile    | routingu pliku urzędu certyfikacji    |
| plik routingu  | routing pliku CERT  |
| plik klucza routingu   | routing plik klucza   |

Tworzenie wpisów tajnych przy użyciu interfejsu wiersza polecenia platformy Azure. Poniżej znajduje się przykład.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Następne kroki

W tym artykule ociepliłem się o następujące tematy:
> [!div class="checklist"]
> * Utwórz magazyn kluczy do zarządzania kluczami SSH dla klastra OpenShift.
> * Utwórz jednostkę usługi do użytku przez dostawcę rozwiązań w chmurze platformy Azure.

Następnie wdrożyć klaster OpenShift:

- [Wdrażanie platformy kontenerowej OpenShift](./openshift-container-platform-3x.md)
- [Wdrażanie oferty platformy kontenerowej OpenShift self-managed Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
