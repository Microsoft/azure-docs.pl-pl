---
title: Warstwy i funkcje usługi Registry
description: Dowiedz się więcej o funkcjach i ograniczeniach w warstwach usług podstawowa, standardowa i Premium (SKU) Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: e2a5ad52775e9000aa0beb0a926d809da1c5a0e0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048478"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry warstw usług

Azure Container Registry jest dostępny w wielu warstwach usług (nazywanych również jednostkami SKU). Te warstwy zapewniają przewidywalną cenę i kilka opcji dopasowania do wzorców pojemności i użycia prywatnego rejestru platformy Docker na platformie Azure.

| Warstwa | Opis |
| --- | ----------- |
| **Podstawowe** | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Podstawowe rejestry mają takie same funkcje programistyczne jak standard i Premium (takie jak Azure Active Directory [integracja z uwierzytelnianiem](container-registry-authentication.md#individual-login-with-azure-ad), [Usuwanie obrazu][container-registry-delete]i elementy [webhook][container-registry-webhook]). Jednak przepływność dołączonego magazynu i obrazu jest najbardziej odpowiednia w przypadku małych scenariuszy użycia. |
| **Standardowy** | Standardowe rejestry oferują te same możliwości co podstawowa, dzięki czemu można zwiększyć pojemność magazynu i przepływność obrazu. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Rejestry Premium zapewniają największą ilość dołączonego magazynu i współbieżnych operacji, co umożliwia wykonywanie scenariuszy o dużej pojemności. Oprócz wyższej przepływności obrazów funkcja Premium dodaje funkcje, takie jak [replikacja geograficzna][container-registry-geo-replication] służąca do zarządzania pojedynczym rejestrem w wielu regionach, [zaufania zawartości](container-registry-content-trust.md) dla podpisywania tagów obrazu, [prywatnego linku z prywatnymi punktami końcowymi](container-registry-private-link.md) w celu ograniczenia dostępu do rejestru. |

Warstwy Basic, standard i Premium zapewniają te same funkcje programistyczne. Wszystkie korzyści z [magazynu obrazów][container-registry-storage] są również zarządzane całkowicie przez platformę Azure. Wybranie warstwy wyższego poziomu zapewnia lepszą wydajność i skalowalność. W przypadku wielu warstw usług możesz rozpocząć pracę z usługą Basic, a następnie przekonwertować ją na Standard i Premium w miarę wzrostu użycia rejestru.

## <a name="service-tier-features-and-limits"></a>Funkcje i limity warstwy usług

W poniższej tabeli przedstawiono szczegółowe informacje o funkcjach i limitach rejestru dla warstw usługi Basic, standard i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Zmiana warstw

Warstwę usług w rejestrze można zmienić za pomocą interfejsu wiersza polecenia platformy Azure lub w Azure Portal. Możesz swobodnie poruszać się między warstwą, dopóki warstwa, do której chcesz się przełączyć, ma wymaganą maksymalną pojemność magazynu. 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przejść między warstwami usług w interfejsie wiersza polecenia platformy Azure, użyj polecenia [AZ ACR Update][az-acr-update] . Na przykład, aby przełączyć się do warstwy Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W obszarze **Przegląd** rejestru kontenerów w Azure Portal wybierz pozycję **Aktualizuj**, a następnie wybierz nową **jednostkę SKU** z listy rozwijanej jednostka SKU.

![Aktualizowanie jednostki SKU rejestru kontenerów w Azure Portal][update-registry-sku]

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach poszczególnych Azure Container Registry warstw usług, zobacz [Container Registry Cennik][container-registry-pricing].

Aby uzyskać szczegółowe informacje o cenach transferów danych, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości. 

## <a name="next-steps"></a>Następne kroki

**Azure Container Registry plan**

Odwiedź [Plan ACR][acr-roadmap] w witrynie GitHub, aby uzyskać informacje o nadchodzących funkcjach usługi.

**Azure Container Registry UserVoice**

Prześlij i zagłosuj na nowe sugestie dotyczące funkcji w witrynie [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md