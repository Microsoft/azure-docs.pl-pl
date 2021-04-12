---
title: Jak korzystać z usługi Azure Queue Storage z poziomu programu PowerShell — Azure Storage
description: Wykonywanie operacji na platformie Azure Queue Storage za pomocą programu PowerShell. Za pomocą usługi Azure Queue Storage można przechowywać dużą liczbę komunikatów, które są dostępne dla protokołu HTTP/HTTPS.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1436114a070dff7120a7de10937cbfa58644d305
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276250"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Jak używać usługi Azure Queue Storage z poziomu programu PowerShell

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do usługi Azure queue storage](storage-queues-introduction.md). W tym artykule opisano typowe operacje Queue Storage. Omawiane kwestie:

> [!div class="checklist"]
>
> - Tworzenie kolejki
> - Pobieranie kolejki
> - Dodawanie komunikatu
> - Odczytaj wiadomość
> - Usuwanie wiadomości
> - Usuwanie kolejki

Ten przewodnik zawiera wymagania Azure PowerShell ( `Az` ) modułu v 0.7 lub nowszego. Uruchom `Get-Module -ListAvailable Az` , aby znaleźć aktualnie zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Brak poleceń cmdlet programu PowerShell dla płaszczyzny danych dla kolejek. Aby wykonywać operacje płaszczyzny danych, takie jak dodawanie komunikatu, odczytywanie wiadomości i usuwanie komunikatu, należy użyć biblioteki klienta programu .NET Storage, która jest dostępna w programie PowerShell. Tworzysz obiekt Message, a następnie możesz użyć poleceń, takich jak `AddMessage` do wykonywania operacji na tym komunikacie. W tym artykule opisano, jak to zrobić.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Pobierz listę lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. To ćwiczenie będzie używane `eastus` . Zapisz ją w zmiennej `location` do użycia w przyszłości.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Zapisz nazwę grupy zasobów w zmiennej do użycia w przyszłości. W tym przykładzie grupa zasobów o nazwie `howtoqueuesrg` zostanie utworzona w regionie `eastus`.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz standardowe konto magazynu ogólnego przeznaczenia z magazynem lokalnie nadmiarowy (LRS) przy użyciu polecenia [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Pobierz kontekst konta magazynu, który definiuje konto magazynu do użycia. Wykonując działania względem konta magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Poniższy przykład najpierw nawiązuje połączenie z usługą Azure Storage przy użyciu kontekstu konta magazynu, który obejmuje nazwę konta magazynu i jego klucz dostępu. Następnie wywołuje polecenie cmdlet [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) , aby utworzyć kolejkę o nazwie `howtoqueue` .

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Aby uzyskać informacje dotyczące konwencji nazewnictwa dla usługi Azure Queue Storage, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Pobieranie kolejki

Można wykonywać zapytania i pobierać określoną kolejkę lub listę wszystkich kolejek na koncie magazynu. W poniższych przykładach pokazano, jak pobrać wszystkie kolejki z konta magazynu i określoną kolejkę; Oba polecenia korzystają z polecenia cmdlet [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) .

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Operacje, które mają wpływ na rzeczywiste komunikaty w kolejce, korzystają z biblioteki klienta programu .NET Storage jako uwidocznionej w programie PowerShell. Aby dodać komunikat do kolejki, Utwórz nowe wystąpienie obiektu Message, [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) Class. Następnie Wywołaj [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) metodę. Element `CloudQueueMessage` można utworzyć na podstawie ciągu (w formacie UTF-8) lub tablicy bajtowej.

Poniższy przykład pokazuje, jak dodać komunikat do kolejki.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Jeśli używasz [Eksplorator usługi Azure Storage](https://storageexplorer.com), możesz nawiązać połączenie z kontem platformy Azure i wyświetlić kolejki na koncie magazynu, a następnie przejść do kolejki, aby wyświetlić komunikaty w kolejce.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Odczytaj komunikat z kolejki, a następnie usuń go

Komunikaty są odczytywane w najlepszym przypadku, gdy jest to pierwsze zamówienie. Nie jest to gwarantowane. Po odczytaniu komunikatu z kolejki jest on niewidoczny dla wszystkich innych procesów, które przeglądają kolejkę. Pozwala to zagwarantować, że jeśli kod nie przetworzy komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu może uzyskać ten sam komunikat i spróbować ponownie.

Ten **limit czasu niewidoczności** określa, jak długo komunikat pozostaje niewidoczny przed ponownym udostępnieniem do przetworzenia. Wartość domyślna to 30 sekund.

Kod odczytuje komunikat z kolejki w dwóch krokach. Po wywołaniu [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) metody uzyskasz następny komunikat w kolejce. Komunikat zwrócony z programu `GetMessage` stał się niewidoczny dla każdego innego kodu odczytującego komunikaty z tej kolejki. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metodę.

W poniższym przykładzie przeczytasz trzy komunikaty w kolejce, a następnie zaczekaj 10 sekund (limit czasu niewglądu). Następnie należy ponownie przeczytać trzy komunikaty, usuwając komunikaty po ich przeczytaniu przez wywołanie `DeleteMessage` . Jeśli spróbujesz odczytać kolejkę po usunięciu wiadomości, `$queueMessage` zostanie ona zwrócona jako `$null` .

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie znajdujące się w niej komunikaty, wywołaj `Remove-AzStorageQueue` polecenie cmdlet. Poniższy przykład pokazuje, jak usunąć określoną kolejkę używaną w tym ćwiczeniu przy użyciu `Remove-AzStorageQueue` polecenia cmdlet.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w tym ćwiczeniu, Usuń grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku usuwa utworzone konto magazynu i grupę zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz informacje o podstawowym zarządzaniu Queue Storage za pomocą programu PowerShell, w tym o tym, jak:

> [!div class="checklist"]
>
> - Tworzenie kolejki
> - Pobieranie kolejki
> - Dodawanie komunikatu
> - Przeczytaj następną wiadomość
> - Usuwanie wiadomości
> - Usuwanie kolejki

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Polecenia cmdlet Microsoft Azure PowerShell Storage

- [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

- [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
