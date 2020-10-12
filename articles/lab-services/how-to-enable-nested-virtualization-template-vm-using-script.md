---
title: Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services (skrypt) | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną szablonu z wieloma maszynami wirtualnymi wewnątrz przy użyciu skryptu.  Innymi słowy należy włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251499"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services przy użyciu skryptu

Wirtualizacja zagnieżdżona umożliwia tworzenie środowiska z obsługą wiele maszyn wirtualnych w ramach maszyny wirtualnej szablonu laboratorium. Opublikowanie szablonu zapewni każdemu użytkownikowi w laboratorium z maszyną wirtualną skonfigurowaną z wieloma maszynami wirtualnymi.  Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej i Azure Lab Services, zobacz [Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Kroki opisane w tym artykule koncentrują się na konfigurowaniu zagnieżdżonej wirtualizacji dla systemu Windows Server 2016, Windows Server 2019 lub Windows 10. Za pomocą skryptu można skonfigurować maszynę szablonu przy użyciu funkcji Hyper-V.  Poniższe kroki przeprowadzą Cię przez proces korzystania ze [skryptów funkcji Hyper-V dla usług Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Wybierz opcję **duże (Wirtualizacja zagnieżdżona)** lub **Średni (Wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Wirtualizacja zagnieżdżona nie będzie działała inaczej.  

## <a name="run-script"></a>Uruchamianie skryptu

1. Jeśli używasz programu Internet Explorer, może być konieczne dodanie `https://github.com` do listy zaufanych witryn.
    1. Otwórz program Internet Explorer.
    1. Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Opcje internetowe**.  
    1. Po wyświetleniu okna dialogowego **Opcje internetowe** wybierz pozycję **zabezpieczenia**, wybierz pozycję **Zaufane witryny**, kliknij przycisk **witryny** .
    1. Gdy zostanie wyświetlone okno dialogowe **Zaufane witryny** , Dodaj `https://github.com` do listy zaufanych witryn sieci Web i wybierz pozycję **Zamknij**.

        ![Zaufane witryny](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Pobierz pliki repozytorium git, jak opisano w poniższych krokach.
    1. Przejdź do  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Kliknij przycisk **klonowania lub pobierania** .
    1. Kliknij pozycję **Pobierz plik zip**.
    1. Wyodrębnij plik ZIP

    >[!TIP]
    >Repozytorium git można również sklonować pod adresem [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Uruchom program **PowerShell** w trybie **administratora** .
1. W oknie programu PowerShell przejdź do folderu z pobranym skryptem. Jeśli przechodzenie jest z górnego folderu plików repozytorium, skrypt znajduje się w lokalizacji `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Może być konieczna zmiana zasad wykonywania w celu pomyślnego uruchomienia skryptu. Uruchom następujące polecenie:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Uruchom skrypt:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skrypt może wymagać ponownego uruchomienia komputera. Postępuj zgodnie z instrukcjami ze skryptu, a następnie uruchom skrypt do momentu wyświetlenia **skryptu** w danych wyjściowych.
1. Nie zapomnij zresetować zasad wykonywania. Uruchom następujące polecenie:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Podsumowanie

Teraz komputer z szablonem jest gotowy do tworzenia maszyn wirtualnych funkcji Hyper-V. Aby uzyskać instrukcje dotyczące tworzenia maszyn wirtualnych funkcji Hyper-v, zobacz temat [Tworzenie maszyny wirtualnej w funkcji Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Zobacz również artykuł [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) , aby wyewidencjonować dostępne systemy operacyjne i oprogramowanie.  

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)