---
title: Ustawienia wypychania do konfiguracji aplikacji z Azure Pipelines
description: Dowiedz się, jak używać Azure Pipelines do wypychania wartości klucza do magazynu konfiguracji aplikacji
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: e1a4fb52a5f9622758e9ed805bf9380f5f608870
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068256"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Ustawienia wypychania do konfiguracji aplikacji z Azure Pipelines

Zadanie [wypychania konfiguracji aplikacji platformy Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) powoduje wypychanie wartości klucza z pliku konfiguracji do magazynu konfiguracji aplikacji. To zadanie umożliwia pełne koło w ramach potoku, ponieważ teraz można ściągnąć ustawienia z magazynu konfiguracji aplikacji oraz ustawienia wypychania do magazynu konfiguracji aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- Zasób konfiguracji aplikacji — utwórz go bezpłatnie w [Azure Portal](https://portal.azure.com).
- Projekt usługi Azure DevOps — [Utwórz go bezpłatnie](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Zadanie wypychania konfiguracji aplikacji platformy Azure — Pobierz bezpłatnie z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Utwórz połączenie usługi

[Połączenie z usługą](/azure/devops/pipelines/library/service-endpoints) umożliwia dostęp do zasobów w ramach subskrypcji platformy Azure z projektu usługi Azure DevOps.

1. W usłudze Azure DevOps przejdź do projektu zawierającego potok docelowy i Otwórz **Ustawienia projektu** w lewym dolnym rogu.
1. W obszarze **potoki** wybierz pozycję **połączenia usługi** , a następnie wybierz pozycję **nowe połączenie usługi** w prawym górnym rogu.
1. Wybierz **Azure Resource Manager**.
![Zrzut ekranu przedstawia Wybieranie Azure Resource Manager z listy rozwijanej nowe połączenie z usługą.](./media/new-service-connection.png)
1. W oknie dialogowym **Metoda uwierzytelniania** wybierz pozycję Nazwa **główna usługi (automatycznie)**.
    > [!NOTE]
    > Uwierzytelnianie **tożsamości zarządzanej** nie jest obecnie obsługiwane w przypadku zadania konfiguracji aplikacji.
1. Wypełnij swoją subskrypcję i zasób. Nadaj nazwę połączeniu z usługą.

Po utworzeniu połączenia usługi Znajdź nazwę przypisanej do niej nazwy głównej usługi. W następnym kroku dodasz nowe przypisanie roli do tej jednostki usługi.

1. Przejdź do pozycji **Ustawienia projektu**  >  **połączenia usługi**.
1. Wybierz połączenie usługi utworzone w poprzedniej sekcji.
1. Wybierz pozycję Zarządzaj jednostką **usługi**.
1. Zwróć uwagę na wymienioną **nazwę wyświetlaną** .
![Zrzut ekranu przedstawia nazwę wyświetlaną jednostki usługi.](./media/service-principal-display-name.png)

## <a name="add-role-assignment"></a>Dodaj przypisanie roli

Przypisz odpowiednie przypisania roli konfiguracji aplikacji do poświadczeń używanych w ramach zadania, aby zadanie miało dostęp do magazynu konfiguracji aplikacji.

1. Przejdź do magazynu konfiguracji aplikacji docelowej. 
1. Po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)**.
1. Po prawej stronie kliknij przycisk **Dodaj przypisania roli** .
![Zrzut ekranu przedstawia przycisk Dodaj przypisania roli.](./media/add-role-assignment-button.png)
1. W obszarze **rola** wybierz pozycję **właściciel danych konfiguracji aplikacji**. Ta rola umożliwia zadanie odczytu i zapisu w magazynie konfiguracji aplikacji. 
1. Wybierz nazwę główną usługi skojarzoną z połączeniem usługi, które zostało utworzone w poprzedniej sekcji.
![Zrzut ekranu przedstawia okno dialogowe Dodawanie przypisania roli.](./media/add-role-assignment.png)

  
## <a name="use-in-builds"></a>Użyj w kompilacjach

W tej sekcji zawarto informacje dotyczące korzystania z zadania wypychania konfiguracji aplikacji platformy Azure w potoku kompilacji usługi Azure DevOps.

1. Przejdź do strony potoku kompilacji, klikając **pozycję potoki potoki**  >  . Dokumentację potoków kompilacji można znaleźć [tutaj](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Jeśli tworzysz nowy potok kompilacji, w ostatnim kroku procesu na karcie **Przegląd** wybierz pozycję **Pokaż asystenta** po prawej stronie potoku.
      ![Zrzut ekranu przedstawia przycisk Pokaż Asystenta dla nowego potoku.](./media/new-pipeline-show-assistant.png)
      - Jeśli używasz istniejącego potoku kompilacji, kliknij przycisk **Edytuj** w prawym górnym rogu.
      ![Zrzut ekranu przedstawia przycisk Edytuj dla istniejącego potoku.](./media/existing-pipeline-show-assistant.png)
1. Wyszukaj zadanie **wypychania konfiguracji aplikacji platformy Azure** .
![Zrzut ekranu przedstawia okno dialogowe Dodawanie zadania z opcją wypychania konfiguracji aplikacji platformy Azure w polu wyszukiwania.](./media/add-azure-app-configuration-push-task.png)
1. Skonfiguruj parametry niezbędne do wykonania zadania w celu wypchnięcia wartości klucza z pliku konfiguracji do magazynu konfiguracji aplikacji. Wyjaśnienia parametrów są dostępne w poniższej sekcji **Parametry** i w etykietach narzędzi obok każdego parametru.
![Zrzut ekranu przedstawia parametry zadania wypychania konfiguracji aplikacji.](./media/azure-app-configuration-push-parameters.png)
1. Zapisz i zakolejkowaniaj kompilację. W dzienniku kompilacji zostaną wyświetlone wszystkie błędy, które wystąpiły podczas wykonywania zadania.

## <a name="use-in-releases"></a>Użyj w wersjach

W tej sekcji zawarto informacje dotyczące korzystania z zadania wypychania konfiguracji aplikacji platformy Azure w potokach wydań usługi Azure DevOps.

1. Przejdź do strony potoku wydania, wybierając pozycję wersje **potoków**  >  . Dokumentację potoków wydań można znaleźć [tutaj](/azure/devops/pipelines/release).
1. Wybierz istniejący potok wersji. Jeśli go nie masz, wybierz pozycję **+ Nowy** , aby utworzyć nowy.
1. Wybierz przycisk **Edytuj** w prawym górnym rogu, aby edytować potoku wydania.
1. Z listy rozwijanej **zadania** wybierz **etap** , do którego chcesz dodać zadanie. Więcej informacji na temat etapów można znaleźć [tutaj](/azure/devops/pipelines/release/environments).
![Zrzut ekranu przedstawia wybrany etap na liście rozwijanej zadania.](./media/pipeline-stage-tasks.png)
1. Kliknij **+** obok zadania, do którego chcesz dodać nowe zadanie.
![Zrzut ekranu przedstawia przycisk plus obok zadania.](./media/add-task-to-job.png)
1. W oknie dialogowym **Dodawanie zadań** wpisz polecenie **Konfiguracja aplikacji Azure wypchnij** do pola wyszukiwania i wybierz je.
1. Skonfiguruj niezbędne parametry w ramach zadania, aby wypchnąć kluczowe wartości z pliku konfiguracji do magazynu konfiguracji aplikacji. Wyjaśnienia parametrów są dostępne w poniższej sekcji **Parametry** i w etykietach narzędzi obok każdego parametru.
1. Zapisz i wydawanie kolejki. W dzienniku zlecenia zostaną wyświetlone wszystkie błędy, które wystąpiły podczas wykonywania zadania.

## <a name="parameters"></a>Parametry

Następujące parametry są używane przez zadanie wypychania konfiguracji aplikacji:

- **Subskrypcja platformy Azure**: Lista rozwijana zawierająca dostępne połączenia usługi platformy Azure. Aby zaktualizować i odświeżyć listę dostępnych połączeń usługi platformy Azure, kliknij przycisk **Odśwież subskrypcję platformy Azure** z prawej strony pola tekstowego.
- **Nazwa konfiguracji aplikacji**: Lista rozwijana, która ładuje dostępne magazyny konfiguracji w ramach wybranej subskrypcji. Aby zaktualizować i odświeżyć listę dostępnych magazynów konfiguracji, naciśnij przycisk **Odśwież nazwę konfiguracji aplikacji** z prawej strony pola tekstowego.
- **Ścieżka pliku konfiguracji**: ścieżka do pliku konfiguracji. Parametr **ścieżki pliku konfiguracji** rozpoczyna się w katalogu głównym repozytorium plików. Możesz przeglądać artefakt kompilacji, aby wybrać plik konfiguracji. ( `...` przycisk z prawej strony pola tekstowego). Obsługiwane formaty plików to: YAML, JSON, Properties. Poniżej znajduje się przykładowy plik konfiguracji w formacie JSON.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Separator**: separator używany do spłaszczania plików JSON i YML.
- **Głębokość**: głębokość, do której zostaną spłaszczone pliki JSON i YML.
- **Prefix**: ciąg, który jest dołączany na początku każdego klucza wypychanego do magazynu konfiguracji aplikacji.
- **Label**: ciąg, który jest dodawany do każdej wartości klucza jako etykieta w magazynie konfiguracji aplikacji.
- **Typ zawartości**: ciąg, który jest dodawany do każdej wartości klucza jako typ zawartości w magazynie konfiguracji aplikacji.
- **Tagi**: obiekt JSON w formacie `{"tag1":"val1", "tag2":"val2"}` , który definiuje Tagi, które są dodawane do każdej wartości klucza wypychanej do magazynu konfiguracji aplikacji.
- **Usuń wszystkie inne Key-Values w sklepie o określonym prefiksie i etykiecie**: wartość domyślna nie jest **zaznaczona**.
  - **Sprawdzono**: usuwa wszystkie wartości klucza w magazynie konfiguracji aplikacji, które pasują do określonego prefiksu i etykiety przed wypchnięciem nowych wartości klucza z pliku konfiguracyjnego.
  - **Niezaznaczone**: wypycha wszystkie wartości klucza z pliku konfiguracji do magazynu konfiguracji aplikacji i pozostawia wszystkie inne w magazynie konfiguracji aplikacji bez zmian.



## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi nieoczekiwany błąd, Dzienniki debugowania można włączyć, ustawiając zmienną potoku `system.debug` na `true` .

## <a name="faq"></a>Często zadawane pytania

**Jak można przekazać wiele plików konfiguracji?**

Utwórz wiele wystąpień zadania wypychania konfiguracji aplikacji platformy Azure w ramach tego samego potoku, aby wypchnąć wiele plików konfiguracji do magazynu konfiguracji aplikacji.

**Dlaczego otrzymuję błąd 409 podczas próby wypychania wartości klucza do mojego magazynu konfiguracji?**

Zostanie wyświetlony komunikat o błędzie 409 konfliktu, jeśli zadanie spróbuje usunąć lub zastąpić klucz-wartość, która jest zablokowana w magazynie konfiguracji aplikacji.
