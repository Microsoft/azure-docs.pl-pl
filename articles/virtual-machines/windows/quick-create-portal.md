---
title: Szybki Start — Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Windows w witrynie Azure Portal
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/15/2021
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0ba28d003f359af12de6242c6d2444fb8adab0d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562759"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Ta metoda bazuje na opartym na przeglądarce interfejsie użytkownika umożliwiającym tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal wdrożyć maszynę wirtualną (VM) na platformie Azure z systemem Windows Server 2019. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu RDP i zainstalujesz serwer internetowy usług IIS.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wpisz **maszyny wirtualne** w wyszukiwaniu.
1. W obszarze **usługi** wybierz pozycję **maszyny wirtualne**.
1. Na stronie **maszyny wirtualne** wybierz pozycję **Dodaj** **maszynę wirtualną**. 
1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz nazwę *myResourceGroup*. 

    ![Zrzut ekranu przedstawiający sekcję Szczegóły projektu, w której można wybrać subskrypcję platformy Azure i grupę zasobów dla maszyny wirtualnej](./media/quick-create-portal/project-details.png)

1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Region**. Wybierz pozycję *Windows Server 2019 Datacenter* dla **obrazu** i *Standard_DS1_v2* **rozmiar**. Inne wartości pozostaw domyślne.

    ![Zrzut ekranu przedstawiający sekcję Szczegóły wystąpienia, w której podano nazwę maszyny wirtualnej i wybierz jej region, obraz i rozmiar](./media/quick-create-portal/instance-details.png)

1. W obszarze **Konto administratora** podaj nazwę użytkownika, taką jak *azureuser*, oraz hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Zrzut ekranu przedstawiający sekcję konta administratora, w której podano nazwę użytkownika i hasło administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze **reguły portów ruchu przychodzącego** wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** i **http (80)** z listy rozwijanej.

    ![Zrzut ekranu przedstawiający sekcję reguły portów ruchu przychodzącego, w której wybierane są dozwolone porty przychodzące](./media/quick-create-portal/inbound-port-rules.png)

1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.

    ![Zrzut ekranu przedstawiający przycisk przegląd i Utwórz w dolnej części strony](./media/quick-create-portal/review-create.png)

1. Po uruchomieniu walidacji wybierz przycisk **Utwórz** u dołu strony.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

    ![Zrzut ekranu przedstawiający następny krok przechodzenia do zasobu](./media/quick-create-portal/next-steps.png)

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną. Te kroki pozwolą Ci połączyć się z maszyną wirtualną z komputera z systemem Windows. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) ze sklepu Mac App Store.

1. Na stronie Przegląd dla swojej maszyny wirtualnej wybierz przycisk **Połącz** , a następnie wybierz pozycję **RDP**. 

    ![Zrzut ekranu przedstawiający stronę przegląd maszyny wirtualnej z informacją o lokalizacji przycisku Połącz](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na stronie **Połącz z** protokołem RDP zachowaj domyślne opcje łączenia przy użyciu adresu IP i portu 3389, a następnie kliknij pozycję **Pobierz plik RDP**.

2. Otwórz pobrany plik RDP i kliknij **Połącz**, gdy wyświetli się odpowiedni monit. 

3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika jako  \\ *nazwę użytkownika* localhost, wprowadź hasło utworzone dla maszyny wirtualnej, a następnie kliknij przycisk **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij pozycję **Tak** lub **Kontynuuj**, aby utworzyć połączenie.

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy usług IIS. Na maszynie wirtualnej otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu zamknij połączenie RDP z maszyną wirtualną.


## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

W portalu wybierz maszynę wirtualną, a następnie w obszarze przegląd maszyny wirtualnej Umieść wskaźnik myszy na adresie IP, aby wyświetlić **kopię do schowka**. Skopiuj adres IP i wklej go na karcie przeglądarki. Zostanie otwarta domyślna strona powitalna usług IIS, która powinna wyglądać następująco:

![Zrzut ekranu witryny domyślnej usług IIS w przeglądarce](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. 

Przejdź do grupy zasobów maszyny wirtualnej, a następnie wybierz pozycję **Usuń grupę zasobów**. Potwierdź nazwę grupy zasobów, aby zakończyć usuwanie zasobów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono prostą maszynę wirtualną, otwarto port sieciowy dla ruchu w sieci Web i zainstalowano podstawowy serwer sieci Web. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](./tutorial-manage-vm.md)
