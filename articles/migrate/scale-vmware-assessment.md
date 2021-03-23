---
title: Ocenianie dużej liczby serwerów w środowisku VMware na potrzeby migracji na platformę Azure za pomocą Azure Migrate
description: Opisuje sposób oceny dużej liczby serwerów w środowisku VMware na potrzeby migracji na platformę Azure przy użyciu usługi Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778257"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Ocenianie dużej liczby serwerów w środowisku VMware na potrzeby migracji na platformę Azure


W tym artykule opisano sposób oceny dużych liczb (1000 35000) serwerów lokalnych w środowisku VMware na potrzeby migracji na platformę Azure przy użyciu narzędzia do odnajdywania i oceny Azure Migrate.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Zaplanuj ocenę na dużą skalę.
> * Skonfiguruj uprawnienia platformy Azure i przygotuj oprogramowanie VMware do oceny.
> * Utwórz projekt Azure Migrate i Utwórz ocenę.
> * Zapoznaj się z oceną zgodnie z planem migracji.


> [!NOTE]
> Jeśli chcesz wypróbować weryfikację koncepcji do oceny kilku serwerów przed oceną na dużą skalę, postępuj zgodnie z naszą [serią samouczków](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Planowanie oceny

Planując ocenę dużej liczby serwerów w środowisku VMware, istnieje kilka rzeczy, które należy wziąć pod uwagę:

- **Planowanie Azure Migrate projektów**: informacje na temat wdrażania projektów Azure Migrate. Na przykład jeśli centra danych znajdują się w różnych lokalizacje geograficzneach lub chcesz przechowywać metadane związane z odnajdywaniem, oceną lub migracją w innej lokalizacji geograficznej, może być konieczne przeprowadzenie wielu projektów. 
- **Planowanie urządzeń**: Azure Migrate używa lokalnego urządzenia Azure Migrate wdrożonego jako maszyna wirtualna VMware w celu ciągłego odnajdywania serwerów. Urządzenie monitoruje zmiany środowiska, takie jak Dodawanie serwerów, dysków lub kart sieciowych. Wysyła również metadane i dane dotyczące wydajności na platformie Azure. Należy ustalić liczbę urządzeń, które należy wdrożyć.
- **Planowanie kont do odnajdowania**: urządzenie Azure Migrate używa konta z dostępem do vCenter Server w celu odnajdywania serwerów na potrzeby oceny i migracji. W przypadku odnajdywania więcej niż 10 000 serwerów należy skonfigurować wiele kont, ponieważ jest to wymagane, między serwerami wykrytymi z jakichkolwiek dwóch urządzeń w projekcie nie ma nakładania się. 

> [!NOTE]
> W przypadku konfigurowania wielu urządzeń upewnij się, że nie nakładają się na serwery na kontach vCenter. Odnajdywanie z takim nakładaniem się jest nieobsługiwane. Jeśli serwer zostanie odnaleziony przez więcej niż jedno urządzenie, spowoduje to zduplikowanie podczas odnajdywania i problemów podczas włączania replikacji dla serwera przy użyciu Azure Portal w ramach migracji serwera.

## <a name="planning-limits"></a>Limity planowania
 
Użyj limitów podsumowania w tej tabeli w celu zaplanowania.

**Planowanie** | **Limity**
--- | --- 
**Projekty Azure Migrate** | Oceń do 35 000 serwerów w projekcie.
**Urządzenie usługi Azure Migrate** | Urządzenie może wykryć do 10 000 serwerów na vCenter Server.<br/> Urządzenie może łączyć się tylko z jednym vCenter Server.<br/> Urządzenie może być skojarzone tylko z pojedynczym projektem Azure Migrate.<br/>  Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem Azure Migrate. <br/><br/> 
**Grupa** | Można dodać maksymalnie 35 000 serwerów w jednej grupie.
**Ocena Azure Migrate** | W ramach jednej oceny można ocenić do 35 000 serwerów.

Mając na uwadze następujące limity, Oto kilka przykładowych wdrożeń:


**serwer vCenter** | **Serwery na serwerze** | **Zalecenie** | **Akcja**
---|---|---|---
Jeden | < 10 000 | Jeden Azure Migrate projektu.<br/> Jedno urządzenie.<br/> Jedno konto vCenter do odnajdowania. | Skonfiguruj urządzenie, Połącz się z vCenter Server przy użyciu konta.
Jeden | > 10 000 | Jeden Azure Migrate projektu.<br/> Wiele urządzeń.<br/> Wiele kont vCenter. | Skonfiguruj urządzenie dla każdego 10 000 serwerów.<br/><br/> Skonfiguruj konta vCenter i Podziel zapasy, aby ograniczyć dostęp dla konta do mniej niż 10 000 serwerów.<br/> Połącz każde urządzenie z serwerem vCenter Server przy użyciu konta.<br/> Można analizować zależności między serwerami, które są wykrywane przy użyciu różnych urządzeń. <br/> <br/> Upewnij się, że nie ma nakładania się między serwerami na kontach vCenter. Odnajdywanie z takim nakładaniem się jest nieobsługiwane. Jeśli serwer zostanie odnaleziony przez więcej niż jedno urządzenie, powoduje to duplikaty podczas odnajdywania i rozwiązywania problemów podczas włączania replikacji dla serwera przy użyciu Azure Portal w ramach migracji serwera.
Wiele | < 10 000 |  Jeden Azure Migrate projektu.<br/> Wiele urządzeń.<br/> Jedno konto vCenter do odnajdowania. | Skonfiguruj urządzenia, Połącz się z vCenter Server przy użyciu konta.<br/> Można analizować zależności między serwerami, które są wykrywane przy użyciu różnych urządzeń.
Wiele | > 10 000 | Jeden Azure Migrate projektu.<br/> Wiele urządzeń.<br/> Wiele kont vCenter. | W przypadku vCenter Server odnajdywania serwerów < 10 000 Skonfiguruj urządzenie dla każdego vCenter Server.<br/><br/> W przypadku vCenter Server odnajdywania serwerów > 10 000 Skonfiguruj urządzenie dla każdego serwera 10 000.<br/> Skonfiguruj konta vCenter i Podziel zapasy, aby ograniczyć dostęp dla konta do mniej niż 10 000 serwerów.<br/> Połącz każde urządzenie z serwerem vCenter Server przy użyciu konta.<br/> Można analizować zależności między serwerami, które są wykrywane przy użyciu różnych urządzeń. <br/><br/> Upewnij się, że nie ma nakładania się między serwerami na kontach vCenter. Odnajdywanie z takim nakładaniem się jest nieobsługiwane. Jeśli serwer zostanie odnaleziony przez więcej niż jedno urządzenie, powoduje to duplikaty podczas odnajdywania i rozwiązywania problemów podczas włączania replikacji dla serwera przy użyciu Azure Portal w ramach migracji serwera.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planowanie odnajdywania w środowisku z wieloma dzierżawcami

W przypadku planowania środowiska z wieloma dzierżawcami można określać zakres odnajdywania na vCenter Server.

- Zakres odnajdywania urządzenia można ustawić na vCenter Server centrami danych, klastrami lub folderami klastrów, hostów lub folderów hostów lub pojedynczych serwerów.
- Jeśli Twoje środowisko jest współużytkowane przez dzierżawców i chcesz osobno wykryć każdą dzierżawę, możesz zakres dostępu do konta vCenter używanego przez urządzenie do odnajdywania. 
    - Jeśli dzierżawy współużytkują hosty, możesz chcieć zasięgać według folderów maszyn wirtualnych. Azure Migrate nie może odnaleźć serwerów, jeśli konto vCenter ma dostęp udzielony na poziomie folderu programu vCenter VM. Jeśli zamierzasz przeznaczyć zakres odnajdywania według folderów maszyn wirtualnych, możesz to zrobić, upewniając się, że konto vCenter ma dostęp tylko do odczytu przypisany na poziomie serwera. [Dowiedz się więcej](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Przygotuj platformę Azure i oprogramowanie VMware na potrzeby odnajdywania i oceny:

1. Sprawdź [wymagania i ograniczenia dotyczące obsługi programu VMware](migrate-support-matrix-vmware.md).
2. Skonfiguruj uprawnienia dla Twojego konta platformy Azure, aby móc korzystać z Azure Migrate.
3. Przygotuj oprogramowanie VMware do oceny.

Aby skonfigurować te ustawienia, postępuj zgodnie z instrukcjami podanymi w [tym samouczku](./tutorial-discover-vmware.md) .


## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami dotyczącymi planowania wykonaj następujące czynności:

1. Utwórz projekty Azure Migrate.
2. Dodaj Azure Migrate odnajdywania i oceny do projektów.

[Dowiedz się więcej](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Utwórz oceny dla serwerów w środowisku VMware.
1. Zapoznaj się z ocenami w temacie przygotowanie do planowania migracji.


Aby skonfigurować te ustawienia, postępuj zgodnie z instrukcjami podanymi w [tym samouczku](./tutorial-assess-vmware-azure-vm.md) .
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowane skalowanie Azure Migrate ocen w przypadku serwerów w środowisku VMware
> * Przygotowano platformę Azure i oprogramowanie VMware do oceny
> * Utworzono projekt Azure Migrate i uruchomiono oceny
> * Przegląd ocen w przygotowaniu do migracji.

Teraz [Dowiedz się, jak](concepts-assessment-calculation.md) są obliczane oceny i jak [modyfikować oceny](how-to-modify-assessment.md).