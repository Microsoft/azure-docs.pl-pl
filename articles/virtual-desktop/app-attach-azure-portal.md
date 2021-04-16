---
title: Windows Virtual Desktop portal dołączania aplikacji MSIX — Azure
description: Jak skonfigurować dołączanie aplikacji MSIX dla aplikacji Windows Virtual Desktop użyciu Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21dbab6c8d4fb12fe79434a6994dd7f5b8a49190
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502712"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Konfigurowanie dołączania aplikacji MSIX przy użyciu witryny Azure Portal

W tym artykule opisano sposób skonfigurowania dołączania aplikacji MSIX w Windows Virtual Desktop środowiska.

## <a name="requirements"></a>Wymagania

Oto co jest potrzebne do skonfigurowania dołączania aplikacji MSIX:

- Działające wdrożenie Windows Virtual Desktop wdrożenia. Aby dowiedzieć się, jak wdrożyć Windows Virtual Desktop (klasyczne), zobacz [Tworzenie dzierżawy w](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)Windows Virtual Desktop . Aby dowiedzieć się, jak wdrożyć Windows Virtual Desktop z Azure Resource Manager integracji, zobacz Tworzenie puli [hostów przy](./create-host-pools-azure-marketplace.md)użyciu Azure Portal .
- Pula Windows Virtual Desktop z co najmniej jednym aktywnym hostem sesji.
- Narzędzie do tworzenia pakietów MSIX.
- Aplikacja w pakiecie MSIX została rozszerzona na obraz MSIX przekazany do udziału plików.
- Udział plików we wdrożeniu Windows Virtual Desktop, w którym będzie przechowywany pakiet MSIX.
- Udział plików, do którego został przekazany obraz MSIX, musi być również dostępny dla wszystkich maszyn wirtualnych w puli hostów. Użytkownicy będą potrzebować uprawnień tylko do odczytu, aby uzyskać dostęp do obrazu.
- Jeśli certyfikat nie jest publicznie zaufany, postępuj zgodnie z instrukcjami w tece [Instalowanie certyfikatów.](app-attach.md#install-certificates)

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Wyłączanie aktualizacji automatycznych dla aplikacji dołączanych do aplikacji MSIX

Przed rozpoczęciem należy wyłączyć automatyczne aktualizacje dla aplikacji dołączanych do aplikacji MSIX. Aby wyłączyć aktualizacje automatyczne, należy uruchomić następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>Zaleca się ponowne uruchomienie maszyny wirtualnej po włączeniu funkcji Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Konfigurowanie interfejsu zarządzania dołączania aplikacji MSIX

Następnie należy pobrać i skonfigurować interfejs zarządzania dołączania aplikacji MSIX dla Azure Portal.

Aby skonfigurować interfejs zarządzania:

1. [Otwórz Azure Portal](https://portal.azure.com).
2. Jeśli zostanie wyświetlony monit z pytaniem, czy uważasz rozszerzenie za wiarygodne, wybierz pozycję **Zezwalaj.**

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający okno niezaufanych rozszerzeń. "Zezwalaj" jest wyróżnione na czerwono.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Dodawanie obrazu MSIX do puli hostów

Następnie należy dodać obraz MSIX do puli hostów.

Aby dodać obraz MSIX:

1. Otwórz witrynę Azure Portal.

2. Wprowadź **Windows Virtual Desktop** na pasku wyszukiwania, a następnie wybierz nazwę usługi.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający użytkownika wybierającego pozycję "Windows Virtual Desktop" z menu rozwijanego paska wyszukiwania w Azure Portal. "Windows Virtual Desktop" jest wyróżnione na czerwono.](media/find-and-select.png)

3. Wybierz pulę hostów, w której planujesz umieścić aplikacje MSIX.

4. Wybierz **pozycję Pakiety MSIX,** aby otworzyć siatkę danych ze wszystkimi pakietami **MSIX** aktualnie dodanymi do puli hostów.

5. Wybierz **pozycję + Dodaj,** aby otworzyć **kartę Dodawanie pakietu MSIX.**

6. Na karcie **Dodawanie pakietu MSIX** wprowadź następujące wartości:

      - W **przypadku ścieżki obrazu MSIX** wprowadź prawidłową ścieżkę UNC, która będzie wskazać obraz MSIX w udziałach plików. (Na przykład `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` ). Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj,** aby sprawdzić, czy ścieżka jest prawidłowa, aby sprawdzić kontener MSIX.

      - W **przypadku pakietu MSIX** wybierz odpowiednią nazwę pakietu MSIX z menu rozwijanego. To menu zostanie wypełnione tylko po wprowadzeniu prawidłowej ścieżki obrazu w ścieżce obrazu **MSIX.**

      - W **przypadku aplikacji pakietu** upewnij się, że lista zawiera wszystkie aplikacje MSIX, które mają być dostępne dla użytkowników w pakiecie MSIX.

      - Opcjonalnie wprowadź nazwę **wyświetlaną,** jeśli chcesz, aby pakiet był bardziej przyjazny dla użytkownika we wdrożeniach użytkowników.

      - Upewnij się, **że wersja** ma prawidłowy numer wersji.

      - Wybierz typ **rejestracji,** którego chcesz użyć. Którego z nich używasz, zależy od twoich potrzeb:

    - **Rejestracja na żądanie** odracza pełną rejestrację aplikacji MSIX do momentu, gdy użytkownik uruchomi aplikację. Jest to typ rejestracji, z których zalecamy korzystanie.

    - **Logowanie blokuje tylko** rejestracje podczas logowania użytkownika. Nie zalecamy tego typu, ponieważ może to prowadzić do dłuższych czasów logowania użytkowników.

7.  W **przypadku opcji** Stan wybierz preferowany stan.
    -  Stan **Aktywny** umożliwia użytkownikom interakcję z pakietem.
    -  Stan **Nieaktywny** Windows Virtual Desktop zignorować pakiet i nie dostarczyć go do użytkowników.

8. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publikowanie aplikacji MSIX w grupie aplikacji

Następnie należy opublikować aplikacje w pakiecie. Należy to zrobić zarówno dla grup aplikacji klasycznych, jak i zdalnych.

Jeśli masz już obraz MSIX, przejdź do publikowania aplikacji [MSIX w grupie aplikacji.](#publish-msix-apps-to-an-app-group) Jeśli chcesz przetestować starsze aplikacje, postępuj zgodnie z instrukcjami w teście Tworzenie pakietu [MSIX](/windows/msix/packaging-tool/create-app-package-msi-vm/) z instalatora pulpitu na maszynie wirtualnej, aby przekonwertować starszą aplikację na pakiet MSIX.

Aby opublikować aplikacje:

1. Na stronie Windows Virtual Desktop zasobów wybierz **kartę Grupy** aplikacji.

2. Wybierz grupę aplikacji, w której chcesz opublikować aplikacje.

   >[!NOTE]
   >Aplikacje MSIX można dostarczać z dołączeniem aplikacji MSIX do grup aplikacji zdalnych i klasycznych

3. Po dojecheniu do grupy aplikacji wybierz **kartę** Aplikacje. **Siatka** Aplikacje będzie wyświetlać wszystkie istniejące aplikacje w grupie aplikacji.

4. Wybierz **pozycję + Dodaj,** aby otworzyć **kartę Dodaj** aplikację.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający użytkownika wybierającego pozycję + Dodaj, aby otworzyć kartę dodawania aplikacji](media/select-add.png)

5. W **przypadku źródła** aplikacji wybierz źródło aplikacji.
    - Jeśli używasz grupy aplikacji klasycznych, wybierz pakiet **MSIX.**
      
      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający klienta wybierającego pakiet MSIX z menu rozwijanego źródła aplikacji. Pakiet MSIX został wyróżniony na czerwono.](media/select-source.png)
    
    - Jeśli używasz zdalnej grupy aplikacji, wybierz jedną z następujących opcji:
        
        - Menu Start
        - Ścieżka aplikacji
        - Pakiet MSIX

    - W **polach** Nazwa aplikacji wprowadź opisową nazwę aplikacji.

    Można również skonfigurować następujące funkcje opcjonalne:
   
    - W **polach** Nazwa wyświetlana wprowadź nową nazwę pakietu, która będzie wyświetlana użytkownikom.

    - W **polu** Opis wprowadź krótki opis pakietu aplikacji.

    - Jeśli używasz zdalnej grupy aplikacji, możesz również skonfigurować następujące opcje:

        - **Ścieżka ikony**
        - **Indeks ikon**
        - **Pokaż w internetowym kanale informacyjnym**

6. Po zakończeniu wybierz pozycję **Zapisz**.

>[!NOTE]
>Gdy użytkownik zostanie przypisany do grupy aplikacji zdalnych i grupy aplikacji pulpitu z tej samej puli hostów, grupa aplikacji klasycznych będzie wyświetlana w kanale informacyjnym.

## <a name="assign-a-user-to-an-app-group"></a>Przypisywanie użytkownika do grupy aplikacji

Po przypisaniu aplikacji MSIX do grupy aplikacji należy udzielić użytkownikom dostępu do nich. Dostęp można przypisać, dodając użytkowników lub grupy użytkowników do grupy aplikacji z opublikowanymi aplikacjami MSIX. Postępuj zgodnie z instrukcjami w te Azure Portal zarządzanie grupami [aplikacji,](manage-app-groups.md) aby przypisać użytkowników do grupy aplikacji.

>[!NOTE]
>Aplikacje zdalne dołączania aplikacji MSIX mogą znikać ze źródła danych podczas testowania aplikacji zdalnych w publicznej wersji zapoznawczej. Aplikacje nie są wyświetlane, ponieważ pula hostów, z których korzystasz w środowisku oceny, jest obsługiwany przez brokera usług pulpitu zdalnego w środowisku produkcyjnym. Ponieważ broker usług pulpitu zdalnego w środowisku produkcyjnym nie rejestruje obecności aplikacji MSIX dołączania aplikacji zdalnych, aplikacje nie będą wyświetlane w kanale informacyjnym.

## <a name="change-msix-package-state"></a>Zmienianie stanu pakietu MSIX

Następnie należy zmienić stan pakietu MSIX na  Aktywny lub Nieaktywny **w** zależności od tego, co chcesz zrobić z pakietem. Aktywne pakiety to pakiety, z których użytkownicy mogą korzystać po ich opublikowaniu. Nieaktywne pakiety są ignorowane Windows Virtual Desktop, dzięki czemu użytkownicy nie mogą wchodzić w interakcje z aplikacjami w jej obrębie.

### <a name="change-state-with-the-applications-list"></a>Zmienianie stanu na liście aplikacji

Aby zmienić stan pakietu na liście Aplikacje:

1. Przejdź do puli hostów i wybierz pozycję **Pakiety MSIX.** Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz pakiety MSIX, których stany należy zmienić, a następnie wybierz **pozycję Zmień stan**.

### <a name="change-state-with-update-package"></a>Zmienianie stanu za pomocą pakietu aktualizacji

Aby zmienić stan pakietu za pomocą pakietu aktualizacji:

1. Przejdź do puli hostów i wybierz pozycję **Pakiety MSIX.** Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz z listy pakietów MSIX nazwę pakietu, którego stan chcesz zmienić. Spowoduje to otwarcie **karty Aktualizowanie** pakietu.

3. Przełącz przełącznik Stan **na opcję Nieaktywne** **lub** **Aktywne,** a następnie wybierz pozycję **Zapisz.**

## <a name="change-msix-package-registration-type"></a>Zmiana typu rejestracji pakietu MSIX

Aby zmienić typ rejestracji pakietu:

1. Wybierz pozycję **Pakiety MSIX.** Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz **pozycję Nazwa pakietu w** **siatce pakietów MSIX.** Spowoduje to otwarcie bloku w celu zaktualizowania pakietu.

3. Przełącz typ **rejestracji za pomocą** **przycisku blokowania Na żądanie/Zaloguj** się zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz.**

## <a name="remove-an-msix-package"></a>Usuwanie pakietu MSIX

Aby usunąć pakiet MSIX z puli hostów:

1. Wybierz pozycję **Pakiety MSIX.**  Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz wielokropek po prawej stronie nazwę pakietu, który chcesz usunąć, a następnie wybierz pozycję **Usuń**.

## <a name="remove-msix-apps"></a>Usuwanie aplikacji MSIX

Aby usunąć poszczególne aplikacje MSIX z pakietu:

1. Przejdź do puli hostów i wybierz **pozycję Grupy aplikacji.**

2. Wybierz grupę aplikacji, z której chcesz usunąć aplikacje MSIX.

3. Otwórz **kartę** Aplikacje.

4. Wybierz aplikację, którą chcesz usunąć, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Zadaj naszemu społeczności pytania dotyczące tej funkcji na stronie [Windows Virtual Desktop TechCo w witrynie](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Opinię na ten temat możesz również Windows Virtual Desktop w [centrum Windows Virtual Desktop opinii.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Oto kilka innych artykułów, które mogą okazać się przydatne:

- [Słownik dołączania aplikacji MSIX](app-attach-glossary.md)
- [Często zadawane pytania dotyczące dołączania aplikacji MSIX](app-attach-faq.md)
