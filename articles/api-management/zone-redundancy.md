---
title: Obsługa strefy dostępności dla usługi Azure API Management
description: Dowiedz się, jak zwiększyć odporność wystąpienia usługi Azure API Management Service w regionie, włączając nadmiarowość stref.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559163"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Obsługa strefy dostępności dla usługi Azure API Management 

W tym artykule pokazano, jak włączyć nadmiarowość strefy dla wystąpienia API Management przy użyciu Azure Portal. [Nadmiarowość strefy zapewnia](../availability-zones/az-overview.md#availability-zones) odporność i wysoką dostępność wystąpienia usługi w określonym regionie (lokalizacji) platformy Azure. Dzięki nadmiarowości strefy brama i płaszczyzna sterowania wystąpienia usługi API Management (interfejs API Zarządzanie, portal dla deweloperów, konfiguracja usługi Git) są replikowane między centrami danych w fizycznie oddzielonych strefach, dzięki czemu jest odporna na awarię strefy. 

API Management obsługuje również wdrożenia w wielu [regionach,](api-management-howto-deploy-multi-region.md)co pomaga zmniejszyć opóźnienie żądań odbierane przez geograficznie rozproszonych odbiorców interfejsu API i zwiększa dostępność składnika bramy, jeśli jeden region przejdzie w tryb offline. Kombinacja stref dostępności w celu zapewnienia nadmiarowości w obrębie regionu i wdrożeń w wielu regionach w celu zwiększenia dostępności bramy w przypadku regionalnej 30. API Management niezawodności i wydajności wystąpienia.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Obsługiwane regiony

Konfigurowanie API Management nadmiarowości strefy jest obecnie obsługiwane w następujących regionach świadczenia usługi Azure.

* Australia Wschodnia
* Brazylia Południowa
* Kanada Środkowa
* Indie Środkowe
* East US
* Wschodnie stany USA 2
* Francja Środkowa
* Japonia Wschodnia
* South Central US
* Southeast Asia
* Południowe Zjednoczone Królestwo
* Zachodnie stany USA 2
* Zachodnie stany USA 3

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz [Tworzenie API Management usługi](get-started-create-service-instance.md). Wybierz warstwę usługi Premium.
* Jeśli wystąpienie API Management jest wdrożone w [](api-management-using-with-vnet.md)sieci wirtualnej, upewnij się, że sieć wirtualna, podsieć i publiczny adres IP zostały ustawione w dowolnej nowej lokalizacji, w której planujesz włączyć nadmiarowość stref.

## <a name="enable-zone-redundancy---portal"></a>Włączanie nadmiarowości strefy — portal

W portalu opcjonalnie włącz nadmiarowość stref podczas dodawania lokalizacji do usługi API Management lub aktualizowania konfiguracji istniejącej lokalizacji.

1. W Azure Portal przejdź do usługi API Management i wybierz **pozycję Lokalizacje** w menu.
1. Wybierz istniejącą lokalizację lub wybierz **pozycję + Dodaj** na górnym pasku. Lokalizacja musi obsługiwać [strefy dostępności](#supported-regions).
1. Wybierz liczbę jednostek **[skalowania](upgrade-and-scale.md)** w lokalizacji.
1. W **strefie dostępności** wybierz co najmniej jedną strefę. Liczba wybranych jednostek musi być równomiernie rozsyłana w różnych strefach dostępności. Jeśli na przykład wybrano 3 jednostki, wybierz 3 strefy, aby każda strefa hostuje jedną jednostkę.
1. Jeśli wystąpienie API Management jest wdrożone w sieci [wirtualnej,](api-management-using-with-vnet.md)skonfiguruj ustawienia sieci wirtualnej w tej lokalizacji. Wybierz istniejącą sieć wirtualną, podsieć i publiczny adres IP, które są dostępne w tej lokalizacji.
1. Wybierz **pozycję Zastosuj,** a następnie wybierz **pozycję Zapisz.**

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Włączanie nadmiarowości strefowej":::

> [!IMPORTANT]
> Publiczny adres IP w lokalizacji zmienia się po włączeniu, dodaniu lub usunięciu stref dostępności. Podczas aktualizowania stref dostępności w regionie przy użyciu ustawień sieciowych należy skonfigurować inny zasób publicznego adresu IP niż skonfigurowany wcześniej.

> [!NOTE]
> Zastosowanie zmiany do wystąpienia API Management może potrwać od 15 do 45 minut.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na [temat wdrażania wystąpienia usługi Azure API Management Service w wielu regionach platformy Azure.](api-management-howto-deploy-multi-region.md)
* Nadmiarowość stref można również włączyć przy użyciu [Azure Resource Manager szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones).
* Dowiedz się więcej o [usługach platformy Azure, które obsługują strefy dostępności.](../availability-zones/az-region.md)
* Dowiedz się więcej o budowania [niezawodności na platformie](/azure/architecture/framework/resiliency/overview) Azure.