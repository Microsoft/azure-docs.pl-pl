---
title: Klastry MATLAB na maszynach wirtualnych
description: Korzystanie z Microsoft Azure maszyn wirtualnych do tworzenia rozproszonych klastrów serwerów w programie MATLAB w celu uruchamiania równoległych obciążeń programu MATLAB
author: mscurrell
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: c2561a6dc3ad8c0af1c266b3822a80c76f45c174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639685"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Tworzenie rozproszonych klastrów serwerów programu MATLAB na maszynach wirtualnych platformy Azure
Za pomocą Microsoft Azure maszyny wirtualne można utworzyć jeden lub więcej klastrów serwerów rozproszonego przetwarzania w programie MATLAB do uruchamiania równoległych obciążeń programu MATLAB intensywnie korzystających z obliczeń. Zainstaluj oprogramowanie w programie MATLAB Distributed Computing Server na maszynie wirtualnej, aby użyć go jako obrazu podstawowego i użyć szablonu szybkiego startu platformy Azure lub skryptu Azure PowerShell (dostępnego w witrynie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) do wdrażania klastra i zarządzania nim. Po wdrożeniu Połącz się z klastrem w celu uruchomienia obciążeń.

> [!IMPORTANT]
> Ponieważ ten artykuł został zapisany, obecnie jest formalne wsparcie dla korzystania z aplikacji MATLAB na platformie Azure. Zaleca się, aby ostatnio używane funkcje zamiast szablonu i skryptów, do których odwołuje się ten artykuł. Wyszukaj w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/) "MATLAB"; Więcej informacji o uruchamianiu aplikacji programu MATLAB na platformie Azure jest dostępnych w witrynie [MathWorks](https://www.mathworks.com/solutions/cloud.html#public-cloud).

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Informacje o rozproszonym serwerze przetwarzania danych programu MATLAB i MATLAB
Platforma [MATLAB](https://www.mathworks.com/products/matlab/) jest zoptymalizowana pod kątem rozwiązywania problemów inżynieryjnych i naukowych. W programie MATLAB użytkownicy z symulacjami na dużą skalę i zadaniami przetwarzania danych mogą korzystać z MathWorks równoległych produktów obliczeniowych w celu przyspieszenia obciążeń intensywnie korzystających z obliczeń, korzystając z klastrów obliczeniowych i usług siatkowych. [Zestaw narzędzi do przetwarzania równoległego](https://www.mathworks.com/products/parallel-computing/) umożliwia programowi MATLAB users zrównoleglanie aplikacje i korzystanie z procesorów wielordzeniowych, procesorów GPU i klastrów obliczeniowych. Program [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) umożliwia użytkownikom programu MATLAB korzystanie z wielu komputerów w klastrze obliczeniowym.

Korzystając z usługi Azure Virtual Machines, można tworzyć w programie MATLAB klastry serwerów rozproszonego przetwarzania danych, które mają wszystkie te same mechanizmy, które są dostępne do przesyłania równoległych zadań jako klastry lokalne, takie jak zadania interaktywne, zadania wsadowe, zadania niezależne i komunikacja z zadaniami. Korzystanie z platformy Azure w połączeniu z platformą MATLAB ma wiele korzyści w porównaniu do aprowizacji i używania tradycyjnego sprzętu lokalnego: zakresu rozmiarów maszyn wirtualnych, tworzenia klastrów na żądanie, dzięki czemu płacisz tylko za zasoby obliczeniowe, które są używane, i możliwość testowania modeli na dużą skalę.  

## <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki** — komputer kliencki z systemem Windows musi komunikować się z platformą Azure i klastrem rozproszonego serwera przetwarzania danych programu MATLAB po wdrożeniu.
* **Azure PowerShell** — zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/) , aby zainstalować go na komputerze klienckim.
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut. W przypadku większych klastrów należy wziąć pod uwagę subskrypcję z płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu.
* **limit przydziału procesorów wirtualnych vCPU** — może być konieczne zwiększenie limitu przydziału vCPU w celu wdrożenia dużego klastra lub więcej niż jednego klastra serwera rozproszonego przetwarzania danych programu MATLAB. Aby zwiększyć limit przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) .
* **MATLAB, Parallel Computing Toolbox i MATLAB Distributed Computing Server licenses** — skrypty zakładają, że [MathWorks hostowanego Menedżera licencji](https://www.mathworks.com/help/install/license-management.html) jest używany dla wszystkich licencji.  
* **Oprogramowanie programistyczne serwera przetwarzania rozproszonego** w programie Matlab — zostanie zainstalowane na maszynie wirtualnej, która będzie używana jako podstawowy obraz maszyny wirtualnej dla maszyn wirtualnych klastra.

## <a name="high-level-steps"></a>Kroki wysokiego poziomu
Aby korzystać z usługi Azure Virtual Machines na potrzeby klastrów serwera rozproszonego przetwarzania danych w programie MATLAB, wymagane są następujące czynności wysokiego poziomu. Szczegółowe instrukcje znajdują się w dokumentacji dołączonej do szablonu i skryptów szybkiego startu w witrynie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Tworzenie podstawowego obrazu maszyny wirtualnej**  

   * Pobierz i zainstaluj oprogramowanie programu MATLAB Distributed Computing Server na tej maszynie wirtualnej.

     > [!NOTE]
     > Ten proces może potrwać kilka godzin, ale wystarczy wykonać tę czynność tylko raz dla każdej wersji programu MATLAB, która jest używana.   
     >
     >
2. **Utwórz co najmniej jeden klaster**  

   * Użyj dostarczonego skryptu programu PowerShell lub Użyj szablonu szybkiego startu, aby utworzyć klaster z podstawowego obrazu maszyny wirtualnej.   
   * Zarządzanie klastrami przy użyciu dostarczonego skryptu programu PowerShell, który umożliwia wyświetlanie, wstrzymywanie, wznawianie i usuwanie klastrów.

## <a name="cluster-configurations"></a>Konfiguracje klastrów
Obecnie skrypt tworzenia klastra i szablon umożliwiają utworzenie pojedynczej topologii rozproszonego serwera przetwarzania w programie MATLAB. Jeśli chcesz, Utwórz co najmniej jeden dodatkowy klaster, w którym każdy z nich ma inną liczbę maszyn wirtualnych procesu roboczego, przy użyciu różnych rozmiarów maszyn wirtualnych itd.

### <a name="matlab-client-and-cluster-in-azure"></a>Klient i klaster programu MATLAB na platformie Azure
Węzeł klienta programu MATLAB, węzeł harmonogram zadań programu MATLAB i węzły "Worker" rozproszonego serwera przetwarzania w programie Matlab są skonfigurowane jako maszyny wirtualne platformy Azure w sieci wirtualnej, jak pokazano na poniższej ilustracji.


* Aby użyć klastra, Połącz się Pulpit zdalny z węzłem klienta. Węzeł klienta uruchamia klienta programu MATLAB.
* Węzeł klienta ma udział plików, do którego mogą uzyskać dostęp wszyscy pracownicy.
* MathWorks hostowanego Menedżera licencji jest używany do sprawdzania licencji dla wszystkich programów w programie MATLAB.
* Domyślnie jeden proces roboczy serwer przetwarzania rozproszonego programu MATLAB na vCPU jest tworzony na maszynach wirtualnych procesu roboczego, ale można określić dowolną liczbę.

## <a name="use-an-azure-based-cluster"></a>Korzystanie z klastra opartego na platformie Azure
Podobnie jak w przypadku innych typów klastrów programu MATLAB Distributed Computing Server, należy użyć Menedżera profilów klastra w kliencie programu MATLAB (na maszynie wirtualnej klienta) w celu utworzenia profilu klastra programu MATLAB Job Scheduler.

![Menedżer profilów klastra](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowe instrukcje dotyczące wdrażania rozproszonych klastrów serwerów przetwarzania rozproszonego programu MATLAB i zarządzania nimi na platformie Azure, zobacz repozytorium [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) zawierające szablony i skrypty.
* Przejdź do [witryny MathWorks](https://www.mathworks.com/) , aby uzyskać szczegółową dokumentację dla programu MATLAB i MATLAB serwer przetwarzania rozproszonego.
