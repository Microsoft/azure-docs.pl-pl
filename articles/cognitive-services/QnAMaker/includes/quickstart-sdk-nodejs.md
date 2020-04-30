---
title: 'Szybki Start: QnA Makera Biblioteka kliencka dla środowiska Node. js'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką kliencką QnA Maker dla środowiska Node. js.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 48038c8e7e8250190d79aba7901567e18881e912
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204035"
---
Użyj QnA Maker biblioteki klienta dla środowiska Node. js, aby:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego publikowania
* Zaczekaj na długo uruchomione zadanie
* Usuń bazę wiedzy

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | referencyjna[biblioteki kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [źródłowego (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | środowiska[Node. js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz tworzenia i punkt końcowy. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API usługi QnA Maker. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna (`F0`) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu QnA Maker platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla QnA Maker przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po pozyskaniu klucza i punktu końcowego z zasobu Pobierz wartości z Azure Portal dla nowego zasobu na stronie Szybki Start.

[Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)o `QNAMAKER_AUTHORING_KEY` nazwach i `QNAMAKER_ENDPOINT`. Można skopiować plik [ENV. przykład](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) do `.env` i używać zmiennych środowiskowych w tym pliku.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Uruchom `npm init -y` polecenie, aby utworzyć aplikację Node z `package.json` plikiem.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj wymagane i opcjonalne pakiety NPM:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

`package.json` Plik aplikacji jest aktualizowany z zależnościami. `dotenv` Jest to opcjonalne i służy do ustawiania zmiennych środowiskowych w pliku tekstowym. Nie sprawdzaj `.env` do kontroli źródła.


## <a name="object-model"></a>Model obiektów

Klient QnA Maker jest obiektem [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu elementu serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta Użyj właściwości [bazy wiedzy](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) , aby utworzyć i opublikować bazę wiedzy oraz zarządzać nią.

Zarządzaj bazą wiedzy, wysyłając obiekt JSON. W przypadku operacji natychmiastowych Metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedź jest IDENTYFIKATORem operacji. Wywoływanie [klienta. Operations. Details](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) — Metoda z identyfikatorem operacji w celu określenia [stanu żądania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta QnA Maker dla środowiska Node. js:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Pobierz opublikowany punkt końcowy](#get-published-endpoint)
* [Pobierz stan operacji](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Dodawanie zależności

Utwórz plik o nazwie `index.js`. Dodaj bibliotekę QnA Maker i zależności do pliku.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

|Zmienna środowiskowa|Zmienna Node. js|Przykład|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Klucz jest ciągiem znaków 32 i jest dostępny w Azure Portal na QnA Maker zasobu na stronie szybkiego startu. Ta wartość nie jest taka sama jak klucz punktu końcowego przewidywania.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Twój punkt końcowy tworzenia w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`zawiera **nazwę zasobu**. Nie jest to ten sam adres URL służący do wykonywania zapytań dotyczących punktu końcowego przewidywania.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Następnie Utwórz obiekt ApiKeyCredentials przy użyciu klucza i użyj go w punkcie końcowym, aby utworzyć obiekt [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) . Użyj obiektu klienta, aby uzyskać obiekt [klienta bazy wiedzy](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) .


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) z trzech źródeł:

* W przypadku **zawartości redakcyjnej**Użyj obiektu [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
* Dla **plików**Użyj obiektu [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) .
* W przypadku **adresów URL**Użyj listy ciągów.

Wywołaj metodę [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) z informacjami bazy wiedzy. Informacje bazy wiedzy są zasadniczo obiektem JSON.

Gdy metoda Create zwraca wartość, Przekaż zwrócony identyfikator operacji do metody [wait_for_operation](#get-status-of-an-operation) , aby sondować stan. Metoda wait_for_operation zwraca po zakończeniu operacji. Przeanalizuj `resourceLocation` wartość nagłówka zwróconej operacji, aby uzyskać nowy identyfikator bazy wiedzy.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Upewnij się, że Dołącz [`wait_for_operation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, w celu pomyślnego utworzenia bazy wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując informacje o IDENTYFIKATORze bazy wiedzy i [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) zawierającym obiekty [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)i [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO do metody [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) . DTO są również zasadniczo obiektami JSON. Użyj metody [wait_for_operation](#get-status-of-an-operation) , aby określić, czy aktualizacja zakończyła się pomyślnie.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Upewnij się, że Dołącz [`wait_for_operation`](#get-status-of-an-operation) funkcję, do której odwołuje się powyższy kod, aby pomyślnie zaktualizować bazę wiedzy.

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym. Sprawdź kod odpowiedzi HTTP, aby sprawdzić, czy publikowanie zakończyło się pomyślnie.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Pobierz opublikowany punkt końcowy

Po opublikowaniu bazy wiedzy uzyskaj dostęp do opublikowanej bazy wiedzy za pośrednictwem interfejsu API generateAnswer środowiska uruchomieniowego przewidywania zapytań. W tym celu należy dysponować kluczem punktu końcowego środowiska uruchomieniowego. Jest to inne niż klucz tworzenia.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Dwa klucze punktu końcowego są zwracane z wywołania. W celu uzyskania dostępu do punktu końcowego środowiska uruchomieniowego jest wymagany tylko jeden.

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) z parametrem identyfikatora bazy wiedzy.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . Użyj [identyfikatora operacji](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

Wywołanie _delayTimer_ w poniższym bloku kodu służy do symulowania logiki ponawiania. Zamień ten program na własną logikę ponawiania.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node index.js` polecenia z katalogu aplikacji.

Wszystkie fragmenty kodu w tym artykule są [dostępne](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) i można je uruchamiać jako pojedynczy plik.

```console
node index.js
```

Program drukuje stan konsoli:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)