---
title: Role i zadania procesu nauka danych zespołu
description: Konspekt najważniejszych składników, ról pracowników i skojarzonych zadań dla grupy nauki o danych.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082753"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Role i zadania procesu nauka danych zespołu

Proces nauki o danych zespołowych (przetwarzania TDSP) to struktura opracowana przez firmę Microsoft, która oferuje strukturalną metodologię do wydajnego tworzenia rozwiązań do analizy predykcyjnej i inteligentnych aplikacji. W tym artykule przedstawiono podstawowe role pracowników i powiązane zadania dotyczące standaryzacji zespołu ds. analizy danych na tym procesie.

Ten artykuł wprowadzający zawiera linki do samouczków dotyczących konfigurowania środowiska przetwarzania TDSP. Samouczki zawierają szczegółowe wskazówki dotyczące używania Azure DevOps Projects, repozytoriów Azure Repos i Azure Boards.  Celem motywacji jest przechodzenie z koncepcji poprzez modelowanie i wdrażanie.

Samouczki korzystają z usługi Azure DevOps, ponieważ jest to implementacja przetwarzania TDSP w firmie Microsoft. Usługa Azure DevOps ułatwia współpracę poprzez integrację zabezpieczeń opartych na rolach, zarządzanie elementami roboczymi i śledzenie oraz hosting kodu, udostępnianie i kontrola źródła. Samouczki korzystają również z usługi Azure [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) jako pulpitu analitycznego, który ma kilka popularnych narzędzi do nauki o danych, które są wstępnie skonfigurowane i zintegrowane z oprogramowaniem firmy Microsoft i usługami platformy Azure. 

Możesz użyć samouczków, aby zaimplementować przetwarzania TDSP przy użyciu innych narzędzi do obsługi kodu, planowania Agile i środowisk deweloperskich, ale niektóre funkcje mogą być niedostępne.

## <a name="structure-of-data-science-groups-and-teams"></a>Struktura grup i zespołów analizy danych

Funkcje analizy danych w przedsiębiorstwach są często zorganizowane w następujące hierarchie:

- Grupa nauki o danych
  - Zespół ds. analizy danych w grupie

W takiej strukturze istnieją grupy potencjalni klienci i liderzy zespołu. Zazwyczaj projekt analizy danych jest wykonywany przez zespół ds. analizy danych. Zespoły do nauki o danych mają potencjalną liczbę zadań związanych z zarządzaniem projektami i ładumi oraz indywidualnymi analitykami danych i inżynierami do wykonywania części projektu analizy danych i inżynierii danych. Początkowa konfiguracja projektu i nadzór odbywa się przez liderów grup, zespołów lub projektów.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definicja i zadania dla czterech ról przetwarzania TDSP
Z założeniem, że jednostka analizy danych składa się z zespołów w grupie, istnieją cztery odrębne role dla przetwarzania TDSP personel:

1. **Menedżer grupy**: zarządza całą jednostką analizy danych w przedsiębiorstwie. Jednostka do nauki o danych może mieć wiele zespołów, z których każdy pracuje w wielu projektach naukowych w różnych firmach. Menedżer grupy może delegować zadania do surogatu, ale zadania skojarzone z rolą nie zmieniają się.
   
2. **Lider zespołu**: zarządza zespołem w jednostce analizy danych przedsiębiorstwa. Zespół składa się z wielu analityków danych. W przypadku małej jednostki analizy danych Menedżer grupy i lider zespołu mogą należeć do tej samej osoby.
   
3. **Lider projektu**: zarządza codziennymi działaniami poszczególnych naukowców zajmujących się danymi w konkretnym projekcie analizy danych.
   
4. **Poszczególni Współautorzy projektu**: naukowców danych, analityków biznesowych, inżynierów danych, architektów i innych, którzy wykonują projekt analizy danych.

> [!NOTE]
> W zależności od struktury i rozmiaru przedsiębiorstwa pojedyncza osoba może odtwarzać więcej niż jedną rolę lub więcej niż jedna osoba może wypełnić rolę.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Zadania, które mają zostać wykonane przez cztery role

Na poniższym diagramie przedstawiono zadania najwyższego poziomu dla każdej roli procesu nauki o danych zespołowych. Ten schemat i poniższe, bardziej szczegółowy zarys zadań dla każdej roli przetwarzania TDSP, może pomóc w wyborze potrzebnego samouczka na podstawie Twoich obowiązków.

![Przegląd ról i zadań](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Zadania Menedżera grupy

Menedżer grupy lub wyznaczeni administrator systemu przetwarzania TDSP wykonuje następujące zadania w celu zastosowania przetwarzania TDSP:

- Tworzy **organizację** usługi Azure DevOps i projekt grupy w organizacji. 
- Tworzy **repozytorium szablonu projektu** w projekcie grupy usługi Azure DevOps i wystawia je z repozytorium szablonu projektu opracowanego przez zespół programu Microsoft przetwarzania tdspe. Repozytorium szablonów projektów programu Microsoft przetwarzania TDSPe zawiera następujące informacje:
  - **Standardowa struktura katalogów**, w tym katalogi dla danych, kodu i dokumentów.
  - Zestaw **znormalizowanych szablonów dokumentów** , który prowadzi do wydajnego procesu nauki o danych.
- Tworzy **repozytorium narzędzi**i nasiona z repozytorium narzędzi opracowanego przez zespół Microsoft przetwarzania tdspe. Repozytorium narzędzi przetwarzania TDSP firmy Microsoft udostępnia zestaw przydatnych narzędzi, które ułatwiają wydajniejsze działanie Analityka danych. Repozytorium narzędzi firmy Microsoft zawiera narzędzia do interaktywnej eksploracji, analizy, raportowania i modelowania linii bazowej oraz raportowania.
- Konfiguruje **zasady kontroli zabezpieczeń** dla konta organizacji.

Aby uzyskać szczegółowe instrukcje, zobacz [zadania programu Group Manager dla zespołu ds. analizy danych](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Zadania lidera zespołu

Lider zespołu lub wyznaczeni Administrator projektu wykonuje następujące zadania w celu zastosowania przetwarzania TDSP:

- Tworzy **projekt** zespołowy w organizacji usługi Azure DevOps dla grupy.
- Tworzy **repozytorium szablonów projektu** w projekcie i wystawia je z repozytorium szablonów projektów grupy skonfigurowanym przez Menedżera grupy lub delegata.
- Tworzy **repozytorium narzędzi zespołowych**, umieszcza je w repozytorium narzędzi grupy i dodaje do repozytorium narzędzia specyficzne dla zespołu.
- Opcjonalnie tworzy [Magazyn plików platformy Azure](https://azure.microsoft.com/services/storage/files/) do przechowywania przydatnych zasobów danych dla zespołu. Inni członkowie zespołu mogą instalować ten udostępniony magazyn plików w chmurze na swoich pulpitach analitycznych.
- Opcjonalnie instaluje magazyn plików platformy Azure na **DSVM** zespołu i dodaje do niego zasoby danych zespołu.
- Konfiguruje **kontrolę zabezpieczeń** , dodając członków zespołu i konfigurując ich uprawnienia.

Aby uzyskać szczegółowe instrukcje, zobacz temat [zadania lidera zespołu dla zespołu ds. analizy danych](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Zadania lidera projektu

Klient projektu wykonuje następujące zadania w celu przyjęcia przetwarzania TDSP:

- Tworzy **repozytorium projektu** w projekcie zespołowym i wystawia je z repozytorium szablonu projektu.
- Opcjonalnie tworzy **Magazyn plików platformy Azure** do przechowywania zasobów danych projektu.
- Opcjonalnie instaluje magazyn plików platformy Azure do **DSVM** i dodaje do niego zasoby danych projektu.
- Konfiguruje **kontrolę zabezpieczeń** , dodając członków projektu i konfigurując ich uprawnienia.

Aby uzyskać szczegółowe instrukcje, zobacz temat [zadania lidera projektu dla zespołu ds. analizy danych](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Zadania poszczególnych współautorów projektu

Pojedynczy współautor projektu, zazwyczaj analityk danych, przeprowadza następujące zadania przy użyciu przetwarzania TDSP:

- Klonuje **repozytorium projektu** skonfigurowane przez lidera projektu.
- Opcjonalnie instaluje współużytkowany zespół i **Magazyn plików platformy Azure** w **Data Science Virtual Machine** (DSVM).
- Wykonuje projekt.

Aby uzyskać szczegółowe instrukcje dotyczące dołączania do projektu, zobacz temat [zadania poszczególnych współautorów dla zespołu ds. analizy danych](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Przepływ pracy wykonywania projektu analizy danych

Wykonując odpowiednie samouczki, analityki danych, potencjalni klienci projektu i liderzy zespołu mogą tworzyć elementy robocze do śledzenia wszystkich zadań i etapów projektu od początku do końca. Korzystanie Azure Repos promuje współpracę między analitykami danych i gwarantuje, że artefakty generowane podczas wykonywania projektu są kontrolowane i udostępniane przez wszystkich członków projektu. Usługa Azure DevOps umożliwia łączenie Azure Boards elementów roboczych z gałęziami repozytorium Azure Repos i łatwe śledzenie tego, co zostało zrobione dla elementu pracy.

Poniższy rysunek przedstawia przepływ pracy przetwarzania TDSP na potrzeby wykonywania projektu:

![Typowy przepływ pracy projektu analizy danych](./media/roles-tasks/overview-project-execute.png)

Kroki przepływu pracy można grupować w trzy działania:

- Liderzy projektu przeprowadzają Planowanie przebiegu
- Analitykowie danych tworzą artefakty w odniesieniu `git` do elementów roboczych
- Potencjalni klienci projektu lub inni członkowie zespołu wykonują przeglądy kodu i scalają gałęzie robocze z gałęzią podstawową

Aby uzyskać szczegółowe instrukcje dotyczące przepływu pracy wykonywanego w projekcie, zobacz [opracowywanie projektów analizy danych](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repozytorium szablonów projektów przetwarzania TDSP

Użyj [repozytorium szablonów projektów](https://github.com/Azure/Azure-TDSP-ProjectTemplate) zespołu przetwarzania TDSP firmy Microsoft, aby obsłużyć wydajne wykonywanie projektu i współpracę. Repozytorium oferuje ustandaryzowaną strukturę katalogów i szablony dokumentów, których można użyć do własnych projektów przetwarzania TDSP.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z bardziej szczegółowymi opisami ról i zadań zdefiniowanych przez proces nauka danych zespołu:

- [Zadania Menedżera grupy dla zespołu ds. analizy danych](group-manager-tasks.md)
- [Zadania lidera zespołu dla zespołu ds. analizy danych](team-lead-tasks.md)
- [Zadania lidera projektu dla zespołu ds. analizy danych](project-lead-tasks.md)
- [Projekt poszczególnych zadań współautora dla zespołu ds. analizy danych](project-ic-tasks.md)
