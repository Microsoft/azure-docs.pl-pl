---
title: Opcje projektowania Cognitive Services platformy Azure
description: Dowiedz się, jak korzystać z usługi Azure Cognitive Services z różnymi opcjami tworzenia i wdrażania, takimi jak biblioteki klienta, interfejsy API REST, Logic Apps, Automatyzacja, Azure Functions, Azure App Service, Azure Databricks i wiele innych.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a952bfa45515b9d35549d03d18fd94103679344d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349473"
---
# <a name="cognitive-services-development-options"></a>Opcje programowania usług Cognitive Services

Ten dokument zawiera ogólne omówienie opcji programowania i wdrażania, które ułatwiają rozpoczęcie pracy z usługą Azure Cognitive Services.

Azure Cognitive Services to oparte na chmurze usługi AI, które umożliwiają deweloperom tworzenie analiz w aplikacjach i produktach bez dokładnej znajomości uczenia maszynowego. Dzięki Cognitive Services masz dostęp do możliwości i modeli AI, które są kompilowane, przeszkolone i aktualizowane przez firmę Microsoft — gotowe do użycia w aplikacjach. W wielu przypadkach istnieje również możliwość dostosowania modeli potrzeb Twojej firmy. 

Cognitive Services są zorganizowane w czterech kategoriach: decyzji, języka, mowy i wizji. Zazwyczaj dostęp do tych usług odbywa się za pomocą interfejsów API REST, bibliotek klienckich i narzędzi niestandardowych (takich jak interfejsy wiersza polecenia) udostępnianych przez firmę Microsoft. Jednak jest to tylko jedna ścieżka do sukcesu. Za pomocą platformy Azure masz również dostęp do kilku opcji programistycznych, takich jak:

* Narzędzia do automatyzacji i integracji, takie jak Logic Apps i Automatyzacja.
* Opcje wdrażania, takie jak Azure Functions i App Service. 
* Cognitive Services kontenerów platformy Docker na potrzeby bezpiecznego dostępu.
* Narzędzia takie jak Apache Spark, Azure Databricks, Azure Synapse Analytics i Azure Kubernetes Service na potrzeby scenariuszy danych Big Data. 

Zanim przejdziemy do usługi, ważne jest, aby wiedzieć, że Cognitive Services jest używany głównie do dwóch różnych zadań. Na podstawie zadania, które chcesz wykonać, masz różne opcje programowania i wdrażania do wyboru. 

* [Opcje programowania na potrzeby przewidywania i analizy](#development-options-for-prediction-and-analysis)
* [Narzędzia do dostosowywania i konfigurowania modeli](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Opcje programowania na potrzeby przewidywania i analizy 

Narzędzia, które będą używane do dostosowywania i konfigurowania modeli, są inne niż te, które będą używane do wywoływania Cognitive Services. Najprawdopodobniej, większość Cognitive Services pozwala na wysyłanie danych i uzyskiwanie wglądu bez żadnego dostosowania. Przykład: 

* Można wysłać obraz do usługi przetwarzanie obrazów, aby wykryć słowa i frazy lub liczbę osób w ramce.
* Można wysłać plik audio do usługi mowy i uzyskać transkrypcje i przetłumaczyć mowę na tekst w tym samym czasie
* Można wysłać plik PDF do usługi aparat rozpoznawania formularzy i wykryć tabele, komórki i tekst w tych komórkach, a następnie uzyskać dane wyjściowe JSON ze współrzędnymi i szczegółami

Platforma Azure oferuje szeroką gamę narzędzi przeznaczonych dla różnych typów użytkowników, z których można korzystać z Cognitive Services. Narzędzia oparte na projektancie są najłatwiej korzystać z programu i są szybkie do konfiguracji i automatyzowania, ale mogą mieć ograniczenia dotyczące dostosowywania. Nasze interfejsy API REST i biblioteki klienckie zapewniają użytkownikom większą kontrolę i elastyczność, ale wymagają większej nakładu pracy, czasu i wiedzy na potrzeby tworzenia rozwiązań. Jeśli korzystasz z interfejsów API REST i bibliotek klienckich, oczekujesz, że masz doświadczenie w pracy z nowoczesnymi językami programowania, takimi jak C#, Java, Python, JavaScript lub innym popularnym językiem programowania. 

Przyjrzyjmy się różnym sposobom, które można współdziałać z Cognitive Services.

### <a name="client-libraries-and-rest-apis"></a>Biblioteki klienta i interfejsy API REST

Cognitive Services biblioteki klienckie i interfejsy API REST zapewniają bezpośredni dostęp do usługi. Te narzędzia zapewniają programistyczny dostęp do Cognitive Services, ich modeli bazowych, a w wielu przypadkach umożliwiają programistyczne Dostosowywanie modeli i rozwiązań. 

* **Użytkownicy docelowi**: deweloperzy i analityki danych
* **Korzyści**: zapewnia największą elastyczność wywoływania usług z dowolnego języka i środowiska. 
* **Interfejs użytkownika**: tylko N/A-kod
* **Subskrypcje**: konto platformy Azure i zasoby Cognitive Services

Jeśli chcesz dowiedzieć się więcej o dostępnych bibliotekach klienta i interfejsach API REST, Skorzystaj z naszego [omówienia Cognitive Services](index.yml) , aby wybrać i rozpocząć pracę z jednym z naszych przewodników szybki start dla wizji, decyzji, języka i mowy.

### <a name="cognitive-services-for-big-data"></a>Usługi Cognitive Services dla danych big data

Za pomocą Cognitive Services dla danych Big Data można osadzić ciągłe ulepszanie, inteligentnych modeli bezpośrednio do Apache Spark &trade; i obliczeń SQL. Te narzędzia wykorzystają deweloperów z szczegółów sieci niskiego poziomu, dzięki czemu mogą skupić się na tworzeniu inteligentnych, rozproszonych aplikacji. Cognitive Services dla danych Big Data obsługuje następujące platformy i łączniki: Azure Databricks, Azure Synapse, Azure Kubernetes Service i łączniki danych.

* **Użytkownicy docelowi**: Naukowcy i inżynierowie danych
* **Zalety**: usługa Azure Cognitive Services dla danych Big Data umożliwia użytkownikom korzystanie z terabajtów danych przez Cognitive Services przy użyciu Apache Spark &trade; . Można łatwo tworzyć inteligentne aplikacje o dużej skali z dowolnym magazynem danych.
* **Interfejs użytkownika**: tylko N/A-kod
* **Subskrypcje**: konto platformy Azure i zasoby Cognitive Services

Jeśli chcesz dowiedzieć się więcej na temat danych Big Data dla Cognitive Services, dobrym miejscem do rozpoczęcia jest zapoznanie się z [omówieniem](./big-data/cognitive-services-for-big-data.md). Jeśli wszystko jest gotowe do rozpoczęcia kompilowania, wypróbuj nasze przykłady w języku [Python](./big-data/samples-python.md) lub [Scala](./big-data/samples-scala.md) .

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions i zadania usługi Azure Service Web

[Azure Functions](../azure-functions/index.yml) i [Azure App Service zadania sieci Web](../app-service/index.yml) udostępniają usługi integracji w pierwszej kolejności, które są przeznaczone dla deweloperów i są oparte na [usłudze Azure App Services](../app-service/index.yml). Te produkty zapewniają infrastrukturę bezserwerową do pisania kodu. W tym kodzie można wykonywać wywołania naszych usług, korzystając z naszych bibliotek klientów i interfejsów API REST. 

* **Użytkownicy docelowi**: deweloperzy i analityki danych
* **Zalety**: bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu wyzwalanego przez zdarzenia. 
* **Interfejs użytkownika**: tak
* **Subskrypcje**: konto platformy Azure i Cognitive Services resource + Azure Functions Subscription

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) korzystać z tego samego projektanta przepływów pracy i łączników, co zapewnia automatyzację, ale oferuje bardziej zaawansowane i kontrolki, w tym integracje z programem Visual Studio i DevOps. Automatyzacja i wyłączanie ułatwia integrację z zasobami usług poznawczych za pomocą łączników specyficznych dla usługi, które zapewniają serwer proxy lub otokę obejmującą interfejsy API. Są to te same łączniki, które są dostępne w automatyzacji. 

* **Użytkownicy docelowi**: deweloperzy, Integratory, specjaliści IT, DevOps
* **Zalety**: model programistyczny w pierwszej kolejności (deklaracyjne) zapewniający zaawansowane opcje i integrację w rozwiązaniu z niską ilością kodu
* **Interfejs użytkownika**: tak
* **Subskrypcje**: konto platformy Azure + Cognitive Services resource + Logic Apps Deployment

### <a name="power-automate"></a>Power Automate 

Automatyzacja i wyłączanie usługi jest usługą na [platformie](/power-platform/) , która pomaga tworzyć zautomatyzowane przepływy pracy między aplikacjami i usługami bez pisania kodu. Oferujemy kilka łączników, które ułatwiają współpracę z zasobem Cognitive Services w rozwiązaniu do automatyzacji. Automatyzacja jest oparta na Logic Apps. 

* **Użytkownik docelowy**: użytkownicy biznesowi (analityków) i Administratorzy programu SharePoint
* **Korzyści**: Automatyzowanie powtarzalnych zadań ręcznych po prostu przez nagrywanie kliknięć myszą, naciśnięcie klawiszy i kopiowanie kroków wklejania z pulpitu!
* **Narzędzia interfejsu użytkownika**: tak — tylko interfejs użytkownika
* **Subskrypcje**: konto platformy Azure i Cognitive Services Resource + energia Automatyzuj subskrypcję i subskrypcja pakietu Office 365

### <a name="ai-builder"></a>AI Builder 

[Konstruktor AI](/ai-builder/overview) to funkcja platformy Microsoft, która pozwala zwiększyć wydajność biznesową dzięki automatyzacji procesów i przewidywania wyników. Konstruktor AI umożliwia korzystanie z możliwości systemu AI w rozwiązaniach za pomocą środowiska i kliknięcia. Wiele usług poznawczych, takich jak aparat rozpoznawania formularzy, analiza tekstu i przetwarzanie obrazów, zostały bezpośrednio zintegrowane w tym miejscu i nie musisz tworzyć własnych Cognitive Services. 

* **Użytkownik docelowy**: użytkownicy biznesowi (analityków) i Administratorzy programu SharePoint
* **Korzyści**: rozwiązanie gotowe, które umożliwia korzystanie z możliwości systemu AI za pomocą środowiska i kliknięcia. Nie są wymagane żadne umiejętności kodowania ani analizy danych.
* **Narzędzia interfejsu użytkownika**: tak — tylko interfejs użytkownika
* **Subskrypcje**: Konstruktor AI

### <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

Aby zarządzać wdrożeniami, możesz użyć akcji Azure DevOps i GitHub. W [poniższej sekcji](#continuous-integration-and-delivery-with-devops-and-github-actions) omówiono dwa przykłady integracji Ci/CD do uczenia i wdrażania niestandardowych modeli dla mowy i usługi Language UNDERSTANDING (Luis). 

* **Użytkownicy docelowi**: deweloperzy, analityki danych i inżynierowie danych
* **Korzyści**: umożliwia programowe i programistyczne Dostosowywanie, aktualizowanie i wdrażanie aplikacji oraz modeli. Istnieje znacząca korzyść w przypadku regularnego korzystania z danych w celu ulepszania i aktualizowania modeli na potrzeby mowy, wizji, języka i decyzji. 
* **Narzędzia interfejsu użytkownika**: tylko kod w języku N/A 
* **Subskrypcje**: konto platformy Azure i zasób Cognitive Services i konto GitHub

## <a name="tools-to-customize-and-configure-models"></a>Narzędzia do dostosowywania i konfigurowania modeli

W miarę postępów tworzenia aplikacji lub przepływu pracy przy użyciu Cognitive Services może się okazać, że konieczne jest dostosowanie modelu w celu osiągnięcia odpowiedniej wydajności. Wiele naszych usług pozwala na tworzenie wbudowanych modeli w celu spełnienia określonych potrzeb firmy. W przypadku wszystkich naszych dostosowywalnych usług udostępniamy zarówno środowisko oparte na interfejsie użytkownika, jak i interfejsy API dla szkoleń opartych na kodzie. Przykład:

* Chcesz przeszkolić model Custom Speech, aby prawidłowo rozpoznawał terminy medyczne z użyciem współczynnika błędów wyrazów (WER) poniżej 3%
* Chcesz utworzyć klasyfikator obrazu z Custom Vision, które mogą odróżnić różnice między drzewami iglastymi i liściaste
* Chcesz utworzyć niestandardowy głos neuronowych z osobistymi danymi głosowymi, aby uzyskać ulepszony zautomatyzowany komfort obsługi klienta

Narzędzia, które będą używane do uczenia i konfiguracji modeli, są inne niż te, które będą używane do wywoływania Cognitive Services. W wielu przypadkach Cognitive Services obsługujące dostosowywanie udostępniają portale i narzędzia interfejsu użytkownika ułatwiające uczenie, ocenę i wdrażanie modeli. Szybko zapoznaj się z kilkoma opcjami:<br><br>

| Filar | Usługa | Interfejs użytkownika dostosowywania | Szybki start |
|--------|---------|------------------|------------|
| Obraz | Niestandardowe przetwarzanie obrazów | https://www.customvision.ai/ | [Szybki start](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Obraz | Rozpoznawanie formularzy | Przykładowe narzędzie do etykietowania | [Szybki start](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Decyzja | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Szybki start](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Decyzja | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [Szybki start](./metrics-advisor/quickstarts/web-portal.md) |
| Decyzja | Personalizacja | Interfejs użytkownika jest dostępny w Azure Portal w obszarze zasobu narzędzia Personalizacja. | [Szybki start](./personalizer/quickstart-personalizer-sdk.md) |
| Język | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Język | QnA Maker | https://www.qnamaker.ai/ | [Szybki start](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Język | Translator/translator niestandardowy | https://portal.customtranslator.azure.ai/ | [Szybki start](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Mowa | Polecenia niestandardowe | https://speech.microsoft.com/ | [Szybki start](./speech-service/custom-commands.md) |
| Mowa | Custom Speech | https://speech.microsoft.com/ | [Szybki start](./speech-service/custom-speech-overview.md) |
| Mowa | Niestandardowy głos | https://speech.microsoft.com/ | [Szybki start](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Ciągła integracja i dostarczanie dzięki akcjom DevOps i GitHub

Language Understanding i usługa mowy oferuje rozwiązania ciągłej integracji i ciągłego wdrażania, które są obsługiwane przez działania usługi Azure DevOps i GitHub. Narzędzia te służą do zautomatyzowanego szkolenia, testowania i wydania zarządzania modelami niestandardowymi. 

* [Ciągła integracja/ciągłe wdrażanie dla usługi Custom Speech](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [Ciągłej integracji/ciągłego dostarczania LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Kontenery Premium 

Wiele Cognitive Services można wdrożyć w kontenerach na potrzeby dostępu premium i używania. Użycie tych kontenerów zapewnia elastyczność Cognitive Services bliżej danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych. Aby uzyskać pełną listę kontenerów Cognitive Services, zobacz [Premium Containers for Cognitive Services](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Następne kroki
<!--
* Learn more about low code development options for Cognitive Services -->
* [Utwórz zasób Cognitive Services i Rozpocznij Kompilowanie](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)