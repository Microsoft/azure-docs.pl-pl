---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Pola obowiązkowe. Zobacz więcej na temat aka.ms/skyeye/meta.
title: Programowanie za pomocą interfejsów API v3: Opis Azure Media Services: informacje o regułach, które są stosowane do jednostek i interfejsów API podczas tworzenia z Media Services v3. usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążenie: MS. temat: koncepcyjne MS. Date: 10/23/2020 MS. Author: inhenkel MS. Custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Programowanie przy użyciu interfejsu API usługi Media Services w wersji 3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Programista może używać Media Services [interfejsów API REST](/rest/api/media/) lub bibliotek klienckich, które umożliwiają współpracę z interfejsem API REST w celu łatwego tworzenia, zarządzania i obsługi niestandardowych przepływów pracy w multimediach. Interfejs API [Media Services v3](https://aka.ms/ams-v3-rest-sdk) jest oparty na specyfikacji openapi (znanej wcześniej jako Swagger).

W tym artykule omówiono reguły dotyczące jednostek i interfejsów API podczas opracowywania programu z Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services

Aby uzyskać prawa dostępu do zasobów usługi Media Services i do interfejsu API usługi Media Services, należy się najpierw uwierzytelnić. Usługa Media Services obsługuje uwierzytelnianie [oparte na usłudze Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Dostępne są dwie typowe opcje uwierzytelniania:
 
* **Uwierzytelnianie jednostki usługi**: Służy do uwierzytelniania usług (na przykład: aplikacji internetowych, aplikacji funkcji, aplikacji logiki, interfejsu API i mikrousług). Aplikacje, które często korzystają z tej metody uwierzytelniania, to aplikacje uruchamiające usługi demonów, usługi warstwy środkowej lub zaplanowane zadania. Na przykład w przypadku aplikacji sieci Web zawsze powinna być warstwą średnią, która łączy się z Media Services za pomocą nazwy głównej usługi.
* **Uwierzytelnianie użytkowników**: Służy do uwierzytelniania osoby korzystającej z aplikacji w celu współdziałania z zasobami usługi Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o podanie poświadczeń. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo.

Interfejs API usługi Media Services wymaga, aby użytkownik lub aplikacja wykonująca żądania interfejsu API REST mieli dostęp do zasobu konta usługi Media Services i korzystali z roli **Współautor** lub **Właściciel**. Dostęp do interfejsu API można uzyskać, korzystając z roli **Czytelnik**, ale w takiej sytuacji dostępne będą tylko operacje **Get** i **List**.Aby uzyskać więcej informacji, zobacz temat [Kontrola dostępu oparta na rolach (Azure RBAC) dla kont Media Services](rbac-overview.md).

Zamiast tworzyć jednostkę usługi, należy rozważyć użycie tożsamości zarządzanych na potrzeby zasobów platformy Azure w celu uzyskania dostępu do interfejsu API usługi Media Services za pośrednictwem usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat tożsamości zarządzanych na potrzeby zasobów platformy Azure, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Nazwa główna usługi Azure AD

Aplikacja usługi Azure AD i nazwa główna usługi powinny znajdować się w tej samej dzierżawie. Po utworzeniu aplikacji nadaj **współautorowi** aplikacji lub roli **właściciela** dostęp do konta Media Services.

Jeśli nie masz pewności, czy masz uprawnienia do tworzenia aplikacji usługi Azure AD, zobacz [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Na poniższej ilustracji liczba reprezentuje przepływ żądań w kolejności chronologicznej:

![Uwierzytelnianie aplikacji warstwy środkowej przy użyciu usługi AAD z internetowego interfejsu API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikacja warstwy środkowej żąda tokenu dostępu usługi Azure AD, który ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.
   * Media Services identyfikator URI zasobu.
   * Identyfikator URI zasobu dla Media Services REST.
   * Wartości aplikacji usługi Azure AD: identyfikator klienta i klucz tajny klienta.

   Aby uzyskać wszystkie konieczne wartości, zobacz [Access Azure Media Services API](./access-api-howto.md).

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API REST usługi Azure Media przy użyciu tokenu usługi Azure AD.
5. Warstwa środkowa pobiera dane z Media Services.

### <a name="samples"></a>Samples

Zapoznaj się z poniższymi przykładami, które pokazują, jak nawiązać połączenie z jednostką usługi Azure AD:

* [Połącz z usługą REST](media-rest-apis-with-postman.md)  
* [Nawiązywanie połączeń przy użyciu języka Java](configure-connect-java-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy .NET](configure-connect-dotnet-howto.md)
* [Nawiązywanie połączeń przy użyciu platformy Node.js](configure-connect-nodejs-howto.md)
* [Nawiązywanie połączeń przy użyciu języka Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID).

Nazwy zasobów Media Services nie mogą zawierać: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", znaku pojedynczego cudzysłowu ani znaków kontrolnych. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków.

Aby uzyskać więcej informacji na temat nazewnictwa Azure Resource Manager, zobacz [wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nazwy plików/obiektów BLOB w obrębie elementu zawartości

Nazwy plików/obiektów BLOB w obrębie elementu zawartości muszą spełniać zarówno [wymagania dotyczące nazw obiektów BLOB](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) , jak i [wymagania dotyczące nazw NTFS](/windows/win32/fileio/naming-a-file). Przyczyną tych wymagań jest możliwość skopiowania plików z magazynu obiektów BLOB do lokalnego dysku NTFS w celu przetworzenia.

## <a name="long-running-operations"></a>Długotrwałe operacje

Operacje oznaczone za pomocą `x-ms-long-running-operation` w Azure Media Services [pliku struktury Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) są długotrwałymi operacjami. 

Aby uzyskać szczegółowe informacje o sposobie śledzenia asynchronicznych operacji platformy Azure, zobacz [asynchroniczne operacje](../../azure-resource-manager/management/async-operations.md).

Media Services ma następujące długotrwałe operacje:

* [Utwórz zdarzenia na żywo](/rest/api/media/liveevents/create)
* [Aktualizowanie wydarzeń na żywo](/rest/api/media/liveevents/update)
* [Usuń wydarzenie na żywo](/rest/api/media/liveevents/delete)
* [Uruchom wydarzenie na żywo](/rest/api/media/liveevents/start)
* [Zatrzymaj LiveEvent](/rest/api/media/liveevents/stop)

  Użyj `removeOutputsOnStop` parametru, aby usunąć wszystkie skojarzone wyjście na żywo podczas zatrzymywania zdarzenia.  
* [Resetuj LiveEvent](/rest/api/media/liveevents/reset)
* [Utwórz LiveOutput](/rest/api/media/liveevents/create)
* [Usuń LiveOutput](/rest/api/media/liveevents/delete)
* [Utwórz StreamingEndpoint](/rest/api/media/streamingendpoints/create)
* [Aktualizacja StreamingEndpoint](/rest/api/media/streamingendpoints/update)
* [Usuń StreamingEndpoint](/rest/api/media/streamingendpoints/delete)
* [Uruchom StreamingEndpoint](/rest/api/media/streamingendpoints/start)
* [Zatrzymaj StreamingEndpoint](/rest/api/media/streamingendpoints/stop)
* [Skalowanie StreamingEndpoint](/rest/api/media/streamingendpoints/scale)

Po pomyślnym wysłaniu długiej operacji otrzymujesz element "201 created" i musi on być sondowany o uzupełnianie operacji przy użyciu zwróconego identyfikatora operacji.

W artykule [śledzenie asynchronicznych operacji na platformie Azure](../../azure-resource-manager/management/async-operations.md) wyjaśniono, jak śledzić stan asynchronicznych operacji platformy Azure za pomocą wartości zwracanych w odpowiedzi.

Tylko jedna długotrwała operacja jest obsługiwana dla danego zdarzenia na żywo lub dowolnego skojarzonego z nim wyjścia na żywo. Po uruchomieniu należy wykonać długotrwałą operację przed rozpoczęciem kolejnej długotrwałej operacji na tym samym LiveEvent lub wszystkich skojarzonych danych wyjściowych na żywo. W przypadku wydarzeń na żywo z wieloma wyjściami dynamicznymi musisz oczekiwać na ukończenie długotrwałej operacji na jednym z danych wyjściowych na żywo przed wyzwoleniem długotrwałej operacji na innych danych wyjściowych na żywo.

## <a name="sdks"></a>Zestawy SDK

> [!NOTE]
> Zestawy SDK Azure Media Services V3 nie mają gwarancji, że są bezpieczne wątkowo. Podczas tworzenia aplikacji wielowątkowej należy dodać własną logikę synchronizacji wątków, aby chronić klienta lub użyć nowego obiektu AzureMediaServicesClient na wątek. Należy również zachować ostrożność w przypadku problemów z wielowątkowością wprowadzanych przez obiekty opcjonalne dostarczone przez kod klientowi (na przykład wystąpienie HttpClient w programie .NET).

|SDK|Odwołanie|
|---|---|
|[Zestaw SDK platformy .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Dokumentacja platformy .NET](/dotnet/api/overview/azure/mediaservices/management)|
|[Zestaw SDK Java](https://aka.ms/ams-v3-java-sdk)|[Dokumentacja języka Java](/java/api/overview/azure/mediaservices/management)|
|[Zestaw SDK dla języka Python](https://aka.ms/ams-v3-python-sdk)|[Dokumentacja języka Python](/python/api/overview/azure/mediaservices/management)|
|[Zestaw SDK dla platformy Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Dokumentacja środowiska Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Zestaw SDK dla języka Go](https://aka.ms/ams-v3-go-sdk) |[Dokumentacja języka Go](https://aka.ms/ams-v3-go-ref)|
|[Zestaw SDK dla języka Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Zobacz też

- [EventGrid .NET SDK zawierający zdarzenia usługi Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definicje zdarzeń Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Kodeks postępowania — często zadawane pytania](https://opensource.microsoft.com/codeofconduct/faq/) lub skontaktuj się opencode@microsoft.com z innymi pytaniami lub komentarzami.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrowanie, porządkowanie, stronicowanie jednostek Media Services

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Azure Media Services](filter-order-page-entitites-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="see-also"></a>Zobacz też

Aby uzyskać wszystkie konieczne wartości, zobacz [Access Azure Media Services API](./access-api-howto.md).

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z Media Services przy użyciu języka Java](configure-connect-java-howto.md)
* [Nawiązywanie połączenia z usługą Media Services przy użyciu platformy .NET](configure-connect-dotnet-howto.md)
* [Nawiązywanie połączenia z Media Services przy użyciu Node.js](configure-connect-nodejs-howto.md)
* [Nawiązywanie połączenia z Media Services przy użyciu języka Python](configure-connect-python-howto.md)