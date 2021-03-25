---
title: Konfigurowanie platformy Azure Percept DK
description: Połącz swój zestaw deweloperski z platformą Azure i Wdróż swój pierwszy model AI
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 8cecd1b57395fa51fd95c824e88885d9c3ae3f09
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023303"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Skonfiguruj platformę Azure Percept DK i Wdróż swój pierwszy model AI

Ukończ działanie Instalatora platformy Azure Percept DK, aby skonfigurować zestaw deweloperski i wdrożyć pierwszy model AI. Po sprawdzeniu, czy Twoje konto platformy Azure jest zgodne z usługą Azure Percept, będziesz:

- Łączenie zestawu deweloperskiego z siecią Wi-Fi
- Skonfiguruj logowanie SSH dla dostępu zdalnego do Twojego zestawu deweloperskiego
- Utwórz nowy IoT Hub do użycia z usługą Azure Percept
- Łączenie Twojego zestawu deweloperskiego z Twoim IoT Hub i kontem platformy Azure

Jeśli wystąpią jakiekolwiek problemy w trakcie tego procesu, zapoznaj się z [przewodnikiem rozwiązywania problemów](./how-to-troubleshoot-setup.md) , aby zapoznać się z możliwymi rozwiązaniami.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (dev Kit).
- Komputer-host oparty na systemie Windows, Linux lub OS X z możliwością Wi-Fi i przeglądarką sieci Web.
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto platformy Azure musi mieć rolę **właściciela** lub **współautora** w ramach subskrypcji. Postępuj zgodnie z poniższymi instrukcjami, aby sprawdzić rolę konta platformy Azure. Aby uzyskać więcej informacji na temat definicji ról platformy Azure, zapoznaj się z [dokumentacją kontroli dostępu opartą na rolach na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

    > [!CAUTION]
    > Jeśli masz wiele kont platformy Azure, przeglądarka może buforować poświadczenia z innego konta. Aby uniknąć pomyłek, zalecane jest zamknięcie wszystkich nieużywanych okien przeglądarki i zalogowanie się do [Azure Portal](https://portal.azure.com/) przed rozpoczęciem instalacji. Zapoznaj się z [przewodnikiem rozwiązywania problemów](./how-to-troubleshoot-setup.md) , aby uzyskać dodatkowe informacje na temat zalogowania się przy użyciu odpowiedniego konta.

### <a name="check-your-azure-account-role"></a>Sprawdź rolę konta platformy Azure

Aby sprawdzić, czy Twoje konto platformy Azure jest "właściciel" lub "Współautor" w ramach subskrypcji, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu tego samego konta platformy Azure, które ma być używane z usługą Azure Percept.

1. Kliknij ikonę **subskrypcje** (wygląda na żółtą).

1. Wybierz swoją subskrypcję z listy. Jeśli Twoja subskrypcja nie jest widoczna, upewnij się, że zalogowano się przy użyciu prawidłowego konta platformy Azure. Jeśli chcesz utworzyć nową subskrypcję, wykonaj następujące [kroki](../cost-management-billing/manage/create-subscription.md).

1. Z menu subskrypcja wybierz pozycję **Kontrola dostępu (IAM)**.
1. Kliknij pozycję **Wyświetl mój dostęp**.
1. Sprawdź rolę:
    - Jeśli Twoja rola jest wyświetlana jako **czytelnik** lub jeśli zostanie wyświetlony komunikat informujący, że nie masz uprawnień do wyświetlania ról, musisz postępować zgodnie z niezbędnym procesem w organizacji, aby podnieść rolę konta.
    - Jeśli Twoja rola jest wymieniona jako **właściciel** lub **współautor**, Twoje konto będzie działać z usługą Azure Percept i możesz kontynuować pracę z instalacją.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Uruchom środowisko instalacji usługi Azure Percept DK

1. Podłącz komputer hosta bezpośrednio do punktu dostępu Wi-Fi zestawu deweloperskiego. Podobnie jak w przypadku łączenia się z innymi sieciami Wi-Fi, Otwórz ustawienia sieci i Internetu na komputerze, kliknij następującą sieć i wprowadź hasło sieciowe po wyświetleniu monitu:

    - **Nazwa sieci**: w zależności od wersji systemu operacyjnego zestawu deweloperskiego nazwa Wi-Fi punktu dostępu to **SCZ-xxxx** lub **APD-xxxx** (gdzie "XXXX" to ostatnie cztery cyfry adresu MAC zestawu deweloperów).
    - **Hasło**: można znaleźć na karcie powitalnej, która została dostarczona z zestawem deweloperskim

    > [!WARNING]
    > Po nawiązaniu połączenia z punktem dostępu do usługi Azure Percept Wi-Fi, komputer-host tymczasowo utraci połączenie z Internetem. Aktywne wywołania konferencji wideo, przesyłania strumieniowego sieci Web lub inne środowiska sieciowe będą przerywane.

1. Po nawiązaniu połączenia z punktem dostępu Wi-Fi zestawu deweloperów komputer-host automatycznie uruchomi środowisko instalacji w nowym oknie przeglądarki z **nowym. urządzeniem/** na pasku adresu. Jeśli karta nie zostanie otwarta automatycznie, uruchom środowisko instalacji, przechodząc do programu [http://10.1.1.1](http://10.1.1.1) . Upewnij się, że Twoja przeglądarka jest zalogowana przy użyciu tych samych poświadczeń konta platformy Azure, które mają być używane z usługą Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Strona powitalna.":::

    > [!CAUTION]
    > Usługa sieci Web środowiska konfiguracji zostanie wyłączona po 30 minutach, w których nie jest używany. W takim przypadku należy ponownie uruchomić zestaw deweloperów, aby uniknąć problemów z łącznością z punktem dostępu Wi-Fi zestawu deweloperskiego.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Ukończ działanie Instalatora platformy Azure Percept DK

1. Kliknij przycisk **dalej** na ekranie **powitalnym** .

1. Na stronie **połączenie sieciowe** kliknij pozycję **Połącz z nową siecią Wi-Fi**.

    Jeśli zestaw deweloperski został już podłączony do sieci Wi-Fi, kliknij przycisk **Pomiń**.

1. Wybierz sieć Wi-Fi z listy dostępnych sieci i kliknij przycisk **Połącz**. Po wyświetleniu monitu wprowadź hasło sieciowe.

1. Po pomyślnym nawiązaniu połączenia z wybraną siecią przez zestaw dev Kit na stronie zostanie wyświetlony adres IPv4 przypisany do Twojego zestawu deweloperskiego. **Zapisz adres IPv4 wyświetlany na stronie.** W przypadku łączenia się z zestawem deweloperskim za pośrednictwem protokołu SSH w celu rozwiązywania problemów i aktualizacji urządzeń będzie potrzebny adres IP.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Kopiuj adres IP.":::

    > [!NOTE]
    > Adres IP może się zmieniać przy każdym rozruchu urządzenia.

1. Zapoznaj się z umową licencyjną, wybierz opcję **znam i Akceptuję Umowę licencyjną** (przewiń w dół do umowy), a następnie kliknij przycisk **dalej**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Zaakceptuj umowę licencyjną.":::

1. Wprowadź nazwę konta SSH i hasło, a **następnie Zapisz informacje logowania do późniejszego użycia**.

    > [!NOTE]
    > SSH (Secure Shell) to protokół sieciowy, który umożliwia zdalne nawiązywanie połączenia z zestawem deweloperskim za pośrednictwem komputera hosta.

1. Na następnej stronie kliknij pozycję **Konfiguracja jako nowe urządzenie** , aby utworzyć nowe urządzenie na koncie platformy Azure.

1. Kliknij przycisk **Kopiuj** , aby skopiować kod urządzenia. Następnie kliknij pozycję **Zaloguj się na platformie Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Kopiuj kod urządzenia.":::

1. Zostanie otwarta nowa karta przeglądarki z oknem informującym o **wprowadzeniu kodu**. Wklej kod do okna i kliknij przycisk **dalej**. NIE zamykaj karty **Zapraszamy** z instalacją.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Wprowadź kod urządzenia.":::

1. Zaloguj się do usługi Azure Percept przy użyciu poświadczeń konta platformy Azure, które będą używane z Twoim zestawem deweloperskim. Pozostaw kartę przeglądarki otwarta po zakończeniu.

    > [!CAUTION]
    > Przeglądarka może buforować inne poświadczenia. Sprawdź dokładnie, czy logujesz się przy użyciu odpowiedniego konta.

    Po pomyślnym zalogowaniu się do usługi Azure PERCENT na urządzeniu Wróć do karty **Zapraszamy** , aby kontynuować instalację.

1. Gdy na karcie **Zapraszamy** zostanie wyświetlona strona **Przypisz urządzenie do usługi Azure IoT Hub** , wykonaj jedną z następujących czynności:

    - Jeśli masz już IoT Hub, których chcesz używać z usługą Azure Percept i znajduje się na tej stronie, wybierz ją i przejdź do kroku 15.
    - Jeśli nie masz IoT Hub lub chcesz utworzyć nowy, kliknij pozycję **Utwórz nowy IoT Hub platformy Azure**.

    > [!IMPORTANT]
    > Jeśli masz IoT Hub, ale nie jest on wyświetlany na liście, być może zarejestrowano Cię w usłudze Azure Percept przy użyciu nieprawidłowych poświadczeń. Aby uzyskać pomoc, zobacz [Przewodnik rozwiązywania problemów Instalatora](./how-to-troubleshoot-setup.md) .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Wybierz IoT Hub.":::

1. Aby utworzyć nowy IoT Hub, wykonaj następujące pola:

    - Wybierz subskrypcję platformy Azure, która będzie używana z usługą Azure Percept.
    - Wybierz istniejącą grupę zasobów. Jeśli jeszcze nie istnieje, kliknij przycisk **Utwórz nowy** i postępuj zgodnie z monitami.
    - Wybierz region platformy Azure znajdujący się najbliżej lokalizacji fizycznej.
    - Nadaj nowemu IoT Hub nazwę.
    - Wybierz warstwę cenową S1 (standardowa).

    > [!NOTE]
    > Jeśli potrzebujesz wyższej [przepływności komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) dla aplikacji systemu AI, możesz w dowolnym momencie [uaktualnić IoT Hub do wyższej warstwy Standardowa](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) w witrynie Azure Portal. Warstwy B i F nie obsługują usługi Azure Percept.

1. Wdrożenie IoT Hub może potrwać kilka minut. Po zakończeniu wdrażania kliknij pozycję **zarejestruj**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="Pomyślnie wdrożono IoT Hub.":::

1. Wprowadź nazwę urządzenia dla Twojego zestawu deweloperskiego, a następnie kliknij przycisk **dalej**.

1. Poczekaj na pobranie modułów urządzenia — zajmie to kilka minut.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalizowanie instalacji.":::

1. Gdy zostanie wyświetlone **zakończenie instalacji urządzenia!** Twój zestaw deweloperski został pomyślnie połączony z IoT Hub i pobrał wymagane oprogramowanie. Twój zestaw deweloperów automatycznie rozłączy się z punktem dostępu Wi-Fi w wyniku tych dwóch powiadomień:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Ostrzeżenie dotyczące rozłączenia środowiska Instalatora.":::

1. Podłącz komputer hosta do sieci Wi-Fi, do której Devkit podłączony w kroku 2.

1. Kliknij przycisk **Kontynuuj do Azure Portal**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Przejdź do usługi Azure Percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Wyświetlanie strumienia wideo zestawu dev Kit i wdrażanie przykładowego modelu

1. Na [stronie Przegląd usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) znajduje się punkt uruchamiania służący do uzyskiwania dostępu do wielu różnych przepływów pracy zarówno na początku, jak i na potrzeby zaawansowanego opracowywania rozwiązań. Aby rozpocząć, kliknij pozycję **urządzenia** w menu po lewej stronie.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Wyświetl listę urządzeń.":::

1. Sprawdź, czy Twój zestaw deweloperski jest wymieniony jako **połączony** i kliknij go, aby wyświetlić stronę urządzenia.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Wybierz urządzenie.":::

1. Kliknij pozycję **Wyświetl strumień urządzenia**. Jeśli po raz pierwszy przeglądasz strumień wideo urządzenia, zobaczysz powiadomienie o tym, że nowy model jest wdrażany w prawym górnym rogu. Może to potrwać kilka minut.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Wyświetl strumień wideo.":::

    Po wdrożeniu modelu otrzymasz kolejne powiadomienie z linkiem **Wyświetl strumień** . Kliknij link, aby wyświetlić strumień wideo z aparatu usługi Azure Percept Vision w nowym oknie przeglądarki. Zestaw dev Kit jest wstępnie załadowany z modelem AI, który automatycznie wykonuje wykrywanie obiektów wielu wspólnych obiektów.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Zobacz wykrywanie obiektów.":::

1. Usługa Azure Percept Studio ma także kilka przykładowych modeli AI. Aby wdrożyć przykładowy model do zestawu deweloperskiego, przejdź z powrotem do strony urządzenia i kliknij pozycję **Wdróż przykładowy model**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Poznaj wstępnie skompilowane modele.":::

1. Wybierz przykładowy model z biblioteki, a następnie kliknij pozycję **Wdróż na urządzeniu**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Zobacz wykrywanie obiektów w akcji.":::

1. Po pomyślnym wdrożeniu modelu zobaczysz powiadomienie z linkiem **Wyświetl strumień** w prawym górnym rogu ekranu. Aby wyświetlić inferencing modelu w akcji, kliknij link w obszarze powiadomień lub Wróć do strony urządzenie, a następnie kliknij pozycję **Wyświetl strumień urządzenia**. Wszystkie modele wcześniej uruchomione w zestawie deweloperskim zostaną teraz zastąpione nowym modelem.

## <a name="video-walkthrough"></a>Przewodnik wideo

Aby zapoznać się ze szczegółowymi krokami opisanymi powyżej, zobacz następujący film wideo (środowisko instalacyjne jest uruchamiane o godzinie 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie rozwiązania bez obsługi kodu](./tutorial-nocode-vision.md)