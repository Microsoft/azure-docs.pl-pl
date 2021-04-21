---
title: Szybki start — tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: d8400eb051c09fac4cb88863ad2fac12d2ca0a1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789887"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Szybki start: tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

W tym przewodniku Szybki start utworzysz centrum powiadomień przy użyciu interfejsu wiersza polecenia platformy Azure. Pierwsza sekcja zawiera instrukcje tworzenia Notification Hubs nazw. Druga sekcja zawiera instrukcje tworzenia centrum powiadomień w istniejącej przestrzeni nazw. Dowiesz się również, jak utworzyć niestandardowe zasady dostępu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs interfejs wiersza polecenia platformy Azure wymaga wersji 2.0.67 lub nowszej. Uruchom polecenie [az version](/cli/azure/reference-index#az_version), aby znaleźć zainstalowane wersje i biblioteki zależne. Aby uaktualnić do najnowszej wersji, uruchom polecenie [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure Notification Hubs, podobnie jak wszystkie zasoby platformy Azure, musi zostać wdrożona w grupie zasobów.  Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.  Zobacz [Co to jest Azure Resource Manager](../azure-resource-manager/management/overview.md) aby dowiedzieć się więcej o grupach zasobów.

W tym przewodniku Szybki start utwórz grupę zasobów o nazwie **spnhubrg** w lokalizacji **eastus** za pomocą następującego [polecenia az group create.](/cli/azure/group#az_group_create)

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Tworzenie Notification Hubs nazw

1. Utwórz przestrzeń nazw dla centrów powiadomień.

   Przestrzeń nazw zawiera co najmniej jeden koncentrator, a nazwa musi być unikatowa we wszystkich subskrypcjach platformy Azure i mieć co najmniej sześć znaków. Aby sprawdzić dostępność nazwy, użyj [polecenia az notification-hub namespace check-availability.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Interfejs wiersza polecenia platformy Azure odpowiada na żądanie dotyczące dostępności, wyświetlając następujące dane wyjściowe konsoli:

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

   Zwróć uwagę na drugi wiersz odpowiedzi interfejsu wiersza polecenia platformy Azure, `"isAvailable": true` . Ten wiersz `false` odczytuje, jeśli żądana nazwa określona dla przestrzeni nazw jest niedostępny. Po potwierdzeniu dostępności nazwy uruchom polecenie [az notification-hub namespace create,](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) aby utworzyć przestrzeń nazw.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Jeśli polecenie podane w poleceniu jest niedostępne lub nie spełnia reguł nazewnictwa i ograniczeń dotyczących zasobów platformy Azure, interfejs wiersza polecenia platformy Azure odpowie `--name` `az notification-hub namespace create` następującymi danych wyjściowych konsoli: [](../azure-resource-manager/management/resource-name-rules.md)

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Jeśli pierwsza nazwa, która była próbna, nie powiedzie się, wybierz inną nazwę dla nowej przestrzeni nazw i ponownie `az notification-hub namespace create` uruchom polecenie.

   > [!NOTE]
   > W tym kroku należy zastąpić wartość parametru w każdym poleceniu interfejsu wiersza polecenia platformy `--namespace` Azure skopiowaną z tego przewodnika Szybki start.

2. Pobierz listę przestrzeni nazw.

   Aby wyświetlić szczegółowe informacje o nowej przestrzeni nazw, użyj [polecenia az notification-hub namespace list.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) Parametr `--resource-group` jest opcjonalny, jeśli chcesz wyświetlić wszystkie przestrzenie nazw dla subskrypcji.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Tworzenie centrów powiadomień

1. Utwórz pierwsze centrum powiadomień.

   W nowej przestrzeni nazw można teraz utworzyć co najmniej jedno centrum powiadomień. Uruchom polecenie [az notification-hub create,](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) aby utworzyć centrum powiadomień.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Utwórz drugie centrum powiadomień.

   W jednej przestrzeni nazw można utworzyć wiele centrów powiadomień. Aby utworzyć drugie centrum powiadomień w tej samej przestrzeni nazw, uruchom polecenie `az notification-hub create` ponownie przy użyciu innej nazwy centrum.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Pobierz listę centrów powiadomień.

   Interfejs wiersza polecenia platformy Azure zwraca komunikat o sukcesie lub błędzie dla każdego wykonanego polecenia. Jednak możliwość wykonywania zapytań o listę centrów powiadomień zapewnia. W tym celu zaprojektowano [polecenie az notification-hub list.](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list)

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Praca z zasadami dostępu

1. Usługa Azure Notification Hubs używa [zabezpieczeń sygnatury dostępu współdzielonych](./notification-hubs-push-notification-security.md) za pomocą zasad dostępu. Dwie zasady są tworzone automatycznie podczas tworzenia centrum powiadomień. Parametry połączenia z tych zasad są potrzebne do skonfigurowania powiadomień wypychanych. Polecenie [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) zawiera listę nazw zasad i ich odpowiednich grup zasobów.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Nie używaj zasad _DefaultFullSharedAccessSignature_ w aplikacji. Te zasady mają być używane tylko w zaciemniach. Używaj tylko `Listen` zasad dostępu w aplikacji klienckiej.

2. Jeśli chcesz utworzyć dodatkowe reguły autoryzacji o znaczących nazwach, możesz utworzyć i dostosować własne zasady dostępu za pomocą polecenia [az notification-hub authorization-rule create.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) Parametr `--rights` to rozdzielana spacjami lista uprawnień, które chcesz przypisać.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Istnieją dwa zestawy kluczy i parametry połączenia dla każdej zasady dostępu. Będą potrzebne później do [skonfigurowania centrum powiadomień.](./configure-notification-hub-portal-pns-settings.md) Aby wyświetlić listę kluczy i parametry połączenia dla Notification Hubs dostępu, użyj [polecenia az notification-hub authorization-rule list-keys.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Przestrzeń [Notification Hubs i](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) centrum [powiadomień mają](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) oddzielne zasady dostępu. Upewnij się, że używasz poprawnego odwołania interfejsu wiersza polecenia platformy Azure podczas wykonywania zapytań o klucze i parametry połączenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, użyj polecenia [az group delete:](/cli/azure/group)

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Następne kroki

* W tym przewodniku Szybki start utworzono centrum powiadomień. Aby dowiedzieć się, jak skonfigurować centrum przy użyciu ustawień systemu powiadomień platformy (PNS), zobacz [Konfigurowanie powiadomień wypychanych w centrum powiadomień](configure-notification-hub-portal-pns-settings.md)

* Odkryj rozbudowane możliwości zarządzania centrami powiadomień za pomocą interfejsu wiersza polecenia platformy Azure:

  [Notification Hubs pełna lista referencyjna](/cli/azure/ext/notification-hub/notification-hub)

  [Notification Hubs listy odwoływnych przestrzeni nazw](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Notification Hubs odwoływać się do reguł autoryzacji](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs listy odwoływać się do poświadczeń](/cli/azure/ext/notification-hub/notification-hub/credential)