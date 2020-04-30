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
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082451"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Szybki Start: tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

W tym przewodniku szybki start utworzysz centrum powiadomień przy użyciu interfejsu wiersza polecenia platformy Azure. W pierwszej sekcji przedstawiono kroki umożliwiające utworzenie przestrzeni nazw centrum powiadomień i zazapytanie informacji o zasadach dostępu dla tej przestrzeni nazw. Druga sekcja zawiera kroki umożliwiające utworzenie centrum powiadomień w istniejącej przestrzeni nazw.  Dowiesz się również, jak utworzyć niestandardowe zasady dostępu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs wymaga wersji 2.0.67 lub nowszej interfejsu wiersza polecenia platformy Azure. Uruchom `az --version` , aby znaleźć wersję i zainstalowane biblioteki zależne. Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Zaloguj się.

   Zaloguj się przy użyciu polecenia [AZ login](/cli/azure/reference-index#az-login) , jeśli używasz lokalnej instalacji interfejsu wiersza polecenia.

    ```azurecli-interactive
    az login
    ```

    Postępuj zgodnie z instrukcjami wyświetlanymi w terminalu, aby ukończyć proces uwierzytelniania.

2. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

   Aby uruchomić polecenie interfejsu wiersza polecenia platformy Azure dla centrów powiadomień, zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure [dla Notification Hubs](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Utwórz grupę zasobów.

   Centra powiadomień platformy Azure, takie jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

   W tym przewodniku szybki start Utwórz grupę zasobów o nazwie *spnhubrg* w lokalizacji *Wschodnie* przy użyciu następującego polecenia [AZ Group Create](/cli/azure/group#az-group-create) :

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Tworzenie przestrzeni nazw centrum powiadomień

1. Tworzenie przestrzeni nazw dla centrów powiadomień

   Przestrzeń nazw zawiera jeden lub więcej centrów, a nazwa musi być unikatowa we wszystkich subskrypcjach platformy Azure.  Aby sprawdzić dostępność danej przestrzeni nazw usługi, użyj polecenia [AZ Notification-Hub Namespace Check-Availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .  Uruchom polecenie [AZ Notification-Hub Namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) , aby utworzyć przestrzeń nazw.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Wyświetl listę kluczy i parametrów połączenia dla zasad dostępu do obszaru nazw.

   Zasady dostępu o nazwie **RootManageSharedAccessKey** są tworzone automatycznie dla nowej przestrzeni nazw.  Każda zasada dostępu ma dwa zestawy kluczy i parametrów połączenia.  Aby wyświetlić listę kluczy i parametrów połączenia dla przestrzeni nazw, uruchom polecenie [AZ Notification-Hub Namespace-Keys list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Tworzenie centrów powiadomień

1. Utwórz swoje pierwsze centrum powiadomień.

   Centrum powiadomień można teraz utworzyć w nowej przestrzeni nazw.  Uruchom polecenie [AZ Notification-Hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) , aby utworzyć centrum powiadomień.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Utwórz drugie centrum powiadomień.

   W jednej przestrzeni nazw można utworzyć wiele centrów powiadomień.  Aby utworzyć drugie centrum powiadomień w tej samej przestrzeni nazw, uruchom ponownie `az notification-hub create` polecenie przy użyciu innej nazwy centrum.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Korzystanie z zasad dostępu

1. Utwórz nową regułę autoryzacji dla centrum powiadomień.

   Zasady dostępu są tworzone automatycznie dla każdego nowego centrum powiadomień.  Aby utworzyć i dostosować własne zasady dostępu, użyj polecenia [AZ Notification-Hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) .

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Wyświetlanie listy zasad dostępu dla centrum powiadomień.

   Aby zbadać, jakie zasady dostępu istnieją dla centrum powiadomień, użyj polecenia [AZ Notification-Hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) .

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Nie należy używać zasad **DefaultFullSharedAccessSignatureymi** w aplikacji. Jest on przeznaczony tylko do użycia w zapleczu.  Używaj tylko zasad dostępu **nasłuchiwania** w aplikacji klienckiej.

3. Wyświetlanie listy kluczy i parametrów połączenia dla zasad dostępu centrum powiadomień

   Istnieją dwa zestawy kluczy i parametrów połączenia dla każdej zasady dostępu.  Będą one potrzebne później do obsługi powiadomień wypychanych.  Aby wyświetlić listę kluczy i parametrów połączeń dla zasad dostępu centrum powiadomień, użyj polecenia [AZ Notification-Hub Authorization-Keys list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Przestrzeń nazw centrum powiadomień](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) i [centrum powiadomień](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mają oddzielne zasady dostępu.  Upewnij się, że używasz poprawnego odwołania do interfejsu wiersza polecenia platformy Azure podczas wykonywania zapytań dotyczących kluczy i parametrów połączenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Zobacz także

Odkryj pełne możliwości zarządzania centrami powiadomień za pomocą interfejsu wiersza polecenia platformy Azure.

* [Notification Hubs pełną listę referencyjną interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/notification-hub/notification-hub)
* [Lista odwołań do interfejsu wiersza polecenia platformy Azure dla Notification Hubs przestrzeni nazw](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Lista odwołań do interfejsu wiersza polecenia platformy Azure Notification Hubs reguły autoryzacji](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Lista odwołań do interfejsu wiersza polecenia platformy Azure dla usługi Notification Hubs](/cli/azure/ext/notification-hub/notification-hub/credential)
