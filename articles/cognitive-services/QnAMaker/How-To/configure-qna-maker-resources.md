---
title: Konfigurowanie usługi QnA Maker — QnA Maker
description: Ten dokument zawiera opis zaawansowanych konfiguracji zasobów QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220157"
---
# <a name="configure-qna-maker-resources"></a>Konfigurowanie zasobów QnA Maker

Użytkownik może skonfigurować QnA Maker do korzystania z innego zasobu wyszukiwania poznawczego. Mogą również skonfigurować ustawienia usługi App Service, jeśli korzystają z QnA Maker GA.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurowanie QnA Maker do używania różnych zasobów Wyszukiwanie poznawcze

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) w portalu, zostanie utworzona usługa wyszukiwania i zostanie ona połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie App Service tak, aby korzystało już z istniejącej usługi wyszukiwania, i usunąć właśnie utworzoną.

Zasób **App Service** QNA Maker używa zasobu wyszukiwanie poznawcze. Aby zmienić zasób Wyszukiwanie poznawcze używany przez QnA Maker, należy zmienić ustawienie w Azure Portal.

1. Pobierz **klucz administratora** i **nazwę** zasobu Wyszukiwanie poznawcze, którego chcesz QNA Maker użyć.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Znajdź **App Service** skojarzone z Twoim zasobem QNA Maker. Oba z mają tę samą nazwę.

1. Wybierz opcję **Ustawienia**, a następnie **Konfiguracja**. Spowoduje to wyświetlenie wszystkich istniejących ustawień App Service QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Azure Portal pokazujący App Service ustawienia konfiguracji](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Zmień wartości następujących kluczy:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Aby użyć nowych ustawień, należy ponownie uruchomić usługę App Service. Wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Uruchom ponownie**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Azure Portal ponownego uruchomienia App Service po zmianie ustawień konfiguracji](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Jeśli utworzysz usługę QnA za pomocą szablonów Azure Resource Manager, możesz utworzyć wszystkie zasoby i kontrolować tworzenie App Service, aby użyć istniejącej usługi wyszukiwania.

Dowiedz się więcej na temat konfigurowania [ustawień aplikacji](../../../app-service/configure-common.md#configure-app-settings)App Service.

### <a name="get-the-latest-runtime-updates"></a>Pobierz najnowsze aktualizacje środowiska uruchomieniowego

Środowisko uruchomieniowe QnAMaker jest częścią wystąpienia Azure App Service, które jest wdrażane podczas [tworzenia usługi QnAMaker](./set-up-qnamaker-service-azure.md) w Azure Portal. Aktualizacje są wykonywane okresowo dla środowiska uruchomieniowego. Wystąpienie App Service QnA Maker jest w trybie aktualizacji z kwietnia 2019 (wersja 5 +). Ta aktualizacja została zaprojektowana tak, aby zadbać o brak przestojów podczas uaktualniania.

Bieżącą wersję można sprawdzić pod adresem https://www.qnamaker.ai/UserSettings . Jeśli wersja jest starsza niż wersja 5. x, należy ponownie uruchomić App Service, aby zastosować najnowsze aktualizacje:

1. Przejdź do usługi QnAMaker (Grupa zasobów) w [Azure Portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![QnAMaker grupę zasobów platformy Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz wystąpienie App Service i Otwórz sekcję **Przegląd** .

    > [!div class="mx-imgBorder"]
    > ![Wystąpienie App Service QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Uruchom ponownie App Service. Proces aktualizacji powinien zakończyć się w ciągu kilku sekund. Wszystkie aplikacje zależne lub botów korzystające z tej usługi QnAMaker będą niedostępne dla użytkowników końcowych w trakcie tego okresu.

    ![Uruchom ponownie wystąpienie App Service QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Skonfiguruj ustawienie bezczynności usługi App Service, aby uniknąć limitu czasu

Usługa App Service, która obsługuje środowisko uruchomieniowe przewidywania QnA Maker dla opublikowanej bazy wiedzy, ma konfigurację przekroczenia limitu czasu bezczynności, która domyślnie automatycznie przekroczy limit czasu, jeśli usługa jest bezczynna. W przypadku QnA Maker oznacza to, że interfejs API usługi przewidywania środowiska uruchomieniowego generateAnswer sporadycznie przebiega czasu po upływie okresów braku ruchu.

Aby zapewnić, że aplikacja punktu końcowego przewidywania została załadowana nawet w przypadku braku ruchu, ustaw wartość bezczynne na zawsze włączone.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz usługę App Service dla QnA Maker. Będzie ona mieć taką samą nazwę jak zasób QnA Maker, ale będzie mieć inny **typ** App Service.
1. Znajdź **Ustawienia** , a następnie wybierz pozycję **Konfiguracja**.
1. W okienku Konfiguracja wybierz pozycję **Ustawienia ogólne**, a następnie znajdź pozycję **zawsze włączone** i wybierz pozycję **włączone** jako wartość.

    > [!div class="mx-imgBorder"]
    > ![W okienku Konfiguracja wybierz pozycję * * Ustawienia ogólne * *, a następnie znajdź * * zawsze włączone * *, a następnie wybierz * * na * * jako wartość.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Wybierz pozycję **Zapisz** , aby zapisać konfigurację.
1. Zostanie wyświetlony monit, czy chcesz ponownie uruchomić aplikację, aby użyć nowego ustawienia. Wybierz opcję **Kontynuuj**.

Dowiedz się więcej na temat konfigurowania [ustawień ogólnych](../../../app-service/configure-common.md#configure-general-settings)App Service.

### <a name="business-continuity-with-traffic-manager"></a>Ciągłość działania dzięki usłudze Traffic Manager

Głównym celem planu ciągłości działalności biznesowej jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak czasu dla bot lub aplikacji.

> [!div class="mx-imgBorder"]
> ![QnA Maker plan BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Pomysł wysokiego poziomu przedstawiony powyżej jest następujący:

1. Skonfiguruj dwie [usługi QNA Maker Parallel Services](set-up-qnamaker-service-azure.md) w [sparowanych regionach platformy Azure](../../../best-practices-availability-paired-regions.md).

1. [Utwórz kopię zapasową](../../../app-service/manage-backup.md) głównej usługi App Service QNA Maker i [Przywróć](../../../app-service/web-sites-restore.md) ją w konfiguracji pomocniczej. Zapewni to, że obie konfiguracje będą działały z tą samą nazwą hosta i kluczami.

1. Zachowaj synchronizację podstawowych i pomocniczych indeksów usługi Azure Search. Skorzystaj [z przykładu GitHub, aby](https://github.com/pchoudhari/QnAMakerBackupRestore) zobaczyć, jak tworzyć kopie zapasowe indeksów platformy Azure.

1. Wykonaj kopię zapasową Application Insights za pomocą [eksportu ciągłego](../../../azure-monitor/app/export-telemetry.md).

1. Po skonfigurowaniu stosów podstawowych i pomocniczych Użyj usługi [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) , aby skonfigurować dwa punkty końcowe i skonfigurować metodę routingu.

1. Należy utworzyć Transport Layer Security (TLS), wcześniej znany jako Secure Sockets Layer (SSL), certyfikat dla punktu końcowego usługi Traffic Manager. [Powiąż certyfikat TLS/SSL](../../../app-service/configure-ssl-bindings.md) w usługach aplikacji.

1. Na koniec użyj punktu końcowego w usłudze Traffic Manager w bot lub aplikacji.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Konfigurowanie usługi QnA Maker Managed (wersja zapoznawcza) do używania różnych zasobów Wyszukiwanie poznawcze

Jeśli utworzysz usługę QnA Managed (wersja zapoznawcza) i jej zależności (takie jak wyszukiwanie) za pomocą portalu, zostanie utworzona usługa wyszukiwania i zostanie ona połączona z usługą QnA Maker Managed (wersja zapoznawcza). Po utworzeniu tych zasobów możesz zaktualizować usługę wyszukiwania na karcie **Konfiguracja** .

1. Przejdź do usługi Managed QnA Maker (wersja zapoznawcza) w Azure Portal.

1. Wybierz pozycję **Konfiguracja** i wybierz usługę wyszukiwanie poznawcze platformy Azure, którą chcesz połączyć z usługą zarządzania QNA Maker (wersja zapoznawcza).

    ![Zrzut ekranu przedstawiający stronę konfiguracji zarządzania (wersja zapoznawcza) QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Jeśli zmienisz usługę Azure Search skojarzoną z QnA Maker, utracisz dostęp do wszystkich już obecnych baz wiedzy. Przed zmianą usługi Azure Search upewnij się, że zostały wyeksportowane istniejące bazy wiedzy.

---
