---
title: Testowanie aplikacji na platformie Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć udział plików w laboratorium i zainstalować go na maszynie lokalnej i maszynie wirtualnej w laboratorium, a następnie wdrożyć aplikacje pulpitu/sieci Web w udziale plików i przetestować je.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2dbbf349da4e352fe20a22db03cc9063d801990
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87282249"
---
# <a name="test-your-app-in-azure"></a>Testowanie aplikacji na platformie Azure 
Ten artykuł zawiera kroki testowania aplikacji na platformie Azure przy użyciu DevTest Labs. Najpierw należy skonfigurować udział plików w środowisku laboratoryjnym i zainstalować go jako dysk na lokalnym komputerze deweloperskim i maszynie wirtualnej w laboratorium. Następnie możesz użyć programu Visual Studio 2019 do wdrożenia aplikacji w udziale plików, aby można było uruchomić aplikację na maszynie wirtualnej w laboratorium.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
1. [Utwórz subskrypcję platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz, i zaloguj się do [Azure Portal](https://portal.azure.com).
2. Postępuj zgodnie z instrukcjami w [tym artykule](devtest-lab-create-lab.md) , aby utworzyć laboratorium przy użyciu Azure DevTest Labs. Przypnij laboratorium do pulpitu nawigacyjnego, aby można je było łatwo znaleźć przy następnym logowaniu. Azure DevTest Labs umożliwia szybkie tworzenie zasobów na platformie Azure przez zminimalizowanie ilości odpadów i sterowanie nimi. Aby dowiedzieć się więcej na temat DevTest Labs, zobacz [Omówienie](devtest-lab-overview.md). 
3. Utwórz konto usługi Azure Storage w grupie zasobów laboratorium, wykonując instrukcje przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-account-create.md) . Na stronie **Tworzenie konta magazynu** wybierz pozycję **Użyj istniejącej** **grupy zasobów**, a następnie wybierz **grupę zasobów laboratorium**. 
4. Utwórz udział plików w usłudze Azure Storage, wykonując instrukcje przedstawione w artykule [Tworzenie udziału plików w Azure Files](../storage/files/storage-how-to-create-file-share.md) . 

## <a name="mount-the-file-share-on-your-local-machine"></a>Zainstaluj udział plików na komputerze lokalnym
1. Na komputerze lokalnym Użyj skryptu z sekcji [Instalowanie udziału plików platformy](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) Azure w artykule [Korzystanie z udziału plików platformy Azure z systemem Windows](../storage/files/storage-how-to-use-files-windows.md) . 
2. Następnie użyj `net use` polecenia, aby zainstalować udział plików na komputerze. Oto przykładowe polecenie: Podaj nazwę usługi Azure Storage i nazwę udziału plików przed uruchomieniem polecenia. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Tworzenie maszyny wirtualnej w laboratorium
1. Na stronie **udział plików** wybierz **grupę zasobów** w menu stron nadrzędnych u góry. Zostanie wyświetlona strona **Grupa zasobów** . 
    
    ![Wybierz grupę zasobów z menu stron nadrzędnych](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na stronie **Grupa zasobów** wybierz **laboratorium** utworzone w DevTest Labs.

    ![Wybieranie laboratorium](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na stronie **laboratorium DevTest** dla laboratorium wybierz pozycję **+ Dodaj** na pasku narzędzi. 

    ![Dodaj przycisk dla laboratorium](media/test-app-in-azure/add-button-in-lab.png)
4. Na stronie **Wybierz stronę podstawową** Wyszukaj pozycję **smalldisk**, a następnie wybierz pozycję **[Smalldisk] Windows Server 2016 Data Center**. 

    ![Wybieranie małego dysku systemu Windows Server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na stronie **maszyna wirtualna** Określ wartość w polu **Nazwa maszyny wirtualnej**, **Nazwa użytkownika**, **hasło**, a następnie wybierz pozycję **Utwórz**.    
    
    ![Utwórz stronę maszyny wirtualnej](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Instalowanie udziału plików na maszynie wirtualnej
1. Po pomyślnym utworzeniu maszyny wirtualnej wybierz **maszynę wirtualną** z listy.    

    ![Wybierz maszynę wirtualną laboratorium](media/test-app-in-azure/select-lab-vm.png)
2. Wybierz pozycję **Połącz** na pasku narzędzi, aby nawiązać połączenie z maszyną wirtualną. 
3. [Zainstalowanie programu Azure PowerShell](/powershell/azure/install-az-ps).
4. Postępuj zgodnie z instrukcjami w sekcji Instalowanie udziału plików. 

## <a name="publish-your-app-from-visual-studio"></a>Publikowanie aplikacji w programie Visual Studio
W tej sekcji opublikowano aplikację z programu Visual Studio do testowej maszyny wirtualnej w chmurze.

1. Utwórz aplikację pulpitu/sieci Web przy użyciu programu Visual Studio 2019.
2. Skompiluj aplikację.
3. Aby opublikować aplikację, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**. 
4. W **Kreatorze publikacji** wprowadź **dysk** mapowany na udział plików.

    **Aplikacja klasyczna:**

    ![Aplikacja klasyczna](media/test-app-in-azure/desktop-app.png)

    **Aplikacja sieci Web:**

    ![Aplikacja internetowa](media/test-app-in-azure/web-app.png)

1. Wybierz pozycję **dalej** , aby zakończyć przepływ pracy publikowania, a następnie wybierz pozycję **Zakończ**. Po zakończeniu kroków kreatora program Visual Studio kompiluje aplikację i opublikuje ją w udziale plików. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testowanie aplikacji na testowej maszynie wirtualnej w laboratorium

1. Przejdź do strony maszyny wirtualnej dla maszyny wirtualnej w laboratorium. 
2. Wybierz pozycję **Rozpocznij** na pasku narzędzi, aby uruchomić maszynę wirtualną, jeśli jest w stanie zatrzymania. Można skonfigurować zasady automatycznego uruchamiania i automatycznego zamykania dla maszyny wirtualnej, aby uniknąć uruchamiania i zatrzymywania za każdym razem. 
3. Wybierz pozycję **Połącz**.

    ![Strona maszyny wirtualnej](media/test-app-in-azure/virtual-machine-page.png)
4. W ramach maszyny wirtualnej Uruchom **Eksploratora plików**, a następnie wybierz **ten komputer** , aby znaleźć udział plików.

    ![Znajdź udział w maszynie wirtualnej](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Z jakiegoś powodu, jeśli nie możesz znaleźć udziału plików na maszynie wirtualnej lub na komputerze lokalnym, możesz go zainstalować ponownie, uruchamiając `net use` polecenie. Polecenie można znaleźć `net use` w kreatorze **połączenia** **udziału plików** w Azure Portal.
1. Otwórz udział plików i upewnij się, że zobaczysz aplikację wdrożoną w programie Visual Studio. 

    ![Otwórz udział na maszynie wirtualnej](media/test-app-in-azure/open-file-share.png)

    Teraz możesz uzyskiwać dostęp do aplikacji i testować ją w testowej maszynie wirtualnej utworzonej na platformie Azure.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, aby dowiedzieć się, jak korzystać z maszyn wirtualnych w laboratorium. 

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
- [Uruchom ponownie maszynę wirtualną laboratorium](devtest-lab-restart-vm.md)
- [Zmiana rozmiaru maszyny wirtualnej laboratorium](devtest-lab-resize-vm.md)
