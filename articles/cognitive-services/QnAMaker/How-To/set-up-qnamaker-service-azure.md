---
title: Konfigurowanie usługi QnA Maker - QnA Maker
description: Przed utworzeniem dowolnej bazy wiedzy programu QnA Maker należy najpierw skonfigurować usługę QnA Maker na platformie Azure. Każdy, kto ma autoryzację do tworzenia nowych zasobów w ramach subskrypcji, może skonfigurować usługę QnA Maker.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402987"
---
# <a name="manage-qna-maker-resources"></a>Zarządzanie zasobami programu QnA Maker

Przed utworzeniem dowolnej bazy wiedzy programu QnA Maker należy najpierw skonfigurować usługę QnA Maker na platformie Azure. Każdy, kto ma autoryzację do tworzenia nowych zasobów w ramach subskrypcji, może skonfigurować usługę QnA Maker.

Przed utworzeniem zasobu pomocne jest solidne zrozumienie następujących pojęć:

* [Zasoby programu QnA Maker](../Concepts/azure-resources.md)
* [Tworzenie i publikowanie kluczy](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Tworzenie nowej usługi QnA Maker

Ta procedura tworzy zasoby platformy Azure potrzebne do zarządzania zawartością bazy wiedzy. Po wykonaniu tych kroków znajdziesz klucze _subskrypcji_ na stronie **Klucze** zasobu w witrynie Azure portal.

1. Zaloguj się do witryny Azure portal i utwórz zasób [programu QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Wybierz **pozycję Utwórz** po przeczytaniu regulaminu:

    ![Tworzenie nowej usługi QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. W **aplikacji QnA Maker**wybierz odpowiednie warstwy i regiony:

    ![Tworzenie nowej usługi QnA Maker — warstwa cenowa i regiony](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * W polu **Nazwa** wprowadź unikatową nazwę, aby zidentyfikować tę usługę QnA Maker. Ta nazwa identyfikuje również punkt końcowy programu QnA Maker, z którymi będą skojarzone bazy wiedzy.
    * Wybierz **subskrypcję,** w ramach której zostanie wdrożony zasób QnA Maker.
    * Wybierz **warstwę Cen dla** usług zarządzania QnA Maker (interfejsy API portalu i zarządzania). Zobacz [więcej szczegółów na temat cen sku](https://aka.ms/qnamaker-pricing).
    * Utwórz nową **grupę zasobów** (zalecane) lub użyj istniejącej grupy, w której można wdrożyć ten zasób programu QnA Maker. Program QnA Maker tworzy kilka zasobów platformy Azure. Podczas tworzenia grupy zasobów do przechowywania tych zasobów można łatwo znaleźć, zarządzać i usunąć te zasoby według nazwy grupy zasobów.
    * Wybierz **lokalizację grupy zasobów**.
    * Wybierz **warstwę cenową wyszukiwania** usługi Azure Cognitive Search. Jeśli bezpłatna warstwa opcja jest niedostępna (jest wyszarzona), oznacza to, że masz już bezpłatną usługę wdrożoną za pośrednictwem subskrypcji. W takim przypadku musisz zacząć od warstwy Podstawowa. Zobacz [szczegóły cen usługi Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).
    * Wybierz **lokalizację wyszukiwania,** w której mają zostać wdrożone indeksy usługi Azure Cognitive Search. Ograniczenia dotyczące miejsca przechowywania danych klienta pomogą określić lokalizację, którą wybierzesz dla usługi Azure Cognitive Search.
    * W polu **Nazwa aplikacji** wprowadź nazwę wystąpienia usługi Azure App Service.
    * Domyślnie usługa App Service domyślnie ma standardową warstwę (S1). Plan można zmienić po utworzeniu. Dowiedz się więcej o [cenach usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Wybierz **lokalizację witryny sieci Web,** w której zostanie wdrożona usługa App Service.

        > [!NOTE]
        > **Lokalizacja wyszukiwania** może się różnić od **lokalizacji witryny sieci Web.**

    * Wybierz, czy chcesz włączyć **usługę Application Insights**. Jeśli **usługa Application Insights** jest włączona, aplikacja QnA Maker zbiera dane telemetryczne dotyczące ruchu, dzienników czatu i błędów.
    * Wybierz **lokalizację wglądu** w aplikacje, w której zostanie wdrożony zasób usługi Application Insights.
    * Aby uzyskać oszczędności, można [udostępnić](#configure-qna-maker-to-use-different-cognitive-search-resource) niektóre, ale nie wszystkie zasoby platformy Azure utworzone dla programu QnA Maker.

1. Po sprawdzeniu poprawności wszystkich pól wybierz pozycję **Utwórz**. Proces może potrwać kilka minut.

1. Po zakończeniu wdrażania zostaną wyświetlona następujące zasoby utworzone w ramach subskrypcji:

   ![Zasób utworzył nową usługę QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    Zasób z _typem usług Cognitive Services_ ma klucze _subskrypcji._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Znajdowanie kluczy subskrypcji w witrynie Azure portal

Klucze subskrypcji można wyświetlać i resetować z witryny Azure Portal, w której utworzono zasób programu QnA Maker.

1. Przejdź do zasobu QnA Maker w witrynie Azure portal i wybierz zasób, który ma typ _usług Cognitive Services:_

    ![Lista zasobów programu QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klawiszy:**

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Znajdowanie kluczy punktów końcowych w portalu programu QnA Maker

Punkt końcowy znajduje się w tym samym regionie co zasób, ponieważ klucze punktu końcowego są używane do wywołania bazy wiedzy.

Kluczami punktu końcowego można zarządzać za pomocą [portalu QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu QnA Maker](https://qnamaker.ai), przejdź do swojego profilu, a następnie wybierz **pozycję Ustawienia usługi:**

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Wyświetlanie lub resetowanie klawiszy:

    > [!div class="mx-imgBorder"]
    > ![Menedżer kluczy punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Odśwież klucze, jeśli uważasz, że zostały naruszone. Może to wymagać odpowiednich zmian w aplikacji klienckiej lub kod bota.

## <a name="upgrade-qna-maker-sku"></a>Uaktualnienie SKU programu QnA Maker

Jeśli chcesz mieć więcej pytań i odpowiedzi w bazie wiedzy, poza bieżącą warstwą, uaktualnij warstwę cenową usług QnA Maker.

Aby uaktualnić jednostkę SKU do zarządzania QnA Maker:

1. Przejdź do zasobu programu QnA Maker w witrynie Azure portal i wybierz **warstwę cenową**.

    ![Zasób QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Wybierz odpowiednią jednostkę SKU i naciśnij **klawisz Select**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uaktualnianie usługi aplikacji

 Gdy baza wiedzy musi obsługiwać więcej żądań z aplikacji klienckiej, uaktualnij warstwę cenową usługi App Service.

Można [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub skalować w poziomie usługi app service.

Przejdź do zasobu usługi app service w witrynie Azure portal i wybierz opcję **Skalowanie w górę** lub **Skaluj w poziomie** zgodnie z wymaganiami.

![Skala usługi aplikacji QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Uaktualnianie usługi Azure Cognitive Search

Jeśli planujesz mieć wiele baz wiedzy, uaktualnij warstwę cenową usługi Azure Cognitive Search.

Obecnie nie można wykonać uaktualnienia w miejscu jednostki SKU wyszukiwania platformy Azure. Można jednak utworzyć nowy zasób wyszukiwania platformy Azure z żądaną jednostką SKU, przywrócić dane do nowego zasobu, a następnie połączyć je ze stosem QnA Maker. W tym celu wykonaj następujące kroki:

1. Utwórz nowy zasób wyszukiwania platformy Azure w witrynie Azure portal i wybierz żądaną jednostkę SKU.

    ![Zasób wyszukiwania platformy Azure programu QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Przywróć indeksy z oryginalnego zasobu wyszukiwania platformy Azure do nowego. Zobacz [przykładowy kod przywracania kopii zapasowej](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Po przywróceniu danych przejdź do nowego zasobu wyszukiwania platformy Azure, wybierz pozycję **Klucze**i zapisz **nazwę** i **klucz administrator:**

    ![Klucze wyszukiwania platformy Azure programu QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Aby połączyć nowy zasób wyszukiwania platformy Azure ze stosem QnA Maker, przejdź do wystąpienia usługi aplikacji QnA Maker.

    ![Wystąpienie usługi aplikacji aplikacji programu QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Wybierz **ustawienia aplikacji** i zmodyfikuj ustawienia w polach **AzureSearchName** i **AzureSearchAdminKey** z kroku 3.

    ![Ustawienie usługi aplikacji QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Uruchom ponownie wystąpienie usługi App Service.

    ![Ponowne uruchamianie wystąpienia usługi aplikacji QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Pobierz najnowsze aktualizacje środowiska wykonawczego

Środowisko uruchomieniowe QnAMaker jest częścią wystąpienia usługi Azure App Service, które jest wdrażane podczas [tworzenia usługi QnAMaker](./set-up-qnamaker-service-azure.md) w witrynie Azure portal. Aktualizacje są okresowo do środowiska wykonawczego. Wystąpienie usługi QnA Maker App Service jest w trybie automatycznej aktualizacji po wydaniu rozszerzenia witryny z kwietnia 2019 r. (wersja 5+). Ta aktualizacja ma na celu zadbanie o zero przestojów podczas uaktualnień.

Aktualną wersję można https://www.qnamaker.ai/UserSettingssprawdzić w punkcie . Jeśli twoja wersja jest starsza niż wersja 5.x, należy ponownie uruchomić usługę App Service, aby zastosować najnowsze aktualizacje:

1. Przejdź do usługi QnAMaker (grupy zasobów) w [witrynie Azure portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Grupa zasobów platformy QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz wystąpienie usługi App Service i otwórz sekcję **Przegląd.**

    > [!div class="mx-imgBorder"]
    > ![Wystąpienie usługi aplikacji QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Uruchom ponownie usługę aplikacji. Proces aktualizacji powinien zakończyć się w ciągu kilku sekund. Wszystkie zależne aplikacje lub boty korzystające z tej usługi QnAMaker będą niedostępne dla użytkowników końcowych w tym okresie ponownego uruchamiania.

    ![Ponowne uruchamianie wystąpienia usługi aplikacji QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurowanie programu QnA Maker do używania innego zasobu wyszukiwania funkcji Poznawczych

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) za pośrednictwem portalu, usługa wyszukiwania zostanie utworzona dla Ciebie i połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie usługi App Service, aby użyć wcześniej istniejącej usługi wyszukiwania i usunąć właśnie utworzoną usługę.

Zasób usługi **aplikacji** QnA Maker korzysta z zasobu wyszukiwania funkcji Poznawczych. Aby zmienić zasób wyszukiwania funkcji Poznawczych używany przez program QnA Maker, należy zmienić ustawienie w witrynie Azure portal.

1. Pobierz **klucz administratora** i **nazwę** zasobu wyszukiwania poznawczego, którego program QnA Maker ma być używany.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i znajdź **usługę app service** skojarzoną z zasobem programu QnA Maker. Oba mają taką samą nazwę.

1. Wybierz **pozycję Ustawienia**, a następnie pozycję **Konfiguracja**. Spowoduje to wyświetlenie wszystkich istniejących ustawień usługi aplikacji QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający witrynę Azure portal z ustawieniami konfiguracji usługi App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Zmień wartości dla następujących klawiszy:

    * **Narzędzie AzureSearchAdminKey**
    * **Usługa AzureSearchName**

1. Aby korzystać z nowych ustawień, należy ponownie uruchomić usługę aplikacji. Wybierz **pozycję Przegląd**, a następnie wybierz pozycję Uruchom **ponownie**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający ponowne uruchamianie usługi App Service w portalu Azure po zmianie ustawień konfiguracji](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Jeśli tworzysz usługę QnA za pomocą szablonów usługi Azure Resource Manager, możesz utworzyć wszystkie zasoby i kontrolować tworzenie usługi App Service, aby korzystać z istniejącej usługi wyszukiwania.

Dowiedz się więcej o konfigurowaniu [ustawień aplikacji](../../../app-service/configure-common.md#configure-app-settings)usługi App Service .

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Konfigurowanie ustawienia bezczynnego usługi aplikacji w celu uniknięcia przekroku czasu

Usługa aplikacji, która służy qnA maker przewidywanie środowiska uruchomieniowego dla opublikowanej bazy wiedzy, ma konfigurację limitu czasu bezczynności, która domyślnie automatycznie limit czasu, jeśli usługa jest bezczynna. W przypadku programu QnA Maker oznacza to, że środowisko uruchomieniowe przewidywanie generateAnswer API od czasu do czasu się po upływie czasu po okresach bez ruchu.

Aby zachować ładowane aplikacji punktu końcowego przewidywania nawet wtedy, gdy nie ma ruchu, należy ustawić bezczynnie zawsze włączone.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz usługę aplikacji zasobu QnA Maker. Będzie miał taką samą nazwę jak zasób QnA Maker, ale będzie miał inny **typ** usługi app service.
1. Znajdź **ustawienia,** a następnie wybierz **pozycję Konfiguracja**.
1. W okienku Konfiguracja wybierz pozycję **Ustawienia ogólne**, a następnie znajdź pozycję **Zawsze włączony**i jako wartość wybierz pozycję **Włącz.**

    > [!div class="mx-imgBorder"]
    > ![W okienku Konfiguracja wybierz **Ustawienia ogólne**, a następnie znajdź pozycję **Zawsze włączony**, a jako wartość wybierz pozycję **On** .](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Wybierz **pozycję Zapisz,** aby zapisać konfigurację.
1. Pojawi się pytanie, czy chcesz ponownie uruchomić aplikację, aby użyć nowego ustawienia. Wybierz przycisk **Kontynuuj**.

Dowiedz się więcej o konfigurowaniu [ustawień ogólnych](../../../app-service/configure-common.md#configure-general-settings)usługi App Service .

## <a name="delete-azure-resources"></a>Usuwanie zasobów platformy Azure

Jeśli usuniesz dowolne zasoby platformy Azure używane dla baz wiedzy programu QnA Maker, bazy wiedzy nie będą już działać. Przed usunięciem jakichkolwiek zasobów należy wyeksportować bazy wiedzy ze strony **Ustawienia.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze App](../../../app-service/index.yml) i [usłudze wyszukiwania](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie bazy wiedzy](../Quickstarts/create-publish-knowledge-base.md)
