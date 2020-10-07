---
title: 'Szybki Start: QnA Maker z interfejsami API REST dla Node.js'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z interfejsami API REST QnA Maker Node.js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: ef17f08677d715292a8ee49621156d130e2f5a6b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777491"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Szybki Start: QnA Maker interfejsów API REST dla Node.js

Rozpocznij pracę z interfejsami API REST QnA Maker Node.js. Wykonaj następujące kroki, aby wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.

Użyj QnA Maker interfejsów API REST, aby Node.js do:

* Tworzenie bazy wiedzy
* Zastępowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Usuwanie bazy wiedzy
* Pobieranie bazy wiedzy
* Pobierz stan operacji

[Dokumentacja](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  referencyjna [PrzykładyNode.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org).
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu QnA Maker platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla QnA Maker przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po uzyskaniu klucza z zasobu [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla zasobu o nazwie `QNAMAKER_RESOURCE_KEY` i `QNAMAKER_AUTHORING_ENDPOINT` . Użyj wartości klucza i punktu końcowego znajdujących się na stronie **szybkiego startu** zasobu w Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init -y` polecenie, aby utworzyć plik węzła `package.json` .

```console
npm init -y
```

Dodaj `reqeuestretry` pakiety i `request` npm:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności w przypadku QnA Maker interfejsów API REST dla Node.js:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Zastępowanie bazy wiedzy](#replace-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Pobieranie bazy wiedzy](#download-the-knowledge-base)
* [Pobierz stan operacji](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Dodawanie zależności

Utwórz plik o nazwie `rest-apis.js` i Dodaj następujące zależności.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Dodawanie funkcji narzędzi

Dodaj następujące funkcje narzędziowe.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Dodawanie informacji o zasobach platformy Azure

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

Ustaw następujące wartości środowiskowe:

* `QNAMAKER_RESOURCE_KEY` - **Klucz** jest ciągiem znaków 32 i jest dostępny w Azure Portal na QNA Maker zasobu na stronie **Szybki Start** . Ta wartość nie jest taka sama jak klucz punktu końcowego przewidywania.
* `QNAMAKER_AUTHORING_ENDPOINT` — Twój punkt końcowy tworzenia w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` zawiera **nazwę zasobu**. Nie jest to ten sam adres URL służący do wykonywania zapytań dotyczących punktu końcowego przewidywania.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi utworzone na podstawie obiektu JSON:

* **Zawartość redakcyjna**.
* **Pliki** — pliki lokalne, które nie wymagają żadnych uprawnień.
* **Adresy URL** — publicznie dostępne adresy URL.

Użyj [interfejsu API REST, aby utworzyć bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Zastępowanie bazy wiedzy

Zastępowanie [bazy wiedzy przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Publikowanie bazy wiedzy. Ten proces sprawia, że baza wiedzy jest dostępna z punktu końcowego przewidywania zapytań HTTP.

[Publikowanie bazy wiedzy przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>Pobierz bazę wiedzy

Użyj [interfejsu API REST, aby pobrać bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Gdy skończysz korzystać z bazy wiedzy, usuń ją.

Użyj [interfejsu API REST, aby usunąć bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Długotrwałe procesy, takie jak proces tworzenia, zwracają identyfikator operacji, który należy sprawdzić przy użyciu oddzielnego wywołania interfejsu API REST. Ta funkcja przyjmuje treść odpowiedzi tworzenia. Ważnym kluczem jest `operationState` , który określa, czy należy kontynuować sondowanie.

Użyj [interfejsu API REST, aby monitorować operacje w bazie wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Dodaj metodę Main

Dodaj następującą `main` metodę.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node rest-apis.js` polecenia z katalogu aplikacji.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Samouczek: Tworzenie i odpowiadanie na KB](../tutorials/create-publish-query-in-portal.md)

* [Co to jest interfejs API usługi QnA Maker?](../Overview/overview.md)
* [Edytowanie bazy wiedzy](../how-to/edit-knowledge-base.md)
* [Pobierz analizę użycia](../how-to/get-analytics-knowledge-base.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).
