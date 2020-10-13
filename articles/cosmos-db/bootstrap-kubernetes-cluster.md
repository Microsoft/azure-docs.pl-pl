---
title: Jak korzystać z usługi Azure Kubernetes z usługą Azure Cosmos DB
description: Dowiedz się, jak załadować klaster Kubernetes na platformie Azure, który używa Azure Cosmos DB (wersja zapoznawcza)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: b9e6e1388465542e9fb3ac69540c981a1fd70d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570127"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Jak używać usługi Azure Kubernetes z Azure Cosmos DB (wersja zapoznawcza)

Interfejs API etcd w Azure Cosmos DB umożliwia korzystanie z Azure Cosmos DB jako magazynu zaplecza dla usługi Azure Kubernetes. Azure Cosmos DB implementuje protokół sieci szkieletowej etcd, który umożliwia serwerom interfejsu API węzła podstawowego korzystanie z Azure Cosmos DB podobnie jak w przypadku dostępu do lokalnie zainstalowanej etcd. Interfejs API etcd w Azure Cosmos DB jest obecnie w wersji zapoznawczej. Gdy korzystasz z interfejsu API usługi Azure Cosmos etcd jako magazynu zapasowego dla Kubernetes, uzyskasz następujące korzyści: 

* Nie trzeba ręcznie konfigurować etcd i zarządzać nimi.
* Wysoka dostępność etcd, gwarantowana przez Cosmos (99,99% w jednym regionie, 99,999% w wielu regionach).
* Elastyczna skalowalność etcd.
* Zabezpiecz domyślnie & Enterprise gotowe.
* Wiodące w branży, kompleksowe umowy SLA.

Aby dowiedzieć się więcej o interfejsie API etcd w Azure Cosmos DB, zapoznaj się z artykułem dotyczącym [przeglądu](etcd-api-introduction.md) . W tym artykule pokazano, jak za pomocą [aparatu usługi Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (AKS-Engine) załadować klaster Kubernetes na platformie Azure, który używa [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) zamiast lokalnie zainstalowanego i skonfigurowanego etcd. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Interfejs wiersza polecenia platformy Azure można pobrać dla danego systemu operacyjnego i zainstalować program.

1. Zainstaluj [najnowszą wersję](https://github.com/Azure/aks-engine/releases) aparatu Azure Kubernetes. Instrukcje dotyczące instalacji dla różnych systemów operacyjnych są dostępne na stronie [aparatu usługi Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) . Wystarczy wykonać kroki opisane w sekcji **Instalowanie aparatu AKS** w połączonym dokumencie. Po pobraniu pliku zip wyodrębnij go.

   Aparat usługi Azure Kubernetes (**AKS-Engine**) generuje szablony Azure Resource Manager dla klastrów Kubernetes na platformie Azure. Wejście do AKS-Engine jest plikiem definicji klastra opisującym żądany klaster, w tym Orchestrator, Features i Agents. Struktura plików wejściowych jest podobna do publicznego interfejsu API usługi Azure Kubernetes.

1. Interfejs API etcd w Azure Cosmos DB jest obecnie w wersji zapoznawczej. Zarejestruj się, aby korzystać z wersji zapoznawczej w: https://aka.ms/cosmosetcdapi-signup . Po przesłaniu formularza subskrypcja będzie mogła korzystać z interfejsu API usługi Azure Cosmos etcd. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Wdróż klaster przy użyciu Azure Cosmos DB

1. Otwórz okno wiersza polecenia i zaloguj się do platformy Azure przy użyciu następującego polecenia:

   ```azurecli-interactive
   az login 
   ```

1. Jeśli masz więcej niż jedną subskrypcję, przejdź do subskrypcji, która została dozwolona dla Azure Cosmos DB interfejsu API etcd. Możesz przełączyć się do wymaganej subskrypcji przy użyciu następującego polecenia:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Następnie utwórz nową grupę zasobów, w której zostaną wdrożone zasoby wymagane przez klaster usługi Azure Kubernetes. Upewnij się, że grupa zasobów została utworzona w regionie "środkowe". Nie jest wymagane, aby grupa zasobów była w regionie "Centralna", jednak interfejs API usługi Azure Cosmos etcd jest obecnie dostępny do wdrożenia tylko w regionie "Centralna". Dlatego najlepszym rozwiązaniem jest posiadanie Kubernetes klastra z wystąpieniem Cosmos etcd:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Następnie Utwórz nazwę główną usługi dla klastra usługi Azure Kubernetes, aby mogła ona komunikować się z zasobami, które są częścią tej samej grupy zasobów. Można utworzyć nazwę główną usługi przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal w tym przykładzie zostanie utworzony interfejs wiersza polecenia.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   To polecenie wyprowadza szczegóły jednostki usługi, na przykład:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Zanotuj **identyfikator appid** i pola **hasła** , ponieważ będziesz używać tych parametrów w następnych krokach. 

1. W wierszu polecenia przejdź do folderu, w którym znajduje się plik wykonywalny aparatu Azure Kubernetes. Na przykład w wierszu polecenia można przejść do folderu jako:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Otwórz wybrany edytor tekstu i zdefiniuj szablon Menedżer zasobów, który wdraża klaster Azure Kubernetes za pomocą interfejsu API Azure Cosmos DB etcd. Skopiuj poniższą definicję JSON do edytora tekstów i Zapisz plik jako `apiModel.json` :

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   W pliku definicji JSON/klastra, parametr klucza do uwagi to **"cosmosEtcd": true**. Ten parametr znajduje się we właściwościach "masterProfile" i wskazuje wdrożenie do użycia interfejsu API usługi Azure Cosmos etcd zamiast zwykłych etcd. 

1. Wdróż klaster usługi Azure Kubernetes, który używa Azure Cosmos DB przy użyciu następującego polecenia:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Aparat Azure Kubernetes korzysta z definicji klastra, która przedstawia żądany kształt, rozmiar i konfigurację usługi Azure Kubernetes. Istnieje kilka funkcji, które można włączyć za pomocą definicji klastra. W tym przykładzie zostaną użyte następujące parametry:

   * **Identyfikator subskrypcji:** Identyfikator subskrypcji platformy Azure z włączonym Azure Cosmos DB interfejsem API etcd.
   * **Identyfikator klienta:** Identyfikator appId jednostki usługi. `appId`Został zwrócony jako dane wyjściowe w kroku 4.
   * **Wpis tajny klienta:** Hasło jednostki usługi lub losowo generowane hasło. Ta wartość została zwrócona jako dane wyjściowe w parametrze "Password" w kroku 4. 
   * **dnsPrefix:** Unikatowa nazwa systemu DNS w regionie. Ta wartość będzie częścią nazwy hosta (przykładowe wartości to-myprod1, Staging).
   * **Lokalizacja:**  Lokalizacja, w której ma zostać wdrożony klaster. obecnie obsługiwane jest tylko "centralne".

   > [!Note]
   > Interfejs API usługi Azure Cosmos etcd jest obecnie dostępny do wdrożenia tylko w regionie "centralne". 
 
   * **model API:** W pełni kwalifikowana ścieżka do pliku szablonu.
   * **Wymuś zastępowanie:** Ta opcja służy do automatycznego zastępowania istniejących plików w katalogu wyjściowym.
 
   Następujące polecenie pokazuje przykładowe wdrożenie:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Ukończenie wdrożenia szablonu trwa kilka minut. Po pomyślnym zakończeniu wdrożenia zobaczysz następujące dane wyjściowe w wierszu polecenia:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Grupa zasobów zawiera teraz zasoby, takie jak maszyna wirtualna, konto platformy Azure Cosmos (interfejs API etcd), Sieć wirtualna, zestaw dostępności i inne zasoby wymagane przez klaster Kubernetes. 

Nazwa konta usługi Azure Cosmos będzie zgodna z określonym prefiksem DNS dołączonym do k8s. Twoje konto usługi Azure Cosmos zostanie automatycznie udostępnione przy użyciu bazy danych o nazwie **EtcdDB** i kontenera o nazwie **EtcdData**. W kontenerze będą przechowywane wszystkie powiązane dane etcd. Kontener jest inicjowany z określoną liczbą jednostek żądań i można [skalować (zwiększać/zmniejszać) przepływność](scaling-throughput.md) na podstawie obciążenia. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak korzystać z usługi Azure Cosmos Database, kontenerów i elementów](databases-containers-items.md)
* Dowiedz się, jak [zoptymalizować alokowane koszty przepływności](optimize-cost-throughput.md)

