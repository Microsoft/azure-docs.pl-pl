---
title: Rozwiązywanie problemów Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy związane z zewnętrznymi kontrolkami w Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: af0c95c197a70291e0fc863f3256be0e9a1da7f1
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133204"
---
# <a name="troubleshoot-azure-data-factory"></a>Rozwiązywanie problemów Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z zewnętrznymi działaniami związanymi z kontrolą w Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Działanie łącznika i kopiowania

W przypadku problemów dotyczących łącznika, np. Napotkano błąd przy użyciu działania kopiowania, zobacz [Rozwiązywanie problemów Azure Data Factory łączników](connector-troubleshoot-guide.md).
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Kod błędu: 3200

- **Komunikat**: Błąd 403.

- **Przyczyna**:`The Databricks access token has expired.`

- **Zalecenie**: domyślnie token dostępu Azure Databricks jest ważny przez 90 dni. Utwórz nowy token i zaktualizuj połączoną usługę.


### <a name="error-code--3201"></a>Kod błędu: 3201

- **Komunikat**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Przyczyna**:`Bad authoring: Notebook path not specified correctly.`

- **Zalecenie**: Określ ścieżkę notesu w działaniu datakosteks.

<br/>  

- **Komunikat**:`Cluster... does not exist.`

- **Przyczyna**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Zalecenie**: Sprawdź, czy klaster datakostki istnieje.

<br/>  

- **Komunikat**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Określ ścieżki bezwzględne dla schematów adresów obszaru roboczego lub `dbfs:/folder/subfolder/foo.py` dla plików przechowywanych w systemie plików datakostki.

<br/>  

- **Komunikat**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Sprawdź [definicję połączonej usługi](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Komunikat**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Sprawdź [definicję połączonej usługi](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Komunikat**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Zapoznaj się z komunikatem o błędzie.

<br/>

### <a name="error-code--3202"></a>Kod błędu: 3202

- **Komunikat**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Przyczyna**:`Too many Databricks runs in an hour.`

- **Zalecenie**: Sprawdź, czy wszystkie potoki korzystające z tego obszaru roboczego datakostki są używane na potrzeby ich tworzenia zadań.  Jeśli potoki uruchomiły zbyt wiele kostek datakostków, Migruj niektóre potoki do nowego obszaru roboczego.

<br/>  

- **Komunikat**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Przyczyna**:`Authoring error: No value provided for the parameter.`

- **Zalecenie**: Sprawdź kod JSON potoku i upewnij się, że wszystkie parametry w notesie baseParameters określają niepustą wartość.

<br/>  

- **Komunikat**: `User: `SimpleUserContext {userId =..., Name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Przyczyna**: użytkownik, który wygenerował token dostępu, nie może uzyskać dostępu do klastra datakostks określonego w połączonej usłudze.

- **Zalecenie**: Upewnij się, że użytkownik ma wymagane uprawnienia w obszarze roboczym.


### <a name="error-code--3203"></a>Kod błędu: 3203

- **Komunikat**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Przyczyna**: klaster został przerwany. W przypadku klastrów interaktywnych może to być sytuacja wyścigu.

- **Zalecenie**: najlepszym sposobem na uniknięcie tego błędu jest użycie klastrów zadań.


### <a name="error-code--3204"></a>Kod błędu: 3204

- **Komunikat**:`Job execution failed.`

- **Przyczyna**: komunikaty o błędach wskazują różne problemy, takie jak nieoczekiwany stan klastra lub określone działanie. W ogóle nie jest wyświetlany żaden komunikat o błędzie.

- **Zalecenie**: nie dotyczy
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Poniższa tabela dotyczy języka U-SQL.
      
### <a name="error-code--2709"></a>Kod błędu: 2709

- **Komunikat**:`The access token is from the wrong tenant.`

- **Przyczyna**: nieprawidłowa dzierżawa Azure Active Directory (Azure AD).

- **Zalecenie**: nieprawidłowa dzierżawa Azure Active Directory (Azure AD).

<br/>

- **Komunikat**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Przyczyna**: ten błąd jest spowodowany przez ograniczenie na Data Lake Analytics.

- **Zalecenie**: Zmniejsz liczbę przesłanych zadań do Data Lake Analytics, zmieniając wyzwalacze Data Factory i ustawienia współbieżności w działaniach. Lub Zwiększ limity Data Lake Analytics.

<br/>  

- **Komunikat**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Przyczyna**: ten błąd jest spowodowany przez ograniczenie na Data Lake Analytics.

- **Zalecenie**: Zmniejsz liczbę przesłanych zadań do Data Lake Analytics, zmieniając wyzwalacze Data Factory i ustawienia współbieżności w działaniach. Lub Zwiększ limity Data Lake Analytics.


### <a name="error-code--2705"></a>Kod błędu: 2705

- **Komunikat**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Przyczyna**: Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie**: Upewnij się, że nazwa główna usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego.


### <a name="error-code--2711"></a>Kod błędu: 2711

- **Komunikat**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Przyczyna**: Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie**: Upewnij się, że nazwa główna usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego.

<br/>  

- **Komunikat**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Przyczyna**: ścieżka do pliku U-SQL jest niepoprawna lub poświadczenia połączonej usługi nie mają dostępu.

- **Zalecenie**: Sprawdź ścieżkę i poświadczenia podane w połączonej usłudze.


### <a name="error-code--2704"></a>Kod błędu: 2704

- **Komunikat**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Przyczyna**: Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie**: Upewnij się, że nazwa główna usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego.


### <a name="error-code--2707"></a>Kod błędu: 2707

- **Komunikat**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Przyczyna**: konto Data Lake Analytics w połączonej usłudze jest nieprawidłowe.

- **Zalecenie**: Sprawdź, czy odpowiednie konto zostało podane.


### <a name="error-code--2703"></a>Kod błędu: 2703

- **Komunikat**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Przyczyna**: błąd pochodzi z Data Lake Analytics.

- **Zalecenie**: błąd podobny do przykładu oznacza, że zadanie zostało przesłane do Data Lake Analytics, a skrypt nie powiódł się. Zbadaj w Data Lake Analytics. W portalu przejdź do konta Data Lake Analytics i Wyszukaj zadanie przy użyciu identyfikatora uruchomienia działania Data Factory (nie identyfikatora uruchomienia potoku). Zadanie zawiera więcej informacji o błędzie i pomaga w rozwiązywaniu problemów. Jeśli rozwiązanie nie jest wyczyszczone, skontaktuj się z zespołem pomocy technicznej Data Lake Analytics i podaj adres URL zadania, który zawiera nazwę konta i identyfikator zadania.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Kod błędu: 3602

- **Komunikat**:`Invalid HttpMethod: '%method;'.`

- **Przyczyna**: Metoda http określona w ładunku działania nie jest obsługiwana przez działanie funkcji platformy Azure.

- **Zalecenie**: obsługiwane metody http to put, post, get, DELETE, Options, nagłówkowy i Trace.


### <a name="error-code--3603"></a>Kod błędu: 3603

- **Komunikat**:`Response Content is not a valid JObject.`

- **Przyczyna**: funkcja platformy Azure, która została wywołana, nie zwróciła ładunku JSON w odpowiedzi. Działanie funkcji ADF platformy Azure obsługuje tylko zawartość odpowiedzi JSON.

- **Zalecenie**: zaktualizuj funkcję platformy Azure, aby zwracała prawidłowy ładunek JSON, np. funkcja języka C# może zwrócić (ActionResult) nowe OkObjectResult\"(\""\"{\"ID: 123}");


### <a name="error-code--3606"></a>Kod błędu: 3606

- **Komunikat**: Brak klucza funkcji w działaniu funkcji platformy Azure.

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "functionKey".


### <a name="error-code--3607"></a>Kod błędu: 3607

- **Komunikat**:`Azure function activity missing function name.`

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "FunctionName".


### <a name="error-code--3608"></a>Kod błędu: 3608

- **Komunikat**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Przyczyna**: Szczegóły funkcji platformy Azure w definicji działania mogą być nieprawidłowe.

- **Zalecenie**: Popraw Szczegóły funkcji platformy Azure i spróbuj ponownie.


### <a name="error-code--3609"></a>Kod błędu: 3609

- **Komunikat**:`Azure function activity missing functionAppUrl.`

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "functionAppUrl".


### <a name="error-code--3610"></a>Kod błędu: 3610

- **Komunikat**:`There was an error while calling endpoint.`

- **Przyczyna**: adres URL funkcji może być nieprawidłowy.

- **Zalecenie**: Upewnij się, że wartość parametru "functionAppUrl" w kodzie JSON działania jest poprawna, i spróbuj ponownie.


### <a name="error-code--3611"></a>Kod błędu: 3611

- **Komunikat**:`Azure function activity missing Method in JSON.`

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "Method".


### <a name="error-code--3612"></a>Kod błędu: 3612

- **Komunikat**:`Azure function activity missing LinkedService definition in JSON.`

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction zawiera szczegóły połączonej usługi.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Kod błędu: 4101

- **Komunikat**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Przyczyna**: Zły format lub Brak definicji właściwości "% PropertyName;".

- **Zalecenie**: Sprawdź, czy działanie "% ActivityName;" ma zdefiniowaną Właściwość "% PropertyName;" z poprawnymi danymi.


### <a name="error-code--4110"></a>Kod błędu: 4110

- **Komunikat**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Przyczyna**: definicja działania AzureMLExecutePipeline nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureMLExecutePipeline ma szczegóły połączonej usługi.


### <a name="error-code--4111"></a>Kod błędu: 4111

- **Komunikat**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Przyczyna**: nieprawidłowa definicja działania.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureMLExecutePipeline zawiera poprawne szczegóły połączonej usługi.


### <a name="error-code--4112"></a>Kod błędu: 4112

- **Komunikat**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Przyczyna**: Zły format lub Brak definicji właściwości "% PropertyName;".

- **Zalecenie**: Sprawdź, czy połączona usługa ma właściwość "% PropertyName;", która została zdefiniowana z prawidłowymi danymi.


### <a name="error-code--4121"></a>Kod błędu: 4121

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna**: poświadczenia używane do uzyskiwania dostępu do Azure Machine Learning utraciły ważność.

- **Zalecenie**: Upewnij się, że poświadczenie jest prawidłowe, i ponów próbę


### <a name="error-code--4122"></a>Kod błędu: 4122

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna**: poświadczenie podane w Azure Machine Learning połączonej usłudze jest nieprawidłowe lub nie ma uprawnień do wykonania tej operacji.

- **Zalecenie**: Upewnij się, że poświadczenie w połączonej usłudze jest prawidłowe i ma uprawnienia dostępu do Azure Machine Learning.


### <a name="error-code--4123"></a>Kod błędu: 4123

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna**: właściwości działania, takie jak pipelineParameters, są nieprawidłowe dla potoku Azure ml.

- **Zalecenie**: Sprawdź wartość właściwości działania, aby dopasować oczekiwany ładunek opublikowanego potoku usługi Azure ml określony w połączonej usłudze.


### <a name="error-code--4124"></a>Kod błędu: 4124

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna**: opublikowany punkt końcowy potoku platformy Azure ml nie istnieje.

- **Zalecenie**: Upewnij się, że opublikowany Azure Machine Learning punkt końcowy potoku określony w połączonej usłudze istnieje w Azure Machine Learning.


### <a name="error-code--4125"></a>Kod błędu: 4125

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna**: błąd serwera na Azure Machine Learning.

- **Zalecenie**: spróbuj ponownie później. Skontaktuj się Azure Machine Learning zespołem, aby uzyskać pomoc w przypadku pozostawania problemu.


### <a name="error-code--4126"></a>Kod błędu: 4126

- **Komunikat**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Przyczyna**: nie można uruchomić potoku usługi Azure ml.

- **Zalecenie**: zaewidencjonuj Azure Machine Learning, aby uzyskać więcej dzienników błędów i naprawić potok ml.



## <a name="common"></a>Wspólne

### <a name="error-code--2103"></a>Kod błędu: 2103

- **Komunikat**:`Please provide value for the required property '%propertyName;'.`

- **Przyczyna**: nie dostarczono wartości właściwości, ale jest ona wymagana w scenariuszu.

- **Zalecenie**: Podaj wartość z wiadomości i spróbuj ponownie.


### <a name="error-code--2104"></a>Kod błędu: 2104

- **Komunikat**:`The type of the property '%propertyName;' is incorrect.`

- **Przyczyna**: typ podanej właściwości nie jest zgodny z oczekiwaniami.

- **Zalecenie**: Popraw typ właściwości i spróbuj ponownie.


### <a name="error-code--2105"></a>Kod błędu: 2105

- **Komunikat**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Przyczyna**: wartość właściwości jest nieprawidłowa lub nie ma oczekiwanego formatu.

- **Zalecenie**: Wyszukaj w dokumentacji właściwości i upewnij się, że podana wartość ma oczekiwany format i typ.


### <a name="error-code--2106"></a>Kod błędu: 2106

- **Komunikat**:`The storage connection string is invalid. %errorMessage;`

- **Przyczyna**: parametry połączenia dla magazynu są nieprawidłowe lub mają niepoprawny format.

- **Zalecenie**: przejdź do Azure Portal, Znajdź magazyn, skopiuj parametry połączenia i wklej je w połączonej usłudze i spróbuj ponownie.


### <a name="error-code--2108"></a>Kod błędu: 2108

- **Komunikat**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Przyczyna**: żądanie nie powiodło się z powodu podstawowego problemu, takiego jak połączenie sieciowe, błąd DNS, weryfikacja certyfikatu serwera lub przekroczenie limitu czasu.

- **Zalecenie**: Użyj programu Fiddler/Poster, aby sprawdzić poprawność żądania.


### <a name="error-code--2110"></a>Kod błędu: 2110

- **Komunikat**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Przyczyna**: połączona usługa określona w działaniu była nieprawidłowa.

- **Zalecenie**: Upewnij się, że typ połączonej usługi jest jednym z obsługiwanych typów dla działania. Na przykład w przypadku działań HDI typem połączonej usługi może być HDInsight lub HDInsightOnDemand.


### <a name="error-code--2111"></a>Kod błędu: 2111

- **Komunikat**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Przyczyna**: typ podanej właściwości nie jest zgodny z oczekiwaniami.

- **Zalecenie**: Popraw typ właściwości i spróbuj ponownie.


### <a name="error-code--2112"></a>Kod błędu: 2112

- **Komunikat**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Przyczyna**: typ chmury jest nieobsługiwany lub nie można go określić dla magazynu z EndpointSuffix.

- **Zalecenie**: Użyj magazynu w innej chmurze i spróbuj ponownie.


### <a name="error-code--2128"></a>Kod błędu: 2128

- **Komunikat**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Przyczyna**: łączność sieciowa, błąd DNS, weryfikacja certyfikatu serwera lub przekroczenie limitu czasu.

- **Zalecenie**: Sprawdź, czy punkt końcowy, który próbujesz trafić, odpowiada na żądania. Można używać narzędzi takich jak programu Fiddler/Poster.



## <a name="custom"></a>Niestandardowy

Poniższa tabela ma zastosowanie do Azure Batch.
      
### <a name="error-code--2500"></a>Kod błędu: 2500

- **Komunikat**:`Hit unexpected exception and execution failed.`

- **Przyczyna**:`Can't launch command, or the program returned an error code.`

- **Zalecenie**: Upewnij się, że plik wykonywalny istnieje. Jeśli program został uruchomiony, upewnij się, że *stdout. txt* i *stderr. txt* zostały przekazane do konta magazynu. Dobrym sposobem jest wyemitujenie dzienników copious w kodzie w celu debugowania.


### <a name="error-code--2501"></a>Kod błędu: 2501

- **Komunikat**:`Cannot access user batch account; please check batch account settings.`

- **Przyczyna**: Nieprawidłowa nazwa klucza dostępu do partii lub puli.

- **Zalecenie**: Sprawdź nazwę puli i klucz dostępu do usługi Batch w połączonej usłudze.


### <a name="error-code--2502"></a>Kod błędu: 2502

- **Komunikat**:`Cannot access user storage account; please check storage account settings.`

- **Przyczyna**: Nieprawidłowa nazwa konta magazynu lub klucz dostępu.

- **Zalecenie**: Sprawdź nazwę konta magazynu i klucz dostępu w połączonej usłudze.


### <a name="error-code--2504"></a>Kod błędu: 2504

- **Komunikat**:`Operation returned an invalid status code 'BadRequest'.`

- **Przyczyna**: zbyt wiele plików w folderPath aktywności niestandardowej. Łączny rozmiar elementu resourceFiles nie może być większy niż 32 768 znaków.

- **Zalecenie**: usuwanie niepotrzebnych plików. Lub Dodaj do nich polecenie rozpakować, aby je wyodrębnić. Na przykład użyj`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Kod błędu: 2505

- **Komunikat**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Przyczyna**: działania niestandardowe obsługują tylko konta magazynu używające klucza dostępu.

- **Zalecenie**: Zapoznaj się z opisem błędu.


### <a name="error-code--2507"></a>Kod błędu: 2507

- **Komunikat**:`The folder path does not exist or is empty: ...`

- **Przyczyna**: żadne pliki nie znajdują się na koncie magazynu w określonej ścieżce.

- **Zalecenie**: ścieżka folderu musi zawierać pliki wykonywalne, które chcesz uruchomić.


### <a name="error-code--2508"></a>Kod błędu: 2508

- **Komunikat**:`There are duplicate files in the resource folder.`

- **Przyczyna**: wiele plików o tej samej nazwie znajduje się w różnych podfolderach folderPath.

- **Zalecenie**: działania niestandardowe Spłaszcz strukturę folderów w obszarze folderPath.  Jeśli potrzebujesz zachować strukturę folderów, pliki zip i Wyodrębnij je w Azure Batch przy użyciu polecenia Rozpakuj. Na przykład użyj`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Kod błędu: 2509

- **Komunikat**:`Batch   url ... is invalid; it must be in Uri format.`

- **Przyczyna**: adresy URL partii muszą być podobne do`https://mybatchaccount.eastus.batch.azure.com`

- **Zalecenie**: Zapoznaj się z opisem błędu.


### <a name="error-code--2510"></a>Kod błędu: 2510

- **Komunikat**:`An   error occurred while sending the request.`

- **Przyczyna**: adres URL partii jest nieprawidłowy.

- **Zalecenie**: Sprawdź adres URL partii.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Kod błędu: 200

- **Komunikat**:`Unexpected error happened: '%error;'.`

- **Przyczyna**: Wystąpił wewnętrzny problem z usługą.

- **Zalecenie**: Aby uzyskać dalszą pomoc, skontaktuj się z pomocą techniczną APD.


### <a name="error-code--201"></a>Kod błędu: 201

- **Komunikat**:`JobType %jobType; is not found.`

- **Przyczyna**: istnieje nowy typ zadania, który nie jest obsługiwany przez ADF.

- **Zalecenie**: Aby uzyskać dalszą pomoc, skontaktuj się z zespołem pomocy technicznej usługi ADF.


### <a name="error-code--202"></a>Kod błędu: 202

- **Komunikat**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna**: komunikat o błędzie powinien zawierać szczegóły dotyczące tego, co poszło źle.

- **Zalecenie**: komunikat o błędzie powinien pomóc w rozwiązaniu problemu. Jeśli nie ma wystarczającej ilości informacji, skontaktuj się z pomocą techniczną usługi ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--203"></a>Kod błędu: 203

- **Komunikat**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna**: komunikat o błędzie powinien zawierać szczegóły dotyczące tego, co poszło źle.

- **Zalecenie**: komunikat o błędzie powinien pomóc w rozwiązaniu problemu. Jeśli nie ma wystarczającej ilości informacji, skontaktuj się z pomocą techniczną usługi ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--204"></a>Kod błędu: 204

- **Komunikat**:`The resumption token is missing for runId '%runId;'.`

- **Przyczyna**: Wystąpił wewnętrzny problem z usługą.

- **Zalecenie**: Aby uzyskać dalszą pomoc, skontaktuj się z pomocą techniczną APD.


### <a name="error-code--205"></a>Kod błędu: 205

- **Komunikat**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Przyczyna**: Wystąpił błąd podczas tworzenia klastra HDI na żądanie.

- **Zalecenie**: Aby uzyskać dalszą pomoc, skontaktuj się z pomocą techniczną APD.


### <a name="error-code--206"></a>Kod błędu: 206

- **Komunikat**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Przyczyna**: Wystąpił wewnętrzny problem z usługą, która spowodowała to.

- **Zalecenie**: może to być problem przejściowy. Spróbuj ponownie wykonać zadanie, a jeśli problem będzie nadal występował, skontaktuj się z pomocą techniczną ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--207"></a>Kod błędu: 207

- **Komunikat**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Przyczyna**: Wystąpił błąd wewnętrzny podczas próby ustalenia regionu z podstawowego konta magazynu.

- **Zalecenie**: wypróbuj inny magazyn. Jeśli nie jest to akceptowalne rozwiązanie, skontaktuj się z zespołem pomocy technicznej usługi ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--208"></a>Kod błędu: 208

- **Komunikat**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Przyczyna**: Wystąpił błąd wewnętrzny podczas próby odczytu nazwy głównej usługi lub wystąpienia uwierzytelniania MSI.

- **Zalecenie**: Rozważ podanie nazwy głównej usługi, która ma uprawnienia do tworzenia klastra HDInsight w podanej subskrypcji, i spróbuj ponownie. Upewnij się, że [Zarządzanie tożsamościami jest prawidłowo skonfigurowane](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities). Jeśli nie jest to akceptowalne rozwiązanie, skontaktuj się z zespołem pomocy technicznej usługi ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--2300"></a>Kod błędu: 2300

- **Komunikat**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera komunikat podobny do "nie można rozpoznać nazwy zdalnej." może to oznaczać, że podany identyfikator URI klastra jest nieprawidłowy.


- **Zalecenie**: Upewnij się, że klaster nie został usunięty i że podany identyfikator URI jest prawidłowy. Po otwarciu identyfikatora URI w przeglądarce powinien zostać wyświetlony interfejs użytkownika Ambari. Jeśli klaster znajduje się w sieci wirtualnej, identyfikator URI powinien być prywatnym identyfikatorem URI. Aby go otworzyć, użyj maszyny wirtualnej, która jest częścią tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [ten](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)temat.
                  

<br>

- **Przyczyna**: Jeśli komunikat o błędzie zawiera komunikat podobny do "zadanie zostało anulowane." oznacza to, że upłynął limit czasu przesłania zadania.

- **Zalecenie**: przyczyną problemu może być ogólna łączność z usługą HDInsight lub łączność sieciowa. Najpierw upewnij się, że interfejs użytkownika usługi HDInsight Ambari jest dostępny z dowolnej przeglądarki. Upewnij się, że poświadczenia są nadal ważne. Aby uzyskać więcej informacji, Przeczytaj [Ambari internetowy interfejs użytkownika](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui). Jeśli używasz własnego środowiska uruchomieniowego (IR), upewnij się, że jest to możliwe z maszyny wirtualnej lub maszyny, na której zainstalowano samoobsługowe środowisko IR. Następnie spróbuj ponownie przesłać zadanie z Data Factory. Jeśli nadal się nie powiedzie, skontaktuj się z zespołem Data Factory, aby uzyskać pomoc techniczną.

<br>

- **Przyczyna**: Jeśli komunikat o błędzie zawiera komunikat podobny do "Administrator użytkownika jest zablokowany w Ambari" lub "nieautoryzowane: Ambari nazwa użytkownika lub hasło jest niepoprawne", oznacza to, że poświadczenia usługi HDInsight są nieprawidłowe lub wygasły.

- **Zalecenie**: Popraw poświadczenia i ponownie Wdróż połączoną usługę. Najpierw upewnij się, że poświadczenia działają w usłudze HDInsight, otwierając identyfikator URI klastra w dowolnej przeglądarce i próbując się zalogować. Jeśli poświadczenia nie działają, możesz je zresetować z poziomu Azure Portal. W przypadku klastra ESP możesz [zresetować hasło, korzystając z funkcji samoobsługowego resetowania hasła](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password).

<br>

- **Przyczyna**: Jeśli komunikat o błędzie zawiera komunikat podobny do "502 — serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy", ten błąd jest zwracany przez usługę HDInsight.

- **Zalecenie**: w przypadku błędu 502, większość czasu wynika z tego, że proces serwera Ambari został zamknięty. Usługi Ambari można uruchomić ponownie przez ponowne uruchomienie węzła głównego.  

    1. Połącz się z jednym z węzłów w usłudze HDInsight przy użyciu protokołu SSH.
    2. Określ hosta aktywnego węzła głównego, uruchamiając polecenie "ping headnodehost".
    3. Połącz się z aktywnym węzłem głównym, gdy serwer Ambari znajduje się w aktywnym węźle głównym przy użyciu protokołu SSH.  
    4. Uruchom ponownie aktywny węzeł główny.

        Aby uzyskać więcej informacji: Zapoznaj się z dokumentacją rozwiązywania problemów usługi Azure HDInsight, na przykład: [AMBARI UI 502 błąd](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [RpcTimeoutException for Apache Spark Thrift Server](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception), [Rozwiązywanie problemów z błędami bramy w programie Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br>

- **Przyczyna**: Jeśli komunikat o błędzie zawiera komunikat podobny do "nie można obsłużyć żądania przesłania zadania, ponieważ usługa Templeton jest zajęta za pomocą zbyt wielu żądań przesłania zadania" lub "root Queue. joblauncher ma już 500 aplikacji, nie można zaakceptować przesyłania aplikacji", oznacza to, że zbyt wiele zadań jest przesyłanych do usługi HDInsight w tym samym czasie.

- **Zalecenie**: Rozważ ograniczenie liczby współbieżnych zadań przesyłanych do usługi HDInsight. Zapoznaj się z Data Factory współbieżności działań, jeśli zadania są przesyłane przez to samo działanie. Zmień Wyzwalacze w celu rozłożenia współbieżnych uruchomień potoków w czasie. Zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors) usługi `templeton.parallellism.job.submit` HDInsight, aby dostosować ją w miarę sugerowania błędu.


### <a name="error-code--2301"></a>Kod błędu: 2301

- **Komunikat**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Przyczyna**: klaster lub Usługa HDInsight ma problemy.


- **Zalecenie**: ten błąd występuje, gdy usługa ADF nie pobierze odpowiedzi z klastra usługi HDInsight podczas próby uzyskania stanu uruchomionego zadania. Może to być przyczyną problemów z klastrem lub Usługa HDInsight może mieć awarię. Zapoznaj się z dokumentacją rozwiązywania https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideproblemów usługi HDInsight pod adresem lub skontaktuj się z pomocą techniczną, aby uzyskać dalszą pomoc.
                


### <a name="error-code--2302"></a>Kod błędu: 2302

- **Komunikat**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Przyczyna**: zadanie zostało przesłane do klastra HDI i nie powiodło się.

- **Zalecenie**: Postępuj zgodnie z linkiem dzienniki przędzy w danych wyjściowych przebiegu działania i poszukaj błędów w danych wyjściowych HDI. Skontaktuj się z zespołem usługi HDInsight, aby uzyskać pomoc w razie potrzeby.


### <a name="error-code--2303"></a>Kod błędu: 2303

- **Komunikat**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Przyczyna**: zadanie zostało przesłane do klastra HDI i nie powiodło się.

- **Zalecenie**: Postępuj zgodnie z linkiem dzienniki przędzy w danych wyjściowych przebiegu działania i poszukaj błędów w danych wyjściowych HDI. Spróbuj ponownie lub skontaktuj się z zespołem usługi HDInsight, aby uzyskać pomoc w razie potrzeby.


### <a name="error-code--2304"></a>Kod błędu: 2304

- **Komunikat**:`MSI authentication is not supported on storages for HDI activities.`

- **Przyczyna**: połączone usługi magazynu używane w działaniu HDI połączonej usługi lub HDI są skonfigurowane przy użyciu uwierzytelniania MSI, które nie jest obsługiwane.

- **Zalecenie**: Podaj pełne parametry połączenia dla kont magazynu używanych w ramach połączonej usługi HDI lub działania HDI.


### <a name="error-code--2305"></a>Kod błędu: 2305

- **Komunikat**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Przyczyna**: informacje o połączeniu dla klastra HDI są nieprawidłowe, podany użytkownik nie ma uprawnień do wykonania wymaganej akcji lub Usługa HDInsight ma problemy z odpowiedzią na żądania z usługi ADF.

- **Zalecenie**: Upewnij się, że informacje o użytkowniku są poprawne. Sprawdź również, czy interfejs użytkownika Ambari dla klastra HDI może być otwarty w przeglądarce z maszyny wirtualnej, na której jest zainstalowany program IR w przypadku samodzielnego środowiska IR, lub można go otworzyć z dowolnego komputera w przypadku Azure IR.


### <a name="error-code--2306"></a>Kod błędu: 2306

- **Komunikat**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Przyczyna**: kod JSON podany dla akcji skryptu jest nieprawidłowy.


- **Zalecenie**: komunikat o błędzie powinien pomóc w zidentyfikowaniu problemu. Popraw konfigurację JSON i spróbuj ponownie. Sprawdź https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service , aby uzyskać więcej informacji.
                

### <a name="error-code--2310"></a>Kod błędu: 2310

- **Komunikat**:`Failed to submit Spark job. Error: '%message;'`

- **Przyczyna**: moduł ADF próbował utworzyć partię w klastrze Spark przy użyciu interfejsu API usługi Livy (usługi Livy/Batch), ale wystąpił błąd.

- **Zalecenie**: Aby rozwiązać ten problem, postępuj zgodnie z komunikatem o błędzie. Jeśli nie ma wystarczającej ilości informacji, aby rozwiązać ten problem, skontaktuj się z zespołem HDI i podaj identyfikator partii i identyfikator zadania, które można znaleźć w danych wyjściowych przebiegu działania na stronie monitorowania ADF.


### <a name="error-code--2312"></a>Kod błędu: 2312

- **Komunikat**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Przyczyna**: zadanie nie powiodło się w klastrze usługi HDInsight Spark.

- **Zalecenie**: Postępuj zgodnie z linkami znajdującymi się na stronie monitorowania danych wyjściowych działania w ramach usługi ADF, aby rozwiązać problem z uruchamianiem w klastrze usługi HDInsight Spark. Skontaktuj się z zespołem pomocy technicznej usługi HDInsight, aby uzyskać dalszą pomoc.


### <a name="error-code--2313"></a>Kod błędu: 2313

- **Komunikat**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Przyczyna**: partia została usunięta w klastrze usługi HDInsight Spark.

- **Zalecenie**: Rozwiązywanie problemów z partiami w klastrze usługi HDInsight Spark. Skontaktuj się z pomocą techniczną HDInsight, aby uzyskać dalszą pomoc. 


### <a name="error-code--2328"></a>Kod błędu: 2328

- **Komunikat**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna**:`The error message should show the details of what went wrong.`

- **Zalecenie**: komunikat o błędzie powinien pomóc w rozwiązaniu problemu.


### <a name="error-code--2329"></a>Kod błędu: 2329

- **Komunikat**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna**: komunikat o błędzie powinien zawierać szczegóły dotyczące tego, co poszło źle.

- **Zalecenie**: komunikat o błędzie powinien pomóc w rozwiązaniu problemu.


### <a name="error-code--2331"></a>Kod błędu: 2331

- **Komunikat**:`The file path should not be null or empty.`

- **Przyczyna**: podana ścieżka pliku jest pusta.

- **Zalecenie**: Podaj ścieżkę do pliku, który istnieje.


### <a name="error-code--2340"></a>Kod błędu: 2340

- **Komunikat**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Przyczyna**: połączona usługa HDInsightOnDemand nie obsługuje wykonywania za pośrednictwem SelfHosted IR.

- **Zalecenie**: wybierz Azure IR i spróbuj ponownie.


### <a name="error-code--2341"></a>Kod błędu: 2341

- **Komunikat**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Przyczyna**: podany adres URL nie ma poprawnego formatu.

- **Zalecenie**: Popraw adres URL klastra i spróbuj ponownie.


### <a name="error-code--2342"></a>Kod błędu: 2342

- **Komunikat**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Przyczyna**: podane poświadczenia są nieprawidłowe dla klastra lub wystąpił problem z konfiguracją sieci lub połączeniem albo w środowisku IR występują problemy z połączeniem z klastrem.

- **Zalecenie**:  
      1. Sprawdź, czy poświadczenia są poprawne, otwierając interfejs użytkownika Ambari klastra usługi HDInsight w przeglądarce.
      2. Jeśli klaster znajduje się w sieci wirtualnej i jest używany własny IR, adres URL HDI powinien być prywatnym adresem URL w sieci wirtualnych, oznacza to, że po nazwie klastra powinna być dostępna wartość "-int". Na przykład "https://mycluster.azurehdinsight.net/" należy zmienić na "https://mycluster-int.azurehdinsight.net/".
      2. Jeśli klaster znajduje się w sieci wirtualnej, używany jest samoobsługowy środowisko IR, a prywatny adres URL był używany, a połączenie nadal nie powiodło się, a następnie maszyna wirtualna, na której zainstalowano środowisko IR, ma problemy z nawiązywaniem połączenia z HDI. Połącz się z maszyną wirtualną, na której zainstalowano środowisko IR, i Otwórz interfejs użytkownika Ambari w przeglądarce. Użyj prywatnego adresu URL klastra. To połączenie powinno być wykonane z przeglądarki. Jeśli nie, skontaktuj się z zespołem pomocy technicznej usługi HDInsight, aby uzyskać dalszą pomoc.
      3. Jeśli nie jest używane własne środowisko IR, klaster HDI powinien być dostępny publicznie. Otwórz interfejs użytkownika Ambari w przeglądarce i upewnij się, że zostanie otwarty. Jeśli wystąpią problemy z klastrem lub usługami na nim, skontaktuj się z zespołem pomocy technicznej usługi HDInsight w celu uzyskania pomocy.
      Ogólnie rzecz biorąc, adres URL klastra HDI używany w połączonej usłudze ADF musi być dostępny dla usługi ADF IR (samodzielny lub platformę Azure) w celu przeprowadzenia połączenia testowego i uruchomienia programu. Można to łatwo zweryfikować, otwierając ten adres URL z przeglądarki albo z maszyny wirtualnej, albo z dowolnej maszyny publicznej.
    


### <a name="error-code--2343"></a>Kod błędu: 2343

- **Komunikat**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Przyczyna**: Nazwa użytkownika lub hasło są puste.

- **Zalecenie**: Podaj poprawne poświadczenia, aby nawiązać połączenie z usługą HDI, i spróbuj ponownie.


### <a name="error-code--2345"></a>Kod błędu: 2345

- **Komunikat**:`Failed to read the content of the hive script. Error: '%message;'`

- **Przyczyna**: plik skryptu nie istnieje lub ADF nie może połączyć się z lokalizacją skryptu.

- **Zalecenie**: Sprawdź, czy skrypt istnieje, a skojarzona połączona usługa ma odpowiednie poświadczenia do połączenia.


### <a name="error-code--2346"></a>Kod błędu: 2346

- **Komunikat**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Przyczyna**: w ramach usługi ADF podjęto próbę nawiązania połączenia ODBC z klastrem HDI, a błąd nie powiódł się.

- **Zalecenie**: komunikat o błędzie i kod błędu powinny pomóc w rozwiązywaniu problemów z błędami połączenia ODBC. W przypadku, gdy nie wystarczy rozwiązać ten problem, skontaktuj się z zespołem usługi Azure HDInsight, aby uzyskać pomoc techniczną.


### <a name="error-code--2347"></a>Kod błędu: 2347

- **Komunikat**:`Hive execution through ODBC failed with error message '%message;'.`

- **Przyczyna**: moduł ADF przesłał skrypt Hive do wykonania do klastra HDI za pośrednictwem połączenia ODBC, a skrypt zakończył się niepowodzeniem w HDI.

- **Zalecenie**: wykonanie skryptu Hive nie powiodło się w klastrze HDI oraz komunikat o błędzie i kod błędu powinien pomóc w rozwiązywaniu problemów. W przypadku, gdy nie wystarczy rozwiązać ten problem, skontaktuj się z zespołem usługi Azure HDInsight, aby uzyskać pomoc techniczną.


### <a name="error-code--2348"></a>Kod błędu: 2348

- **Komunikat**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Przyczyna**: właściwości połączonej usługi Storage nie są ustawione prawidłowo.

- **Zalecenie**: tylko pełne parametry połączenia są obsługiwane w głównej usłudze połączonej magazynu dla działań HDI. Upewnij się, że nie używasz uwierzytelniania lub aplikacji MSI.


### <a name="error-code--2350"></a>Kod błędu: 2350

- **Komunikat**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Przyczyna**: poświadczenia podane w celu nawiązania połączenia z magazynem, w którym znajdują się pliki, są nieprawidłowe lub pliki te nie istnieją.

- **Zalecenie**: ten błąd występuje, gdy ADF wykonuje kroki przygotowania dla działań HDI. Próbuje on skopiować pliki do magazynu głównego przed przesłaniem zadania do HDI. Upewnij się, że pliki znajdują się w podanej lokalizacji, połączenie z magazynem jest poprawne. Działania HDI APD nie obsługują uwierzytelniania MSI na kontach magazynu związanych z działaniami HDI, dlatego upewnij się, że te połączone usługi mają pełne klucze lub używają Azure Key Vault.


### <a name="error-code--2351"></a>Kod błędu: 2351

- **Komunikat**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Przyczyna**: plik nie istnieje w określonej ścieżce.

- **Zalecenie**: Sprawdź, czy plik rzeczywiście istnieje, a połączona usługa z informacjami o połączeniu wskazującym na ten plik ma poprawne poświadczenia.


### <a name="error-code--2352"></a>Kod błędu: 2352

- **Komunikat**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Przyczyna**: właściwości połączonej usługi magazynu plików nie są ustawione prawidłowo.

- **Zalecenie**: Upewnij się, że właściwości połączonej usługi magazynu plików są prawidłowo skonfigurowane.


### <a name="error-code--2353"></a>Kod błędu: 2353

- **Komunikat**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Przyczyna**: właściwości połączonej usługi magazynu skryptów nie są ustawione prawidłowo.

- **Zalecenie**: Upewnij się, że właściwości połączonej usługi magazynu skryptów są prawidłowo skonfigurowane.


### <a name="error-code--2354"></a>Kod błędu: 2354

- **Komunikat**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Przyczyna**: typ połączonej usługi Storage nie jest obsługiwany przez działanie.

- **Zalecenie**: Upewnij się, że wybrana połączona usługa ma jeden z obsługiwanych typów dla działania. Działania HDI obsługują usługi połączone AzureBlobStorage i AzureBlobFSStorage. Aby uzyskać więcej informacji, przeczytaj temat [porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options)


### <a name="error-code--2355"></a>Kod błędu: 2355

- **Komunikat**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Przyczyna**: podana dla commandEnvironment jest niepoprawna.

- **Zalecenie**:  
      Upewnij się, że podana wartość jest podobna do: \"commandEnvironment\": [ \"VariableName = VariableValue\" ], a Każda zmienna pojawia się na liście tylko raz.
    


### <a name="error-code--2356"></a>Kod błędu: 2356

- **Komunikat**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Przyczyna**: zmienna została podana dwukrotnie w commandEnvironment.

- **Zalecenie**:  
      Upewnij się, że podana wartość jest podobna do: \"commandEnvironment\": [ \"VariableName = VariableValue\" ], a Każda zmienna pojawia się na liście tylko raz.
    


### <a name="error-code--2357"></a>Kod błędu: 2357

- **Komunikat**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Przyczyna**: podane poświadczenia są nieprawidłowe.

- **Zalecenie**: Sprawdź informacje o połączeniu w połączonej usłudze ADLS Gen 1 i upewnij się, że połączenie testowe zakończy się pomyślnie.


### <a name="error-code--2358"></a>Kod błędu: 2358

- **Komunikat**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Przyczyna**: podana wartość dla wymaganej właściwości "TimeToLive" ma nieprawidłowy format. 

- **Zalecenie**: zaktualizuj wartość do sugerowanego zakresu i spróbuj ponownie.


### <a name="error-code--2359"></a>Kod błędu: 2359

- **Komunikat**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Przyczyna**: podana wartość właściwości "role" jest nieprawidłowa.

- **Zalecenie**: zaktualizuj wartość tak, aby była jedną z sugestii, i spróbuj ponownie.


### <a name="error-code--2360"></a>Kod błędu: 2360

- **Komunikat**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Przyczyna**: podane parametry połączenia dla HCatalogLinkedService są nieprawidłowe.

- **Zalecenie**: zaktualizuj wartość do poprawnych parametrów połączenia SQL platformy Azure i spróbuj ponownie.


### <a name="error-code--2361"></a>Kod błędu: 2361

- **Komunikat**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Przyczyna**: utworzenie klastra nie powiodło się, a w usłudze HDInsight nie został zwrócony błąd.

- **Zalecenie**: Otwórz Azure Portal i spróbuj znaleźć zasób HDI o podanej nazwie i sprawdź stan aprowizacji. Skontaktuj się z zespołem pomocy technicznej HDInsight, aby uzyskać dalszą pomoc.


### <a name="error-code--2362"></a>Kod błędu: 2362

- **Komunikat**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Przyczyna**: podany dodatkowy magazyn nie był magazynem obiektów blob platformy Azure.

- **Zalecenie**: Podaj konto magazynu obiektów blob platformy Azure jako dodatkowy magazyn dla połączonej usługi HDInsight na żądanie.



## <a name="web-activity"></a>Działanie internetowe

### <a name="error-code--2128"></a>Kod błędu: 2128

- **Komunikat**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Przyczyna**: łączność sieciowa, błąd DNS, weryfikacja certyfikatu serwera lub przekroczenie limitu czasu.

- **Zalecenie**: Sprawdź, czy punkt końcowy, który próbujesz trafić, odpowiada na żądania. Można używać narzędzi takich jak programu Fiddler/Poster.


### <a name="error-code--2108"></a>Kod błędu: 2108

- **Komunikat**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Przyczyna**: żądanie nie powiodło się z powodu podstawowego problemu, takiego jak połączenie sieciowe, błąd DNS, weryfikacja certyfikatu serwera lub przekroczenie limitu czasu.

- **Zalecenie**: Użyj programu Fiddler/Poster, aby sprawdzić poprawność żądania.
<br>


#### <a name="more-details"></a>Więcej szczegółów
Aby użyć programu Fiddler do utworzenia sesji HTTP monitorowanej aplikacji sieci Web:

1. Pobierz, zainstaluj i Otwórz [programu Fiddler](https://www.telerik.com/download/fiddler).

1. Jeśli aplikacja sieci Web używa protokołu HTTPS, przejdź do pozycji **Narzędzia** > **programu Fiddler opcje** > **https**. Wybierz pozycję **Przechwyć połączenie HTTPS** i **Odszyfruj ruch https**.

   ![Opcje programu Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Jeśli aplikacja używa certyfikatów TLS/SSL, Dodaj do urządzenia certyfikat programu Fiddler. Przejdź do **narzędzi Narzędzia** > **programu Fiddler opcje** > **https** > **Actions** > **Eksportuj certyfikat główny do pulpitu**.

1. Wyłącz przechwytywanie, przechodząc do **File** > **ruchu przechwytywania**plików. Lub naciśnij klawisz **F12**.

1. Wyczyść pamięć podręczną przeglądarki, aby wszystkie elementy w pamięci podręcznej zostały usunięte i muszą zostać pobrane ponownie.

1. Utwórz żądanie:

   1. Wybierz kartę **układacz** .

   1. Ustaw metodę HTTP i adres URL.
   
   1. Dodaj nagłówki i treść żądania, jeśli zachodzi taka potrzeba.

   1. Wybierz pozycję **Wykonaj**.

1. Włącz ponownie przechwytywanie ruchu i Ukończ problematyczną transakcję na stronie.

1. Przejdź do **pliku** > **Zapisz** > **wszystkie sesje**.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą programu Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)


            
