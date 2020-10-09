---
title: Magazyn obrazów kontenerów
description: Szczegółowe informacje na temat sposobu przechowywania obrazów kontenerów platformy Docker w Azure Container Registry, w tym zabezpieczeń, nadmiarowości i pojemności.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214064"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Magazyn obrazów kontenerów w Azure Container Registry

Każda usługa Azure Container Registry w warstwach [podstawowa, standardowa i Premium](container-registry-skus.md) nie oferuje zaawansowanych funkcji usługi Azure Storage, takich jak szyfrowanie — w wersji zaszyfrowanej w celu zapewnienia bezpieczeństwa danych obrazu i nadmiarowości geograficznej na potrzeby ochrony danych obrazów. W poniższych sekcjach opisano funkcje i limity magazynu obrazów w Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Szyfrowanie na poziomie spoczynku

Wszystkie obrazy kontenerów w rejestrze są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go na bieżąco, gdy użytkownik lub jego aplikacje i usługi pobierają obraz. Opcjonalnie Zastosuj dodatkową warstwę szyfrowania z [kluczem zarządzanym przez klienta](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

Platforma Azure używa schematu magazynu geograficznie nadmiarowego w celu ochrony przed utratą obrazów kontenerów. Azure Container Registry automatycznie replikuje obrazy kontenerów do wielu odległych geograficznie centrów danych, zapobiegając ich utracie w przypadku awarii magazynu regionalnego.

## <a name="geo-replication"></a>Replikacja geograficzna

W przypadku scenariuszy wymagających jeszcze większej liczby gwarancji o wysokiej dostępności Rozważ użycie funkcji [replikacji geograficznej](container-registry-geo-replication.md) w rejestrach w warstwie Premium. Replikacja geograficzna pomaga chronić przed utratą dostępu do rejestru w przypadku *całkowitego* błędu regionalnego, a nie tylko awarii magazynu. Replikacja geograficzna zapewnia również inne korzyści, takie jak magazyn obrazów w sieci, w celu szybszego wypychania i ściągania w rozproszonych scenariuszach programistycznych lub wdrożeniowych.

## <a name="scalable-storage"></a>Skalowalny magazyn

Azure Container Registry umożliwia utworzenie jako wielu repozytoriów, obrazów, warstw lub tagów, ile potrzebujesz, do [limitu magazynu rejestru](container-registry-skus.md#service-tier-features-and-limits). 

Bardzo duża liczba repozytoriów i tagów może mieć wpływ na wydajność rejestru. Okresowe usuwanie nieużywanych repozytoriów, tagów i obrazów w ramach procedury obsługi rejestru oraz opcjonalne Ustawianie [zasad przechowywania](container-registry-retention-policy.md) dla nieoznakowanych manifestów. Usunięte zasoby rejestru, takie jak repozytoria, obrazy i Tagi, *nie mogą* zostać odzyskane po usunięciu. Więcej informacji o usuwaniu zasobów rejestru znajduje się [w temacie Usuwanie obrazów kontenera w Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Koszty magazynu

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Container Registry][pricing].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rejestrów kontenerów Basic, standard i Premium, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
