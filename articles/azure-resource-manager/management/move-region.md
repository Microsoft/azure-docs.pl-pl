---
title: Przenoszenie zasobów platformy Azure do innego regionu
description: Zawiera omówienie przechodzenia zasobów platformy Azure między regionami platformy Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007642"
---
# <a name="moving-azure-resources-across-regions"></a>Przeniesienie zasobów platformy Azure między regionami

Ten artykuł zawiera informacje o przenoszeniu zasobów platformy Azure między regionami platformy Azure.

Strefy usługi Azure lokalizacje geograficzne, regiony i dostępność stanowią podstawę globalnej infrastruktury platformy Azure. Usługa Azure [lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/) zwykle zawiera co najmniej dwa [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Region jest obszarem w obszarze geograficznym zawierającym Strefy dostępności i wiele centrów danych. 

Po wdrożeniu zasobów w konkretnym regionie platformy Azure istnieje kilka powodów, dla których warto przenieść zasoby do innego regionu.

- **Wyrównaj do uruchamiania regionu**: Przenieś zasoby do nowo wprowadzonego regionu platformy Azure, który nie był wcześniej dostępny.
- **Wyrównanie do usług/funkcji**: Przenieś zasoby, aby skorzystać z usług lub funkcji dostępnych w określonym regionie.
- **Reagowanie na rozwój biznesowy**: przenoszenie zasobów do regionu w odpowiedzi na zmiany biznesowe, takie jak fuzje lub pozyskiwania.
- **Wyrównaj do sąsiedztwa**: Przenieś zasoby do regionu lokalnego dla Twojej firmy.
- **Spełnianie wymagań dotyczących danych**: Przenieś zasoby w celu dostosowania ich do wymagań dotyczących miejsca zamieszkania lub klasyfikacji danych. [Dowiedz się więcej](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Odpowiadanie na wymagania dotyczące wdrażania**: Przenieś zasoby, które zostały wdrożone w ramach błędu, lub przejdź w odpowiedzi na potrzeby pojemności. 
- **Reagowanie na likwidowanie**: Przenieś zasoby z powodu likwidacji regionów.

## <a name="move-resources-with-resource-mover"></a>Przenoszenie zasobów przy użyciu przenoszenia zasobów

Zasoby można przenosić do innego regionu za pomocą [usługi Azure Resource](../../resource-mover/overview.md)przenoszącej. Program do przenoszenia zasobów oferuje następujące informacje:

- Jedno centrum do przemieszczania zasobów między regionami.
- Krótszy czas przenoszenia i złożoność. Wszystko, czego potrzebujesz, znajduje się w jednej lokalizacji.
- Proste i spójne środowisko do przemieszczania różnych typów zasobów platformy Azure.
- Prosty sposób identyfikowania zależności między zasobami, które chcesz przenieść. Ułatwia to przenoszenie powiązanych zasobów razem, dzięki czemu wszystko działa zgodnie z oczekiwaniami w regionie docelowym po przeniesieniu.
- Automatyczne czyszczenie zasobów w regionie źródłowym, jeśli chcesz je usunąć po przeniesieniu.
- Testowy. Możesz wypróbować przechodzenie, a następnie odrzucić go, jeśli nie chcesz wykonać pełnego przeniesienia.

Zasoby można przenosić do innego regionu przy użyciu kilku różnych metod:

- **Rozpocznij przenoszenie zasobów z grupy zasobów**: przy użyciu tej metody można rozpocząć przenoszenie regionu z grupy zasobów. Po wybraniu zasobów, które chcesz przenieść, proces kontynuuje się w centrum przenoszenia zasobów, aby sprawdzić zależności zasobów i zorganizować proces przenoszenia. [Dowiedz się więcej](../../resource-mover/move-region-within-resource-group.md).
- **Rozpocznij przenoszenie zasobów bezpośrednio z centrum przenoszenia zasobów**: dzięki tej metodzie można uruchomić proces przenoszenia regionu bezpośrednio w centrum. [Dowiedz się więcej](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>Obsługa przenoszenia regionów

Za pomocą przenoszenia zasobów można obecnie przenieść te zasoby do innego regionu:

- Maszyny wirtualne platformy Azure i skojarzone dyski
- Karty interfejsów sieciowych
- Zestawy dostępności
- Sieci wirtualne platformy Azure
- Publiczne adresy IP
- Sieciowe grupy zabezpieczeń
- Wewnętrzne i publiczne usługi równoważenia obciążenia
- Bazy danych SQL Azure i pule elastyczne

## <a name="region-move-process"></a>Proces przenoszenia regionu

Rzeczywisty proces przemieszczania zasobów między regionami zależy od zasobów, które są przenoszone. Jednak istnieją pewne typowe kluczowe kroki:

1. **Sprawdzanie wymagań wstępnych**: wymagania wstępne obejmują upewnienie się, że zasoby, które są potrzebne, są dostępne w regionie docelowym, sprawdzają, czy masz wystarczające limity przydziału i sprawdzają, czy subskrypcja ma dostęp do regionu docelowego.
2. **Analizowanie zależności**: zasoby mogą mieć zależności od innych zasobów. Przed przeniesieniem Ustal zależności, aby przeniesione zasoby nadal działały zgodnie z oczekiwaniami po przeniesieniu.
3. **Przygotuj do przeniesienia**: te czynności należy wykonać w regionie podstawowym przed przeniesieniem. Na przykład może być konieczne wyeksportowanie szablonu Azure Resource Manager lub rozpoczęcie replikowania zasobów z lokalizacji źródłowej do docelowej.
4. **Przenoszenie zasobów**: sposób przenoszenia zasobów zależy od tego, co się stało. Może być konieczne wdrożenie szablonu w regionie docelowym lub awaria zasobów w celu przełączenia do obiektu docelowego.
5. **Odrzuć zasoby docelowe**: po przeniesieniu zasobów możesz chcieć teraz przyjrzeć się zasobom w regionie docelowym i zdecydować, czy nie ma żadnych niepotrzebnych elementów.
6. **Zatwierdź przeniesienie**: po sprawdzeniu zasobów w regionie docelowym niektóre zasoby mogą wymagać ostatecznej akcji zatwierdzania. Na przykład w regionie docelowym, który jest teraz regionem podstawowym, może być konieczne skonfigurowanie odzyskiwania po awarii do nowego regionu pomocniczego. 
7. **Wyczyść Źródło**: wreszcie po zakończeniu i uruchomieniu w nowym regionie można wyczyścić i zlikwidować zasoby utworzone w ramach przenoszenia oraz zasoby w regionie podstawowym.



## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](../../resource-mover/about-move-process.md) o procesie przenoszenia w obszarze przenoszenia zasobów.
