---
title: Szyfrowanie danych wdrożenia
description: Dowiedz się więcej na temat szyfrowania danych utrwalonych dla zasobów wystąpienia kontenera oraz sposobu szyfrowania danych przy użyciu klucza zarządzanego przez klienta
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790873"
---
# <a name="encrypt-deployment-data"></a>Szyfrowanie danych wdrożenia

Podczas uruchamiania Azure Container Instances (ACI) w chmurze usługa ACI zbiera i utrwala dane związane z kontenerami. Usługa ACI automatycznie szyfruje te dane, gdy są utrwalane w chmurze. To szyfrowanie chroni dane w celu spełnienia wymagań organizacji dotyczących zabezpieczeń i zgodności. ACI umożliwia również szyfrowanie tych danych przy użyciu własnego klucza, co zapewnia większą kontrolę nad danymi związanymi z wdrożeniami usługi ACI.

## <a name="about-aci-data-encryption"></a>Informacje o szyfrowaniu danych WCI 

Dane w UCI są szyfrowane i odszyfrowywać przy użyciu 256-bitowego szyfrowania AES. Jest ona włączona dla wszystkich wdrożeń usługi ACI i nie trzeba modyfikować wdrożenia ani kontenerów, aby korzystać z tego szyfrowania. Dotyczy to metadanych dotyczących wdrożenia, zmiennych środowiskowych, kluczy przekazywanych do kontenerów oraz dzienników utrwalonych po zatrzymaniu kontenerów, aby nadal je widzieć. Szyfrowanie nie ma wpływu na wydajność grupy kontenerów i nie ma dodatkowych kosztów szyfrowania.

## <a name="encryption-key-management"></a>Zarządzanie kluczami szyfrowania

Możesz polegać na kluczach zarządzanych przez firmę Microsoft na szyfrowaniu danych kontenera lub zarządzać szyfrowaniem przy użyciu własnych kluczy. W poniższej tabeli porównano te opcje: 

|    |    Klucze zarządzane przez firmę Microsoft     |     Klucze zarządzane przez klienta     |
|----|----|----|
|    **Operacje szyfrowania/odszyfrowywania**    |    Azure    |    Azure    |
|    **Magazyn kluczy**    |    Microsoft Key Store    |    Azure Key Vault    |
|    **Odpowiedzialność za rotację kluczy**    |    Microsoft    |    Customer    |
|    **Dostęp do klucza**    |    Tylko firma Microsoft    |    Microsoft, Klient    |

Pozostała część dokumentu obejmuje kroki wymagane do zaszyfrowania danych wdrożenia usługi ACI przy użyciu klucza (klucza zarządzanego przez klienta). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Szyfrowanie danych przy użyciu klucza zarządzanego przez klienta

### <a name="create-service-principal-for-aci"></a>Tworzenie jednostki usługi dla usługi ACI

Pierwszym krokiem jest upewnienie się, że dzierżawa platformy [Azure](../active-directory/develop/quickstart-create-new-tenant.md) ma przypisaną jednostkę usługi na Azure Container Instances usługi. 

> [!IMPORTANT]
> Aby pomyślnie uruchomić poniższe polecenie i utworzyć jednostkę usługi, potwierdź, że masz uprawnienia do tworzenia jednostki usługi w dzierżawie.
>

Następujące polecenie interfejsu wiersza polecenia skonfiguruje usługę ACI SP w środowisku platformy Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

W danych wyjściowych uruchomienia tego polecenia powinna być wyświetlana nazwa główna usługi, dla których ustawiono wartość "displayName": "Azure Container Instance Service".

Jeśli nie możesz pomyślnie utworzyć jednostki usługi:
* potwierdź, że masz do tego uprawnienia w dzierżawie
* Sprawdź, czy w twojej dzierżawie istnieje już główna usługa w celu wdrożenia w usłudze ACI. Możesz to zrobić, uruchamiając i `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` zamiast tego korzystając z tej jednostki usługi

### <a name="create-a-key-vault-resource"></a>Tworzenie zasobu Key Vault zasobów

Utwórz nową Azure Key Vault przy [użyciu Azure Portal](../key-vault/general/quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](../key-vault/general/quick-create-cli.md)lub [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

W przypadku właściwości magazynu kluczy należy stosować się do następujących wytycznych: 
* Nazwa: wymagana jest unikatowa nazwa. 
* Subskrypcja: wybierz subskrypcję.
* W obszarze Grupa zasobów wybierz istniejącą grupę zasobów lub utwórz nową i wprowadź nazwę grupy zasobów.
* W menu rozwijanym Lokalizacja wybierz lokalizację.
* Możesz pozostawić wartości domyślne innych opcji lub wybrać je na podstawie dodatkowych wymagań.

> [!IMPORTANT]
> W przypadku używania kluczy zarządzanych przez klienta do szyfrowania szablonu wdrożenia usługi ACI zaleca się ustawienie następujących dwóch właściwości w magazynie kluczy: Usuwanie nieużytkcyjne i Nie przeczyszczaj. Te właściwości nie są domyślnie włączone, ale można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure w nowym lub istniejącym magazynie kluczy.

### <a name="generate-a-new-key"></a>Generowanie nowego klucza 

Po utworzeniu magazynu kluczy przejdź do zasobu w Azure Portal. W menu nawigacji po lewej stronie bloku zasobów w obszarze Ustawienia kliknij pozycję **Klucze**. W widoku "Klucze" kliknij pozycję "Generuj/zaimportuj", aby wygenerować nowy klucz. Użyj dowolnej unikatowej nazwy dla tego klucza i innych preferencji w zależności od wymagań. 

![Generowanie nowego klucza](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Ustawianie zasad dostępu

Utwórz nowe zasady dostępu, aby zezwalać usłudze ACI na dostęp do klucza.

* Po wygenerowaniu klucza wróć do bloku zasobów magazynu kluczy, w obszarze Ustawienia kliknij pozycję **Zasady dostępu.**
* Na stronie "Zasady dostępu" dla magazynu kluczy kliknij pozycję **Dodaj zasady dostępu.**
* Ustawianie uprawnień *klucza w* celu dołączania **i** **odpakowywaniu uprawnień** klucza ![ zestawu kluczy](./media/container-instances-encrypt-data/set-key-permissions.png)
* W *przypadku opcji Wybierz podmiot* zabezpieczeń wybierz pozycję Azure Container Instance **Service**
* Kliknij **pozycję Dodaj** u dołu 

Zasady dostępu powinny teraz być wyświetlane w zasadach dostępu magazynu kluczy.

![Nowe zasady dostępu](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

> [!IMPORTANT]
> Szyfrowanie danych wdrożenia przy użyciu klucza zarządzanego przez klienta jest dostępne w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wprowadzana. Określ tę wersję interfejsu API w szablonie wdrożenia. Jeśli masz problemy z tym problemem, skontaktuj się z pomoc techniczna platformy Azure.

Po skonfigurowaniu zasad dostępu i klucza magazynu kluczy dodaj następujące właściwości do szablonu wdrożenia usługi ACI. Dowiedz się więcej na temat wdrażania zasobów usługi ACI za pomocą szablonu z samouczka: wdrażanie grupy z wieloma kontenerami przy [użyciu Resource Manager szablonu](./container-instances-multi-container-group.md). 
* W `resources` obszarze ustaw wartość `apiVersion` `2019-12-01` .
* W sekcji właściwości grupy kontenerów szablonu wdrożenia dodaj element `encryptionProperties` , który zawiera następujące wartości:
  * `vaultBaseUrl`: nazwę DNS magazynu kluczy można znaleźć w bloku przeglądu zasobu magazynu kluczy w portalu
  * `keyName`: nazwa klucza wygenerowanego wcześniej
  * `keyVersion`: bieżąca wersja klucza. Można to znaleźć, klikając sam klucz (w obszarze "Klucze" w sekcji Ustawienia zasobu magazynu kluczy)
* W obszarze właściwości grupy kontenerów dodaj właściwość `sku` z wartością `Standard` . Właściwość `sku` jest wymagana w interfejsie API w wersji 2019-12-01.

Poniższy fragment kodu szablonu przedstawia te dodatkowe właściwości do szyfrowania danych wdrożenia:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Poniżej znajduje się kompletny szablon dostosowany z szablonu w części [Samouczek:](./container-instances-multi-container-group.md)wdrażanie grupy z wieloma kontenerami przy użyciu Resource Manager szablonu. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Wdrażanie zasobów

Jeśli plik szablonu został utworzony i edytowany na pulpicie, możesz przekazać go do katalogu Cloud Shell, przeciągając do niego plik. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wd wdrażaj szablon za pomocą [polecenia az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Po zakończeniu wdrażania wszystkie powiązane z nim dane utrwalone przez usługę ACI zostaną zaszyfrowane przy użyciu podanego klucza.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create
