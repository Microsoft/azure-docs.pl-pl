---
title: 'ML Studio (klasyczny): kody błędów interfejsu API REST — Azure'
description: Te kody błędów mogą być zwracane przez operację na Azure Machine Learning usłudze sieci Web.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c5a457a673500c542dd6130d5cfadd73ab677f7b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518355"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasyczny) kody błędów interfejsu API REST

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Operacje w usłudze sieci Web Azure Machine Learning Studio (klasycznej) mogą zwrócić następujące kody błędów.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (kod stanu HTTP 400)
 
Podano nieprawidłowy argument.
 
Ta klasa błędów oznacza, że argument podany w miejscu jest nieprawidłowy. Może to być poświadczenie lub lokalizacja usługi Azure Storage w przypadku, gdy przejdziesz do sieci Web. Zapoznaj się z błędem "kod" w sekcji "Szczegóły", aby zdiagnozować, który konkretny argument był nieprawidłowy.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| BadParameterValue | Podana wartość parametru nie spełnia wymagań reguły parametru na parametrze |
| BadSubscriptionId | Identyfikator subskrypcji, który jest używany do oceny, nie jest obecny w zasobie |
| BadVersionCall | Przekazano nieprawidłowy parametr wersji podczas wywołania interfejsu API: {0} . Sprawdź stronę pomocy interfejsu API, aby przekazywać poprawną wersję, i spróbuj ponownie. |
| BatchJobInputsNotSpecified | Następujące wymagane dane wejściowe nie zostały określone w żądaniu: {0} . Upewnij się, że wszystkie dane wejściowe są określone, i spróbuj ponownie. |
| BatchJobInputsTooManySpecified | Żądanie określiło więcej danych wejściowych niż zdefiniowano w usłudze. Lista zaakceptowanych danych wejściowych: {0} . Upewnij się, że wszystkie dane wejściowe zostały określone prawidłowo, i spróbuj ponownie. |
| BlobNameTooLong | Ścieżka magazynu obiektów blob platformy Azure podana dla danych wyjściowych diagnostyki jest zbyt długa: {0} . Skróć ścieżkę i spróbuj ponownie. |
| BlobNotFound | Nie można uzyskać dostępu do podanego obiektu blob platformy Azure {0} .  Komunikat o błędzie platformy Azure: {1} . |
| ContainerIsEmpty | Nie podano nazwy kontenera usługi Azure Storage. Podaj prawidłową nazwę kontenera i spróbuj ponownie. |
| ContainerSegmentInvalid | Nieprawidłowa nazwa kontenera. Podaj prawidłową nazwę kontenera i spróbuj ponownie. |
| ContainerValidationFailed | Walidacja kontenera obiektów BLOB nie powiodła się z powodu błędu: {0} . |
| DataTypeNotSupported | Podano nieobsługiwany typ danych. Podaj prawidłowe typy danych i spróbuj ponownie. |
| DuplicateInputInBatchCall | Żądanie wsadowe jest nieprawidłowe. Nie można jednocześnie określić pojedynczych i wielu danych wejściowych. Usuń jeden z tych elementów z żądania i spróbuj ponownie. |
| ExpiryTimeInThePast | Czas wygaśnięcia podany w przeszłości: {0} . Podaj przyszły czas wygaśnięcia w formacie UTC i spróbuj ponownie. Aby nigdy nie wygasnąć, ustaw czas wygaśnięcia na NULL. |
| IncompleteSettings | Ustawienia diagnostyki są niekompletne. |
| InputBlobRelativeLocationInvalid | Nie podano nazwy obiektu BLOB usługi Azure Storage. Podaj prawidłową nazwę obiektu BLOB i spróbuj ponownie. |
| InvalidBlob | Nieprawidłowa specyfikacja obiektu BLOB dla obiektu BLOB: {0} . Sprawdź poprawność parametrów połączenia/ścieżki względnej lub tokenu sygnatury dostępu współdzielonego, a następnie spróbuj ponownie. |
| InvalidBlobConnectionString | Parametry połączenia określone dla jednego z danych wejściowych/wyjściowych obiektów BLOB są nieprawidłowe: {0} . Popraw to i spróbuj ponownie. |
| InvalidBlobExtension | Odwołanie do obiektu BLOB: {0} zawiera nieprawidłowe lub brak rozszerzenia pliku. Obsługiwane rozszerzenia plików dla tego typu danych wyjściowych to: " {1} ". |
| InvalidInputNames | W żądaniu określono nieprawidłowe nazwy wejściowe usługi: {0} . Zamapuj dane wejściowe na prawidłowe dane wejściowe usługi i spróbuj ponownie. |
| InvalidOutputOverrideName | Nieprawidłowa nazwa przesłonięcia danych wyjściowych: {0} . Usługa nie ma węzła wyjściowego o tej nazwie. Przekaż poprawną nazwę węzła wyjściowego, aby przesłonić (dotyczy uwzględniania wielkości liter). |
| InvalidQueryParameter | Nieprawidłowy parametr zapytania " {0} ". {1} |
| MissingInputBlobInformation | Brak informacji o obiekcie blob usługi Azure Storage. Podaj prawidłowe parametry połączenia oraz ścieżkę względną lub identyfikator URI i spróbuj ponownie. |
| MissingJobId | Nie podano identyfikatora zadania. Identyfikator zadania jest zwracany, gdy zadanie zostało przesłane po raz pierwszy. Sprawdź, czy identyfikator zadania jest poprawny, i spróbuj ponownie. |
| MissingKeys | Nie podano kluczy lub nie podano klucza podstawowego lub pomocniczego. |
| MissingModelPackage | Nie podano identyfikatora pakietu modelu lub pakietu modelu. Podaj prawidłowy identyfikator pakietu modelu lub pakiet modelu i spróbuj ponownie. |
| MissingOutputOverrideSpecification | W żądaniu brakuje specyfikacji obiektu BLOB dla zastąpienia danych wyjściowych {0} . Określ prawidłową lokalizację obiektu BLOB z żądaniem lub Usuń specyfikację wyjściową, jeśli nie podano przesłonięcia lokalizacji. |
| MissingRequestInput | Usługa sieci Web oczekuje danych wejściowych, ale nie podano danych wejściowych. Upewnij się, że na podstawie opublikowanych portów wejściowych w modelu znajdują się prawidłowe dane wejściowe, i spróbuj ponownie. |
| MissingRequiredGlobalParameters | Nie wszystkie wymagane parametry usługi sieci Web. Sprawdź, czy parametry oczekiwane dla modułów są poprawne, i spróbuj ponownie. |
| MissingRequiredOutputOverrides | Podczas wywoływania punktu końcowego usługi zaszyfrowanej obowiązkowe jest przekazywanie danych wyjściowych dla wszystkich wyników usługi. Brak zastąpień w tej chwili dla następujących danych wyjściowych: {0} |
| MissingWebServiceGroupId | Nie podano identyfikatora grupy usług sieci Web. Podaj prawidłowy identyfikator grupy usług sieci Web i spróbuj ponownie. |
| MissingWebServiceId | Nie podano identyfikatora usługi sieci Web. Podaj prawidłowy identyfikator usługi sieci Web i spróbuj ponownie. |
| MissingWebServicePackage | Nie podano pakietu usługi sieci Web. Podaj prawidłowy pakiet usługi sieci Web i spróbuj ponownie. |
| MissingWorkspaceId | Nie podano identyfikatora obszaru roboczego. Podaj prawidłowy identyfikator obszaru roboczego i spróbuj ponownie. |
| ModelConfigurationInvalid | Nieprawidłowa konfiguracja modelu w pakiecie modelu. Upewnij się, że konfiguracja modelu zawiera wyjściową definicję punktów końcowych, standardowy punkt końcowy błędu oraz punkt końcowy o wartości STD i spróbuj ponownie. |
| ModelPackageIdInvalid | Nieprawidłowy identyfikator pakietu modelu. Sprawdź, czy identyfikator pakietu modelu jest poprawny, i spróbuj ponownie. |
| RequestBodyInvalid | Nie podano treści żądania lub wystąpił błąd podczas deserializacji treści żądania. |
| RequestIsEmpty | Nie podano żądania. Podaj prawidłowe żądanie i spróbuj ponownie. |
| UnexpectedParameter | Podano nieoczekiwane parametry. Sprawdź, czy wszystkie nazwy parametrów są wpisane prawidłowo, czy są spełnione tylko oczekiwane parametry, i spróbuj ponownie. |
| UnknownError | Nieznany błąd. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nie można zmienić wymagań współbieżnych żądań dla {0} usługi sieci Web. |
| WebServiceIdInvalid | Podano nieprawidłowy identyfikator usługi sieci Web. Identyfikator usługi sieci Web powinien być prawidłowym identyfikatorem GUID. |
| WebServiceTooManyConcurrentRequestRequirement | Nie można ustawić współbieżnego wymagania żądania na więcej niż {0} . |
| WebServiceTypeInvalid | Podano nieprawidłowy typ usługi sieci Web. Sprawdź, czy prawidłowy typ usługi sieci Web jest poprawny, i spróbuj ponownie. Prawidłowe typy usługi sieci Web: {0} . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (kod stanu HTTP 400)
 
Podano nieprawidłowy argument użytkownika.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| InputMismatchError | Dane wejściowe nie pasują do schematu portu wejściowego. |
| InputParseError | Analizowanie wektora wejściowego nie powiodło się.  Sprawdź, czy wektor wejściowy ma poprawną liczbę kolumn i typów danych.  Dodatkowe szczegóły: {0} . |
| MissingRequiredGlobalParameters | Brak parametrów oczekiwanych przez usługę sieci Web. Sprawdź, czy wszystkie wymagane parametry oczekiwane przez usługę sieci Web są poprawne, i spróbuj ponownie. |
| UnexpectedParameter | Sprawdź, czy są spełnione tylko wymagane parametry oczekiwane przez usługę sieci Web, i spróbuj ponownie. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (kod stanu HTTP 400)
 
Żądanie jest nieprawidłowe w bieżącym kontekście.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| CannotStartJob | Nie można uruchomić zadania, ponieważ jest ono w {0} stanie. |
| IncompatibleModel | Model jest niezgodny z wersją żądania. Wersja żądania obsługuje tylko pojedyncze modele wyjściowe elementu DataTable. |
| MultipleInputsNotAllowed | Model nie zezwala na używanie wielu danych wejściowych. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (kod stanu HTTP 400)
 
Wykonanie modułu napotkało błąd wewnętrzny biblioteki.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (kod stanu HTTP 400)
 
Wykonanie modułu napotkało błąd.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (kod stanu HTTP 400)
 
Nieprawidłowy pakiet usługi sieci Web. Upewnij się, że podany pakiet usługi sieci Web jest poprawny, i spróbuj ponownie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| FormatError | Pakiet usługi sieci Web jest źle sformułowany. Uzyskać {0} |
| RuntimesError | Wykres pakietu usługi sieci Web jest nieprawidłowy. Uzyskać {0} |
| ValidationError | Wykres pakietu usługi sieci Web jest nieprawidłowy. Uzyskać {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nieautoryzowany (kod stanu HTTP 401)
 
Żądanie nie ma autoryzacji dostępu do zasobu.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| AdminRequestUnauthorized | Brak autoryzacji |
| ManagementRequestUnauthorized | Brak autoryzacji |
| ScoreRequestUnauthorized | Podano nieprawidłowe poświadczenia. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (kod stanu HTTP 404)
 
Nie znaleziono zasobu.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModelPackageNotFound | Nie znaleziono pakietu modelu. Sprawdź, czy identyfikator pakietu modelu jest poprawny, i spróbuj ponownie. |
| WebServiceIdNotFoundInWorkspace | Nie znaleziono usługi sieci Web w tym obszarze roboczym. Występuje niezgodność między webServiceId i identyfikator obszaru roboczego. Sprawdź, czy podana usługa sieci Web jest częścią obszaru roboczego, i spróbuj ponownie. |
| WebServiceNotFound | Nie znaleziono usługi sieci Web. Sprawdź, czy identyfikator usługi sieci Web jest poprawny, i spróbuj ponownie. |
| Zgłosić workspacenotfound | Nie znaleziono obszaru roboczego. Sprawdź, czy identyfikator obszaru roboczego jest poprawny, i spróbuj ponownie. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (kod stanu HTTP 408)
 
Nie można ukończyć operacji w dozwolonym czasie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| RequestCanceled | Żądanie zostało anulowane przez klienta. |
| ScoreRequestTimeout | Przekroczono limit czasu żądania wykonania. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kod stanu HTTP 409)
 
Zasób już istnieje.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nieprawidłowa nazwa parametru wyjściowego. Spróbuj użyć modułu edytora metadanych, aby zmienić nazwę kolumn i spróbuj ponownie. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (kod stanu HTTP 413)
 
Model przekroczył przydzielony przydział pamięci.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| OutOfMemoryLimit | Model zużywał więcej pamięci niż jest to odpowiednie dla niego. Maksymalna dozwolona ilość pamięci dla modelu wynosi {0} MB. Sprawdź, czy w modelu występują problemy. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (kod stanu HTTP 500)
 
Wykonanie napotkało błąd wewnętrzny.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Proces kontenera uległ awarii z powodu błędu systemu |
| ContainerProcessTerminatedWithUnknownError | Proces kontenera uległ awarii z nieznanym błędem |
| ContainerValidationFailed | Walidacja kontenera obiektów BLOB nie powiodła się z powodu błędu: {0} . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nie dostarczono żadnych argumentów. Sprawdź, czy poprawne argumenty są przekazane, i spróbuj ponownie. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Identyfikator portu = {0} ma nieobsługiwany typ danych: {1} . |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Generowanie struktury Swagger nie powiodło się, szczegóły: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Nieznany kod stanu zadania {0} . |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, szczegóły: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (kod stanu HTTP 500)
 
Wykonanie napotkało błąd wewnętrzny. Brak pamięci w systemie. Spróbuj ponownie.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (kod stanu HTTP 500)
 
Nieprawidłowy pakiet modelu. Sprawdź, czy podany pakiet modelu jest poprawny, i spróbuj ponownie.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (kod stanu HTTP 500)
 
Nieprawidłowy pakiet usługi sieci Web. Sprawdź, czy podany pakiet internetowy jest poprawny, i spróbuj ponownie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| ModuleError | Wykres pakietu usługi sieci Web jest nieprawidłowy. Uzyskać {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (kod stanu HTTP 503)
 
Nie można wykonać żądania, ponieważ kontenery są inicjowane.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>Serviceniedostępne (kod stanu HTTP 503)
 
Usługa jest tymczasowo niedostępna.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| NoMoreResources | Brak dostępnych zasobów dla żądania. |
| RequestThrottled | Żądanie zostało ograniczone dla {0} punktu końcowego. Maksymalna współbieżność punktu końcowego to {1} . |
| TooManyConcurrentRequests | Wysłano zbyt wiele współbieżnych żądań. |
| TooManyHostsBeingInitialized | Zbyt wiele hostów jest inicjowanych w tym samym czasie. Rozważ ograniczenie/ponowną próbę. |
| TooManyHostsBeingInitializedPerModel | Zbyt wiele hostów jest inicjowanych w tym samym czasie. Rozważ ograniczenie/ponowną próbę. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (kod stanu HTTP 504)
 
Nie można ukończyć operacji w dozwolonym czasie.
 
| Kod błędu | Komunikat użytkownika |
| ---------- |--------------|
| BackendInitializationTimeout | Nie można ukończyć inicjowania usługi sieci Web w dozwolonym czasie. |
| BackendScoreTimeout | Nie można ukończyć wykonywania żądania usługi sieci Web w dozwolonym czasie. |
