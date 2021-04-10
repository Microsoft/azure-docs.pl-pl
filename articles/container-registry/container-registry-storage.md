---
title: Magazyn obrazów kontenerów
description: Szczegółowe informacje na temat sposobu przechowywania obrazów kontenera i innych artefaktów w Azure Container Registry, w tym zabezpieczeń, nadmiarowości i pojemności.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047749"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Magazyn obrazów kontenerów w Azure Container Registry

Każda usługa Azure Container Registry w warstwach [podstawowa, standardowa i Premium](container-registry-skus.md) oferuje zaawansowane funkcje usługi Azure Storage, w tym szyfrowanie w spoczynku. W poniższych sekcjach opisano funkcje i limity magazynu obrazów w Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Szyfrowanie na poziomie spoczynku

Wszystkie obrazy kontenerów i inne artefakty w rejestrze są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go na bieżąco, gdy użytkownik lub jego aplikacje i usługi pobierają obraz. Opcjonalnie Zastosuj dodatkową warstwę szyfrowania z [kluczem zarządzanym przez klienta](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Magazyn regionalny

Azure Container Registry przechowuje dane w regionie, w którym jest tworzony rejestr, aby pomóc klientom spełnić wymagania dotyczące miejsca zamieszkania i zgodności danych. We wszystkich regionach z wyjątkiem Brazylii południe i Azja Południowo-Wschodnia, platforma Azure może również przechowywać dane rejestru w sparowanym regionie w tej samej lokalizacji geograficznej. W regionach Brazylia Południowa i Południowo-Wschodnia dane rejestru są zawsze ograniczone do regionu, w celu uwzględnienia wymagań zamieszkania danych dla tych regionów.

Jeśli wystąpi awaria regionalna, dane rejestru mogą stać się niedostępne i nie są automatycznie odzyskiwane. Klienci, którzy chcą mieć swoje dane rejestru w wielu regionach w celu zapewnienia lepszej wydajności w różnych lokalizacje geograficzne lub którzy chcą odporności w przypadku awarii regionalnej, powinni włączyć [replikację geograficzną](container-registry-geo-replication.md).

## <a name="geo-replication"></a>Replikacja geograficzna

W przypadku scenariuszy wymagających zapewnienia wysokiej dostępności należy rozważyć użycie funkcji [replikacji geograficznej](container-registry-geo-replication.md) w rejestrach w warstwie Premium. Replikacja geograficzna pomaga chronić przed utratą dostępu do rejestru w przypadku awarii regionalnej. Replikacja geograficzna zapewnia również inne korzyści, takie jak magazyn obrazów w sieci, w celu szybszego wypychania i ściągania w rozproszonych scenariuszach programistycznych lub wdrożeniowych.

## <a name="zone-redundancy"></a>Nadmiarowość stref

Aby pomóc utworzyć odporny na błędy i wysoką dostępność rejestr kontenerów platformy Azure, opcjonalnie Włącz [nadmiarowość stref](zone-redundancy.md) w wybranych regionach świadczenia usługi Azure. Funkcja warstwy Premium usługi, nadmiarowość stref korzysta ze [stref dostępności](../availability-zones/az-overview.md) platformy Azure w celu replikowania rejestru do co najmniej trzech oddzielnych stref w każdym z włączonych regionów. Połącz replikację geograficzną i nadmiarowość strefy, aby zwiększyć niezawodność i wydajność rejestru. 

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
