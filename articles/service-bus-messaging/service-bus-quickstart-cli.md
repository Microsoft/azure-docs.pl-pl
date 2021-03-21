---
title: Szybki start — używanie interfejsu wiersza polecenia do tworzenia kolejki usługi Service Bus | Microsoft Docs
description: W tym przewodniku szybki start dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia przestrzeni nazw Service Bus, a następnie kolejki w tej przestrzeni nazw.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95810627"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure w celu utworzenia Service Bus przestrzeni nazw i kolejki
Ten przewodnik Szybki Start przedstawia sposób tworzenia Service Bus przestrzeni nazw i kolejki przy użyciu interfejsu wiersza polecenia platformy Azure. Pokazano w nim także, jak uzyskać poświadczenia autoryzacji, które mogą być używane przez aplikację kliencką do wysyłania/odbierania komunikatów z kolejki. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][free account] przed rozpoczęciem.

W tym przewodniku szybki start użyjesz Azure Cloud Shell, które można uruchomić po zalogowaniu się do Azure Portal. Aby uzyskać szczegółowe informacje na temat Azure Cloud Shell, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md). Możesz również [zainstalować](/cli/azure/install-azure-cli) i używać Azure PowerShell na komputerze. 

## <a name="provision-resources"></a>Inicjowanie zasobów
1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Uruchom Azure Cloud Shell, wybierając ikonę pokazaną na poniższej ilustracji. Przełącz się do trybu **bash** , jeśli Cloud Shell jest w trybie **programu PowerShell** . 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell uruchamiania":::
3. Uruchom następujące polecenie, aby utworzyć grupę zasobów platformy Azure. W razie potrzeby zaktualizuj nazwę grupy zasobów i lokalizację. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Uruchom następujące polecenie, aby utworzyć przestrzeń nazw wiadomości Service Bus.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Uruchom następujące polecenie, aby utworzyć kolejkę w przestrzeni nazw utworzonej w poprzednim kroku. W tym przykładzie `ContosoRG` jest grupą zasobów utworzoną w poprzednim kroku. `ContosoSBusNS` jest nazwą Service Bus przestrzeni nazw utworzonej w tej grupie zasobów. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Uruchom następujące polecenie, aby pobrać podstawowe parametry połączenia dla przestrzeni nazw. Te parametry połączenia służą do nawiązywania połączenia z kolejką i wysyłania i odbierania komunikatów. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Zanotuj parametry połączenia i nazwę kolejki. Są one używane do wysyłania i odbierania wiadomości. 


## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie Service Bus przestrzeni nazw i kolejki w przestrzeni nazw. Aby dowiedzieć się, jak wysyłać/odbierać komunikaty do/z kolejki, zobacz jeden z następujących przewodników szybki start w sekcji **wysyłanie i odbieranie komunikatów** . 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

