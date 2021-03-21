---
title: używanie programu Azure PowerShell do tworzenia kolejki usługi Service Bus
description: W tym przewodniku szybki start dowiesz się, jak utworzyć Service Bus przestrzeń nazw i kolejkę w przestrzeni nazw przy użyciu Azure PowerShell.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8cf024735c66e6bae9d334e3d8ce8d0a0eed1426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95799104"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Użyj Azure PowerShell, aby utworzyć Service Bus przestrzeń nazw i kolejkę
Ten przewodnik Szybki Start przedstawia sposób tworzenia Service Bus przestrzeni nazw i kolejki przy użyciu Azure PowerShell. Pokazano w nim także, jak uzyskać poświadczenia autoryzacji, które mogą być używane przez aplikację kliencką do wysyłania/odbierania komunikatów z kolejki. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, upewnij się, że masz subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][] przed rozpoczęciem. 

W tym przewodniku szybki start użyjesz Azure Cloud Shell, które można uruchomić po zalogowaniu się do Azure Portal. Aby uzyskać szczegółowe informacje na temat Azure Cloud Shell, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md). Możesz również [zainstalować](/powershell/azure/install-Az-ps) i używać Azure PowerShell na komputerze. 


## <a name="provision-resources"></a>Inicjowanie zasobów
1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Uruchom Azure Cloud Shell, wybierając ikonę pokazaną na poniższej ilustracji: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell uruchamiania":::
3. W dolnym oknie Cloud Shell Przełącz z **bash** do **programu PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Przełącz do trybu programu PowerShell":::    
4. Uruchom następujące polecenie, aby utworzyć grupę zasobów platformy Azure. W razie potrzeby zaktualizuj nazwę grupy zasobów i lokalizację. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Uruchom następujące polecenie, aby utworzyć przestrzeń nazw wiadomości Service Bus. W tym przykładzie `ContosoRG` jest grupą zasobów utworzoną w poprzednim kroku. `ContosoSBusNS` jest nazwą Service Bus przestrzeni nazw utworzonej w tej grupie zasobów. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Uruchom następujące kroki, aby utworzyć kolejkę w przestrzeni nazw utworzonej w poprzednim kroku. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Pobierz podstawowe parametry połączenia dla przestrzeni nazw. Te parametry połączenia służą do nawiązywania połączenia z kolejką i wysyłania i odbierania komunikatów. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

