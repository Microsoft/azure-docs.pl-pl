---
title: Regiony i Strefy dostępności na platformie Azure
description: Dowiedz się więcej na temat regionów i Strefy dostępności na platformie Azure, aby spełnić wymagania techniczne i prawne.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 4adfb63ecab72eb42e188af472bb5387a0276a79
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723779"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regiony i Strefy dostępności na platformie Azure

Usługi Microsoft Azure są dostępne globalnie w celu zapewnienia optymalnego poziomu operacji w chmurze. Możesz wybrać najlepszy region dla potrzeb na podstawie zagadnień technicznych i prawnych: możliwości usług, miejsca zamieszkania danych, wymagania dotyczące zgodności i opóźnienia.

## <a name="terminology"></a>Terminologia

Aby lepiej zrozumieć regiony i Strefy dostępności na platformie Azure, zapoznaj się z najważniejszymi pojęciami lub koncepcjami.

| Termin lub pojęcie | Opis |
| --- | --- |
| region | Zestaw centrów danych wdrożonych w ramach określonego czasu oczekiwania i połączony za pomocą dedykowanej regionalnej sieci o małym opóźnieniu. |
| geograficzne | Obszar świata zawierający co najmniej jeden region świadczenia usługi Azure. Lokalizacje geograficzne definiują dyskretny rynek, który zachowuje granice miejsca zamieszkania i zgodności. Obszary geograficzne umożliwiają klientom, którzy mają określone potrzeby związane z rezydencją danych i zgodnością, przechowywanie ich danych i aplikacji w bliskim sąsiedztwie. Lokalizacje geograficzne są odporne na uszkodzenia, aby przetrzymywać pełną awarię regionu poprzez połączenie z naszą dedykowaną infrastrukturą sieciową o dużej pojemności. |
| Strefa dostępności | Unikatowe lokalizacje fizyczne w regionie. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. |
| Zalecany region | Region, który zapewnia szeroką gamę możliwości usługi i jest przeznaczony do obsługi Strefy dostępności teraz lub w przyszłości. Są one określone w Azure Portal zgodnie z **zaleceniami**. |
| alternatywny region (inny) | Region, który rozszerza zasięg platformy Azure w granicach miejsca zamieszkania danych, w której istnieje również zalecany region. Alternatywne regiony pomagają zoptymalizować opóźnienie i zapewnić drugi region dla potrzeb związanych z odzyskiwaniem po awarii. Nie są one przeznaczone do obsługi Strefy dostępności (chociaż platforma Azure przeprowadza regularne oceny tych regionów, aby określić, czy powinny one stać się zalecanymi regionami). Są one określone w Azure Portal jako **inne**. |
| Usługa fundamentowa | Podstawowa usługa platformy Azure, która jest dostępna we wszystkich regionach, gdy region jest ogólnie dostępny. |
| Usługa podstawowe | Usługa platformy Azure dostępna we wszystkich zalecanych regionach w ciągu 90 dni od regionu Ogólna dostępność lub dostępność oparta na zapotrzebowaniu w regionach alternatywnych. |
| wyspecjalizowana usługa | Usługa platformy Azure, która jest zależna od popytu dostępność w regionach, obsługiwana przez dostosowany/wyspecjalizowany sprzęt. |
| Usługa regionalna | Usługa platformy Azure, która jest wdrożona w regionie i umożliwia klientowi określenie regionu, w którym zostanie wdrożona usługa. Aby uzyskać pełną listę, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Usługa nieregionalna | Usługa platformy Azure, dla której nie ma zależności w określonym regionie świadczenia usługi Azure. Usługi nieregionalne są wdrażane w dwóch lub więcej regionach i w przypadku awarii regionalnej wystąpienie usługi w innym regionie kontynuuje obsługę klientów. Aby uzyskać pełną listę, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regiony

Region to zestaw centrów danych wdrożonych w ramach określonego czasu oczekiwania i połączony za pomocą dedykowanej regionalnej sieci o małym opóźnieniu. Platforma Azure zapewnia elastyczność wdrażania aplikacji, które są potrzebne, w tym między wieloma regionami w celu zapewnienia odporności między regionami. Aby uzyskać więcej informacji, zobacz [Omówienie filaru odporności](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Strefy dostępności

Strefa dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi strefowo nadmiarowe replikujeją aplikacje i dane między Strefy dostępności, aby chronić je przed awariami jednego punktu. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje tę dystrybucję w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są zaplanowane do aktualizacji w tym samym czasie.

Twórz wysoką dostępność w architekturze aplikacji dzięki umieszczeniu na niej zasobów obliczeniowych, magazynu, sieci i danych w ramach strefy i replikacji w innych strefach. Usługi platformy Azure, które obsługują strefy dostępności, dzielą się na dwie kategorie:

- **Usługi strefowe** — miejsce, w którym zasób jest przypięty do określonej strefy (na przykład maszyny wirtualne, dyski zarządzane, standardowe adresy IP) lub
- **Usługi strefowo nadmiarowe** — gdy platforma Azure jest replikowana automatycznie między strefami (na przykład magazyn strefowo nadmiarowy, SQL Database).

Aby zapewnić kompleksową ciągłość biznesową na platformie Azure, skompiluj architekturę aplikacji przy użyciu kombinacji Strefy dostępności z parami regionów platformy Azure. Można synchronicznie replikować aplikacje i dane za pomocą Strefy dostępności w regionie świadczenia usługi Azure, aby zapewnić wysoką dostępność i asynchroniczną replikację w regionach platformy Azure w celu ochrony przed odzyskiwaniem po awarii.
 
![Widok koncepcyjny jednej strefy przechodzącej w regionie](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identyfikatory stref dostępności (numery 1, 2 i 3 na zdjęciu powyżej) są logicznie mapowane na rzeczywiste strefy fizyczne dla każdej subskrypcji. Oznacza to, że dostępność Strefa 1 w danej subskrypcji może odwoływać się do innej strefy fizycznej niż Strefa 1 dostępności w innej subskrypcji. W związku z tym zaleca się, aby nie polegać na identyfikatorach strefy dostępności w różnych subskrypcjach na potrzeby umieszczania maszyn wirtualnych.

## <a name="region-and-service-categories"></a>Kategorie regionów i usług

Podejście platformy Azure do dostępności usług platformy Azure w różnych regionach jest najlepiej opisane przez ekspresowe usługi, które są dostępne w zalecanych regionach i regionach alternatywnych.

- **Zalecany region** — region, który zapewnia szeroką gamę możliwości usługi i jest przeznaczony do obsługi strefy dostępności teraz lub w przyszłości. Są one określone w Azure Portal zgodnie z **zaleceniami**.
- **Region alternatywny (inny)** — region rozszerzający zasięg platformy Azure w obrębie miejsca zamieszkania danych, w którym znajduje się również zalecany region. Alternatywne regiony pomagają zoptymalizować opóźnienie i zapewnić drugi region dla potrzeb związanych z odzyskiwaniem po awarii. Nie są one przeznaczone do obsługi Strefy dostępności (chociaż platforma Azure przeprowadza regularne oceny tych regionów, aby określić, czy powinny one stać się zalecanymi regionami). Są one określone w Azure Portal jako **inne**.

### <a name="comparing-region-types"></a>Porównywanie typów regionów

Usługi platformy Azure są pogrupowane w trzy kategorie: podstawowe, podstawowe i wyspecjalizowane usługi. Ogólne zasady wdrażania usług w dowolnym regionie przez platformę Azure są określane głównie przez typ regionu, kategorie usług i zapotrzebowanie klienta:

- Podstawowe **— dostępne** we wszystkich zalecanych i alternatywnych regionach, gdy region jest ogólnie dostępny, lub w ciągu 90 dni od nowej usługi, która staje się ogólnie dostępna.
- Podstawowe **— dostępne** we wszystkich zalecanych regionach w ciągu 90 dni od ogólnego udostępnienia regionu; zależne od popytu w alternatywnych regionach (wiele jest już wdrożone w dużym podzbiorze regionów alternatywnych).
- **Wyspecjalizowane** oferty usług ukierunkowanych, które są często ukierunkowane na branżowe lub objęte przez dostosowany sprzęt. Dostępność oparta na zapotrzebowaniu w różnych regionach (wiele jest już wdrożonych w dużym podzbiorze zalecanych regionów).

Aby zobaczyć, które usługi są wdrażane w danym regionie, a także przyszły plan wersji zapoznawczej lub ogólnej dostępności usług w regionie, zobacz artykuły [dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Jeśli oferta usługi nie jest dostępna w określonym regionie, możesz podzielić się zainteresowaniami, kontaktując się z przedstawicielem handlowym firmy Microsoft.

| Typ regionu | Inne niż regionalne | Podstawowe | Podstawowa pomoc techniczna | Wyspecjalizowany | Strefy dostępności | Rezydencja danych |
| --- | --- | --- | --- | --- | --- | --- |
| Zalecane | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Sterowane popytem | :heavy_check_mark: | :heavy_check_mark: |
| Formę | :heavy_check_mark: | :heavy_check_mark: | Sterowane popytem | Sterowane popytem | Nie dotyczy | :heavy_check_mark: |

### <a name="services-by-category-with-availability-zones"></a>Usługi według kategorii z Strefy dostępności

Jak wspomniano wcześniej, platforma Azure klasyfikuje usługi w trzy kategorie: podstawowe, podstawowe i wyspecjalizowane. Kategorie usług są przypisywane w ogólnej dostępności. Często usługi uruchamiają swój cykl życia jako wyspecjalizowaną usługę, a wzrost popytu i użycia może być podniesiony do podstawy lub do podstaw. W poniższej tabeli wymieniono kategorie usług jako podstawowe, podstawowe. Należy zwrócić uwagę na następujące kwestie dotyczące tabeli:

- Niektóre usługi nie są regionalne. Aby uzyskać informacje i listę usług nieregionalnych, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/).
- Nie ma na liście starszej generacji usług lub maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [wcześniejszych generacji rozmiarów maszyn wirtualnych](../virtual-machines/sizes-previous-gen.md)
- . Usługi nie mają przypisanej kategorii do momentu ogólnego udostępnienia. Aby uzyskać informacje i listę usług w wersji zapoznawczej, zobacz artykuły [dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Podstawowe                           | Podstawowa pomoc techniczna                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Konta magazynu                       | API Management                                    | 
> | Application Gateway                    | Konfiguracja aplikacji                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | Usługa Azure Data Lake Storage 2. generacji           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Publiczny adres IP platformy Azure                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Search                            | 
> | Azure SQL: wystąpienie zarządzane           | Azure Cognitive Services                          | 
> | Disk Storage                           | Azure Cognitive Services: przetwarzanie obrazów         | 
> | Event Hubs                             | Cognitive Services platformy Azure: Content Moderator       | 
> | Key Vault                              | Cognitive Services platformy Azure: powierzchnie                    | 
> | Moduł równoważenia obciążenia                          | Azure Cognitive Services: czytnik immersyjny        | 
> | Service Bus                            | Cognitive Services platformy Azure: Language Understanding  | 
> | Service Fabric                         | Azure Cognitive Services: usługi mowy         | 
> | Magazyn: gorąca/chłodna Blob Storage warstw   | Azure Cognitive Services: analiza tekstu          | 
> | Magazyn: Managed Disks                 | Cognitive Services platformy Azure: translator              | 
> | Virtual Machine Scale Sets             | Azure Data Explorer                               | 
> | Virtual Machines                       | Azure Data Share                                  | 
> | Virtual Machines: dedykowany Host platformy Azure | Azure Database for MySQL                          | 
> | Virtual Machines: Av2-Series           | Azure Database for PostgreSQL                     | 
> | Virtual Machines: Bs-Series            | Azure DDoS Protection                             | 
> | Virtual Machines: DSv2-Series          | Azure Firewall                                    | 
> | Virtual Machines: DSv3-Series          | Azure Firewall Manager                            | 
> | Virtual Machines: Dv2-Series           | Azure Functions                                   | 
> | Virtual Machines: Dv3-Series           | Azure IoT Hub                                     |     
> | Virtual Machines: ESv3-Series          | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Ev3-Series           | Azure Machine Learning                            | 
> | Virtual Network                        | Azure Monitor: Application Insights               | 
> | VPN Gateway                            | Azure Monitor: Log Analytics                      | 
> |                                        | Link prywatny platformy Azure                                | 
> |                                        | Azure Red Hat OpenShift                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Usługa Azure Stream Analytics                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Batch                                             | 
> |                                        | Cloud Services: Seria M                          | 
> |                                        | Container Instances                               | 
> |                                        | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Notification Hubs                                 | 
> |                                        | Blob Storage Premium                              | 
> |                                        | Magazyn plików w warstwie Premium                             | 
> |                                        | Virtual Machines: Ddsv4-Series                    | 
> |                                        | Virtual Machines: Ddv4-Series                     | 
> |                                        | Virtual Machines: Dsv4-Series                     | 
> |                                        | Virtual Machines: Dv4-Series                      | 
> |                                        | Virtual Machines: Edsv4-Series                    | 
> |                                        | Virtual Machines: Edv4-Series                     | 
> |                                        | Virtual Machines: Esv4-Series                     | 
> |                                        | Virtual Machines: Ev4-Series                      | 
> |                                        | Virtual Machines: Fsv2-Series                     | 
> |                                        | Virtual Machines: Seria M                        | 
> |                                        | Wirtualna sieć WAN                                       | 



### <a name="specialized-services"></a>Wyspecjalizowane usługi
Jak wspomniano wcześniej, platforma Azure klasyfikuje usługi w trzy kategorie: podstawowe, podstawowe i wyspecjalizowane. Kategorie usług są przypisywane w ogólnej dostępności. Często usługi uruchamiają swój cykl życia jako wyspecjalizowaną usługę, a wzrost popytu i użycia może być podniesiony do podstawy lub do podstaw. W poniższej tabeli wymieniono wyspecjalizowane usługi. 

> [!div class="mx-tableFixed"]
> | Wyspecjalizowany                                          |
> |------------------------------------------------------|
> | Interfejs API platformy Azure dla standardu FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Cognitive Services: Wykrywacz anomalii           |
> | Cognitive Services platformy Azure: Custom Vision              |
> | Cognitive Services platformy Azure: aparat rozpoznawania formularzy            |
> | Azure Cognitive Services: Personalizacja               |
> | Cognitive Services platformy Azure: QnA Maker                  |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Dedykowany moduł HSM platformy Azure                                  |
> | Azure Digital Twins                                  |
> | Bot kondycji platformy Azure                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure SignalR Service                                |
> | Usługa w chmurze Azure wiosny                           |
> | Azure Time Series Insights                           |
> | Rozwiązanie Azure VMware                                |
> | Azure VMware Solution by CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure Machine Learning Studio (klasyczny)              |
> | Spatial Anchors                                      |
> | Magazyn: Archive Storage                             |
> | Ultra Disk Storage                                   |
> | Video Indexer                                        |
> | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: Seria DCsv2                       |
> | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: HBv2-Series                        |
> | Virtual Machines: HCv1-Series                        |
> | Virtual Machines: Seria H                           |
> | Virtual Machines: LSv2-Series                        |
> | Virtual Machines: Mv2-Series                         |
> | Virtual Machines: NCv3-Series                        |
> | Virtual Machines: NDv2-Series                        |
> | Virtual Machines: NVv3-Series                        |
> | Virtual Machines: NVv4-Series                        | 
> | Virtual Machines: Oprogramowanie SAP HANA na platformie Azure — duże wystąpienia  |




## <a name="next-steps"></a>Następne kroki

- [Regiony obsługujące Strefy dostępności na platformie Azure](az-region.md)
- [Szablony szybkiego startu](https://aka.ms/azqs)
