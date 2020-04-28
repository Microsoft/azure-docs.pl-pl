---
title: Magazyn obrazów kontenerów
description: Szczegółowe informacje na temat sposobu przechowywania obrazów kontenerów platformy Docker w Azure Container Registry, w tym zabezpieczeń, nadmiarowości i pojemności.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456206"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Magazyn obrazów kontenerów w Azure Container Registry

Każda usługa Azure Container Registry w warstwach [podstawowa, standardowa i Premium](container-registry-skus.md) nie oferuje zaawansowanych funkcji usługi Azure Storage, takich jak szyfrowanie — w wersji zaszyfrowanej w celu zapewnienia bezpieczeństwa danych obrazu i nadmiarowości geograficznej na potrzeby ochrony danych obrazów. W poniższych sekcjach opisano funkcje i limity magazynu obrazów w Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Szyfrowanie na poziomie spoczynku

Wszystkie obrazy kontenerów w rejestrze są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go na bieżąco, gdy użytkownik lub jego aplikacje i usługi pobierają obraz.

## <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Platforma Azure używa schematu magazynu geograficznie nadmiarowego w celu ochrony przed utratą obrazów kontenerów. Azure Container Registry automatycznie replikuje obrazy kontenerów do wielu odległych geograficznie centrów danych, zapobiegając ich utracie w przypadku awarii magazynu regionalnego.

## <a name="geo-replication"></a>Replikacja geograficzna

W przypadku scenariuszy wymagających jeszcze większej liczby gwarancji o wysokiej dostępności Rozważ użycie funkcji [replikacji geograficznej](container-registry-geo-replication.md) w rejestrach w warstwie Premium. Replikacja geograficzna pomaga chronić przed utratą dostępu do rejestru w przypadku *całkowitego* błędu regionalnego, a nie tylko awarii magazynu. Replikacja geograficzna zapewnia również inne korzyści, takie jak magazyn obrazów w sieci, w celu szybszego wypychania i ściągania w rozproszonych scenariuszach programistycznych lub wdrożeniowych.

## <a name="image-limits"></a>Limity obrazu

W poniższej tabeli opisano limity dotyczące obrazów kontenerów i magazynów dla rejestrów kontenerów platformy Azure.

| Zasób | Limit |
| -------- | :---- |
| Repozytoria | Bez ograniczeń |
| Obrazy | Bez ograniczeń |
| Warstwy | Bez ograniczeń |
| Tagi | Bez ograniczeń|
| Magazyn | 5 TB |

Bardzo duża liczba repozytoriów i tagów może mieć wpływ na wydajność rejestru. Okresowe usuwanie nieużywanych repozytoriów, tagów i obrazów w ramach procedury obsługi rejestru. Usunięte zasoby rejestru, takie jak repozytoria, obrazy i Tagi, *nie mogą* zostać odzyskane po usunięciu. Więcej informacji o usuwaniu zasobów rejestru znajduje się [w temacie Usuwanie obrazów kontenera w Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Koszty magazynu

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Container Registry][pricing].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat różnych jednostek SKU Azure Container Registry (podstawowa, standardowa, Premium), zobacz [Azure Container Registry jednostek SKU](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
