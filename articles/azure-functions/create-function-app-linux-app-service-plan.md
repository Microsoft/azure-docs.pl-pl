---
title: Tworzenie aplikacji funkcji w systemie Linux z poziomu Azure Portal
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu witryny Azure Portal.
ms.topic: how-to
ms.date: 02/28/2019
ms.openlocfilehash: b2de36faf07ad661ff8817adc48b726f54990ceb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754114"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Tworzenie aplikacji funkcji w systemie Linux w planie Azure App Service

Usługa Azure Functions umożliwia obsługę funkcji w systemie Linux w domyślnym kontenerze usługi Azure App Service. W tym artykule przedstawiono sposób użycia [Azure Portal](https://portal.azure.com) do utworzenia aplikacji funkcji hostowanej w systemie Linux, która jest uruchamiana w [planie App Service](functions-scale.md#app-service-plan). Możesz również [skorzystać z własnego kontenera niestandardowego](functions-create-function-linux-custom-image.md).

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów. W tym artykule opisano tworzenie planu App Service podczas tworzenia aplikacji funkcji.

1. Wybierz przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure Portal, a następnie wybierz pozycję **Compute** > **Aplikacja funkcji**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Macintosh** | Linux | Aplikacja funkcji jest uruchamiana w systemie Linux. |
    | **Publikowanie** | Code | Używany jest domyślny kontener systemu Linux dla **stosu środowiska uruchomieniowego** . Wszystko, co musisz podać, to kod projektu aplikacji funkcji. Innym rozwiązaniem jest opublikowanie niestandardowego [obrazu platformy Docker](functions-create-function-linux-custom-image.md). |
    | **[Plan hostingu](functions-scale.md)** | Plan usługi App Service | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Po uruchomieniu w planie App Service można kontrolować [skalowanie aplikacji funkcji](functions-scale.md).  |
    | **Plan usługi App Service/lokalizacja** | Utwórz plan | Wybierz pozycję **Utwórz nową** i podaj nazwę **planu App Service** . Wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub w niemal innych usługach, do których dostęp ma funkcja. Wybierz żądaną **[warstwę cenową](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>W tym samym planie App Service nie można uruchamiać aplikacji funkcji systemu Linux ani Windows. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    | **[Magazyn](../storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Enabled (Włączony) | Application Insights jest domyślnie wyłączona. Zalecamy włączenie integracji Application Insights teraz i wybranie lokalizacji hostingu w najbliższej lokalizacji planu App Service. Jeśli chcesz to zrobić później, zobacz [Azure Functions monitorowania](functions-monitoring.md).  |

3. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

4. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

Następnie należy utworzyć funkcję w nowej aplikacji funkcji. Nawet po udostępnieniu aplikacji funkcji może upłynąć kilka minut, aby można było w pełni zainicjować.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

W tej sekcji pokazano, jak utworzyć funkcję w nowej aplikacji funkcji w portalu.

> [!NOTE]
> Środowisko programistyczne portalu może być przydatne podczas próby wypróbowania Azure Functions. W przypadku większości scenariuszy Rozważ możliwość lokalnego tworzenia funkcji i publikowania projektu w aplikacji funkcji przy użyciu [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) lub [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. W nowej aplikacji funkcji wybierz kartę **Przegląd** i po jej załadowaniu całkowicie wybierz pozycję **+ Nowa funkcja**.

    ![Utwórz nową funkcję na karcie Przegląd](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na karcie **Szybki Start** wybierz pozycję **w portalu**, a następnie wybierz pozycję **Kontynuuj**.

    ![Wybierz platformę do programowania funkcji.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Wybierz opcję **Element WebHook + interfejs API**, a następnie wybierz opcję **Utwórz**.

    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Funkcja zostanie utworzona przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP właściwego dla danego języka.

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)**, a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji, zobacz [Powiązania protokołu HTTP w usłudze Azure Functions](functions-bindings-http-webhook.md).
