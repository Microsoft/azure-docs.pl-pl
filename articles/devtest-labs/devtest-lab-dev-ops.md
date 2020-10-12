---
title: Integracja Azure DevTest Labs i DevOps | Microsoft Docs
description: Dowiedz się, jak używać laboratoriów Azure DevTest Labs w ramach potoków ciągłej integracji (CI)/ciągłego dostarczania (CD) w środowisku przedsiębiorstwa.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481072"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integracja Azure DevTest Labs i Azure DevOps
DevOps to metodologia opracowywania oprogramowania, która integruje programowanie oprogramowania (dev) z operacjami (Ops) dla systemu. Ten system może dostarczać nowe funkcje, aktualizacje i poprawki na wyrównaniu z celami biznesowymi. Ta metodologia obejmuje wszystko od projektowania nowych funkcji w oparciu o cele, wzorce użycia i opinie klientów. w celu naprawienia, odzyskania i zabezpieczenia systemu w przypadku wystąpienia problemów. Łatwo zidentyfikowany składnik tej metodologii jest potokiem ciągłej integracji (CI)/ciągłego dostarczania (CD). Potok ciągłej integracji/ciągłego dostarczania informacji, kodu i zasobów z zatwierdzenia przez serię kroków, które obejmują kompilowanie, testowanie i wdrażanie, aby utworzyć system. Ten artykuł koncentruje się na różnych sposobach efektywnego wykorzystania laboratoriów w ramach potoku w środowisku przedsiębiorstwa. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Zalety korzystania z laboratoriów w przepływie pracy DevOps 

### <a name="focused-access"></a>Dostęp ukierunkowany 
Używanie laboratorium jako składnika umożliwia określonym ekosystemowi skojarzenie z ograniczoną grupą osób. Zwykle zespół lub Grupa, która pracuje w wspólnym obszarze lub konkretna funkcja, będzie miała przypisane laboratorium.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikacja infrastruktury w chmurze 
Deweloper może szybko skonfigurować ekosystem programistyczny, który zawiera pole dev z kodem źródłowym i narzędziami. Deweloper może również utworzyć środowisko, które jest niemal identyczne z konfiguracją produkcyjną. Pomaga w szybszym tworzeniu pętli wewnętrznej. 

### <a name="pre-production"></a>Przedprodukcyjne 
W przypadku laboratorium w potoku ciągłej integracji/ciągłego wdrażania można łatwiej korzystać z wielu różnych środowisk przedprodukcyjnych lub maszyn działających w tym samym czasie na potrzeby testów asynchronicznych. Różne infrastruktury pomocy technicznej, takie jak agenci kompilacji, można wdrażać i zarządzać nimi w ramach laboratorium. 

## <a name="devops-with-devtest-labs"></a>DevOps z DevTest Labs 

### <a name="development--operation"></a>Programowanie/operacja 
Laboratorium powinno być skoncentrowane na zespole, który działa w obszarze funkcji. Ten typowy fokus umożliwia udostępnianie zasobów specyficznych dla obszaru, takich jak narzędzia, skrypty i szablony Menedżer zasobów. Pozwala to na szybsze zmiany przy jednoczesnym ograniczeniu negatywnych efektów do mniejszej grupy. Te zasoby udostępnione umożliwiają deweloperom tworzenie maszyn wirtualnych na potrzeby programowania przy użyciu wszystkich niezbędnych kodów, narzędzi i konfiguracji. Można je utworzyć dynamicznie lub mieć system, który tworzy obrazy podstawowe z dostosowaniami. Deweloperzy nie tylko mogą tworzyć maszyny wirtualne, ale mogą również tworzyć środowiska DevTest Labs na podstawie niezbędnych szablonów do tworzenia odpowiednich zasobów platformy Azure w laboratorium. Wszelkie zmiany i destrukcyjne działania mogą być wykonywane w środowisku laboratoryjnym bez wpływu na innych użytkowników. Rozważ scenariusz, w którym produkt jest systemem autonomicznym zainstalowanym na komputerze klienta. W tym scenariuszu w DevTest Labs Ulepszono tworzenie maszyn wirtualnych, które obejmują Instalowanie dodatkowego oprogramowania przy użyciu artefaktów i wstępne tworzenie konfiguracji klienta w celu szybkiego testowania pętli wewnętrznej w kodzie. 
  
## <a name="cicd-pipeline"></a>Potok ciągłej integracji/ciągłego dostarczania 
Potok ciągłej integracji/ciągłego wdrażania to jeden z kluczowych składników w DevOps, który przenosi kod z żądania ściągnięcia dewelopera, integruje go z istniejącym kodem i wdraża go w ekosystemie produkcyjnym. Wszystkie zasoby nie muszą znajdować się w środowisku laboratoryjnym. Na przykład host Jenkins można skonfigurować poza laboratorium jako bardziej trwały zasób. Poniżej przedstawiono kilka konkretnych przykładów integracji laboratoriów z potokiem. 

### <a name="build"></a>Kompilacja 
Potok kompilacji koncentruje się na tworzeniu pakietu składników, który zostanie przetestowany w celu przekazania go do potoku wydania. Laboratoria mogą być częścią potoku kompilacji jako lokalizacji agentów kompilacji i innych zasobów pomocy technicznej. Możliwość dynamicznego kompilowania infrastruktury pozwala na większą kontrolę. Dzięki możliwości korzystania z wielu środowisk w laboratorium każda kompilacja może być uruchamiana asynchronicznie, przy użyciu identyfikatora kompilacji jako części informacji o środowisku, aby jednoznacznie identyfikować zasoby do określonej kompilacji.   

W przypadku agentów kompilacji zdolność laboratorium do ograniczania dostępu zwiększa bezpieczeństwo i zmniejsza prawdopodobieństwo przypadkowych uszkodzeń.  

### <a name="test"></a>Testowanie 
DevTest Labs umożliwia potok ciągłej integracji/ciągłego wdrażania, który umożliwia automatyzację tworzenia zasobów platformy Azure (maszyn wirtualnych, środowisk), których można użyć do automatycznego i ręcznego testowania. Maszyny wirtualne zostaną utworzone przy użyciu artefaktów lub formuł, które używają informacji z procesu kompilacji, aby utworzyć różne konfiguracje niestandardowe niezbędne do testowania.   

### <a name="release"></a>Release 
DevTest Labs jest często używany do weryfikacji w sekcji Release przed wdrożeniem kodu. Jest to podobne do testowania w sekcji Kompilacja. Zasobów produkcyjnych nie należy wdrażać w ramach DevTest Labs. 

### <a name="customization"></a>Dostosowywanie 
W usłudze Azure DevOps istnieją zadania, które umożliwiają manipulowanie maszynami wirtualnymi i środowiskami w określonych laboratoriach. Chociaż Azure DevOps Services są jednym ze sposobów zarządzania potokiem ciągłej integracji/ciągłego wdrażania, można zintegrować laboratorium z dowolnym systemem, który obsługuje możliwość wywoływania interfejsów API REST, wykonywania skryptów programu PowerShell lub korzystania z interfejsu wiersza polecenia platformy Azure. 

Chociaż niektórzy menedżerowie potoku CI/CD mają istniejące wtyczki Open Source, które mogą zarządzać zasobami w ramach platformy Azure i DevTest Labs. Może być konieczne użycie niektórych niestandardowych skryptów w celu dopasowania do określonych potrzeb potoku.  Z tego względu podczas wykonywania zadania jednostka usługi jest używana z odpowiednią rolą w celu uzyskania dostępu do laboratorium. Nazwa główna usługi zwykle wymaga dostępu roli współautor do laboratorium. Aby uzyskać więcej informacji, zobacz [Integrowanie usługi Azure DevTests Labs z potokiem ciągłej integracji i dostarczania na platformie Azure DevOps](devtest-lab-integrate-ci-cd.md). 
 