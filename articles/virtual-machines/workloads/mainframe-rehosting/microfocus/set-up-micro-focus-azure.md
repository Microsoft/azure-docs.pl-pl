---
title: Instalowanie programu Micro Focus Enterprise Server 5,0 i Enterprise Developer 5,0 na platformie Azure | Microsoft Docs
description: W tym artykule dowiesz się, jak zainstalować program Micro Focus Enterprise Server 5,0 i Enterprise Developer 5,0 na Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 11fde4480f4e2ed9feda00fa2af0e5937a77b9ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986884"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Instalowanie programu Micro Focus Enterprise Server 5,0 i Enterprise Developer 5,0 na platformie Azure

W tym artykule przedstawiono sposób konfigurowania programu [Micro Focus Enterprise Server 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) i [deweloperów Enterprise Developer 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) na Microsoft Azure.

Typowym obciążeniem na platformie Azure jest środowisko programistyczne i testowe. Ten scenariusz jest powszechny, ponieważ jest to ekonomiczne i łatwe do wdrożenia i odrywania. Dzięki serwerowi korporacyjnemu firma Micro Focus stworzyła jedną z największych dostępnych platform do udostępniania komputerów mainframe. Obciążenia z/OS można uruchamiać na tańszej platformie x86 na platformie Azure przy użyciu maszyn wirtualnych z systemem Windows lub Linux.

Ta konfiguracja korzysta z maszyn wirtualnych platformy Azure z systemem Windows Server 2016 z witryny Azure Marketplace z zainstalowanym programem Microsoft SQL Server 2017. Ta konfiguracja dotyczy również Azure Stack.

Odpowiednie środowisko programistyczne dla programu Enterprise Server to deweloper przedsiębiorstwa, który działa w Microsoft Visual Studio 2017 lub nowszej, Visual Studio Community (bezpłatnie do pobrania) lub przezaćmienie. W tym artykule pokazano, jak wdrożyć go za pomocą maszyny wirtualnej z systemem Windows Server 2016, która jest dostarczana z programem Visual Studio 2017 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem sprawdź następujące wymagania wstępne:

-   Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

-   Oprogramowanie mikro Focus i ważną licencję (lub licencję próbną). Jeśli jesteś istniejącym klientem programu Micro Focus, skontaktuj się z przedstawicielem firmy Micro Focus. W przeciwnym razie [Poproś o wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

-   Zapoznaj się z dokumentacją dla [przedsiębiorstw Server i Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/).

    > [!Note]
    > Istnieje kilka opcji kontrolowania dostępu do maszyn wirtualnych:
    > -   Najlepszym rozwiązaniem jest skonfigurowanie [usługi Azure bastionu](https://azure.microsoft.com/services/azure-bastion/).
    > -   Tunel [wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja](../../../../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md) .
    > -   Maszyna wirtualna w serwera przesiadkowego.

## <a name="install-enterprise-server"></a>Instalowanie serwera Enterprise Server

1.  Aby zapewnić lepsze zabezpieczenia i możliwości zarządzania, należy rozważyć utworzenie nowej grupy zasobów tylko dla tego projektu — na przykład **RGMicroFocusEntServer**. Użyj pierwszej części nazwy na platformie Azure, aby wybrać typ zasobu, który ma być łatwiejszy na liście.

2.  Tworzy maszynę wirtualną. W portalu Azure Marketplace wybierz maszynę wirtualną i żądany system operacyjny. Oto zalecana konfiguracja:

    -   **Serwer przedsiębiorstwa:** Wybierz **maszynę wirtualną ES2 v3** (z 2 procesorów wirtualnych vCPU i 16 GB pamięci) z zainstalowanym systemem Windows Server 2016 i SQL Server 2017. Ten obraz jest dostępny w portalu Azure Marketplace. Serwer przedsiębiorstwa może również używać Azure SQL Database.

    -   **Deweloper przedsiębiorstwa:** Wybierz **maszynę wirtualną B2ms** (z 2 procesorów wirtualnych vCPU i 8 GB pamięci) z zainstalowanym systemem Windows 10 i Visual Studio. Ten obraz jest dostępny w portalu Azure Marketplace.

3.  W sekcji **podstawowe** wprowadź nazwę użytkownika i hasło. Wybierz **subskrypcję** i **lokalizację/region** , których chcesz użyć dla maszyn wirtualnych. Wybierz pozycję **RGMicroFocusEntServer** dla grupy zasobów.

4.  Umieść obie maszyny wirtualne w tej samej sieci wirtualnej, aby mogły komunikować się ze sobą.

5.  Zaakceptuj wartości domyślne dla pozostałych ustawień. Zapamiętaj nazwę użytkownika i hasło, które tworzysz dla administratora tych maszyn wirtualnych.

6.  Po utworzeniu maszyn wirtualnych Otwórz porty przychodzące **9003, 86** i **80** dla protokołów HTTP i **3389** dla Remote Desktop Protocol (RDP) na komputerze z serwerem przedsiębiorstwa i **3389** na komputerze dewelopera.

7.  Aby zalogować się do maszyny wirtualnej z serwerem przedsiębiorstwa, w Azure Portal wybierz maszynę wirtualną z ES2 v3. Przejdź do sekcji **Przegląd** i wybierz pozycję **Połącz** , aby uruchomić sesję RDP. Zaloguj się przy użyciu poświadczeń utworzonych dla maszyny wirtualnej.

8.  W sesji RDP Załaduj następujące dwa pliki. Ponieważ używasz systemu Windows, możesz przeciągać i upuszczać pliki do sesji RDP:

    -   `es\_50.exe`, plik instalacyjny serwera przedsiębiorstwa.

    -   `mflic`, odpowiedni plik licencji — serwer przedsiębiorstwa nie zostanie załadowany bez tego.

9.  Kliknij dwukrotnie plik, aby rozpocząć instalację. W pierwszym oknie Wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego.

    ![Ekran instalacji serwera korporacyjnego Micro Focus](media/install-image-1.png)

    Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

    ![Ekran instalacji serwera korporacyjnego Micro Focus](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Sprawdź aktualizacje

Po zakończeniu instalacji upewnij się, że zainstalowano dodatkowe aktualizacje zgodnie z wymaganiami wstępnymi, takimi jak pakiet redystrybucyjny i .NET Framework języka Microsoft C++ wraz z serwerem przedsiębiorstwa.

### <a name="upload-the-license"></a>Przekaż licencję

1.  Rozpocznij Administrowanie licencjami programu Micro Focus.

2.  Wybierz pozycję **Rozpocznij** \> administrację licencji programu **License Manager** \> **License Administration**, a następnie kliknij kartę **Instalacja** . Wybierz typ formatu licencji do przekazania: plik licencji lub 16-znakowy kod licencji. Na przykład w przypadku pliku w **pliku licencji**przejdź do `mflic` pliku * przekazanego wcześniej do maszyny wirtualnej i wybierz pozycję **Zainstaluj licencje**.

    ![Okno dialogowe Administrowanie licencjami w programie Micro Focus](media/install-image-3.png)

3.  Sprawdź, czy serwer przedsiębiorstwa ładuje. Spróbuj uruchomić witrynę administracyjną przedsiębiorstwa Server z przeglądarki przy użyciu tego adresu URL: `http://localhost:86/` . Zostanie wyświetlona strona Administracja serwera przedsiębiorstwa, jak pokazano.

    ![Strona administrowania serwerem przedsiębiorstwa](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Zainstaluj dewelopera przedsiębiorstwa na komputerze dewelopera

1.  Wybierz utworzoną wcześniej grupę zasobów (na przykład **RGMicroFocusEntServer**), a następnie wybierz obraz dewelopera.

2.  Aby zalogować się do maszyny wirtualnej, przejdź do sekcji **Omówienie** i wybierz pozycję **Połącz**. To logowanie uruchamia sesję RDP. Zaloguj się przy użyciu poświadczeń utworzonych dla maszyny wirtualnej.

3.  W sesji RDP Załaduj następujące dwa pliki (przeciągnij i upuść, jeśli chcesz):

    -   `edvs2017.exe`, plik instalacyjny serwera przedsiębiorstwa.

    -   `mflic`, odpowiedni plik licencji (deweloper przedsiębiorstwa nie będzie ładowany).

4.  Kliknij dwukrotnie plik **edvs2017.exe** , aby rozpocząć instalację. W pierwszym oknie Wybierz lokalizację instalacji i zaakceptuj umowę licencyjną użytkownika końcowego. Jeśli chcesz, wybierz pozycję **Zainstaluj Rumba 9,5** , aby zainstalować ten emulator terminalu, którego prawdopodobnie potrzebujesz.

    ![Okno dialogowe Instalator programu Enterprise Developer dla programu Visual Studio 2017](media/install-image-5.png)

5.  Po zakończeniu instalacji zostanie wyświetlony następujący komunikat:

    ![Komunikat o pomyślnym skonfigurowaniu](media/install-image-6.png)

6.  Uruchom Menedżera licencji programu Micro Focus tak samo jak w przypadku programu Enterprise Server. Wybierz **Start** \> **Micro Focus License Manager** \> kolejno pozycje Start **Zarządzanie**licencjami Menedżer licencji, a następnie kliknij kartę **Instaluj** .

7.  Wybierz typ formatu licencji do przekazania: plik licencji lub 16-znakowy kod licencji. Na przykład dla pliku w **pliku licencji**przejdź do `mflic` pliku przekazanego wcześniej do maszyny wirtualnej i wybierz pozycję  **Zainstaluj licencje**.

    ![Okno dialogowe Administrowanie licencjami w programie Micro Focus](media/install-image-7.png)

Podczas ładowania dewelopera przedsiębiorstwa wdrożenie mikrofokusu i środowiska testowego na platformie Azure zostało zakończone.

**Następne kroki**

-   [Konfigurowanie aplikacji BankDemo](./demo.md)

-   [Uruchamianie serwera korporacyjnego w kontenerach platformy Docker](./run-enterprise-server-container.md)

-   [Migracja aplikacji komputerów mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
