---
title: Używanie App Service Environment i zarządzanie nim
description: Dowiedz się, jak tworzyć, publikować i skalować aplikacje w App Service Environment. Znajdź wszystkie typowe zadania w tym artykule.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 86d0569d95df18924ed47682b75d7491c71d4483
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021031"
---
# <a name="use-an-app-service-environment"></a>Używanie środowiska usługi App Service

App Service Environment (ASE) to wdrożenie Azure App Service w podsieci w wystąpieniu usługi Azure Virtual Network klienta. Środowisko ASE składa się z:

- **Frontony**: gdzie http lub https kończy się w App Service Environment
- **Procesy robocze**: zasoby obsługujące aplikacje
- **Baza danych**: zawiera informacje, które definiują środowisko
- **Magazyn**: używany do hostowania aplikacji opublikowanych przez klienta

Środowisko ASE można wdrożyć przy użyciu zewnętrznego lub wewnętrznego wirtualnego adresu IP (VIP) na potrzeby dostępu do aplikacji. Wdrożenie z zewnętrznym adresem VIP jest zwykle nazywane *zewnętrznym* środowiskiem ASE. Wdrożenie z wewnętrznym adresem VIP nosi nazwę *ILB ASE* , ponieważ używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej na temat środowiska ILB ASE, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Tworzenie aplikacji w środowisku ASE

Aby utworzyć aplikację w środowisku ASE, należy użyć tego samego procesu jak w przypadku normalnego tworzenia aplikacji, ale z kilkoma niewielkimi różnicami. Podczas tworzenia nowego planu App Service:

- Zamiast wybierać lokalizację geograficzną, w której ma zostać wdrożona aplikacja, należy wybrać środowisko ASE jako lokalizację.
- Wszystkie plany App Service utworzone w środowisku ASE mogą znajdować się tylko w warstwie cenowej izolowanej.

Jeśli nie masz środowiska ASE, możesz go utworzyć, postępując zgodnie z instrukcjami w temacie [tworzenie App Service Environment][MakeExternalASE].

Aby utworzyć aplikację w środowisku ASE:

1. Wybierz pozycję **Utwórz zasób**  >  **Sieć Web + aplikacje mobilne**  >  **aplikacji sieci Web**.

1. wprowadź nazwę aplikacji. Jeśli w środowisku ASE został już wybrany plan App Service, nazwa domeny aplikacji odzwierciedla nazwę domeny środowiska ASE:

    ![Wybór nazwy aplikacji][1]

1. Wybierz subskrypcję.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz pozycję **Użyj istniejącej** , a następnie wybierz jedną z listy rozwijanej.

1. Wybierz system operacyjny.

1. Wybierz istniejący plan App Service w środowisku ASE lub Utwórz nowy, wykonując następujące czynności:

    a. Z menu po lewej stronie Azure Portal wybierz pozycję **Utwórz zasób > aplikacji sieci Web**.

    b. Wybierz subskrypcję.

    c. Wybierz lub Utwórz grupę zasobów.

    d. Wprowadź nazwę aplikacji sieci Web.

    e. Wybierz pozycję **Code** lub **DockerContainer**.

    f. Wybierz stos środowiska uruchomieniowego.

    przykład Wybierz pozycję **Linux** lub **Windows**. 

    h. Wybierz środowisko ASE na liście rozwijanej **region** . 

    i. Wybierz lub Utwórz nowy plan App Service. W przypadku tworzenia nowego planu App Service wybierz odpowiedni **wyizolowany** rozmiar jednostki SKU.

    ![Warstwy cenowe izolowane][2]

    > [!NOTE]
    > Aplikacje Linux i aplikacje systemu Windows nie mogą znajdować się w tym samym planie App Service, ale mogą być w tej samej App Service Environment.
    >

1. Wybierz pozycję **Przegląd + Utwórz**, upewnij się, że informacje są poprawne, a następnie wybierz pozycję **Utwórz**.

## <a name="how-scale-works"></a>Jak działa skalowanie

Każda aplikacja App Service uruchamiana w planie App Service. Środowiska App Service przechowują plany App Service, a App Service plany przechowują aplikacje. Skalowanie aplikacji pozwala skalować plan App Service i wszystkie aplikacje tego samego planu.

W przypadku skalowania planu App Service wymagana jest automatyczna infrastruktura. Istnieje opóźnienie czasu na skalowanie operacji podczas dodawania infrastruktury. Jeśli w sekwencji jest kilka operacji skalowania, pierwsze żądanie skalowania infrastruktury jest przetwarzane na, a pozostałe są umieszczane w kolejce. Po zakończeniu pierwszej operacji skalowania pozostałe żądania infrastruktury działają razem. Po dodaniu infrastruktury App Service plany są przypisywane zgodnie z potrzebami. Tworzenie nowego planu App Service jest operacją skalowania, ponieważ żąda dodatkowego sprzętu.

W App Service wielodostępnym skalowanie jest natychmiastowe, ponieważ pula zasobów jest łatwo dostępna do obsługi. W środowisku ASE nie ma takiego buforu, a zasoby są przyliczane na podstawie potrzeb.

W środowisku ASE można skalować plan App Service do 100 wystąpień. Środowisko ASE może mieć nawet 201 wszystkich wystąpień dla wszystkich planów App Service w tym środowisku ASE.

## <a name="ip-addresses"></a>Adresy IP

App Service może przydzielić dedykowany adres IP do aplikacji. Ta funkcja jest dostępna po skonfigurowaniu protokołu SSL opartego na protokole IP, zgodnie z opisem w [powiązaniu istniejącego niestandardowego certyfikatu TLS/SSL do Azure App Service][ConfigureSSL]. W środowisku ILB ASE nie można dodać więcej adresów IP, które mają być używane dla protokołu SSL opartego na protokole IP.

Za pomocą zewnętrznego środowiska ASE można skonfigurować protokół SSL oparty na protokole IP dla aplikacji w taki sam sposób, jak w App Service wielodostępnej. W środowisku ASE zawsze istnieje jeden adres zapasowy, do 30 adresów IP. Za każdym razem, gdy używasz jednej z nich, zostanie dodany inny, aby adres był zawsze dostępny. Do przydzielenia innego adresu IP wymagany jest czas opóźnienia. To opóźnienie uniemożliwia Dodawanie adresów IP w krótkim czasie.

## <a name="front-end-scaling"></a>Skalowanie frontonu

Po skalowaniu planów App Service procesy robocze są automatycznie dodawane do ich obsługi. Każde środowisko ASE jest tworzone z dwoma frontonami. Frontony są automatycznie skalowane z szybkością jednego frontonu dla każdego zestawu 15 App Service wystąpienia planu. Na przykład jeśli masz trzy App Service plany z pięcioma wystąpieniami, masz łącznie 15 wystąpień i trzy frontony. Jeśli skalujesz do całkowitej liczby 30 wystąpień, będziesz mieć cztery frontony. Ten wzorzec jest kontynuowany po skalowaniu w poziomie.

Liczba frontonów, które są przydzielone domyślnie, jest dobra do umiarkowanego obciążenia. Można obniżyć stosunek do najmniejszego, jak jeden fronton dla każdego pięciu wystąpień. Możesz również zmienić rozmiar frontonu. Domyślnie są to pojedyncze rdzeń. W Azure Portal można zmienić ich rozmiar na dwa lub cztery rdzenie.

Istnieje opłata za zmianę współczynnika lub rozmiaru frontonu. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure App Service][Pricing]. Jeśli chcesz poprawić pojemność obciążenia środowiska ASE, uzyskasz większą poprawę dzięki pierwszemu skalowaniu do frontonów dwurdzeniowych przed dopasowaniem współczynnika skalowania. Zmiana rozmiaru rdzenia frontonu spowoduje uaktualnienie środowiska ASE i należy wykonać poza zwykłymi godzinami pracy.

Zasoby frontonu są punktami końcowymi protokołu HTTP/HTTPS dla środowiska ASE. Z domyślną konfiguracją frontonu użycie pamięci na fronton jest spójne około 60 procent. Podstawowym powodem skalowania frontonu jest użycie procesora CPU, które jest przede wszystkim związane z ruchem HTTPS.

## <a name="app-access"></a>Dostęp do aplikacji

W zewnętrznym środowisku ASE sufiks domeny używany do tworzenia aplikacji jest *. &lt; asename &gt; . p.azurewebsites.NET*. Jeśli środowisko ASE ma nazwę _External-ASE_ i hostuje aplikację o nazwie _contoso_ w tym środowisku ASE, dotrzesz do niej przy użyciu następujących adresów URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Aby uzyskać informacje o sposobach tworzenia zewnętrznego środowiska ASE, zobacz [tworzenie App Service Environment][MakeExternalASE].

W środowisku ILB ASE sufiks domeny używany do tworzenia aplikacji to *. &lt; asename &gt; . appserviceenvironment.NET*. Jeśli środowisko ASE ma nazwę _ILB-ASE_ i hostuje aplikację o nazwie _contoso_ w tym środowisku ASE, dotrzesz do niej przy użyciu następujących adresów URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Informacje o sposobach tworzenia środowiska ILB ASE można znaleźć w temacie [Create and Use the ILB ASE][MakeILBASE].

Adres URL usługi SCM służy do uzyskiwania dostępu do konsoli usługi kudu lub publikowania aplikacji przy użyciu Web Deploy. Aby uzyskać informacje na temat konsoli kudu, zobacz [kudu Console for Azure App Service][Kudu]. Konsola kudu udostępnia interfejs użytkownika sieci Web do debugowania, przekazywania plików, edytowania plików i wielu innych.

### <a name="dns-configuration"></a>Konfiguracja usługi DNS 

W przypadku korzystania z zewnętrznego środowiska ASE aplikacje utworzone w środowisku ASE są rejestrowane przy użyciu Azure DNS. Nie ma żadnych dodatkowych kroków, a następnie w zewnętrznym środowisku ASE, dla którego Twoje aplikacje będą dostępne publicznie. W środowisku ILB ASE należy zarządzać własnym systemem DNS. Można to zrobić na własnym serwerze DNS lub w Azure DNS strefach prywatnych.

Aby skonfigurować system DNS na własnym serwerze DNS przy użyciu środowiska ILB ASE:

1. Utwórz strefę dla &lt; nazwy środowiska ASE &gt; . appserviceenvironment.NET
1. Utwórz rekord A w tej strefie, który wskazuje * na adres IP ILB
1. Utwórz rekord A w tej strefie, który wskazuje na ILB adres IP
1. Utwórz strefę w programie &lt; ASE Name &gt; . appserviceenvironment.NET o nazwie SCM
1. Utwórz rekord A w strefie SCM, który wskazuje * na adres IP ILB

Aby skonfigurować serwer DNS w Azure DNS strefach prywatnych:

1. Utwórz strefę prywatną Azure DNS o nazwie &lt; ASE Name &gt; . appserviceenvironment.NET
1. Utwórz rekord A w tej strefie, który wskazuje * na adres IP ILB
1. Utwórz rekord A w tej strefie, który wskazuje na ILB adres IP
1. Utwórz rekord A w tej strefie, który wskazuje *. SCM na adres IP ILB

Ustawienia DNS dla domyślnego sufiksu domeny środowiska ASE nie ograniczają aplikacji do dostępu do tych nazw. Możesz ustawić niestandardową nazwę domeny bez żadnej weryfikacji w aplikacjach w środowisku ILB ASE. Jeśli chcesz utworzyć strefę o nazwie *contoso.NET*, możesz to zrobić i wskazać adres IP ILB. Niestandardowa nazwa domeny działa w przypadku żądań aplikacji, ale nie dla witryny SCM. Witryna SCM jest dostępna tylko w witrynie *&lt; nazwa_aplikacji &gt; . SCM. &lt; asename &gt; . appserviceenvironment.NET*. 

Strefa o nazwie *. &lt; asename &gt; . appserviceenvironment.NET* jest globalnie unikatowy. Przed 2019 maja klienci mogli określić sufiks domeny ILB ASE. Jeśli chcesz użyć *. contoso.com* dla sufiksu domeny, możesz to zrobić, aby uwzględnić witrynę SCM. Istniały problemy związane z tym modelem, w tym: Zarządzanie domyślnym certyfikatem SSL, brakiem logowania jednokrotnego w witrynie SCM oraz wymaganie użycia certyfikatu wieloznacznego. Proces uaktualniania domyślnego certyfikatu programu ILB ASE został również zakłócony i spowodowało ponowne uruchomienie aplikacji. Aby rozwiązać te problemy, zachowanie ILB ASE zostało zmienione tak, aby używało sufiksu domeny na podstawie nazwy środowiska ASE i z sufiksem własności firmy Microsoft. Zmiana zachowania środowiska ILB ASE ma wpływ tylko na ILB środowisk ASE wykonane po 2019 maja. Wcześniej istniejące ILB środowisk ASE muszą nadal zarządzać domyślnym certyfikatem środowiska ASE i ich konfiguracją DNS.

## <a name="publishing"></a>Publikowanie

W środowisku ASE, podobnie jak w App Service wielodostępne, można publikować według następujących metod:

- Wdrażanie w sieci Web
- FTP
- Ciągła integracja (CI)
- Przeciąganie i upuszczanie w konsoli kudu
- Środowisko IDE, takie jak Visual Studio, zaćmienie lub IntelliJ pomysł

W przypadku zewnętrznego środowiska ASE te opcje publikowania działają w ten sam sposób. Aby uzyskać więcej informacji, zobacz [wdrażanie w Azure App Service][AppDeploy].

Za pomocą ILB ASE punkty końcowe publikowania są dostępne tylko w ILB. ILB znajduje się w prywatnym adresie IP w podsieci środowiska ASE w sieci wirtualnej. Jeśli nie masz dostępu do sieci do ILB, nie możesz publikować żadnych aplikacji w tym środowisku ASE. Zgodnie z opisem w temacie [Tworzenie i używanie środowiska ILB ASE][MakeILBASE]należy skonfigurować serwer DNS dla aplikacji w systemie. To wymaganie obejmuje punkt końcowy SCM. Jeśli punkty końcowe nie są prawidłowo zdefiniowane, nie można publikować. Środowisk IDE musi również mieć dostęp sieciowy do ILB, aby opublikować go bezpośrednio.

Bez dodatkowych zmian internetowe systemy CI, takie jak GitHub i Azure DevOps, nie współpracują z ILB ASE, ponieważ punkt końcowy publikowania nie jest dostępny dla Internetu. Możesz włączyć publikowanie w środowisku ILB ASE z poziomu usługi Azure DevOps, instalując własny Agent wydania w sieci wirtualnej, która zawiera ILB ASE. Alternatywnie można również użyć systemu CI, który używa modelu ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Zobaczysz ją w profilu publikowania aplikacji i w okienku portalu aplikacji (w temacie **Omówienie**  >  **podstawowe** i również w obszarze **Właściwości**).

## <a name="storage"></a>Storage

Środowisko ASE ma 1 TB magazynu dla wszystkich aplikacji w środowisku ASE. Plan App Service w odizolowanej cenie SKU ma limit wynoszący 250 GB. W środowisku ASE 250 GB miejsca do magazynowania jest dodawane App Service planowanie do limitu 1 TB. Możesz mieć więcej App Service planów niż tylko cztery, ale nie Dodaliśmy więcej miejsca poza limit 1 TB.

## <a name="logging"></a>Rejestrowanie

Możesz zintegrować środowisko ASE z Azure Monitor, aby wysyłać dzienniki dotyczące środowiska ASE do usługi Azure Storage, Azure Event Hubs lub Log Analytics. Te elementy są rejestrowane Dzisiaj:

| Istniał | Wiadomość |
|---------|----------|
| Środowisko ASE jest w złej kondycji | Określony środowisko ASE jest w złej kondycji ze względu na nieprawidłową konfigurację sieci wirtualnej. Środowisko ASE zostanie zawieszone w przypadku kontynuowania stanu złej kondycji. Upewnij się, że zostały wykonane następujące wytyczne: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Za mało miejsca w podsieci ASE | Określone środowisko ASE znajduje się w podsieci, która jest w prawie nieprawidłowym miejscu. Istnieją {0} adresy. Po wyczerpaniu tych adresów środowisko ASE nie będzie w stanie skalować.  |
| Środowisko ASE zbliża się do łącznego limitu wystąpień | Określone środowisko ASE zbliża się do całkowitego limitu wystąpień środowiska ASE. Obecnie zawiera {0} App Service wystąpienia planu o maksymalnej liczbie 201 wystąpień. |
| Środowisko ASE nie może nawiązać połączenia z zależnością | Nie można nawiązać połączenia z określonym środowiskiem ASE {0} .  Upewnij się, że zostały wykonane następujące wytyczne: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Środowisko ASE jest zawieszone | Określona środowisko ASE jest zawieszone. Zawieszenie środowiska ASE może być spowodowane brakiem konta lub konfiguracją nieprawidłowej sieci wirtualnej. Rozwiąż główną przyczynę i Wznów środowisko ASE, aby kontynuować obsługę ruchu. |
| Rozpoczęto Uaktualnianie środowiska ASE | Rozpoczęto Uaktualnianie platformy do określonego środowiska ASE. Oczekiwane opóźnienia operacji skalowania. |
| Ukończono Uaktualnianie środowiska ASE | Uaktualnianie platformy do określonego środowiska ASE zostało zakończone. |
| Rozpoczęto operacje skalowania | {0}Rozpoczęcie skalowanie planu App Service (). Żądany stan: {1} I {2} pracownicy.
| Ukończono operacje skalowania | Zakończono skalowanie planu App Service ( {0} ). Bieżący stan: {1} I {2} pracownicy. |
| Operacje skalowania zakończyły się niepowodzeniem | Skalowanie planu App Service ( {0} ) nie powiodło się. Bieżący stan: {1} I {2} pracownicy. |

Aby włączyć rejestrowanie w środowisku ASE:

1. W portalu przejdź do pozycji **Ustawienia diagnostyki**.
1. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.
1. Podaj nazwę dla integracji dzienników.
1. Wybierz i skonfiguruj lokalizacje docelowe dzienników.
1. Wybierz pozycję **AppServiceEnvironmentPlatformLogs**.

![Ustawienia dziennika diagnostyki środowiska ASE][4]

W przypadku integracji z usługą Log Analytics można zobaczyć dzienniki, wybierając pozycję **dzienniki** w portalu środowiska ASE i tworząc zapytanie względem **AppServiceEnvironmentPlatformLogs**. Dzienniki są emitowane tylko wtedy, gdy środowisko ASE ma zdarzenie, które zostanie wyzwolone. Jeśli środowisko ASE nie ma takiego zdarzenia, nie będzie żadnych dzienników. Aby szybko zobaczyć przykład dzienników w obszarze roboczym Log Analytics, wykonaj operację skalowania z jednym z planów App Service w środowisku ASE. Następnie można uruchomić zapytanie z **AppServiceEnvironmentPlatformLogs** , aby wyświetlić te dzienniki. 

**Tworzenie alertu**

Aby utworzyć alert dotyczący dzienników, postępuj zgodnie z instrukcjami w temacie [Tworzenie, wyświetlanie i zarządzanie alertami dzienników przy użyciu Azure monitor](../../azure-monitor/platform/alerts-log.md). W skrócie:

* Otwieranie strony alertów w portalu ASE
* Wybierz **nową regułę alertu**
* Wybierz zasób, który ma być obszarem roboczym Log Analytics
* Ustaw warunek z niestandardowym wyszukiwaniem w dzienniku, aby użyć zapytania, takiego jak "AppServiceEnvironmentPlatformLogs | gdzie ResultDescription zawiera "rozpoczęto skalowanie" lub dowolny z nich. Ustaw odpowiednio próg. 
* Dodaj lub Utwórz grupę akcji zgodnie z potrzebami. Grupa akcji służy do definiowania odpowiedzi na alert, takiej jak wysyłanie wiadomości e-mail lub wiadomości SMS
* Nazwij swój alert i Zapisz go.

## <a name="upgrade-preference"></a>Preferencja uaktualniania

Jeśli masz wiele środowisk ASE, możesz chcieć uaktualnić niektóre środowisk ASE przed innymi. W ramach obiektu **Menedżer zasobów ASE HostingEnvironment** można ustawić wartość dla **upgradePreference**. Ustawienie **upgradePreference** można skonfigurować przy użyciu szablonu, ARMClient lub https://resources.azure.com . Trzy możliwe wartości to:

- **Brak**: platforma Azure UAKTUALNI środowisko ASE bez określonej partii. Ta wartość jest domyślna.
- **Wczesne**: środowisko ASE zostanie uaktualnione w pierwszej połowie App Service uaktualnieniami.
- **Późne**: środowisko ASE zostanie uaktualnione w drugiej połowie App Service uaktualnień.

Jeśli używasz programu https://resources.azure.com , wykonaj następujące kroki, aby ustawić wartość **upgradePreferences** :

1. Przejdź do resources.azure.com i zaloguj się przy użyciu konta platformy Azure.
1. Zapoznaj się z tematem Resources for subscriptions \/ \[ name \] \/ resourceGroups nazwa \/ \[ grupy zasobów \] \/ providers \/ Microsoft. Web \/ hostingEnvironments \/ \[ ASE Name \] .
1. Na górze wybierz pozycję **Odczyt/zapis** .
1. Wybierz pozycję **Edit** (Edytuj).
1. Ustaw **upgradePreference** na jedną z trzech wartości, które chcesz.
1. Wybierz pozycję **poprawka**.

![zasoby ekranu platformy Azure com][5]

Funkcja **upgradePreferences** jest najbardziej zrozumiała, gdy masz wiele środowisk aseów, ponieważ "wczesne" środowisk ASE zostanie uaktualnione przed "późnym" środowisk ASE. Jeśli masz wiele środowisk aseów, należy ustawić swoje programowanie i test środowisk ASE na "wczesne", a produkcyjny środowisk ASE "późno".

## <a name="pricing"></a>Cennik

Jednostka SKU cenowej o nazwie *izolowanej* jest używana tylko z środowisk ASE. Wszystkie plany App Service, które są hostowane w środowisku ASE, znajdują się w odizolowanej jednostce SKU cenowej. Stawki odizolowane dla planów App Service mogą różnić się w zależności od regionu.

Oprócz ceny planów App Service, istnieje stała stawka dla samego ASE. Płaska stawka nie zmienia rozmiaru środowiska ASE. Płaci za infrastrukturę środowiska ASE przy domyślnej szybkości skalowania jednego dodatkowego frontonu dla każdego 15 App Service wystąpienia planu.

Jeśli domyślna szybkość skalowania jednego frontonu dla każdego 15 App Service wystąpienia planu nie jest wystarczająco szybko dostępna, można dostosować współczynnik, w którym są dodawane frontony lub rozmiar frontonu. Po dostosowaniu współczynnika lub rozmiaru płacisz za rdzenie frontonu, które nie zostaną dodane domyślnie.

Na przykład, jeśli dostosowujesz współczynnik skalowania do 10, fronton zostanie dodany dla każdego 10 wystąpień w planach App Service. Stała opłata obejmuje szybkość skalowania jednego frontonu dla każdego 15 wystąpień. Ze stosunkiem skali 10 opłata jest naliczana za trzeci fronton, który jest dodawany do 10 wystąpień planu App Service. Nie musisz płacisz za niego w przypadku osiągnięcia 15 wystąpień, ponieważ została ona dodana automatycznie.

W przypadku dostosowania rozmiaru frontonu do dwóch rdzeni, ale nie dostosowania współczynnika, płacisz za dodatkowe rdzenie. Środowisko ASE jest tworzone z dwoma frontonami, więc nawet poniżej progu skalowania automatycznego, które byłyby płatne za dwa dodatkowe rdzenie, jeśli zwiększono rozmiar do frontonów dwurdzeniowych.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Usuwanie środowiska ASE

Aby usunąć środowisko ASE:

1. Wybierz pozycję **Usuń** w górnej części okienka **App Service Environment** .

1. Wprowadź nazwę środowiska ASE, aby potwierdzić, że chcesz go usunąć. Usunięcie środowiska ASE spowoduje również usunięcie całej jego zawartości.

    ![Usuwanie środowiska ASE][3]

1. Wybierz pozycję **OK**.

## <a name="ase-cli"></a>INTERFEJS WIERSZA POLECENIA ŚRODOWISKA ASE

Istnieją funkcje wiersza polecenia do administrowania do środowiska ASE.  Polecenia AZ CLI są wymienione poniżej.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
