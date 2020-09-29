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
ms.date: 09/29/2020
ms.author: memildin
ms.openlocfilehash: bde4b21f9dfff62ef43afc9c9d8e5a858631d304
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447371"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Zapobiegaj zawieszonego wpisów DNS i unikaj przejęcia domen podrzędnych

W tym artykule opisano typowe zagrożenie bezpieczeństwa związane z przejmowaniem domeny oraz czynności, które można podjąć w celu rozwiązania tego problemu.


## <a name="what-is-subdomain-takeover"></a>Co to jest przejęcie poddomeny?

Przejęcia poddomen są typowymi zagrożeniami o wysokim znaczeniu dla organizacji, które regularnie tworzą i usuwają wiele zasobów. Przejęcie poddomeny może wystąpić, gdy istnieje [rekord DNS](https://docs.microsoft.com/azure/dns/dns-zones-records#dns-records) wskazujący na niezainicjowany zasób platformy Azure. Takie rekordy DNS są również znane jako wpisy "zawieszonego DNS". Rekordy CNAME są szczególnie zagrożone tym zagrożeniem. Przejęcia domen podrzędnych umożliwiają złośliwym podmiotom przekierowywanie ruchu przeznaczonego dla domeny organizacji do lokacji wykonującej złośliwe działanie.

Typowy scenariusz przejęcia domeny podrzędnej:

1. **Tworzenie**

    1. Zasób platformy Azure można zainicjować przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) `app-contogreat-dev-001.azurewebsites.net` .

    1. Do strefy DNS można przypisać rekord CNAME z poddomeną `greatapp.contoso.com` , która kieruje ruch do zasobu platformy Azure.

1. **ANULOWANIA obsługi**

    1. Zasób platformy Azure jest cofany lub usuwany, gdy nie jest już wymagany. 
    
        W tym momencie rekord CNAME `greatapp.contoso.com` *powinien* zostać usunięty ze strefy DNS. Jeśli rekord CNAME nie zostanie usunięty, jest anonsowany jako aktywna domena, ale nie kieruje ruchu do aktywnego zasobu platformy Azure. Jest to definicja rekordu DNS "zawieszonego".

    1. Poddomena zawieszonego, `greatapp.contoso.com` , jest teraz podatna na zagrożenia i można ją przełączać przez przypisanie do innego zasobu subskrypcji platformy Azure.

1. **PRZEJĘĆ**

    1. Korzystając z powszechnie dostępnych metod i narzędzi, aktor zagrożeń odnajduje poddomenę zawieszonego.  

    1. Aktor zagrożeń inicjuje zasób platformy Azure o tej samej nazwie FQDN wcześniej kontrolowanego zasobu. W tym przykładzie `app-contogreat-dev-001.azurewebsites.net` .

    1. Ruch wysyłany do domeny podrzędnej `myapp.contoso.com` jest teraz kierowany do zasobu złośliwego aktora, gdzie kontroluje zawartość.



![Przejęcie poddomeny z rozinicjowanej witryny sieci Web](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Ryzyko przejęcia domeny podrzędnej

Gdy rekord DNS wskazuje na zasób, który nie jest dostępny, rekord ten powinien zostać usunięty ze strefy DNS. Jeśli nie została usunięta, jest to rekord "zawieszonego DNS" i tworzy możliwość przejęcia poddomeny.

Zawieszonego wpisy DNS umożliwiają uczestnikom zagrożeń przejęcie kontroli nad skojarzoną nazwą DNS w celu hostowania złośliwej witryny sieci Web lub usługi. Złośliwe strony i usługi w poddomenie organizacji mogą spowodować:

- **Utrata kontroli nad zawartością poddomeny** — nieprzerwana prasa dotycząca niezdolności do zabezpieczania swojej zawartości przez organizację oraz uszkodzenia marki i utraty zaufania.

- **Zbieranie plików cookie z niepodejrzanych osób odwiedzających** — często aplikacje sieci Web mogą uwidaczniać pliki cookie sesji w poddomenach (*. contoso.com), w związku z czym każda poddomena może uzyskiwać do nich dostęp. Aktory zagrożeń mogą korzystać z przejęcia poddomeny w celu utworzenia autentycznej strony, nakłonienia niepodejrzanych użytkowników do ich odwiedzenia i zebrania plików cookie (nawet bezpiecznych plików cookie). Powszechna koncepcja polega na tym, że za pomocą certyfikatów SSL chroni lokację oraz pliki cookie użytkowników z przejęcia. Jednak aktor zagrożeń może użyć poddomeny przejętej, aby zastosować do i odebrać prawidłowy certyfikat SSL. Prawidłowe certyfikaty SSL zapewniają im dostęp do zabezpieczonych plików cookie i mogą bardziej zwiększyć postrzeganą wiarygodność złośliwej witryny.

- **Kampanie wyłudzające informacje** — w kampaniach wyłudzających informacje mogą być używane jako autentyczne. Dotyczy to złośliwych witryn i rekordów MX, które umożliwiają aktorowi zagrożeń odbieranie wiadomości e-mail skierowanych do uprawnionej poddomeny o znanej, bezpiecznej marki.

- **Dalsze zagrożenia** — złośliwe witryny mogą służyć do eskalacji innych klasycznych ataków, takich jak XSS, CSRF, obejście CORS i nie tylko.



## <a name="identify-dangling-dns-entries"></a>Identyfikowanie wpisów DNS zawieszonego

Aby zidentyfikować wpisy DNS w organizacji, które mogą być zawieszonego, [Użyj narzędzi programu](https://aka.ms/DanglingDNSDomains)PowerShell hostowanych przez usługę GitHub firmy Microsoft.

To narzędzie ułatwia klientom platformy Azure Wyświetlanie listy wszystkich domen z rekordem CNAME skojarzonym z istniejącym zasobem platformy Azure, który został utworzony w ramach ich subskrypcji lub dzierżawców.

Jeśli rekordy CNAME znajdują się w innych usługach DNS i wskaż zasoby platformy Azure, podaj rekordy CNAME w pliku wejściowym do narzędzia.

Narzędzie obsługuje zasoby platformy Azure wymienione w poniższej tabeli. Narzędzie wyodrębnia lub przyjmuje jako dane wejściowe wszystkie rekordy CNAME dzierżawcy.


| Usługa                   | Typ                                        | FQDNproperty                               | Przykład                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | Microsoft. Network/usługi frontdoor                | Właściwości. cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | Microsoft. Storage/storageaccounts           | Properties. obiektu. blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | Microsoft. CDN/profile/punkty końcowe            | Właściwości. Nazwa hosta                        | `abc.azureedge.net`             |
| Publiczne adresy IP       | Microsoft. Network/adresów publicipaddress         | Properties. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | Microsoft. Network/trafficmanagerprofiles    | Properties. dnsConfig. FQDN                  | `abc.trafficmanager.net`        |
| Wystąpienie kontenera platformy Azure  | Microsoft. containerinstance/containergroups | Properties. ipAddress. FQDN                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | Microsoft. apimanagement/Service             | Properties. hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Azure App Service         | Microsoft. Web/witryny                         | Właściwości. defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service — miejsca | Microsoft. Web/Sites/miejsca                   | Właściwości. defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Wymagania wstępne

Uruchom zapytanie jako użytkownik, który ma:

- co najmniej dostęp na poziomie czytelnika do subskrypcji platformy Azure
- dostęp do odczytu do grafu zasobów platformy Azure

Jeśli jesteś administratorem globalnym dzierżawy organizacji, Podnieś poziom swojego konta, aby mieć dostęp do całej subskrypcji organizacji przy użyciu wskazówek w temacie [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania](../../role-based-access-control/elevate-access-global-admin.md).


> [!TIP]
> Wykres zasobów platformy Azure ma ograniczenia dotyczące ograniczania i stronicowania, które należy wziąć pod uwagę w przypadku dużego środowiska platformy Azure. 
> 
> [Dowiedz się więcej o pracy z dużymi zestawami danych zasobów platformy Azure](../../governance/resource-graph/concepts/work-with-data.md).
> 
> Narzędzie używa tworzenia wsadowych subskrypcji, aby uniknąć tych ograniczeń.

### <a name="run-the-script"></a>Uruchamianie skryptu

Dowiedz się więcej na temat skryptu programu PowerShell, **Get-DanglingDnsRecords.ps1**i Pobierz go z witryny GitHub: https://aka.ms/DanglingDNSDomains .

## <a name="remediate-dangling-dns-entries"></a>Koryguj wpisy DNS zawieszonego 

Przejrzyj strefy DNS i zidentyfikuj rekordy CNAME, które zostały zawieszonego lub przejęcia. Jeśli poddomeny mają być zawieszonego lub przejęte, Usuń zagrożone poddomeny i zmniejsz ryzyko, wykonując następujące czynności:

1. Ze strefy DNS Usuń wszystkie rekordy CNAME wskazujące nazwy FQDN zasobów, które nie są już obsługiwane.

1. Aby włączyć kierowanie ruchu do zasobów w kontrolce, należy udostępnić dodatkowe zasoby z nazwami FQDN określonymi w rekordach CNAME domen zawieszonego.

1. Przejrzyj kod aplikacji w celu odwoływania się do określonych poddomen i zaktualizuj wszystkie nieprawidłowe lub nieaktualne odwołania poddomen.

1. Sprawdź, czy wystąpiło naruszenie, i podejmuj działania zgodnie z procedurami odpowiedzi na zdarzenia w organizacji. Wskazówki i najlepsze rozwiązania dotyczące badania tego problemu można znaleźć poniżej.

    Jeśli logika aplikacji jest taka, że wpisy tajne, takie jak poświadczenia OAuth, zostały wysłane do domeny podrzędnej zawieszonego, lub informacje poufne dla prywatności zostały wysłane do poddomen zawieszonego, te dane mogły zostać ujawnione stronom trzecim.

1. Dowiedz się, dlaczego rekord CNAME nie został usunięty ze strefy DNS, gdy zasób został anulowany, i wykonaj kroki, aby upewnić się, że rekordy DNS są odpowiednio aktualizowane, gdy zasoby platformy Azure zostaną wycofane w przyszłości.


## <a name="prevent-dangling-dns-entries"></a>Zapobiegaj zawieszonego wpisów DNS

Upewnienie się, że Twoja organizacja ma zaimplementowane procesy, aby zapobiec zawieszonego wpisów DNS, a wynikiem przejęcia poddomeny jest kluczowy element w programie zabezpieczeń.

Niektóre usługi platformy Azure oferują funkcje, które ułatwiają tworzenie środków zapobiegawczych i są szczegółowo opisane poniżej. Inne metody przeciwdziałania tego problemu muszą zostać ustanowione w ramach najlepszych praktyk w zakresie lub standardowych procedur operacyjnych.


### <a name="use-azure-dns-alias-records"></a>Użyj Azure DNS rekordów aliasów

[Rekordy aliasów](../../dns/dns-alias.md#scenarios) Azure DNS mogą uniemożliwiać odwołania zawieszonego przez Sprzęganie cyklu życia rekordu DNS z zasobem platformy Azure. Rozważmy na przykład rekord DNS, który jest kwalifikowany jako rekord aliasu, aby wskazywał na publiczny adres IP lub profil Traffic Manager. Jeśli usuniesz te zasoby bazowe, rekord aliasu DNS będzie pustym zestawem rekordów. Nie odwołuje się już do usuniętego zasobu. Należy pamiętać, że istnieją ograniczenia dotyczące możliwości ochrony przy użyciu rekordów aliasów. Dzisiaj lista jest ograniczona do:

- Azure Front Door
- Profile usługi Traffic Manager
- Punkty końcowe usługi Azure Content Delivery Network (CDN)
- Publiczne adresy IP

Pomimo tego, że oferty usługi są ograniczone, zalecamy korzystanie z rekordów aliasów w celu obrony przed przejęciem poddomeny, jeśli to możliwe.

[Dowiedz się więcej o możliwościach rekordów aliasów Azure DNS](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Użyj niestandardowej weryfikacji domeny Azure App Service

Podczas tworzenia wpisów DNS dla Azure App Service Utwórz asuid. poddomeny Rekord TXT z IDENTYFIKATORem weryfikacji domeny. Gdy taki rekord TXT istnieje, żadna inna subskrypcja platformy Azure nie może zweryfikować domeny niestandardowej, która jest w tej chwili przełączona. 

Te rekordy nie uniemożliwiają innej osobie tworzenia Azure App Service o tej samej nazwie, która znajduje się we wpisie rekordu CNAME. Bez możliwości udowodnienia własności nazwy domeny, aktorzy zagrożeń nie mogą odbierać ruchu ani kontrolować zawartości.

[Dowiedz się więcej o sposobie mapowania istniejącej niestandardowej nazwy DNS na Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Kompiluj i automatyzuj procesy w celu ograniczenia zagrożenia

Często deweloperzy i zespoły operacji mogą uruchamiać procesy oczyszczania, aby uniknąć zawieszonego zagrożeń DNS. Poniższe praktyki ułatwią zagwarantowanie, że organizacja nie ponosi tego zagrożenia. 

- **Utwórz procedury zapobiegania:**

    - Poinformuj deweloperów aplikacji, aby przekierować adresy przy każdym usunięciu zasobów.

    - W przypadku likwidowania usługi należy umieścić na liście wymaganych sprawdzeń pozycję "Usuń wpis DNS".

    - Umieść [blokady usuwania](../../azure-resource-manager/management/lock-resources.md) dla wszystkich zasobów, które mają niestandardowy wpis DNS. Blokada usuwania służy jako wskaźnik, że mapowanie musi zostać usunięte przed anulowaniem aprowizacji zasobu. Takie działania mogą być wykonywane tylko w połączeniu z wewnętrznymi programami edukacyjnymi.

- **Utwórz procedury odnajdywania:**

    - Regularnie Przeglądaj rekordy DNS, aby upewnić się, że domeny podrzędne są zamapowane na zasoby platformy Azure, które:

        - Istnieje — kwerenda stref DNS dla zasobów wskazujących poddomeny platformy Azure, takich jak *. azurewebsites.net lub *. cloudapp.azure.com (zobacz [listę referencyjną domen platformy Azure](azure-domains.md)).
        - Jesteś posiadaczem potwierdzenia, że masz wszystkie zasoby, które są przeznaczone dla poddomen DNS.

    - Obsługa katalogu usług w pełni kwalifikowanych punktów końcowych nazw domen (FQDN) platformy Azure oraz właścicieli aplikacji. Aby skompilować katalog usług, uruchom następujący skrypt zapytania grafu zasobów platformy Azure. Ten skrypt zawiera informacje o punktach końcowych FQDN zasobów, do których masz dostęp, i wyprowadza je w pliku CSV. Jeśli masz dostęp do wszystkich subskrypcji dzierżawy, skrypt traktuje wszystkie te subskrypcje, jak pokazano w poniższym przykładowym skrypcie. Aby ograniczyć wyniki do określonego zestawu subskrypcji, należy edytować skrypt jak pokazano.


- **Utwórz procedury korygowania:**
    - Po znalezieniu wpisów DNS zawieszonego zespół musi zbadać, czy nastąpiło naruszenie.
    - Sprawdź, dlaczego adres nie został przekierowany, gdy zasób został zlikwidowany.
    - Usuń rekord DNS, jeśli nie jest już używany, lub wskaż go właściwym zasobom platformy Azure (FQDN) należącym do organizacji.
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usług pokrewnych i funkcji platformy Azure, których można użyć do obrony przed przejęciem domeny podrzędnej, zobacz następujące strony.

- [Zapobiegaj zawieszonego rekordów DNS z Azure DNS](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Użyj identyfikatora weryfikacji domeny podczas dodawania domen niestandardowych w Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)