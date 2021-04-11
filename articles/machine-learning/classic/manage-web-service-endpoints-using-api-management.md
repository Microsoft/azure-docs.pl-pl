---
title: 'ML Studio (klasyczny): zarządzanie usługami sieci Web za pomocą API Management — Azure'
description: Przewodnik przedstawiający sposób zarządzania usługami sieci Web Azure przy użyciu API Management. Zarządzanie punktami końcowymi interfejsu API REST przez definiowanie dostępu użytkowników, ograniczanie użycia i monitorowanie pulpitu nawigacyjnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 293643ab5354c51142baaf281b1845cfc9fa6f23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518032"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Zarządzanie usługami sieci Web Azure Machine Learning Studio (klasycznymi) za pomocą API Management

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak szybko rozpocząć korzystanie z API Management do zarządzania usługami sieci Web Azure Machine Learning Studio (klasycznymi).

## <a name="what-is-azure-api-management"></a>Co to jest usługa Azure API Management?
Azure API Management to usługa platformy Azure, która umożliwia zarządzanie punktami końcowymi interfejsu API REST przez definiowanie dostępu użytkowników, ograniczanie użycia i monitorowanie pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [witrynę Azure API Management](https://azure.microsoft.com/services/api-management/) . Aby rozpocząć pracę z usługą Azure API Management, zobacz [Przewodnik importowania i publikowania](../../api-management/import-and-publish.md). Ten przewodnik, w którym bazuje ten przewodnik, zawiera więcej tematów, takich jak konfiguracje powiadomień, cenniki, obsługa odpowiedzi, uwierzytelnianie użytkowników, tworzenie produktów, subskrypcje deweloperów i pulpit nawigacyjny użycia.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz wykonać następujące czynności:

* Konto platformy Azure.
* Konto Azure.
* Obszar roboczy, usługa i api_key dla eksperymentu platformy Azure wdrożonego jako usługa sieci Web. Aby uzyskać szczegółowe informacje na temat sposobu tworzenia eksperymentu dotyczącego platformy Azure, zobacz [Przewodnik Szybki Start](create-experiment.md)dotyczący programu Studio. Aby uzyskać informacje na temat sposobu wdrażania eksperymentu programu Studio (klasycznego) jako usługi sieci Web, zobacz sekcję dotyczącą [wdrożenia programu Studio](deploy-a-machine-learning-web-service.md) , aby uzyskać szczegółowe informacje na temat sposobu wdrażania eksperymentu platformy Azure jako usługi sieci Web. Alternatywnie dodatek A zawiera instrukcje dotyczące sposobu tworzenia i testowania prostego eksperymentu dotyczącego usługi Azure i wdrażania go jako usługa sieci Web.

## <a name="create-an-api-management-instance"></a>Tworzenie wystąpienia usługi API Management

Usługą sieci Web Azure Machine Learning można zarządzać przy użyciu wystąpienia API Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób**.
3. W polu wyszukiwania wpisz "API Management", a następnie wybierz zasób "API Management".
4. Kliknij pozycję **Utwórz**.
5. Wartość **Nazwa** zostanie użyta do utworzenia unikatowego adresu URL (w tym przykładzie użyto "demoazureml").
6. Wybierz **subskrypcję**, **grupę zasobów** i **lokalizację** wystąpienia usługi.
7. Określ wartość dla **nazwy organizacji** (w tym przykładzie jest stosowane "demoazureml").
8. Wprowadź **adres E-mail administratora** — ten adres e-mail będzie używany na potrzeby powiadomień z systemu API Management.
9. Kliknij pozycję **Utwórz**.

Utworzenie nowej usługi może potrwać do 30 minut.

![Zrzut ekranu przedstawia okno dialogowe usługi zarządzania P I z opcjami wymaganymi do utworzenia usługi.](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Tworzenie interfejsu API
Po utworzeniu wystąpienia usługi następnym krokiem jest utworzenie interfejsu API. Interfejs API składa się z zestawu operacji, które mogą być wywoływane z poziomu aplikacji klienckiej. Operacje interfejsu API są przekierowywane do istniejących usług sieci Web. Ten przewodnik tworzy interfejsy API, które są używane przez serwer proxy dla istniejących rekordów zasobów usługi Azure i BES sieci Web.

Aby utworzyć interfejs API:

1. W Azure Portal Otwórz utworzone wystąpienie usługi.
2. W okienku nawigacji po lewej stronie wybierz pozycję **interfejsy API**.

   ![Interfejs API Management — menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kliknij pozycję **Dodaj interfejs API**.
2. Wprowadź **nazwę interfejsu API sieci Web** (w tym przykładzie jest użyty "interfejs API demonstracyjnej usługi Azure").
3. W przypadku **adresu URL usługi sieci Web** wpisz " `https://ussouthcentral.services.azureml.net` ".
4. Wprowadź znak * * sufiks adresu URL interfejsu API sieci Web. Stanie się ona ostatnią częścią adresu URL, który będzie używany przez klientów do wysyłania żądań do wystąpienia usługi (w tym przykładzie jest używana "usługa Azure-demonstracyjna").
5. W obszarze **schemat adresu URL interfejsu API sieci Web** wybierz pozycję **https**.
6. W obszarze **produkty** wybierz pozycję **Starter**.
7. Kliknij pozycję **Zapisz**.


## <a name="add-the-operations"></a>Dodaj operacje

Operacje są dodawane i konfigurowane do interfejsu API w portalu wydawców. Aby uzyskać dostęp do portalu wydawców, kliknij pozycję **Portal wydawcy** w Azure Portal dla usługi API Management, wybierz pozycję **interfejsy API**, **operacje**, a następnie kliknij pozycję **Dodaj operację**.

![Dodaj operację](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Zostanie wyświetlone okno **Nowa operacja** , a karta **podpis** zostanie domyślnie wybrana.

## <a name="add-rrs-operation&quot;></a>Operacja dodawania rekordów zasobów
Najpierw utwórz operację dla usługi rekordów zasobów Azure:

1. Dla **zlecenia http** wybierz pozycję **post**.
2. W polu **Szablon adresu URL** wpisz &quot; `/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}` &quot;.
3. Wprowadź **nazwę wyświetlaną** (w tym przykładzie jest używana funkcja &quot;rerr Execute").

   ![Zrzut ekranu przedstawia stronę podpisu, na której można wprowadzić nazwę wyświetlaną.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kliknij pozycję **odpowiedzi**  >  **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
5. Kliknij przycisk **Zapisz** , aby zapisać tę operację.

   ![Zrzut ekranu przedstawia stronę wykonywanie operacji R R S z przyciskiem Zapisz.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Dodawanie operacji BES

> [!NOTE]
> Zrzuty ekranu nie są uwzględniane w tym miejscu dla operacji BES, ponieważ są one podobne do tych w przypadku dodawania zasobów.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Prześlij (ale nie Uruchom) zadanie wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http** wybierz pozycję **post**.
3. W polu **Szablon adresu URL** wpisz " `/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}` ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie zostanie użyta wartość "BES Submit").
5. Kliknij pozycję **odpowiedzi**  >  **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
6. Kliknij pozycję **Zapisz**.

### <a name="start-a-batch-execution-job"></a>Uruchom zadanie wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http** wybierz pozycję **post**.
3. Dla **zlecenia http** wpisz " `/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}` ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie jest stosowana wartość "BES Start").
6. Kliknij pozycję **odpowiedzi**  >  **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
7. Kliknij pozycję **Zapisz**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Pobieranie stanu lub wyniku zadania wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http** wybierz pozycję **Pobierz**.
3. W polu **Szablon adresu URL** wpisz " `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie jest stosowana wartość "BES status").
6. Kliknij pozycję **odpowiedzi**  >  **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
7. Kliknij pozycję **Zapisz**.

### <a name="delete-a-batch-execution-job"></a>Usuń zadanie wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http** wybierz pozycję **Usuń**.
3. W polu **Szablon adresu URL** wpisz " `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie zostanie użyta wartość "BES Delete").
5. Kliknij pozycję **odpowiedzi**  >  **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
6. Kliknij pozycję **Zapisz**.

## <a name="call-an-operation-from-the-developer-portal"></a>Wywoływanie operacji z portalu dla deweloperów

Operacje można wywołać bezpośrednio z portalu dla deweloperów, który zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API. W tym kroku zostanie wywołana metoda **Execute RR** , która została dodana do **interfejsu API demonstracyjnego usługi Azure**. 

1. Kliknij przycisk **Portal dla deweloperów**.

   ![Zrzut ekranu przedstawia link portalu dla deweloperów.](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kliknij pozycję **interfejsy API** w górnym menu, a następnie kliknij pozycję **demona interfejsu API platformy Azure** , aby wyświetlić dostępne operacje.

   ![Zrzut ekranu przedstawia demonstrację systemu Azure w wersji 1.](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Wybierz pozycję **rekordy zasobów wykonaj** dla operacji. Kliknij przycisk **Wypróbuj**.

   ![Zrzut ekranu przedstawia demonstrację systemu Azure w wersji 1, okno dialogowe z zaznaczoną opcją Opublikuj r S i przycisk Wypróbuj.](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. W polu **parametry żądania** wpisz swój **obszar roboczy** i  **usługę**, wpisz "2,0 dla **apiversion** i" true ", aby uzyskać **szczegółowe informacje**. **Obszar roboczy** i **usługę** można znaleźć na pulpicie nawigacyjnym usługi sieci Web Azure (zobacz **Testowanie usługi sieci Web** w dodatku A).

   W przypadku **nagłówków żądania** kliknij pozycję **Dodaj nagłówek** i wpisz "Content-Type" i "Application/JSON". Kliknij pozycję **Dodaj nagłówek** ponownie, a następnie wpisz "Authorization" i "Bearer *\<your service API-KEY\>* ". Klucz API-KEY można znaleźć na pulpicie nawigacyjnym usługi sieci Web Azure (zobacz **Testowanie usługi sieci Web** w dodatku A).

   Dla **treści żądania** wpisz `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}` .

   ![Zrzut ekranu przedstawia demonstrację platformy Azure w wersji 1, żądanie, nagłówki żądań, treść żądania i autoryzację.](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kliknij pozycję **Wyślij**.

   ![Zrzut ekranu przedstawia przycisk Wyślij.](./media/manage-web-service-endpoints-using-api-management/send.png)

Po wywołaniu operacji Portal dla deweloperów wyświetla **żądany adres URL** z usługi zaplecza, **stan odpowiedzi**, **nagłówki odpowiedzi** i dowolną **zawartość odpowiedzi**.

![Zrzut ekranu przedstawia Portal deweloperów wyświetlający informacje o stanie odpowiedzi, opóźnieniu odpowiedzi, nagłówkach odpowiedzi i treści odpowiedzi.](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Dodatek A — tworzenie i testowanie prostej usługi sieci Web Azure
### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Poniżej przedstawiono procedurę tworzenia prostego eksperymentu w usłudze Azure i wdrażania go jako usługi sieci Web. Usługa sieci Web przyjmuje jako wejściową kolumnę dowolnego tekstu i zwraca zestaw funkcji reprezentowanych jako liczby całkowite. Na przykład:

| Tekst | Tekst zmieszany |
| --- | --- |
| To jest dobry dzień |1 1 2 2 0 2 0 1 |

Najpierw za pomocą wybranej przeglądarki przejdź do: [https://studio.azureml.net/](https://studio.azureml.net/) i wprowadź poświadczenia, aby się zalogować. Następnie utwórz nowy, pusty eksperyment.

![Zrzut ekranu przedstawia nową stronę z wybranym EKSPERYMENTem i wyszukiwaniem tekstu.](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Zmień nazwę na **SimpleFeatureHashingExperiment**. Rozwiń pozycję **zapisane zestawy danych** i przeciągnij **przeglądy książki z usługi Amazon** do eksperymentu.

![Zrzut ekranu przedstawia próbki po lewej stronie i okienku SimpleFeatureHashingExperiment z prawej strony, aby przeciągnąć elementy w tym miejscu.](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozwiń pozycję **Przekształcanie danych** i **manipulowanie** , a następnie przeciągnij **pozycję Wybierz kolumny w zestawie danych** do eksperymentu. Połącz **przeglądy książki z usługi Amazon** , aby **wybrać kolumny w zestawie danych**.

![Łączenie modułu zestawu danych przeglądów książki z modułem kolumny projektu](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kliknij pozycję **Wybierz kolumny w zestawie danych** , a następnie kliknij pozycję **Uruchom selektor kolumny** i wybierz pozycję **Col2**. Kliknij znacznik wyboru, aby zastosować te zmiany.

![Wybieranie kolumn przy użyciu nazw kolumn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozwiń **Analiza tekstu** i przeciągnij **skróty funkcji** na eksperyment. Połącz **Wybierz kolumny w zestawie danych** , aby **wyznaczać skróty funkcji**.

![Zrzut ekranu przedstawia element skrótu funkcji dodawany do obszaru roboczego.](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Wpisz **3** dla **bitsize skrótu**. Spowoduje to utworzenie 8 (23) kolumn.

![Zrzut ekranu przedstawia właściwości z wybranym skrótem funkcji i można wprowadzić wartość skrótu bitsize.](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

W tym momencie można kliknąć przycisk **Uruchom** , aby przetestować eksperyment.

![Zrzut ekranu przedstawia przycisk Uruchom.](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Tworzenie usługi internetowej
Teraz Utwórz usługę sieci Web. Rozwiń węzeł **Usługa sieci Web** i przeciągnij **dane wejściowe** na eksperyment. Połącz **dane wejściowe** z **mieszaniem funkcji**. Przeciągnij również **dane wyjściowe** na eksperyment. Połącz **dane wyjściowe** z **mieszaniem funkcji**.

![Zrzut ekranu przedstawia obszar roboczy po określonych zmianach.](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kliknij pozycję **Opublikuj usługę sieci Web**.

![Zrzut ekranu przedstawia przycisk Publikuj usługę sieci WEB.](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kliknij przycisk **tak** , aby opublikować eksperyment.

![Zrzut ekranu przedstawia komunikat potwierdzający i opcję opublikowania.](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testowanie usługi internetowej
Usługa sieci Web platformy Azure składa się z punktów końcowych RSS (usługa żądania/odpowiedź) i BES (usługa wykonywania wsadowego). Funkcja RSS jest do wykonania synchronicznego. BES jest przeznaczony do asynchronicznego wykonywania zadań. Aby przetestować usługę sieci Web za pomocą przykładowego źródła Python poniżej, może być konieczne pobranie i zainstalowanie zestawu Azure SDK dla języka Python (zobacz: [jak zainstalować język Python](/azure/developer/python/azure-sdk-install)).

Przykładem może być również **obszar roboczy**, **Usługa** i **api_key** eksperymentu dla poniższego źródła. Obszar roboczy i usługę można znaleźć, klikając pozycję **żądanie/odpowiedź** lub **wykonanie wsadowe** dla eksperymentu na pulpicie nawigacyjnym usługi sieci Web.

![Zrzut ekranu przedstawia okienko żądania, w którym można znaleźć wartości obszaru roboczego i usługi.](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

**Api_key** można znaleźć, klikając eksperyment na pulpicie nawigacyjnym usługi sieci Web.

![Zrzut ekranu przedstawia eksperyment na pulpicie nawigacyjnym usługi sieci Web, gdzie można znaleźć klucz P I.](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Punkt końcowy zasobów testów
##### <a name="test-button"></a>Przycisk Testuj
Łatwym sposobem przetestowania punktu końcowego RR jest kliknięcie przycisku **Testuj** na pulpicie nawigacyjnym usługi sieci Web.

![Zrzut ekranu przedstawia eksperyment na pulpicie nawigacyjnym usługi sieci Web, który ma przycisk Testuj.](./media/manage-web-service-endpoints-using-api-management/test.png)

Wpisz **to dobry dzień** dla **Col2**. Kliknij znacznik wyboru.

![Zrzut ekranu przedstawia okno dialogowe wprowadzanie danych do predykcyjnego, w którym można wprowadzić tekst podobny do przykładu to dobry dzień.](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zobaczysz coś przypominającego

![Zrzut ekranu przedstawia wynik eksperymentu, który odczytuje to dobry dzień i kilka cyfr w cudzysłowie.](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Przykładowy kod
Innym sposobem na przetestowanie rekordów RR jest kod klienta. Jeśli klikniesz pozycję **żądanie/odpowiedź** na pulpicie nawigacyjnym i przewiniesz do dołu, zobaczysz przykładowy kod dla języków C#, Python i R. Zostanie również wyświetlona składnia żądania RR, w tym identyfikator URI żądania, nagłówki i treść.

W tym przewodniku przedstawiono przykład działającego środowiska Python. Należy zmodyfikować ją z **obszarem roboczym**, **usługą** i **api_key** eksperymentu.

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>Test Endpoint BES
Kliknij pozycję **wykonywanie wsadowe** na pulpicie nawigacyjnym i przewiń w dół. Zobaczysz przykładowy kod dla języków C#, Python i R. Zostanie również wyświetlona składnia żądań BES w celu przesłania zadania, uruchomienia zadania, uzyskania stanu lub wyników zadania oraz usunięcia zadania.

W tym przewodniku przedstawiono przykład działającego środowiska Python. Należy zmodyfikować ją z **obszarem roboczym**, **usługą** i **api_key** eksperymentu. Ponadto należy zmodyfikować **nazwę konta magazynu**, **klucz konta magazynu** i **nazwę kontenera magazynu**. Na koniec należy zmodyfikować lokalizację **pliku wejściowego** i lokalizację **pliku wyjściowego**.

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```