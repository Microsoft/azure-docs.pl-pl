---
title: Pobieranie parametrów połączenia — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera instrukcje dotyczące uzyskiwania parametrów połączenia, które mogą być używane przez klientów do łączenia się z usługą Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86537194"
---
# <a name="get-an-event-hubs-connection-string"></a>Pobierz parametry połączenia Event Hubs

Aby użyć Event Hubs, należy utworzyć Event Hubs przestrzeni nazw. Przestrzeń nazw jest kontenerem określania zakresu dla wielu centrów zdarzeń lub tematów Kafka. Ta przestrzeń nazw zapewnia unikatową [nazwę FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po utworzeniu przestrzeni nazw można uzyskać parametry połączenia wymagane do komunikowania się z Event Hubs.

W parametrach połączenia dla usługi Azure Event Hubs są osadzone następujące składniki,

* FQDN = nazwa FQDN utworzonej przestrzeni nazw EventHubs (zawiera nazwę przestrzeni nazw EventHubs, a następnie servicebus.windows.net)
* SharedAccessKeyName = nazwa wybrana dla kluczy SAS aplikacji
* SharedAccessKey = wygenerowana wartość klucza.

Szablon parametrów połączenia wygląda jak
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Przykładowe parametry połączenia mogą wyglądać jak `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

W tym artykule przedstawiono różne sposoby uzyskiwania parametrów połączenia.

## <a name="get-connection-string-from-the-portal"></a>Pobierz parametry połączenia z portalu
1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
2. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie. 
3. Wybierz **Event Hubs** w sekcji **Analiza** . 
4. Na liście centrów zdarzeń wybierz centrum zdarzeń.
6. Na stronie **obszar nazw Event Hubs** wybierz pozycję **zasady dostępu współdzielonego** w menu po lewej stronie.

    ![Element menu zasad dostępu współdzielonego](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Wybierz **zasady dostępu współdzielonego** na liście zasad. Wartość domyślna to o nazwie: **RootManageSharedAccessPolicy**. Można dodać zasady z odpowiednimi uprawnieniami (odczyt, zapis) i korzystać z tych zasad. 

    ![Event Hubs zasad dostępu współdzielonego](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Wybierz przycisk **Kopiuj** obok pola **Parametry połączenia — klucz podstawowy** . 

    ![Event Hubs — uzyskiwanie parametrów połączenia](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Pobieranie parametrów połączenia z Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można użyć [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) , aby pobrać parametry połączenia dla konkretnej nazwy zasad/reguły, jak pokazano poniżej:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Pobieranie parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure
Aby uzyskać parametry połączenia dla przestrzeni nazw, można użyć następującego elementu:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Można też użyć poniższego elementu, aby uzyskać parametry połączenia dla jednostki EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia platformy Azure dla Event Hubs, zobacz [interfejs wiersza polecenia platformy Azure dla Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](./event-hubs-about.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
