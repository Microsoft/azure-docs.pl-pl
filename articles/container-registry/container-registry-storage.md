---
title: Magazyn obrazów kontenerów
description: Szczegółowe informacje na temat sposobu przechowywania obrazów kontenera i innych artefaktów w Azure Container Registry, w tym zabezpieczeń, nadmiarowości i pojemności.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036014"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Magazyn obrazów kontenerów w Azure Container Registry

Każda usługa Azure Container Registry w warstwach [podstawowa, standardowa i Premium](container-registry-skus.md) nie oferuje zaawansowanych funkcji usługi Azure Storage, takich jak szyfrowanie — w wersji zaszyfrowanej w celu zapewnienia bezpieczeństwa danych obrazu i nadmiarowości geograficznej na potrzeby ochrony danych obrazów. W poniższych sekcjach opisano funkcje i limity magazynu obrazów w Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Szyfrowanie na poziomie spoczynku

Wszystkie obrazy kontenerów i inne artefakty w rejestrze są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go na bieżąco, gdy użytkownik lub jego aplikacje i usługi pobierają obraz. Opcjonalnie Zastosuj dodatkową warstwę szyfrowania z [kluczem zarządzanym przez klienta](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

W przypadku rejestrów kontenerów wdrożonych w większości regionów platforma Azure korzysta ze schematu magazynu geograficznie nadmiarowego, aby pomóc w ochronie przed utratą obrazów kontenerów i innych artefaktów. Azure Container Registry automatycznie replikuje obrazy kontenerów do wielu wielograficznie odległych centrów danych, zapobiegając ich utracie w przypadku wystąpienia awarii magazynu regionalnego.

> [!IMPORTANT]
> * Jeśli wystąpi awaria magazynu regionalnego, dane rejestru można odzyskać tylko przez skontaktowanie się z pomocą techniczną platformy Azure. 
> * Ze względu na wymagania dotyczące danych miejsca zamieszkania w regionie Brazylia Południowa i Azja Południowo-Wschodnia Azure Container Registry dane w tych regionach są przechowywane [tylko w lokalnych lokalizacjach geograficznych](https://azure.microsoft.com/global-infrastructure/geographies/). W przypadku Azja Południowo-Wschodnia wszystkie dane są przechowywane w Singapurze. W regionie Brazylia Południowa wszystkie dane są przechowywane w Brazylii. Gdy region zostanie utracony ze względu na znaczną awarię, firma Microsoft nie będzie mogła odzyskać danych Azure Container Registry.

## <a name="geo-replication"></a>Replikacja geograficzna

W przypadku scenariuszy wymagających jeszcze większej liczby gwarancji o wysokiej dostępności Rozważ użycie funkcji [replikacji geograficznej](container-registry-geo-replication.md) w rejestrach w warstwie Premium. Replikacja geograficzna pomaga chronić przed utratą dostępu do rejestru w przypadku *całkowitego* błędu regionalnego, a nie tylko awarii magazynu. Replikacja geograficzna zapewnia również inne korzyści, takie jak magazyn obrazów w sieci, w celu szybszego wypychania i ściągania w rozproszonych scenariuszach programistycznych lub wdrożeniowych.

## <a name="zone-redundancy"></a>Nadmiarowość stref

Aby utworzyć odporny na błędy i wysoką dostępność rejestr kontenerów platformy Azure, opcjonalnie Włącz [nadmiarowość stref](zone-redundancy.md) w wybranych regionach platformy Azure. Funkcja warstwy Premium usługi, nadmiarowość stref korzysta ze [stref dostępności](../availability-zones/az-overview.md) platformy Azure w celu replikowania rejestru do co najmniej trzech oddzielnych stref w każdym z włączonych regionów. Połącz replikację geograficzną i nadmiarowość strefy, aby zwiększyć niezawodność i wydajność rejestru. 

## <a name="scalable-storage"></a>Skalowalny magazyn

Azure Container Registry umożliwia utworzenie jako wielu repozytoriów, obrazów, warstw lub tagów, ile potrzebujesz, do [limitu magazynu rejestru](container-registry-skus.md#service-tier-features-and-limits). 

Duża liczba repozytoriów i tagów może mieć wpływ na wydajność rejestru. Okresowe usuwanie nieużywanych repozytoriów, tagów i obrazów w ramach procedury obsługi rejestru oraz opcjonalne Ustawianie [zasad przechowywania](container-registry-retention-policy.md) dla nieoznakowanych manifestów. Usunięte zasoby rejestru, takie jak repozytoria, obrazy i Tagi, *nie mogą* zostać odzyskane po usunięciu. Więcej informacji o usuwaniu zasobów rejestru znajduje się [w temacie Usuwanie obrazów kontenera w Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Koszty magazynu

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Container Registry][pricing].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rejestrów kontenerów Basic, standard i Premium, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
