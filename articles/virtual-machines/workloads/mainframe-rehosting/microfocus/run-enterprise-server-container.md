---
title: Uruchamianie programu Micro Focus Enterprise Server 4,0 w kontenerze platformy Docker na platformie Azure Virtual Machines
description: Hostowanie obciążeń systemu mainframe firmy IBM z/OS przy użyciu programu Micro Focus Enterprise Server w kontenerze platformy Docker na platformie Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488460"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Uruchamianie programu Micro Focus Enterprise Server 4,0 w kontenerze platformy Docker na platformie Azure

W kontenerze Docker na platformie Azure można uruchomić system Micro Focus Enterprise Server 4,0. W tym samouczku pokazano, jak to zrobić. Używa demonstracji systemu Windows CICS (system kontroli informacji o klientach) dla serwera przedsiębiorstwa.

Platforma Docker dodaje przenośność i izolację do aplikacji. Można na przykład wyeksportować obraz platformy Docker z jednej maszyny wirtualnej z systemem Windows do uruchomienia na innym lub z repozytorium do systemu Windows Server przy użyciu platformy Docker. Obraz platformy Docker jest uruchamiany w nowej lokalizacji z tą samą konfiguracją — bez konieczności instalowania serwera Enterprise. Jest to część obrazu. Nadal obowiązują zagadnienia dotyczące licencjonowania.

W tym samouczku zostanie zainstalowane oprogramowanie **Windows 2016 Datacenter z** maszyną wirtualną kontenerów (VM) z portalu Azure Marketplace. Ta maszyna wirtualna zawiera **platformę Docker 18.09.0**. W poniższych krokach pokazano, jak wdrożyć kontener, uruchomić go, a następnie połączyć się z nim przy użyciu emulatora 3270.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem sprawdź następujące wymagania wstępne:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Oprogramowanie mikro Focus i ważną licencję (lub licencję próbną). Jeśli jesteś istniejącym klientem programu Micro Focus, skontaktuj się z przedstawicielem firmy Micro Focus. W przeciwnym razie [Poproś o wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Pliki demonstracyjne platformy Docker są dołączone do wersji Enterprise Server 4,0. W tym samouczku\_jest\_wykorzystywany\_serwer\_wieloetapowe dockerfile 4,0 Windows. zip. Uzyskuj dostęp do niego z tego samego miejsca, do którego uzyskano dostęp do pliku instalacyjnego serwera przedsiębiorstwa, lub przejdź do obszaru *Micro Focus* , aby rozpocząć pracę.

- Dokumentacja dotycząca [rozwiązań Enterprise Server i Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. \_Zabezpiecz nośnik z pliku wieloetapowe dockerfile\_\_4,0\_Windows. zip serwera. Zabezpiecz plik licencjonowania ES-Docker-prod-XXXXXXXX. mflic (wymagany do skompilowania obrazów platformy Docker).

2. Utwórz maszynę wirtualną. W tym celu otwórz Azure Portal, wybierz pozycję **Utwórz zasób** w lewym górnym rogu i przefiltruj według *systemu Windows Server*. W wynikach wybierz pozycję **Windows Server 2016 Datacenter — z kontenerami**.

     ![Wyniki wyszukiwania Azure Portal](media/01-portal.png)

3. Aby skonfigurować właściwości dla maszyny wirtualnej, wybierz Szczegóły wystąpienia:

    1. Wybierz rozmiar maszyny wirtualnej. Na potrzeby tego samouczka Rozważ użycie **standardowej maszyny\_wirtualnej DS2 v2** z 2 procesorów wirtualnych vCPU i 7 GB pamięci.

    2. Wybierz **region** i **grupę zasobów** , do której chcesz wykonać wdrożenie.

    3. Aby uzyskać **Opcje dostępności**, użyj ustawienia domyślnego.

    4. W polu **Nazwa użytkownika**wpisz konto administratora, którego chcesz użyć, i hasło.

    5. Upewnij się, że **port 3389 RDP** jest otwarty. Tylko ten port musi być ujawniony publicznie, aby można było zalogować się do maszyny wirtualnej. Następnie Zaakceptuj wszystkie wartości domyślne i kliknij przycisk **Przegląd + Utwórz**.

     ![Utwórz okienko maszyny wirtualnej](media/container-02.png)

4. Poczekaj na zakończenie wdrożenia (kilka minut). Zostanie wyświetlony komunikat z informacją, że maszyna wirtualna została utworzona.

5. Kliknij pozycję **Przejdź do zasobu** , aby przejść do bloku **Przegląd** dla maszyny wirtualnej.

6. Po prawej stronie kliknij przycisk **Połącz** . Opcje **Połącz z maszyną wirtualną** pojawiają się po prawej stronie.

7. Kliknij przycisk **Pobierz plik RDP** , aby pobrać plik RDP, który umożliwia dołączenie do maszyny wirtualnej.

8. Po zakończeniu pobierania pliku Otwórz go, a następnie wpisz nazwę użytkownika i hasło, które zostały utworzone dla maszyny wirtualnej.

     > [!NOTE]
     > Nie używaj poświadczeń firmowych, aby się zalogować. (Klient RDP zakłada, że może być konieczne ich użycie. Nie jesteś.)

9.  Wybierz pozycję **więcej opcji**, a następnie wybierz swoje poświadczenia maszyny wirtualnej.

W tym momencie maszyna wirtualna jest uruchomiona i dołączona za pośrednictwem protokołu RDP. Użytkownik jest zalogowany i gotowy do następnego kroku.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Utwórz katalog piaskownicy i Przekaż plik zip

1.  Utwórz katalog na maszynie wirtualnej, w którym można przekazać pliki demonstracyjne i licencje. Na przykład **C:\\piaskownica**.

2.  Przekaż **\_serwer\_wieloetapowe dockerfile\_4,0\_Windows. zip** i **es-Docker-prod-XXXXXXXX. mflic** do utworzonego katalogu.

3.  Wyodrębnij zawartość pliku zip do **\_serwera\_wieloetapowe dockerfile\_\_4,0 systemu Windows** utworzonego przez proces wyodrębniania. Ten katalog zawiera plik Readme (plik HTML i txt) i dwa podkatalogi, **EnterpriseServer** i **przykłady**.

4.  Copy **es-Docker-prod-XXXXXXXX. mflic** do\\C: piaskownicy\\\_serwer\_wieloetapowe dockerfile\_4,0\_Windows\\EnterpriseServer i C:\\piaskownica\\Enterprise\_Server\_wieloetapowe dockerfile\_4,0\_Windows\\przykłady\\CICS directorys.

> [!NOTE]
> Upewnij się, że plik licencji jest kopiowany do obu katalogów. Są one wymagane dla kroku kompilacja Docker, aby upewnić się, że obrazy są prawidłowo licencjonowane.

## <a name="check-docker-version-and-create-base-image"></a>Sprawdzanie wersji platformy Docker i tworzenie obrazu podstawowego

> [!IMPORTANT]
> Tworzenie odpowiedniego obrazu platformy Docker jest procesem dwuetapowym. Najpierw Utwórz podstawowy obraz programu Enterprise Server 4,0.Następnie utwórz inny obraz dla platformy x64. Mimo że można utworzyć obraz x86 (32-bitowy), Użyj obrazu 64-bitowego.

1. Otwórz wiersz polecenia.

2. Sprawdź, czy platforma Docker jest zainstalowana. W wierszu polecenia wpisz polecenie:

    ```
        docker version
    ```

     Na przykład wersja została 18.09.0 podczas zapisywania.

3. Aby zmienić katalog, wpisz **CD \sandbox\ ent_server_dockerfiles_4.0_windows \enterpriseserver**.

4. Wpisz **Bld. bat IacceptEULA** , aby rozpocząć proces kompilacji dla początkowego obrazu podstawowego. Zaczekaj kilka minut, aż proces zostanie uruchomiony. W wynikach Zwróć uwagę na dwa utworzone obrazy — jeden dla x64 i jeden dla x86:

     ![okno Polecenie przedstawiające obrazy](media/container-04.png)

5. Aby utworzyć końcowy obraz dla pokazu CICS, przełącz się do katalogu CICS, wpisując w **systemie\\\\piaskownicę\_CD\_Server\_wieloetapowe dockerfile\_4,0\\przykłady\\systemu Windows CICS**.

6. Aby utworzyć obraz, wpisz polecenie **Bld. bat x64**. Poczekaj kilka minut, aż proces zostanie uruchomiony, i komunikat informujący o tym, że obraz został utworzony.

7. Wpisz **obrazy platformy Docker** , aby wyświetlić listę wszystkich obrazów platformy Docker zainstalowanych na maszynie wirtualnej. Upewnij się, że **acctdemo** jest jednym z nich.

     ![okno Polecenie przedstawiające acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Uruchamianie obrazu 

1.  Aby uruchomić serwer Enterprise Server 4,0 i aplikację acctdemo, w wierszu polecenia wpisz:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Zainstaluj emulator terminalu 3270, taki jak [x3270](http://x3270.bgp.nu/) , i użyj go do dołączenia, za pośrednictwem portu 9040, do obrazu, który jest uruchomiony.

3.  Pobierz adres IP kontenera acctdemo, aby platforma Docker mogła działać jako serwer DHCP dla kontenerów, którymi zarządza:

    1.  Pobierz identyfikator uruchomionego kontenera. W wierszu polecenia wpisz **Docker PS** i zanotuj identyfikator (**22a0fe3159d0** w tym przykładzie). Zapisz go w następnym kroku.

    2.  Aby uzyskać adres IP dla kontenera acctdemo, użyj identyfikatora kontenera z poprzedniego kroku w następujący sposób:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Przykład:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Zanotuj adres IP dla obrazu acctdemo. Na przykład adres w następujących danych wyjściowych to 172.19.202.52.

     ![okno Polecenie pokazywane adresy IP](media/container-06.png)

5. Zainstaluj obraz przy użyciu emulatora. Skonfiguruj emulator, aby używał adresu obrazu acctdemo i portu 9040. Tutaj jest **172.19.202.52:9040**. Będziesz wyglądać podobnie. Zostanie otwarty ekran **jednokrotnego do CICS** .

    ![Ekran jednokrotnego do CICS](media/container-07.png)

6. Zaloguj się do regionu CICS, wprowadzając **SYSAD** dla **UserID** i **SYSAD** dla **hasła**.

7. Wyczyść ekran przy użyciu keymap emulatora. Dla x3270 wybierz opcję menu **keymap** .

8. Aby uruchomić aplikację acctdemo, wpisz **ACCT**. Zostanie wyświetlony ekran początkowy aplikacji.

     ![Ekran demonstracyjny konta](media/container-08.png)

9. Eksperymentuj z wyświetlanymi typami kont. Na przykład wpisz **D** dla żądania i **11111** dla **konta**. Inne numery kont do wypróbowania to 22222, 33333 i tak dalej.

     ![Ekran demonstracyjny konta](media/container-09.png)

10. Aby wyświetlić konsolę administracyjną Enterprise Server, przejdź do wiersza polecenia i wpisz polecenie **Start http: 172.19.202.52:86**

     ![Konsola administracyjna serwera przedsiębiorstwa](media/container-010.png)

Gotowe. Teraz uruchamiasz aplikację CICS i zarządzasz nią w kontenerze platformy Docker.

## <a name="next-steps"></a>Następne kroki

- [Instalowanie programu Micro Focus Enterprise Server 4,0 i Enterprise Developer 4,0 na platformie Azure](./set-up-micro-focus-azure.md)
- [Migracja aplikacji komputerów mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
