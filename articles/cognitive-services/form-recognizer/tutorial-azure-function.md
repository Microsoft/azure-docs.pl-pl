---
title: 'Samouczek: Używanie funkcji platformy Azure do przetwarzania przechowywanych dokumentów'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak używać funkcji platformy Azure do wyzwalania przetwarzania dokumentów, które są przekazywane do kontenera usługi Azure Blob Storage.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: bf455d9401593b5c09fa295e492368a2a5bee240
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048696"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Samouczek: Używanie funkcji platformy Azure do przetwarzania przechowywanych dokumentów

Aparatu rozpoznawania formularzy można używać jako części procesu automatycznego przetwarzania danych utworzonego przy użyciu Azure Functions. W tym przewodniku pokazano, jak za pomocą funkcji platformy Azure przetwarzać dokumenty przekazane do kontenera usługi Azure Blob Storage. Ten przepływ pracy wyodrębnia dane tabeli z przechowywanych dokumentów przy użyciu usługi układu aparatu rozpoznawania formularzy i zapisuje dane tabeli w pliku CSV na platformie Azure. Następnie można wyświetlić dane przy użyciu Power BI firmy Microsoft (nieomówione w tym miejscu).

> [!div class="mx-imgBorder"]
> ![Diagram przepływu pracy usługi platformy Azure](./media/tutorial-azure-function/workflow-diagram.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Tworzenie projektu usługi Azure Functions
> * Wyodrębnij dane układu z przekazanych formularzy
> * Przekazywanie danych układu do usługi Azure Storage

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Utwórz zasób aparatu rozpoznawania formularza "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz aparatu rozpoznawania formularza i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
  * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
  * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Lokalny dokument PDF do analizy. Możesz pobrać ten [przykładowy dokument](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) do użycia.
* Zainstalowano język [Python 3.8. x](https://www.python.org/downloads/) .
* [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zainstalowane.
* [Azure Functions Core Tools](../../azure-functions/functions-run-local.md?tabs=windows%2ccsharp%2cbash#install-the-azure-functions-core-tools) zainstalowane.
* Visual Studio Code z zainstalowanymi następującymi rozszerzeniami:
  * [Azure Functions rozszerzenie](/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Rozszerzenie języka Python](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

[Utwórz konto usługi Azure Storage](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) na Azure Portal. Wybierz pozycję **StorageV2** jako rodzaj konta.

W okienku po lewej stronie wybierz kartę **CORS** i Usuń istniejące zasady CORS, jeśli istnieją.

Po wdrożeniu programu Utwórz dwa puste kontenery magazynu obiektów BLOB o nazwie **test** i **Output**.

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

Otwórz program Visual Studio Code. Jeśli zainstalowano rozszerzenie Azure Functions, w okienku nawigacji po lewej stronie powinno być widoczne logo platformy Azure. Wybierz ją. Utwórz nowy projekt i po wyświetleniu monitu utwórz folder lokalny **coa_new** zawierający projekt.

![Przycisk tworzenia funkcji programu vscode](./media/tutorial-azure-function/vs-code-create-function.png)


Zostanie wyświetlony monit o skonfigurowanie wielu ustawień:
* W oknie **Wybieranie języka wybierz język** Python.
* W wierszu **Wybierz szablon** wybierz pozycję wyzwalacz usługi Azure Blob Storage. Następnie nadaj domyślną nazwę wyzwalacza.
* W monicie **Wybieranie ustawienia wybierz** opcję Utwórz nowe ustawienia aplikacji lokalnej.
* Wybierz swoją subskrypcję platformy Azure przy użyciu utworzonego konta magazynu. Następnie musisz wprowadzić nazwę kontenera magazynu (w tym przypadku `test/{name}` )
* Zdecyduj, aby otworzyć projekt w bieżącym oknie. 

![Przykład tworzenia monitu programu vscode](./media/tutorial-azure-function/vs-code-prompt.png)

Po wykonaniu tych kroków programu vscode doda nowy projekt funkcji platformy Azure z skryptem w języku Python *\_ \_ init \_ \_ . PR* . Ten skrypt zostanie wyzwolony, gdy plik zostanie przekazany do kontenera magazynu **testowego** , ale nie będzie nic robić.

## <a name="test-the-function"></a>Testowanie funkcji

Naciśnij klawisz F5, aby uruchomić podstawową funkcję. Programu vscode wyświetli monit o wybranie konta magazynu do interfejsu. Wybierz utworzone konto magazynu i Kontynuuj.

Otwórz Eksplorator usługi Azure Storage i przekaż przykładowy dokument PDF do kontenera **testowego** . Następnie sprawdź Terminal programu vscode. Skrypt powinien rejestrować, czy został wyzwolony przez przekazanie pliku PDF.

![Test terminalu programu vscode](./media/tutorial-azure-function/vs-code-terminal-test.png)


Zatrzymaj skrypt przed kontynuowaniem.

## <a name="add-form-processing-code"></a>Dodaj kod przetwarzania formularza

Następnie dodasz własny kod do skryptu języka Python, aby wywołać usługę aparat rozpoznawania formularzy i analizować przekazane dokumenty przy użyciu [interfejsu API układu](concept-layout.md)aparatu rozpoznawania formularzy.

W programu vscode przejdź do pliku *requirements.txt* funkcji. Definiuje zależności dla skryptu. Dodaj następujące pakiety języka Python do pliku:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Następnie otwórz skrypt *\_ \_ init \_ \_ . PR* . Dodaj następujące instrukcje `import`:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

Wygenerowaną funkcję można pozostawić `main` jako-is. Dodasz niestandardowy kod wewnątrz tej funkcji.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

Poniższy blok kodu wywołuje interfejs API [przeanalizowania](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) analizatora rozpoznawania formularzy dla przekazanego dokumentu. Wypełnij pola punkt końcowy i wartości klucza. 


# <a name="version-20"></a>[Wersja 2,0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[Wersja 2,1 Preview](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób aparatu rozpoznawania formularza utworzony w sekcji **wymagania wstępne** został wdrożony pomyślnie, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń Cognitive Services](../cognitive-services-security.md) .

Następnie Dodaj kod, aby wysłać zapytanie do usługi i uzyskać zwrócone dane. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Następnie Dodaj następujący kod, aby nawiązać połączenie z kontenerem **danych wyjściowych** usługi Azure Storage. Wprowadź własne wartości w polu Nazwa i klucz konta magazynu. Klucz można uzyskać na karcie **klucze dostępu** zasobu magazynu w Azure Portal.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

Poniższy kod analizuje odpowiedź aparatu rozpoznawania zwracanego formularza, tworzy plik CSV i przekazuje go do kontenera **danych wyjściowych** . 


> [!IMPORTANT]
> Prawdopodobnie trzeba będzie edytować ten kod, aby dopasować strukturę własnych dokumentów formularza.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Na koniec ostatni blok kodu przekazuje wyodrębnione dane tabeli i tekstu do elementu BLOB Storage.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Uruchamianie funkcji

Naciśnij klawisz F5, aby ponownie uruchomić funkcję. Użyj Eksplorator usługi Azure Storage, aby przekazać przykładowy formularz PDF do kontenera magazynu **testowego** . Ta akcja powinna wyzwolić uruchomienie skryptu i powinien zostać wyświetlony wynikowy plik CSV (wyświetlany jako tabela) w kontenerze **danych wyjściowych** .

Możesz połączyć ten kontener z Power BI, aby tworzyć rozbudowane wizualizacje danych, które zawiera.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji platformy Azure w języku Python w celu automatycznego przetwarzania przekazanych dokumentów PDF i wyprowadzania ich zawartości w formacie bardziej przyjaznym dla danych. Następnie Dowiedz się, jak używać Power BI do wyświetlania danych.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Co to jest rozpoznawanie formularzy?](overview.md)
* Dowiedz się więcej o [interfejsie API układu](concept-layout.md)