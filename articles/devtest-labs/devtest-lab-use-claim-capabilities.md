---
title: Korzystanie z funkcji roszczeń w Azure DevTest Labs | Microsoft Docs
description: Poznaj różne scenariusze korzystania z możliwości usługi Claim/unroście Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 73ed3c0b94a66f5d17b5c8e2561c65bb48579aa1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476533"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Korzystanie z funkcji roszczeń w Azure DevTest Labs
Usługa Azure DevTest Labs podnosi efektywność i wydajność deweloperów i testerów. Ten artykuł koncentruje się na zaroście lub odroście maszyn wirtualnych w Azure DevTest Labs. Przedstawiono w nim również różne sposoby ulepszenia środowiska użytkownika. Przed przystąpieniem do różnych scenariuszy, w których ta funkcja może zostać użyta, przyjrzyjmy się tym, co **to jest i** jak działa.

## <a name="claimable-machines"></a>Maszyny z zajmowaniem
Komputer z zastrzeżeniem jest maszyną wirtualną, która jest tworzona w laboratorium bez właściciela. Gdy komputer zostanie przejęty, użytkownik ma pełen zakres opcji dla tej maszyny wirtualnej. Gdy użytkownik przejmuje komputer, wprowadzono kilka zmian. Maszyna wirtualna zostanie przeniesiona z listy maszyn wirtualnych z **zastrzeżeniem** do listy **moje maszyny wirtualne** w Azure Portal. 

Użytkownik może nawiązać połączenie z maszyną wirtualną, dostosować artefakty, uruchomić ponownie, zatrzymać lub odrościć maszynę. Istnieje kilka sposobów, aby umożliwić domaganie się maszyny wirtualnej:

- Utwórz maszynę i Odbierz ją, aby przenieść ją do puli z zastrzeżeniem. 
- Utwórz maszynę wirtualną i umieść ją w puli udostępnionej przy użyciu [ustawień zaawansowanych](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Istnieją dwa przypadki, w których można efektywnie wykorzystać możliwości roszczeń/niezgłaszania roszczeń. Pierwszy przypadek wymaga więcej forethought i planowania, aby mógł zostać zaprojektowany i wykonany prawidłowo. Drugim jest więcej sytuacji. Poniżej przedstawiono kilka przykładów różnych przypadków.

## <a name="designed-use-of-claimable-machines"></a>Zaprojektowane użycie maszyn z żądaniami

- **Tworzenie oprogramowania/testowanie:** Umożliwiają deweloperom i testerom wydajniejszą pracę dzięki skonfigurowaniu maszyn gotowych i w stanie niedostępnym. Posiadanie zestawu maszyn wirtualnych z różnymi konfiguracjami, narzędziami niezbędnymi i najnowszym kodem umożliwia użytkownikom zgłaszanie maszyn wirtualnych i rozpoczęcie pracy bez konieczności poświęcania czasu na skonfigurowanie maszyny. Przed zażądaniem maszyn wirtualnych maszyny są obsługiwane, ale są zamykane, co minimalizuje koszt posiadania maszyn, które są rzadziej używane. Gdy maszyny wirtualne są zbędne, użytkownik po prostu przejmuje maszynę wirtualną, co spowoduje uruchomienie komputera. Opcja unclaim nie jest tak przydatna w tym przypadku, ponieważ tworzenie nowej maszyny wirtualnej jest często łatwiejsze i tańsze.
- **Klasy/laboratoria:** Należy wstępnie skonfigurować maszyny wirtualne dla klasy lub laboratorium, aby studenci mogli od razu połączyć się z maszyną przy użyciu Azure Portal.  Po potwierdzeniu przez studenta maszyny wirtualnej laboratorium gwarantuje, że nikt nie może zatwierdzić tego samego komputera. Automatyzacja tego procesu zapewnia dostępność wymaganej liczby maszyn z określonym środowiskiem. Jeśli uczniowie nie są wyświetlane lub działają z opóźnieniem, maszyny nieodebrane mogą być dostępne, dopóki sesja nie zostanie przekroczona z minimalnymi kosztami. Opcja unclaim nie jest skuteczna w tym scenariuszu, ponieważ maszyna wirtualna jest w nieznanym stanie po zakończeniu poprzedniego użytkownika.
- **Demonstracje:** Użyj maszyn do pokazów, gdzie maszyny w laboratorium są skonfigurowane z określonymi środowiskami. Ta funkcja jest przydatna, gdy wiele osób może uzyskać demonstrację w tym samym czasie lub w losowych porach, na przykład na konferencji. Opcja unclaim może być przydatna w tej sytuacji, ponieważ Demonstracja nie powinna zmienić stanu maszyny, umożliwiając użytkownikom zwracanie maszyny wirtualnej z powrotem do puli z możliwością docelową w celu przeprowadzenia następnej demonstracyjnej. W przypadku nieograniczonego komputera i ponoszenia minimalnych kosztów maszyny wirtualne mogą pozostać w laboratorium przez dłuższy czas.
- **Pracownicy tymczasowy/wykonawczy:** Zezwól użytkownikom na korzystanie z komputera. Gdy opuszczają je, zwracają maszynę wirtualną do puli z zastrzeżeniem bez utraty danych. Po odroście maszyny wirtualnej inny użytkownik może przejąć maszynę wirtualną i kontynuować lub przejrzeć maszynę w celu uzyskania dodatkowych informacji.
- **Ogólnie rzecz biorąc:** Możliwość automatycznego konfigurowania i wdrażania maszyn wirtualnych w określonej erze jest przydatna w wielu różnych sytuacjach. Istnieje kilka różnych sytuacji, w których funkcja Claim/unclaims ułatwia użytkownikom wydajniejsze proces tworzenia maszyn wirtualnych w stanie niedostępnym z konfiguracją zestawu. Konfiguracje mogą obejmować różne systemy operacyjne, Języki, dyski lub [inne oprogramowanie (artefakty)](devtest-lab-artifact-author.md) , w zależności od potrzeb. Możliwość pozyskania maszyny wirtualnej z laboratorium umożliwia użytkownikowi laboratorium uzyskanie prawidłowo skonfigurowanego systemu bez poświęcania czasu lub nakładu pracy podczas konfigurowania maszyny. Menedżer laboratorium może użyć żądanego stanu maszyn wirtualnych, aby zwiększyć liczbę wygenerowanych maszyn, oczyścić maszynę i określić priorytety konfiguracji. [Fabryka obrazów](image-factory-create.md) jest dobrym przykładem zautomatyzowanego procesu tworzenia maszyn wirtualnych i obrazów dla wielu laboratoriów. Skrypty można modyfikować w taki sposób, aby wykonywały jedną z następujących sytuacji z odpowiednimi zmianami lub użyć jako odniesienia do tworzenia niestandardowego systemu.

## <a name="situational-use-of-claimable-machines"></a>Przypadekowe wykorzystywanie maszyn z zażądanymi żądaniami

- Korzystaj z możliwości roszczeń/niezgłaszania roszczeń, która pozwala użytkownikom na przekazywanie kontroli nad maszynami z jednego do drugiego i nie musi jasno wiedzieć, kto będzie do niego korzystać.
- Opracowywanie, testowanie i debugowanie scenariusza, w którym konkretna Konfiguracja komputera może odtworzyć usterkę, można odejść, aby inny deweloper mógł przejąć maszynę i kontynuować pracę. Ta funkcja jest szczególnie przydatna, ponieważ większa liczba osób pracuje zdalnie w różnych obszarach świata. 
- Członkowie zespołu mogą korzystać z jednego środowiska. Można na przykład ręcznie skonfigurować złożone środowisko, które nie może być zautomatyzowane lub tworzyć zasoby, które mogą obsługiwać tylko modyfikacje pojedynczych danych wejściowych, takich jak obrazy. W przeszłości ten problem został rozwiązany przez zainicjowanie i uruchomienie dedykowanej maszyny. Funkcja, którą można zastrzec, to poprawa w procesie ręcznym przez posiadanie wbudowanej kontroli dostępu użytkownika i identyfikacji wizualnej, jeśli jest dostępny. W przypadku braku zgłoszenia maszyna wirtualna jest nieobsługiwana w celu obniżenia kosztów.
- Posiadanie dysku danych dołączonego do maszyny wirtualnej. Każdy dysk do ~ 1 TB danych umożliwia przekazywanie dużych ilości danych bez konieczności kopiowania ani duplikowania danych. Maszyna wirtualna zostanie początkowo utworzona z dołączonym dyskiem z dużą ilością danych.  Każdy użytkownik może następnie przejąć komputer i uzyskać dostęp do danych. Po zakończeniu Odbierz maszynę wirtualną, aby zezwolić innym użytkownikom na korzystanie z tej maszyny.

Istnieją pewne ograniczenia dotyczące korzystania z maszyn z ograniczeniami, najczęściej mających na celu uzyskanie dostępu do komputera. Jeśli komputer jest przyłączony do domeny, użytkownik, który przejmie ten komputer, będzie musiał uzyskać dostęp, zazwyczaj jest to realizowane przez przyznanie dostępu do grupy obejmującej wszystkich użytkowników w laboratorium podczas tworzenia maszyny wirtualnej. Jeśli komputer nie został przyłączony do domeny, należy uruchomić polecenie **zresetuj hasło maszyny wirtualnej** w repozytorium publicznym, aby dodać użytkownika jako administratora.  Artefakty mogą być stosowane nawet po rozpoczęciu lub zażądaniu maszyny.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem: [Tworzenie i zarządzanie maszynami wirtualnymi z możliwością dochodzenia w Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
