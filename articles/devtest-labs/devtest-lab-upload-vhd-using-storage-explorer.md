---
title: Przekaż plik VHD do Azure DevTest Labs przy użyciu Eksplorator usługi Storage
description: Przekaż plik VHD do konta magazynu laboratorium przy użyciu Eksplorator usługi Microsoft Azure Storage
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4e421932ebba5d4f389000c12bcf44ac2f37599
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91282531"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Przekaż plik VHD do konta magazynu laboratorium przy użyciu Eksplorator usługi Microsoft Azure Storage

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W Azure DevTest Labs pliki VHD mogą służyć do tworzenia obrazów niestandardowych, które są używane do obsługi maszyn wirtualnych. W tym artykule pokazano, jak za pomocą [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) przekazywać plik VHD do konta magazynu laboratorium. Po przekazaniu pliku VHD [sekcja następne kroki](#next-steps) zawiera listę artykułów, które ilustrują, jak utworzyć obraz niestandardowy na podstawie przekazanego pliku VHD. Aby uzyskać więcej informacji na temat dysków i dysków VHD na platformie Azure, zobacz [wprowadzenie do usługi Managed disks](../virtual-machines/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przeprowadzą Cię przez proces przekazywania pliku VHD do DevTest Labs przy użyciu [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Pobierz i zainstaluj najnowszą wersję Eksplorator usługi Microsoft Azure Storage](https://www.storageexplorer.com).

1. Pobierz nazwę konta magazynu laboratorium przy użyciu Azure Portal:

    1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
    
    1. Z listy laboratoriów wybierz odpowiednie laboratorium.  
    
    1. W bloku laboratorium wybierz pozycję **Konfiguracja**. 
    
    1. W bloku **Konfiguracja** laboratorium wybierz pozycję **obrazy niestandardowe (VHD)**.
    
    1. W bloku **obrazy niestandardowe** wybierz pozycję **+ Dodaj**. 
    
    1. W bloku **obrazu niestandardowego** wybierz pozycję **wirtualny dysk twardy**.
    
    1. W bloku **wirtualnego dysku twardego** wybierz pozycję **Przekaż dysk VHD przy użyciu programu PowerShell**.
    
        ![Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell][0]
    
    1. Blok **przekazywanie obrazu przy użyciu programu PowerShell** wyświetla wywołanie polecenia cmdlet **Add-AzureVhd** . Pierwszy parametr (*miejsce docelowe*) zawiera nazwę konta magazynu dla laboratorium w następującym formacie:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Zanotuj nazwę konta magazynu, która jest używana w dalszych krokach.
    
1. Połącz się z kontem subskrypcji platformy Azure przy użyciu Eksplorator usługi Storage.

    > [!TIP] 
    > 
    > Eksplorator usługi Storage obsługuje kilka opcji połączeń. W tej sekcji przedstawiono łączenie się z kontem magazynu skojarzonym z subskrypcją platformy Azure. Aby wyświetlić inne opcje połączenia obsługiwane przez Eksplorator usługi Storage, zapoznaj się z artykułem [wprowadzenie](../vs-azure-tools-storage-manage-with-storage-explorer.md)do usługi Eksplorator usługi Storage.
 
    1. Otwórz Eksploratora usługi Storage.
    
    1. W Eksplorator usługi Storage wybierz pozycję **Ustawienia konta platformy Azure**. 
    
        ![Ustawienia konta platformy Azure][1]
    
    1. W okienku po lewej stronie zostaną wyświetlone konta Microsoft, do których się zalogowano. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto** i postępuj zgodnie z instrukcjami wyświetlanymi w oknach dialogowych, aby zalogować się przy użyciu konta Microsoft skojarzonego z co najmniej jedną aktywną subskrypcją platformy Azure.
    
        ![Dodaj konto][2]
    
    1. Po pomyślnym zalogowaniu się przy użyciu konta Microsoft w okienku po lewej stronie zostaną wyświetlone wszystkie subskrypcje platformy Azure skojarzone z tym kontem. Wybierz subskrypcję platformy Azure, przy użyciu której chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Wybranie opcji **wszystkie subskrypcje** powoduje przełączenie zaznaczenia wszystkich lub żadnej z wymienionych subskrypcji platformy Azure).
    
        ![Wybieranie subskrypcji platformy Azure][3]
    
    1. W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.
    
        ![Wybrane subskrypcje platformy Azure][4]

1. Znajdź konto magazynu laboratorium:

    1. W okienku Eksplorator usługi Storage lewe zlokalizuj i rozwiń węzeł subskrypcji platformy Azure, która jest właścicielem laboratorium.
    
    1. W węźle subskrypcja rozwiń węzeł **konta magazynu**.

    1. Rozwiń węzeł konta magazynu laboratorium, aby wyświetlić węzły dla **kontenerów obiektów BLOB**, **udziałów plików**, **kolejek** i **tabel**.
    
    1. Rozwiń węzeł **kontenery obiektów BLOB** .
    
    1. Wybierz kontener obiektów BLOB przekazywania, aby wyświetlić jego zawartość w okienku po prawej stronie.
        
        ![Przekaż katalog][5]

1. Przekaż plik VHD przy użyciu Eksplorator usługi Storage:

    1. W okienku Eksplorator usługi Storage po prawej stronie powinna zostać wyświetlona lista obiektów BLOB w kontenerze obiektów BLOB **przekazywania** dla konta magazynu laboratorium. Na pasku narzędzi Edytora obiektów BLOB wybierz pozycję **Przekaż** . 
        
        ![Zrzut ekranu przedstawiający okienko Eksplorator usługi Storage po prawej stronie z wybranym przyciskiem "Przekaż".][6]
    
    1. Z menu rozwijanego **Przekaż** wybierz pozycję **Przekaż pliki.**...
    
    1. W oknie dialogowym **przekazywanie plików** wybierz wielokropek.
        
        ![Zrzut ekranu pokazujący okno dialogowe "przekazywanie plików" z wybranym wielokropkiem.][8]  

    1. W oknie dialogowym **Wybieranie plików do przekazania** przejdź do żądanego pliku VHD, zaznacz go, a następnie wybierz pozycję **Otwórz**.
    
    1. Po zwróceniu do okna dialogowego **przekazywanie plików** Zmień **Typ obiektu BLOB** na **stronicowy obiekt BLOB**.
    
    1. Wybierz pozycję **Przekaż**.

        ![Wybierz plik][9]  
    
    1. W okienku **Dziennik aktywności** Eksplorator usługi Storage zostanie wyświetlony stan pobierania (wraz z linkami w celu anulowania przekazywania). Proces przekazywania pliku VHD może być długi w zależności od rozmiaru pliku VHD i szybkości połączenia. 

        ![Stan przekazywania pliku][10]  

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu Azure Portal](devtest-lab-create-template.md)
- [Tworzenie niestandardowego obrazu w Azure DevTest Labs z pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
