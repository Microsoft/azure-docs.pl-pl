---
title: 'ML Studio (klasyczny): wdrażanie i użycie — Azure'
description: Azure Machine Learning Studio (klasyczny) służy do wdrażania przepływów pracy i modeli uczenia maszynowego jako usług sieci Web. Te usługi sieci Web mogą być następnie używane do wywoływania modeli uczenia maszynowego z aplikacji przez Internet, aby wykonywać przewidywania w czasie rzeczywistym lub w trybie wsadowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 57c5462cd6eccab1d9c42e674e10ea34db3612b7
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520361"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasyczne) usługi sieci Web: wdrażanie i użycie

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (klasyczny) służy do wdrażania przepływów pracy i modeli uczenia maszynowego jako usług sieci Web. Te usługi sieci Web mogą być następnie używane do wywoływania modeli uczenia maszynowego z aplikacji przez Internet, aby wykonywać przewidywania w czasie rzeczywistym lub w trybie wsadowym. Ponieważ usługi sieci Web są RESTful, możesz je wywoływać z różnych języków programowania i platform, takich jak .NET i Java, oraz z aplikacji, takich jak program Excel.

W następnych sekcjach znajdują się linki do przewodników, kodu i dokumentacji ułatwiających rozpoczęcie pracy.

## <a name="deploy-a-web-service"></a>Wdrażanie usługi internetowej

### <a name="with-azure-machine-learning-studio-classic"></a>Z Azure Machine Learning Studio (klasyczny)

Portal Studio (klasyczny) i Portal usług sieci Web Microsoft Azure Machine Learning ułatwiają wdrażanie usługi sieci Web i zarządzanie nią bez pisania kodu.

Poniższe linki zawierają ogólne informacje dotyczące sposobu wdrażania nowej usługi sieci Web:

* Aby zapoznać się z omówieniem sposobu wdrażania nowej usługi sieci Web opartej na Azure Resource Manager, zobacz [wdrażanie nowej usługi sieci Web](deploy-a-machine-learning-web-service.md).
* Aby zapoznać się z przewodnikiem dotyczącym wdrażania usługi sieci Web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Aby zapoznać się z pełnym przewodnikiem dotyczącym tworzenia i wdrażania usługi sieci Web, Rozpocznij od [samouczka 1: przewidywanie ryzyka kredytowego](tutorial-part1-credit-risk.md).
* Aby zapoznać się z konkretnymi przykładami, które wdrażają usługę sieci Web, zobacz:

  * [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)
  * [Jak wdrożyć usługę sieci Web w wielu regionach](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Za pomocą interfejsów API dostawcy zasobów usług sieci Web (Azure Resource Manager interfejsów API)

Dostawca zasobów Azure Machine Learning Studio (klasyczny) dla usług sieci Web umożliwia wdrażanie usług sieci Web i zarządzanie nimi przy użyciu wywołań interfejsu API REST. Aby uzyskać więcej informacji, zobacz informacje dotyczące [usługi sieci Web Machine Learning (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell

Dostawca zasobów Azure Machine Learning Studio (klasyczny) dla usług sieci Web umożliwia wdrażanie usług sieci Web i zarządzanie nimi za pomocą poleceń cmdlet programu PowerShell.

Aby użyć poleceń cmdlet, musisz najpierw zalogować się do konta platformy Azure z poziomu środowiska programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Jeśli nie znasz sposobu wywoływania poleceń programu PowerShell opartych na Menedżer zasobów, zobacz [używanie Azure PowerShell z Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Aby wyeksportować eksperyment predykcyjny, użyj [tego przykładowego kodu](https://github.com/ritwik20/AzureML-WebServices). Po utworzeniu pliku exe w kodzie można wpisać:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Uruchomienie aplikacji tworzy szablon JSON usługi sieci Web. Aby użyć szablonu do wdrożenia usługi sieci Web, należy dodać następujące informacje:

* Nazwa i klucz konta magazynu

    Możesz pobrać nazwę i klucz konta magazynu z [Azure Portal](https://portal.azure.com/).
* Identyfikator planu zobowiązania

    Identyfikator planu można uzyskać z portalu [usług sieci Web Azure Machine Learning](https://services.azureml.net) , logując się i klikając nazwę planu.

Dodaj je do szablonu JSON jako elementy podrzędne węzła *Właściwości* na tym samym poziomie, co węzeł *MachineLearningWorkspace* .

Oto przykład:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Więcej informacji można znaleźć w następujących artykułach i przykładowym kodzie:

* Dokumentacja [poleceń cmdlet Azure Machine Learning Studio (klasycznych)](/powershell/module/az.machinelearning) w witrynie MSDN

## <a name="consume-the-web-services"></a>Korzystanie z usług sieci Web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z poziomu interfejsu użytkownika usług sieci Web Azure Machine Learning (testowanie)

Usługę sieci Web można testować za pomocą portalu usług sieci Web Azure Machine Learning. Obejmuje to testowanie interfejsów usługi Request-Response (RR) i usługi wykonywania wsadowego (BES).

* [Wdrażanie nowej usługi sieci Web](deploy-a-machine-learning-web-service.md)
* [Wdrażanie usługi sieci Web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z programu Excel

Możesz pobrać szablon programu Excel, który korzysta z usługi sieci Web:

* [Zużywanie Azure Machine Learning usługi sieci Web z programu Excel](consuming-from-excel.md)
* [Dodatek programu Excel dla usług Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Z poziomu klienta opartego na protokole REST

Usługi sieci Web Azure Machine Learning są interfejsami API RESTful. Można korzystać z tych interfejsów API z różnych platform, takich jak .NET, Python, R, Java itp. Strona **Korzystanie** z usługi sieci Web w [portalu usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) zawiera przykładowy kod, który może pomóc Ci rozpocząć pracę. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).