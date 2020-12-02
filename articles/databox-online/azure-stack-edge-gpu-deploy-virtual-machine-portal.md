---
title: Jak wdrażać maszyny wirtualne na Azure Stack Edge w firmie Pro za pośrednictwem Azure Portal
description: Dowiedz się, jak tworzyć maszyny wirtualne i zarządzać nimi na Azure Stack Edge przy użyciu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 89ef196cb5a124b8b1100871c408400f3fceef5c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467165"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Wdrażaj maszyny wirtualne na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU Pro Azure Portal

Możesz tworzyć maszyny wirtualne i zarządzać nimi na Azure Stack urządzeniu brzegowym przy użyciu Azure Portal, szablonów, poleceń cmdlet Azure PowerShell i za pośrednictwem skryptów interfejsu wiersza polecenia platformy Azure/Python. W tym artykule opisano sposób tworzenia maszyny wirtualnej na urządzeniu Azure Stack brzegowej oraz zarządzania nią przy użyciu Azure Portal. 

Ten artykuł ma zastosowanie do Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge. 

## <a name="vm-deployment-workflow"></a>Przepływ pracy wdrożenia maszyny wirtualnej

Podsumowanie wysokiego poziomu przepływu pracy wdrożenia jest następujące:

1. Włącz interfejs sieciowy na potrzeby obliczeń na urządzeniu Azure Stack Edge. Spowoduje to utworzenie przełącznika wirtualnego w określonym interfejsie sieciowym.
1. Włącz zarządzanie chmurą maszyn wirtualnych na podstawie Azure Portal.
1. Przekaż dysk VHD do konta usługi Azure Storage przy użyciu Eksplorator usługi Storage. 
1. Użyj przekazanego wirtualnego dysku twardego, aby pobrać dysk VHD na urządzenie i utworzyć obraz maszyny wirtualnej na podstawie dysku VHD. 
1. Użyj zasobów utworzonych w poprzednich krokach:
    1. Utworzony obraz maszyny wirtualnej.
    1. VSwitch skojarzony z interfejsem sieciowym, w którym włączono obliczenia.
    1. Podsieć skojarzona z przełącznikiem VSwitch.

    I Utwórz lub określ następujące zasoby w tekście:
    1. Nazwa maszyny wirtualnej, wybierz obsługiwany rozmiar maszyny wirtualnej, poświadczenia logowania dla maszyny wirtualnej. 
    1. Utwórz nowe dyski danych lub Dołącz istniejące dyski danych.
    1. Skonfiguruj statyczny lub dynamiczny adres IP dla maszyny wirtualnej. W przypadku udostępniania statycznego adresu IP należy wybrać jeden z bezpłatnych adresów IP w zakresie podsieci interfejsu sieciowego włączonego do obliczenia.

    Użyj powyższych zasobów, aby utworzyć maszynę wirtualną.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tworzenia maszyn wirtualnych na urządzeniu i zarządzania nimi za pomocą Azure Portal upewnij się, że:

1. Ustawienia sieciowe na urządzeniu z systemem Azure Stack Edge w wersji Pro zostały ukończone zgodnie z opisem w [sekcji Krok 1. Konfigurowanie programu Azure Stack EDGE Pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. Włączono interfejs sieciowy dla obliczeń. Ten interfejs sieciowy IP służy do tworzenia przełącznika wirtualnego dla wdrożenia maszyny wirtualnej. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **obliczenia**. Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego.

        > [!IMPORTANT] 
        > Dla obliczeń można skonfigurować tylko jeden port.

    1. Włącz obliczenia w interfejsie sieciowym. Azure Stack EDGE Pro tworzy przełącznik wirtualny i zarządza nim, odpowiadający interfejsowi sieciowemu.

1. Masz dostęp do wirtualnego dysku twardego z systemem Windows lub Linux, który zostanie użyty do utworzenia obrazu maszyny wirtualnej dla maszyn wirtualnych, które mają zostać utworzone.

## <a name="deploy-a-vm"></a>Wdrażanie maszyny wirtualnej

Wykonaj następujące kroki, aby utworzyć maszynę wirtualną na urządzeniu Azure Stack Edge.

### <a name="add-a-vm-image"></a>Dodawanie obrazu maszyny wirtualnej

1. Przekaż dysk VHD do konta usługi Azure Storage. Postępuj zgodnie z instrukcjami w sekcji [przekazywanie wirtualnego dysku twardego przy użyciu Eksplorator usługi Azure Storage](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. W Azure Portal przejdź do zasobu Azure Stack Edge dla urządzenia Azure Stack Edge. Przejdź do obszaru **obliczeniowego Edge > Virtual Machines**.

    ![Dodaj obraz maszyny wirtualnej 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Wybierz pozycję **Virtual Machines** , aby przejść do strony **Przegląd** . **Włącz** zarządzanie chmurą maszyn wirtualnych.
    ![Dodaj obraz maszyny wirtualnej 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Pierwszym krokiem jest dodanie obrazu maszyny wirtualnej. Wirtualny dysk twardy został już przekazany do konta magazynu we wcześniejszym kroku. Ten wirtualny dysk twardy zostanie użyty do utworzenia obrazu maszyny wirtualnej.

    Wybierz pozycję **Dodaj obraz** , aby pobrać dysk VHD z konta magazynu i dodać go do urządzenia. Proces pobierania trwa kilka minut w zależności od rozmiaru wirtualnego dysku twardego i dostępnej przepustowości internetowej do pobrania. 

    ![Dodaj obraz maszyny wirtualnej 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. W bloku **Dodaj obraz** wprowadź następujące parametry. Wybierz pozycję **Dodaj**.


    |Parametr  |Opis  |
    |---------|---------|
    |Pobierz z magazynu obiektów BLOB    |Przejdź do lokalizacji obiektu blob magazynu na koncie magazynu, na którym został przekazany dysk VHD.         |
    |Pobierz do    | Automatycznie ustaw na bieżące urządzenie, na którym wdrażasz maszynę wirtualną.        |
    |Zapisz obraz jako      | Nazwa tworzonego obrazu maszyny wirtualnej na podstawie wirtualnego dysku twardego, który został przekazany do konta magazynu.        |
    |Typ systemu operacyjnego     |Wybierz z systemu Windows lub Linux jako system operacyjny wirtualnego dysku twardego, który zostanie użyty do utworzenia obrazu maszyny wirtualnej.         |
   

    ![Dodawanie obrazu maszyny wirtualnej 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Dysk VHD zostanie pobrany i zostanie utworzony obraz maszyny wirtualnej. Tworzenie obrazu trwa kilka minut. Zostanie wyświetlone powiadomienie o pomyślnym zakończeniu obrazu maszyny wirtualnej.

    ![Dodawanie obrazu maszyny wirtualnej 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Po pomyślnym utworzeniu obrazu maszyny wirtualnej zostanie on dodany do listy obrazów w bloku **obrazy** .
    ![Dodawanie obrazu maszyny wirtualnej 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Blok **wdrożenia** jest aktualizowany w celu wskazania stanu wdrożenia.

    ![Dodawanie obrazu maszyny wirtualnej 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Nowo dodany obraz jest również wyświetlany na stronie **Przegląd** .
    ![Dodawanie obrazu maszyny wirtualnej 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Dodawanie maszyny wirtualnej

Wykonaj następujące kroki, aby utworzyć maszynę wirtualną po utworzeniu obrazu maszyny wirtualnej.

1. Na stronie **Przegląd** wybierz pozycję **Dodaj maszynę wirtualną**.

    ![Dodaj maszynę wirtualną 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Na karcie **podstawy** wprowadź następujące parametry.


    |Parametr |Opis  |
    |---------|---------|
    |Nazwa maszyny wirtualnej     |         |
    |Obraz     | Wybierz spośród obrazów maszyn wirtualnych dostępnych na urządzeniu.        |
    |Rozmiar     | Wybieraj spośród [obsługiwanych rozmiarów maszyn wirtualnych](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#supported-vm-sizes).        |
    |Nazwa użytkownika     | Użyj domyślnej nazwy użytkownika *azureuser*.        |
    |Typ uwierzytelniania    | Wybierz opcję z klucza publicznego SSH lub hasła zdefiniowanego przez użytkownika.       |
    |Hasło     | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. Hasło musi mieć długość co najmniej 12 znaków i spełniać zdefiniowane [wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).        |
    |Potwierdź hasło    | Wprowadź hasło ponownie.        |


    ![Dodaj maszynę wirtualną 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Wybierz pozycję **Dalej: dyski**.

1. Na karcie **dyski** dołączysz dyski do maszyny wirtualnej. 
    
    1. Możesz **utworzyć i dołączyć nowy dysk** lub **dołączyć istniejący dysk**.

        ![Dodaj maszynę wirtualną 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Wybierz pozycję **Utwórz i Dołącz nowy dysk**. W bloku **Utwórz nowy dysk** Podaj nazwę dysku i rozmiar w GIB.

        ![Dodaj maszynę wirtualną 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Powtórz powyższe czynności, aby dodać więcej dysków. Po utworzeniu dysków zostaną one wyświetlone na karcie **dyski** .

        ![Dodaj maszynę wirtualną 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Wybierz pozycję **Dalej: Sieć**.

1. Na karcie **Sieć** skonfigurujesz łączność sieciową dla maszyny wirtualnej.

    
    |Parametr  |Opis |
    |---------|---------|
    |Sieć wirtualna    | Z listy rozwijanej wybierz przełącznik wirtualny utworzony na urządzeniu Azure Stack Edge po włączeniu obliczeń w interfejsie sieciowym.    |
    |Podsieć     | To pole jest wypełniane automatycznie z podsiecią skojarzoną z interfejsem sieciowym, w którym włączono obliczenia.         |
    |Adres IP     | Podaj statyczny lub dynamiczny adres IP dla maszyny wirtualnej. Statyczny adres IP powinien być dostępny, bezpłatny adres IP z określonego zakresu podsieci.        |

    ![Dodaj maszynę wirtualną 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Wybierz pozycję **Dalej: przegląd + Utwórz**.

1. Na karcie **Recenzja + tworzenie** Sprawdź specyfikacje dla maszyny wirtualnej i wybierz pozycję **Utwórz**.

    ![Dodaj maszynę wirtualną 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Tworzenie maszyny wirtualnej rozpocznie się i może trwać do 20 minut. Aby monitorować Tworzenie maszyn wirtualnych, możesz przejść do pozycji **wdrożenia** .

    ![Dodawanie maszyny wirtualnej 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Po pomyślnym utworzeniu maszyny wirtualnej strona **Przegląd** zostanie zaktualizowana, aby wyświetlić nową maszynę wirtualną.

    ![Dodaj maszynę wirtualną 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Wybierz nowo utworzoną MASZYNę wirtualną, aby przejść do **maszyn wirtualnych**.

    ![Dodaj maszynę wirtualną 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Wybierz maszynę wirtualną, aby wyświetlić szczegóły. 

    ![Dodaj maszynę wirtualną 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Łączenie z maszyną wirtualną

W zależności od tego, czy utworzono maszynę wirtualną z systemem Windows, czy z systemem Linux, kroki do połączenia mogą być różne. Nie można nawiązać połączenia z maszynami wirtualnymi wdrożonymi na urządzeniu za pośrednictwem Azure Portal. Aby nawiązać połączenie z maszyną wirtualną z systemem Linux lub Windows, należy wykonać następujące czynności.

### <a name="connect-to-linux-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux

Wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak administrować urządzeniem z systemem Azure Stack EDGE Pro, zobacz[Korzystanie z lokalnego interfejsu użytkownika sieci Web w celu administrowania usługą Azure Stack EDGE Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
