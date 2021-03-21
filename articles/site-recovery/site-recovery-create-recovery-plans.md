---
title: Utwórz/Dostosuj plany odzyskiwania w Azure Site Recovery
description: Dowiedz się, jak tworzyć i dostosowywać plany odzyskiwania na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009725"
---
# <a name="create-and-customize-recovery-plans"></a>Tworzenie i dostosowywanie planów odzyskiwania

W tym artykule opisano sposób tworzenia i dostosowywania planu odzyskiwania dla trybu failover w [Azure Site Recovery](site-recovery-overview.md). Przed rozpoczęciem [Dowiedz się więcej](recovery-plan-overview.md) o planach odzyskiwania.

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

1. W magazynie Recovery Services wybierz pozycję **plany odzyskiwania (Site Recovery)**  >  **+ plan odzyskiwania**.
2. W obszarze **Utwórz plan odzyskiwania** Określ nazwę planu.
3. Wybierz źródło i cel na podstawie maszyn w planie, a następnie wybierz **Menedżer zasobów** dla modelu wdrażania. Lokalizacja źródłowa musi mieć maszyny, na których włączono tryb failover i odzyskiwanie. 

    **Tryb failover** | **Element źródłowy** | **Cel** 
   --- | --- | ---
   Azure–Azure | Wybierz region platformy Azure | Wybierz region platformy Azure
   Oprogramowanie VMware do platformy Azure | Wybierz serwer konfiguracji | Wybierz platformę Azure
   Maszyny fizyczne na platformie Azure | Wybierz serwer konfiguracji | Wybierz platformę Azure   
   Z funkcji Hyper-V do platformy Azure | Wybierz nazwę lokacji funkcji Hyper-V | Wybierz platformę Azure
   Funkcja Hyper-V (zarządzana przez program VMM) na platformie Azure  | Wybierz serwer programu VMM | Wybierz platformę Azure
  
    . Weź pod uwagę następujące kwestie:
    - Można użyć planu odzyskiwania zarówno w przypadku trybu failover na platformie Azure, jak i powrotu po awarii z platformy Azure.
    - Lokalizacja źródłowa musi mieć maszyny, na których włączono tryb failover i odzyskiwanie.
    - Plan odzyskiwania może zawierać maszyny z tym samym źródłem i elementem docelowym.
    - W tym samym planie można dołączyć maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V zarządzane przez program VMM.
    - Maszyny wirtualne VMware i serwery fizyczne mogą znajdować się w tym samym planie.

4. W obszarze **Wybierz elementy maszyny wirtualne** wybierz maszyny (lub grupę replikacji), które chcesz dodać do planu. Następnie kliknij przycisk **OK**.
    - Maszyny są dodawane do planu jako Grupa domyślna (Grupa 1). Po przejściu w tryb failover wszystkie maszyny w tej grupie są uruchamiane w tym samym czasie.
    - Można wybrać tylko te maszyny, które znajdują się w określonej lokalizacji źródłowej i docelowej. 
5. Kliknij przycisk **OK** , aby utworzyć plan.

## <a name="add-a-group-to-a-plan"></a>Dodawanie grupy do planu

Należy utworzyć dodatkowe grupy i dodać maszyny do różnych grup, aby można było określić inne zachowanie dla poszczególnych grup. Można na przykład określić, kiedy maszyny w grupie mają być uruchamiane po przejściu w tryb failover, lub określić niestandardowe akcje dla każdej grupy.

1. W obszarze **plany odzyskiwania** kliknij prawym przyciskiem myszy plan > **Dostosuj**. Domyślnie po utworzeniu planu wszystkie dodane do niego maszyny znajdują się w grupie domyślnej 1.
2. Kliknij pozycję **+ Grupa**. Domyślnie Nowa grupa jest numerowana w kolejności, w jakiej została dodana. Możesz mieć maksymalnie siedem grup.
3. Wybierz maszynę, którą chcesz przenieść do nowej grupy, kliknij pozycję **Zmień grupę**, a następnie wybierz nową grupę. Alternatywnie kliknij prawym przyciskiem myszy nazwę grupy > **chroniony element** i Dodaj maszyny do grupy. Maszyna lub grupa replikacji może należeć tylko do jednej grupy w planie odzyskiwania.


## <a name="add-a-script-or-manual-action"></a>Dodawanie skryptu lub akcji ręcznej

Możesz dostosować plan odzyskiwania, dodając skrypt lub akcję ręczną. Należy pamiętać, że:

- Jeśli przeprowadzasz replikację do platformy Azure, możesz zintegrować elementy Runbook usługi Azure Automation z planem odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md).
- W przypadku replikowania maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center VMM można utworzyć skrypt na lokalnym serwerze programu VMM i uwzględnić go w planie odzyskiwania.
- Po dodaniu skryptu dodaje nowy zestaw akcji dla grupy. Na przykład zestaw kroków wstępnych dla grupy 1 jest tworzony z nazwą *grupy 1: kroki poprzedzające*. Wszystkie kroki wstępne są wymienione w tym zestawie. Skrypt można dodać w lokacji głównej tylko wtedy, gdy jest wdrożony serwer programu VMM.
- Po dodaniu akcji ręcznej, gdy plan odzyskiwania zostanie uruchomiony, zatrzyma się w punkcie, w którym wstawiono akcję ręczną. W oknie dialogowym zostanie wyświetlony komunikat z prośbą o określenie, czy czynność ręczna została ukończona.
- Aby utworzyć skrypt na serwerze programu VMM, postępuj zgodnie z instrukcjami w [tym artykule](hyper-v-vmm-recovery-script.md).
- Skrypty można stosować podczas pracy w trybie failover w lokacji dodatkowej oraz podczas powrotu po awarii z lokacji dodatkowej do podstawowego. Obsługa zależy od scenariusza replikacji:
    
    **Scenariusz** | **Tryb failover** | **Awarii**
    --- | --- | --- 
    Azure–Azure  | Element Runbook | Element Runbook
    Z programu VMware do platformy Azure | Element Runbook | NA 
    Funkcja Hyper-V z programem VMM na platformie Azure | Element Runbook | Skrypt
    Z lokacji funkcji Hyper-V na platformę Azure | Element Runbook | NA
    Program VMM do pomocniczego programu VMM | Skrypt | Skrypt

1. W planie odzyskiwania kliknij krok, do którego powinna zostać dodana akcja, a następnie określ, kiedy powinna wystąpić akcja:
    1. Jeśli chcesz wykonać akcję, zanim maszyny w grupie zostaną uruchomione po przejściu w tryb failover, wybierz pozycję **Dodaj przed akcją**.
    1. Jeśli chcesz, aby akcja miała miejsce po uruchomieniu maszyn w grupie po zakończeniu pracy w trybie failover, wybierz pozycję **Dodaj akcję post**. Aby przenieść pozycję akcji, wybierz przyciski **Przenieś w górę** lub **Przenieś w dół** .
2. W obszarze **Wstaw akcję** wybierz pozycję **skrypt** lub **Akcja ręczna**.
3. Jeśli chcesz dodać akcję ręczną, wykonaj następujące czynności:
    1. Wpisz nazwę akcji i wpisz instrukcje akcji. Osoba, na której działa przejście w tryb failover, zobaczy te instrukcje.
    1. Określ, czy chcesz dodać akcję ręczną dla wszystkich typów trybu failover (test, tryb failover, planowana praca w trybie failover (jeśli dotyczy)). Następnie kliknij przycisk **OK**.
4. Jeśli chcesz dodać skrypt, wykonaj następujące czynności:
    1. W przypadku dodawania skryptu programu VMM wybierz pozycję **tryb failover do skryptu programu VMM**, a w polu **ścieżka skryptu** wpisz ścieżkę względną do udziału. Na przykład, jeśli udział znajduje się w \\ \<VMMServerName> \MSSCVMMLibrary\RPScripts, określ ścieżkę: \RPScripts\RPScript.PS1.
    1. Jeśli dodajesz książkę uruchamiania usługi Azure Automation, określ **konto Azure Automation** , w którym znajduje się element Runbook, a następnie wybierz odpowiedni **skrypt elementu Runbook platformy Azure**.
5. Uruchom test pracy w trybie failover planu odzyskiwania, aby upewnić się, że skrypt działa zgodnie z oczekiwaniami.

## <a name="watch-a-video"></a>Obejrzyj film

Obejrzyj film wideo, który pokazuje, jak utworzyć plan odzyskiwania.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [uruchamianiu trybu failover](site-recovery-failover.md).  

    
