---
title: (PRZESTARZAŁE) Jednostka usługi klastra Azure Kubernetes
description: Tworzenie jednostki usługi Azure Active Directory dla klastra Kubernetes w usłudze Azure Container Service i zarządzanie nią
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1653fbcaf81981ca3d32d41c2e2892093922011b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85504743"
---
# <a name="deprecated-set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>(PRZESTARZAŁE) Konfigurowanie jednostki usługi Azure AD dla klastra Kubernetes w usłudze Container Service

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu z opisem użycia usługi Azure Kubernetes Service, zobacz [Jednostki usługi w usłudze Azure Kubernetes Service (AKS)](../../aks/kubernetes-service-principal.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

W usłudze Azure Container Service klaster Kubernetes wymaga [jednostki usługi Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) do współpracy z interfejsami API platformy Azure. Jednostka usługi jest potrzebna do dynamicznego zarządzania zasobami, takimi jak [trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) i narzędzie [Azure Load Balancer dla warstwy 4](../../load-balancer/load-balancer-overview.md).


W tym artykule przedstawiono różne sposoby konfigurowania jednostki usługi dla klastra Kubernetes. Przykładowo, jeśli zainstalowano i skonfigurowano [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2), można uruchomić polecenie [`az acs create`](/cli/azure/acs), aby jednocześnie utworzyć klaster Kubernetes i jednostkę usługi.


## <a name="requirements-for-the-service-principal"></a>Wymagania dotyczące nazwy głównej usługi

Możesz użyć istniejącej jednostki usługi Azure AD, która spełnia następujące wymagania, albo utworzyć nową.

* **Zakres**: grupa zasobów

* **Rola**: współautor

* **Wpis tajny klienta**: musi to być hasło. Obecnie nie można używać nazwy głównej usługi do uwierzytelniania certyfikatu.

> [!IMPORTANT]
> Aby utworzyć jednostkę usługi, musisz mieć uprawnienia do zarejestrowania aplikacji w swojej dzierżawie usługi Azure AD i przypisania aplikacji do roli w swojej subskrypcji. Aby sprawdzić, czy masz wymagane uprawnienia, [zajrzyj do portalu](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Opcja 1. Tworzenie jednostki usługi w usłudze Azure AD

Jeśli chcesz utworzyć jednostkę usługi Azure AD przed wdrożeniem klastra Kubernetes, platforma Azure udostępnia kilka metod.

Następujące przykładowe polecenia pokazują, jak można wykonać to za pomocą interfejsu [wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Można też utworzyć jednostkę usługi za pomocą programu [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md), [portalu](../../active-directory/develop/howto-create-service-principal-portal.md) lub innej metody.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

Dane wyjściowe są zbliżone do następujących (pokazane tutaj zostały zredagowane):

![Tworzenie nazwy głównej usługi](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Wyróżniony jest **Identyfikator klienta** ( `appId` ) i **klucz tajny klienta** ( `password` ), który jest używany jako parametry jednostki usługi do wdrożenia klastra.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Określanie jednostki usługi podczas tworzenia klastra Kubernetes

Podaj **identyfikator klienta** (nazywany również `appId`, identyfikator aplikacji) i **wpis tajny klienta** (`password`) istniejącej nazwy głównej usługi jako parametry podczas tworzenia klastra Kubernetes. Upewnij się, że jednostka usługi spełnia wymagania przedstawione na początku tego artykułu.

Te parametry można określić podczas wdrażania klastra Kubernetes za pomocą [interfejsu wiersza polecenia platformy Azure](container-service-kubernetes-walkthrough.md), witryny [Azure Portal](../dcos-swarm/container-service-deployment.md) lub innymi metodami.

>[!TIP]
>Podczas określania **identyfikatora klienta** należy użyć identyfikatora `appId`, a nie `ObjectId` nazwy głównej usługi.
>

Poniższy przykład przedstawia sposób przekazania parametrów poprzez interfejs wiersza polecenia Azure. W tym przykładzie użyto [szablonu Kubernetes quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Pobierz](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) plik parametrów szablonu `azuredeploy.parameters.json` z usługi GitHub.

2. Aby określić nazwę główną usługi, wprowadź wartości dla `servicePrincipalClientId` i `servicePrincipalClientSecret` w pliku. (Należy również podać własne wartości dla `dnsNamePrefix` i `sshRSAPublicKey`. Ten drugi jest kluczem publicznym SSH, aby uzyskać dostęp do klastra. Zapisz plik.

    ![Przekazywanie parametrów nazwy głównej usługi](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Przy użyciu opcji `--parameters` uruchom następujące polecenie, aby ustawić ścieżkę do pliku azuredeploy.parameters.json. To polecenie wdraża klaster w utworzonej grupie zasobów o nazwie `myResourceGroup` w regionie Zachodnie stany USA.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Opcja 2. Wygenerowanie jednostki usługi podczas tworzenia klastra przy użyciu polecenia `az acs create`

Jeśli uruchomisz [`az acs create`](/cli/azure/acs#az-acs-create) polecenie w celu utworzenia klastra Kubernetes, będziesz mieć możliwość automatycznego wygenerowania jednostki usługi.

Tak jak w przypadku innych opcji tworzenia klastra Kubernetes, parametry istniejącej nazwy głównej usługi można określić po uruchomieniu polecenia `az acs create`. Jednak w przypadku pominięcia tych parametrów interfejs wiersza polecenia platformy Azure automatycznie tworzy jednostkę usługi do użycia z usługą Container Service. Podczas wdrażania dzieje się to w sposób niewidoczny dla użytkownika.

Następujące polecenie tworzy klaster Kubernetes i generuje zarówno klucze SSH, jak i poświadczenia nazwy głównej usługi:

```azurecli
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Jeśli konto nie ma uprawnień usługi Azure AD i subskrypcji do utworzenia jednostki usługi, polecenie generuje błąd podobny do: `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Jeśli nie masz uprawnień do utworzenia jednostki usługi w swojej subskrypcji, konieczne może być poproszenie administratora usługi Azure AD lub subskrypcji o przypisanie niezbędnych uprawnień albo o jednostkę usługi do użycia z usługą Azure Container Service.

* Jednostka usługi dla rozwiązania Kubernetes jest częścią konfiguracji klastra. Nie należy jednak używać tożsamości do wdrażania klastra.

* Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Jednostka usługi dla klastra Kubernetes może zostać skojarzona z dowolną prawidłową nazwą aplikacji usługi Azure AD (np. `https://www.contoso.org/example`). Adres URL dla aplikacji nie musi być rzeczywistym punktem końcowym.

* Podczas określania **identyfikatora klienta** jednostki usługi można użyć wartości `appId` (jak pokazano w tym artykule) lub odpowiedniej jednostki usługi `name` (na przykład `https://www.contoso.org/example`).

* Na głównej maszynie wirtualnej i maszynach wirtualnych agentów w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`.

* Gdy używasz polecenia `az acs create`, aby automatycznie wygenerować jednostkę usługi, poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/acsServicePrincipal.json` na maszynie użytej do uruchomienia polecenia.

* Kiedy używasz polecenia `az acs create` do automatycznego wygenerowania jednostki usługi, jednostka usługi może także uwierzytelnić się za pomocą [rejestru Azure Container Registry](../../container-registry/container-registry-intro.md) utworzonego w tej samej subskrypcji.

* Poświadczenia jednostki usługi mogą wygasnąć, powodując przejście węzłów klastra w stan **Niegotowe**. Informacje o radzeniu sobie z tym problemem podano w sekcji [Wygaśnięcie poświadczeń](#credential-expiration).

## <a name="credential-expiration"></a>Wygaśnięcie poświadczeń

O ile podczas tworzenia jednostki usługi nie zostanie określone niestandardowe okno ważności za pomocą parametru `--years`, jej poświadczenia są ważne przez rok od momentu utworzenia. Gdy poświadczenia wygasną, węzły klastra mogą przejść w stan **Niegotowe**.

Aby sprawdzić datę wygaśnięcia jednostki usługi, wykonaj polecenie [az ad app show](/cli/azure/ad/app#az-ad-app-show) z parametrem `--debug`, a następnie sprawdź wartość `endDate` pozycji `passwordCredentials` w dolnej części danych wyjściowych:

```azurecli
az ad app show --id <appId> --debug
```

Dane wyjściowe (przedstawione tu w postaci obciętej):

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

Jeśli poświadczenia jednostki usługi wygasły, zaktualizuj je za pomocą polecenia [az ad sp reset-credentials](/cli/azure/ad/sp):

```azurecli
az ad sp reset-credentials --name <appId>
```

Dane wyjściowe:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

Następnie zaktualizuj plik `/etc/kubernetes/azure.json` o nowe poświadczenia we wszystkich węzłach klastra i ponownie uruchom te węzły.

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij pracę z klastrem Kubernetes](container-service-kubernetes-walkthrough.md) w klastrze usług kontenera.

* Aby rozwiązać problemy z jednostką usługi dla rozwiązania Kubernetes, zobacz [dokumentację aparatu ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).
