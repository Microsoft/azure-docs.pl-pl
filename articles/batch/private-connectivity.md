---
title: Używanie prywatnych punktów końcowych z kontami usługi Azure Batch
description: Dowiedz się, jak połączyć się prywatnie z kontem Azure Batch przy użyciu prywatnych punktów końcowych.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920007"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Używanie prywatnych punktów końcowych z kontami usługi Azure Batch

Domyślnie [konta Azure Batch](accounts.md) mają publiczny punkt końcowy i są dostępne publicznie. Usługa Batch oferuje możliwość tworzenia prywatnych kont usługi Batch, a także wyłączania dostępu do sieci publicznej.

Za pomocą [prywatnego linku platformy Azure](../private-link/private-link-overview.md)można nawiązać połączenie z kontem Azure Batch za pośrednictwem [prywatnego punktu końcowego](../private-link/private-endpoint-overview.md). Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Następnie możesz ograniczyć dostęp do konta Azure Batch za pośrednictwem prywatnych adresów IP.

Link prywatny umożliwia użytkownikom dostęp do konta Azure Batch z poziomu sieci wirtualnej lub z dowolnej równorzędnej sieci wirtualnej. Zasoby mapowane do linku prywatnego są również dostępne lokalnie przez prywatną komunikację równorzędną za pośrednictwem sieci VPN lub [Azure ExpressRoute](../expressroute/expressroute-introduction.md). Można nawiązać połączenie z kontem Azure Batch skonfigurowanym za pomocą linku prywatnego przy użyciu [metody zatwierdzania automatyczne lub ręczne](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

> [!IMPORTANT]
> Obsługa łączności prywatnej w Azure Batch jest obecnie dostępna dla wszystkich regionów, z wyjątkiem Niemiec Central, Niemiec północ, Chiny Wschodnie, Chiny Wschodnie 2, Chiny Północne i Chiny Północne 2.

W tym artykule opisano kroki tworzenia prywatnego konta usługi Batch i uzyskiwania dostępu do niego przy użyciu prywatnego punktu końcowego.

## <a name="azure-portal"></a>Witryna Azure Portal

Wykonaj następujące kroki, aby utworzyć prywatne konto usługi Batch przy użyciu Azure Portal:

1. W okienku **Tworzenie zasobu** wybierz pozycję **Usługa Batch** , a następnie wybierz pozycję **Utwórz**.
2. Wprowadź subskrypcję, grupę zasobów, region i nazwę konta usługi Batch na karcie **podstawowe** , a następnie wybierz pozycję **Dalej: Zaawansowane**.
3. Na karcie **Zaawansowane** ustaw opcję **dostęp do sieci publicznej** na **wyłączony**.
4. W obszarze **Ustawienia** wybierz pozycję **połączenia prywatne punktu końcowego** , a następnie wybierz pozycję **+ prywatny punkt końcowy**.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Połączenia prywatnego punktu końcowego":::
5. W okienku **podstawy** wprowadź lub wybierz subskrypcję, grupę zasobów, nazwę zasobu prywatnego punktu końcowego i szczegóły regionu, a następnie wybierz pozycję **Dalej: zasób**.
6. W okienku **zasobów** Ustaw **typ zasobu** na **Microsoft.Batch/batchAccounts**. Wybierz konto prywatnej partii, do którego chcesz uzyskać dostęp, a następnie wybierz kolejno pozycje **Dalej: Konfiguracja**.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Tworzenie prywatnego punktu końcowego — okienko zasobów":::
7. W okienku **Konfiguracja** wprowadź lub wybierz następujące informacje:
   - **Sieć wirtualna**: Wybierz sieć wirtualną.
   - **Podsieć**: wybierz podsieć.
   - **Integracja z prywatną strefą DNS**: wybierz pozycję **tak**. Aby połączyć się prywatnie z prywatnym punktem końcowym, potrzebny jest rekord DNS. Zalecamy integrację prywatnego punktu końcowego z prywatną strefą DNS. Możesz również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych.
   - **Prywatna strefa DNS strefa**: wybierz pozycję privatelink. \<region\> . batch.azure.com. Prywatna strefa DNS jest określana automatycznie. Nie można go zmienić za pomocą Azure Portal.
8. Wybierz pozycję **Recenzja + Utwórz**, a następnie poczekaj na platformę Azure, aby sprawdzić poprawność konfiguracji.
9. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

Po udostępnieniu prywatnego punktu końcowego można uzyskać dostęp do konta usługi Batch z maszyn wirtualnych w tej samej sieci wirtualnej przy użyciu prywatnego punktu końcowego.

> [!IMPORTANT]
> Wykonywanie operacji poza siecią wirtualną, w której zainicjowano prywatny punkt końcowy, spowoduje wyświetlenie komunikatu "AuthorizationFailure" w witrynie Azure Portal.

Aby wyświetlić adres IP z Azure Portal:

1. Wybierz pozycję **Wszystkie zasoby**.
2. Wyszukaj utworzony wcześniej prywatny punkt końcowy.
3. Wybierz kartę **Przegląd** , aby wyświetlić ustawienia DNS i adresy IP.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Ustawienia DNS i adresy IP prywatnego punktu końcowego":::

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Podczas [tworzenia konta usługi Batch przy użyciu szablonu Azure Resource Manager](quick-create-template.md)należy zmodyfikować szablon, aby ustawić **publicNetworkAccess** jako **wyłączony** , jak pokazano poniżej.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Skonfiguruj strefy DNS

Użyj [prywatnej strefy DNS](../dns/private-dns-privatednszone.md) w podsieci, w której utworzono prywatny punkt końcowy. Skonfiguruj punkty końcowe tak, aby każdy prywatny adres IP był mapowany na wpis DNS.

Podczas tworzenia prywatnego punktu końcowego można zintegrować go z [prywatną strefą DNS](../dns/private-dns-privatednszone.md) na platformie Azure. Jeśli zdecydujesz się użyć [domeny niestandardowej](../dns/dns-custom-domain.md), musisz ją skonfigurować, aby dodać rekordy DNS dla wszystkich prywatnych adresów IP zarezerwowanych dla prywatnego punktu końcowego.

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje dotyczące kosztów związanych z prywatnymi punktami końcowymi, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Bieżące ograniczenia i najlepsze rozwiązania

Podczas tworzenia prywatnego konta w usłudze Partia zadań należy pamiętać o następujących kwestiach:

- Prywatne zasoby punktu końcowego muszą zostać utworzone w tej samej subskrypcji co konto usługi Batch.
- Aby usunąć połączenie prywatne, należy usunąć zasób prywatnego punktu końcowego.
- Po utworzeniu konta w usłudze Batch z dostępem do sieci publicznej nie można zmienić go na prywatny dostęp.
- Rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Batch. Przed dodaniem nowego prywatnego punktu końcowego połączonego z tą prywatną strefą DNS należy ręcznie usunąć rekordy DNS. Jeśli rekordy DNS nie są czyszczone, mogą wystąpić nieoczekiwane problemy z płaszczyzną danych, takie jak awarie danych przez regiony dodane po usunięciu prywatnego punktu końcowego lub usunięciu regionu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [tworzyć pule wsadowe w sieciach wirtualnych](batch-virtual-network.md).
- Dowiedz się, jak [tworzyć pule wsadowe bez publicznych adresów IP](batch-pool-no-public-ip-address.md)
- Dowiedz się, jak [tworzyć pule wsadowe z określonymi publicznymi adresami IP](create-pool-public-ip.md).
- Dowiedz się więcej o [usłudze Azure Private link](../private-link/private-link-overview.md).
