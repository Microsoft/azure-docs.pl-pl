---
title: Warstwy usług i jednostki SKU
description: Dowiedz się więcej o funkcjach i ograniczeniach w warstwach usług podstawowa, standardowa i Premium (SKU) Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456274"
---
# <a name="azure-container-registry-skus"></a>Jednostki SKU Azure Container Registry

Azure Container Registry (ACR) jest dostępny w wielu warstwach usług, znanych jako jednostki SKU. Te jednostki SKU zapewniają przewidywalną cenę i kilka opcji dopasowania do wzorców pojemności i użycia prywatnego rejestru platformy Docker na platformie Azure.

| SKU | Opis |
| --- | ----------- |
| **Podstawowy** | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Podstawowe rejestry mają takie same funkcje programistyczne jak standard i Premium (takie jak Azure Active Directory [integracja z uwierzytelnianiem](container-registry-authentication.md#individual-login-with-azure-ad), [Usuwanie obrazu][container-registry-delete]i elementy [webhook][container-registry-webhook]). Jednak przepływność dołączonego magazynu i obrazu jest najbardziej odpowiednia w przypadku małych scenariuszy użycia. |
| **Standardowa** | Standardowe rejestry oferują te same możliwości co podstawowa, dzięki czemu można zwiększyć pojemność magazynu i przepływność obrazu. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Rejestry Premium zapewniają największą ilość dołączonego magazynu i współbieżnych operacji, co umożliwia wykonywanie scenariuszy o dużej pojemności. Oprócz wyższej przepływności obrazów funkcja Premium dodaje funkcje, takie jak [replikacja geograficzna][container-registry-geo-replication] służąca do zarządzania pojedynczym rejestrem w wielu regionach, [zaufania zawartości](container-registry-content-trust.md) dla podpisywania tagów obrazu, [zapór i sieci wirtualnych (wersja zapoznawcza)](container-registry-vnet.md) w celu ograniczenia dostępu do rejestru. |

Jednostki SKU w warstwach Podstawowa, standardowa i Premium zapewniają te same funkcje programistyczne. Wszystkie korzyści z [magazynu obrazów][container-registry-storage] są również zarządzane całkowicie przez platformę Azure. Wybranie jednostki SKU wyższego poziomu zapewnia większą wydajność i skalowalność. W przypadku wielu warstw usług możesz rozpocząć pracę z usługą Basic, a następnie przekonwertować ją na Standard i Premium w miarę wzrostu użycia rejestru.

## <a name="sku-features-and-limits"></a>Funkcje i limity jednostki SKU

W poniższej tabeli przedstawiono funkcje i limity warstw usług podstawowa, standardowa i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Zmienianie jednostek SKU

Jednostkę SKU rejestru można zmienić za pomocą interfejsu wiersza polecenia platformy Azure lub w Azure Portal. Można swobodnie poruszać się między JEDNOSTKAmi SKU, o ile wymagana jest maksymalna pojemność magazynu. 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby poruszać się między jednostkami SKU w interfejsie wiersza polecenia platformy Azure, użyj polecenia [AZ ACR Update][az-acr-update] . Na przykład, aby przełączyć się do warstwy Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W obszarze **Przegląd** rejestru kontenerów w Azure Portal wybierz pozycję **Aktualizuj**, a następnie wybierz nową **jednostkę SKU** z listy rozwijanej jednostka SKU.

![Aktualizowanie jednostki SKU rejestru kontenerów w Azure Portal][update-registry-sku]

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach dla każdej jednostki SKU Azure Container Registry, zobacz [Cennik usługi Container Registry][container-registry-pricing].

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
