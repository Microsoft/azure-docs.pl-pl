---
title: Interfejs użytkownika Data Manager Microsoft Azure StorSimple
description: Dowiedz się, jak za pomocą interfejsu użytkownika StorSimple Data Manager przekształcać dane znajdujące się na urządzeniach z serii StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5d6273cbef1664984d466fb15c7f02ca505b18e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184544"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Zarządzanie usługą StorSimple Data Manager w programie Azure Portal

W tym artykule wyjaśniono, jak można użyć interfejsu użytkownika StorSimple Data Manager, aby przekształcić dane znajdujące się na urządzeniach z serii StorSimple 8000. Przekształcone dane mogą być następnie używane przez inne usługi platformy Azure, takie jak Azure Media Services, Azure HDInsight, Azure Machine Learning i Azure Wyszukiwanie poznawcze.


## <a name="use-storsimple-data-transformation"></a>Używanie transformacji danych StorSimple

StorSimple Data Manager jest zasobem, w ramach którego tworzone jest wystąpienie transformacji danych. Usługa przekształcania danych umożliwia przekształcanie danych z formatu StorSimple na natywny format w obiektach Blob lub Azure Files. Aby przekształcić dane natywnego formatu StorSimple, należy określić szczegóły dotyczące urządzenia z serii StorSimple 8000 i danych interesujących, które mają być przekształcone.

### <a name="create-a-storsimple-data-manager-service"></a>Tworzenie usługi StorSimple Data Manager

Wykonaj następujące kroki, aby utworzyć usługę StorSimple Data Manager.

1. Użyj poświadczeń konta Microsoft, aby zalogować się do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij pozycję **+ Utwórz zasób** i Wyszukaj StorSimple Data Manager.

    ![Tworzenie usługi StorSimple Data Manager 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Kliknij StorSimple Data Manager a następnie kliknij przycisk **Utwórz**.
    
    ![Tworzenie usługi StorSimple Data Manager 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Dla nowej usługi Określ następujące elementy:

   1. Podaj unikatową **nazwę usługi** dla StorSimple Data Manager. To jest przyjazna nazwa, która może służyć do identyfikowania usługi. Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

   2. Wybierz **subskrypcję** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole jest wypełniane automatycznie (i nie można go wybierać), jeśli masz tylko jedną subskrypcję.

   3. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](/azure/azure-resource-manager/management/manage-resource-groups-portal).

   4. Określ **lokalizację** usługi, która przechowuje Twoje konta magazynu i usługę StorSimple Data Manager. Usługa StorSimple Menedżer urządzeń, Usługa Data Manager i skojarzone konto magazynu powinny znajdować się w obsługiwanych regionach.
    
   5. Aby uzyskać link do tej usługi na pulpicie nawigacyjnym, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
    
   6. Kliknij przycisk **Utwórz**.

      ![Tworzenie usługi StorSimple Data Manager 3](./media/storsimple-data-manager-ui/create-service-4.png)

Tworzenie usługi potrwa kilka minut. Po pomyślnym utworzeniu usługi zobaczysz powiadomienie i zostanie wyświetlona nowa usługa.

### <a name="create-a-data-transformation-job-definition"></a>Tworzenie definicji zadania transformacji danych

W ramach usługi StorSimple Data Manager należy utworzyć definicję zadania transformacji danych. Definicja zadania określa szczegóły StorSimple danych, które użytkownik chce przenieść do konta magazynu w formacie natywnym. Po utworzeniu definicji zadania można uruchomić to zadanie ponownie z innymi ustawieniami środowiska uruchomieniowego.

Wykonaj następujące kroki, aby utworzyć definicję zadania.

1. Przejdź do utworzonej usługi. Przejdź do obszaru **zarządzanie > definicjami zadań**.

2. Kliknij pozycję **+ Definicja zadania**.

    ![Kliknij pozycję + Definicja zadania](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Podaj nazwę dla definicji zadania. Nazwa może mieć długość od 3 do 63 znaków. Nazwa może zawierać wielkie i małe litery, cyfry i łączniki.

4. Określ lokalizację, w której działa zadanie. Ta lokalizacja może różnić się od lokalizacji, w której została wdrożona usługa.

5. Kliknij pozycję **Źródło** , aby określić repozytorium danych źródłowych.

    ![Konfiguruj repozytorium danych źródłowych](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Ponieważ jest to nowa usługa Data Manager, żadne repozytoria danych nie są konfigurowane. W obszarze **Konfiguruj źródło danych**Określ szczegóły urządzenia z serii StorSimple 8000 i interesujące Cię dane.

   Aby dodać StorSimple Menedżer urządzeń jako repozytorium danych, kliknij pozycję **Dodaj nowe** na liście rozwijanej repozytorium danych, a następnie kliknij pozycję **Dodaj repozytorium danych**.

    ![Dodaj nowe repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Wybierz pozycję **StorSimple 8000 Series Manager** jako typ repozytorium danych.
    
   2. Wprowadź przyjazną nazwę dla repozytorium danych źródłowych.
    
   3. Z listy rozwijanej wybierz subskrypcję skojarzoną z usługą StorSimple Menedżer urządzeń.
    
   4. Podaj nazwę Menedżer urządzeń StorSimple dla **zasobu**.

   5. Wprowadź klucz **szyfrowania danych usługi** dla usługi StorSimple Menedżer urządzeń. 

      ![Skonfiguruj repozytorium danych źródłowych 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Po zakończeniu kliknij przycisk **OK** . Spowoduje to zapisanie repozytorium danych. Ponownie Użyj tego StorSimple Menedżer urządzeń w innych definicjach zadań bez konieczności ponownego wprowadzania tych parametrów. Po kliknięciu przycisku **OK** dla nowo utworzonego repozytorium danych źródłowych do wyświetlenia na liście rozwijanej trwa kilka sekund.

7. Z listy rozwijanej dla **repozytorium danych**wybierz utworzone repozytorium danych. 

   1. Wprowadź nazwę urządzenia z serii StorSimple 8000, które zawiera interesujące Cię dane.

   2. Określ nazwę woluminu znajdującego się na urządzeniu StorSimple, które ma Twoje dane zainteresowania.

   3. W podsekcji **filtru** wprowadź katalog główny zawierający Twoje dane interesujące w formacie _\MyRootDirectory\Data_ . Litery dysków takie jak _\c: \data_ nie są obsługiwane. W tym miejscu możesz również dodać dowolne filtry plików.

   4. Usługa transformacji danych działa tylko na najnowszej migawce danych, które są wypychane do platformy Azure.

   5. Kliknij przycisk **OK**.

      ![Konfigurowanie repozytorium danych źródłowych 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Następnie należy skonfigurować docelowe repozytorium danych. Wybierz pozycję konta magazynu, aby umieścić pliki w obiektach Blob na tym koncie. Z listy rozwijanej wybierz pozycję **Dodaj nowe** , a następnie **Skonfiguruj ustawienia**.

9. Wybierz typ repozytorium docelowego, które chcesz dodać, oraz inne parametry skojarzone z repozytorium.

    W przypadku wybrania miejsca docelowego typu konta magazynu można określić przyjazną nazwę, subskrypcję (wybrać tę samą opcję co w przypadku usługi lub innego) oraz konto magazynu.
        ![Konfigurowanie docelowego repozytorium danych 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Kolejka magazynu jest tworzona, gdy zadanie zostanie uruchomione. Jest ona wypełniana przy użyciu komunikatów dotyczących przekształconych obiektów blob, gdy będą gotowe. Nazwa tej kolejki jest taka sama jak nazwa definicji zadania.
    
10. Po dodaniu repozytorium danych odczekaj kilka minut.
    
    1. Wybierz utworzone repozytorium jako element docelowy z listy rozwijanej w polu **nazwa konta docelowego**.

    2. Wybierz typ magazynu jako obiekty blob lub pliki. Określ nazwę kontenera magazynu, w którym znajdują się przekształcone dane. Kliknij przycisk **OK**.

        ![Skonfiguruj docelowe konto magazynu repozytorium danych](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Możesz również zaznaczyć opcję, aby przedstawić oszacowanie czasu trwania zadania przed uruchomieniem zadania. Kliknij przycisk **OK** , aby utworzyć definicję zadania. Twoja definicja zadania została ukończona. Ta definicja zadania może być używana wiele razy za pośrednictwem interfejsu użytkownika z różnymi ustawieniami środowiska uruchomieniowego.

    ![Ukończ definicję zadania](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Nowo utworzona Definicja zadania zostanie dodana do listy definicji zadań dla tej usługi.

### <a name="run-the-job-definition"></a>Uruchamianie definicji zadania

Za każdym razem, gdy zachodzi potrzeba przeniesienia danych z StorSimple na konto magazynu określone w definicji zadania, należy je uruchomić. W czasie wykonywania niektóre parametry można określić inaczej. Kroki tego procesu są następujące:

1. Wybierz usługę StorSimple Data Manager i przejdź do pozycji **zarządzanie > definicje zadań**. Wybierz i kliknij definicję zadania, która ma zostać uruchomiona.
     
     ![Uruchom zadanie 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kliknij przycisk **Uruchom teraz**.
     
     ![Uruchom zadanie 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kliknij pozycję **Parametry uruchomieniowe** , aby zmodyfikować wszystkie ustawienia, które mogą zostać zmienione dla tego uruchomienia zadania. Kliknij przycisk **OK** , a następnie kliknij przycisk **Uruchom** , aby uruchomić zadanie.

    ![Uruchom zadanie Uruchom jako 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Aby monitorować to zadanie, przejdź do obszaru **zadania** w StorSimple Data Manager. Oprócz monitorowania w bloku **zadania** można także nasłuchiwać kolejki magazynu, do której wiadomość jest dodawana za każdym razem, gdy plik zostanie przeniesiony z StorSimple na konto magazynu.

    ![Uruchom zadanie 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Wyświetl dzienniki po zakończeniu zadania

Po zakończeniu zadania można wyświetlić stan zadania. Stan zadania może być **zakończony powodzeniem**, **częściowe** powodzenie i **Niepowodzenie**. Można wyświetlić listę plików, które zostały pomyślnie skopiowane, i pliki, których skopiowanie nie powiodło się. Te listy są dostępne w kontenerze o nazwie **"StorSimple-Data-Manager-joblogs"** w docelowym koncie magazynu. W tym kontenerze można szukać folderu o takiej samej nazwie jak definicja zadania. W tym celu zostanie utworzony folder dla każdego przebiegu zadania, który będzie zawierać listy. Nazwa tego folderu będzie identyfikatorem GUID zadania, które można uzyskać ze strony Szczegóły zadania. Alternatywnie, w większości przypadków zostanie wyświetlony link do dzienników kopiowania na stronie zadania.
Istnieje 2 zbiór plików CSV, które będą widoczne w tym folderze. Wszystkie pliki, które zaczynają się od **copiedfilelist...** , będą zawierać listę pomyślnie skopiowanych plików. Wszystkie pliki, które zaczynają się od **failedfilelist...** zawierają pliki, które nie mogły zostać skopiowane wraz z komunikatem o błędzie.


## <a name="next-steps"></a>Następne kroki

[Użyj zestawu SDK platformy .NET, aby uruchomić zadania StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).
