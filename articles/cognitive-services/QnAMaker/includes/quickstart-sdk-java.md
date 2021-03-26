---
title: 'Szybki Start: QnA Makera Biblioteka kliencka dla języka Java'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z biblioteką klienta QnA Maker dla środowiska Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 755e4fcb9d5b9f1711bc615a29489037ea377d43
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609431"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Użyj biblioteki klienta QnA Maker dla języka Java, aby:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska przewidywania
* Zaczekaj na długo uruchomione zadanie
* Pobierz baza wiedzy
* Uzyskaj odpowiedź z bazy wiedzy
* Usuń bazę wiedzy

[Kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker)  |  źródłowy biblioteki [Pakiet](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Użyj biblioteki klienta QnA Maker dla języka Java, aby:

* Tworzenie bazy wiedzy
* Aktualizowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Zaczekaj na długo uruchomione zadanie
* Pobierz baza wiedzy
* Uzyskaj odpowiedź z bazy wiedzy
* Usuń bazę wiedzy

[Kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker)  |  źródłowy biblioteki [Pakiet](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Zestaw [JDK](https://www.oracle.com/java/technologies/javase-downloads.html).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz tworzenia i punkt końcowy. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API usługi QnA Maker. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Zestaw [JDK](https://www.oracle.com/java/technologies/javase-downloads.html).
* Gdy masz subskrypcję platformy Azure, Utwórz [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal, aby uzyskać klucz tworzenia i punkt końcowy.
    * Uwaga: Pamiętaj, aby zaznaczyć pole wyboru **zarządzane** .
    * Po wdrożeniu zasobów QnA Maker wybierz pozycję **Przejdź do zasobu**. Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API usługi QnA Maker. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

---

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-libraries"></a>Instalowanie bibliotek klienckich

Po zainstalowaniu języka Java można zainstalować biblioteki klienckie przy użyciu programu [Maven](https://maven.apache.org/) z [repozytorium MVN](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker).

### <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

Utwórz nowy plik o nazwie `quickstart.java` i zaimportuj następujące biblioteki.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

- Korzystamy z klucza subskrypcji i tworzenia klucza interchangably. Aby uzyskać więcej informacji na temat klucza tworzenia, należy użyć [klawiszy w QNA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- Wartość QNA_MAKER_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Przejdź do Azure Portal i Znajdź zasób QnA Maker, który został utworzony w sekcji wymagania wstępne. Kliknij pozycję **klucze i punkt końcowy** , w obszarze **Zarządzanie zasobami** , aby zlokalizować klucz tworzenie (subskrypcja) i punkt końcowy QNA Maker.

 ![Punkt końcowy QnA Maker tworzenia](../media/keys-endpoint.png)

- Wartość QNA_MAKER_RUNTIME_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.azurewebsites.net` . Przejdź do Azure Portal i Znajdź zasób QnA Maker, który został utworzony w sekcji wymagania wstępne. Kliknij pozycję **Eksportuj szablon** w obszarze **Automatyzacja** , aby zlokalizować punkt końcowy środowiska uruchomieniowego.

 ![Punkt końcowy QnA Maker środowiska uruchomieniowego](../media/runtime-endpoint.png)
   
- W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](../../../key-vault/general/overview.md) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

- Korzystamy z klucza subskrypcji i tworzenia klucza interchangably. Aby uzyskać więcej informacji na temat klucza tworzenia, należy użyć [klawiszy w QNA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- Wartość QNA_MAKER_ENDPOINT ma format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Przejdź do Azure Portal i Znajdź zasób QnA Maker, który został utworzony w sekcji wymagania wstępne. Kliknij pozycję **klucze i punkt końcowy** , w obszarze **Zarządzanie zasobami** , aby zlokalizować klucz tworzenie (subskrypcja) i punkt końcowy QNA Maker.

 ![Punkt końcowy QnA Maker tworzenia](../media/keys-endpoint.png)
 
- W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [usługa Azure Key](../../../key-vault/general/overview.md) Storage udostępnia bezpieczny Magazyn kluczy.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>Modele obiektów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

QnA Maker używa dwóch różnych modeli obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem, który umożliwia tworzenie, publikowanie i pobieranie bazy wiedzy oraz zarządzanie nią.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** to obiekt służący do wykonywania zapytań względem bazy wiedzy z interfejsem API GenerateAnswer i wysyłania nowych sugerowanych pytań za pomocą interfejsu API uczenia (w ramach [aktywnego uczenia](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

QnA Maker używa następującego modelu obiektów:
* **[QnAMakerClient](#qnamakerclient-object-model)** jest obiektem do tworzenia, zarządzania, publikowania, pobierania i wykonywania zapytań dotyczących bazy wiedzy.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Model obiektów QnAMakerClient

QnA Maker tworzenia klienta jest obiektem [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) , który jest uwierzytelniany na platformie Azure za pomocą MsRest:: serviceclientcredentials, który zawiera klucz.

Po utworzeniu klienta Użyj metod właściwości [bazy](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) danych klienta do tworzenia i publikowania bazy wiedzy oraz zarządzania nią.

W przypadku operacji natychmiastowych Metoda zwykle zwraca wynik, jeśli istnieje. W przypadku długotrwałych operacji odpowiedź jest obiektem [operacji](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) . Wywołaj metodę [Details](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) z `operation.operationId` wartością, aby określić [stan żądania](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java).

### <a name="qnamakerruntimeclient-object-model"></a>Model obiektów QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

QnA Maker środowiska uruchomieniowego jest obiektem [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) .

Po opublikowaniu bazy wiedzy przy użyciu klienta tworzenia należy skorzystać z metody [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) klienta środowiska uruchomieniowego w celu uzyskania odpowiedzi z bazy wiedzy.

Aby utworzyć klienta środowiska uruchomieniowego, należy wywołać [QnAMakerRuntimeManager. Authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) i przekazywanie klucza punktu końcowego środowiska uruchomieniowego. Aby uzyskać klucz punktu końcowego środowiska uruchomieniowego, użyj klienta tworzenia do wywołania metody [GetKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Zasób zarządzany QnA Maker nie wymaga użycia obiektu QnAMakerRuntimeClient. Zamiast tego należy wywołać [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) bezpośrednio dla obiektu [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) .

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Uwierzytelnianie klienta w celu tworzenia bazy wiedzy

Utwórz wystąpienie klienta przy użyciu punktu końcowego tworzenia i klucza subskrypcji.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy zapisuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) z trzech źródeł:

* W przypadku **zawartości redakcyjnej** Użyj obiektu [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java) .
    * Aby użyć metadanych i monitów uzupełniających, użyj kontekstu redakcyjnego, ponieważ te dane są dodawane na indywidualnym poziomie pary QnA.
* Dla **plików** Użyj obiektu [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java) . FileDTO zawiera nazwę pliku, a także publiczny adres URL w celu uzyskania dostępu do pliku.
* W przypadku **adresów URL** Użyj listy ciągów do reprezentowania publicznie dostępnych adresów URL.

Wywołaj metodę [Create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) , a następnie Przekaż `operationId` Właściwość zwracanej operacji do metody Details w celu sondowania stanu. [](#get-status-of-an-operation)

Ostatni wiersz poniższego kodu zwraca identyfikator bazy wiedzy.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, wywołując [aktualizację](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) i przekazując identyfikator bazy wiedzy oraz obiekt [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java) . Ten obiekt z kolei może zawierać:
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Przekaż `operationId` Właściwość zwracanej operacji do metody [Details](#get-status-of-an-operation) w celu sondowania stanu.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj metody [pobierania](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) , aby pobrać bazę danych jako listę [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java). _Nie_ jest to równoznaczne z eksportem QNA Maker portalu ze strony **ustawień** , ponieważ wynik tej metody nie jest plikiem TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [Publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) . Obejmuje to bieżący zapisany i szkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generowanie odpowiedzi z bazy wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Po opublikowaniu bazy wiedzy wymagany jest klucz punktu końcowego środowiska uruchomieniowego w celu zbadania bazy wiedzy. Nie jest to ten sam klucz subskrypcji użyty do utworzenia klienta tworzenia.

Użyj metody [GetKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) , aby pobrać obiekt [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java) .

Utwórz klienta środowiska uruchomieniowego, wywołując [QnAMakerRuntimeManager. Authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) i przekazując klucz punktu końcowego środowiska uruchomieniowego z obiektu EndpointKeysDTO.

Wygeneruj odpowiedź z opublikowanej bazy wiedzy przy użyciu metody [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) . Ta metoda akceptuje identyfikator bazy wiedzy i obiekt [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) .

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Wygeneruj odpowiedź z opublikowanej bazy wiedzy przy użyciu metody [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) . Ta metoda akceptuje identyfikator bazy wiedzy i obiekt [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) .

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

Jest to prosty przykład zapytania dotyczącego bazy wiedzy. Aby zrozumieć zaawansowane scenariusze zapytań, zapoznaj się z [innymi przykładami zapytań](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) z parametrem identyfikatora bazy wiedzy.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>Pobierz stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, mogą trwać wystarczająco długo, aby nie czekać na zakończenie procesu, zostanie zwrócona [operacja](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) . Użyj identyfikatora operacji z operacji do sondowania (z logiką ponownych prób), aby określić stan oryginalnej metody.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>Uruchamianie aplikacji

Oto główna Metoda dla aplikacji.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

Uruchom aplikację w następujący sposób. Oznacza to, że nazwa klasy jest `Quickstart` i Twoje zależności znajdują się w podfolderze o nazwie `lib` poniżej bieżącego folderu.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/version-1)

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/version-2)

Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java).

---
