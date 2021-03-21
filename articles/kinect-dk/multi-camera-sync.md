---
title: Synchronizowanie wielu urządzeń z usługą Azure urządzenia Kinect DK
description: W tym artykule przedstawiono zalety synchronizacji przez wiele urządzeń oraz sposób konfigurowania urządzeń do synchronizacji.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, urządzenia Kinect, specyfikacje, sprzęt, DK, możliwości, Głębokość, kolor, RGB, IMU, tablica, Głębokość, wiele, synchronizacja
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039958"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Synchronizowanie wielu urządzeń z usługą Azure urządzenia Kinect DK

Każde urządzenie z systemem Azure urządzenia Kinect DK obejmuje porty synchronizacji 3,5 mm (**Synchronizacja** i **Synchronizacja**), których można użyć do łączenia wielu urządzeń ze sobą. Po podłączeniu urządzeń oprogramowanie może koordynować czas wyzwalacza między nimi. 

W tym artykule opisano sposób nawiązywania połączenia z urządzeniami i ich synchronizowania.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Zalety korzystania z wielu urządzeń z usługą Azure urządzenia Kinect DK

Istnieje wiele powodów, dla których można korzystać z wielu urządzeń usługi Azure urządzenia Kinect DK, w tym następujących:

- Wypełnij pole Occlusions. Mimo że przekształcenia danych na platformie Azure urządzenia Kinect są wytwarzane na jednym obrazie, dwie aparaty (głębokości i RGB) są w rzeczywistości niewielką odległością od siebie. Przesunięcie sprawia, że Occlusions to możliwe. Zamknięcia występuje, gdy obiekt pierwszego planu blokuje widok części obiektu tła dla jednej z dwóch kamer na urządzeniu. W wyniku obrazu koloru obiekt pierwszego planu wydaje się rzutować cień obiektu tła.  
   Na przykład na poniższym diagramie aparat po lewej stronie widzi szary piksel "P2". Jednak biały obiekt pierwszego planu blokuje lampę podczerwieni po prawej stronie. Kamerę po prawej stronie nie ma danych dla "P2".  
   ![Diagram przedstawia dwie kamery kierowane w tym samym momencie, gdy jeden z nich jest zablokowany.](./media/occlusion.png)  
   Dodatkowe synchronizowane urządzenia mogą udostępniać dane zamknięte.
- Skanuj obiekty w trzech wymiarach.
- Zwiększ obowiązującą szybkość klatek do wartości większej niż 30 klatek na sekundę (FPS).
- Przechwyć wiele obrazów kolorów 4K z tej samej sceny, wszystkie wyrównane w ciągu 100 mikrosekund ( &mu; s) środka ekspozycji.
- Zwiększ pokrycie kamery w obszarze.

## <a name="plan-your-multi-device-configuration"></a>Planowanie konfiguracji wielourządzenie

Przed rozpoczęciem upewnij się, że przeglądasz [wymagania dotyczące sprzętu platformy Azure urządzenia Kinect DK](hardware-specification.md) i [aparatu głębokości Azure urządzenia Kinect DK](depth-camera.md).

> [!NOTE]  
> Usuń zewnętrzną okładki z tworzywa sztucznego, aby uwidocznić i zsynchronizować gniazda.

### <a name="select-a-device-configuration"></a>Wybierz konfigurację urządzenia

W celu skonfigurowania urządzenia można użyć jednej z następujących metod:

- **Konfiguracja łańcucha** łańcuchowego. Zsynchronizuj jedno urządzenie główne i maksymalnie osiem urządzeń podrzędnych.  
   ![Diagram przedstawiający sposób łączenia urządzeń z systemem Azure urządzenia Kinect DK w konfiguracji łańcucha łańcuchowego.](./media/multicam-sync-daisychain.png)
- **Konfiguracja gwiazdy**. Zsynchronizuj jedno urządzenie główne i maksymalnie dwa urządzenia podrzędne.  
   ![Diagram przedstawiający sposób konfigurowania wielu urządzeń z systemem Azure DK w konfiguracji gwiazdy.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Korzystanie z zewnętrznego wyzwalacza synchronizacji

W obu konfiguracjach urządzenie główne zapewnia sygnał wyzwalający dla urządzeń podrzędnych. Można jednak użyć niestandardowego źródła zewnętrznego dla wyzwalacza synchronizacji. Można na przykład użyć tej opcji do synchronizowania przechwytywania obrazu z innym sprzętem. W konfiguracji łańcucha łańcuchowego lub w konfiguracji gwiazdy zewnętrzne źródło wyzwalacza łączy się z urządzeniem głównym.

Źródło zewnętrznego wyzwalacza musi działać w taki sam sposób, jak w przypadku urządzenia głównego. Musi dostarczyć sygnał synchronizacji o następujących cechach:

- Aktywny wysoki
- Szerokość impulsu: większa niż 8 &mu; s
- 5V CZAS WYGAŚNIĘCIA/CMOS
- Maksymalna pojemność pojazdu: nie mniejsza niż 8 milliamps (mA)
- Obsługa częstotliwości: dokładnie 30 FPS, 15 FPS i 5 FPS (częstotliwość sygnału głównego pionie aparatu fotograficznego)

Źródło wyzwalacza musi dostarczyć sygnał do synchronizacji urządzenia głównego **w** porcie przy użyciu kabla audio 3,5 mm. Możesz użyć stereo lub kabla mono. Na platformie Azure urządzenia Kinect DK szorty wszystkie rękawy i pierścienie łącznika kabla audio, a także Zanotuj ich przyczyny. Jak pokazano na poniższym diagramie, urządzenie odbiera sygnał synchronizacji tylko z etykietki łącznika.

![Konfiguracje kabli dla zewnętrznego sygnału wyzwalacza](./media/resources/camera-trigger-signal.jpg)

Aby uzyskać więcej informacji na temat pracy z urządzeniem zewnętrznym, zobacz [Korzystanie z usługi Azure urządzenia Kinect Recorder z zewnętrznymi urządzeniami synchronizowanymi](record-external-synchronized-units.md)

> [!NOTE]  
> Synchronizacja jest pionie dla aparatu RGB. Sygnatury czasowe dla wszystkich urządzeń są ustawione na zero i liczą w górę. Firma Microsoft nie charakteryzuje minimalnej i maksymalnej szerokości pulsu synchronizacji i zaleca naśladując pulsu wygenerowanego przez synchronizację z platformy Azure urządzenia Kinect DK.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Planowanie ustawień aparatu i konfiguracji oprogramowania

Aby uzyskać informacje na temat sposobu konfigurowania oprogramowania w celu kontrolowania kamer i używania danych obrazu, zobacz [zestaw SDK czujnika usługi Azure urządzenia Kinect](about-sensor-sdk.md).

W tej sekcji omówiono kilka czynników wpływających na zsynchronizowane urządzenia (ale nie pojedyncze urządzenia). Twoje oprogramowanie powinno uwzględniać te czynniki.

#### <a name="exposure-considerations"></a>Zagadnienia dotyczące ekspozycji
Jeśli chcesz kontrolować dokładny czas każdego urządzenia, zalecamy użycie ustawienia ręcznego ekspozycji. W przypadku ustawienia Automatyczna ekspozycja każdy aparat kolorów może dynamicznie zmienić rzeczywiste narażenie. Ponieważ ekspozycja wpływa na chronometraż, takie zmiany szybko wypychają kamery z synchronizacji.

W pętli przechwytywania obrazu należy unikać wielokrotnego ustawiania tego samego ustawienia ekspozycji. Wywołaj interfejs API tylko raz, gdy jest to konieczne.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Unikanie zakłóceń między wieloma kamerami głębokości

W przypadku wielu kamer o większej głębokości obrazy nakładające się pola widoku, każdy aparat musi mieć obraz własnej skojarzonej drukarki laserowej. Aby zapobiec zakłócaniu przez lasery między sobą, przechwytywane aparaty powinny być przesunięte od siebie o 160 μs lub więcej.

Dla każdego przechwycenia aparatu fotograficznego, Laser włącza dziewięć razy i jest aktywny dla tylko 125 &mu; s za każdym razem. Laser jest bezczynny dla 14505 &mu; s lub 23905 &mu; s, w zależności od trybu operacji. To zachowanie oznacza, że punkt początkowy dla obliczenia przesunięcia wynosi 125 &mu; s.

Ponadto różnice między zegarem aparatu a zegarem oprogramowania układowego urządzenia zwiększają minimalne przesunięcie do 160 &mu; s. Aby obliczyć dokładniejsze przesunięcie konfiguracji, zanotuj tryb głębokości, którego używasz, i zapoznaj się z [tabelą chronometrażu nieprzetworzonego czujnika głębokości](hardware-specification.md#depth-sensor-raw-timing). Korzystając z danych z tej tabeli, można obliczyć minimalne przesunięcie (czas ekspozycji każdego aparatu) za pomocą następującego równania:

> *Czas ekspozycji* = (wskaźnik pulsu *IR* &times; ) + (czas *bezczynności* &times; )

W przypadku korzystania z przesunięcia 160 &mu; s można skonfigurować maksymalnie dziewięć dodatkowych kamer głębi, aby każda laserowa była włączona, gdy inne lasery są bezczynne.

W oprogramowaniu Użyj ```depth_delay_off_color_usec``` lub, ```subordinate_delay_off_master_usec``` Aby upewnić się, że każda lasera IR jest uruchamiana we własnym &mu; oknie 160 s lub ma inne pole widoku.

> [!NOTE]  
> Rzeczywista szerokość impulsu to 125us, jednak 160us stan dla niektórych Leeway. Przyjmując NFOV UNBINNED na przykład, każdy impuls 125us jest przystępujący do bezczynności 1450. Sumowanie tych elementów — (9 x 125) + (8 x 1450) — daje czas ekspozycji dla programu 12,8 MS. Można przełączać się do tego, że 2 urządzenia mają należeć do pierwszego pulsu w pierwszej części aparatu. Opóźnienie między pierwszą i drugą kamerą może być tak małe jak 125us (szerokość impulsu), jednak zalecamy pewne Leeway to 160us. Podaną 160us można obejść okresy ekspozycji z maksymalnie 10 kamer.

## <a name="prepare-your-devices-and-other-hardware"></a>Przygotowywanie urządzeń i innego sprzętu

Oprócz wielu urządzeń z systemem Azure urządzenia Kinect DK może być konieczne uzyskanie dodatkowych komputerów hostów i innego sprzętu w celu obsługi konfiguracji, którą chcesz skompilować. Skorzystaj z informacji w tej sekcji, aby upewnić się, że wszystkie urządzenia i sprzęt są gotowe przed rozpoczęciem konfigurowania.

### <a name="azure-kinect-dk-devices"></a>Urządzenia z platformą Azure urządzenia Kinect DK

Dla każdego urządzenia z systemem Azure urządzenia Kinect DK, które chcesz synchronizować, wykonaj następujące czynności:

- Upewnij się, że na urządzeniu zainstalowano najnowsze oprogramowanie układowe. Aby uzyskać więcej informacji o sposobach aktualizowania urządzeń, przejdź do pozycji [Aktualizuj oprogramowanie układowe usługi Azure urządzenia Kinect DK](update-device-firmware.md). 
- Usuń pokrycie urządzenia, aby odsłonić porty synchronizacji.
- Zwróć uwagę na numer seryjny każdego z urządzeń. Ten numer zostanie użyty później w procesie instalacji.

### <a name="host-computers"></a>Komputery-hosty

Zazwyczaj każda usługa Azure urządzenia Kinect DK używa własnego komputera hosta. Możesz użyć dedykowanego kontrolera hosta, w zależności od sposobu korzystania z urządzenia i ilości danych przesyłanych przez połączenie USB. 

Upewnij się, że na każdym komputerze hosta jest zainstalowany zestaw SDK czujnika usługi Azure urządzenia Kinect. Aby uzyskać więcej informacji na temat sposobu instalowania zestawu SDK czujnika, przejdź do [przewodnika Szybki Start: Konfigurowanie platformy Azure urządzenia Kinect DK](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Komputery z systemem Linux: pamięć USB na Ubuntu

Domyślnie komputery hosta z systemem Linux przydzielą kontrolerowi USB tylko 16 MB pamięci jądra do obsługi transferów USB. Ta kwota jest zwykle wystarczająca do obsługi pojedynczej usługi Azure urządzenia Kinect DK. Aby jednak obsługiwać wiele urządzeń, kontroler USB musi mieć więcej pamięci. Aby zwiększyć ilość pamięci, wykonaj następujące kroki:

1. Edytuj/**etc/default/grub**.
1. Znajdź następujący wiersz:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Zastąp go przy użyciu tego wiersza:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Te polecenia ustawiają pamięć USB na 32 MB. Jest to przykład ustawienia dwukrotne wartość domyślna. Można ustawić znacznie większą wartość, zgodnie z potrzebami rozwiązania.
1. Uruchom **sudo Update-grub**.
1. Uruchom ponownie komputer.

### <a name="cables"></a>Kable

Aby połączyć urządzenia ze sobą i z komputerami hosta, należy użyć kabli 3,5-mm-do-męskich (nazywanych również kablem audio 3,5-mm). Kable powinny mieć mniej niż 10 metrów i mogą być stereo lub mono.

Wymagana liczba kabli zależy od liczby używanych urządzeń, a także konfiguracji konkretnego urządzenia. Pole urządzenia Kinect na platformie Azure nie zawiera kabli. Należy je zakupić osobno.

Jeśli urządzenia zostaną połączone z konfiguracją gwiazdy, trzeba również mieć jeden rozdzielacz słuchawek.

## <a name="connect-your-devices"></a>Łączenie urządzeń

**Aby nawiązać połączenie z urządzeniami z systemem Azure urządzenia Kinect DK w konfiguracji łańcucha łańcuchowego**

1. Połącz wszystkie usługi Azure urządzenia Kinect DK z możliwością zarządzania.
1. Podłącz każde urządzenie do komputera hosta. 
1. Wybierz jedno urządzenie, które ma być urządzeniem głównym, a następnie podłącz kabel audio 3,5 mm do jego portu **synchronizacji out** .
1. Podłącz drugi koniec kabla do **synchronizacji w** porcie pierwszego urządzenia podrzędnego.
1. Aby podłączyć inne urządzenie, podłącz inny kabel do portu **synchronizacji out** pierwszego urządzenia podrzędnego oraz **zsynchronizuj w** porcie następnego urządzenia.
1. Powtórz poprzedni krok, dopóki wszystkie urządzenia nie zostaną połączone. Ostatnie urządzenie powinno mieć tylko jedno połączenie kablowe. Jego port **synchronizacji out** powinien być pusty.

**Aby nawiązać połączenie z urządzeniami z systemem Azure urządzenia Kinect DK w konfiguracji gwiazdy**

1. Połącz wszystkie usługi Azure urządzenia Kinect DK z możliwością zarządzania.
1. Podłącz każde urządzenie do komputera hosta. 
1. Wybierz jedno urządzenie, które ma być urządzeniem głównym, i Podłącz pojedyncze zakończenie rozdzielacza słuchawek do jego portu **synchronizacji out** .
1. Podłącz kable audio 3,5-mm do końca "podziału" rozdzielacza słuchawek.
1. Podłącz drugi koniec każdego kabla do **synchronizacji w** porcie jednego z urządzeń podrzędnych.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Sprawdź, czy urządzenia są połączone i komunikują się

Aby sprawdzić, czy urządzenia są prawidłowo połączone, użyj [usługi Azure urządzenia Kinect Viewer](azure-kinect-viewer.md). Powtórz tę procedurę w razie potrzeby, aby przetestować każde urządzenie podrzędne w połączeniu z urządzeniem głównym

> [!IMPORTANT]  
> W przypadku tej procedury należy znać numer seryjny każdej usługi Azure urządzenia Kinect DK.

1. Otwórz dwa wystąpienia usługi Azure urządzenia Kinect Viewer.
1. W obszarze **Otwórz urządzenie** wybierz numer seryjny urządzenia podrzędnego, które chcesz przetestować.  
   ![Otwórz urządzenie](./media/open-devices.png)
   > [!IMPORTANT]  
   > Aby uzyskać precyzyjne dopasowanie przechwytywania obrazu między wszystkimi urządzeniami, należy uruchomić urządzenie główne jako ostatnie.  
1. W obszarze **Synchronizacja zewnętrzna** wybierz pozycję **Sub**.  
   ![Uruchomienie podrzędnej kamery](./media/sub-device-start.png)
1.  Wybierz pozycję **Uruchom**.  
    > [!NOTE]  
    > Ponieważ jest to urządzenie podrzędne, usługa Azure urządzenia Kinect Viewer nie wyświetla obrazu po rozpoczęciu urządzenia. Obraz nie jest wyświetlany, dopóki urządzenie podrzędne nie odbierze sygnału synchronizacji z urządzenia głównego.
1. Po rozpoczęciu pracy z urządzeniem podrzędnym Użyj innego wystąpienia usługi Azure urządzenia Kinect Viewer, aby otworzyć urządzenie główne.
1. W obszarze **Synchronizacja zewnętrzna** wybierz pozycję **Master**.
1. Wybierz pozycję **Uruchom**.

Po rozpoczęciu głównego urządzenia Azure urządzenia Kinect, w obu wystąpieniach przeglądarki Azure urządzenia Kinect, powinny być wyświetlane obrazy.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Kalibrowanie urządzeń jako zestawu zsynchronizowanego

Po sprawdzeniu, czy urządzenia komunikują się prawidłowo, można je skalibrować do tworzenia obrazów w jednej domenie.

Na jednym urządzeniu, Głębokość i kamery RGB są fabrycznie skalibrowane, aby współdziałać ze sobą. Jeśli jednak wiele urządzeń musi współdziałać, muszą one być skalibrowane, aby określić sposób przekształcania obrazu z domeny aparatu, który przechwytuje go do domeny aparatu, który ma być używany do przetwarzania obrazów.

Istnieje wiele opcji dla urządzeń z wieloma kalibracją. Firma Microsoft udostępnia [przykładowy kod zielony ekranu usługi GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), który używa metody OpenCV. Plik Readme dla tego przykładowego kodu zawiera więcej szczegółów i instrukcje dotyczące kalibracji urządzeń.

Aby uzyskać więcej informacji na temat kalibracji, zobacz [Korzystanie z funkcji kalibracji usługi Azure urządzenia Kinect](use-calibration-functions.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu synchronizowanych urządzeń można również dowiedzieć się, jak korzystać z
> [!div class="nextstepaction"]
> [Interfejs API zapisywania i odtwarzania zestawu SDK czujnika usługi Azure urządzenia Kinect](record-playback-api.md)

## <a name="related-topics"></a>Powiązane tematy

- [Informacje o zestawie SDK czujnika usługi Azure urządzenia Kinect](about-sensor-sdk.md)
- [Dane techniczne sprzętu zestawu Azure Kinect DK](hardware-specification.md) 
- [Szybki Start: Konfigurowanie platformy Azure urządzenia Kinect DK](set-up-azure-kinect-dk.md) 
- [Aktualizowanie oprogramowania układowego usługi Azure urządzenia Kinect DK](update-device-firmware.md) 
- [Resetowanie zestawu Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Przeglądarka Azure urządzenia Kinect](azure-kinect-viewer.md) 
