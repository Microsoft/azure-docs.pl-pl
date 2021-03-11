---
title: Uruchamianie programu Micro Focus Enterprise Server 5,0 w kontenerze platformy Docker na platformie Azure | Microsoft Docs
description: W tym artykule dowiesz się, jak uruchomić program Micro Focus Enterprise Server 5,0 w kontenerze platformy Docker na Microsoft Azure.
services: virtual-machines
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
ms.openlocfilehash: 5c436eae53b16c980e9725cfef0573367d144842
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548380"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Uruchamianie programu Micro Focus Enterprise Server 5,0 w kontenerze platformy Docker na platformie Azure

W kontenerze Docker na platformie Azure można uruchomić system Micro Focus Enterprise Server 5,0. W tym samouczku pokazano, jak to zrobić. Używa demonstracji systemu Windows CICS (system kontroli informacji o klientach) dla serwera przedsiębiorstwa.

Platforma Docker dodaje przenośność i izolację do aplikacji. Można na przykład wyeksportować obraz platformy Docker z jednej maszyny wirtualnej z systemem Windows do uruchamiania na innym lub z repozytorium na serwerze z systemem Windows przy użyciu platformy Docker. Obraz platformy Docker jest uruchamiany w nowej lokalizacji z tą samą konfiguracją — bez konieczności instalowania serwera Enterprise. Jest to część obrazu. Nadal obowiązują zagadnienia dotyczące licencjonowania.

W tym samouczku zostanie zainstalowane oprogramowanie **Windows 2016 Datacenter z** maszyną wirtualną kontenerów z poziomu portalu Azure Marketplace. Ta maszyna wirtualna zawiera **platformę Docker 18.09.0**. Poniższe kroki pokazują, jak wdrożyć kontener, uruchomić go, a następnie połączyć się z nim przy użyciu emulatora 3270.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem sprawdź następujące wymagania wstępne:

-   Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

-   Oprogramowanie mikro Focus i ważną licencję (lub licencję próbną). Jeśli jesteś istniejącym klientem programu Micro Focus, skontaktuj się z przedstawicielem firmy Micro Focus. W przeciwnym razie [Poproś o wersję próbną](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > Pliki demonstracyjne platformy Docker są dołączone do wersji Enterprise Server 5,0. Ten samouczek używa \_ serwera \_ wieloetapowe dockerfile \_ 5,0 \_windows.zip. Uzyskuj dostęp do niego z tego samego miejsca, do którego uzyskano dostęp do pliku instalacyjnego programu Enterprise Server, lub przejdź na stronę *Micro Focus* , aby rozpocząć pracę.

-   Dokumentacja dotycząca [rozwiązań Enterprise Server i Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22).

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1.  Zabezpiecz nośnik z \_ \_ \_ pliku wieloetapowe dockerfile 5,0 \_windows.zip Server. Zabezpiecz plik licencjonowania ES-Docker-prod-XXXXXXXX. mflic (wymagany do skompilowania obrazów platformy Docker).

2.  Utwórz maszynę wirtualną. W tym celu otwórz Azure Portal, wybierz pozycję **Utwórz zasób** z menu u góry po lewej stronie i przefiltruj według *systemu operacyjnego Windows Server*. W wynikach wybierz pozycję **Windows Server.** Na następnym ekranie wybierz pozycję **Windows Server 2016 Datacenter — z kontenerami**.

    ![Zrzut ekranu przedstawiający wyniki wyszukiwania Azure Portal](./media/run-image-1.png)

3.  Aby skonfigurować właściwości dla maszyny wirtualnej, wybierz Szczegóły wystąpienia:

    1.  Wybierz rozmiar maszyny wirtualnej. Na potrzeby tego samouczka Rozważ użycie **standardowej maszyny wirtualnej DS2 \_ v3** z 2 procesorów wirtualnych vCPU i 16 GB pamięci.

    2.  Wybierz **region** i **grupę zasobów** , do której chcesz przeprowadzić wdrożenie.

    3.  Aby uzyskać **Opcje dostępności**, użyj ustawienia domyślnego.

    4.  W polu **Nazwa użytkownika** wpisz konto administratora, którego chcesz użyć, i hasło.

    5.  Upewnij się, że **port 3389 RDP** jest otwarty. Tylko ten port musi być ujawniony publicznie, aby można było zalogować się do maszyny wirtualnej. Następnie Zaakceptuj wszystkie wartości domyślne i kliknij przycisk **Przegląd + Utwórz**.

    ![Zrzut ekranu przedstawiający okienko Tworzenie maszyny wirtualnej](./media/run-image-2.png)

4.  Poczekaj na zakończenie wdrożenia (kilka minut). Zostanie wyświetlony komunikat z informacją, że maszyna wirtualna została utworzona.

5.  Wybierz pozycję **Przejdź do zasobu** , aby przejść do bloku **Przegląd** dla maszyny wirtualnej.

6.  Po prawej stronie wybierz pozycję **Połącz**. Opcje **Połącz z maszyną wirtualną** pojawiają się po prawej stronie.

7.  Wybierz przycisk **Pobierz plik RDP** , aby pobrać plik protokołu RDP (Remote Desktop Protocol), który umożliwia dołączenie do maszyny wirtualnej.

8.  Po zakończeniu pobierania pliku Otwórz go, a następnie wpisz nazwę użytkownika i hasło, które zostały utworzone dla maszyny wirtualnej.

    > [!Note]    
    > Nie używaj poświadczeń firmowych, aby się zalogować. (Klient RDP zakłada, że może być konieczne ich użycie. Nie jesteś.)

9.  Wybierz pozycję **więcej opcji**, a następnie wybierz swoje poświadczenia maszyny wirtualnej.

W tym momencie maszyna wirtualna jest uruchomiona i dołączona za pośrednictwem protokołu RDP. Użytkownik jest zalogowany i gotowy do następnego kroku.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Utwórz katalog piaskownicy i Przekaż plik zip

1.  Utwórz katalog na maszynie wirtualnej, w którym można przekazać pliki demonstracyjne i licencje. Na przykład **C: \\ piaskownica**.

2.  Przekaż **\_ serwer \_ wieloetapowe dockerfile \_ \_windows.zip5,0** i plik **es-Docker-prod-XXXXXXXX. mflic** do utworzonego katalogu.

3.  Wyodrębnij zawartość pliku zip do **\_ serwera \_ wieloetapowe dockerfile \_ 5,0 \_ systemu Windows** utworzonego przez proces wyodrębniania. Ten katalog zawiera plik Readme (plik HTML i txt) i dwa podkatalogi, **EnterpriseServer** i **przykłady**.

4.  Copy **es-Docker-prod-XXXXXXXX. mflic** do C: \\ piaskownicy \\ \_ serwer \_ wieloetapowe dockerfile \_ 5,0 \_ Windows \\ EnterpriseServer i C: \\ piaskownica \\ Enterprise \_ Server \_ wieloetapowe dockerfile \_ 5,0 \_ Windows \\ przykłady CICS \\ directorys.  
      
    > [!Note]
    > Upewnij się, że plik licencji jest kopiowany do obu katalogów. Są one wymagane dla kroku kompilacja Docker, aby upewnić się, że obrazy są prawidłowo licencjonowane.

## <a name="check-docker-version-and-create-base-image"></a>Sprawdzanie wersji platformy Docker i tworzenie obrazu podstawowego

> [!Important]  
> Tworzenie odpowiedniego obrazu platformy Docker jest procesem dwuetapowym. Najpierw Utwórz podstawowy obraz programu Enterprise Server 5,0. Następnie utwórz inny obraz dla platformy x64. Mimo że można utworzyć obraz x86 (32-bitowy), Użyj obrazu 64-bitowego.

1.  Otwórz wiersz polecenia.

2.  Sprawdź, czy platforma Docker jest zainstalowana. W wierszu polecenia wpisz: **wersja platformy Docker**  
    Na przykład wersja została 18.09.0 podczas zapisywania.

3.  Aby zmienić katalog, wpisz:  
    **dysk CD \\ Piaskownica \\ Enterprise \_ server \_ wieloetapowe dockerfile \_ 5,0 \_ Windows \\ EnterpriseServer**.

4.  Wpisz **bld.bat IacceptEULA** , aby rozpocząć proces kompilacji dla początkowego obrazu podstawowego. Zaczekaj kilka minut, aż proces zostanie uruchomiony. W wynikach Zwróć uwagę na dwa utworzone obrazy — jeden dla x64 i jeden dla x86:

    ![okno Polecenie przedstawiające obrazy](./media/run-image-3.png)

5.  Aby utworzyć końcowy obraz dla pokazu CICS, przełącz się do katalogu CICS, wpisując w **\\ systemie piaskownicę CD \\ \_ Server \_ wieloetapowe dockerfile \_ 5,0 \_ przykłady systemu Windows \\ \\ CICS**.

6.  Aby utworzyć obraz, wpisz **bld.bat x64**. Poczekaj kilka minut, aż proces zostanie uruchomiony, i komunikat informujący o tym, że obraz został utworzony.

7.  Wpisz **obrazy platformy Docker** , aby wyświetlić listę wszystkich obrazów platformy Docker zainstalowanych na maszynie wirtualnej. Upewnij się, że **acctdemo** jest jednym z nich.

    ![okno Polecenie przedstawiające acctdemo](./media/run-image-4.png)

## <a name="run-the-image"></a>Uruchamianie obrazu

1.  Aby uruchomić serwer Enterprise Server 5,0 i aplikację acctdemo, w wierszu polecenia wpisz:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Zainstaluj emulator terminalu 3270, taki jak [x3270](http://x3270.bgp.nu/) , i użyj go do dołączenia, za pośrednictwem portu 9040, do obrazu, który jest uruchomiony.

2.  Uzyskaj adres IP kontenera acctdemo, aby platforma Docker mogła działać jako serwer Dynamic Host Configuration Protocol (DHCP) dla kontenerów, którymi zarządza:

    1.  Pobierz identyfikator uruchomionego kontenera. W wierszu polecenia wpisz **Docker PS** i zanotuj identyfikator (**22a0fe3159d0** w tym przykładzie). Zapisz go w następnym kroku.

    2.  Aby uzyskać adres IP dla kontenera acctdemo, użyj identyfikatora kontenera z poprzedniego kroku w następujący sposób:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Na przykład:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Zanotuj adres IP dla obrazu acctdemo. Na przykład adres w następujących danych wyjściowych to 172.19.202.52.

    ![Zrzut ekranu przedstawiający okno Polecenie pokazujący adres IP](./media/run-image-5.png)

5. Zainstaluj obraz przy użyciu emulatora. Skonfiguruj emulator, aby używał adresu obrazu acctdemo i portu 9040. Tutaj jest **172.19.202.52:9040**. Będziesz wyglądać podobnie. Zostanie otwarty ekran **Logowanie do CICS** .

    ![Zrzut ekranu przedstawiający jednokrotnego do CICS](./media/run-image-6.png)

6. Zaloguj się do regionu CICS, wprowadzając **SYSAD** dla **UserID** i **SYSAD** dla **hasła**.

7. Wyczyść ekran przy użyciu keymap emulatora. Dla x3270 wybierz opcję menu **keymap** .

8. Aby uruchomić aplikację acctdemo, wpisz **ACCT**. Zostanie wyświetlony ekran początkowy aplikacji.

     ![Zrzut ekranu przedstawia okno konsoli zawierające aplikację.](./media/run-image-7.png)

9. Eksperymentuj z wyświetlanymi typami kont. Na przykład wpisz **D** dla żądania i **11111** dla **konta**. Inne numery kont do wypróbowania to 22222, 33333 i tak dalej.

    ![Zrzut ekranu przedstawia edytowanie różnych wartości w aplikacji.](./media/run-image-8.png)

10. Aby wyświetlić konsolę administracyjną Enterprise Server, przejdź do wiersza polecenia i wpisz polecenie **Start http: 172.19.202.52:86**.

    ![Konsola administracyjna serwera przedsiębiorstwa](media/run-image-9.png)

Gotowe. Teraz uruchamiasz aplikację CICS i zarządzasz nią w kontenerze platformy Docker.

## <a name="next-steps"></a>Następne kroki

-   [Instalowanie programu Micro Focus Enterprise Server 5,0 i Enterprise Developer 5,0 na platformie Azure](./set-up-micro-focus-azure.md)

-   [Migracja aplikacji komputerów mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
