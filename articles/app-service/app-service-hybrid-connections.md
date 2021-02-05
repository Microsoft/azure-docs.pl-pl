---
title: Połączenia hybrydowe
description: Dowiedz się, jak tworzyć i używać połączeń hybrydowych w programie Azure App Service, aby uzyskiwać dostęp do zasobów w różnych sieciach.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/04/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 20bdeef0a45bb02fab8841c0dd8ec7755143c693
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575995"
---
# <a name="azure-app-service-hybrid-connections"></a>Połączenia hybrydowe usługi Azure App Service

Połączenia hybrydowe to usługa na platformie Azure i funkcja w Azure App Service. Jako usługa, ma ona użycie i możliwości poza tymi, które są używane w App Service. Aby dowiedzieć się więcej na temat Połączenia hybrydowe i ich użycia poza App Service, zobacz [Azure Relay połączenia hybrydowe][HCService].

W App Service Połączenia hybrydowe może służyć do uzyskiwania dostępu do zasobów aplikacji w dowolnej sieci, która może wykonywać wywołania na platformie Azure przez port 443. Połączenia hybrydowe zapewnia dostęp z aplikacji do punktu końcowego TCP i nie umożliwia nowego sposobu uzyskiwania dostępu do aplikacji. Jak w App Service każde połączenie hybrydowe jest skorelowane z pojedynczym hostem TCP i kombinacją portów. Dzięki temu aplikacje mogą uzyskiwać dostęp do zasobów w dowolnym systemie operacyjnym, pod warunkiem, że jest to punkt końcowy TCP. Funkcja Połączenia hybrydowe nie wie ani nie posługuje się z protokołem aplikacji lub dostępem do niego. Po prostu zapewnia dostęp do sieci.  

## <a name="how-it-works"></a>Jak to działa ##
Połączenia hybrydowe wymaga wdrożenia agenta przekazywania, który może dotrzeć zarówno do odpowiedniego punktu końcowego, jak i do platformy Azure. Agent przekazywania, Menedżer połączeń hybrydowych (HCM), wywołuje do Azure Relay przez port 443. W witrynie aplikacji sieci Web App Service infrastruktura łączy się również z Azure Relay w imieniu aplikacji. Za pomocą połączonych połączeń aplikacja może uzyskać dostęp do żądanego punktu końcowego. W ramach uwierzytelniania i autoryzacji połączenie używa protokołu TLS 1,2 dla zabezpieczeń i kluczy sygnatury dostępu współdzielonego (SAS).    

![Diagram przepływu wysokiego poziomu połączenia hybrydowego][1]

Gdy aplikacja wysyła żądanie DNS pasujące do skonfigurowanego punktu końcowego połączenia hybrydowego, ruch wychodzący TCP zostanie przekierowany przez połączenie hybrydowe.  

> [!NOTE]
> Oznacza to, że należy próbować zawsze używać nazwy DNS dla połączenia hybrydowego. Niektóre oprogramowanie klienckie nie wykonuje wyszukiwania DNS, jeśli punkt końcowy używa adresu IP. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service korzyści z połączeń hybrydowych ###

Istnieje szereg korzyści dla Połączenia hybrydowe możliwości, w tym:

- Aplikacje mogą bezpiecznie uzyskać dostęp do lokalnych systemów i usług.
- Ta funkcja nie wymaga punktu końcowego dostępnego z Internetu.
- Konfiguracja jest szybka i łatwa. Nie są wymagane żadne bramy
- Każde połączenie hybrydowe jest zgodne z pojedynczym hostem: kombinacją portów, przydatną dla zabezpieczeń.
- Zwykle nie wymaga to otworów zapory. Połączenia są wychodzące przez standardowe porty sieci Web.
- Ponieważ ta funkcja jest poziomem sieci, jest niezależny od do języka używanego przez aplikację i technologii używanej przez punkt końcowy.
- Może służyć do zapewnienia dostępu w wielu sieciach z pojedynczej aplikacji. 
- Jest ona obsługiwana w odniesieniu do aplikacji systemu Windows i aplikacji Linux. Nie jest obsługiwana w przypadku aplikacji kontenera systemu Windows.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Elementy, których nie można wykonać za pomocą Połączenia hybrydowe ###

Elementy, które nie są dostępne w Połączenia hybrydowe obejmują:

- Zainstaluj dysk.
- Użyj protokołu UDP.
- Uzyskaj dostęp do usług opartych na protokole TCP, które używają portów dynamicznych, takich jak tryb pasywny FTP lub rozszerzony tryb pasywny.
- Obsługa protokołu LDAP, ponieważ może to wymagać protokołu UDP.
- Active Directory obsługi, ponieważ nie można przyłączyć się do domeny App Service Worker. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Dodawanie i tworzenie Połączenia hybrydowe w aplikacji ##

Aby utworzyć połączenie hybrydowe, przejdź do [Azure Portal][portal] i wybierz aplikację. Wybierz pozycję **Sieć**  >  **Skonfiguruj punkty końcowe połączenia hybrydowego**. Tutaj możesz zobaczyć Połączenia hybrydowe, które są skonfigurowane dla Twojej aplikacji.  

![Zrzut ekranu listy połączeń hybrydowych][2]

Aby dodać nowe połączenie hybrydowe, wybierz pozycję **[+] Dodaj połączenie hybrydowe**.  Zostanie wyświetlona lista Połączenia hybrydowe, które zostały już utworzone. Aby dodać jeden lub więcej z nich do aplikacji, wybierz odpowiednie opcje, a następnie wybierz pozycję **Dodaj wybrane połączenie hybrydowe**.  

![Zrzut ekranu portalu połączenia hybrydowego][3]

Jeśli chcesz utworzyć nowe połączenie hybrydowe, wybierz pozycję **Utwórz nowe połączenie hybrydowe**. Określ: 

- Nazwa połączenia hybrydowego.
- Nazwa hosta punktu końcowego.
- Port punktu końcowego.
- Service Bus przestrzeń nazw, której chcesz użyć.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowego połączenia hybrydowego][4]

Każde połączenie hybrydowe jest powiązane z przestrzenią nazw Service Bus, a każda Service Bus przestrzeń nazw znajduje się w regionie świadczenia usługi Azure. Ważne jest, aby próbować użyć przestrzeni nazw Service Bus w tym samym regionie, w którym znajduje się aplikacja, aby uniknąć opóźnień w sieci.

Jeśli chcesz usunąć połączenie hybrydowe z aplikacji, kliknij ją prawym przyciskiem myszy, a następnie wybierz polecenie **Rozłącz**.  

Po dodaniu połączenia hybrydowego do aplikacji możesz wyświetlić szczegółowe informacje na jego temat, zaznaczając je. 

![Zrzut ekranu przedstawiający szczegóły połączeń hybrydowych][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Tworzenie połączenia hybrydowego w portalu Azure Relay ###

Oprócz środowiska portalu z poziomu aplikacji można utworzyć Połączenia hybrydowe z poziomu portalu Azure Relay. Aby połączenie hybrydowe było używane przez App Service, musi:

* Wymagaj autoryzacji klienta.
* Posiadanie elementu metadanych o nazwie Endpoint, który zawiera wartość parametru Host: port.

## <a name="hybrid-connections-and-app-service-plans"></a>Plany Połączenia hybrydowe i App Service ##

App Service Połączenia hybrydowe są dostępne tylko w jednostkach SKU w warstwach Podstawowa, standardowa, Premium i izolowanych. Istnieją ograniczenia związane z planem cenowym.  

| Plan cenowy | Liczba Połączenia hybrydowe użytecznych w planie |
|----|----|
| Podstawowa | 5 na plan |
| Standardowa | 25 na plan |
| PremiumV2 | 200 na aplikację |
| Izolowana | 200 na aplikację |

Interfejs użytkownika planu App Service pokazuje, ile Połączenia hybrydowe są używane, a także aplikacji.  

![Zrzut ekranu przedstawiający właściwości planu App Service][6]

Wybierz połączenie hybrydowe, aby wyświetlić szczegóły. Możesz zobaczyć wszystkie informacje, które zostały wyświetlone w widoku aplikacji. Możesz również sprawdzić, ile innych aplikacji w tym samym planie używa tego połączenia hybrydowego.

Istnieje limit liczby punktów końcowych połączenia hybrydowego, które mogą być używane w planie App Service. Można jednak używać poszczególnych połączeń hybrydowych w ramach dowolnej liczby aplikacji w tym planie. Na przykład pojedyncze połączenie hybrydowe używane w pięciu oddzielnych aplikacjach w planie App Service jest traktowane jako jedno połączenie hybrydowe.

### <a name="pricing"></a>Cennik ###

Oprócz tego, że jest wymagane App Service planu SKU, istnieje dodatkowy koszt korzystania z Połączenia hybrydowe. Jest naliczana opłata za każdy odbiornik używany przez połączenie hybrydowe. Odbiornik jest Menedżer połączeń hybrydowych. Jeśli masz pięć Połączenia hybrydowe obsługiwanych przez dwóch menedżerów połączeń hybrydowych, które byłyby 10 odbiorników. Aby uzyskać więcej informacji, zobacz [Cennik usługi Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Menedżer połączeń hybrydowych ##

Funkcja Połączenia hybrydowe wymaga agenta przekazywania w sieci, który hostuje punkt końcowy połączenia hybrydowego. Agent przekazywania ma nazwę Menedżer połączeń hybrydowych (HCM). Aby pobrać HCM, z aplikacji w [Azure Portal][portal]wybierz pozycję **Sieć**  >  **Skonfiguruj punkty końcowe połączenia hybrydowego**.  

To narzędzie działa w systemie Windows Server 2012 lub nowszym. HCM działa jako usługa i łączy ruch wychodzący do Azure Relay na porcie 443.  

Po zainstalowaniu HCM można uruchomić HybridConnectionManagerUi.exe, aby użyć interfejsu użytkownika dla narzędzia. Ten plik znajduje się w katalogu instalacyjnym programu Menedżer połączeń hybrydowych. W systemie Windows 10 można również wyszukać *Menedżer połączeń hybrydowych interfejs użytkownika* w polu wyszukiwania.  

![Zrzut ekranu przedstawiający Menedżer połączeń hybrydowych][7]

Po uruchomieniu interfejsu użytkownika HCM pierwszy element jest widoczny w tabeli, która zawiera listę wszystkich Połączenia hybrydowe, które są skonfigurowane za pomocą tego wystąpienia HCM. Jeśli chcesz wprowadzić zmiany, najpierw Uwierzytelnij się na platformie Azure. 

Aby dodać jeden lub więcej Połączenia hybrydowe do HCM:

1. Uruchom interfejs użytkownika HCM.
2. Wybierz opcję **skonfiguruj inne połączenie hybrydowe**.
![Zrzut ekranu przedstawiający konfigurowanie nowych Połączenia hybrydowe][8]

1. Zaloguj się przy użyciu konta platformy Azure, aby uzyskać Połączenia hybrydowe dostęp do Twoich subskrypcji. HCM nie będzie nadal korzystać z konta platformy Azure poza tym. 
1. Wybierz subskrypcję.
1. Wybierz Połączenia hybrydowe, które mają być przekazywane przez HCM.
![Zrzut ekranu przedstawiający Połączenia hybrydowe][9]

1. Wybierz pozycję **Zapisz**.

Teraz możesz zobaczyć dodaną Połączenia hybrydowe. Możesz również wybrać skonfigurowane połączenie hybrydowe, aby wyświetlić szczegóły.

![Zrzut ekranu przedstawiający szczegóły połączenia hybrydowego][10]

Aby można było obsługiwać Połączenia hybrydowe, które jest skonfigurowane z programem, HCM wymaga:

- Dostęp TCP do platformy Azure przez port 443.
- Dostęp TCP do punktu końcowego połączenia hybrydowego.
- Możliwość wyszukiwania w systemie DNS na hoście punktu końcowego i przestrzeni nazw Service Bus.

> [!NOTE]
> Azure Relay korzysta z gniazd sieci Web na potrzeby łączności. Ta funkcja jest dostępna tylko w systemie Windows Server 2012 lub nowszym. Z tego powodu HCM nie jest obsługiwany w żadnym systemie Windows Server 2012.
>

### <a name="redundancy"></a>Nadmiarowość ###

Każdy HCM może obsługiwać wiele Połączenia hybrydowe. Ponadto każde połączenie hybrydowe może być obsługiwane przez wiele HCMs. Domyślne zachowanie polega na kierowaniu ruchu sieciowego przez skonfigurowany HCMs dla danego punktu końcowego. Jeśli potrzebujesz wysokiej dostępności na Połączenia hybrydowe z sieci, uruchom wiele HCMs na oddzielnych komputerach. Algorytm dystrybucji obciążenia używany przez usługę przekazywania do dystrybucji ruchu do HCMs jest przypisaniem losowym. 

### <a name="manually-add-a-hybrid-connection"></a>Ręczne dodawanie połączenia hybrydowego ###

Aby umożliwić komuś spoza subskrypcji hostowanie wystąpienia HCM dla danego połączenia hybrydowego, należy udostępnić parametry połączenia bramy dla połączenia hybrydowego. Parametry połączenia bramy są widoczne we właściwościach połączenia hybrydowego w [Azure Portal][portal]. Aby użyć tego ciągu, wybierz opcję **wprowadź ręcznie** w HCM i wklej w parametrach połączenia bramy.

![Ręczne dodawanie połączenia hybrydowego][11]

### <a name="upgrade"></a>Uaktualnienie ###

Istnieją okresowe aktualizacje Menedżer połączeń hybrydowych w celu rozwiązania problemów lub zapewnienia ulepszeń. Po wydaniu uaktualnień zostanie wyświetlone okno podręczne w interfejsie użytkownika HCM. Zastosowanie uaktualnienia spowoduje zastosowanie zmian i ponowne uruchomienie HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programistyczne Dodawanie połączenia hybrydowego do aplikacji ##

Istnieje obsługa interfejsu wiersza polecenia platformy Azure dla Połączenia hybrydowe. Dostępne polecenia działają zarówno w aplikacji, jak i na poziomie planu App Service.  Polecenia na poziomie aplikacji są następujące:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

Polecenia planu App Service umożliwiają ustawienie klucza, który będzie używany przez połączenie hybrydowe. Dla każdego połączenia hybrydowego są ustawiane dwa klucze, podstawowe i pomocnicze. Można wybrać użycie klucza podstawowego lub pomocniczego za pomocą poniższych poleceń. Dzięki temu można przełączać klucze, aby okresowo generować ponownie klucze. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Zabezpieczanie Połączenia hybrydowe ##

Istniejące połączenie hybrydowe można dodać do innych App Service Web Apps przez dowolnego użytkownika, który ma odpowiednie uprawnienia do podstawowego przekaźnika Azure Service Bus. Oznacza to, że w przypadku konieczności uniemożliwienia innym osobom ponownego korzystania z tego samego połączenia hybrydowego (na przykład gdy zasób docelowy jest usługą, która nie ma żadnych dodatkowych środków zabezpieczeń w celu zapobiegania nieautoryzowanemu dostępowi), należy zablokować dostęp do przekaźnika Azure Service Bus.

Każda osoba mająca `Reader` dostęp do przekaźnika będzie mogła _zobaczyć_ połączenie hybrydowe podczas próby dodania go do aplikacji sieci Web w Azure Portal, ale nie będzie mogła _dodać_ go, ponieważ nie ma uprawnień do pobrania parametrów połączenia, które są używane do nawiązania połączenia z przekaźnikiem. Aby można było pomyślnie dodać połączenie hybrydowe, muszą one mieć `listKeys` uprawnienie ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). `Contributor`Rola lub jakakolwiek inna rola, która obejmuje to uprawnienie w ramach przekaźnika, umożliwi użytkownikom korzystanie z połączenia hybrydowego i dodawanie go do własnych Web Apps.

## <a name="troubleshooting"></a>Rozwiązywanie problemów ##

Stan "połączone" oznacza, że co najmniej jeden HCM jest skonfigurowany przy użyciu tego połączenia hybrydowego i jest w stanie uzyskać dostęp do platformy Azure. Jeśli stan połączenia hybrydowego nie jest **połączony**, połączenie hybrydowe nie zostanie skonfigurowane na żadnym HCM, który ma dostęp do platformy Azure.

Głównym powodem, że klienci nie mogą połączyć się z punktem końcowym, jest fakt, że punkt końcowy został określony przy użyciu adresu IP zamiast nazwy DNS. Jeśli aplikacja nie może nawiązać połączenia z żądanym punktem końcowym i został użyty adres IP, przełącz się do korzystania z nazwy DNS, która jest prawidłowa na hoście, na którym działa HCM. Sprawdź również, czy nazwa DNS jest rozpoznawana poprawnie na hoście, na którym działa HCM. Upewnij się, że nawiązano połączenie z hostem, na którym uruchomiono HCM, do punktu końcowego połączenia hybrydowego.  

W App Service narzędzie wiersza polecenia **tcpping** można wywołać z poziomu konsoli narzędzia zaawansowane (kudu). To narzędzie może powiedzieć, czy masz dostęp do punktu końcowego TCP, ale nie informuje Cię o tym, czy masz dostęp do punktu końcowego połączenia hybrydowego. Gdy korzystasz z narzędzia w konsoli programu względem punktu końcowego połączenia hybrydowego, potwierdzasz, że używa kombinacji hosta: port.  

Jeśli masz klienta wiersza polecenia dla punktu końcowego, możesz przetestować łączność z poziomu konsoli aplikacji. Na przykład można testować dostęp do punktów końcowych serwera sieci Web za pomocą zwinięcia.


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
