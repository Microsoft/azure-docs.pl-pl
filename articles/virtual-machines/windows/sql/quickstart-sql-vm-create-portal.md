---
title: Tworzenie maszyny wirtualnej systemu Windows z programem SQL Server w portalu | Microsoft Docs
description: W tym samouczku pokazano sposób tworzenia maszyny wirtualnej z programem SQL Server 2017 i systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 801a6fc0602882d1af49c06bafcfd51942e6da2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75965653"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej systemu Windows z programem SQL Server 2017 w witrynie Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ten przewodnik Szybki start przeprowadza użytkownika przez proces tworzenia maszyny wirtualnej z programem SQL Server w witrynie Azure Portal.


  > [!TIP]
  > - W tym przewodniku Szybki start przedstawiono ścieżkę szybkiego aprowizowania maszyny wirtualnej SQL i nawiązywania z nią połączenia. Aby uzyskać więcej informacji na temat innych opcji aprowizowania maszyn wirtualnych SQL, zobacz [Provisioning guide for Windows SQL Server VMs in the Azure portal (Przewodnik aprowizowania maszyn wirtualnych systemu Windows z programem SQL Server w witrynie Azure Portal)](virtual-machines-windows-portal-sql-server-provision.md).
  > - Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Uzyskaj subskrypcję platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Wybieranie obrazu maszyny wirtualnej z programem SQL Server

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu swojego konta.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz *SQL Azure* w polu wyszukiwania.
1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Dodatkowe informacje można wyświetlić, wybierając pozycję **Pokaż szczegóły** na kafelku **maszyny wirtualne SQL** .
1. Wybierz **bezpłatną licencję SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016** obraz z listy rozwijanej.

   ![Nowe okno wyszukiwania](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Wybierz przycisk **Utwórz**.

   ![Nowe okno wyszukiwania](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Podawanie podstawowych informacji

Na karcie **podstawowe** podaj następujące informacje:

1. W sekcji **szczegóły projektu** wybierz subskrypcję platformy Azure, a następnie wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. Wpisz _SQLVM-RG_ jako nazwę.

   ![Subskrypcja](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. W obszarze **szczegóły wystąpienia**:
    1. Wpisz _SQLVM_ dla **nazwy maszyny wirtualnej**. 
    1. Wybierz lokalizację dla **regionu**. 
    1. Na potrzeby tego przewodnika Szybki Start pozostaw opcję **dostępność** ustawioną na _niewymaganą nadmiarowość infrastruktury_. Aby uzyskać więcej informacji na temat opcji dostępności, zobacz [dostępność](../../windows/availability.md). 
    1. Na liście **obraz** wybierz opcję _bezpłatna SQL Server licencja: SQL Server 2017 Developer w systemie Windows Server 2016_. 
    1. Wybierz opcję **zmiany rozmiaru** maszyny wirtualnej **Size** i wybierz **podstawową ofertę a2** . Pamiętaj, aby wyczyścić zasoby po wykonaniu tych czynności, aby zapobiec nieoczekiwanym opłatom. 

   ![Szczegóły wystąpienia](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. W obszarze **konto administratora**Podaj nazwę użytkownika, taką jak _azureuser_ i hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. W obszarze **reguły portów ruchu przychodzącego**wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej. 

   ![Reguły portów wejściowych](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Ustawienia programu SQL Server

Na karcie **ustawienia SQL Server** skonfiguruj następujące opcje:

1. W obszarze **zabezpieczenia & sieci**wybierz opcję _publiczny (Internet_) dla **łączności SQL** i zmień port na `1401` , aby uniknąć używania dobrze znanego numeru portu w scenariuszu publicznym. 
1. W obszarze **uwierzytelnianie SQL**wybierz pozycję **Włącz**. Identyfikator logowania SQL jest ustawiony na tą samą nazwę użytkownika i hasło, które zostały skonfigurowane na potrzeby maszyny wirtualnej. Użyj domyślnego ustawienia [**integracji Azure Key Vault**](virtual-machines-windows-ps-sql-keyvault.md). **Konfiguracja magazynu** jest niedostępna dla obrazu maszyny wirtualnej w warstwie Podstawowa SQL Server ale można znaleźć więcej informacji na temat dostępnych opcji innych obrazów w [konfiguracji magazynu](virtual-machines-windows-sql-server-storage-configuration.md#new-vms).  

   ![Ustawienia zabezpieczeń programu SQL Server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. W razie konieczności Zmień inne ustawienia, a następnie wybierz pozycję **Przegląd + Utwórz**. 

   ![Przegląd + tworzenie](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Tworzenie maszyny wirtualnej z programem SQL Server

Na karcie **Przegląd + tworzenie** Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz** , aby utworzyć SQL Server, grupę zasobów i zasoby określone dla tej maszyny wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia. Wdrożenie może potrwać kilka minut. 

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

1. W portalu Znajdź **publiczny adres IP** maszyny wirtualnej SQL Server w sekcji **Przegląd** właściwości maszyny wirtualnej.

1. Na innym komputerze podłączonym do Internetu Otwórz [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. W oknie dialogowym **Connect to Server** (Łączenie z serwerem) lub **Connect to Database Engine** (Łączenie z aparatem bazy danych) edytuj wartość **Server name** (Nazwa serwera). Wprowadź publiczny adres IP maszyny wirtualnej. Następnie dodaj przecinek i port niestandardowy, **1401**, określony podczas konfigurowania nowej maszyny wirtualnej. Na przykład `11.22.33.444,1401`.

1. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).

1. W polu **Login** (Logowanie) wpisz nazwę prawidłowego identyfikatora logowania SQL.

1. W polu **Password** (Hasło) wpisz hasło logowania.

1. Wybierz przycisk **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Zdalne logowanie się do maszyny wirtualnej

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli maszyna wirtualna SQL ma działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, usuwając skojarzone z nią grupy zasobów z poziomu portalu. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pośrednictwem portalu](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono maszynę wirtualną w Azure Portal SQL Server 2017. Aby dowiedzieć się więcej o sposobie przeprowadzania migracji danych do nowego serwera SQL Server, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Migrowanie bazy danych do maszyny wirtualnej SQL](virtual-machines-windows-migrate-sql.md)
