---
title: Często zadawane pytania dotyczące konfiguracji
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem w programie Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fb193637525722bf227241a614cd977fbf70c9ac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074186"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące konfiguracji i zarządzania dla Web Apps na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem dla [Web Apps funkcji Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Czy istnieją ograniczenia, o których należy pamiętać, jeśli chcę przenieść App Service zasoby?

Jeśli planujesz przenoszenie App Service zasobów do nowej grupy zasobów lub subskrypcji, musisz znać kilka ograniczeń. Aby uzyskać więcej informacji, zobacz [ograniczenia App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Jak mogę użyć niestandardowej nazwy domeny dla mojej aplikacji sieci Web?

Aby uzyskać odpowiedzi na często zadawane pytania dotyczące używania niestandardowej nazwy domeny w aplikacji sieci Web platformy Azure, zobacz nasze siedem-minutowe wideo [Dodaj niestandardową nazwę domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Film wideo zawiera Przewodnik dotyczący dodawania niestandardowej nazwy domeny. W tym artykule opisano sposób użycia własnego adresu URL zamiast adresu URL *. azurewebsites.net z aplikacją internetową App Service. Można też zobaczyć szczegółowy przewodnik [dotyczący sposobu mapowania niestandardowej nazwy domeny](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak mogę zakupić nową domenę niestandardową dla mojej aplikacji sieci Web?

Aby dowiedzieć się, jak kupić i skonfigurować domenę niestandardową dla App Service aplikacji sieci Web, zobacz [kupowanie i Konfigurowanie niestandardowej nazwy domeny w App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Jak mogę przekazać i skonfigurować istniejący certyfikat TLS/SSL dla mojej aplikacji sieci Web?

Aby dowiedzieć się, jak przekazać i skonfigurować istniejący niestandardowy certyfikat TLS/SSL, zobacz [Dodawanie certyfikatu TLS/SSL do aplikacji App Service](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Jak mogę kupić i skonfigurować nowy certyfikat TLS/SSL na platformie Azure dla mojej aplikacji sieci Web?

Aby dowiedzieć się, jak kupić i skonfigurować certyfikat TLS/SSL dla aplikacji internetowej App Service, zobacz [Dodawanie certyfikatu TLS/SSL do aplikacji App Service](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Jak mogę przenieść zasoby Application Insights?

Obecnie usługa Azure Application Insights nie obsługuje operacji przenoszenia. Jeśli oryginalna Grupa zasobów zawiera zasób Application Insights, nie można przenieść tego zasobu. Jeśli podczas próby przeniesienia aplikacji App Service zostanie uwzględniony zasób Application Insights, cała operacja przenoszenia zakończy się niepowodzeniem. Jednak Application Insights i plan App Service nie musi znajdować się w tej samej grupie zasobów, co aplikacja, aby aplikacja działała poprawnie.

Aby uzyskać więcej informacji, zobacz [ograniczenia App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Gdzie można znaleźć listę kontrolną wskazówek i dowiedzieć się więcej o operacjach przenoszenia zasobów?

[Ograniczenia App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) przedstawiają sposób przenoszenia zasobów do nowej subskrypcji lub nowej grupy zasobów w ramach tej samej subskrypcji. Możesz uzyskać informacje na temat listy kontrolnej przenoszenia zasobów, dowiedzieć się, które usługi obsługują operację przenoszenia, i Dowiedz się więcej na temat ograniczeń App Service i innych tematów.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak mogę ustawić strefy czasowej serwera dla mojej aplikacji sieci Web?

Aby ustawić strefę czasową serwera dla aplikacji sieci Web:

1. W Azure Portal w subskrypcji App Service przejdź do menu **Ustawienia aplikacji** .
2. W obszarze **Ustawienia aplikacji** Dodaj następujące ustawienie:
    * Klucz = WEBSITE_TIME_ZONE
    * Wartość = *wybrana strefa czasowa*
3. Wybierz pozycję **Zapisz** .

W przypadku usługi App Services, które działają w systemie Windows, zobacz dane wyjściowe z polecenia systemu Windows `tzutil /L` . Użyj wartości z drugiego wiersza każdego wpisu. Na przykład: "Tonga (czas standardowy)". Niektóre z tych wartości są również wymienione w kolumnie **timezone** w [domyślnych strefach czasowych](/windows-hardware/manufacture/desktop/default-time-zones).

W przypadku usług App Services działających w systemie Linux ustaw wartość z [bazy danych Iana](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Na przykład: "Ameryka/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Dlaczego moje ciągłe zadania WebJob czasami kończą się niepowodzeniem?

Domyślnie aplikacje sieci Web są zwalniane, jeśli są bezczynne przez określony czas. Dzięki temu system będzie zaoszczędzić zasoby. W planach Basic i standard można włączyć ustawienie **zawsze włączone** , aby zapewnić, że aplikacja sieci Web zostanie załadowana przez cały czas. Jeśli aplikacja sieci Web uruchamia ciągłe zadania WebJob, należy włączyć opcję **zawsze włączone** lub zadania WebJob mogą nie działać niezawodnie. Aby uzyskać więcej informacji, zobacz [Tworzenie zadania WebJob działającego w sposób ciągły](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak mogę uzyskać wychodzący adres IP dla mojej aplikacji sieci Web?

Aby uzyskać listę wychodzących adresów IP dla aplikacji sieci Web:

1. W Azure Portal, w bloku aplikacji sieci Web, przejdź do menu **Właściwości** .
2. Wyszukaj **wychodzące adresy IP** .

Zostanie wyświetlona lista wychodzących adresów IP.

Aby dowiedzieć się, jak uzyskać wychodzący adres IP, jeśli witryna sieci Web jest hostowana w App Service Environment, zobacz [adresy sieci wychodzące](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak mogę uzyskać zastrzeżony lub dedykowany adres IP ruchu przychodzącego dla mojej aplikacji sieci Web?

Aby skonfigurować dedykowany lub zastrzeżony adres IP dla wywołań przychodzących w witrynie sieci Web aplikacji platformy Azure, należy zainstalować i skonfigurować certyfikat TLS/SSL oparty na protokole IP.

Należy pamiętać, że aby użyć dedykowanego lub zastrzeżony adres IP dla wywołań przychodzących, plan App Service musi być w planie usług w warstwie Podstawowa lub wyższa.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Czy mogę wyeksportować certyfikat App Service, który ma być używany poza platformą Azure, na przykład w przypadku witryny sieci Web hostowanej w innym miejscu? 

Tak, możesz wyeksportować je do użycia poza platformą Azure. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące App Service certyfikatów i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Czy mogę wyeksportować certyfikat App Service, który ma być używany z innymi usługami w chmurze Azure?

Portal zapewnia środowisko pierwszej klasy do wdrażania certyfikatu App Service za pomocą Azure Key Vault do aplikacji App Service. Jednak otrzymamy od klientów żądania używania tych certyfikatów poza platformą App Service, na przykład za pomocą usługi Azure Virtual Machines. Aby dowiedzieć się, jak utworzyć lokalną kopię pliku PFX certyfikatu App Service, aby można było używać certyfikatu z innymi zasobami platformy Azure, zobacz [Tworzenie lokalnej kopii PFX certyfikatu App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące App Service certyfikatów i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Dlaczego podczas próby utworzenia kopii zapasowej mojej aplikacji sieci Web widzę komunikat "częściowe powodzenie"?

Typową przyczyną niepowodzenia tworzenia kopii zapasowej jest to, że niektóre pliki są używane przez aplikację. Pliki, które są używane, są blokowane podczas wykonywania kopii zapasowej. Zapobiega to wykonaniu kopii zapasowej tych plików, co może skutkować niepowodzeniem. Może to uniemożliwić wykluczenie tych plików z procesu tworzenia kopii zapasowej. Można utworzyć kopię zapasową tylko tego, co jest potrzebne. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej tylko ważnych części witryny za pomocą usługi Azure Web Apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak mogę usunąć nagłówka z odpowiedzi HTTP?

Aby usunąć nagłówki z odpowiedzi HTTP, zaktualizuj plik web.config lokacji. Aby uzyskać więcej informacji, zobacz [usuwanie standardowych nagłówków serwera w usłudze Azure Websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Czy jest App Service zgodne ze standardami PCI 3,0 i 3,1?

Obecnie funkcja Web Apps Azure App Service jest zgodna z usługą PCI Data Security Standard (DSS) w wersji 3,0 na poziomie 1. PCI DSS wersja 3,1 znajduje się w naszym przewodniku. Planowanie jest już w trakcie wdrażania najnowszej wersji Standard.

Certyfikat PCI DSS w wersji 3,1 wymaga wyłączenia Transport Layer Security (TLS) 1,0. Obecnie wyłączenie protokołu TLS 1,0 nie jest opcją dla większości planów App Service. Jeśli jednak używasz App Service Environment lub chcesz przeprowadzić migrację obciążenia do App Service Environment, możesz uzyskać większą kontrolę nad Twoim środowiskiem. Obejmuje to wyłączenie protokołu TLS 1,0 przez skontaktowanie się z pomocą techniczną platformy Azure. W najbliższej przyszłości planujemy udostępnienie tych ustawień użytkownikom.

Aby uzyskać więcej informacji, zobacz [Microsoft Azure App Service zgodność aplikacji sieci Web ze standardami PCI 3,0 i 3,1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Jak mogę użyć środowiska przejściowego i miejsc wdrożenia?

W planach App Service w warstwach Standardowa i Premium podczas wdrażania aplikacji sieci Web do App Service można wdrożyć program w osobnym miejscu wdrożenia, a nie w domyślnym gnieździe produkcyjnym. Miejsca wdrożenia to aplikacje sieci Web z własnymi nazwami hostów. Elementy zawartości i konfiguracji aplikacji sieci Web można wymieniać między dwoma miejscami wdrożenia, w tym miejscem produkcyjnym.

Aby uzyskać więcej informacji o używaniu miejsc wdrożenia, zobacz [Konfigurowanie środowiska tymczasowego w App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak mogę dostęp i przeglądanie dzienników zadań WebJob?

Aby przejrzeć dzienniki zadań WebJob:

1. Zaloguj się do **witryny sieci Web kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Wybierz zadanie WebJob.
3. Wybierz przycisk **Przełącz dane wyjściowe** .
4. Aby pobrać plik wyjściowy, wybierz link **pobierania** .
5. Dla poszczególnych przebiegów wybierz opcję **pojedyncze wywołanie** .
6. Wybierz przycisk **Przełącz dane wyjściowe** .
7. Wybierz link pobierania.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Próbuję użyć Połączenia hybrydowe z SQL Server. Dlaczego widzę komunikat "System. OverflowException: operacja arytmetyczna spowodowała przepełnienie"?

W przypadku korzystania z Połączenia hybrydowe w celu uzyskania dostępu do SQL Server aktualizacja Microsoft .NET 10 maja 2016 może powodować niepowodzenie połączeń. Może zostać wyświetlony następujący komunikat:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Rozwiązanie

Wyjątek został spowodowany przez problem z Menedżer połączeń hybrydowych, który został usunięty. Aby rozwiązać ten problem, należy [zaktualizować Menedżer połączeń hybrydowych](https://go.microsoft.com/fwlink/?LinkID=841308) .

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Jak mogę dodać regułę ponownego zapisywania adresu URL?

Aby dodać regułę ponownego zapisywania adresu URL, Utwórz plik web.config z odpowiednimi wpisami konfiguracji w folderze **wwwroot** . Aby uzyskać więcej informacji, zobacz [Azure App Services: Omówienie ponownego zapisywania adresów URL](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak mogę kontrolować ruch przychodzący do App Service?

Na poziomie witryny dostępne są dwie opcje kontrolowania ruchu przychodzącego do App Service:

* Włącz ograniczenia dynamicznego adresu IP. Aby dowiedzieć się, jak włączyć ograniczenia dynamicznego adresu IP, zobacz [Ograniczenia adresów IP i domen dla usługi Azure Websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Włącz zabezpieczenia modułu. Aby dowiedzieć się, jak włączyć zabezpieczenia modułu, zobacz [zapory ModSecurity Web Application Firewall in Azure Websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Jeśli używasz App Service Environment, możesz użyć [zapory Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak mogę blokować porty w aplikacji internetowej App Service?

W App Service udostępnionym środowisku dzierżawców nie można blokować określonych portów ze względu na charakter infrastruktury. Porty TCP 4020, 4022 i 4024 mogą być również otwarte dla zdalnego debugowania programu Visual Studio.

W App Service Environment masz pełną kontrolę nad ruchem przychodzącym i wychodzącym. W celu ograniczenia lub zablokowania określonych portów można użyć sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji na temat App Service Environment, zobacz [wprowadzenie App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak mogę przechwycić ślad F12?

Dostępne są dwie opcje przechwytywania śledzenia F12:

* Śledzenie HTTP F12
* Wyjście z konsoli F12

### <a name="f12-http-trace"></a>Śledzenie HTTP F12

1. W programie Internet Explorer przejdź do witryny sieci Web. Ważne jest, aby zalogować się przed wykonaniem następnych kroków. W przeciwnym razie ślad F12 przechwytuje poufne dane logowania.
2. Naciśnij klawisz F12.
3. Sprawdź, czy karta **Sieć** została wybrana, a następnie wybierz zielony przycisk **odtwarzania** .
4. Wykonaj kroki, które odtwarzają problem.
5. Wybierz czerwony przycisk **Zatrzymaj** .
6. Wybierz przycisk **Zapisz** (ikona dysku) i Zapisz plik HAR (w programie Internet Explorer i Microsoft Edge) *lub* kliknij prawym przyciskiem myszy plik HAR, a następnie wybierz pozycję **Zapisz jako Har z zawartością** (w przeglądarce Chrome).

### <a name="f12-console-output"></a>Wyjście z konsoli F12

1. Wybierz kartę **konsola** .
2. Dla każdej karty zawierającej więcej niż zero elementów wybierz kartę ( **błąd** , **Ostrzeżenie** lub **informacje** ). Jeśli karta nie jest zaznaczona, ikona karty jest szara lub czarna, gdy przesuniesz kursor z tego kursora.
3. Kliknij prawym przyciskiem myszy w obszarze komunikatów okienka, a następnie wybierz polecenie **Kopiuj wszystko** .
4. Wklej skopiowany tekst w pliku, a następnie Zapisz plik.

Aby wyświetlić plik HAR, można użyć [przeglądarki Har](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Dlaczego otrzymuję błąd podczas próby połączenia aplikacji sieci Web App Service z siecią wirtualną, która jest połączona z usługą ExpressRoute?

Jeśli spróbujesz połączyć aplikację sieci Web platformy Azure z siecią wirtualną, która jest połączona z usługą Azure ExpressRoute, zakończy się niepowodzeniem. Zostanie wyświetlony następujący komunikat: "Brama nie jest bramą sieci VPN".

Obecnie nie można mieć połączeń sieci VPN typu punkt-lokacja z siecią wirtualną, która jest połączona z usługą ExpressRoute. Sieć VPN typu punkt-lokacja i ExpressRoute nie mogą współistnieć w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [limity i ograniczenia połączeń sieci VPN ExpressRoute i lokacja-lokacja](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak mogę połączyć aplikację sieci Web App Service z siecią wirtualną, która ma bramę routingu statycznego (oparta na zasadach)

Obecnie łączenie aplikacji sieci Web App Service z siecią wirtualną, która ma bramę routingu statycznego (opartej na zasadach), nie jest obsługiwane. Jeśli docelowa sieć wirtualna już istnieje, musi ona mieć włączoną sieć VPN typu punkt-lokacja z bramą dynamicznej routingu, zanim będzie mogła zostać podłączona do aplikacji. Jeśli Brama jest ustawiona na routing statyczny, nie można włączyć sieci VPN typu punkt-lokacja. 

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Dlaczego w mojej App Service Environment można utworzyć tylko jeden plan App Service, mimo że są dostępne dwa procesy robocze?

Aby zapewnić odporność na uszkodzenia, App Service Environment wymaga, aby każda pula procesów roboczych potrzebowała co najmniej jeden dodatkowy zasób obliczeniowy. Dodatkowym zasobem obliczeniowym nie można przypisać obciążenia.

Aby uzyskać więcej informacji, zobacz [jak utworzyć App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Dlaczego widzę limity czasu podczas próby utworzenia App Service Environment?

Czasami tworzenie App Service Environment kończy się niepowodzeniem. W takim przypadku w dziennikach aktywności zostanie wyświetlony następujący błąd:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Aby rozwiązać ten problem, upewnij się, że żaden z następujących warunków nie jest spełniony:
* Podsieć jest za mała.
* Podsieć nie jest pusta.
* ExpressRoute zapobiega wymagania dotyczące łączności sieciowej App Service Environment.
* Niewłaściwa sieciowa Grupa zabezpieczeń uniemożliwia wymagania dotyczące łączności sieciowej App Service Environment.
* Wymuszone tunelowanie jest włączone.

Aby uzyskać więcej informacji, zobacz [częste problemy podczas wdrażania (tworzenia) nowego Azure App Service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Dlaczego nie mogę usunąć mojego planu App Service?

Nie można usunąć planu App Service, jeśli wszystkie aplikacje App Service są skojarzone z planem App Service. Przed usunięciem planu App Service Usuń wszystkie skojarzone z nim aplikacje App Service z planu App Service.

## <a name="how-do-i-schedule-a-webjob"></a>Jak mogę zaplanować zadanie WebJob?

Zaplanowane zadanie WebJob można utworzyć przy użyciu wyrażeń firmy CRONUS:

1. Utwórz plik Settings. job.
2. W tym pliku JSON Uwzględnij Właściwość Schedule przy użyciu wyrażenia typu CRONUS: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Aby uzyskać więcej informacji o zaplanowanych zadaniach WebJob, zobacz temat [Tworzenie zaplanowanej kopii zadań przy użyciu wyrażenia firmy CRONUS](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak mogę przeprowadzić testowanie penetracji dla aplikacji App Service?

Aby przeprowadzić testowanie penetracji, [Prześlij żądanie](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak mogę skonfigurować niestandardową nazwę domeny dla App Service aplikacji internetowej korzystającej z Traffic Manager?

Aby dowiedzieć się, jak używać niestandardowej nazwy domeny z aplikacją App Service, która korzysta z usługi Azure Traffic Manager do równoważenia obciążenia, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci Web platformy Azure przy użyciu Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mój certyfikat usługi App Service został oznaczony jako oszustwo. Jak rozwiązać ten problem?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Podczas weryfikacji domeny App Service zakupu certyfikatu może zostać wyświetlony następujący komunikat:

"Certyfikat został oflagowany dla potencjalnego oszustwa. Żądanie jest obecnie objęte przeglądem. Jeśli certyfikat nie będzie można użyć w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure.

Ponieważ komunikat wskazuje, ukończenie tego procesu weryfikacji oszustw może potrwać do 24 godzin. W tym czasie zobaczysz komunikat.

Jeśli certyfikat App Service będzie nadal wyświetlany po 24 godzinach, uruchom następujący skrypt programu PowerShell. Skrypt kontaktuje się bezpośrednio z [dostawcą certyfikatu](https://www.godaddy.com/) w celu rozwiązania problemu.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak działa uwierzytelnianie i autoryzacja w App Service?

Aby uzyskać szczegółową dokumentację dotyczącą uwierzytelniania i autoryzacji w App Service, zobacz dokumenty dotyczące różnych logowań dostawcy:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Konto Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak mogę przekierować domyślną domenę *. azurewebsites.net do domeny niestandardowej mojej aplikacji sieci Web platformy Azure?

Podczas tworzenia nowej witryny sieci Web przy użyciu Web Apps na platformie Azure do lokacji zostanie przypisana domyślna domena *sitename* . azurewebsites.NET. Jeśli dodasz niestandardową nazwę hosta do lokacji i nie chcesz, aby użytkownicy mogli uzyskać dostęp do domyślnej domeny *. azurewebsites.net, możesz przekierować domyślny adres URL. Aby dowiedzieć się, jak przekierować cały ruch z domeny domyślnej witryny sieci Web do domeny niestandardowej, zobacz [Przekierowanie domeny domyślnej do domeny niestandardowej w usłudze Azure Web Apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak mogę określić, która wersja programu .NET jest zainstalowana w programie App Service?

Najszybszym sposobem znalezienia wersji Microsoft .NET zainstalowanej w App Service jest użycie konsoli kudu. Możesz uzyskać dostęp do konsoli kudu z poziomu portalu lub przy użyciu adresu URL aplikacji App Service. Aby uzyskać szczegółowe instrukcje, zobacz [Określanie zainstalowanej wersji platformy .NET w App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Dlaczego automatyczne skalowanie nie działa zgodnie z oczekiwaniami?

Jeśli funkcja automatycznego skalowania platformy Azure nie została przeskalowana w poziomie lub przeskalujesz wystąpienie aplikacji sieci Web zgodnie z oczekiwaniami, może się zdarzyć, że Pracujesz w scenariuszu, w którym celowo wybierzesz nie skalować, aby uniknąć nieskończonej pętli z powodu "niestabilny". Zwykle zdarza się to, gdy nie ma odpowiedniego marginesu między progami skalowania w poziomie i skalowania w poziomie. Aby dowiedzieć się, jak uniknąć "niestabilny" i zapoznać się z innymi najlepszymi rozwiązaniami automatycznego skalowania, zobacz [najlepsze rozwiązania dotyczące skalowania automatycznego](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Dlaczego automatyczne skalowanie jest czasami skalowane tylko częściowo?

Automatyczne skalowanie jest wyzwalane, gdy metryki przekroczą wstępnie skonfigurowane granice. Czasami można zauważyć, że pojemność jest częściowo wypełniana w porównaniu z oczekiwaniami. Taka sytuacja może wystąpić, gdy liczba wystąpień jest niedostępna. W tym scenariuszu funkcja automatycznego skalowania powoduje częściowe wypełnienie przy użyciu dostępnej liczby wystąpień. Funkcja automatycznego skalowania powoduje uruchomienie logiki ponownego równoważenia w celu uzyskania większej pojemności. Przypisuje pozostałe wystąpienia. Należy pamiętać, że może to potrwać kilka minut.

Jeśli nie widzisz oczekiwanej liczby wystąpień po kilku minutach, może to być spowodowane tym, że częściowe przepełnienie było wystarczające do przeprowadzenia metryk w granicach. Lub Skalowanie automatyczne mogło być skalowane w dół, ponieważ osiągnęło dolną granicę metryk.

Jeśli żaden z tych warunków nie zostanie spełniony i problem będzie nadal występować, Prześlij żądanie pomocy technicznej.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak mogę włączyć kompresję HTTP dla mojej zawartości?

Aby włączyć kompresję zarówno dla typów zawartości statycznej, jak i dynamicznej, Dodaj następujący kod do pliku web.config na poziomie aplikacji:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Można również określić określone dynamiczne i statyczne typy MIME, które mają być kompresowane. Aby uzyskać więcej informacji, zobacz nasze odpowiedzi na pytania dotyczące forum w [ustawieniach usługi httpCompression w prostej witrynie sieci Web platformy Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak mogę przeprowadzić migrację z środowiska lokalnego do App Service?

Aby migrować lokacje z serwerów sieci Web z systemami Windows i Linux do App Service, można użyć Azure App Service Asystent migracji. Narzędzie do migracji tworzy aplikacje sieci Web i bazy danych na platformie Azure zgodnie z wymaganiami, a następnie publikuje zawartość. Aby uzyskać więcej informacji, zobacz [Azure App Service Asystent migracji](https://appmigration.microsoft.com/).
