---
title: Rozwiązywanie problemów z fabryką danych platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z działaniami kontroli zewnętrznej w usłudze Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c9a1ac831c4300c0523717fddc1fa53417068b89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416573"
---
# <a name="troubleshoot-azure-data-factory"></a>Rozwiązywanie problemów z fabryką danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano typowe metody rozwiązywania problemów z zewnętrznymi działaniami kontroli w usłudze Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Działanie łącznika i kopiowania

W przypadku problemów z łącznikami, takich jak napotkanie błędu przy użyciu działania kopiowania, zobacz [Rozwiązywanie problemów z łącznikami fabryki danych platformy Azure](connector-troubleshoot-guide.md).
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Kod błędu: 3200

- **Komunikat**: Błąd 403.

- **Przyczyna**:`The Databricks access token has expired.`

- **Zalecenie:** Domyślnie token dostępu usługi Azure Databricks jest ważny przez 90 dni. Utwórz nowy token i zaktualizuj usługę połączony.


### <a name="error-code--3201"></a>Kod błędu: 3201

- **Komunikat**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Przyczyna**:`Bad authoring: Notebook path not specified correctly.`

- **Zalecenie:** Określ ścieżkę notesu w działaniu Databricks.

<br/>  

- **Komunikat**:`Cluster... does not exist.`

- **Przyczyna**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Zalecenie:** Sprawdź, czy istnieje klaster Databricks.

<br/>  

- **Komunikat**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie:** Określ ścieżki bezwzględne dla schematów adresowania `dbfs:/folder/subfolder/foo.py` obszaru roboczego lub dla plików przechowywanych w systemie plików Databricks.

<br/>  

- **Komunikat**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Sprawdź [definicję usługi połączonej](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Komunikat**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Sprawdź [definicję usługi połączonej](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Komunikat**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Przyczyna**:`Bad authoring.`

- **Zalecenie**: Zapoznaj się z komunikatem o błędzie.

<br/>

### <a name="error-code--3202"></a>Kod błędu: 3202

- **Komunikat**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Przyczyna**:`Too many Databricks runs in an hour.`

- **Zalecenie:** Sprawdź wszystkie potoki, które używają tego obszaru roboczego Databricks dla ich szybkości tworzenia zadań.  Jeśli potoki uruchomione zbyt wiele Databricks działa w zagregowane, migracji niektórych potoków do nowego obszaru roboczego.

<br/>  

- **Komunikat**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Przyczyna**:`Authoring error: No value provided for the parameter.`

- **Zalecenie:** Sprawdź potok JSON i upewnij się, że wszystkie parametry w notesie baseParameters określić wartość niepuchdną.

<br/>  

- **Komunikat** `User: `: SimpleUserContext{userId=...,user@company.comname= , orgId=...}` is not   authorized to access cluster.`

- **Przyczyna:** Użytkownik, który wygenerował token dostępu, nie może uzyskać dostępu do klastra Databricks określonego w połączonej usłudze.

- **Zalecenie:** Upewnij się, że użytkownik ma wymagane uprawnienia w obszarze roboczym.


### <a name="error-code--3203"></a>Kod błędu: 3203

- **Komunikat**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Przyczyna:** Klaster został zakończony. W przypadku klastrów interaktywnych może to być stan wyścigu.

- **Zalecenie:** Najlepszym sposobem uniknięcia tego błędu jest użycie klastrów zadań.


### <a name="error-code--3204"></a>Kod błędu: 3204

- **Komunikat**:`Job execution failed.`

- **Przyczyna:** Komunikaty o błędach wskazują różne problemy, takie jak nieoczekiwany stan klastra lub określone działanie. Najczęściej w ogóle nie pojawia się żaden komunikat o błędzie.

- **Zalecenie**: Nie dotyczy
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Poniższa tabela dotyczy języka U-SQL.
      
### <a name="error-code--2709"></a>Kod błędu: 2709

- **Komunikat**:`The access token is from the wrong tenant.`

- **Przyczyna:** Niepoprawna dzierżawa usługi Azure Active Directory (Azure AD).

- **Zalecenie:** Niepoprawna dzierżawa usługi Azure Active Directory (Azure AD).

<br/>

- **Komunikat**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Przyczyna:** Ten błąd jest spowodowany przez ograniczanie w usłudze Data Lake Analytics.

- **Zalecenie:** Zmniejsz liczbę przesłanych zadań do usługi Data Lake Analytics, zmieniając wyzwalacze fabryki danych i ustawienia współbieżności w działaniach. Możesz też zwiększyć limity w usłudze Data Lake Analytics.

<br/>  

- **Komunikat**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Przyczyna:** Ten błąd jest spowodowany przez ograniczanie w usłudze Data Lake Analytics.

- **Zalecenie:** Zmniejsz liczbę przesłanych zadań do usługi Data Lake Analytics, zmieniając wyzwalacze fabryki danych i ustawienia współbieżności w działaniach. Możesz też zwiększyć limity w usłudze Data Lake Analytics.


### <a name="error-code--2705"></a>Kod błędu: 2705

- **Komunikat**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Przyczyna:** Podmiot usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie:** Upewnij się, że podmiot usługi lub certyfikat, który użytkownik udostępnia dla zadań usługi Data Lake Analytics, ma dostęp do konta Usługi Data Lake Analytics i domyślnego wystąpienia magazynu usługi Data Lake z folderu głównego.


### <a name="error-code--2711"></a>Kod błędu: 2711

- **Komunikat**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Przyczyna:** Podmiot usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie:** Upewnij się, że podmiot usługi lub certyfikat, który użytkownik udostępnia dla zadań usługi Data Lake Analytics, ma dostęp do konta Usługi Data Lake Analytics i domyślnego wystąpienia magazynu usługi Data Lake z folderu głównego.

<br/>  

- **Komunikat**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Przyczyna:** Ścieżka do pliku U-SQL jest nieprawidłowa lub poświadczenia połączonej usługi nie mają dostępu.

- **Zalecenie:** Sprawdź ścieżkę i poświadczenia podane w połączonej usłudze.


### <a name="error-code--2704"></a>Kod błędu: 2704

- **Komunikat**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Przyczyna:** Podmiot usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie:** Upewnij się, że podmiot usługi lub certyfikat, który użytkownik udostępnia dla zadań usługi Data Lake Analytics, ma dostęp do konta Usługi Data Lake Analytics i domyślnego wystąpienia magazynu usługi Data Lake z folderu głównego.


### <a name="error-code--2707"></a>Kod błędu: 2707

- **Komunikat**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Przyczyna:** Konto Usługi Data Lake Analytics w połączonej usłudze jest nieprawidłowe.

- **Zalecenie:** Sprawdź, czy dostępne jest odpowiednie konto.


### <a name="error-code--2703"></a>Kod błędu: 2703

- **Komunikat**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Przyczyna:** Błąd pochodzi z usługi Data Lake Analytics.

- **Zalecenie:** Błąd podobny do przykładu oznacza, że zadanie zostało przesłane do usługi Data Lake Analytics, a skrypt nie powiódł się. Zbadaj w usłudze Data Lake Analytics. W portalu przejdź do konta Usługi Data Lake Analytics i poszukaj zadania przy użyciu identyfikatora uruchomienia działania usługi Data Factory (nie identyfikatora uruchomienia potoku). Zadanie zawiera więcej informacji na temat błędu i pomoże ci rozwiązać problem. Jeśli rozwiązanie nie jest jasne, skontaktuj się z zespołem pomocy technicznej Usługi Data Lake Analytics i podaj adres URL zadania, który zawiera nazwę konta i identyfikator zadania.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Kod błędu: 3602

- **Komunikat**:`Invalid HttpMethod: '%method;'.`

- **Przyczyna:** Metoda Http określona w ładunku działania nie jest obsługiwana przez działanie funkcji platformy Azure.

- **Zalecenie**: Metody Http, które są obsługiwane są PUT, POST, GET, DELETE, OPCJE, HEAD i TRACE.


### <a name="error-code--3603"></a>Kod błędu: 3603

- **Komunikat**:`Response Content is not a valid JObject.`

- **Przyczyna:** Funkcja platformy Azure, która została wywołana, nie zwróciła ładunku JSON w odpowiedzi. Działanie funkcji platformy ADF Azure obsługuje tylko zawartość odpowiedzi JSON.

- **Zalecenie:** Zaktualizuj funkcję platformy Azure, aby zwrócić prawidłowy ładunek JSON, np.\"\"\"\"


### <a name="error-code--3606"></a>Kod błędu: 3606

- **Komunikat**: Brak klucza funkcji usługi Azure brak klucza funkcji.

- **Przyczyna:** Definicja działania funkcji platformy Azure nie jest kompletna.

- **Zalecenie:** Sprawdź wejście AzureFunction działania JSON definicja ma właściwość o nazwie "functionKey".


### <a name="error-code--3607"></a>Kod błędu: 3607

- **Komunikat**:`Azure function activity missing function name.`

- **Przyczyna:** Definicja działania funkcji platformy Azure nie jest kompletna.

- **Zalecenie:** Sprawdź wejście AzureFunction działania JSON definicja ma właściwość o nazwie "functionName".


### <a name="error-code--3608"></a>Kod błędu: 3608

- **Komunikat**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Przyczyna:** Szczegóły funkcji platformy Azure w definicji działania mogą być niepoprawne.

- **Zalecenie:** Napraw szczegóły funkcji platformy azure i ponów próbę ponownie.


### <a name="error-code--3609"></a>Kod błędu: 3609

- **Komunikat**:`Azure function activity missing functionAppUrl.`

- **Przyczyna:** Definicja działania funkcji platformy Azure nie jest kompletna.

- **Zalecenie:** Sprawdź wejście AzureFunction działania JSON definicja ma właściwość o nazwie "functionAppUrl".


### <a name="error-code--3610"></a>Kod błędu: 3610

- **Komunikat**:`There was an error while calling endpoint.`

- **Przyczyna:** Adres URL funkcji może być niepoprawny.

- **Zalecenie:** Upewnij się, że wartość "functionAppUrl" w działaniu JSON jest poprawna i spróbuj ponownie.


### <a name="error-code--3611"></a>Kod błędu: 3611

- **Komunikat**:`Azure function activity missing Method in JSON.`

- **Przyczyna:** Definicja działania funkcji platformy Azure nie jest kompletna.

- **Zalecenie:** Sprawdź wejście AzureFunction działania JSON definicja ma właściwość o nazwie "metoda".


### <a name="error-code--3612"></a>Kod błędu: 3612

- **Komunikat**:`Azure function activity missing LinkedService definition in JSON.`

- **Przyczyna:** Definicja działania funkcji platformy Azure nie jest kompletna.

- **Zalecenie:** Sprawdź dane wejściowe AzureFunction działania JSON definicja połączyła szczegóły usługi.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Kod błędu: 4101

- **Komunikat**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Przyczyna:** Nieprawidłowy format lub brak definicji właściwości '%propertyName;'.

- **Zalecenie:** Sprawdź, czy działanie "%activityName;" ma właściwość "%propertyName;" zdefiniowaną z poprawnymi danymi.


### <a name="error-code--4110"></a>Kod błędu: 4110

- **Komunikat**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Przyczyna:** Definicja działania AzureMLExecutePipeline nie jest kompletna.

- **Zalecenie:** Sprawdź, czy wejście AzureMLExecutePipeline działania JSON definicja JSON ma połączone szczegóły usługi.


### <a name="error-code--4111"></a>Kod błędu: 4111

- **Komunikat**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Przyczyna:** Nieprawidłowa definicja działania.

- **Zalecenie:** Sprawdź, czy wejście AzureMLExecutePipeline działania JSON definicja ma poprawne szczegóły połączonej usługi.


### <a name="error-code--4112"></a>Kod błędu: 4112

- **Komunikat**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Przyczyna:** Nieprawidłowy format lub brak definicji właściwości '%propertyName;'.

- **Zalecenie:** Sprawdź, czy usługa połączona ma właściwość "%propertyName;" zdefiniowaną z poprawnymi danymi.


### <a name="error-code--4121"></a>Kod błędu: 4121

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna:** Poświadczenia używane do uzyskiwania dostępu do usługi Azure Machine Learning wygasły.

- **Zalecenie**: Sprawdź, czy dane uwierzytelniające są prawidłowe i ponów próbę


### <a name="error-code--4122"></a>Kod błędu: 4122

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna:** Poświadczenia podane w usłudze Linked Service usługi Azure Machine Learning są nieprawidłowe lub nie mają uprawnień do operacji.

- **Zalecenie:** Sprawdź, czy poświadczenia w usłudze linked service są prawidłowe i mają uprawnienia dostępu do usługi Azure Machine Learning.


### <a name="error-code--4123"></a>Kod błędu: 4123

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna:** Właściwości działania, takie jak pipelineParameters są nieprawidłowe dla potoku usługi Azure ML.

- **Zalecenie:** Sprawdź wartość właściwości działania, aby dopasować oczekiwany ładunek opublikowanego potoku usługi Azure ML określonego w usłudze połączonej.


### <a name="error-code--4124"></a>Kod błędu: 4124

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna:** Opublikowany punkt końcowy potoku usługi Azure ML nie istnieje.

- **Zalecenie:** Sprawdź, czy opublikowany punkt końcowy potoku usługi Azure Machine Learning określony w usłudze połączonej istnieje w usłudze Azure Machine Learning.


### <a name="error-code--4125"></a>Kod błędu: 4125

- **Komunikat**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Przyczyna:** Błąd serwera w usłudze Azure Machine Learning.

- **Zalecenie:** Ponów próbę później. Skontaktuj się z zespołem usługi Azure Machine Learning, aby uzyskać pomoc w przypadku pozostania problemem.


### <a name="error-code--4126"></a>Kod błędu: 4126

- **Komunikat**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Przyczyna:** Uruchomienie potoku usługi Azure ML nie powiodło się.

- **Zalecenie:** Sprawdź w usłudze Azure Machine Learning więcej dzienników błędów i naprawić potoku uczenia maszynowego.



## <a name="common"></a>Wspólne

### <a name="error-code--2103"></a>Kod błędu: 2103

- **Komunikat**:`Please provide value for the required property '%propertyName;'.`

- **Przyczyna:** Wartość właściwości nie została podana, jednak jest to wymagane w scenariuszu.

- **Zalecenie:** Podaj wartość z wiadomości i spróbuj ponownie.


### <a name="error-code--2104"></a>Kod błędu: 2104

- **Komunikat**:`The type of the property '%propertyName;' is incorrect.`

- **Przyczyna:** Typ dostarczonej właściwości nie jest zgodnie z oczekiwaniami.

- **Zalecenie:** Proszę naprawić typ obiektu i spróbuj ponownie.


### <a name="error-code--2105"></a>Kod błędu: 2105

- **Komunikat**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Przyczyna:** Wartość właściwości jest nieprawidłowa lub nie ma oczekiwanego formatu.

- **Zalecenie:** Wyszukaj dokumentację właściwości i upewnij się, że podana wartość ma oczekiwany format i typ.


### <a name="error-code--2106"></a>Kod błędu: 2106

- **Komunikat**:`The storage connection string is invalid. %errorMessage;`

- **Przyczyna:** Parametry połączenia dla magazynu są nieprawidłowe lub mają nieprawidłowy format.

- **Zalecenie:** Przejdź do witryny Azure portal, znajdź swój magazyn, skopiuj parametry połączenia i wklej w połączonej usłudze i spróbuj ponownie.


### <a name="error-code--2108"></a>Kod błędu: 2108

- **Komunikat**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Przyczyna:** Żądanie nie powiodło się z powodu podstawowego problemu, takiego jak łączność sieciowa, awaria systemu DNS, sprawdzanie poprawności certyfikatu serwera lub limit czasu.

- **Zalecenie:** Użyj Fiddler / Listonosz do sprawdzania poprawności żądania.


### <a name="error-code--2110"></a>Kod błędu: 2110

- **Komunikat**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Przyczyna:** Usługa połączona określona w działaniu była nieprawidłowa.

- **Zalecenie:** Upewnij się, że typ połączonej usługi jest jednym z obsługiwanych typów dla działania. Na przykład dla działań HDI typu połączonej usługi może być HDInsight lub HDInsightOnDemand.


### <a name="error-code--2111"></a>Kod błędu: 2111

- **Komunikat**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Przyczyna:** Typ dostarczonej właściwości nie jest zgodnie z oczekiwaniami.

- **Zalecenie:** Proszę naprawić typ obiektu i spróbuj ponownie.


### <a name="error-code--2112"></a>Kod błędu: 2112

- **Komunikat**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Przyczyna:** Typ chmury nie jest obsługiwane lub nie można określić dla magazynu z EndpointSuffix.

- **Zalecenie:** Użyj pamięci masowej w innej chmurze i spróbuj ponownie.


### <a name="error-code--2128"></a>Kod błędu: 2128

- **Komunikat**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Przyczyna:** Łączność sieciowa, awaria systemu DNS, sprawdzanie poprawności certyfikatu serwera lub limit czasu.

- **Zalecenie:** Sprawdź, czy punkt końcowy, który próbujesz trafić, odpowiada na żądania. Możesz użyć narzędzi, takich jak Fiddler / Listonosz.



## <a name="custom"></a>Niestandardowy

Poniższa tabela dotyczy usługi Azure Batch.
      
### <a name="error-code--2500"></a>Kod błędu: 2500

- **Komunikat**:`Hit unexpected exception and execution failed.`

- **Przyczyna**:`Can't launch command, or the program returned an error code.`

- **Zalecenie:** Upewnij się, że plik wykonywalny istnieje. Jeśli program został uruchomiony, upewnij się, że *pliki stdout.txt* i *stderr.txt* zostały przesłane na konto magazynu. Jest dobrą praktyką do emitowania obfitych dzienników w kodzie do debugowania.


### <a name="error-code--2501"></a>Kod błędu: 2501

- **Komunikat**:`Cannot access user batch account; please check batch account settings.`

- **Przyczyna:** Niepoprawny klucz dostępu wsadowego lub nazwa puli.

- **Zalecenie:** Sprawdź nazwę puli i klucz dostępu wsadowego w połączonej usłudze.


### <a name="error-code--2502"></a>Kod błędu: 2502

- **Komunikat**:`Cannot access user storage account; please check storage account settings.`

- **Przyczyna:** Nieprawidłowa nazwa konta magazynu lub klucz dostępu.

- **Zalecenie:** Sprawdź nazwę konta magazynu i klucz dostępu w połączonej usłudze.


### <a name="error-code--2504"></a>Kod błędu: 2504

- **Komunikat**:`Operation returned an invalid status code 'BadRequest'.`

- **Przyczyna:** Zbyt wiele plików w folderPath działania niestandardowego. Całkowity rozmiar resourceFiles nie może być większy niż 32 768 znaków.

- **Zalecenie:** Usuń niepotrzebne pliki. Lub skompresuj je i dodaj polecenie rozpakować, aby je wyodrębnić. Na przykład, użyj`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Kod błędu: 2505

- **Komunikat**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Przyczyna:** Działania niestandardowe obsługują tylko konta magazynu, które używają klucza dostępu.

- **Zalecenie**: Zapoznaj się z opisem błędu.


### <a name="error-code--2507"></a>Kod błędu: 2507

- **Komunikat**:`The folder path does not exist or is empty: ...`

- **Przyczyna:** Żadne pliki nie znajdują się na koncie magazynu w określonej ścieżce.

- **Zalecenie:** Ścieżka folderu musi zawierać pliki wykonywalne, które chcesz uruchomić.


### <a name="error-code--2508"></a>Kod błędu: 2508

- **Komunikat**:`There are duplicate files in the resource folder.`

- **Przyczyna:** Wiele plików o tej samej nazwie znajduje się w różnych podfolderach folderPath.

- **Zalecenie**: Działania niestandardowe spłaszczyć strukturę folderów w folderPath.  Jeśli chcesz zachować strukturę folderów, skompresuj pliki i wyodrębnij je w usłudze Azure Batch za pomocą polecenia rozpakuj. Na przykład, użyj`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Kod błędu: 2509

- **Komunikat**:`Batch   url ... is invalid; it must be in Uri format.`

- **Przyczyna**: Adresy URL partii muszą być podobne do`https://mybatchaccount.eastus.batch.azure.com`

- **Zalecenie**: Zapoznaj się z opisem błędu.


### <a name="error-code--2510"></a>Kod błędu: 2510

- **Komunikat**:`An   error occurred while sending the request.`

- **Przyczyna:** Adres URL partii jest nieprawidłowy.

- **Zalecenie:** Sprawdź adres URL partii.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Kod błędu: 200

- **Komunikat**:`Unexpected error happened: '%error;'.`

- **Przyczyna:** Występuje wewnętrzny problem z usługą.

- **Zalecenie**: Prosimy o kontakt z pomocą techniczną ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--201"></a>Kod błędu: 201

- **Komunikat**:`JobType %jobType; is not found.`

- **Przyczyna:** Istnieje nowy typ zadania, który nie jest obsługiwany przez podajnik ADF.

- **Zalecenie:** Prosimy o kontakt z zespołem pomocy technicznej ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--202"></a>Kod błędu: 202

- **Komunikat**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna:** Komunikat o błędzie powinien wyświetlać szczegóły tego, co poszło nie tak.

- **Zalecenie:** Komunikat o błędzie powinien pomóc w rozwiązaniu problemu. Jeśli nie ma wystarczającej ilości informacji, skontaktuj się z pomocą techniczną ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--203"></a>Kod błędu: 203

- **Komunikat**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna:** Komunikat o błędzie powinien wyświetlać szczegóły tego, co poszło nie tak.

- **Zalecenie:** Komunikat o błędzie powinien pomóc w rozwiązaniu problemu. Jeśli nie ma wystarczającej ilości informacji, skontaktuj się z pomocą techniczną ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--204"></a>Kod błędu: 204

- **Komunikat**:`The resumption token is missing for runId '%runId;'.`

- **Przyczyna:** Występuje wewnętrzny problem z usługą.

- **Zalecenie**: Prosimy o kontakt z pomocą techniczną ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--205"></a>Kod błędu: 205

- **Komunikat**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Przyczyna:** Wystąpił błąd podczas tworzenia klastra HDI na żądanie.

- **Zalecenie**: Prosimy o kontakt z pomocą techniczną ADF w celu uzyskania dalszej pomocy.


### <a name="error-code--206"></a>Kod błędu: 206

- **Komunikat**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Przyczyna:** Wystąpił wewnętrzny problem z usługą, która spowodowała to.

- **Zalecenie**: Może to być przejściowy problem. Ponów próbę wykonania zadania, a jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną podajnika ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--207"></a>Kod błędu: 207

- **Komunikat**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Przyczyna:** Wystąpił błąd wewnętrzny podczas próby określenia regionu z podstawowego konta magazynu.

- **Zalecenie:** Spróbuj użyć innego miejsca do magazynowania. W przypadku, gdy nie jest to możliwe do przyjęcia rozwiązanie, skontaktuj się z zespołem pomocy technicznej ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--208"></a>Kod błędu: 208

- **Komunikat**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Przyczyna:** Wystąpił błąd wewnętrzny podczas próby odczytu jednostki usługi lub wystąpienia uwierzytelniania MSI.

- **Zalecenie:** Należy rozważyć zapewnienie jednostki usługi, która ma uprawnienia do tworzenia klastra HDInsight w dostarczonej subskrypcji i spróbuj ponownie. W przypadku, gdy nie jest to możliwe do przyjęcia rozwiązanie, skontaktuj się z zespołem pomocy technicznej ADF, aby uzyskać dalszą pomoc.


### <a name="error-code--2300"></a>Kod błędu: 2300

- **Komunikat**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Przyczyna:** Jeśli komunikat o błędzie zawiera komunikat podobny do "Nie można rozpoznać nazwy zdalnej.", może to oznaczać, że podany identyfikator URI klastra jest nieprawidłowy.


- **Zalecenie:** Upewnij się, że klaster nie został usunięty i że podany identyfikator URI jest poprawny. Po otwarciu identyfikatora URI w przeglądarce powinien zostać wyświetlony interfejs użytkownika Ambari. Jeśli klaster znajduje się w sieci wirtualnej, identyfikator URI powinien być prywatnym identyfikatorem URI. Aby ją otworzyć, należy użyć maszyny Wirtualnej, która jest częścią tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [ten](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Przyczyna:** Gdy komunikat o błędzie zawiera komunikat podobny do "Zadanie zostało anulowane.", oznacza to, że przesunął się limit czasu przesyłania zadania.

- **Zalecenie:** Problemem może być ogólna łączność HDInsight lub łączność sieciowa. Najpierw upewnij się, że interfejs HDInsight Ambari jest dostępny w dowolnej przeglądarce. Upewnij się, że poświadczenia są nadal ważne. Jeśli używasz samodzielnie hostowanego zintegrowanego środowiska uruchomieniowego (IR), upewnij się, że to zrobić z maszyny wirtualnej lub komputera, na którym jest zainstalowany własny hostowany podczerwony podczerwony. Następnie spróbuj ponownie przesłać zadanie z usługi Data Factory. Jeśli nadal nie powiedzie się, skontaktuj się z zespołem fabryki danych, aby uzyskać pomoc.

<br>

- **Przyczyna:** Jeśli komunikat o błędzie zawiera komunikat "Administrator użytkownika jest zablokowany w Ambari" lub "Nieautoryzowane: Nazwa użytkownika ambari lub hasło jest nieprawidłowe", oznacza to, że poświadczenia dla HDInsight są nieprawidłowe lub wygasły.

- **Zalecenie:** Popraw poświadczenia i ponownie wdrożyć połączony serwis. Najpierw upewnij się, że poświadczenia działają na hdinsight, otwierając identyfikator URI klastra w dowolnej przeglądarce i próbuje się zalogować. Jeśli poświadczenia nie działają, można je zresetować z witryny Azure portal.

<br>

- **Przyczyna:** Gdy komunikat o błędzie zawiera komunikat podobny do '502 - Serwer sieci Web otrzymał nieprawidłową odpowiedź, działając jako brama lub serwer proxy', ten błąd jest zwracany przez usługę HDInsight.


- **Zalecenie:** Przejrzyj dokumentację rozwiązywania problemów https://hdinsight.github.io/ambari/ambari-ui-502-error.html https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlusługi https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502Azure HDInsight, na przykład , .
                  

<br>

- **Przyczyna:** Gdy komunikat o błędzie zawiera komunikat podobny do "Nie można obsłużyć żądania zadania, ponieważ usługa templeton jest zajęta zbyt dużą ą ofertą składania ofert pracy" lub "Queue root.joblauncher ma już 500 aplikacji, nie może zaakceptować składania wniosku", oznacza to, że zbyt wiele ofert pracy jest przesyłanych do usługi HDInsight w tym samym czasie.

- **Zalecenie:** Należy rozważyć ograniczenie liczby równoczesnych zadań przesłanych do pliku HDInsight. Zapoznaj się z współbieżności działania fabryki danych, jeśli zadania są przesyłane przez to samo działanie. Zmień wyzwalacze, aby przebiegi potoku równoczesnych były rozłożone w czasie. Zapoznaj się z dokumentacją HDInsight, aby dostosować templeton.parallellism.job.submit, jak sugeruje błąd.


### <a name="error-code--2301"></a>Kod błędu: 2301

- **Komunikat**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Przyczyna:** Klaster lub usługa HDInsight ma problemy.


- **Zalecenie:** Ten błąd występuje, gdy usługa ADF nie otrzymuje odpowiedzi z klastra HDInsight podczas próby uzyskania stanu uruchomionego zadania. Może to być przyczyną problemów w samym klastrze lub usługa HDInsight może mieć awarię. Aby uzyskać dalszą pomoc, https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guidenależy zapoznać się z dokumentacją rozwiązywania problemów z programem HDInsight lub skontaktować się z pomocą techniczną.
                


### <a name="error-code--2302"></a>Kod błędu: 2302

- **Komunikat**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Przyczyna:** Zadanie zostało przesłane do klastra HDI i nie powiodło się.

- **Zalecenie:** Postępuj zgodnie z łączem Dzienniki przędzy w przebiegu działania Wyjście i poszukaj błędów w wyjściu HDI. W razie potrzeby skontaktuj się z zespołem HDInsight, aby uzyskać pomoc techniczną.


### <a name="error-code--2303"></a>Kod błędu: 2303

- **Komunikat**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Przyczyna:** Zadanie zostało przesłane do klastra HDI i nie powiodło się.

- **Zalecenie:** Postępuj zgodnie z łączem Dzienniki przędzy w przebiegu działania Wyjście i poszukaj błędów w wyjściu HDI. Spróbuj ponownie lub skontaktuj się z zespołem HDInsight, aby uzyskać pomoc techniczną w razie potrzeby.


### <a name="error-code--2304"></a>Kod błędu: 2304

- **Komunikat**:`MSI authentication is not supported on storages for HDI activities.`

- **Przyczyna:** Usługi połączone z magazynem używane w usłudze połączonej HDI lub aktywności HDI są skonfigurowane z uwierzytelnianiem MSI, które nie jest obsługiwane.

- **Zalecenie:** Proszę podać pełne parametry połączenia dla kont pamięci masowej używanych w usłudze połączonej HDI lub aktywności HDI.


### <a name="error-code--2305"></a>Kod błędu: 2305

- **Komunikat**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Przyczyna:** Informacje o połączeniu dla klastra HDI są nieprawidłowe, podany użytkownik nie ma uprawnień do wykonywania wymaganej akcji lub usługa HDInsight miała problemy z odpowiedzią na żądania z podajnika ADF.

- **Zalecenie:** Upewnij się, że informacje o użytkowniku są poprawne. Sprawdź również, czy interfejs użytkownika Ambari dla klastra HDI można otworzyć w przeglądarce z maszyny Wirtualnej, gdzie ir jest zainstalowany w przypadku samodzielnego hostowania IR lub mogą być otwierane z dowolnego komputera w przypadku usługi Azure IR.


### <a name="error-code--2306"></a>Kod błędu: 2306

- **Komunikat**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Przyczyna:** Json przewidziany dla akcji skryptu jest nieprawidłowy.


- **Zalecenie:** Komunikat o błędzie powinien pomóc w zidentyfikowaniu problemu. Napraw konfigurację json i spróbuj ponownie. Sprawdź, https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service aby uzyskać więcej informacji.
                


### <a name="error-code--2310"></a>Kod błędu: 2310

- **Komunikat**:`Failed to submit Spark job. Error: '%message;'`

- **Przyczyna:** ADF próbował utworzyć partię w klastrze Platformy Spark przy użyciu interfejsu API Livy (livy/batch), ale otrzymał błąd.

- **Zalecenie:** Postępuj zgodnie z komunikatem o błędzie, aby rozwiązać problem. Jeśli nie ma wystarczającej ilości informacji, aby go rozwiązać, skontaktuj się z zespołem HDI i podaj im identyfikator partii i identyfikator zadania, które można znaleźć na stronie Uruchamianie działania Dane wyjściowe w monitorowaniu usługi ADF.


### <a name="error-code--2312"></a>Kod błędu: 2312

- **Komunikat**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Przyczyna:** Zadanie nie powiodło się w klastrze platformy Spark usługi HDInsight.

- **Zalecenie:** Aby rozwiązać problem z uruchomieniem klastra platformy Spark usługi HDInsight, należy postępować zgodnie z łączami na stronie Uruchamianie działania Dane wyjściowe w udojowym dysku. Aby uzyskać dalszą pomoc, skontaktuj się z zespołem pomocy technicznej HDInsight.


### <a name="error-code--2313"></a>Kod błędu: 2313

- **Komunikat**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Przyczyna:** Partia została usunięta w klastrze platformy Spark usługi HDInsight.

- **Zalecenie:** Rozwiązywanie problemów z partiami w klastrze platformy SPARK usługi HDInsight. Aby uzyskać dalszą pomoc, skontaktuj się z pomocą techniczną usługi HDInsight. 


### <a name="error-code--2328"></a>Kod błędu: 2328

- **Komunikat**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna**:`The error message should show the details of what went wrong.`

- **Zalecenie:** Komunikat o błędzie powinien pomóc w rozwiązaniu problemu.


### <a name="error-code--2329"></a>Kod błędu: 2329

- **Komunikat**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Przyczyna:** Komunikat o błędzie powinien wyświetlać szczegóły tego, co poszło nie tak.

- **Zalecenie:** Komunikat o błędzie powinien pomóc w rozwiązaniu problemu.


### <a name="error-code--2331"></a>Kod błędu: 2331

- **Komunikat**:`The file path should not be null or empty.`

- **Przyczyna:** Podana ścieżka pliku jest pusta.

- **Zalecenie:** Podaj ścieżkę dla istniejącego pliku.


### <a name="error-code--2340"></a>Kod błędu: 2340

- **Komunikat**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Przyczyna:** Usługa połączona HDInsightOnDemand nie obsługuje wykonywania za pośrednictwem selfhosted IR.

- **Zalecenie:** Wybierz ir platformy Azure i spróbuj ponownie.


### <a name="error-code--2341"></a>Kod błędu: 2341

- **Komunikat**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Przyczyna:** Podany adres URL nie jest w prawidłowym formacie.

- **Zalecenie:** Napraw adres URL klastra i spróbuj ponownie.


### <a name="error-code--2342"></a>Kod błędu: 2342

- **Komunikat**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Przyczyna:** Podane poświadczenia są nieprawidłowe dla klastra lub wystąpił problem z konfiguracją sieci lub połączeniem lub podczerwona nazwa użytkownika ma problemy z połączeniem się z klastrem.

- **Zalecenie**:  
      1. Sprawdź, czy poświadczenia są poprawne, otwierając interfejs ambari programu WINDOWS w przeglądarce.
      2. Jeśli klaster znajduje się w sieci wirtualnej i jest używany samodzielnie hostowany podczerwony, adres URL HDI powinien być prywatnym adresem URL w sieciach wirtualnych, co oznacza, że powinien mieć "-int" po nazwie klastra. Na przykładhttps://mycluster.azurehdinsight.net/" " należyhttps://mycluster-int.azurehdinsight.net/zmienić na " ".
      2. Jeśli klaster znajduje się w sieci wirtualnej, używany jest samodzielny podczerwony podczerwony, a prywatny adres URL został użyty, a połączenie nadal nie powiodło się, maszyna wirtualna, na której zainstalowano podczerwony, miała problemy z połączeniem z interfejsem HDI. Połącz się z maszyną wirtualną, na której jest zainstalowana podczerwem, i otwórz interfejs użytkownika Ambari w przeglądarce. Użyj prywatnego adresu URL dla klastra. To połączenie powinno działać z przeglądarki. Jeśli tak nie jest, skontaktuj się z zespołem pomocy technicznej HDInsight, aby uzyskać dalszą pomoc.
      3. Jeśli samodzielnie hostowane podczerwone podczerwone nie jest używany, klaster HDI powinny być dostępne publicznie. Otwórz interfejs użytkownika Ambari w przeglądarce i upewnij się, że się otworzy. Jeśli występują jakiekolwiek problemy z klastrem lub usługami na nim, skontaktuj się z zespołem pomocy technicznej HDInsight, aby uzyskać pomoc.
      Ogólnie rzecz biorąc, adres URL klastra HDI używany w usłudze połączonej z usługą ADF musi być dostępny dla usługi ADF IR (hostowane samodzielnie lub platformy Azure) w celu przekazania połączenia testowego i przebiegu do pracy. Można to łatwo zweryfikować, otwierając ten adres URL z przeglądarki z maszyny Wirtualnej lub dowolnego komputera publicznego.
    


### <a name="error-code--2343"></a>Kod błędu: 2343

- **Komunikat**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Przyczyna:** Nazwa użytkownika lub hasło są puste.

- **Zalecenie:** Podaj poprawne poświadczenia, aby połączyć się z HDI i spróbuj ponownie.


### <a name="error-code--2345"></a>Kod błędu: 2345

- **Komunikat**:`Failed to read the content of the hive script. Error: '%message;'`

- **Przyczyna:** Plik skryptu nie istnieje lub podajnik ADF nie może połączyć się z lokalizacją skryptu.

- **Zalecenie:** Sprawdź, czy skrypt istnieje, a skojarzona usługa połączona ma odpowiednie poświadczenia dla połączenia.


### <a name="error-code--2346"></a>Kod błędu: 2346

- **Komunikat**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Przyczyna:** ADF próbował ustanowić połączenie ODBC z klastrem HDI i nie powiodło się z błędem.

- **Zalecenie:** Komunikat o błędzie i kod błędu powinny pomóc w rozwiązywaniu problemów z błędami połączenia ODBC. W przypadku, gdy nie są one wystarczające, aby rozwiązać ten problem, skontaktuj się z zespołem usługi Azure HDInsight, aby uzyskać pomoc techniczną.


### <a name="error-code--2347"></a>Kod błędu: 2347

- **Komunikat**:`Hive execution through ODBC failed with error message '%message;'.`

- **Przyczyna:** ADF przesłał skrypt gałęzi do wykonania do klastra HDI za pośrednictwem połączenia ODBC, a skrypt nie powiódł się w hdi.

- **Zalecenie:** Wykonanie skryptu gałęzi nie powiodło się w klastrze HDI, a komunikat o błędzie i kod błędu powinny pomóc w rozwiązywaniu problemów. W przypadku, gdy nie są one wystarczające, aby rozwiązać ten problem, skontaktuj się z zespołem usługi Azure HDInsight, aby uzyskać pomoc techniczną.


### <a name="error-code--2348"></a>Kod błędu: 2348

- **Komunikat**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Przyczyna:** Właściwości usługi połączonej magazynu nie są poprawnie ustawione.

- **Zalecenie:** Tylko pełne parametry połączenia są obsługiwane w głównej usługi połączonej pamięci masowej dla działań HDI. Upewnij się, że nie używasz msi auth lub aplikacji.


### <a name="error-code--2350"></a>Kod błędu: 2350

- **Komunikat**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Przyczyna:** Poświadczenia podane w celu połączenia się z magazynem, w którym powinny znajdować się pliki, są niepoprawne lub pliki tam nie istnieją.

- **Zalecenie:** Ten błąd występuje, gdy ADF wykonuje kroki przygotowania do działań HDI. Próbuje skopiować pliki do głównego magazynu przed przesłaniem zadania do HDI. Upewnij się, że pliki istnieją w podanej lokalizacji, połączenie magazynu jest poprawne. Działania usługi ADF HDI nie obsługują uwierzytelniania MSI na kontach magazynu związanych z działaniami HDI, więc upewnij się, że te połączone usługi mają pełne klucze lub korzystają z usługi Azure Key Vault.


### <a name="error-code--2351"></a>Kod błędu: 2351

- **Komunikat**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Przyczyna:** Plik nie istnieje w określonej ścieżce.

- **Zalecenie:** Sprawdź, czy plik rzeczywiście istnieje, a połączona usługa z informacjami o połączeniu wskazującymi ten plik ma poprawne poświadczenia.


### <a name="error-code--2352"></a>Kod błędu: 2352

- **Komunikat**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Przyczyna:** Właściwości usługi połączonej magazynu plików nie są poprawnie ustawione.

- **Zalecenie:** Upewnij się, że właściwości połączonej usługi przechowywania plików są poprawnie skonfigurowane.


### <a name="error-code--2353"></a>Kod błędu: 2353

- **Komunikat**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Przyczyna:** Właściwości połączonej usługi magazynu skryptów nie są poprawnie ustawione.

- **Zalecenie:** Upewnij się, że właściwości połączonej usługi magazynu skryptów są poprawnie skonfigurowane.


### <a name="error-code--2354"></a>Kod błędu: 2354

- **Komunikat**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Przyczyna:** Typ połączonej usługi magazynu nie jest obsługiwany przez działanie.

- **Zalecenie:** Upewnij się, że wybrana usługa połączona ma jeden z obsługiwanych typów dla działania. Działania HDI obsługują usługi powiązane z usługami AzureBlobStorage i AzureBlobFSStorage.


### <a name="error-code--2355"></a>Kod błędu: 2355

- **Komunikat**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Przyczyna:** Podane dla poleceniaŚrodowienia jest niepoprawna.

- **Zalecenie**:  
      Upewnij się, że podana \"wartość jest podobna do: commandEnvironment\": [ \"variableName=variableValue ] A każda zmienna\" pojawia się na liście tylko raz.
    


### <a name="error-code--2356"></a>Kod błędu: 2356

- **Komunikat**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Przyczyna**: Zmienna została podana dwukrotnie w komendzieŚliwowanie .

- **Zalecenie**:  
      Upewnij się, że podana \"wartość jest podobna do: commandEnvironment\": [ \"variableName=variableValue ] A każda zmienna\" pojawia się na liście tylko raz.
    


### <a name="error-code--2357"></a>Kod błędu: 2357

- **Komunikat**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Przyczyna:** Podane poświadczenia są niepoprawne.

- **Zalecenie:** Sprawdź informacje o połączeniu w połączonej usłudze ADLS Gen 1 i upewnij się, że połączenie testowe zakończy się pomyślnie.


### <a name="error-code--2358"></a>Kod błędu: 2358

- **Komunikat**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Przyczyna:** Podana wartość wymaganej właściwości "TimeToLive" ma nieprawidłowy format. 

- **Zalecenie:** Zaktualizuj wartość, aby znaleźć się w sugerowanym zakresie i spróbuj ponownie.


### <a name="error-code--2359"></a>Kod błędu: 2359

- **Komunikat**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Przyczyna:** Podana wartość właściwości "role" jest nieprawidłowa.

- **Zalecenie:** Zaktualizuj wartość, aby była jedną z sugestii i spróbuj ponownie.


### <a name="error-code--2360"></a>Kod błędu: 2360

- **Komunikat**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Przyczyna:** Podany ciąg połączenia dla usługi HCatalogLinkedService jest nieprawidłowy.

- **Zalecenie:** Zaktualizuj wartość do poprawnego ciągu połączenia SQL platformy Azure i spróbuj ponownie.


### <a name="error-code--2361"></a>Kod błędu: 2361

- **Komunikat**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Przyczyna:** Tworzenie klastra nie powiodło się, a podajnik ADF nie otrzymał błędu z usługi HDInsight.

- **Zalecenie:** Otwórz witrynę Azure portal i spróbuj znaleźć zasób HDI z podana nazwą i sprawdź stan inicjowania obsługi administracyjnej. Skontaktuj się z zespołem pomocy technicznej HDInsight, aby uzyskać dalszą pomoc.


### <a name="error-code--2362"></a>Kod błędu: 2362

- **Komunikat**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Przyczyna:** Podany dodatkowy magazyn nie był magazynem obiektów Blob platformy Azure.

- **Zalecenie:** Podaj konto magazynu obiektów Blob platformy Azure jako dodatkowy magazyn dla usługi połączonej usługi HDInsight na żądanie.



## <a name="web-activity"></a>Działanie internetowe

### <a name="error-code--2128"></a>Kod błędu: 2128

- **Komunikat**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Przyczyna:** Łączność sieciowa, awaria systemu DNS, sprawdzanie poprawności certyfikatu serwera lub limit czasu.

- **Zalecenie:** Sprawdź, czy punkt końcowy, który próbujesz trafić, odpowiada na żądania. Możesz użyć narzędzi, takich jak Fiddler / Listonosz.


### <a name="error-code--2108"></a>Kod błędu: 2108

- **Komunikat**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Przyczyna:** Żądanie nie powiodło się z powodu podstawowego problemu, takiego jak łączność sieciowa, awaria systemu DNS, sprawdzanie poprawności certyfikatu serwera lub limit czasu.

- **Zalecenie:** Użyj Fiddler / Listonosz do sprawdzania poprawności żądania.
<br>


#### <a name="more-details"></a>Więcej szczegółów
Aby utworzyć sesję HTTP monitorowanej aplikacji sieci web za pomocą fiddlera:

1. Pobierz, zainstaluj i otwórz [Fiddler](https://www.telerik.com/download/fiddler).

1. Jeśli aplikacja internetowa korzysta z protokołu HTTPS, przejdź do **pozycji Opcje programu Https narzędzia** > **Fiddler Options** > **HTTPS**Fiddler . Wybierz **opcję Przechwyć connecty HTTPS** i **odszyfruj ruch HTTPS**.

   ![Opcje fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Jeśli aplikacja używa certyfikatów TLS/SSL, dodaj certyfikat Fiddler do urządzenia. Przejdź do **narzędzia** > **Opcje fiddler Akcje** > **HTTPS** > **Eksportowanie** > **certyfikatu głównego na pulpicie**.

1. Wyłącz przechwytywanie, przechodząc do ruchu**przechwytywania** **plików** > . Lub naciśnij **klawisz F12**.

1. Wyczyść pamięć podręczną przeglądarki, aby wszystkie elementy w pamięci podręcznej zostały usunięte i muszą zostać pobrane ponownie.

1. Utwórz żądanie:

   1. Wybierz kartę **Kompozytor.**

   1. Ustaw metodę HTTP i adres URL.
   
   1. W razie potrzeby dodaj nagłówki i treść żądania.

   1. Wybierz pozycję **Wykonaj**.

1. Włącz ponowne przechwytywanie ruchu i dokończ problematyczną transakcję na swojej stronie.

1. Przejdź do **pliku** > **Zapisz** > **wszystkie sesje**.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do gry Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej pomocy dotyczącej rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Usługi data factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji fabryki danych](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum przepełnienia stosu dla fabryki danych](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informacje o fabryce danych](https://twitter.com/hashtag/DataFactory)


            
