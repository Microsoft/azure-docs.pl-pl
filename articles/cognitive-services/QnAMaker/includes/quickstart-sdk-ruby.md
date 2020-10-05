---
title: 'Szybki Start: QnA Maker bibliotekę kliencką dla języka Ruby'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką kliencką QnA Maker dla języka Ruby.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: ef0db373dc6faaa470470b8169fdb6ae61aa8dde
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90982735"
---
Użyj biblioteki klienta QnA Maker dla języka Ruby, aby:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska przewidywania
* Zaczekaj na długo uruchomione zadanie
* Pobierz baza wiedzy
* Uzyskiwanie odpowiedzi
* Usuń bazę wiedzy

[Kod źródłowy biblioteki (Tworzenie)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated)  |  [Kod źródłowy biblioteki (środowisko uruchomieniowe)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated)  |  [Pakiet (Tworzenie)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker)  |  [Pakiet (środowisko uruchomieniowe)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime)  |  [Przykłady Ruby](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2. x](https://www.ruby-lang.org/)
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz tworzenia i punkt końcowy. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API usługi QnA Maker. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-libraries"></a>Instalowanie bibliotek klienckich

Po zainstalowaniu instalacji języka Ruby można zainstalować biblioteki klienckie przy użyciu programu:

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>Tworzenie nowej aplikacji Ruby

Utwórz nowy plik o nazwie `quickstart.rb` i zaimportuj następujące biblioteki.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

> [!IMPORTANT]
> Przejdź do Azure Portal i Znajdź klucz i punkt końcowy dla zasobu QnA Maker utworzonego w sekcji wymagania wstępne. Zostaną one umieszczone na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**.
> Do utworzenia bazy danych jest potrzebny cały klucz. Potrzebna jest tylko nazwa zasobu z punktu końcowego. Format to `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` .
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Storage udostępnia bezpieczny Magazyn kluczy.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>Modele obiektów

QnA Maker używa dwóch różnych modeli obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem, który umożliwia tworzenie, publikowanie i pobieranie bazy wiedzy oraz zarządzanie nią.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** to obiekt służący do wykonywania zapytań względem bazy wiedzy z interfejsem API GenerateAnswer i wysyłania nowych sugerowanych pytań za pomocą interfejsu API uczenia (w ramach [aktywnego uczenia](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Model obiektów QnAMakerClient

QnA Maker tworzenia klienta jest obiektem [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) , który jest uwierzytelniany na platformie Azure za pomocą MsRest:: serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta Użyj metod właściwości [bazy](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) wiedzy klienta, aby utworzyć i opublikować bazę wiedzy oraz zarządzać nią.

W przypadku operacji natychmiastowych Metoda zwykle zwraca wynik, jeśli istnieje. W przypadku długotrwałych operacji odpowiedź jest obiektem [operacji](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) . Wywołaj metodę [Operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) z `operation.operation_id` wartością, aby określić [stan żądania](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb).

### <a name="qnamakerruntimeclient-object-model"></a>Model obiektów QnAMakerRuntimeClient

QnA Maker środowiska uruchomieniowego jest obiektem [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) .

Po opublikowaniu bazy wiedzy przy użyciu klienta tworzenia Użyj metody [Runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) klienta środowiska uruchomieniowego, aby uzyskać odpowiedź z bazy wiedzy.

Po wywołaniu `generate_answer` należy przekazać skrót dla `custom_headers` opcjonalnego parametru. Ten skrót powinien zawierać klucz `Authorization` i wartość `EndpointKey YOUR_ENDPOINT_KEY` . Dla wartości YOUR_ENDPOINT_KEY Użyj klienta tworzenia do wywołania [endpoint_keys. Get _keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32).

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Uwierzytelnianie klienta w celu tworzenia bazy wiedzy

Utwórz wystąpienie klienta przy użyciu punktu końcowego tworzenia i klucza subskrypcji.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) z trzech źródeł:

* W przypadku **zawartości redakcyjnej**Użyj obiektu [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb) .
    * Aby użyć metadanych i monitów uzupełniających, użyj kontekstu redakcyjnego, ponieważ te dane są dodawane na indywidualnym poziomie pary QnA.
* Dla **plików**Użyj obiektu [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) . FileDTO zawiera nazwę pliku, a także publiczny adres URL w celu uzyskania dostępu do pliku.
* W przypadku **adresów URL**Użyj listy ciągów do reprezentowania publicznie dostępnych adresów URL.

Wywołaj metodę [Create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) , a następnie Przekaż `operation_id` Właściwość zwróconej operacji do metody [Operations.get_details](#get-status-of-an-operation) w celu sondowania stanu.

Ostatni wiersz poniższego kodu zwraca identyfikator bazy wiedzy.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, wywołując bazę wiedzy [. Update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) i przekazując ją przy użyciu identyfikatora bazy wiedzy i obiektu [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) . Ten obiekt z kolei może zawierać:
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

Przekaż `operation_id` Właściwość zwracanej operacji do metody [Operations.get_details](#get-status-of-an-operation) w celu sondowania stanu.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj metody z bazy [wiedzy. Download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) , aby pobrać bazę danych jako listę [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb). _Nie_ jest to równoznaczne z eksportem QNA Maker portalu ze strony **ustawień** , ponieważ wynik tej metody nie jest plikiem TSV.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [Baza wiedzy. publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego zapytania

Po opublikowaniu bazy wiedzy potrzebny jest klucz punktu końcowego środowiska uruchomieniowego, aby wykonać zapytanie o wiedzę. Nie jest to ten sam klucz subskrypcji użyty do utworzenia klienta tworzenia.

Użyj metody [endpoint_keys. Get _keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) , aby pobrać obiekt [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) .

Użyj jednej z właściwości klucza zwracanego w obiekcie, aby wykonać zapytanie dotyczące bazy wiedzy.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Uwierzytelnianie środowiska uruchomieniowego w celu wygenerowania odpowiedzi

Utwórz [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) , aby wysłać zapytanie do bazy wiedzy w celu wygenerowania odpowiedzi lub pouczenia się z aktywnego uczenia.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

Skorzystaj z QnAMakerRuntimeClient, aby uzyskać odpowiedzi ze wiedzy lub wysłać nowe sugerowane pytania do bazy wiedzy w celu uzyskania [aktywnego uczenia](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

Wygeneruj odpowiedź z opublikowanej bazy wiedzy przy użyciu metody RuntimeClient. Runtime. generateAnswer. Ta metoda akceptuje identyfikator bazy wiedzy i QueryDTO. Uzyskuj dostęp do dodatkowych właściwości QueryDTO, takich jak Top i Context, które mają być używane w rozmowie bot.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

Jest to prosty przykład zapytania dotyczącego bazy wiedzy. Aby zrozumieć zaawansowane scenariusze zapytań, zapoznaj się z [innymi przykładami zapytań](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody Base [. Delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) z parametrem identyfikatora bazy wiedzy.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) . Użyj identyfikatora operacji z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>Uruchamianie aplikacji

Oto główna Metoda dla aplikacji.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

Uruchom aplikację za pomocą polecenia Ruby w pliku szybkiego startu.

```console
ruby quickstart.rb
```

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb).
