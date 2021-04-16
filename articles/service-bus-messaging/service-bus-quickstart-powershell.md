---
title: używanie programu Azure PowerShell do tworzenia kolejki usługi Service Bus
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć przestrzeń nazw Service Bus kolejkę w przestrzeni nazw przy użyciu Azure PowerShell.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f7bf9e5435b00ee3076422cccbe689038051499d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537097"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Używanie Azure PowerShell do tworzenia Service Bus nazw i kolejki
W tym przewodniku Szybki start pokazano, jak utworzyć Service Bus nazw i kolejki przy użyciu Azure PowerShell. Pokazano w nim również, jak uzyskać poświadczenia autoryzacji, których aplikacja klienca może użyć do wysyłania i odbierania komunikatów do/z kolejki. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że masz subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne][] konto. 

W tym przewodniku Szybki start użyjesz Azure Cloud Shell, które można uruchomić po zalogowaniu się do Azure Portal. Aby uzyskać szczegółowe informacje Azure Cloud Shell, zobacz [Overview of Azure Cloud Shell (Omówienie Azure Cloud Shell](../cloud-shell/overview.md)). Można również [zainstalować i](/powershell/azure/install-Az-ps) używać Azure PowerShell na maszynie. 


## <a name="provision-resources"></a>Inicjowanie zasobów
1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Uruchom Azure Cloud Shell, wybierając ikonę pokazaną na poniższej ilustracji: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Uruchamianie Cloud Shell":::
3. W dolnym oknie Cloud Shell przejdź z powłoki **Bash** do programu **PowerShell.** 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Przełączanie do trybu programu PowerShell":::    
4. Uruchom następujące polecenie, aby utworzyć grupę zasobów platformy Azure. Zaktualizuj nazwę grupy zasobów i lokalizację, jeśli chcesz. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Uruchom następujące polecenie, aby utworzyć przestrzeń nazw Service Bus komunikatów. W tym `ContosoRG` przykładzie jest to grupa zasobów utworzona w poprzednim kroku. `ContosoSBusNS` to nazwa przestrzeni nazw Service Bus utworzonej w tej grupie zasobów. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Uruchom następujące czynności, aby utworzyć kolejkę w przestrzeni nazw utworzonej w poprzednim kroku. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Pobierz podstawowe ciągi połączenia dla przestrzeni nazw. Te ciągi połączenia są służące do nawiązywania połączenia z kolejką oraz wysyłania i odbierania komunikatów. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Zanotuj ciąg połączenia i nazwę kolejki. Można ich używać do wysyłania i odbierania komunikatów. 


## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono przestrzeń Service Bus i kolejkę w przestrzeni nazw . Aby dowiedzieć się, jak wysyłać i odbierać komunikaty do/z kolejki, zobacz jeden z następujących przewodników Szybki start w sekcji Wysyłanie i **odbieranie** komunikatów. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
