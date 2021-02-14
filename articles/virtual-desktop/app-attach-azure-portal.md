---
title: Windows Virtual Desktop MSIX — wersja zapoznawcza portalu — Azure
description: Jak skonfigurować dołączenie aplikacji MSIX dla pulpitu wirtualnego systemu Windows przy użyciu Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 19ce054ce29b744f900676ecf4e55af3487d9891
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373474"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Konfigurowanie dołączania aplikacji MSIX przy użyciu witryny Azure Portal

> [!IMPORTANT]
> Dołączenie do aplikacji MSIX jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano sposób konfigurowania dołączania aplikacji MSIX (wersja zapoznawcza) w środowisku pulpitu wirtualnego systemu Windows.

## <a name="requirements"></a>Wymagania

>[!IMPORTANT]
>Przed rozpoczęciem upewnij się, że wypełniasz i prześlesz [ten formularz](https://aka.ms/enablemsixappattach) , aby umożliwić dołączenie aplikacji MSIX do subskrypcji. Jeśli nie masz zatwierdzonego żądania, dołączenie do aplikacji MSIX nie będzie działało. Zatwierdzenie żądań może potrwać do 24 godzin w dniach roboczych. Po zaakceptowaniu i zakończeniu żądania otrzymasz wiadomość e-mail.

Oto, co jest potrzebne do skonfigurowania dołączania aplikacji MSIX:

- Działające wdrożenie pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, jak wdrożyć pulpit wirtualny systemu Windows (klasyczny), zobacz [Tworzenie dzierżawy w systemie Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Aby dowiedzieć się, jak wdrożyć pulpit wirtualny systemu Windows z integracją Azure Resource Manager, zobacz [Tworzenie puli hostów przy użyciu Azure Portal](./create-host-pools-azure-marketplace.md).
- Pula hostów pulpitu wirtualnego systemu Windows z co najmniej jednym aktywnym hostem sesji.
- Ta Pula hostów musi znajdować się w środowisku walidacji. 
- Narzędzie MSIX pakowanie.
- MSIX — spakowana aplikacja rozwinięta do obrazu MSIX, który jest przekazywany do udziału plików.
- Udział plików w ramach wdrożenia pulpitu wirtualnego systemu Windows, w którym będzie przechowywany pakiet MSIX.
- Udział plików, do którego został przekazany obraz MSIX, musi być również dostępny dla wszystkich maszyn wirtualnych w puli hostów. Użytkownicy będą musieli mieć uprawnienia tylko do odczytu w celu uzyskania dostępu do obrazu.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Wyłącz aktualizacje automatyczne dla aplikacji do dołączania aplikacji MSIX

Przed rozpoczęciem należy wyłączyć aktualizacje automatyczne dla aplikacji MSIX. Aby wyłączyć aktualizacje automatyczne, należy uruchomić następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień:

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
>Zalecamy ponowne uruchomienie maszyny wirtualnej po włączeniu funkcji Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Konfigurowanie interfejsu zarządzania dołączaniem aplikacji MSIX

Następnie należy pobrać i skonfigurować interfejs zarządzania dołączania aplikacji MSIX dla Azure Portal.

Aby skonfigurować interfejs zarządzania:

1. [Otwórz Azure Portal](https://portal.azure.com).
2. Jeśli zostanie wyświetlony monit z pytaniem, czy Rozważmy wiarygodność rozszerzenia, wybierz pozycję **Zezwalaj**.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający okno niezaufane rozszerzenia. Wartość "Zezwalaj" jest wyróżniona kolorem czerwonym.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Dodawanie obrazu MSIX do puli hostów

Następnie musisz dodać obraz MSIX do puli hostów.

Aby dodać obraz MSIX:

1. Otwórz witrynę Azure Portal.

2. Wprowadź **Windows Virtual Desktop** na pasku wyszukiwania, a następnie wybierz nazwę usługi.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający użytkownika z menu rozwijanego pasek wyszukiwania w Azure Portal. "Pulpit wirtualny systemu Windows" jest wyróżniony kolorem czerwonym.](media/find-and-select.png)

3. Wybierz pulę hostów, w której planujesz umieścić aplikacje MSIX.

4. Wybierz pozycję **pakiety MSIX** , aby otworzyć siatkę danych ze wszystkimi **pakietami MSIX** aktualnie dodawanymi do puli hostów.

5. Wybierz pozycję **+ Dodaj** , aby otworzyć kartę **Dodawanie pakietu MSIX** .

6. Na karcie **Dodaj pakiet MSIX** wprowadź następujące wartości:

      - Dla **ścieżki obrazu MSIX** Wprowadź prawidłową ścieżkę UNC WSKAZUJĄCĄ obraz MSIX w udziale plików. (Na przykład `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Gdy skończysz, wybierz pozycję **Dodaj** , aby PRZEJRZEĆ kontener MSIX, aby sprawdzić, czy ścieżka jest prawidłowa.

      - W przypadku **pakietu MSIX** wybierz odpowiednią nazwę pakietu MSIX z menu rozwijanego. To menu będzie wypełniane tylko wtedy, gdy w **ścieżce obrazu MSIX** wprowadzono prawidłową ścieżkę obrazu.

      - W przypadku **aplikacji pakietu** upewnij się, że lista zawiera wszystkie aplikacje MSIX, które mają być dostępne dla użytkowników w pakiecie MSIX.

      - Opcjonalnie wprowadź **nazwę wyświetlaną** , jeśli chcesz, aby pakiet był bardziej przyjazny dla użytkownika we wdrożeniach użytkowników.

      - Upewnij się, że **wersja** ma prawidłowy numer wersji.

      - Wybierz **Typ rejestracji** , którego chcesz użyć. Którego użycia zależy od Twoich potrzeb:

    - **Rejestracja na żądanie** opóźnia pełną rejestrację aplikacji MSIX, dopóki użytkownik nie uruchomi aplikacji. Jest to typ rejestracji, którego zalecamy używać.

    - **Zablokuj blokowanie** tylko rejestrów, gdy użytkownik loguje się. Nie zalecamy tego typu, ponieważ może to prowadzić do dłuższego czasu logowania użytkowników.

7.  W polu **stan** wybierz preferowany stan.
    -  Stan **aktywny** umożliwia użytkownikom współdziałanie z pakietem.
    -  Stan **nieaktywny** powoduje, że pulpit wirtualny systemu Windows ignoruje pakiet i nie dostarcza go użytkownikom.

8. Gdy skończysz, wybierz pozycję **Dodaj**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publikowanie aplikacji MSIX w grupie aplikacji

Następnie musisz opublikować aplikacje w pakiecie. Należy to zrobić dla grup aplikacji komputerów stacjonarnych i aplikacji zdalnych.

Jeśli masz już obraz MSIX, przejdź dalej, aby [opublikować aplikacje MSIX w grupie aplikacji](#publish-msix-apps-to-an-app-group). Jeśli chcesz przetestować starsze aplikacje, postępuj zgodnie z instrukcjami w temacie [Tworzenie pakietu MSIX z poziomu Instalatora pulpitu na maszynie wirtualnej](/windows/msix/packaging-tool/create-app-package-msi-vm/) w celu przekonwertowania starszej aplikacji na pakiet MSIX.

Aby opublikować aplikacje:

1. W dostawcy zasobów pulpitu wirtualnego systemu Windows wybierz kartę **grupy aplikacji** .

2. Wybierz grupę aplikacji, w której chcesz opublikować aplikacje.

   >[!NOTE]
   >Aplikacje MSIX mogą być dostarczane z aplikacją MSIX do dołączenia do aplikacji zdalnych i grup aplikacji klasycznych

3. Gdy jesteś w grupie aplikacji, wybierz kartę **aplikacje** . Siatka **aplikacje** będzie wyświetlać wszystkie istniejące aplikacje w grupie aplikacji.

4. Wybierz pozycję **+ Dodaj** , aby otworzyć kartę **Dodawanie aplikacji** .

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający użytkownika, wybierając pozycję + Dodaj, aby otworzyć kartę Dodawanie aplikacji](media/select-add.png)

5. W polu **Źródło aplikacji** wybierz źródło aplikacji.
    - Jeśli używasz grupy aplikacji klasycznej, wybierz pozycję **pakiet MSIX**.
      
      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu klienta z menu rozwijanego Źródło aplikacji, wybierając pozycję pakiet MSIX. Pakiet MSIX został wyróżniony kolorem czerwonym.](media/select-source.png)
    
    - Jeśli używasz zdalnej grupy aplikacji, wybierz jedną z następujących opcji:
        
        - Menu Start
        - Ścieżka aplikacji
        - Pakiet MSIX

    - W polu **Nazwa aplikacji** Wprowadź opisową nazwę aplikacji.

    Można również skonfigurować następujące funkcje opcjonalne:
   
    - W polu **Nazwa wyświetlana** wprowadź nową nazwę pakietu, która będzie widoczna dla użytkowników.

    - W polu **Opis** wprowadź krótki opis pakietu aplikacji.

    - Jeśli używasz zdalnej grupy aplikacji, możesz również skonfigurować następujące opcje:

        - **Ścieżka ikony**
        - **Indeks ikony**
        - **Pokaż w źródle danych sieci Web**

6. Po zakończeniu wybierz pozycję **Zapisz**.

>[!NOTE]
>Gdy użytkownik jest przypisany do grupy aplikacji zdalnych i grupy aplikacji klasycznych z tej samej puli hostów, w źródle danych zostanie wyświetlona Grupa aplikacji klasycznych.

## <a name="assign-a-user-to-an-app-group"></a>Przypisywanie użytkownika do grupy aplikacji

Po przypisaniu aplikacji MSIX do grupy aplikacji należy udzielić użytkownikom dostępu do nich. Dostęp można przypisywać przez dodawanie użytkowników lub grup użytkowników do grupy aplikacji z opublikowanymi aplikacjami MSIX. Postępuj zgodnie z instrukcjami w temacie [Zarządzanie grupami aplikacji przy użyciu Azure Portal,](manage-app-groups.md) aby przypisać użytkowników do grupy aplikacji.

>[!NOTE]
>Aplikacje zdalne dołączania aplikacji MSIX mogą zniknąć z kanału informacyjnego w przypadku testowania aplikacji zdalnych w trakcie publicznej wersji zapoznawczej. Aplikacje nie są wyświetlane, ponieważ pula hostów używana w środowisku ewaluacyjnym jest obsługiwana przez brokera usług pulpitu zdalnego w środowisku produkcyjnym. Ponieważ Broker usług pulpitu zdalnego w środowisku produkcyjnym nie rejestruje obecności aplikacji MSIX dołączania aplikacji zdalnych, aplikacje nie będą wyświetlane w kanale informacyjnym.

## <a name="change-msix-package-state"></a>Zmień stan pakietu MSIX

Następnie należy zmienić stan pakietu MSIX na **aktywny** lub **nieaktywny**, w zależności od tego, co chcesz zrobić z pakietem. Pakiety aktywne to pakiety, z którymi użytkownicy mogą korzystać po ich opublikowaniu. Nieaktywne pakiety są ignorowane przez pulpit wirtualny systemu Windows, przez co użytkownicy nie mogą korzystać z aplikacji w ramach programu.

### <a name="change-state-with-the-applications-list"></a>Zmień stan na listę aplikacji

Aby zmienić stan pakietu na listę aplikacji:

1. Przejdź do puli hostów i wybierz pozycję **pakiety MSIX**. Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz pakiety MSIX, których Stany chcesz zmienić, a następnie wybierz pozycję **Zmień stan**.

### <a name="change-state-with-update-package"></a>Zmień stan z pakietem aktualizacji

Aby zmienić stan pakietu z pakietem aktualizacji:

1. Przejdź do puli hostów i wybierz pozycję **pakiety MSIX**. Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz nazwę pakietu, którego stan chcesz zmienić z listy pakietów MSIX. Spowoduje to otwarcie karty **Aktualizowanie pakietu** .

3. Przełącz przełącznik **stanu** do **nieaktywnego** lub **aktywnego**, a następnie wybierz pozycję **Zapisz.**

## <a name="change-msix-package-registration-type"></a>Zmień typ rejestracji pakietu MSIX

Aby zmienić typ rejestracji pakietu:

1. Wybierz pozycję **pakiety MSIX**. Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz **nazwę pakietu w** **siatce pakietów MSIX** spowoduje to otwarcie bloku w celu zaktualizowania pakietu.

3. Przełącz **Typ rejestracji** za pomocą przycisku **Zablokuj na żądanie/Zaloguj** się zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz.**

## <a name="remove-an-msix-package"></a>Usuń pakiet MSIX

Aby usunąć pakiet MSIX z puli hostów:

1. Wybierz pozycję **pakiety MSIX**.  Powinna zostać wyświetlona lista wszystkich istniejących pakietów MSIX w puli hostów.

2. Wybierz wielokropek po prawej stronie nazwę pakietu, który chcesz usunąć, a następnie wybierz pozycję **Usuń**.

## <a name="remove-msix-apps"></a>Usuń aplikacje MSIX

Aby usunąć pojedyncze aplikacje MSIX z pakietu:

1. Przejdź do puli hostów i wybierz pozycję **grupy aplikacji**.

2. Wybierz grupę aplikacji, z której chcesz usunąć aplikacje MSIX.

3. Otwórz kartę **aplikacje** .

4. Wybierz aplikację, którą chcesz usunąć, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszymi pytaniami dotyczącymi tej funkcji w [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Poniżej znajdują się inne artykuły, które mogą okazać się przydatne:

- [Dołączanie słownika aplikacji MSIX](app-attach-glossary.md)
- [Załączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)
