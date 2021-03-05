---
title: Konfigurowanie platformy Azure Percept DK
description: Połącz swój zestaw deweloperski z platformą Azure i Wdróż swój pierwszy model AI
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 49bf89d38edef6a9186cbdb5bb89a763339385b4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175825"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Skonfiguruj platformę Azure Percept DK i Wdróż swój pierwszy model AI

Rozpocznij pracę z usługą Azure Percept DK i platformą Azure Percept Studio przy użyciu środowiska instalacji Azure Percept DK, aby połączyć urządzenie z platformą Azure i wdrożyć pierwszy model AI. Po sprawdzeniu, czy Twoje konto platformy Azure jest zgodne z usługą Azure Percept Studio, należy wykonać czynności konfiguracyjne, aby upewnić się, że usługa Azure Percept DK została skonfigurowana tak, aby utworzyć weryfikację w usłudze Edge AI.

W przypadku wystąpienia jakichkolwiek problemów występujących w tej Szybki start zapoznaj się z przewodnikiem [rozwiązywania problemów](./troubleshoot-dev-kit.md) , aby uzyskać informacje na temat możliwych rozwiązań.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK.
- Komputer hosta z systemem Windows, Linux lub OS X z funkcją Wi-Fi i przeglądarką sieci Web.
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto platformy Azure musi mieć rolę "właściciel" lub "Współautor" w subskrypcji. Dowiedz się więcej na temat [definicji ról platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Aby sprawdzić, czy Twoje konto platformy Azure jest kontem "właściciel" lub "Współautor" w subskrypcji, wykonaj następujące czynności.

1. Przejdź do Azure Portal i zaloguj się przy użyciu tego samego konta platformy Azure, którego zamierzasz używać w usłudze Azure Percept Studio. 

    > [!NOTE]
    > Jeśli masz wiele kont platformy Azure, przeglądarka może buforować poświadczenia z innego konta. Zapoznaj się z przewodnikiem rozwiązywania problemów, aby uzyskać więcej informacji na temat zalogowania się przy użyciu odpowiedniego konta.

1. Rozwiń menu główne w lewym górnym rogu ekranu, a następnie kliknij pozycję "subskrypcje" lub wybierz pozycję "subskrypcje" w menu ikony na stronie głównej. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Wybierz swoją subskrypcję z listy. Jeśli Twoja subskrypcja nie jest wyświetlana na liście, upewnij się, że zalogowano się przy użyciu prawidłowego konta platformy Azure. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Jeśli chcesz utworzyć nową subskrypcję, wykonaj następujące [kroki](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Z menu subskrypcja wybierz pozycję "kontrola dostępu (IAM)"
1. Kliknij przycisk "Wyświetl mój dostęp".
1. Sprawdź rolę
    - Jeśli zostanie wyświetlona rola "czytelnik" lub jeśli zostanie wyświetlony komunikat z informacją, że nie masz uprawnień do wyświetlania ról, musisz postępować zgodnie z wymaganym procesem w organizacji, aby uzyskać podwyższoną rolę konta.
    - Jeśli zostanie wyświetlona rola jako "właściciel" lub "Współautor", Twoje konto będzie współpracować z usługą Azure Percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Uruchom środowisko instalacji usługi Azure Percept DK

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Połącz komputer hosta bezpośrednio z punktem dostępu Wi-Fi zestawu deweloperskiego. Odbywa się to tak samo jak łączenie się z innymi sieciami Wi-Fi,
    - **Nazwa sieci**: SCZ-xxxx (gdzie "XXXX" to ostatnie cztery cyfry adresu sieciowego Mac zestawu deweloperów)
    - **hasło**: można znaleźć na karcie powitalnej, która została dostarczona z zestawem deweloperskim

    > [!WARNING]
    > Po nawiązaniu połączenia z punktem dostępu Wi-Fi w usłudze Azure Percept, komputer-host tymczasowo utraci połączenie z Internetem. Aktywne wywołania konferencji wideo, przesyłania strumieniowego sieci Web lub inne środowiska sieciowe będą przerywane do momentu zakończenia kroku 3 środowiska instalacji usługi Azure Percept.

1. Po nawiązaniu połączenia z punktem dostępu Wi-Fi zestawu deweloperskiego urządzenie hosta automatycznie uruchomi środowisko instalacji Azure Percept DK w nowym oknie przeglądarki. Jeśli nie jest on automatycznie otwierany, możesz uruchomić go ręcznie, otwierając okno przeglądarki i przechodząc do [http://10.1.1.1](http://10.1.1.1) . 

1. Po uruchomieniu środowiska konfiguracji usługi Azure Percept można przystąpić do wykonywania czynności konfiguracyjnych. 

    > [!NOTE]
    > Usługa sieci Web środowiska Instalatora usługi Azure Percept DK zostanie zamknięta po 30 minutach od nieużycia i po zakończeniu instalacji. W takim przypadku zaleca się ponowne uruchomienie zestawu deweloperskiego, aby uniknąć problemów z łącznością z punktem dostępu Wi-Fi w ramach zestawu dev Kit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Ukończ działanie Instalatora platformy Azure Percept DK

1. Rozpocznij pracę — kliknij przycisk **dalej** na ekranie powitalnym.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Strona powitalna.":::

1. Nawiąż połączenie z siecią Wi-Fi — na stronie połączenie sieciowe kliknij pozycję **Połącz z nową siecią Wi** -Fi, aby połączyć Devkit z siecią Wi.

    Jeśli ten zestaw deweloperski został wcześniej połączony z siecią Wi-Fi lub jeśli masz już połączenie z instalacją Azure Percept DK za pośrednictwem sieci Wi-Fi, kliknij link **Pomiń** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Nawiązywanie połączenia z siecią Wi-Fi.":::

1. Wybierz sieć Wi-Fi z dostępnych połączeń i Połącz się. (Wymaga lokalnego hasła sieci Wi-Fi)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Wybierz sieć Wi-Fi."::: 

1. Skopiuj adres IP — po pomyślnym nawiązaniu połączenia przez Devkit z wybraną siecią Zanotuj **adres IPv4** wyświetlany na stronie. **Ten adres IP będzie potrzebny w dalszej części tego przewodnika Szybki Start**. 

    > [!NOTE]
    > Adres IP może się zmieniać przy każdym rozruchu urządzenia.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Kopiuj adres IP.":::

1. Przejrzyj i zaakceptuj umowę licencyjną — Przeczytaj umowę licencyjną, wybierz opcję **znam i** Akceptuję Umowę licencyjną (należy przewinąć w dół umowy), a następnie kliknij przycisk **dalej**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Zaakceptuj umowę licencyjną.":::

1. Utwórz konto logowania SSH — Skonfiguruj protokół SSH dla dostępu zdalnego do Devkit. Utwórz nazwę użytkownika i hasło SSH. 

    > [!NOTE]
    > SSH (Secure Shell) to protokół sieciowy służący do bezpiecznej komunikacji między urządzeniem hosta a zestawem deweloperskim. Aby uzyskać więcej informacji na temat protokołu SSH, zobacz [ten artykuł](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Utwórz konto SSH."::: 

1. Rozpocznij proces połączenia z programem dev Kit — na następnym ekranie kliknij pozycję **Połącz z nowym urządzeniem** , aby rozpocząć proces łączenia zestawu deweloperskiego z usługą Azure IoT Hub. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Nawiązywanie połączenia z platformą Azure."::: 

1. Skopiuj kod urządzenia — kliknij link **Kopiuj** , aby skopiować swój kod urządzenia. Następnie kliknij pozycję **Zaloguj się do platformy Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Kopiuj kod urządzenia."::: 

1. Wklej kod urządzenia — zostanie otwarta nowa karta przeglądarki z oknem z prośbą o skopiowanie kodu urządzenia. Wklej kod do okna i kliknij przycisk **dalej**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Wprowadź kod urządzenia."::: 

1. Zaloguj się do platformy Azure — następne okno wymaga zalogowania się przy użyciu konta platformy Azure, które zostało zweryfikowane na początku Szybki start. Wprowadź te poświadczenia logowania i kliknij przycisk **dalej**. 

    > [!NOTE]
    > Przeglądarka może buforować inne poświadczenia. Sprawdź dokładnie, czy logujesz się przy użyciu odpowiedniego konta.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Zaloguj się do platformy Azure.":::
 
1. Nie zamykaj karty przeglądarki środowiska Instalatora w tym kroku — po zalogowaniu zostanie wyświetlony ekran z potwierdzeniem, że użytkownik zalogował się. Mimo że istnieje wiele bliskich okien, **zalecamy, aby nie zamykać żadnych okien**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Pomyślne logowanie."::: 

1. Wróć do karty przeglądarki obsługującej środowisko instalacji.
1. Wybierz opcję IoT Hub
    - Jeśli masz już IoT Hub i znajduje się na niej na tej stronie, możesz ją zaznaczyć i **przejść do kroku 17**.
    - Jeśli nie masz IoT Hub lub chcesz utworzyć nowy, przejdź na dół listy, a następnie kliknij pozycję **Utwórz nowy IoT Hub platformy Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Wybierz IoT Hub."::: 

1. Utwórz nowe IoT Hub — Wypełnij wszystkie pola na tej stronie. 
    - Wybierz subskrypcję platformy Azure, która będzie używana z usługą Azure Percept Studio
    - Wybierz istniejącą grupę zasobów. Jeśli jeszcze nie istnieje, kliknij pozycję "Utwórz nowy" i postępuj zgodnie z monitami. 
    - Wybierz region platformy Azure. 
    - Nadaj nowemu IoT Hub nazwę
    - Wybierz warstwę cenową (zwykle jest to zgodne z subskrypcją)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Utwórz nowy IoT Hub."::: 

1. Poczekaj na wdrożenie IoT Hub — może to potrwać kilka minut

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Wdróż IoT Hub."::: 

1. Rejestrowanie Twojego zestawu dev Kit — po zakończeniu wdrażania kliknij przycisk **zarejestruj** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="Pomyślnie wdrożono IoT Hub."::: 

1. Nazwij zestaw dev Kit — wprowadź nazwę urządzenia dla Twojego zestawu deweloperskiego, a następnie kliknij przycisk **dalej**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Nazwij urządzenie."::: 

1. Poczekaj na pobranie domyślnych modeli AI — zajmie to kilka minut

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalizowanie instalacji."::: 

1. Zobacz sekcję "Vision AI" w działaniu — Twoje Devkit zostało pomyślnie połączone z usługą Azure IoT Hub i otrzymało domyślny model wykrywania obiektów programu Vision AI. Aparat Percepti na platformie Azure umożliwia teraz wykrywanie obiektów inferencing bez połączenia z chmurą. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Kliknij pozycję Podgląd danych wyjściowych wideo."::: 
       
    > [!NOTE]
    > Proces dołączania i połączenie z urządzeniem w sieci Wi-Fi na komputerze hosta zostanie zamknięty w tym momencie, ale zestaw deweloperski pozostanie połączony z Internetem.   Możesz ponownie uruchomić proces dołączania przy użyciu zestawu deweloperskiego, który umożliwi przejście z powrotem przez dołączenie i ponowne połączenie urządzenia z innym centrum IOT, które jest skojarzone z tą samą lub inną subskrypcją platformy Azure.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Ostrzeżenie dotyczące rozłączenia środowiska Instalatora."::: 

1. Przejdź do Azure Portal — Wróć do okna środowisko instalacji i kliknij przycisk **Kontynuuj do Azure Portal** , aby rozpocząć tworzenie niestandardowych modeli AI w usłudze Azure Percept Studio.

    > [!NOTE]
    > Sprawdź, czy komputer hosta nie jest już połączony z punktem dostępu do zestawu deweloperów w ustawieniach sieci Wi-Fi i jest teraz ponownie połączony z lokalną siecią Wi-Fi.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Przejdź do usługi Azure Percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Wyświetlanie urządzenia w usłudze Azure Percept Studio i wdrażanie wspólnych aplikacji przykładowych

1. Wyświetlanie listy urządzeń na stronie przeglądowej [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) . Strona omówienia usługi Azure Percept to Twój punkt uruchamiania umożliwiający uzyskanie dostępu do wielu różnych przepływów pracy zarówno na początku, jak i w zaawansowanym modelu i rozwiązaniu rozwiązań AI

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Wyświetl listę urządzeń.":::
    
1. Upewnij się, że właśnie utworzone urządzenie jest połączone i kliknij je.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Wybierz urządzenie.":::

1. Wyświetl strumień urządzenia, aby zobaczyć, co widzi Twoja kamera z zestawem deweloperskim. Model wykrywania obiektów domyślnych jest wdrażany poza Box i wykryje liczbę wspólnych obiektów.

    > [!NOTE]
    > po pierwszym wykonaniu tej czynności na nowym urządzeniu otrzymasz powiadomienie, że nowy moduł jest wdrażany w prawym górnym rogu.  Gdy ta wartość zostanie nagrana, będzie można uruchomić okno ze strumieniem wideo aparatu. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Wyświetl strumień wideo.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Zobacz wykrywanie obiektów.":::

1. Poznaj wstępnie skompilowane Przykładowe modele AI.   Usługa Azure Precept Studio ma wiele popularnych wstępnie skompilowanych przykładów, które można wdrożyć za pomocą jednego kliknięcia.   Wybierz pozycję "wdróż przykładowy model", aby je wyświetlić i wdrożyć.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Poznaj wstępnie skompilowane modele.":::
    
1. Wdróż nowy wstępnie utworzony przykład na podłączonym urządzeniu. Wybierz próbkę z biblioteki i kliknij pozycję "wdróż na urządzeniu".

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Zobacz wykrywanie obiektów w akcji.":::

## <a name="next-steps"></a>Następne kroki

Możesz postępować zgodnie z podobnym przepływem, aby wypróbować [wstępnie skompilowane modele mowy](./tutorial-no-code-speech.md).