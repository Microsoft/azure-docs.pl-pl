---
title: Warstwy i funkcje usługi rejestru
description: Dowiedz się więcej o funkcjach i limitach (limitach przydziału) w warstwach usług Podstawowa, Standardowa i Premium (SKU) Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 323d36fe022d8b8e9618b8beb1facae93d22df4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781257"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry usług

Azure Container Registry jest dostępna w wielu warstwach usług (nazywanych również jednostkami SKU). Te warstwy zapewniają przewidywalne ceny i kilka opcji dostosowania do wzorców pojemności i użycia prywatnego rejestru platformy Docker na platformie Azure.

| Warstwa | Opis |
| --- | ----------- |
| **Podstawowa** | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Rejestry podstawowe mają takie same możliwości programowe jak w wersjach [][container-registry-delete]Standardowa i Premium (takie jak integracja uwierzytelniania usługi [Azure Active Directory,](container-registry-authentication.md#individual-login-with-azure-ad)usuwanie obrazów i [elementów webhook).][container-registry-webhook] Jednak uwzględniona przepływność magazynu i obrazu jest najbardziej odpowiednia dla scenariuszy mniejszego użycia. |
| **Standardowa** | Rejestry w standardowych usługach oferują takie same możliwości jak rejestry w chmurze Podstawowa z większą uwzględnioną przepływnością magazynu i obrazu. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Rejestry w chmurze w chmurze zapewniają największą ilość dołączonego magazynu i operacji współbieżnych, umożliwiając scenariusze o dużej ilości danych. Oprócz większej przepływności obrazu, wersja [][container-registry-geo-replication] Premium dodaje funkcje, takie jak replikacja geograficzna do zarządzania pojedynczym rejestrem w wielu regionach, [](container-registry-content-trust.md) zaufanie do zawartości dla podpisywania tagów obrazów, [](container-registry-private-link.md) łącze prywatne z prywatnymi punktami końcowymi w celu ograniczenia dostępu do rejestru. |

Wszystkie warstwy Podstawowa, Standardowa i Premium zapewniają te same możliwości programowe. Wszystkie one również korzystają z magazynu [obrazów zarządzanego][container-registry-storage] w całości przez platformę Azure. Wybranie warstwy wyższego poziomu zapewnia większą wydajność i skalę. W przypadku wielu warstw usług możesz zacząć korzystać z warstwy Podstawowa, a następnie konwertować na warstwy Standardowa i Premium w przypadku wzrostu użycia rejestru.

## <a name="service-tier-features-and-limits"></a>Funkcje i limity warstwy usług

W poniższej tabeli przedstawiono szczegółowo funkcje i limity rejestru w warstwach usług Podstawowa, Standardowa i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Zmienianie warstw

Warstwę usług rejestru można zmienić za pomocą interfejsu wiersza polecenia platformy Azure lub w Azure Portal. Możesz swobodnie przechodzić między warstwami, o ile przełączana warstwa ma wymaganą maksymalną pojemność magazynu. 

Podczas przechodzenia między warstwami usług nie występują przestoje rejestru ani nie ma to wpływu na operacje rejestru.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przechodzić między warstwami usług w interfejsie wiersza polecenia platformy Azure, użyj [polecenia az acr update.][az-acr-update] Aby na przykład przełączyć się na wersję Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W przeglądzie rejestru **kontenerów** w Azure Portal wybierz pozycję **Aktualizuj,** a następnie wybierz nową **pozycję SKU** z listy rozwijanej SKU.

![Aktualizowanie wersji SKU rejestru kontenerów w Azure Portal][update-registry-sku]

## <a name="pricing"></a>Ceny

Aby uzyskać informacje o cenach poszczególnych warstw Azure Container Registry, zobacz [Container Registry cennik.][container-registry-pricing]

Aby uzyskać szczegółowe informacje o cenach transferów danych, zobacz [Szczegóły cennika przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Następne kroki

**Azure Container Registry mapy**

Odwiedź stronę [mapy usługi ACR w][acr-roadmap] witrynie GitHub, aby znaleźć informacje o nadchodzących funkcjach w usłudze.

**Azure Container Registry UserVoice**

Prześlij sugestie dotyczące nowych funkcji i zagłosuj na nie w [uciece ACR UserVoice.][container-registry-uservoice]

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md