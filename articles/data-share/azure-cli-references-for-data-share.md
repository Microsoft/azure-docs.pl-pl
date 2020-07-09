---
title: Dokumentacja interfejsu wiersza polecenia platformy Azure dla udziału danych platformy Azure
description: Strona docelowa interfejsu wiersza polecenia platformy Azure dla udziału danych platformy Azure
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300619"
---
# <a name="azure-cli-for-azure-data-share"></a>Interfejs wiersza polecenia platformy Azure dla udziału danych platformy Azure

Interfejs wiersza polecenia platformy Azure ([Azure CLI](/cli/azure/what-is-azure-cli)) to zestaw poleceń służących do tworzenia zasobów platformy Azure i zarządzania nimi.  Jest ona dostępna dla wielu usług platformy Azure, w tym udziału danych platformy Azure.  Istnieje ponad 65 różnych poleceń dla udziału danych!  Te polecenia umożliwiają efektywne działanie usługi z poziomu wiersza polecenia.

## <a name="references-for-data-share"></a>Odwołania do udziału danych

Wszystkie polecenie interfejsu wiersza polecenia platformy Azure dla udziału danych platformy Azure są obecnie rozszerzeniami interfejsu wiersza polecenia platformy Azure.  Rozszerzenie zapewnia dostęp do poleceń eksperymentalnych i wstępnych.  Dowiedz się więcej na temat odwołań do rozszerzeń w temacie [używanie rozszerzeń przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

|Dokumentacja interfejsu wiersza polecenia platformy Azure |Opis
|-|-|-|
| [AZ datashare](/cli/azure/ext/datashare/datashare) | Wszystkie polecenia do zarządzania udziałem danych platformy Azure.
| [AZ datashare Account](/cli/azure/ext/datashare/datashare/account) | Polecenia służące do zarządzania kontami udziałów danych platformy Azure.
| [AZ datashare Consumer](/cli/azure/ext/datashare/datashare/consumer) | Polecenia dla klientów służące do zarządzania udziałem danych platformy Azure.
| [AZ datashare DataSet](/cli/azure/ext/datashare/datashare/dataset) | Polecenia służące dostawcom do zarządzania zestawami danych Azure Data Share.
| [AZ datashare zaproszenia](/cli/azure/ext/datashare/datashare/invitation) | Polecenia dla klientów służące do zarządzania zaproszeniami udziału danych platformy Azure.
| [AZ datashare Provider-Share-Subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Polecenia dla dostawców do zarządzania subskrypcjami udziałów danych platformy Azure.
| [AZ datashare Synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | Polecenia służące do zarządzania synchronizacją udziałów danych platformy Azure.
| [AZ datashare Synchronization-Setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Polecenia służące dostawcom do zarządzania ustawieniami synchronizacji udziałów danych platformy Azure.

## <a name="reference-examples"></a>Przykłady odwołań

Przykłady są dostarczane z każdym odwołaniem do interfejsu wiersza polecenia platformy Azure. Mimo że można także wykonać te zadania za pośrednictwem Azure Portal, przy użyciu interfejsu wiersza polecenia platformy Azure wymagane jest pojedynczy wiersz poleceń.  Oto kilka bloków kodu, dzięki którym możesz zastanowić się, jak łatwo można korzystać z interfejsu wiersza polecenia platformy Azure.

Aby można było korzystać z udziału danych platformy Azure, najpierw potrzebna jest Grupa zasobów.  Grupy zasobów platformy Azure są proste do tworzenia i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

Tworzenie konta udziału danych jest proste.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Zobacz także

* [Rozpocznij pracę z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) , aby dowiedzieć się więcej na temat instalacji i logowania.

* Odkryj dodatkowe odwołania do [podstawowych](/cli/azure/reference-index) i [rozszerzeń](/cli/azure/azure-cli-extensions-list) w dokumentacji interfejsu wiersza polecenia platformy Azure.
