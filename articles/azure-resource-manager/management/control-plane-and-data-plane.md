---
title: Operacje płaszczyzny kontroli i płaszczyzny danych
description: Opisuje różnicę między operacjami płaszczyzny kontroli i płaszczyzny danych. Operacje płaszczyzny kontroli są obsługiwane przez Azure Resource Manager. Operacje płaszczyzny danych są obsługiwane przez usługę.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371273"
---
# <a name="azure-control-plane-and-data-plane"></a>Płaszczyzna danych i płaszczyzna kontroli platformy Azure

Operacje na platformie Azure można podzielić na dwie kategorie — płaszczyzny kontroli i płaszczyzny danych. W tym artykule opisano różnice między tymi dwoma typami operacji.

Za pomocą płaszczyzny kontroli możesz zarządzać zasobami w ramach subskrypcji. Płaszczyzny danych używa się do korzystania z możliwości udostępnianych przez wystąpienie typu zasobu.

Na przykład:

* Można utworzyć maszynę wirtualną za pomocą płaszczyzny kontroli. Po utworzeniu maszyny wirtualnej można korzystać z niej za pomocą operacji płaszczyzny danych, takich jak Remote Desktop Protocol (RDP).

* Konto magazynu można utworzyć za pomocą płaszczyzny kontroli. Za pomocą płaszczyzny danych można odczytywać i zapisywać dane na koncie magazynu.

* Bazę danych Cosmos można utworzyć za pomocą płaszczyzny kontroli. Aby wykonać zapytanie dotyczące danych w bazie danych Cosmos, należy użyć płaszczyzny danych.

## <a name="control-plane"></a>Płaszczyzna sterowania

Wszystkie żądania dotyczące operacji płaszczyzny kontroli są wysyłane do adresu URL Azure Resource Manager. Ten adres URL różni się w środowisku platformy Azure.

* W przypadku globalnego systemu Azure adres URL to `https://management.azure.com` .
* W przypadku Azure Government adres URL to `https://management.usgovcloudapi.net/` .
* W przypadku platformy Azure (Niemcy) adres URL to `https://management.microsoftazure.de/` .
* W przypadku Microsoft Azure Chin, adres URL to `https://management.chinacloudapi.cn` .

Aby sprawdzić, które operacje używają adresu URL Azure Resource Manager, zobacz [interfejs API REST platformy Azure](/rest/api/azure/). Na przykład [operacja tworzenia lub aktualizacji](/rest/api/mysql/databases/createorupdate) dla programu MySQL jest operacją płaszczyzny kontroli, ponieważ adres URL żądania to:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager obsługuje wszystkie żądania płaszczyzny kontroli. Automatycznie stosuje funkcje platformy Azure, które zostały wdrożone w celu zarządzania zasobami, takie jak:

* [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Blokady zarządzania](lock-resources.md)
* [Dzienniki aktywności](view-activity-logs.md)

Po uwierzytelnieniu żądania Azure Resource Manager wysyła je do dostawcy zasobów, co kończy operację.

Płaszczyzna kontroli obejmuje dwa scenariusze obsługi żądań — "zielone pole" i "brązowe pole". Zielone pole odwołuje się do nowych zasobów. Pole brązowy odwołuje się do istniejących zasobów. Podczas wdrażania zasobów Azure Resource Manager rozumie, kiedy należy utworzyć nowe zasoby i kiedy zaktualizować istniejące zasoby. Nie musisz martwić się, że zostaną utworzone identyczne zasoby.

## <a name="data-plane"></a>Płaszczyzna danych

Żądania operacji płaszczyzny danych są wysyłane do punktu końcowego, który jest specyficzny dla danego wystąpienia. Na przykład [Operacja wykrywania języka](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) w Cognitive Services jest operacją płaszczyzny danych, ponieważ adres URL żądania to:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Operacje płaszczyzny danych nie są ograniczone do interfejsu API REST. Mogą wymagać dodatkowych poświadczeń, takich jak logowanie do maszyny wirtualnej lub serwera bazy danych.

Funkcje, które wymuszają zarządzanie i nadzór, mogą nie dotyczyć operacji płaszczyzny danych. Należy wziąć pod uwagę różne sposoby, w których użytkownicy współpracują z rozwiązaniami. Na przykład blokada uniemożliwiająca użytkownikom usuwanie bazy danych nie uniemożliwia użytkownikom usuwania danych za pośrednictwem zapytań.

Aby zarządzać operacjami płaszczyzny danych, można użyć niektórych zasad. Aby uzyskać więcej informacji, zobacz [tryby dostawcy zasobów (wersja zapoznawcza) w Azure Policy](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem Azure Resource Manager, zobacz [co to jest Azure Resource Manager?](overview.md)

* Aby dowiedzieć się więcej na temat wpływu definicji zasad na nowe zasoby i istniejące zasoby, zobacz [ocenę wpływu nowej definicji Azure Policy](../../governance/policy/concepts/evaluate-impact.md).
