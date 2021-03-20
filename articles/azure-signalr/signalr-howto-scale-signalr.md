---
title: Skalowanie wystąpienia usługi Azure Signal Service
description: Dowiedz się, jak skalować wystąpienie usługi Azure Signal Service, aby zwiększyć lub zmniejszyć pojemność, za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89595771"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Jak skalować wystąpienie usługi Azure sygnalizujące?
W tym artykule opisano sposób skalowania wystąpienia usługi Azure Signal Service. Istnieją dwa scenariusze skalowania, skalowanie w górę i w poziomie.

* [Skalowanie w górę](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): uzyskiwanie większej liczby jednostek, połączeń, wiadomości i innych. Skalowanie w górę przez zmianę warstwy cenowej z bezpłatna na Standard.
* [Skalowanie w poziomie](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Zwiększ liczbę jednostek sygnalizujących. Możesz skalować w poziomie do maksymalnie 100 jednostek. Dostępne są pewne opcje jednostkowe, które można wybrać do skalowania: 1, 2, 5, 10, 20, 50 i 100 jednostek dla jednego wystąpienia usługi sygnalizującej.

Zastosowanie ustawień skalowania może potrwać kilka minut. W rzadkich przypadkach może upłynąć około 30 minut. Nie wymagają one zmiany kodu ani ponownego wdrażania aplikacji serwera.

Aby uzyskać informacje na temat cen i pojemności poszczególnych usług sygnalizujących, zobacz [szczegóły cennika usługi Azure sygnalizującej](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Zmiana usługi sygnalizującej z warstwy **bezpłatna** na warstwę **standardowa** lub odwrotnie, publiczny adres IP usługi zostanie zmieniony i zwykle trwa 30-60 minut, aby PROPAGOWAĆ zmianę do serwerów DNS w całym Internecie. Usługa może być nieosiągalna, zanim system DNS zostanie zaktualizowany. Zazwyczaj nie zaleca się zbyt częstej zmiany warstwy cenowej.


## <a name="scale-on-azure-portal"></a>Skalowanie na Azure Portal

1. W przeglądarce otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Z menu po lewej stronie usługi sygnalizującego wybierz pozycję **Skala**.
   
3. Wybierz warstwę cenową, a następnie kliknij pozycję **Wybierz**. Ustaw liczbę jednostek dla warstwy **standardowa** .
   
    ![Skalowanie w portalu](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Kliknij pozycję **Zapisz**.

## <a name="scale-using-azure-cli"></a>Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy nowy zasób usługi sygnalizującego dla warstwy **bezpłatna** i nowej grupy zasobów, a następnie skaluje je do warstwy **standardowa** . 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Zanotuj rzeczywistą nazwę wygenerowaną dla nowej grupy zasobów. Tej nazwy grupy zasobów użyjesz, gdy zechcesz usunąć wszystkie zasoby w grupie.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Porównaj warstwy cenowe

Aby uzyskać szczegółowe informacje, takie jak uwzględnione komunikaty i połączenia dla każdej warstwy cenowej, zobacz [szczegóły cennika usługi sygnalizującego](https://azure.microsoft.com/pricing/details/signalr-service/).

Aby zapoznać się z tabelą limity usług, przydziałów i ograniczeń w poszczególnych warstwach, zobacz [limity usługi sygnałów](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku zawarto informacje na temat skalowania wystąpienia usługi pojedynczej sygnalizacji.

Obsługa wielu punktów końcowych jest także obsługiwana w scenariuszach skalowania, fragmentowania i między regionami.

> [!div class="nextstepaction"]
> [Usługa sygnalizująca skalowanie z wieloma wystąpieniami](./signalr-howto-scale-multi-instances.md)
