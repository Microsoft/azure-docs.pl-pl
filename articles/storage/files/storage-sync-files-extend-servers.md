---
title: 'Samouczek: rozszerzanie serwerów plików systemu Windows przy użyciu usługi Azure File Sync | Microsoft Docs'
description: Dowiedz się, jak rozszerzyć serwery plików systemu Windows przy użyciu usługi Azure File Sync — od początku do końca.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d2a0d79a5cdd53f8376c088fc986c20908575eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329464"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Samouczek: rozszerzanie serwerów plików systemu Windows przy użyciu usługi Azure File Sync

W tym artykule przedstawiono podstawowe kroki rozszerzania pojemności magazynu systemu Windows Server przy użyciu Azure File Sync. Chociaż samouczek zawiera system Windows Server jako maszynę wirtualną platformy Azure, zazwyczaj ten proces należy wykonać w przypadku serwerów lokalnych. Instrukcje wdrażania usługi Azure File Sync w środowisku znajdują się w artykule [Deploy Azure File Sync](storage-sync-files-deployment-guide.md) (Wdrażanie usługi Azure File Sync).

> [!div class="checklist"]
> * Wdrażanie usługi synchronizacji magazynu
> * Przygotowywanie systemu Windows Server do używania z usługą Azure File Sync
> * Pobieranie agenta usługi Azure File Sync
> * Rejestrowanie systemu Windows Server w usłudze synchronizacji magazynu
> * Tworzenie grupy synchronizacji i punktu końcowego w chmurze
> * Tworzenie punktu końcowego serwera

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Na potrzeby tego samouczka konieczne jest wykonanie następujących czynności przed rozpoczęciem wdrażania usługi Azure File Sync:

- Utworzenie konta usługi Azure Storage i udziału plików
- Skonfigurowanie maszyny wirtualnej z systemem Windows Server 2016 Datacenter
- Przygotowanie maszyny wirtualnej z systemem Windows Server na potrzeby usługi Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Tworzenie folderu i pliku TXT

Na komputerze lokalnym utwórz nowy folder o nazwie _FilesToSync_ i dodaj plik tekstowy o nazwie _mytestdoc.txt_. W dalszej części tego samouczka przekażesz ten plik do udziału plików.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Tworzenie udziału plików

Po wdrożeniu konta usługi Azure Storage należy utworzyć udział plików.

1. W witrynie Azure Portal wybierz polecenie **Przejdź do zasobu**.
1. Wybierz pozycję **Pliki** w okienku konta magazynu.

    ![Wybieranie pozycji Pliki](./media/storage-sync-files-extend-servers/click-files.png)

1. Wybierz pozycję **+ Udział plików**.

    ![Wybieranie przycisku Dodaj udział plików](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nazwij nowy udział plików _afsfileshare_. W polu **Przydział** wpisz cyfrę 1, a następnie wybierz polecenie **Utwórz**. Maksymalny limit przydziału to 5 TiB, ale w tym samouczku potrzebujesz tylko 1 GB.

    ![Podawanie nazwy i limitu przydziału dla nowego udziału plików](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Wybierz nowy udział plików. W lokalizacji udziału plików wybierz polecenie **Przekaż**.

    ![Przekazywanie pliku](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Przejdź do folderu _FilesToSync_, w którym został utworzony plik TXT, wybierz plik _mytestdoc.txt_ i wybierz pozycję **Przekaż**.

    ![Przeglądanie w celu wyszukania udziału plików](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Na tym etapie utworzono konto magazynu i udział plików zawierający jeden plik. Teraz wdrożysz maszynę wirtualną platformy Azure z systemem Windows Server 2016 Datacenter do reprezentowania serwera lokalnego w ramach tego samouczka.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Wdrażanie maszyny wirtualnej i dołączanie dysku danych

1. Przejdź do witryny Azure Portal i rozwiń menu po lewej stronie. W lewym górnym rogu wybierz pozycję **Utwórz zasób**.
1. W polu wyszukiwania nad listą zasobów **Azure Marketplace** wpisz nazwę **Windows Server 2016 Datacenter** i wybierz ją w wynikach. Wybierz pozycję **Utwórz**.
1. Przejdź do karty **podstawowe informacje** . W obszarze **szczegóły projektu** wybierz grupę zasobów utworzoną dla tego samouczka.

   ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. W obszarze **Szczegóły wystąpienia** podaj nazwę maszyny wirtualnej. Możesz na przykład użyć nazwy _myVM_.
1. Nie zmieniaj wartości domyślnych w polach **Region**, **Opcje dostępności**, **Obraz** i **Rozmiar**.
1. W obszarze **Konto administratora** podaj **nazwę użytkownika** i **hasło** dla maszyny wirtualnej.
1. W obszarze **Reguły portów wejściowych** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz pozycje **RDP (3389)** i **HTTP** z menu rozwijanego.

1. Przed utworzeniem maszyny wirtualnej musisz utworzyć dysk z danymi.

   1. Wybierz pozycję **Dalej: Dyski**.

      ![Dodawanie dysków danych](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Na karcie **Dyski** w obszarze **Opcje dysku** pozostaw wartości domyślne.
   1. W obszarze **DYSKI Z DANYMI** wybierz pozycję **Utwórz i dołącz nowy dysk**.

   1. Użyj ustawień domyślnych wszędzie oprócz pola **Rozmiar (GiB)**, które możesz zmienić na wartość **1 GB** na potrzeby tego samouczka.

      ![Szczegóły dysku z danymi](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

   Możesz wybrać ikonę **Powiadomienia**, aby sprawdzić **postęp wdrażania**. Utworzenie nowej maszyny wirtualnej może zająć kilka minut.

1. Po zakończeniu wdrażania maszyny wirtualnej wybierz pozycję **Przejdź do zasobu**.

   ![Przechodzenie do zasobu](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Na tym etapie utworzono nową maszynę wirtualną i dołączono dysk z danymi. Następnie możesz nawiązać połączenie z maszyną wirtualną.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

1. W witrynie Azure Portal wybierz pozycję **Połącz** na stronie właściwości maszyny wirtualnej.

   ![Nawiązywanie połączenia z maszyną wirtualną na platformie Azure z portalu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** pozostaw opcje domyślne, aby nawiązać połączenie za pomocą **adresu IP** na porcie 3389. Wybierz pozycję **Pobierz plik RDP**.

   ![Pobieranie pliku RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otwórz pobrany plik RDP i wybierz polecenie **Połącz**, gdy wyświetli się odpowiedni monit.
1. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika w formacie *localhost\nazwa_użytkownika* oraz hasło utworzone dla maszyny wirtualnej, a następnie wybierz pozycję **OK**.

   ![Więcej opcji](./media/storage-sync-files-extend-servers/local-host2.png)

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz opcję **tak** lub **Kontynuuj** , aby utworzyć połączenie.

### <a name="prepare-the-windows-server"></a>Przygotowywanie systemu Windows Server

W przypadku systemu Windows Server 2016 Datacenter wyłącz ustawienie Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer. Ten krok jest wymagany tylko w przypadku początkowej rejestracji serwera. Tę pozycję można włączyć ponownie po zarejestrowaniu serwera.

Na maszynie wirtualnej z systemem Windows Server 2016 Datacenter automatycznie otwiera się Menedżer serwera.  Jeśli Menedżer serwera nie jest domyślnie otwarty, wyszukaj go w menu Start.

1. W **Menedżerze serwera** wybierz pozycję **Serwer lokalny**.

   ![Pozycja „Serwer lokalny” po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. W okienku **Właściwości** wybierz link **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  

    ![Okienko „Konfiguracja zwiększonych zabezpieczeń” w interfejsie użytkownika Menedżera serwera](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. W oknie dialogowym **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** wybierz pozycję **Wyłączono** dla pozycji **Administratorzy** i **Użytkownicy**.

    ![Okno podręczne „Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer” z wybraną pozycją „Wyłączono”](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Teraz można dodać dysk z danymi do maszyny wirtualnej.

### <a name="add-the-data-disk"></a>Dodawanie dysku z danymi

1. Na maszynie wirtualnej z systemem **Windows Server 2016 Datacenter** wybierz kolejno pozycje **Pliki i usługi magazynowania** > **Wolumin** > **Dyski**.

    ![Dysk danych](media/storage-sync-files-extend-servers/your-disk.png)

1. Kliknij prawym przyciskiem myszy dysk o pojemności 1 GB o nazwie **Msft Virtual Disk** i wybierz pozycję **Nowy wolumin**.
1. Wykonaj kroki kreatora. Użyj ustawień domyślnych i zapisz przypisaną literę dysku.
1. Wybierz przycisk **Utwórz**.
1. Wybierz pozycję **Zamknij**.

   Na tym etapie przeniesiono dysk do trybu online i utworzono wolumin. Otwórz Eksploratora plików na maszynie wirtualnej z systemem Windows Server, aby potwierdzić obecność dodanego przed chwilą dysku z danymi.

1. W Eksploratorze plików na maszynie wirtualnej rozwiń pozycję **Ten komputer** i otwórz nowy dysk. W tym przykładzie jest to dysk F:.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Nowy**  >  **folder**. Nadaj folderowi nazwę _FilesToSync_.
1. Otwórz folder **FilesToSync**.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Nowy**  >  **dokument tekstowy**. Nadaj plikowi tekstowemu nazwę _MyTestFile_.

    ![Dodawanie nowego pliku tekstowego](media/storage-sync-files-extend-servers/new-file.png)

1. Zamknij **Eksploratora plików** i **Menedżera serwera**.

### <a name="download-the-azure-powershell-module"></a>Pobieranie modułu programu Azure PowerShell

Następnie na maszynie wirtualnej z systemem Windows Server 2016 Datacenter zainstaluj moduł programu Azure PowerShell na serwerze.

1. Na maszynie wirtualnej otwórz okno programu PowerShell z podwyższonym poziomem uprawnień.
1. Uruchom następujące polecenie:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Jeśli masz wersję pakietu NuGet starszą niż 2.8.5.201, zostanie wyświetlony monit o pobranie i zainstalowanie najnowszej wersji pakietu NuGet.

   Galeria programu PowerShell domyślnie nie jest skonfigurowana jako zaufane repozytorium modułu PowerShellGet. Po pierwszym użyciu Galerii programu PowerShell zostanie wyświetlony następujący komunikat:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Odpowiedź **tak** lub **tak na wszystko** , aby kontynuować instalację.

Moduł `Az` to zbiorczy moduł poleceń cmdlet programu Azure PowerShell. Po jego zainstalowaniu są pobierane wszystkie dostępne moduły usługi Azure Resource Manager i są udostępniane do użycia ich polecenia cmdlet.

W tym momencie skonfigurowano środowisko na potrzeby tego samouczka. Możesz teraz wdrożyć usługę synchronizacji magazynu.

## <a name="deploy-the-service"></a>Wdrażanie usługi

Aby wdrożyć usługę Azure File Sync, zacznij od umieszczenia zasobu **Usługi synchronizacji magazynu** w grupie zasobów dla wybranej subskrypcji. Usługa synchronizacji magazynu dziedziczy uprawnienia dostępu z subskrypcji i grupy zasobów.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**, a następnie wyszukaj usługę **Azure File Sync**.
1. W wynikach wyszukiwania wybierz usługę **Azure File Sync**.
1. Wybierz pozycję **Utwórz**, aby otworzyć kartę **Wdrażanie synchronizacji magazynu**.

   ![Wdrażanie synchronizacji magazynu](media/storage-sync-files-extend-servers/afs-info.png)

   W otwartym okienku wprowadź następujące informacje:

   | Wartość | Opis |
   | ----- | ----- |
   | **Nazwa** | Unikatowa nazwa (na subskrypcję) dla usługi synchronizacji magazynu.<br><br>W tym samouczku użyj nazwy _afssyncservice02_. |
   | **Subskrypcja** | Subskrypcja platformy Azure używana na potrzeby tego samouczka. |
   | **Grupa zasobów** | Grupa zasobów, która zawiera usługę synchronizacji magazynu.<br><br>W tym samouczku użyj grupy zasobów _afsresgroup101918_. |
   | **Lokalizacja** | East US |

1. Po zakończeniu wybierz pozycję **Utwórz** w celu wdrożenia **usługi synchronizacji magazynu**.
1. Wybierz kartę **Powiadomienia**, a następnie pozycję **Przejdź do zasobu**.

## <a name="install-the-agent"></a>Instalowanie agenta

Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure.

1. Na maszynie wirtualnej z systemem **Windows Server 2016 Datacenter** otwórz program **Internet Explorer**.
1. Przejdź do [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Przewiń w dół do sekcji **Agent usługi Azure File Sync**, a następnie wybierz pozycję **Pobierz**.

   ![Pobieranie agenta synchronizacji](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaznacz pole wyboru pliku **StorageSyncAgent_V3_WS2016.EXE** i wybierz pozycję **Dalej**.

   ![Wybieranie agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. Zaznacz opcję **Zezwalaj, gdy**  >  **uruchomisz** polecenie  >  **Otwórz**.
1. Jeśli okno programu PowerShell nie zostało jeszcze zamknięte, wykonaj tę czynność.
1. Zaakceptuj wartości domyślne w **Kreatorze instalacji agenta synchronizacji magazynu**.
1. Wybierz pozycję **Zainstaluj**.
1. Wybierz pozycję **Zakończ**.

Wdrożono usługę synchronizacji z platformą Azure i zainstalowano agenta na maszynie wirtualnej z systemem Windows Server 2016 Datacenter. Teraz należy zarejestrować maszynę wirtualną w usłudze synchronizacji magazynu.

## <a name="register-windows-server"></a>Rejestrowanie systemu Windows Server

Zarejestrowanie systemu Windows Server w usłudze synchronizacji magazynu powoduje ustanowienie relacji zaufania między serwerem (lub klastrem) oraz usługą synchronizacji magazynu. Serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu. Można go synchronizować z innymi serwerami i udziałami plików platformy Azure skojarzonymi z tą usługą synchronizacji magazynu.

Interfejs użytkownika rejestracji serwera powinien zostać otwarty automatycznie po zainstalowaniu agenta usługi Azure File Sync. Jeśli tak się nie stanie, można otworzyć go ręcznie z lokalizacji pliku: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Po otwarciu interfejsu użytkownika rejestracji serwera na maszynie wirtualnej wybierz przycisk **OK**.
1. Wybierz pozycję **Zaloguj się**, aby rozpocząć.
1. Zaloguj się przy użyciu poświadczeń konta platformy Azure, a następnie wybierz pozycję **Zaloguj się**.
1. Podaj następujące informacje:

   ![Zrzut ekranu przedstawiający interfejs użytkownika rejestracji serwera](media/storage-sync-files-extend-servers/signin.png)

   | Wartość | Opis |
   | ----- | ----- |
   | **Subskrypcja platformy Azure** | Subskrypcja, która zawiera usługę synchronizacji magazynu na potrzeby tego samouczka. |
   | **Grupa zasobów** | Grupa zasobów, która zawiera usługę synchronizacji magazynu. W tym samouczku użyj grupy zasobów _afsresgroup101918_. |
   | **Usługa synchronizacji magazynu** | Nazwa usługi synchronizacji magazynu. W tym samouczku użyj nazwy _afssyncservice02_. |

1. Wybierz pozycję **Zarejestruj**, aby ukończyć rejestrację serwera.
1. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowanie. Zaloguj się i wybierz przycisk **Dalej**.
1. Wybierz przycisk **OK**.

## <a name="create-a-sync-group"></a>Tworzenie grupy synchronizacji

Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Grupa synchronizacji musi zawierać jeden punkt końcowy w chmurze, który reprezentuje udział plików platformy Azure. Grupa synchronizacji musi również zawierać co najmniej jeden punkt końcowy serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanym serwerze. Aby utworzyć grupę synchronizacji:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **+ Grupa synchronizacji** z poziomu usługi synchronizacji magazynu. W tym samouczku użyj nazwy *afssyncservice02*.

   ![Tworzenie nowej grupy synchronizacji w witrynie Azure Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

   | Wartość | Opis |
   | ----- | ----- |
   | **Nazwa grupy synchronizacji** | Ta nazwa musi być unikatowa w obrębie usługi synchronizacji magazynu, ale może być to dowolna nazwa logiczna z Twojego punktu widzenia. W tym samouczku użyj nazwy *afssyncgroup*.|
   | **Subskrypcja** | Subskrypcja, w której wdrożono usługę synchronizacji magazynu na potrzeby tego samouczka. |
   | **Konto magazynu** | Wybierz pozycję **Wybierz konto magazynu**. W wyświetlonym okienku wybierz konto magazynu, które zawiera utworzony udział plików platformy Azure. Na potrzeby tego samouczka użyj nazwy *afsstoracct101918*. |
   | **Udział plików platformy Azure** | Nazwa utworzonego udziału plików platformy Azure. Na potrzeby tego samouczka użyj nazwy *afsfileshare*. |

1. Wybierz przycisk **Utwórz**.

Jeśli wybierzesz grupę synchronizacji, zobaczysz, że masz teraz jeden **punkt końcowy w chmurze**.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera

Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze. Może być to na przykład folder w woluminie serwera. Aby dodać punkt końcowy serwera:

1. Wybierz nowo utworzoną grupę synchronizacji, a następnie wybierz pozycję **Dodaj punkt końcowy serwera**.

   ![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. W okienku **Dodawanie punktu końcowego serwera** wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

   | Wartość | Opis |
   | ----- | ----- |
   | **Zarejestrowany serwer** | Nazwa utworzonego serwera. Na potrzeby tego samouczka użyj nazwy *afsvm101918*. |
   | **Ścieżka** | Ścieżka systemu Windows Server do utworzonego dysku. Na potrzeby tego samouczka użyj nazwy *f:\filestosync*. |
   | **Obsługa warstw w chmurze** | Pozostaw tę funkcję wyłączoną na potrzeby tego samouczka. |
   | **Wolne miejsce w woluminie** | Pozostaw to pole puste na potrzeby tego samouczka. |

1. Wybierz przycisk **Utwórz**.

Pliki zostały teraz zsynchronizowane między udziałem plików platformy Azure i systemem Windows Server.

![Pomyślnie zsynchronizowana usługa Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić zasoby utworzone w tym samouczku, najpierw usuń punkty końcowe z usługi synchronizacji magazynu. Następnie Wyrejestruj serwer z usługą synchronizacji magazynu, Usuń grupy synchronizacji i Usuń usługę synchronizacji.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowe kroki umożliwiające zwiększenie pojemności magazynu systemu Windows Server przy użyciu Azure File Sync. Dokładniejsze Omówienie wdrażania Azure File Sync można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Planowanie wdrażania usługi Azure File Sync](./storage-sync-files-planning.md)
