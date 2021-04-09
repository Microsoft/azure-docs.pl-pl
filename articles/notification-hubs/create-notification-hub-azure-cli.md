---
title: Szybki Start — tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak utworzyć centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c565be3aea43fc4dc86fd793e0dc2dea0b08d4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563753"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Szybki Start: tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

W tym przewodniku szybki start utworzysz centrum powiadomień przy użyciu interfejsu wiersza polecenia platformy Azure. Pierwsza sekcja zawiera kroki umożliwiające utworzenie Notification Hubs przestrzeni nazw. Druga sekcja zawiera kroki umożliwiające utworzenie centrum powiadomień w istniejącej przestrzeni nazw. Dowiesz się również, jak utworzyć niestandardowe zasady dostępu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs wymaga wersji 2.0.67 lub nowszej interfejsu wiersza polecenia platformy Azure. Uruchom polecenie [az version](/cli/azure/reference-index#az_version), aby znaleźć zainstalowane wersje i biblioteki zależne. Aby uaktualnić do najnowszej wersji, uruchom polecenie [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Notification Hubs platformy Azure, podobnie jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów.  Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.  Aby dowiedzieć się więcej na temat grup zasobów, zobacz [co to jest Azure Resource Manager](../azure-resource-manager/management/overview.md) .

W tym przewodniku szybki start Utwórz grupę zasobów o nazwie **spnhubrg** w lokalizacji **Wschodnie** przy użyciu następującego polecenia [AZ Group Create](/cli/azure/group#az-group-create) .

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Tworzenie przestrzeni nazw Notification Hubs

1. Utwórz przestrzeń nazw dla centrów powiadomień.

   Przestrzeń nazw zawiera jeden lub więcej centrów, a nazwa musi być unikatowa we wszystkich subskrypcjach platformy Azure i mieć co najmniej sześć znaków. Aby sprawdzić dostępność nazwy, użyj polecenia [AZ Notification-Hub Namespace Check-Availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Interfejs wiersza polecenia platformy Azure reaguje na żądanie dostępności, wyświetlając następujące dane wyjściowe konsoli:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Zwróć uwagę na drugi wiersz odpowiedzi interfejsu wiersza polecenia platformy Azure `"isAvailable": true` . Ten wiersz służy `false` do odczytywania, czy poprzednia nazwa określona dla przestrzeni nazw jest niedostępna. Po potwierdzeniu dostępności nazwy Uruchom polecenie [AZ Notification-Hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) , aby utworzyć przestrzeń nazw.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Jeśli `--name` podane `az notification-hub namespace create` polecenie jest niedostępne lub nie spełnia [reguł nazewnictwa i ograniczeń dotyczących zasobów platformy Azure](../azure-resource-manager/management/resource-name-rules.md), interfejs wiersza polecenia platformy Azure reaguje na następujące dane wyjściowe konsoli:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Jeśli pierwsza wypróbowana nazwa nie powiedzie się, wybierz inną nazwę dla nowej przestrzeni nazw i `az notification-hub namespace create` ponownie uruchom polecenie.

   > [!NOTE]
   > W tym kroku należy zastąpić wartość `--namespace` parametru w każdym poleceniu interfejsu wiersza polecenia platformy Azure kopiowanym z tego przewodnika Szybki Start.

2. Pobierz listę przestrzeni nazw.

   Aby wyświetlić szczegółowe informacje o nowej przestrzeni nazw, użyj polecenia [AZ Notification-Hub Namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) . Ten `--resource-group` parametr jest opcjonalny, jeśli chcesz zobaczyć wszystkie przestrzenie nazw dla subskrypcji.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Tworzenie centrów powiadomień

1. Utwórz swoje pierwsze centrum powiadomień.

   W nowej przestrzeni nazw można teraz utworzyć co najmniej jeden koncentrator powiadomień. Uruchom polecenie [AZ Notification-Hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) , aby utworzyć centrum powiadomień.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Utwórz drugie centrum powiadomień.

   W jednej przestrzeni nazw można utworzyć wiele centrów powiadomień. Aby utworzyć drugie centrum powiadomień w tej samej przestrzeni nazw, uruchom `az notification-hub create` ponownie polecenie przy użyciu innej nazwy centrum.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Pobierz listę centrów powiadomień.

   Interfejs wiersza polecenia platformy Azure zwraca komunikat o powodzeniu lub błędzie z każdym wykonanym poleceniem; jednak możliwość wykonywania zapytań o listę centrów powiadomień to pewność. Polecenie [AZ Notification-Hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) zostało zaprojektowane do tego celu.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Korzystanie z zasad dostępu

1. Usługa Azure Notification Hubs używa [zabezpieczeń sygnatury dostępu współdzielonego](./notification-hubs-push-notification-security.md) za pomocą zasad dostępu. Dwie zasady są tworzone automatycznie podczas tworzenia centrum powiadomień. Parametry połączenia z tych zasad są konieczne do konfigurowania powiadomień wypychanych. Polecenie [AZ Notification-Hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) zawiera listę nazw zasad i odpowiednich grup zasobów.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Nie należy używać zasad _DefaultFullSharedAccessSignatureymi_ w aplikacji. Ta zasada jest przeznaczona do użycia tylko w zapleczu. Używaj tylko `Listen` zasad dostępu w aplikacji klienckiej.

2. Jeśli chcesz utworzyć dodatkowe reguły autoryzacji z istotnymi nazwami, możesz utworzyć i dostosować własne zasady dostępu za pomocą polecenia [AZ Notification-Hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) . `--rights`Parametr jest listą rozdzielaną spacją uprawnień, które chcesz przypisać.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Istnieją dwa zestawy kluczy i parametrów połączenia dla każdej zasady dostępu. Będziesz ich potrzebować później, aby [skonfigurować centrum powiadomień](./configure-notification-hub-portal-pns-settings.md). Aby wyświetlić listę kluczy i parametrów połączenia dla zasad dostępu Notification Hubs, użyj polecenia [AZ Notification-Hub Authorization-Keys list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Przestrzeń nazw Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) i [centrum powiadomień](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mają oddzielne zasady dostępu. Upewnij się, że używasz poprawnego odwołania do interfejsu wiersza polecenia platformy Azure podczas wykonywania zapytań dotyczących kluczy i parametrów połączenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group) .

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Następne kroki

* W tym przewodniku Szybki start utworzono centrum powiadomień. Aby dowiedzieć się, jak skonfigurować centrum przy użyciu ustawień systemu powiadomień platformy (PNS), zobacz [Konfigurowanie powiadomień wypychanych w centrum powiadomień](configure-notification-hub-portal-pns-settings.md)

* Odkryj szerokie możliwości zarządzania centrami powiadomień za pomocą interfejsu wiersza polecenia platformy Azure:

  [Lista pełnych odwołań Notification Hubs](/cli/azure/ext/notification-hub/notification-hub)

  [Lista odwołań Notification Hubs przestrzeni nazw](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Lista odwołań reguł autoryzacji Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Lista odwołań poświadczeń Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/credential)