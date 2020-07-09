---
title: Przenoszenie zasobów platformy Azure do innego regionu
description: Zawiera omówienie przechodzenia zasobów platformy Azure między regionami platformy Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75485210"
---
# <a name="moving-azure-resources-across-regions"></a>Przeniesienie zasobów platformy Azure między regionami

Ten artykuł zawiera informacje o przenoszeniu zasobów platformy Azure między regionami platformy Azure.

Usługa Azure lokalizacje geograficzne, regiony i Strefy dostępności stanowią podstawę globalnej infrastruktury platformy Azure. Usługa Azure [lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/) zwykle zawiera co najmniej dwa [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Region jest obszarem w obszarze geograficznym zawierającym Strefy dostępności i wiele centrów danych. 

Po wdrożeniu zasobów w konkretnym regionie platformy Azure istnieje kilka powodów, dla których warto przenieść zasoby do innego regionu.

- **Wyrównaj do uruchamiania regionu**: Przenieś zasoby do nowo wprowadzonego regionu platformy Azure, który nie był wcześniej dostępny.
- **Wyrównanie do usług/funkcji**: Przenieś zasoby, aby skorzystać z usług lub funkcji dostępnych w określonym regionie.
- **Reagowanie na rozwój biznesowy**: przenoszenie zasobów do regionu w odpowiedzi na zmiany biznesowe, takie jak fuzje lub pozyskiwania.
- **Wyrównaj do sąsiedztwa**: Przenieś zasoby do regionu lokalnego dla Twojej firmy.
- **Spełnianie wymagań dotyczących danych**: Przenieś zasoby w celu dostosowania ich do wymagań dotyczących miejsca zamieszkania lub klasyfikacji danych. [Dowiedz się więcej](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Odpowiadanie na wymagania dotyczące wdrażania**: Przenieś zasoby, które zostały wdrożone w ramach błędu, lub przejdź w odpowiedzi na potrzeby pojemności. 
- **Reagowanie na likwidowanie**: Przenieś zasoby z powodu likwidacji regionów.

## <a name="move-process"></a>Przenieś proces

Rzeczywisty proces przenoszenia zależy od zasobów, które są przenoszone. Jednak istnieją pewne typowe kluczowe kroki:

- **Sprawdzanie wymagań wstępnych**: wymagania wstępne obejmują upewnienie się, że zasoby, które są potrzebne, są dostępne w regionie docelowym, sprawdzają, czy masz wystarczające limity przydziału i sprawdzają, czy subskrypcja ma dostęp do regionu docelowego.
- **Analizowanie zależności**: zasoby mogą mieć zależności od innych zasobów. Przed przeniesieniem Ustal zależności, aby przeniesione zasoby nadal działały zgodnie z oczekiwaniami po przeniesieniu.
- **Przygotuj do przeniesienia**: te czynności należy wykonać w regionie podstawowym przed przeniesieniem. Na przykład może być konieczne wyeksportowanie szablonu Azure Resource Manager lub rozpoczęcie replikowania zasobów z lokalizacji źródłowej do docelowej.
- **Przenoszenie zasobów**: sposób przenoszenia zasobów zależy od tego, co się stało. Może być konieczne wdrożenie szablonu w regionie docelowym lub awaria zasobów w celu przełączenia do obiektu docelowego.
- **Odrzuć zasoby docelowe**: po przeniesieniu zasobów możesz chcieć teraz przyjrzeć się zasobom w regionie docelowym i zdecydować, czy nie ma żadnych niepotrzebnych elementów.
- **Zatwierdź przeniesienie**: po sprawdzeniu zasobów w regionie docelowym niektóre zasoby mogą wymagać ostatecznej akcji zatwierdzania. Na przykład w regionie docelowym, który jest teraz regionem podstawowym, może być konieczne skonfigurowanie odzyskiwania po awarii do nowego regionu pomocniczego. 
- **Wyczyść Źródło**: wreszcie po zakończeniu i uruchomieniu w nowym regionie można wyczyścić i zlikwidować zasoby utworzone w ramach przenoszenia oraz zasoby w regionie podstawowym.



## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z listą zasobów, które obsługują przenoszenie między regionami, zobacz [przenoszenie operacji przenoszenia zasobów](region-move-support.md).
