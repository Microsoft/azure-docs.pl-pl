---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Pola obowiązkowe. Zobacz więcej na temat aka.ms/skyeye/meta.
title: punkty końcowe przesyłania strumieniowego (Źródło): Azure Media Services opis: informacje na temat punktów końcowych przesyłania strumieniowego (pochodzenia), dynamicznego tworzenia pakietów i usługi przesyłania strumieniowego, które dostarczają zawartość bezpośrednio do aplikacji odtwarzacza klienta lub Content Delivery Network (CDN). usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążenie: MS. temat: koncepcyjne MS. Date: 02/13/2020 MS. Author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Punkty końcowe przesyłania strumieniowego (Źródło) w Azure Media Services

W Microsoft Azure Media Services [punkt końcowy przesyłania strumieniowego](/rest/api/media/streamingendpoints) reprezentuje dynamiczny (just-in-Time) pakiet i pierwotną usługę, która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienta przy użyciu jednego z popularnych protokołów multimediów przesyłania strumieniowego (HLS lub pauzy). Ponadto **punkt końcowy przesyłania strumieniowego** zapewnia dynamiczne (just-in-Time) szyfrowanie do wiodących w branży protokołów DRM. 

Podczas tworzenia konta Media Services zostanie utworzony **domyślny** punkt końcowy przesyłania strumieniowego w stanie zatrzymanym. Więcej punktów końcowych przesyłania strumieniowego można utworzyć przy użyciu konta (zobacz [przydziały i limity](limits-quotas-constraints-reference.md)).

> [!NOTE]
> Aby rozpocząć przesyłanie strumieniowe filmów wideo, należy uruchomić **punkt końcowy przesyłania strumieniowego** , z którego chcesz przesłać strumieniowo wideo.
>
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

Upewnij się również, że zapoznaj się z tematem [dynamiczne pakowanie](encode-dynamic-packaging-concept.md) . 

## <a name="naming-convention"></a>Konwencja nazewnictwa

Format nazwy hosta adresu URL przesyłania strumieniowego to: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , gdzie `servicename` = Nazwa punktu końcowego przesyłania strumieniowego lub nazwa zdarzenia na żywo.

W przypadku korzystania z domyślnego punktu końcowego przesyłania strumieniowego `servicename` jest pomijany, więc adres URL: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Ograniczenia

* Nazwa punktu końcowego przesyłania strumieniowego ma maksymalną wartość 24 znaków.
* Nazwa powinna być zgodna z tym wzorcem [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Typy

Istnieją dwa typy **punktów końcowych przesyłania strumieniowego** : **standardowa** (wersja zapoznawcza) i **Premium**. Typ jest definiowany przez liczbę jednostek skalowania ( `scaleUnits` ) przydzielonego dla punktu końcowego przesyłania strumieniowego.

Maksymalny limit jednostek przesyłania strumieniowego to zwykle 10. Skontaktuj się z nami [tutaj](https://azure.microsoft.com/support/create-ticket/) , aby zgłosić limit dla konta.

Tabela zawiera opis typów:

|Typ|Jednostki skalowania|Opis|
|--------|--------|--------|  
|**Standardowa**|0|Domyślny punkt końcowy przesyłania strumieniowego jest typem **standardowym** — można go zmienić na typ Premium przez dostosowanie `scaleUnits` .|
|**Premium**|>0|Wersja **Premium** Punkty końcowe przesyłania strumieniowego są odpowiednie dla zaawansowanych obciążeń i zapewniają dedykowaną i skalowalną przepustowość. Przechodzenie do typu **Premium** przez dostosowanie `scaleUnits` (jednostki przesyłania strumieniowego). `scaleUnits` Zapewnij dedykowaną pojemność wyjściową, którą można zakupić w przyrostach wynoszących 200 MB/s. W przypadku korzystania z typu **Premium** każda włączona jednostka zapewnia dodatkową przepustowość dla aplikacji. |

> [!NOTE]
> W przypadku klientów chcących dostarczyć zawartość do dużych odbiorców internetowych zalecamy włączenie usługi CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać informacje o umowie SLA, zobacz [Cennik i Umowa SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

Cecha|Standardowa (Standard)|Premium
---|---|---
Przepływność |Do 600 MB/s i może zapewnić znacznie wyższą skuteczną przepływność w przypadku użycia sieci CDN.|200 MB/s na jednostkę przesyłania strumieniowego (SU). W przypadku korzystania z sieci CDN można zapewnić znacznie wyższą skuteczną przepływność.
CDN|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatycznie Skaluj do dostosowanej przepływności.|Dodatkowe usługi SUs
Filtrowanie/G20 IP/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane w przypadku większości scenariuszy przesyłania strumieniowego.|Profesjonalne użycie.

<sup>1</sup> używany bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

### <a name="versions"></a>Wersje

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Rozliczenia|
|--------------|----------|-----------------|-----------------|-----------------|
|Klasyczny|1.0|0|NA|Bezpłatna|
|Standardowy punkt końcowy przesyłania strumieniowego (wersja zapoznawcza)|2.0|0|Tak|Płatna|
|Jednostki przesyłania strumieniowego w warstwie Premium|1.0|>0|Tak|Płatna|
|Jednostki przesyłania strumieniowego w warstwie Premium|2.0|>0|Tak|Płatna|

### <a name="features"></a>Funkcje

Cecha|Standardowa (Standard)|Premium
---|---|---
Przepływność |Do 600 MB/s i może zapewnić znacznie wyższą skuteczną przepływność w przypadku użycia sieci CDN.|200 MB/s na jednostkę przesyłania strumieniowego (SU). W przypadku korzystania z sieci CDN można zapewnić znacznie wyższą skuteczną przepływność.
CDN|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatycznie Skaluj do dostosowanej przepływności.|Dodatkowe jednostki przesyłania strumieniowego.
Filtrowanie/G20 IP/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane w przypadku większości scenariuszy przesyłania strumieniowego.|Profesjonalne użycie. 

<sup>1</sup> używany bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

Aby uzyskać informacje o umowie SLA, zobacz [Cennik i Umowa SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migracja między typami

Źródło | Działanie | Akcja
---|---|---
Wdrożenie klasyczne|Standardowa (Standard)|Trzeba się zadecydować
Klasyczny|Premium| Skalowanie (dodatkowe jednostki przesyłania strumieniowego)
Standard/Premium|Klasyczny|Niedostępne (Jeśli wersja punktu końcowego przesyłania strumieniowego to 1,0). Można zmienić na klasyczny z ustawieniem scaleunits na "0".
Standardowa (z usługą CDN/bez)|Premium z tymi samymi konfiguracjami|Dozwolone w stanie **uruchomienia** . (za pośrednictwem Azure Portal)
Premium (z usługą CDN/bez)|Standardowa z tymi samymi konfiguracjami|Dozwolone w stanie **uruchomienia** (za pośrednictwem Azure Portal)
Standardowa (z usługą CDN/bez)|Premium z inną konfiguracją|Dozwolone w stanie **zatrzymania** (za pośrednictwem Azure Portal). Niedozwolone w stanie uruchomienia.
Premium (z usługą CDN/bez)|Standardowa z inną konfiguracją|Dozwolone w stanie **zatrzymania** (za pośrednictwem Azure Portal). Niedozwolone w stanie uruchomienia.
Wersja 1,0 z usługą SU >= 1 z usługą CDN|Standardowa/Premium bez sieci CDN|Dozwolone w stanie **zatrzymanym** . Niedozwolone w stanie **uruchomienia** .
Wersja 1,0 z usługą SU >= 1 z usługą CDN|Standardowa z siecią CDN/bez|Dozwolone w stanie **zatrzymanym** . Niedozwolone w stanie **uruchomienia** . Usługa CDN w wersji 1,0 zostanie usunięta i zostanie utworzona nowa i uruchomiona.
Wersja 1,0 z usługą SU >= 1 z usługą CDN|Premium z usługą CDN/bez|Dozwolone w stanie **zatrzymanym** . Niedozwolone w stanie **uruchomienia** . Klasyczna sieć CDN zostanie usunięta, a nowa została utworzona i uruchomiona.







## <a name="streaming-endpoint-properties"></a>Właściwości punktu końcowego przesyłania strumieniowego

Ta sekcja zawiera szczegółowe informacje dotyczące niektórych właściwości punktu końcowego przesyłania strumieniowego. Przykłady tworzenia nowego punktu końcowego przesyłania strumieniowego i opisy wszystkich właściwości można znaleźć w temacie [punkt końcowy przesyłania strumieniowego](/rest/api/media/streamingendpoints/create).

- `accessControl`: Służy do konfigurowania następujących ustawień zabezpieczeń dla tego punktu końcowego przesyłania strumieniowego: klucze uwierzytelniania nagłówka sygnatury Akamai i adresy IP, które mogą łączyć się z tym punktem końcowym. Tę właściwość można ustawić tylko wtedy, gdy ustawiono `cdnEnabled` wartość false.

- `cdnEnabled`: Wskazuje, czy Azure CDN integracji dla tego punktu końcowego przesyłania strumieniowego jest włączona (domyślnie wyłączona). W przypadku wybrania `cdnEnabled` wartości true następujące konfiguracje zostaną wyłączone: `customHostNames` i `accessControl` .

    Nie wszystkie centra danych obsługują integrację Azure CDN. Aby sprawdzić, czy centrum danych ma dostęp do integracji Azure CDN, wykonaj następujące czynności:

  - Spróbuj ustawić `cdnEnabled` wartość na true.
  - Sprawdź zwrócony wynik dla `HTTP Error Code 412` (PreconditionFailed) z komunikatem "przesyłanie strumieniowe CdnEnabled właściwości nie można ustawić na wartość true, ponieważ funkcja sieci CDN nie jest dostępna w bieżącym regionie".

    Jeśli zostanie wyświetlony ten błąd, centrum danych nie będzie go obsługiwać. Wypróbuj inne centrum danych.

- `cdnProfile`: Gdy `cdnEnabled` ma wartość true, można również przekazać `cdnProfile` wartości. `cdnProfile` jest nazwą profilu CDN, w którym zostanie utworzony punkt końcowy usługi CDN. Możesz podać istniejący cdnProfile lub użyć nowego. Jeśli wartość jest RÓWNa NULL i `cdnEnabled` ma wartość true, zostanie użyta wartość domyślna "AzureMediaStreamingPlatformCdnProfile". Jeśli podane `cdnProfile` już istnieje, jest w nim tworzony punkt końcowy. Jeśli profil nie istnieje, zostanie automatycznie utworzony nowy profil.
- `cdnProvider`: Gdy Usługa CDN jest włączona, można również przekazać `cdnProvider` wartości. `cdnProvider` kontroluje, który dostawca będzie używany. Obecnie obsługiwane są trzy wartości: "StandardVerizon", "PremiumVerizon" i "StandardAkamai". Jeśli nie podano wartości i `cdnEnabled` ma wartość true, używana jest wartość "StandardVerizon" (to jest domyślna).
- `crossSiteAccessPolicies`: Służy do określania zasad dostępu między lokacjami dla różnych klientów. Aby uzyskać więcej informacji, zobacz [specyfikację plików zasad między domenami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) i [udostępnianie usługi w granicach domen](/previous-versions/azure/azure-services/gg185950(v=azure.100)). Ustawienia dotyczą tylko Smooth Streaming.
- `customHostNames`: Służy do konfigurowania punktu końcowego przesyłania strumieniowego w celu akceptowania ruchu kierowanego do niestandardowej nazwy hosta. Ta właściwość jest prawidłowa dla punktów końcowych przesyłania strumieniowego w warstwie Standardowa i Premium i można ją ustawić w przypadku `cdnEnabled` : false.

    Własność nazwy domeny musi zostać potwierdzona przez Media Services. Media Services weryfikuje własność nazwy domeny, wymagając `CName` rekordu zawierającego identyfikator konta Media Services jako składnika, który ma zostać dodany do domeny w użyciu. Przykładowo dla elementu "sports.contoso.com", który ma być używany jako niestandardowa nazwa hosta dla punktu końcowego przesyłania strumieniowego, rekord dla programu `<accountId>.contoso.com` musi być skonfigurowany tak, aby wskazywał jedną z nazw hostów weryfikacyjnych Media Services. Nazwa hosta weryfikacji składa się z verifydns. `\<mediaservices-dns-zone>` .

    Poniżej znajdują się oczekiwane strefy DNS, które mają być używane w zweryfikowanym rekordzie dla różnych regionów świadczenia usługi Azure.
  
  - Ameryka Północna, Europa, Singapur, Hongkong SAR, Japonia:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Chinach

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Na przykład rekord, `CName` który mapuje "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" na "verifydns.Media.Azure.NET", udowadnia, że identyfikator Media Services 945a4c4e-28ea-45cd-8ccb-a519f6b700ad ma własność domeny contoso.com, co umożliwia użycie dowolnej nazwy w obszarze contoso.com jako niestandardowej nazwy hosta dla punktu końcowego przesyłania strumieniowego w ramach tego konta. Aby znaleźć wartość identyfikatora usługi multimediów, przejdź do [Azure Portal](https://portal.azure.com/) i wybierz swoje konto usługi multimediów. **Identyfikator konta** jest wyświetlany w prawym górnym rogu strony.

    Jeśli podjęto próbę ustawienia niestandardowej nazwy hosta bez właściwej weryfikacji `CName` rekordu, odpowiedź DNS zakończy się niepowodzeniem, a następnie będzie buforowana przez jakiś czas. Po wprowadzeniu prawidłowego rekordu może upłynąć trochę czasu, dopóki buforowana odpowiedź nie zostanie ponownie sprawdzona. W zależności od dostawcy DNS dla domeny niestandardowej trwa to kilka minut, aby ponownie sprawdzić poprawność rekordu.

    Oprócz `CName` mapowania `<accountId>.<parent domain>` do programu `verifydns.<mediaservices-dns-zone>` należy utworzyć inny, `CName` który mapuje niestandardową nazwę hosta (na przykład `sports.contoso.com` ) na nazwę hosta punktu końcowego Media Services streaming (na przykład `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Punkty końcowe przesyłania strumieniowego znajdujące się w tym samym centrum danych nie mogą współużytkować tej samej nazwy hosta niestandardowego.

    Obecnie Media Services nie obsługuje protokołu TLS z domenami niestandardowymi.

- `maxCacheAge` -Zastępuje domyślny maksymalny nagłówek sterowania pamięcią podręczną HTTP ustawiony przez punkt końcowy przesyłania strumieniowego dla fragmentów nośnika i manifestów na żądanie. Wartość jest ustawiona w sekundach.
- `resourceState` -

    - Zatrzymano: początkowy stan punktu końcowego przesyłania strumieniowego po utworzeniu
    - Uruchamianie: przechodzi do stanu uruchomienia
    - Uruchamianie: umożliwia przesyłanie strumieniowe zawartości do klientów
    - Skalowanie: trwa zwiększanie lub zmniejszanie jednostek skalowania
    - Zatrzymywanie: przechodzi do stanu zatrzymania
    - Usuwanie: jest usuwane

- `scaleUnits`: Zapewniają dedykowaną pojemność wyjściową, którą można zakupić w przyrostach wynoszących 200 MB/s. Jeśli musisz przejść do typu **Premium** , Dostosuj `scaleUnits` .

## <a name="why-use-multiple-streaming-endpoints"></a>Dlaczego warto używać wielu punktów końcowych przesyłania strumieniowego?

Pojedynczy punkt końcowy przesyłania strumieniowego może przesyłać strumieniowo zarówno wideo na żywo, jak i na żądanie, a większość klientów używa tylko jednego punktu końcowego przesyłania strumieniowego. W tej sekcji przedstawiono kilka przykładów użycia wielu punktów końcowych przesyłania strumieniowego.

* Każda jednostka zastrzeżona zezwala na 200 MB/s przepustowości. Jeśli potrzebujesz więcej niż 2 000 MB/s (2 GB/s) przepustowości, możesz użyć drugiego punktu końcowego przesyłania strumieniowego i zrównoważenia obciążenia, aby zapewnić dodatkową przepustowość.

    Sieć CDN jest jednak najlepszym sposobem osiągnięcia skalowania w poziomie na potrzeby przesyłania strumieniowego zawartości, ale jeśli dostarczasz tyle zawartości, którą sieć CDN pobiera ponad 2 GB/s, możesz dodać dodatkowe punkty końcowe przesyłania strumieniowego. W takim przypadku konieczne będzie przekazanie adresów URL zawartości, które są zrównoważone w dwóch punktach końcowych przesyłania strumieniowego. Takie podejście umożliwia lepsze buforowanie niż próba wysłania żądań do każdego źródła losowo (na przykład za pośrednictwem usługi Traffic Manager). 
    
    > [!TIP]
    > Zwykle Jeśli sieć CDN trwa ściąganie ponad 2 GB/s, coś może być błędnie skonfigurowane (na przykład bez osłony pochodzenia).
    
* Równoważenie obciążenia różnymi dostawcami sieci CDN. Na przykład można skonfigurować domyślny punkt końcowy przesyłania strumieniowego do korzystania z usługi Verizon CDN i utworzyć drugi, aby użyć Akamai. Następnie Dodaj Równoważenie obciążenia między tymi dwoma, aby osiągnąć równoważenie z różnymi usługami CDN. 

    Jednak klienci często korzystają z równoważenia obciążenia dla wielu dostawców sieci CDN przy użyciu jednego źródła.
* Przesyłaj strumieniowo zawartość mieszaną: żywo i wideo na żądanie. 

    Wzorce dostępu do zawartości na żywo i na żądanie są bardzo różne. Zawartość na żywo pozwala uzyskać wiele popytu na całą zawartość jednocześnie. Zawartość wideo na żądanie (zawartość archiwum long tail for Instance) ma niskie użycie w tej samej zawartości. W ten sposób buforowanie działa bardzo dobrze w przypadku zawartości na żywo, ale nie na długiej zawartości.

    Rozważmy scenariusz, w którym klienci głównie oglądają zawartość na żywo, ale mogą jedynie czasami oglądać zawartość na żądanie i są obsługiwane z tego samego punktu końcowego przesyłania strumieniowego. Niskie użycie zawartości na żądanie zajmie miejsce w pamięci podręcznej, które będzie lepiej zapisane dla zawartości na żywo. W tym scenariuszu zalecamy obsługę zawartości na żywo z jednego punktu końcowego przesyłania strumieniowego i długiej zawartości z innego punktu końcowego przesyłania strumieniowego. Poprawi to wydajność zawartości zdarzenia na żywo.
    
## <a name="scaling-streaming-with-cdn"></a>Skalowanie przesyłania strumieniowego przy użyciu sieci CDN

Zobacz następujące artykuły:

- [Omówienie usługi CDN](../../cdn/cdn-overview.md)
- [Skalowanie przesyłania strumieniowego przy użyciu sieci CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Zadawaj pytania i otrzymuj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="see-also"></a>Zobacz też

[Dynamiczne tworzenie pakietów](encode-dynamic-packaging-concept.md)

## <a name="next-steps"></a>Następne kroki

[Zarządzanie punktami końcowymi przesyłania strumieniowego](manage-streaming-endpoints-howto.md)
