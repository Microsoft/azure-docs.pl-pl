---
title: Zapobiegaj przejęciu domeny podrzędnej przy użyciu rekordów aliasów Azure DNS i weryfikacji domeny niestandardowej Azure App Service
description: Dowiedz się, jak uniknąć typowego zagrożenia o wysokim poziomie ważności przejęcia poddomeny
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890828"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Zapobiegaj zawieszonego wpisów DNS i unikaj przejęcia domen podrzędnych

W tym artykule opisano typowe zagrożenie bezpieczeństwa związane z przejmowaniem domeny oraz czynności, które można podjąć w celu rozwiązania tego problemu.


## <a name="what-is-subdomain-takeover"></a>Co to jest przejęcie poddomeny?

Przejęcia poddomen są typowymi zagrożeniami o wysokim znaczeniu dla organizacji, które regularnie tworzą i usuwają wiele zasobów. Przejęcie poddomeny może wystąpić, gdy istnieje rekord DNS wskazujący na niezainicjowany zasób platformy Azure. Takie rekordy DNS są również znane jako wpisy "zawieszonego DNS". Rekordy CNAME są szczególnie zagrożone tym zagrożeniem.

Typowy scenariusz przejęcia domeny podrzędnej:

1. Zostanie utworzona witryna sieci Web. 

    W tym przykładzie `app-contogreat-dev-001.azurewebsites.net` .

1. Wpis CNAME zostanie dodany do serwera DNS wskazującego na witrynę sieci Web. 

    W tym przykładzie utworzono następującą przyjazną nazwę: `greatapp.contoso.com` .

1. Po kilku miesiącach lokacja nie będzie już konieczna, więc zostanie usunięta **bez** usuwania odpowiadającego wpisu DNS. 

    Wpis DNS CNAME jest teraz "zawieszonego".

1. Niemal natychmiast po usunięciu lokacji aktor zagrożeń odnajduje brakującą lokację i tworzy własną witrynę sieci Web pod adresem `app-contogreat-dev-001.azurewebsites.net` .

    Teraz ruch przeznaczony dla programu `greatapp.contoso.com` prowadzi do witryny platformy Azure aktora zagrożeń, a aktora zagrożeń kontroluje zawartość, która jest wyświetlana. 

    Usługa DNS zawieszonego była wykorzystywana, a poddomena contoso "GreatApp" jest ofiarą przejęcia poddomeny. 

![Przejęcie poddomeny z rozinicjowanej witryny sieci Web](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Ryzyko przejęcia domeny podrzędnej

Gdy rekord DNS wskazuje na zasób, który nie jest dostępny, rekord ten powinien zostać usunięty ze strefy DNS. Jeśli nie została usunięta, jest to rekord "zawieszonego DNS" i tworzy możliwość przejęcia poddomeny.

Zawieszonego wpisy DNS umożliwiają uczestnikom zagrożeń przejęcie kontroli nad skojarzoną nazwą DNS w celu hostowania złośliwej witryny sieci Web lub usługi. Złośliwe strony i usługi w poddomenie organizacji mogą skutkować:

- **Utrata kontroli nad zawartością poddomeny** — nieprzerwana prasa dotycząca niezdolności do zabezpieczania swojej zawartości przez organizację oraz uszkodzenia marki i utraty zaufania.

- **Zbieranie plików cookie z niepodejrzanych osób odwiedzających** — często aplikacje sieci Web mogą uwidaczniać pliki cookie sesji w poddomenach (*. contoso.com), w związku z czym każda poddomena może uzyskiwać do nich dostęp. Aktory zagrożeń mogą korzystać z przejęcia poddomeny w celu utworzenia autentycznej strony, nakłonienia niepodejrzanych użytkowników do ich odwiedzenia i zebrania plików cookie (nawet bezpiecznych plików cookie). Powszechna koncepcja polega na tym, że za pomocą certyfikatów SSL chroni lokację oraz pliki cookie użytkowników z przejęcia. Jednak aktor zagrożeń może użyć poddomeny przejętej, aby zastosować do i odebrać prawidłowy certyfikat SSL. Prawidłowe certyfikaty SSL zapewniają im dostęp do zabezpieczonych plików cookie i mogą bardziej zwiększyć postrzeganą wiarygodność złośliwej witryny.

- **Kampanie wyłudzające informacje** — w przypadku kampanii wyłudzających informacje mogą być używane jako autentyczne. Dotyczy to złośliwych witryn, a także dla rekordów MX, które umożliwiają aktorowi zagrożeń odbieranie wiadomości e-mail skierowanych do uprawnionej poddomeny o znanej, bezpiecznej marki.

- **Dalsze zagrożenia** — złośliwe witryny mogą służyć do eskalacji innych klasycznych ataków, takich jak XSS, CSRF, obejście CORS i nie tylko.



## <a name="preventing-dangling-dns-entries"></a>Zapobieganie zawieszonego wpisów DNS

Upewnienie się, że Twoja organizacja ma zaimplementowane procesy, aby zapobiec zawieszonego wpisów DNS, a wynikiem przejęcia poddomeny jest kluczowy element w programie zabezpieczeń.

Dostępne obecnie miary zapobiegawcze są wymienione poniżej.


### <a name="use-azure-dns-alias-records"></a>Użyj Azure DNS rekordów aliasów

[Rekordy aliasów](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) Azure DNS mogą uniemożliwiać odwołania zawieszonego przez Sprzęganie cyklu życia rekordu DNS z zasobem platformy Azure. Rozważmy na przykład rekord DNS, który jest kwalifikowany jako rekord aliasu, aby wskazywał na publiczny adres IP lub profil Traffic Manager. Jeśli usuniesz te zasoby bazowe, rekord aliasu DNS będzie pustym zestawem rekordów. Nie odwołuje się już do usuniętego zasobu. Należy pamiętać, że istnieją ograniczenia dotyczące możliwości ochrony przy użyciu rekordów aliasów. Dzisiaj lista jest ograniczona do:

- Azure Front Door
- Profile usługi Traffic Manager
- Punkty końcowe usługi Azure Content Delivery Network (CDN)
- Publiczne adresy IP

Pomimo tego, że oferty usługi są ograniczone, zalecamy korzystanie z rekordów aliasów w celu obrony przed przejęciem poddomeny, jeśli to możliwe.

[Dowiedz się więcej](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) o możliwościach rekordów aliasów Azure DNS.



### <a name="use-azure-app-services-custom-domain-verification"></a>Użyj niestandardowej weryfikacji domeny Azure App Service

Podczas tworzenia wpisów DNS dla Azure App Service Utwórz asuid. poddomeny Rekord TXT z IDENTYFIKATORem weryfikacji domeny. Gdy taki rekord TXT istnieje, żadna inna subskrypcja platformy Azure nie może zweryfikować domeny niestandardowej, która jest w tej chwili przełączona. 

Te rekordy nie uniemożliwiają innej osobie tworzenia Azure App Service o tej samej nazwie, która znajduje się we wpisie rekordu CNAME. Bez możliwości udowodnienia własności nazwy domeny, aktorzy zagrożeń nie mogą odbierać ruchu ani kontrolować zawartości.

[Dowiedz się więcej](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) o sposobie mapowania istniejącej NIESTANDARDOWEJ nazwy DNS na Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Kompiluj i automatyzuj procesy w celu ograniczenia zagrożenia

Często deweloperzy i zespoły operacji mogą uruchamiać procesy oczyszczania, aby uniknąć zawieszonego zagrożeń DNS. Poniższe praktyki ułatwią zagwarantowanie, że organizacja nie ponosi tego zagrożenia. 

- **Utwórz procedury zapobiegania:**

    - Poinformuj deweloperów aplikacji, aby przekierować adresy przy każdym usunięciu zasobów.

    - W przypadku likwidowania usługi należy umieścić na liście wymaganych sprawdzeń pozycję "Usuń wpis DNS".

    - Umieść [blokady usuwania](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) dla wszystkich zasobów, które mają niestandardowy wpis DNS. Blokada usuwania służy jako wskaźnik, że mapowanie musi zostać usunięte przed anulowaniem aprowizacji zasobu. Takie działania mogą być wykonywane tylko w połączeniu z wewnętrznymi programami edukacyjnymi.

- **Utwórz procedury odnajdywania:**

    - Regularnie Przeglądaj rekordy DNS, aby upewnić się, że domeny podrzędne są zamapowane na zasoby platformy Azure, które:

        - Istnieje — kwerenda stref DNS dla zasobów wskazujących poddomeny platformy Azure, takich jak *. azurewebsites.net lub *. cloudapp.azure.com (zobacz [tę listę odwołań](azure-domains.md)).
        - Jesteś posiadaczem potwierdzenia, że masz wszystkie zasoby, które są przeznaczone dla poddomen DNS.

    - Obsługa katalogu usług w pełni kwalifikowanych punktów końcowych nazw domen (FQDN) platformy Azure oraz właścicieli aplikacji. Aby skompilować katalog usług, uruchom następujący skrypt zapytania grafu zasobów platformy Azure. Ten skrypt zawiera informacje o punktach końcowych FQDN zasobów, do których masz dostęp, i wyprowadza je w pliku CSV. Jeśli masz dostęp do wszystkich subskrypcji dzierżawy, skrypt traktuje wszystkie te subskrypcje, jak pokazano w poniższym przykładowym skrypcie. Aby ograniczyć wyniki do określonego zestawu subskrypcji, należy edytować skrypt jak pokazano.

        >[!IMPORTANT]
        > **Uprawnienia** — uruchom zapytanie jako użytkownik, który ma dostęp do wszystkich subskrypcji platformy Azure. 
        >
        > **Ograniczenia** — usługa Azure Resource Graph ma limity ograniczania i stronicowania, które należy wziąć pod uwagę w przypadku dużego środowiska platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) o pracy z dużymi zestawami danych zasobów platformy Azure. Poniższy przykładowy skrypt używa tworzenia wsadowego subskrypcji, aby uniknąć tych ograniczeń.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Lista typów i ich `FQDNProperty` wartości określone w powyższym zapytaniu grafu zasobów:

        |Nazwa zasobu  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|Microsoft. Network/usługi frontdoor|Właściwości. cName|
        |Azure Blob Storage|Microsoft. Storage/storageaccounts|Properties. obiektu. blob|
        |Azure CDN|Microsoft. CDN/profile/punkty końcowe|Właściwości. Nazwa hosta|
        |Publiczne adresy IP|Microsoft. Network/adresów publicipaddress|Properties. dnsSettings. FQDN|
        |Azure Traffic Manager|Microsoft. Network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Wystąpienie kontenera platformy Azure|Microsoft. containerinstance/containergroups|Properties. ipAddress. FQDN|
        |Azure API Management|Microsoft. apimanagement/Service|Properties. hostnameConfigurations. hostName|
        |Azure App Service|Microsoft. Web/witryny|Właściwości. defaultHostName|
        |Azure App Service — miejsca|Microsoft. Web/Sites/miejsca|Właściwości. defaultHostName|


- **Utwórz procedury korygowania:**
    - Po znalezieniu wpisów DNS zawieszonego zespół musi zbadać, czy nastąpiło naruszenie.
    - Sprawdź, dlaczego adres nie został przekierowany, gdy zasób został zlikwidowany.
    - Usuń rekord DNS, jeśli nie jest już używany, lub wskaż go właściwym zasobom platformy Azure (FQDN) należącym do organizacji.
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usług pokrewnych i funkcji platformy Azure, których można użyć do obrony przed przejęciem domeny podrzędnej, zobacz następujące strony.

- [Azure DNS obsługuje używanie rekordów aliasów dla domen niestandardowych](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Użyj identyfikatora weryfikacji domeny podczas dodawania domen niestandardowych w Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
