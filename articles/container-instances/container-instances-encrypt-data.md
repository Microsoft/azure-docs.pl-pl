---
title: Szyfrowanie danych wdrożenia
description: Informacje o szyfrowaniu danych utrwalonych dla zasobów wystąpień kontenera i sposobach szyfrowania danych za pomocą klucza zarządzanego przez klienta
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1c45999dbb354e8c2d550be82cdf37a6694d2dbb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825681"
---
# <a name="encrypt-deployment-data"></a>Szyfrowanie danych wdrożenia

W przypadku uruchamiania zasobów Azure Container Instances (ACI) w chmurze usługa ACI zbiera i utrzymuje dane związane z kontenerami. Program ACI automatycznie szyfruje te dane, gdy są utrwalane w chmurze. To szyfrowanie chroni dane, aby pomóc sprostać zobowiązaniom w zakresie zabezpieczeń i zgodności w organizacji. ACI udostępnia również opcję szyfrowania tych danych przy użyciu własnego klucza, co zapewnia większą kontrolę nad danymi związanymi z wdrożeniami ACI.

## <a name="about-aci-data-encryption"></a>Informacje o szyfrowaniu danych ACI 

Dane w ACI są szyfrowane i odszyfrowywane przy użyciu 256-bitowego szyfrowania AES. Jest on włączony dla wszystkich wdrożeń ACI i nie trzeba modyfikować wdrożenia ani kontenerów, aby korzystać z tego szyfrowania. Obejmuje to metadane dotyczące wdrożenia, zmienne środowiskowe, klucze, które są przesyłane do kontenerów, i dzienniki utrwalane po zatrzymaniu kontenerów, aby nadal były widoczne. Szyfrowanie nie ma wpływu na wydajność grupy kontenerów i nie ma dodatkowych opłat za szyfrowanie.

## <a name="encryption-key-management"></a>Zarządzanie kluczami szyfrowania

Możesz polegać na kluczach zarządzanych przez firmę Microsoft do szyfrowania danych kontenera lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. W poniższej tabeli porównano następujące opcje: 

|    |    Klucze zarządzane przez firmę Microsoft     |     Klucze zarządzane przez klienta     |
|----|----|----|
|    **Operacje szyfrowania/odszyfrowywania**    |    Azure    |    Azure    |
|    **Magazyn kluczy**    |    Magazyn kluczy firmy Microsoft    |    W usłudze Azure Key Vault    |
|    **Odpowiedzialność za kluczowe rotacje**    |    Microsoft    |    Klient    |
|    **Dostęp do klucza**    |    Tylko firma Microsoft    |    Firma Microsoft, klient    |

Pozostała część dokumentu obejmuje kroki wymagane do zaszyfrowania danych wdrożenia ACI za pomocą klucza (klucza zarządzanego przez klienta). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Szyfrowanie danych za pomocą klucza zarządzanego przez klienta

### <a name="create-service-principal-for-aci"></a>Utwórz nazwę główną usługi dla ACI

Pierwszym krokiem jest upewnienie się, że [dzierżawa platformy Azure](../active-directory/develop/quickstart-create-new-tenant.md) ma nazwę główną usługi przypisaną do przyznawania uprawnień do usługi Azure Container Instances. 

> [!IMPORTANT]
> Aby uruchomić następujące polecenie i utworzyć jednostkę usługi pomyślnie, potwierdź, że masz uprawnienia do tworzenia jednostek usługi w dzierżawie.
>

Następujące polecenie interfejsu wiersza polecenia spowoduje skonfigurowanie ACI SP w środowisku platformy Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Dane wyjściowe z uruchamiania tego polecenia powinny zawierać nazwę główną usługi, która została skonfigurowana przy użyciu "displayName" usługi wystąpienia kontenera platformy Azure.

Jeśli nie możesz pomyślnie utworzyć jednostki usługi:
* Potwierdź, że masz uprawnienia do tego w dzierżawie
* Sprawdź, czy jednostka usługi już istnieje w dzierżawie, aby można było wdrożyć ją w usłudze ACI. Można to zrobić, uruchamiając `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` i używając tej nazwy głównej usługi

### <a name="create-a-key-vault-resource"></a>Tworzenie zasobu Key Vault

Utwórz Azure Key Vault przy użyciu [Azure Portal](../key-vault/secrets/quick-create-portal.md#create-a-vault), [interfejsu wiersza polecenia](../key-vault/secrets/quick-create-cli.md)lub [programu PowerShell](../key-vault/secrets/quick-create-powershell.md). 

W przypadku właściwości magazynu kluczy należy użyć następujących wytycznych: 
* Nazwa: wymagana jest unikatowa nazwa. 
* Subskrypcja: wybierz subskrypcję.
* W obszarze Grupa zasobów wybierz istniejącą grupę zasobów lub Utwórz nową, a następnie wprowadź nazwę grupy zasobów.
* W menu rozwijanym Lokalizacja wybierz lokalizację.
* Inne opcje można pozostawić na wartości domyślne lub wybrać na podstawie dodatkowych wymagań.

> [!IMPORTANT]
> W przypadku korzystania z kluczy zarządzanych przez klienta do szyfrowania szablonu wdrożenia ACI zaleca się, aby w magazynie kluczy zostały ustawione następujące dwie właściwości, nietrwałe usuwanie i nie przeczyszczanie. Te właściwości nie są domyślnie włączone, ale można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla nowego lub istniejącego magazynu kluczy.

### <a name="generate-a-new-key"></a>Generuj nowy klucz 

Po utworzeniu magazynu kluczy przejdź do zasobu w Azure Portal. W menu nawigacji po lewej stronie bloku zasób w obszarze Ustawienia kliknij pozycję **klucze**. W widoku dla "klucze" kliknij pozycję "Generuj/Importuj", aby wygenerować nowy klucz. Użyj dowolnej unikatowej nazwy dla tego klucza oraz wszelkich innych preferencji w zależności od wymagań. 

![Generuj nowy klucz](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Ustawianie zasad dostępu

Utwórz nowe zasady dostępu, aby umożliwić usłudze ACI dostęp do klucza.

* Po wygenerowaniu klucza z powrotem w bloku zasobów magazynu kluczy w obszarze Ustawienia kliknij pozycję **zasady dostępu**.
* Na stronie "zasady dostępu" dla magazynu kluczy kliknij pozycję **Dodaj zasady dostępu**.
* Ustaw *uprawnienia klucza* do uwzględniania uprawnień klucza **Get** i **unotoki** ![ zestawu kluczy](./media/container-instances-encrypt-data/set-key-permissions.png)
* W obszarze *Wybieranie podmiotu zabezpieczeń*wybierz pozycję **Usługa wystąpienia kontenera platformy Azure**
* Kliknij pozycję **Dodaj** u dołu 

Zasady dostępu powinny teraz pojawiać się w zasadach dostępu magazynu kluczy.

![Nowe zasady dostępu](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

> [!IMPORTANT]
> Szyfrowanie danych wdrożenia za pomocą klucza zarządzanego przez klienta jest dostępne w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wdrażana. Określ tę wersję interfejsu API w szablonie wdrożenia. Jeśli masz jakieś problemy z tym, skontaktuj się z pomocą techniczną platformy Azure.

Po skonfigurowaniu klucza i zasad dostępu w magazynie kluczy Dodaj następujące właściwości do szablonu wdrożenia ACI. Dowiedz się więcej o wdrażaniu zasobów ACI za pomocą szablonu w [samouczku: Wdróż grupę z wieloma kontenerami przy użyciu szablonu Menedżer zasobów](./container-instances-multi-container-group.md). 
* W obszarze `resources` Ustaw `apiVersion` wartość `2019-12-01` .
* W sekcji Właściwości grupy kontenerów szablonu wdrożenia Dodaj element `encryptionProperties` , który zawiera następujące wartości:
  * `vaultBaseUrl`: nazwa DNS magazynu kluczy znajduje się w bloku przegląd zasobu magazynu kluczy w portalu
  * `keyName`: Nazwa wygenerowanego wcześniej klucza
  * `keyVersion`: bieżąca wersja klucza. Można to zrobić, klikając sam klucz (w obszarze "klucze" w sekcji Ustawienia zasobu magazynu kluczy).
* W obszarze właściwości grupy kontenerów Dodaj `sku` Właściwość o wartości `Standard` . `sku`Właściwość jest wymagana w interfejsie API w wersji 2019-12-01.

Poniższy fragment kodu przedstawia te dodatkowe właściwości służące do szyfrowania danych wdrożenia:

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

Poniżej znajduje się kompletny szablon dostosowany do szablonu w [samouczku: wdrażanie wielokontenerowej grupy przy użyciu szablonu Menedżer zasobów](./container-instances-multi-container-group.md). 

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

Jeśli utworzono i edytowano plik szablonu na pulpicie, można przekazać go do katalogu Cloud Shell, przeciągając plik do niego. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Po zakończeniu wdrożenia wszystkie dane związane z nim utrwalane przez usługę ACI zostaną zaszyfrowane przy użyciu podanego klucza.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
