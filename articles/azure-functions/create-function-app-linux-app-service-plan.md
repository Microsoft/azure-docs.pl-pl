---
title: Tworzenie aplikacji funkcji w systemie Linux z poziomu Azure Portal
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure w systemie Linux przy użyciu Azure Portal.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 20390239ed58e42749e9a3bae472a2f3f6324bb2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181258"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Tworzenie aplikacji funkcji w systemie Linux w planie Azure App Service

Usługa Azure Functions umożliwia obsługę funkcji w systemie Linux w domyślnym kontenerze usługi Azure App Service. W tym artykule przedstawiono sposób użycia [Azure Portal](https://portal.azure.com) do utworzenia aplikacji funkcji hostowanej w systemie Linux, która jest uruchamiana w [planie App Service](functions-scale.md#app-service-plan). Możesz również [skorzystać z własnego kontenera niestandardowego](functions-create-function-linux-custom-image.md).

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów. W tym artykule opisano tworzenie planu App Service podczas tworzenia aplikacji funkcji.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **obliczeniowe**  >  **aplikacja funkcji**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Tworzenie aplikacji funkcji w witrynie Azure Portal":::

1. Na stronie **podstawowe** Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| **Kod** (domyślny) | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz pozycję **.NET Core** dla języków C# i F #. |
    |**Wersja**| Numer wersji | Wybierz wersję zainstalowanego środowiska uruchomieniowego.  |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Strona Podstawy":::

1. Wybierz pozycję **Dalej: hosting**. Na stronie **hosting** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../azure-functions/functions-scale.md#storage-account-requirements). |
    |**System operacyjny**| **Linux** | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. |
    | **[Planowanie](../azure-functions/functions-scale.md)** | **Zużycie (bezserwerowe)** | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. W domyślnym planie **Zużycie** zasoby są dodawane dynamicznie zgodnie z wymaganiami funkcji. W tym przypadku hostingu [bezserwerowego](https://azure.microsoft.com/overview/serverless-computing/) płacisz tylko za czas wykonywania funkcji. W przypadku uruchomienia w ramach planu usługi App Service musisz zarządzać [skalowaniem aplikacji funkcji](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Strona hostingu":::

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Tak** (domyślnie) | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie lub wybierając pozycję **Utwórz nowe**, możesz zmienić nazwę Application Insights lub wybrać inny region w obszarze [geograficznym platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w którym chcesz przechowywać dane. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Strona monitorowania":::

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i obejrzyj komunikat **wdrażanie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

    ![Powiadomienie dotyczące wdrożenia](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Nawet po udostępnieniu aplikacji funkcji może upłynąć kilka minut, aby można było w pełni zainicjować.

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalacza HTTP

W tej sekcji pokazano, jak utworzyć funkcję w nowej aplikacji funkcji w portalu.

> [!NOTE]
> Środowisko programistyczne portalu może być przydatne podczas próby wypróbowania Azure Functions. W przypadku większości scenariuszy Rozważ możliwość lokalnego tworzenia funkcji i publikowania projektu w aplikacji funkcji przy użyciu [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) lub [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. W menu po lewej stronie okna **funkcje** wybierz pozycję **funkcje**, a następnie wybierz pozycję **Dodaj** z górnego menu. 
 
1. W oknie **Nowa funkcja** wybierz pozycję **wyzwalacz http**.

    ![Wybierz funkcję wyzwalacza HTTP](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. W oknie **Nowa funkcja** Zaakceptuj nazwę domyślną **nowej funkcji** lub wprowadź nową nazwę. 

1. Z listy rozwijanej **poziom autoryzacji** wybierz pozycję **anonimowe** , a następnie wybierz pozycję **Utwórz funkcję**.

    Platforma Azure tworzy funkcję wyzwalacza HTTP. Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji wyzwalacza HTTP wybierz pozycję **Kod + test** z menu po lewej stronie, a następnie wybierz pozycję **Pobierz adres URL funkcji** z górnego menu.

    ![Wybierz adres URL funkcji Get](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. W oknie dialogowym **pobieranie adresu URL funkcji** wybierz opcję **domyślne** z listy rozwijanej, a następnie wybierz ikonę **Kopiuj do schowka** . 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `?name=<your_name>` na końcu tego adresu URL i naciśnij klawisz ENTER, aby uruchomić żądanie. 

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

1. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia, Wróć do strony **Code + test** w portalu i rozwiń strzałkę **dzienniki** w dolnej części strony.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalacza HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji, zobacz [Powiązania protokołu HTTP w usłudze Azure Functions](functions-bindings-http-webhook.md).