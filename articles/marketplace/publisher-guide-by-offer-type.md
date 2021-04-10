---
title: Przewodnik publikowania według typu oferty — Microsoft Commercial Marketplace
description: W tym artykule opisano typy ofert, które są dostępne w komercyjnym portalu Microsoft Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 3be967b611f6b75705a60bfa14523832b0652931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879228"
---
# <a name="publishing-guide-by-offer-type"></a>Podręcznik publikowania dla różnych typów ofert

W tym artykule opisano typy ofert, które są dostępne w portalu komercyjnym. *Typ oferty* definiuje strukturę oferty, która obejmuje metadane, artefakty i inną zawartość prezentowaną w komercyjnym portalu Marketplace.

Po wybraniu [opcji publikowania](determine-your-listing-type.md)musisz wybrać typ oferty przed rozpoczęciem tworzenia oferty w centrum partnerskim. Typ oferty będzie odpowiadać typowi rozwiązania, aplikacji lub oferty usługi, którą chcesz opublikować, a także jego wyrównaniem do produktów i usług firmy Microsoft.

Możesz skonfigurować pojedynczy typ oferty na różne sposoby, aby włączyć różne opcje publikowania, opcję wyświetlania, Inicjowanie obsługi lub Cennik. Opcja publikowania i konfiguracja typu oferty są również zgodne z wymaganiami dotyczącymi ofert i wymaganiami technicznymi.

Przed utworzeniem oferty zapoznaj się ze sklepem online i wymaganiami dotyczącymi kwalifikacji typów i wymaganiami dotyczącymi publikowania technicznego.

## <a name="list-of-offer-types"></a>Lista typów ofert

W poniższej tabeli przedstawiono typy ofert komercyjnych portalu Marketplace w centrum partnerskim.

| **Typ oferty**    | **Opis**  |
| :------------------- | :-------------------|
| [**Azure Application**](plan-azure-application-offer.md) | Istnieją dwa rodzaje planów aplikacji platformy Azure: _szablon rozwiązania_ i _aplikacja zarządzana_. Oba typy planów obsługują Automatyzowanie wdrażania i konfigurowania rozwiązania poza jedną maszyną wirtualną. Można zautomatyzować proces udostępniania wielu zasobów, w tym maszyn wirtualnych, sieci i zasobów magazynu, aby zapewnić złożone rozwiązania, takie jak rozwiązania IaaS. Oba typy planów mogą korzystać z wielu różnych rodzajów zasobów platformy Azure, w tym między innymi maszyn wirtualnych.<ul><li>Plany **szablonów rozwiązań** to jeden z głównych sposobów publikowania rozwiązania na rynku komercyjnym. Plany szablonów rozwiązań nie są transakcyjne w komercyjnej witrynie Marketplace, ale mogą służyć do wdrażania płatnych ofert maszyn wirtualnych, które są rozliczane za pomocą komercyjnej witryny Marketplace. Użyj typu planu szablonu rozwiązania, gdy klient będzie zarządzać rozwiązaniem, a transakcje są rozliczane za pomocą innego planu.</li><br><li>Plany **aplikacji zarządzanych** umożliwiają łatwe tworzenie i dostarczanie w pełni zarządzanych aplikacji gotowe dla klientów. Mają one te same możliwości co plany szablonów rozwiązań, z niektórymi różnicami między kluczami:</li><ul><li> Zasoby są wdrażane w grupie zasobów i są zarządzane przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów.</li><li>Jako wydawca należy określić koszt dla ciągłej pomocy technicznej rozwiązania, a transakcje są obsługiwane za pomocą komercyjnej witryny Marketplace.</li></ul>Użyj typu planu aplikacji zarządzanej, gdy użytkownik lub klient wymaga, aby rozwiązanie było zarządzane przez partnera, lub wdrożyć rozwiązanie oparte na subskrypcji.</ul> |
| [**Kontener platformy Azure**](marketplace-containers.md) | Użyj typu oferty kontenera platformy Azure, jeśli Twoje rozwiązanie jest obrazem kontenera Docker, który został zainicjowany jako usługa kontenera platformy Azure oparta na Kubernetes. |
| [**Maszyna wirtualna platformy Azure**](marketplace-virtual-machines.md) | Podczas wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem Użyj typu oferty maszyny wirtualnej. |
| [**Usługa doradcza**](./plan-consulting-service-offer.md) | Usługi doradcze ułatwiają łączenie się z usługami w celu wspierania i zwiększania użytkowania usług Azure, Dynamics 365 i pakietów usługi.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Możesz publikować oferty AppSource, które kompilują lub rozszerą program Dynamics 365 Business Central, Dynamics 365 Customer Engagement, aplikacje zaawansowane i aplikacje finansowe i operacyjne.|
| [**Moduł IoT Edge**](iot-edge-module.md) | Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. |
| [**Usługa zarządzana**](./plan-managed-service-offer.md) | Można utworzyć oferty usługi zarządzanej i zarządzać subskrypcjami delegowanymi przez klienta lub grupami zasobów za pomocą [usługi Azure Lighthouse](../lighthouse/overview.md).|
| [**Aplikacja Power BI** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Możesz publikować oferty AppSource, które kompilują lub rozszerzającą Power BI i Microsoft 365.|
| [**Oprogramowanie jako usługa**](plan-saas-offer.md) | Użyj typu oferty oprogramowanie jako usługa (SaaS), aby umożliwić klientowi kupowanie rozwiązań technicznych opartych na technologii SaaS w ramach subskrypcji. Aby uzyskać informacje na temat wymagań dotyczących logowania jednokrotnego dla ofert SaaS, zobacz [oferty usługi Azure AD i transacting SaaS w portalu komercyjnym](azure-ad-saas.md). |


## <a name="next-steps"></a>Następne kroki

- Przejrzyj wymagania dotyczące uprawnień w odpowiednim artykule dla typu oferty, aby sfinalizować wybór i konfigurację oferty.
- Przejrzyj wzorce publikowania dla każdego sklepu online, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.