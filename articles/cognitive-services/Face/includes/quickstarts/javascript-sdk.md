---
title: Biblioteka klienta języka JavaScript — Szybki Start
description: Użyj biblioteki klienta twarzy dla języka JavaScript, aby wykrywać twarze, znajdować podobne (Wyszukiwanie twarzy według obrazu), identyfikować twarze (Wyszukiwanie twarzy) i migrować dane twarzy.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: b4a63f76cbcd9e98295f5edcf7ff2d06979e6556
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244633"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Przewodnik Szybki Start: Tworzenie biblioteki klienckiej dla języka JavaScript

Wprowadzenie do rozpoznawania twarzy przy użyciu biblioteki klienckiej twarzy dla języka JavaScript. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta programu Front dla języka JavaScript, aby:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie grupy osób](#create-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)

[Dokumentacja](/javascript/api/@azure/cognitiveservices-face/)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face)  |  [Przykłady](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Najnowsza wersja [Node.js](https://nodejs.org/en/)
* Gdy masz subskrypcję platformy Azure, [Utwórz zasób czołowy](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z Face API. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Zainstaluj `ms-rest-azure` pakiety i `azure-cognitiveservices-face` npm:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](), która zawiera przykłady kodu w tym przewodniku Szybki Start.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. 

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób czołowy utworzony w sekcji **wymagań wstępnych** został pomyślnie wdrożony, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre z najważniejszych funkcji biblioteki klienckiej platformy .NET programu Front:

|Nazwa|Opis|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[Rozpoznawanie twarzy](/javascript/api/@azure/cognitiveservices-face/face)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |

## <a name="code-examples"></a>Przykłady kodu

Poniższe fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej programu Front for .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie grupy osób](#create-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), która zawiera przykłady kodu w tym przewodniku Szybki Start.

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials)** z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient)** .

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Zadeklaruj wartości globalne i funkcję pomocnika

Następujące wartości globalne są potrzebne w przypadku kilku operacji, które zostaną później dodane.

Adres URL wskazuje folder obrazów przykładowych. Identyfikator UUID będzie używany jako nazwa i identyfikator dla utworzonej osoby.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Użyj następującej funkcji, aby poczekać na zakończenie szkolenia przez osobę.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

### <a name="get-detected-face-objects"></a>Pobieranie wykrytych obiektów czołowych

Utwórz nową metodę wykrywania twarzy. `DetectFaceExtract`Metoda przetwarza trzy obrazy pod podanym adresem URL i tworzy listę obiektów **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** w pamięci programu. Lista wartości **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** określa funkcje do wyodrębnienia. 

`DetectFaceExtract`Metoda następnie analizuje i drukuje dane atrybutów dla każdej wykrytej cechy. Każdy atrybut musi być określony osobno w wywołaniu oryginalnego interfejsu API wykrywania kroju (na liście **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** ). Poniższy kod przetwarza każdy atrybut, ale prawdopodobnie trzeba będzie tylko użyć jednego lub kilku.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Możesz również wykrywać twarze w obrazie lokalnym. Zobacz metody [,](/javascript/api/@azure/cognitiveservices-face/face) takie jak [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte buźki (Źródło) i przeszukuje zestaw innych twarzy (target), aby znaleźć dopasowania (Wyszukiwanie twarzy według obrazu). Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw Zdefiniuj metodę wykrywania drugiej czołowej. Należy wykryć twarze w obrazach, zanim będzie można je porównać, a ta metoda wykrywania jest zoptymalizowana pod kątem operacji porównania. Nie wyodrębnimy szczegółowych atrybutów kroju, takich jak w powyższej sekcji, i używa innego modelu rozpoznawania.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Znajdź dopasowania

Poniższa metoda wykrywa twarze w zestawie obrazów docelowych i w jednym obrazie źródłowym. Następnie porównuje je i odnajduje wszystkie obrazy docelowe podobne do obrazu źródłowego. Na koniec drukuje szczegóły dopasowania do konsoli programu.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identyfikowanie kroju

Operacja [identyfikowania](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) pobiera obraz osoby (lub wielu osób) i szuka tożsamości każdej twarzy na obrazie (wyszukiwanie rozpoznawania twarzy). Porównuje każdą wykrytą twarzą z [osobą](/javascript/api/@azure/cognitiveservices-face/persongroup), która jest bazą danych różnych obiektów [osób](/javascript/api/@azure/cognitiveservices-face/person) , których funkcje twarzy są znane. Aby wykonać operację identyfikowania, należy najpierw utworzyć i przeszkolić [osobę](/javascript/api/@azure/cognitiveservices-face/persongroup).

### <a name="add-faces-to-person-group"></a>Dodawanie twarzy do grupy osób

Utwórz następującą funkcję, aby dodać twarzy do tej [osoby](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Poczekaj na szkolenie grupy osób

Utwórz następującą funkcję pomocnika, aby poczekać na zakończenie szkolenia przez grupę osób.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Tworzenie grupy osób

Następujący kod:
- Tworzy [odbiorcę](/javascript/api/@azure/cognitiveservices-face/persongroup)
- Dodaje twarze do grupy osób przez wywołanie `AddFacesToPersonGroup` , która została wcześniej zdefiniowana.
- Pociąga za siebie grupę osób.
- Identyfikuje powierzchnie w grupie osób.

Ta grupa **osób** i skojarzone z nią obiekty **osób** są teraz gotowe do użycia w operacjach sprawdzania, identyfikowania lub grupowania.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Można również utworzyć **odbiorcę** z obrazów lokalnych. Zobacz metody [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) , takie jak [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Główną

Na koniec Utwórz `main` funkcję i Wywołaj ją.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak używać biblioteki klienta twarzy dla języka JavaScript, aby wykonywać podstawowe zadania rozpoznawania twarzy. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie Face API (JavaScript)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
