---
title: Tworzenie Service Bus tematów i subskrypcji za pomocą interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start dowiesz się, jak utworzyć temat Service Bus i subskrypcje w tym temacie przy użyciu interfejsu wiersza polecenia platformy Azure
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90069700"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć temat Service Bus i subskrypcje w temacie
W tym przewodniku szybki start utworzysz temat Service Bus przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie utworzysz subskrypcje w tym temacie. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do kolejek Service Bus, w których każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji typu "jeden do wielu" przy użyciu wzorca publikowania/subskrybowania. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtru dla tematu w oparciu o subskrypcję, co umożliwia filtrowanie lub ograniczanie komunikatów do tematu otrzymywanych przez poszczególne subskrypcje tematów.

Tematy Service Bus i subskrypcje umożliwiają skalowanie do przetwarzania dużej liczby komunikatów w dużej liczbie użytkowników i aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][free account] przed rozpoczęciem.

W tym przewodniku szybki start użyjesz Azure Cloud Shell, które można uruchomić po zalogowaniu się do Azure Portal. Aby uzyskać szczegółowe informacje na temat Azure Cloud Shell, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md). Możesz również [zainstalować](/cli/azure/install-azure-cli) i używać Azure PowerShell na komputerze. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Tworzenie tematu i subskrypcji usługi Service Bus
Każda [subskrypcja tematu](service-bus-messaging-overview.md#topics) może otrzymywać kopie wszystkich komunikatów. Tematy są w pełni protokołowane i semantycznie zgodne z kolejkami usługi Service Bus. Tematy usługi Service Bus obsługują najróżniejsze reguły wyboru z warunkami filtru, z użyciem opcjonalnych akcji, które ustawiają lub modyfikują właściwości komunikatów. Za każdym razem, gdy reguła pasuje, generuje komunikat. Aby dowiedzieć się więcej o regułach, filtrach i akcjach, kliknij ten [link](topic-filters.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Uruchom Azure Cloud Shell, wybierając ikonę pokazaną na poniższej ilustracji. Przełącz się do trybu **bash** , jeśli Cloud Shell jest w trybie **programu PowerShell** . 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell uruchamiania":::
3. Uruchom następujące polecenie, aby utworzyć grupę zasobów platformy Azure. W razie potrzeby zaktualizuj nazwę grupy zasobów i lokalizację. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Uruchom następujące polecenie, aby utworzyć przestrzeń nazw wiadomości Service Bus. Zaktualizuj nazwę przestrzeni nazw, która ma być unikatowa. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Uruchom następujące polecenie, aby utworzyć temat w przestrzeni nazw. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Utwórz pierwszą subskrypcję w temacie
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Tworzenie drugiej subskrypcji w temacie
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Utwórz trzecią subskrypcję w temacie
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Utwórz filtr dla pierwszej subskrypcji z filtrem przy użyciu właściwości niestandardowych ( `StoreId` jest to jedna z `Store1` , `Store2` i `Store3` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Utwórz filtr dla drugiej subskrypcji z filtrem przy użyciu właściwości klienta ( `StoreId = Store4` )

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Utwórz filtr dla trzeciej subskrypcji z filtrem przy użyciu właściwości klienta ( `StoreId` nie w `Store1` ,, `Store2` `Store3` lub `Store4` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Uruchom następujące polecenie, aby pobrać podstawowe parametry połączenia dla przestrzeni nazw. Te parametry połączenia służą do nawiązywania połączenia z kolejką i wysyłania i odbierania komunikatów. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Zanotuj parametry połączenia i nazwę tematu. Są one używane do wysyłania i odbierania wiadomości. 
    

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak wysyłać komunikaty do tematu i odbierać je za pośrednictwem subskrypcji, zobacz następujący artykuł: Wybierz język programowania w spisie treści. 

> [!div class="nextstepaction"]
> [Publikowanie i subskrybowanie komunikatów](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
