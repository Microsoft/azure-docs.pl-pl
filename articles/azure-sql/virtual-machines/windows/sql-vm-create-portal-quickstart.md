---
title: Utwórz SQL Server na maszynie wirtualnej z systemem Windows w Azure Portal | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć maszynę wirtualną z systemem Windows przy użyciu SQL Server 2017 w Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 335a63faa440e057c282f992b67b301289a7a4bb
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356977"
---
# <a name="quickstart-create-sql-server-2017-on-a-windows-virtual-machine-in-the-azure-portal"></a>Szybki Start: tworzenie SQL Server 2017 na maszynie wirtualnej z systemem Windows w Azure Portal

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Windows](sql-vm-create-portal-quickstart.md)
> * [Linux](../linux/sql-vm-create-portal-quickstart.md)

W tym przewodniku szybki start opisano tworzenie SQL Server maszyny wirtualnej (VM) w Azure Portal.


  > [!TIP]
  > - W tym przewodniku Szybki start przedstawiono ścieżkę szybkiego aprowizowania maszyny wirtualnej SQL i nawiązywania z nią połączenia. Aby uzyskać więcej informacji na temat innych opcji inicjowania obsługi maszyn wirtualnych SQL, zobacz [Przewodnik aprowizacji dla SQL Server na maszynie wirtualnej z systemem Windows w Azure Portal](create-sql-vm-portal.md).
  > - Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Uzyskaj subskrypcję platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Wybieranie obrazu maszyny wirtualnej z programem SQL Server

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu swojego konta.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz *SQL Azure* w polu wyszukiwania.
1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Dodatkowe informacje można wyświetlić, wybierając pozycję **Pokaż szczegóły** na kafelku **maszyny wirtualne SQL** .
1. Wybierz **bezpłatną licencję SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016** obraz z listy rozwijanej.

   ![Zrzut ekranu pokazujący, gdzie wybierasz bezpłatną licencję SQL Server: SQL Server 2017 Developer w obrazie systemu Windows Server 2016.](./media/sql-vm-create-portal-quickstart/select-sql-2017-vm-image.png)

1. Wybierz pozycję **Utwórz**.

   ![Nowe okno wyszukiwania](./media/sql-vm-create-portal-quickstart/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Podawanie podstawowych informacji

Na karcie **podstawowe** podaj następujące informacje:

1. W sekcji **szczegóły projektu** wybierz subskrypcję platformy Azure, a następnie wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. Wpisz _SQLVM-RG_ jako nazwę.

   ![Subskrypcja](./media/sql-vm-create-portal-quickstart/basics-project-details.png)

1. W obszarze **szczegóły wystąpienia**:
    1. Wpisz _SQLVM_ dla **nazwy maszyny wirtualnej**. 
    1. Wybierz lokalizację dla **regionu**. 
    1. Na potrzeby tego przewodnika Szybki Start pozostaw opcję **dostępność** ustawioną na _niewymaganą nadmiarowość infrastruktury_. Aby uzyskać więcej informacji na temat opcji dostępności, zobacz [dostępność](../../../virtual-machines/availability.md). 
    1. Na liście **obraz** wybierz opcję _bezpłatna SQL Server licencja: SQL Server 2017 Developer w systemie Windows Server 2016_. 
    1. Wybierz opcję **zmiany rozmiaru** maszyny wirtualnej  i wybierz **podstawową ofertę a2** . Pamiętaj, aby wyczyścić zasoby po wykonaniu tych czynności, aby zapobiec nieoczekiwanym opłatom. 

   ![Szczegóły wystąpienia](./media/sql-vm-create-portal-quickstart/basics-instance-details.png)

1. W obszarze **konto administratora** Podaj nazwę użytkownika, taką jak _azureuser_ i hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](./media/sql-vm-create-portal-quickstart/basics-administrator-account.png)

1. W obszarze **reguły portów ruchu przychodzącego** wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej. 

   ![Reguły portów wejściowych](./media/sql-vm-create-portal-quickstart/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Ustawienia programu SQL Server

Na karcie **ustawienia SQL Server** skonfiguruj następujące opcje:

1. W obszarze **zabezpieczenia & sieci** wybierz opcję _publiczny (Internet_) dla **łączności SQL** i zmień port na, aby `1401` uniknąć używania dobrze znanego numeru portu w scenariuszu publicznym. 
1. W obszarze **uwierzytelnianie SQL** wybierz pozycję **Włącz**. Poświadczenia logowania SQL są ustawiane na tę samą nazwę użytkownika i hasło, które zostały skonfigurowane dla maszyny wirtualnej. Użyj domyślnego ustawienia [**integracji Azure Key Vault**](azure-key-vault-integration-configure.md). **Konfiguracja magazynu** jest niedostępna dla obrazu maszyny wirtualnej w warstwie Podstawowa SQL Server, ale można znaleźć więcej informacji na temat dostępnych opcji innych obrazów w [konfiguracji magazynu](storage-configuration.md#new-vms).  

   ![Ustawienia zabezpieczeń programu SQL Server](./media/sql-vm-create-portal-quickstart/sql-server-settings.png)


1. W razie konieczności Zmień inne ustawienia, a następnie wybierz pozycję **Przegląd + Utwórz**. 

   ![Przeglądanie i tworzenie](./media/sql-vm-create-portal-quickstart/review-create.png)


## <a name="create-the-sql-server-vm"></a>Tworzenie maszyny wirtualnej z programem SQL Server

Na karcie **Przegląd + tworzenie** Przejrzyj podsumowanie, a następnie wybierz pozycję  **Utwórz** , aby utworzyć SQL Server, grupę zasobów i zasoby określone dla tej maszyny wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia. Wdrożenie może potrwać kilka minut. 

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

1. W portalu Znajdź **publiczny adres IP** maszyny wirtualnej SQL Server w sekcji **Przegląd** właściwości maszyny wirtualnej.

1. Na innym komputerze podłączonym do Internetu Otwórz [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. W oknie dialogowym **Connect to Server** (Łączenie z serwerem) lub **Connect to Database Engine** (Łączenie z aparatem bazy danych) edytuj wartość **Server name** (Nazwa serwera). Wprowadź publiczny adres IP maszyny wirtualnej. Następnie Dodaj przecinek i Dodaj port niestandardowy (**1401**), który został określony podczas konfigurowania nowej maszyny wirtualnej. Na przykład `11.22.33.444,1401`.

1. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).

1. W polu **Login** (Logowanie) wpisz nazwę prawidłowego identyfikatora logowania SQL.

1. W polu **Password** (Hasło) wpisz hasło logowania.

1. Wybierz pozycję **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-vm-create-portal-quickstart/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Zdalne logowanie się do maszyny wirtualnej

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli maszyna wirtualna SQL ma działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, usuwając skojarzone z nią grupy zasobów z poziomu portalu. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pośrednictwem portalu](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono maszynę wirtualną w Azure Portal SQL Server 2017. Aby dowiedzieć się więcej o sposobie przeprowadzania migracji danych do nowego serwera SQL Server, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Migrowanie bazy danych do maszyny wirtualnej SQL](migrate-to-vm-from-sql-server.md)