---
title: Połącz się z punktami końcowymi REST z Azure Logic Apps
description: Monitoruj punkty końcowe REST w zautomatyzowanych zadaniach, procesach i przepływach pracy przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 810aaae9634a7de8d07b6d49edd0c6c2eda96754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730885"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Wywoływanie punktów końcowych REST przy użyciu Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego łącznika protokołu HTTP + Swagger można zautomatyzować przepływy pracy, które regularnie wywołują każdy punkt końcowy REST za pośrednictwem [pliku struktury Swagger](https://swagger.io) , tworząc Aplikacje logiki. Wyzwalacz i akcja środowiska Swagger protokołu HTTP działają tak samo jak [wyzwalacz http i akcja](connectors-native-http.md) , ale zapewniają lepsze środowisko projektanta aplikacji logiki, uwidaczniając strukturę interfejsu API i dane wyjściowe opisane przez plik Swagger. Aby zaimplementować wyzwalacz sondowania, postępuj zgodnie z wzorcem sondowania opisanym w temacie [Tworzenie niestandardowych interfejsów API do wywoływania innych interfejsów API, usług i systemów z usługi Logic Apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL pliku struktury Swagger (nie OpenAPI), który opisuje docelowy punkt końcowy REST

  Zwykle punkt końcowy REST musi spełniać te kryteria, aby łącznik działał:

  * Plik struktury Swagger musi być hostowany na adresie URL protokołu HTTPS, który jest dostępny publicznie.
  
  * Plik struktury Swagger musi zawierać `operationID` dla każdej operacji w definicji. Jeśli nie, łącznik pokazuje tylko ostatnią operację w pliku struktury Swagger. 

  * Plik struktury Swagger musi mieć włączoną funkcję [udostępniania zasobów między źródłami (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

  Aby odwołać się do pliku struktury Swagger, który nie jest hostowany lub który nie spełnia wymagań dotyczących zabezpieczeń i między źródłami danych, można [przekazać plik Swagger do kontenera obiektów BLOB na koncie usługi Azure Storage](#host-swagger)i włączyć funkcję CORS dla tego konta magazynu, aby można było odwołać się do pliku.

  W przykładach w tym temacie użyto [Face API Cognitive Services](../cognitive-services/face/overview.md), który wymaga [Cognitive Services konta i klucza dostępu](../cognitive-services/cognitive-services-apis-create-account.md).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, z której ma zostać wywołany docelowy punkt końcowy. Aby rozpocząć od wyzwalacza protokołu HTTP + Swagger, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji protokołu HTTP + Swagger, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. Ten przykład używa wyzwalacza protokołu HTTP + Swagger jako pierwszego kroku.

## <a name="add-an-http--swagger-trigger"></a>Dodawanie wyzwalacza protokołu HTTP + Swagger

Ten wbudowany wyzwalacz wysyła żądanie HTTP do adresu URL pliku struktury Swagger opisującego interfejs API REST i zwraca odpowiedź zawierającą zawartość tego pliku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W projektancie w polu wyszukiwania wprowadź ciąg "Swagger" jako filtr. Z listy **wyzwalacze** Wybierz wyzwalacz **http + Swagger** .

   ![Wybieranie wyzwalacza HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. W polu **adres URL punktu końcowego struktury Swagger** wprowadź adres URL pliku struktury Swagger, a następnie wybierz przycisk **dalej**.

   Ten przykład używa adresu URL programu Swagger, który znajduje się w regionie zachodnie stany USA dla [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki z wyzwalaczem "H T T P + Swagger" oraz Właściwość "punkt końcowy struktury Swagger U R L" ustawioną na wartość U R L.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Gdy Projektant pokazuje operacje opisane przez plik Swagger, wybierz operację, której chcesz użyć.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki z wyzwalaczem "H T T P + Swagger" oraz listę wyświetlającą operacje programu Swagger.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Podaj wartości parametrów wyzwalacza, które różnią się w zależności od wybranej operacji, która ma zostać uwzględniona w wywołaniu punktu końcowego. Skonfiguruj Cykl dla tego, jak często wyzwalacz ma wywołać punkt końcowy.

   Ten przykład zmienia nazwę wyzwalacza na "wyzwalacz HTTP + struktura Swagger: wykrywanie kroju i", aby krok miał bardziej opisową nazwę.

   ![Zrzut ekranu przedstawiający projektanta aplikacji logiki z wyzwalaczem "H T T P + Swagger", w którym jest wyświetlana operacja "wykrywanie kroju".](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Aby dodać inne dostępne parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

   Aby uzyskać więcej informacji o typach uwierzytelniania dostępnych dla protokołu HTTP + Swagger, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki przy użyciu akcji uruchamianych podczas uruchamiania wyzwalacza.

1. Po zakończeniu Pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="add-an-http--swagger-action"></a>Dodawanie akcji protokołu HTTP + Swagger

Ta wbudowana akcja wysyła żądanie HTTP do adresu URL pliku struktury Swagger opisującego interfejs API REST i zwraca odpowiedź zawierającą zawartość tego pliku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W kroku, w którym chcesz dodać akcję protokołu HTTP + Swagger, wybierz pozycję **nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W projektancie w polu wyszukiwania wprowadź ciąg "Swagger" jako filtr. Z listy **Akcje** wybierz akcję **http + Swagger** .

    ![Wybieranie akcji protokołu HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. W polu **adres URL punktu końcowego struktury Swagger** wprowadź adres URL pliku struktury Swagger, a następnie wybierz przycisk **dalej**.

   Ten przykład używa adresu URL programu Swagger, który znajduje się w regionie zachodnie stany USA dla [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Wprowadź adres URL dla punktu końcowego struktury Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Gdy Projektant pokazuje operacje opisane przez plik Swagger, wybierz operację, której chcesz użyć.

   ![Operacje w pliku struktury Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Podaj wartości parametrów akcji, które różnią się w zależności od wybranej operacji, która ma zostać uwzględniona w wywołaniu punktu końcowego.

   Ten przykład nie ma parametrów, ale zmienia nazwę akcji na "Akcja protokołu HTTP + Swagger:" — zidentyfikuj ", aby krok miał bardziej opisową nazwę.

   ![Szczegóły operacji](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Aby dodać inne dostępne parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

   Aby uzyskać więcej informacji o typach uwierzytelniania dostępnych dla protokołu HTTP + Swagger, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Po zakończeniu Pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Hostowanie struktury Swagger w usłudze Azure Storage

Można odwołać się do pliku struktury Swagger, który nie jest hostowany lub który nie spełnia wymagań dotyczących zabezpieczeń i między źródłami, przekazując ten plik do kontenera obiektów BLOB na koncie usługi Azure Storage i włączając funkcję CORS na tym koncie magazynu. Aby tworzyć, konfigurować i przechowywać pliki struktury Swagger w usłudze Azure Storage, wykonaj następujące kroki:

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md).

1. Teraz Włącz funkcję CORS dla obiektu BLOB. W menu konta magazynu wybierz pozycję **CORS**. Na karcie **BLOB Service** Określ te wartości, a następnie wybierz pozycję **Zapisz**.

   | Właściwość | Wartość |
   |----------|-------|
   | **Dozwolone źródła** | `*` |
   | **Dozwolone metody** | `GET`, `HEAD`, `PUT` |
   | **Dozwolone nagłówki** | `*` |
   | **Uwidocznione nagłówki** | `*` |
   | **Maksymalny wiek** (w sekundach) | `200` |
   |||

   Chociaż w tym przykładzie użyto [Azure Portal](https://portal.azure.com), można użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/)lub automatycznie skonfigurować to ustawienie przy użyciu tego przykładowego [skryptu programu PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Utwórz kontener obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). W okienku **Przegląd** kontenera wybierz pozycję **Zmień poziom dostępu**. Z listy **poziom dostępu publicznego** wybierz pozycję **obiekt BLOB (Anonimowy dostęp do odczytu tylko dla obiektów BLOB)**, a następnie wybierz **przycisk OK**.

1. [Przekaż plik Swagger do kontenera obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)za pomocą [Azure Portal](https://portal.azure.com) lub [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

1. Aby odwołać się do pliku w kontenerze obiektów blob, Pobierz adres URL HTTPS następujący po tym formacie, w którym jest rozróżniana wielkość liter, od Eksplorator usługi Azure Storage:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Dokumentacja łączników

Poniżej znajduje się więcej informacji na temat danych wyjściowych wyzwalacza lub akcji protokołu HTTP + Swagger. Wywołanie HTTP + Swagger zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| nagłówka | object | Nagłówki żądania |
| body | object | Obiekt JSON | Obiekt z zawartością treści z żądania |
| kod stanu | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowano |
| 400 | Złe żądanie |
| 401 | Brak autoryzacji |
| 403 | Forbidden |
| 404 | Nie znaleziono |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
