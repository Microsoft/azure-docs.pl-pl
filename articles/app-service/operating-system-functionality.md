---
title: Funkcjonalność systemu operacyjnego
description: Dowiedz się więcej na temat funkcji systemu operacyjnego w Azure App Service w systemie Windows. Dowiedz się, jakie typy plików, sieci i rejestru uzyskują dostęp do aplikacji.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 949e408544e25cb55622cf2a1b1d2dddb92350a6
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150154"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funkcjonalność systemu operacyjnego na Azure App Service
W tym artykule opisano typowe podstawowe funkcje systemu operacyjnego, które są dostępne dla wszystkich aplikacji systemu Windows działających na [Azure App Service](./overview.md). Ta funkcja obejmuje dostęp do plików, sieci i rejestru oraz dzienniki i zdarzenia diagnostyczne. 

> [!NOTE] 
> [Aplikacje systemu Linux](overview.md#app-service-on-linux) w App Service uruchamiane we własnych kontenerach. Żaden dostęp do systemu operacyjnego hosta nie jest dozwolony, masz dostęp do kontenera. Podobnie w przypadku [aplikacji działających w kontenerach systemu Windows](quickstart-custom-container.md?pivots=container-windows)użytkownik ma dostęp administracyjny do kontenera, ale nie ma dostępu do systemu operacyjnego hosta. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Warstwy planu App Service
App Service uruchamia aplikacje klienta w środowisku hostingu z wieloma dzierżawcami. Aplikacje wdrożone w warstwach **bezpłatna** i **współdzielona** są uruchamiane w ramach procesów roboczych na udostępnionych maszynach wirtualnych, podczas gdy aplikacje wdrożone w warstwach **standardowa** i **Premium** są uruchamiane na maszynach wirtualnych przeznaczonych specjalnie dla aplikacji skojarzonych z pojedynczym klientem.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ponieważ App Service obsługuje bezproblemowe skalowanie między różnymi warstwami, Konfiguracja zabezpieczeń wymuszana dla App Service aplikacji pozostaje taka sama. Gwarantuje to, że aplikacje nie będą nagle działać inaczej, podczas gdy App Service planuje przełączać się między warstwami.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Platformy programistyczne
App Service warstwy cenowe kontrolują ilość zasobów obliczeniowych (procesor CPU, magazyn dyskowy, pamięć i ruch wychodzący w sieci), które są dostępne dla aplikacji. Jednak szerokość platformy dostępna dla aplikacji pozostaje taka sama niezależnie od warstw skalowania.

App Service obsługuje różne platformy programistyczne, w tym ASP.NET, klasyczne ASP, node.js, PHP i Python — wszystkie uruchomienia jako rozszerzenia w ramach usług IIS. Aby uprościć i znormalizować konfigurację zabezpieczeń, App Service aplikacje zwykle uruchamiają różne platformy programistyczne z ustawieniami domyślnymi. Jednym z metod konfigurowania aplikacji może być dostosowanie obszaru powierzchni interfejsu API do poszczególnych platform programistycznych. App Service zamiast tego przyjmuje bardziej ogólne podejście, włączając wspólną podstawę funkcji systemu operacyjnego niezależnie od struktury projektowania aplikacji.

Poniższe sekcje zawierają podsumowanie ogólnych rodzajów funkcji systemu operacyjnego dostępnych dla App Service aplikacji.

<a id="FileAccess"></a>

## <a name="file-access"></a>Dostęp do plików
Istnieją różne dyski w App Service, w tym dyski lokalne i dyski sieciowe.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Dyski lokalne
App Service to usługa działająca w oparciu o infrastrukturę platformy Azure PaaS (platforma jako usługa). W związku z tym dyski lokalne, które są "dołączone" do maszyny wirtualnej, są tymi samymi dyskami, które są dostępne dla każdej roli procesu roboczego działającej na platformie Azure. Możliwości obejmują:

- Dysk systemu operacyjnego (D:\ litera
- Dysk aplikacji zawierający pliki cspkg pakietu platformy Azure używane wyłącznie przez App Service (i niedostępne dla klientów)
- Stacja "użytkownika" (C:\ Drive), którego rozmiar zmienia się w zależności od rozmiaru maszyny wirtualnej. 

Ważne jest, aby monitorować wykorzystanie dysku w miarę zwiększania się aplikacji. Po osiągnięciu limitu przydziału dysku może on mieć niekorzystny wpływ na aplikację. Na przykład: 

- Aplikacja może zgłosić błąd informujący o braku wystarczającej ilości miejsca na dysku.
- Błędy dysku mogą być widoczne podczas przeglądania do konsoli kudu.
- Wdrożenie z usługi Azure DevOps lub programu Visual Studio może zakończyć się niepowodzeniem `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` .
- Aplikacja może mieć negatywny wpływ na wydajność.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Dyski sieciowe (udziały UNC)
Jednym z unikatowych aspektów App Service, które ułatwiają wdrażanie i konserwację aplikacji, jest to, że cała zawartość użytkownika jest przechowywana na zestawie udziałów UNC. Ten model jest dobrze mapowany do wspólnego wzorca magazynu zawartości używanego przez lokalne środowiska hostingu w sieci Web, które mają wiele serwerów o zrównoważonym obciążeniu. 

W ramach App Service istnieje wiele udziałów UNC utworzonych w każdym centrum danych. Procent zawartości użytkownika dla wszystkich klientów w poszczególnych centrach danych jest przypisywany do każdego udziału UNC. Ponadto cała zawartość pliku dla subskrypcji jednego klienta jest zawsze umieszczana w tym samym udziale UNC. 

Ze względu na sposób działania usług platformy Azure, określona maszyna wirtualna odpowiedzialna za hostowanie udziału UNC zmieni się z upływem czasu. Jest gwarantowane, że udziały UNC będą instalowane przez różne maszyny wirtualne w miarę ich przygotowywania w trakcie normalnego działania platformy Azure. Z tego powodu aplikacje nigdy nie powinny wprowadzać sztywno założeń, że informacje o maszynie w ścieżce pliku UNC pozostaną stabilne w czasie. Zamiast tego powinny *używać wygodnej* , **D:\home\site** ścieżki bezwzględnej, która zapewnia App Service. Ta przysymulowana ścieżka bezwzględna zapewnia przenośną metodę niezależny od, App-and-User-dla odwołującą się do własnej aplikacji. Za pomocą **D:\home\site**, jeden może przesyłać pliki udostępnione z aplikacji do aplikacji bez konieczności konfigurowania nowej ścieżki bezwzględnej dla każdego transferu.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typy dostępu do plików przyznane aplikacji
Każda subskrypcja klienta ma zastrzeżoną strukturę katalogów w określonym udziale UNC w centrum danych. Klient może mieć wiele aplikacji utworzonych w ramach określonego centrum danych, więc wszystkie katalogi należące do pojedynczej subskrypcji klienta są tworzone w tym samym udziale UNC. Udział może zawierać katalogi, takie jak te dla zawartości, dzienniki błędów i diagnostyki oraz wcześniejsze wersje aplikacji utworzonej przez kontrolę źródła. Zgodnie z oczekiwaniami katalogi aplikacji klienta są dostępne do odczytu i zapisu w czasie wykonywania przez kod aplikacji aplikacji.

Na dyskach lokalnych podłączonych do maszyny wirtualnej, na której działa aplikacja, App Service rezerwuje fragmentu miejsca na C:\ Stacja dysków dla tymczasowego magazynu lokalnego dla konkretnej aplikacji. Mimo że aplikacja ma pełny dostęp do odczytu i zapisu do własnego tymczasowego magazynu lokalnego, magazyn naprawdę nie jest przeznaczony do użycia bezpośrednio w kodzie aplikacji. Zamiast tego należy zapewnić tymczasowy magazyn plików dla usług IIS i struktur aplikacji sieci Web. App Service również ogranicza ilość tymczasowego magazynu lokalnego dostępnego dla każdej aplikacji, aby uniemożliwić poszczególnym aplikacjom zużywanie nadmiernych ilości lokalnych plików magazynu.

Dwa przykłady użycia tymczasowego magazynu lokalnego przez App Service są katalogiem plików tymczasowych ASP.NET i katalogiem skompresowanych plików usług IIS. System kompilacji ASP.NET używa katalogu "Temporary ASP.NET Files" jako tymczasowej lokalizacji pamięci podręcznej kompilacji. Usługi IIS używają katalogu "tymczasowe pliki skompresowane usługi IIS" do przechowywania danych wyjściowych skompresowanych odpowiedzi. Oba typy użycia plików (a także inne) są ponownie mapowane w App Service do tymczasowego magazynu lokalnego dla aplikacji. To ponowne mapowanie zapewnia, że funkcje będą nadal działać zgodnie z oczekiwaniami.

Każda aplikacja w App Service jest uruchamiana jako Losowa unikatowa tożsamość procesu roboczego o niskim poziomie uprawnień o nazwie "tożsamość puli aplikacji", opisana tutaj: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) . Kod aplikacji używa tej tożsamości do podstawowego dostępu tylko do odczytu do dysku systemu operacyjnego (D:\ dysk). Oznacza to, że kod aplikacji może wyświetlać listę wspólnych struktur katalogów i odczytywać typowe pliki na dysku z systemem operacyjnym. Chociaż może to być nieco szerszego poziomu dostępu, te same katalogi i pliki są dostępne podczas aprowizacji roli proces roboczy w usłudze hostowanej platformy Azure i odczytywania zawartości dysku. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Dostęp do plików w wielu wystąpieniach
Katalog macierzysty zawiera zawartość aplikacji i może zapisywać w niej kod aplikacji. Jeśli aplikacja działa w wielu wystąpieniach, katalog macierzysty jest współużytkowany między wszystkimi wystąpieniami, tak aby wszystkie wystąpienia były widoczne w tym samym katalogu. Jeśli na przykład aplikacja zapisuje przekazane pliki do katalogu macierzystego, te pliki są natychmiast dostępne dla wszystkich wystąpień. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Dostęp do sieci
Kod aplikacji może korzystać z protokołów TCP/IP i UDP, aby wychodzące połączenia sieciowe z punktami końcowymi dostępnymi do Internetu, które uwidaczniają usługi zewnętrzne. Aplikacje mogą używać tych samych protokołów do łączenia się z usługami w ramach platformy Azure, na przykład przez ustanawianie połączeń HTTPS do SQL Database.

Istnieje również ograniczona możliwość dla aplikacji do nawiązywania jednego lokalnego połączenia sprzężenia zwrotnego i nasłuchiwania aplikacji na tym lokalnym gnieździe sprzężenia zwrotnego. Ta funkcja istnieje głównie do włączania aplikacji nasłuchujących lokalnych gniazd sprzężenia zwrotnego w ramach ich funkcjonalności. Każda aplikacja widzi "prywatne" połączenie sprzężenia zwrotnego. Aplikacja "A" nie może nasłuchiwać lokalnego gniazda sprzężenia zwrotnego określonego przez aplikację "B".

Nazwane potoki są również obsługiwane jako mechanizm komunikacji między procesami (IPC) między różnymi procesami, które zbiorczo uruchamiają aplikację. Na przykład moduł FastCGI usług IIS korzysta z nazwanych potoków, aby koordynować poszczególne procesy, które uruchamiają strony PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Wykonywanie kodu, procesy i pamięć
Jak wspomniano wcześniej, aplikacje działają w ramach procesów roboczych z niskim poziomem uprawnień przy użyciu tożsamości losowej puli aplikacji. Kod aplikacji ma dostęp do przestrzeni pamięci skojarzonej z procesem roboczym, a także wszystkich procesów podrzędnych, które mogą być duplikowane przez procesy CGI lub inne aplikacje. Jednak jedna aplikacja nie może uzyskać dostępu do pamięci lub danych innej aplikacji, nawet jeśli znajduje się na tej samej maszynie wirtualnej.

Aplikacje mogą uruchamiać skrypty lub strony z obsługiwanymi strukturami programowania w sieci Web. App Service nie konfiguruje żadnych ustawień platformy sieci Web w celu uzyskania większej liczby trybów ograniczonych. Na przykład aplikacje ASP.NET uruchomione w App Service działają w "pełnej" relacji zaufania, a nie w trybie ograniczonym zaufania. Platformy sieci Web, w tym klasyczne ASP i ASP.NET, mogą wywoływać składniki COM (ale nie procesy), takie jak ADO (ActiveX Data Objects), które są domyślnie zarejestrowane w systemie operacyjnym Windows.

Aplikacje mogą zduplikować i uruchamiać dowolny kod. Jest to możliwe, aby aplikacja mogła wykonywać takie czynności jak duplikowanie powłoki poleceń lub uruchamianie skryptu programu PowerShell. Jednak mimo że dowolny kod i procesy można zduplikować z aplikacji, programy wykonywalne i skrypty nadal są ograniczone do uprawnień przypisanych do nadrzędnej puli aplikacji. Na przykład aplikacja może zduplikować plik wykonywalny, który wykonuje wychodzące wywołanie HTTP, ale ten sam plik wykonywalny nie może próbować usunąć powiązania adresu IP maszyny wirtualnej z jej karty sieciowej. Wychodzące wywołanie sieciowe może mieć kod z niskimi uprawnieniami, ale próba zmiany konfiguracji ustawień sieciowych na maszynie wirtualnej wymaga uprawnień administracyjnych.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Dzienniki i zdarzenia diagnostyczne
Informacje dziennika to kolejny zestaw danych, do których niektóre aplikacje próbują uzyskać dostęp. Typy informacji dziennika dostępne dla kodu uruchomionego w App Service zawierają informacje diagnostyczne i dzienniki wygenerowane przez aplikację, która jest również łatwo dostępna dla aplikacji. 

Na przykład dzienniki W3C protokołu HTTP generowane przez aktywną aplikację są dostępne w katalogu dziennika w lokalizacji udziału sieciowego utworzonego dla aplikacji lub dostępne w usłudze BLOB Storage, jeśli klient skonfigurował rejestrowanie W3C w magazynie. Ta ostatnia opcja umożliwia zebranie dużych ilości dzienników bez ryzyka przekroczenia limitów magazynowania plików skojarzonych z udziałem sieciowym.

W podobnym przerostie informacje diagnostyczne z aplikacji .NET mogą być również rejestrowane przy użyciu infrastruktury śledzenia i diagnostyki .NET, z opcjami zapisywania informacji śledzenia w udziale sieciowym aplikacji, a także do lokalizacji magazynu obiektów BLOB.

Obszary rejestrowania i śledzenia diagnostyki, które nie są dostępne dla aplikacji, to zdarzenia ETW systemu Windows i typowe dzienniki zdarzeń systemu Windows (na przykład system, aplikacja i dzienniki zdarzeń zabezpieczeń). Ze względu na to, że informacje śledzenia funkcji ETW mogą być potencjalnie widoczne dla maszyn (z prawidłowymi listami ACL), dostęp do odczytu i zapisu do zdarzeń ETW jest blokowany. Deweloperzy mogą zauważyć, że wywołania interfejsu API odczytu i zapisu zdarzeń ETW oraz typowe dzienniki zdarzeń systemu Windows wyglądają na działanie, ale jest to spowodowane tym, że App Service jest "faking" wywołania, aby pojawiły się pomyślnie. W rzeczywistości kod aplikacji nie ma dostępu do danych tego zdarzenia.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Dostęp do rejestru
Aplikacje mają dostęp tylko do odczytu do wielu (choć nie wszystkie) rejestru maszyny wirtualnej, w której są uruchomione. W rzeczywistości oznacza to, że klucze rejestru zezwalające na dostęp tylko do odczytu do grupy Użytkownicy lokalni są dostępne dla aplikacji. Jednym z obszarów rejestru, który nie jest obecnie obsługiwany w przypadku dostępu do odczytu lub zapisu, jest HKEY \_ bieżącej \_ gałęzi użytkownika.

Dostęp do zapisu w rejestrze jest blokowany, w tym dostęp do dowolnych kluczy rejestru dla poszczególnych użytkowników. Z punktu widzenia aplikacji dostęp do zapisu w rejestrze nigdy nie powinien być w środowisku platformy Azure, ponieważ aplikacje mogą wykonywać migrację między różnymi maszynami wirtualnymi. Jedyną trwałym magazynem zapisywalnym, z którym może być zależna aplikacja, jest struktura katalogów zawartości dla aplikacji przechowywana w App Service udziałach UNC. 

## <a name="remote-desktop-access"></a>Dostęp do pulpitu zdalnego

App Service nie zapewnia dostępu pulpitu zdalnego do wystąpień maszyn wirtualnych.

## <a name="more-information"></a>Więcej informacji

[Azure App Service piaskownicy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) — najbardziej aktualne informacje o środowisku wykonawczym App Service. Ta strona jest utrzymywana bezpośrednio przez zespół deweloperów App Service.