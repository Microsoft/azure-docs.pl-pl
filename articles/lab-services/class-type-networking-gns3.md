---
title: Skonfiguruj laboratorium sieciowe przy użyciu Azure Lab Services i GNS3 | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium przy użyciu Azure Lab Services do uczenia sieci za pomocą usługi GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500515"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Konfigurowanie laboratorium do uczenia klasy sieci 
W tym artykule opisano sposób konfigurowania klasy, która koncentruje się na umożliwieniu uczniów do emulowania, konfigurowania, testowania i rozwiązywania problemów z sieciami wirtualnymi i rzeczywistymi przy użyciu oprogramowania [GNS3](https://www.gns3.com/) . 

Ten artykuł zawiera dwa główne sekcje. W pierwszej sekcji opisano, jak utworzyć laboratorium klasowe. Druga sekcja zawiera informacje na temat tworzenia maszyny szablonu z włączoną funkcją wirtualizacji zagnieżdżonej oraz z zainstalowaną i skonfigurowaną GNS3.

## <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, musisz mieć subskrypcję platformy Azure, aby rozpocząć pracę. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services lub użyć istniejącego konta. Zapoznaj się z poniższym samouczkiem dotyczącym tworzenia nowego konta laboratorium: [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).

Postępuj zgodnie z [tym samouczkiem](tutorial-setup-classroom-lab.md) , aby utworzyć nowe laboratorium, a następnie Zastosuj następujące ustawienia:

| Rozmiar maszyny wirtualnej | Image (Obraz) |
| -------------------- | ----- | 
| Duże (Wirtualizacja zagnieżdżona) | Windows 10 Pro, wersja 1909 |

## <a name="template-machine"></a>Komputer szablonu 

Po utworzeniu komputera szablonu Uruchom maszynę i Połącz się z nią, aby wykonać trzy następujące zadania. 
 
1. Przygotuj maszynę szablonu do wirtualizacji zagnieżdżonej.
2. Zainstaluj GNS3.
3. Utwórz zagnieżdżoną maszynę wirtualną GNS3 w funkcji Hyper-V.
4. Skonfiguruj GNS3 do korzystania z maszyny wirtualnej funkcji Hyper-V systemu Windows.
5. Dodaj odpowiednie urządzenia.
6. Publikuj szablon.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Przygotuj komputer szablonu do wirtualizacji zagnieżdżonej
- Postępuj zgodnie z instrukcjami w [tym artykule](how-to-enable-nested-virtualization-template-vm.md) , aby przygotować maszynę wirtualną szablonu do wirtualizacji zagnieżdżonej. 

### <a name="install-gns3"></a>Zainstaluj GNS3
- Postępuj zgodnie z instrukcjami dotyczącymi [instalowania GNS3 w systemie Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Upewnij się, że zainstalowano **maszynę wirtualną GNS3** w oknie dialogowym składnika, zobacz poniżej.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Ostatecznie zobaczysz GNS3 maszynę wirtualną. Upewnij się, że wybrano opcję **Hyper-V** .

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Ta opcja spowoduje pobranie skryptu programu PowerShell i plików VHD w celu utworzenia maszyny wirtualnej GNS3 w Menedżerze funkcji Hyper-V. Kontynuuj instalację, używając wartości domyślnych. **Po zakończeniu instalacji nie uruchamiaj GNS3**.

### <a name="create-gns3-vm"></a>Tworzenie maszyny wirtualnej GNS3
Po zakończeniu instalacji plik zip **"GNS3.VM.Hyper-V.2.2.17.zip"** zostanie pobrany do tego samego folderu, w którym znajduje się plik instalacyjny zawierający dyski i skrypt programu PowerShell w celu utworzenia maszyny wirtualnej funkcji Hyper-V.
- **Wyodrębnij wszystko** na GNS3.VM.Hyper-V.2.2.17.zip.  Ta akcja spowoduje wyodrębnienie dysków i skryptu programu PowerShell w celu utworzenia maszyny wirtualnej.
- **Uruchom polecenie przy użyciu programu PowerShell** na skrypcie programu powershell "create-vm.ps1", klikając prawym przyciskiem myszy plik.
- Może zostać wyświetlone żądanie zmiany zasad wykonywania. Wprowadź wartość "Y", aby wykonać skrypt.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Po zakończeniu skryptu można potwierdzić, że maszyna wirtualna "GNS3 VM" została utworzona w Menedżerze funkcji Hyper-V.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Konfigurowanie GNS3 do korzystania z maszyny wirtualnej funkcji Hyper-V
Teraz, gdy GNS3 jest zainstalowana i dodawana jest maszyna wirtualna GNS3, uruchom GNS3, aby połączyć te dwa ze sobą.  [Kreator instalacji GNS3 zostanie uruchomiony automatycznie.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)..  
- Użyj **urządzeń uruchamiających z maszyny wirtualnej.** .  Użyj wartości domyślnych dla pozostałej części kreatora, dopóki nie zostanie **znalezione narzędzie VMware vmrun.** .

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Wybierz przycisk **OK** i **Anuluj działanie** kreatora.
- Aby nawiązać połączenie z maszyną wirtualną funkcji Hyper-V, Otwórz pozycję **Edytuj**  ->  **Preferencje**  ->  **GNS3 maszyny wirtualnej** i wybierz pozycję **Włącz maszynę wirtualną GNS3** i wybierz opcję **Hyper-V** .
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Dodaj odpowiednie urządzenia

W tym momencie należy dodać odpowiednie [urządzenia dla klasy.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Publikowanie szablonu

Teraz, gdy maszyna wirtualna jest skonfigurowana prawidłowo i jest gotowa do opublikowania, istnieje kilka kluczowych punktów do sprawdzenia.
- Upewnij się, że maszyna wirtualna GNS3 jest wyłączana lub wyłączona.  Publikowanie, gdy maszyna wirtualna jest nadal uruchomiona, spowoduje uszkodzenie maszyny wirtualnej.
- Zamknięcie GNS3, opublikowanie i uruchomienie może prowadzić do niezamierzonych efektów ubocznych.
- Wyczyść wszystkie pliki instalacyjne lub inne niepotrzebne pliki.

## <a name="cost"></a>Koszt  

Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć poniższego przykładu: 
 
W przypadku klasy 25 studentów z upływem 20 godzin planowanego czasu zajęć i 10 godzin przydziału dla prac domowych lub przydziałów, Cena za laboratorium to: 

25 studentów * (20 + 10) godz. * 84 jednostek laboratoryjnych * 0,01 USD za godzinę = 630 USD. 

**Ważne:** Oszacowanie kosztów odbywa się tylko na przykład.  Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono procedurę tworzenia laboratorium do konfiguracji sieci przy użyciu usługi GNS3.

## <a name="next-steps"></a>Następne kroki
Następne kroki są wspólne do konfigurowania dowolnego laboratorium:

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users).