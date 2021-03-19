---
title: Hostowanie wielu witryn w usłudze Azure Application Gateway
description: Ten artykuł zawiera omówienie obsługi wielolokacjowej platformy Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87474329"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostowanie wielu witryn usługi Application Gateway

Obsługa wielu witryn umożliwia skonfigurowanie więcej niż jednej aplikacji sieci Web na tym samym porcie bramy aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie nawet ponad 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład trzy domeny — contoso.com, fabrikam.com i adatum.com — wskazują adres IP bramy aplikacji. Utworzysz trzy odbiorniki obejmujące wiele witryn i skonfigurujesz każdy odbiornik dla odpowiedniego portu i ustawienia protokołu. 

Możesz również określić nazwy hosta z symbolami wieloznacznymi w odbiorniku obejmującym wiele witryn, z maksymalnie pięcioma nazwami hostów na odbiornik. Aby dowiedzieć się więcej, zobacz [symbole wieloznaczne nazw hostów w odbiorniku](#wildcard-host-names-in-listener-preview).

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Application Gateway wiele lokacji":::

> [!IMPORTANT]
> Reguły są przetwarzane w kolejności, w której są wyświetlane w portalu dla jednostki SKU w wersji 1. W przypadku jednostki SKU v2 dokładne dopasowania mają wyższy priorytet. Zdecydowanie zaleca się skonfigurowanie odbiorników obejmujących wiele lokacji przed skonfigurowaniem podstawowego odbiornika.  Zapewni to skierowanie ruchu do odpowiedniego zaplecza. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik.

Żądania dotyczące adresu `http://contoso.com` są kierowane do puli ContosoServerPool, a żądania dotyczące adresu `http://fabrikam.com` — do puli FabrikamServerPool.

Analogicznie, można hostować wiele poddomen w tej samej domenie nadrzędnej w ramach tego samego wdrożenia bramy aplikacji. Można na przykład hostować `http://blog.contoso.com` i obsługiwać `http://app.contoso.com` pojedyncze wdrożenie bramy aplikacji.

## <a name="wildcard-host-names-in-listener-preview"></a>Nazwy hostów symboli wieloznacznych w odbiorniku (wersja zapoznawcza)

Application Gateway umożliwia routing oparty na hoście przy użyciu odbiornika HTTP (S) z obsługą wiele witryn. Teraz można korzystać z symboli wieloznacznych, takich jak gwiazdka (*) i znak zapytania (?) w nazwie hosta oraz do 5 nazw hostów dla odbiornika HTTP (S) z jedną lokacją. Na przykład `*.contoso.com`.

Używając symbolu wieloznacznego w nazwie hosta, można dopasować wiele nazw hostów w pojedynczym odbiorniku. Na przykład `*.contoso.com` może być zgodne z `ecom.contoso.com` , `b2b.contoso.com` `customer1.b2b.contoso.com` i tak dalej. Korzystając z tablicy nazw hostów, można skonfigurować więcej niż jedną nazwę hosta dla odbiornika, aby kierować żądania do puli zaplecza. Może na przykład znajdować się odbiornik, `contoso.com, fabrikam.com` który akceptuje żądania dla nazwy hosta.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Odbiornik symboli wieloznacznych":::

>[!NOTE]
> Ta funkcja jest w wersji zapoznawczej i jest dostępna tylko dla Standard_v2 i WAF_v2 SKU Application Gateway. Aby dowiedzieć się więcej na temat wersji zapoznawczych, zobacz [warunki użytkowania tutaj](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
>Ta funkcja jest obecnie dostępna tylko za pomocą [Azure PowerShell](tutorial-multiple-sites-powershell.md) i [interfejsu wiersza polecenia platformy Azure](tutorial-multiple-sites-cli.md). Obsługa portalu będzie dostępna wkrótce.
> Należy pamiętać, że ponieważ obsługa portalu nie jest w pełni dostępna, jeśli używasz tylko parametru HostNames, odbiornik zostanie wyświetlony jako odbiornik podstawowy w portalu, a kolumna Nazwa hosta w widoku listy odbiorników nie będzie zawierać skonfigurowanych nazw hostów. W przypadku wszelkich zmian w odbiorniku z symbolami wieloznacznymi upewnij się, że używasz Azure PowerShell lub interfejsu wiersza polecenia, dopóki nie zostanie ono obsługiwane w portalu.

W [Azure PowerShell](tutorial-multiple-sites-powershell.md)należy użyć `-HostNames` zamiast `-HostName` . Za pomocą nazw hostów można wspominać do 5 nazw hostów jako wartości rozdzielane przecinkami i używać symboli wieloznacznych. Na przykład `-HostNames "*.contoso.com,*.fabrikam.com"`

W [interfejsie wiersza polecenia platformy Azure](tutorial-multiple-sites-cli.md)należy użyć `--host-names` zamiast `--host-name` . Nazwy hostów można wymieniać do 5 nazw hostów jako wartości rozdzielane przecinkami i używać symboli wieloznacznych. Na przykład `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Dozwolone znaki w polu nazwy hostów:

* `(A-Z,a-z,0-9)` -znaki alfanumeryczne
* `-` -myślnik lub minus
* `.` -Period jako ogranicznik
*   `*` -może być zgodne z wieloma znakami w dozwolonym zakresie
*   `?` -może pasować do pojedynczego znaku w dozwolonym zakresie

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Warunki używania symboli wieloznacznych i wielu nazw hostów w odbiorniku:

*   W pojedynczym odbiorniku można wymieniać maksymalnie 5 nazw hostów
*   Gwiazdkę `*` można podać tylko raz w składniku nazwy stylu domeny lub nazwy hosta. Na przykład Component1 *. component2*. component3. `(*.contoso-*.com)` jest prawidłowy.
*   Nazwa hosta może zawierać maksymalnie dwie gwiazdki `*` . Na przykład `*.contoso.*` jest prawidłowy i `*.contoso.*.*.com` nieprawidłowy.
*   Nazwa hosta może zawierać maksymalnie 4 symbole wieloznaczne. Na przykład, `????.contoso.com` , `w??.contoso*.edu.*` są prawidłowe, ale `????.contoso.*` jest nieprawidłowy.
*   Użycie gwiazdki `*` i znaku zapytania `?` razem w składniku nazwy hosta ( `*?` lub `?*` lub `**` ) jest nieprawidłowe. Na przykład `*?.contoso.com` i `**.contoso.com` są nieprawidłowe.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Zagadnienia i ograniczenia dotyczące używania symboli wieloznacznych lub wielu nazw hostów w odbiorniku:

*   [Zakończenie protokołu SSL i kompleksowe zabezpieczenia SSL](ssl-overview.md) wymagają skonfigurowania protokołu jako https i przekazania certyfikatu do użycia w konfiguracji odbiornika. Jeśli jest to odbiornik z obsługą wiele lokacji, można również wprowadzić nazwę hosta, zazwyczaj jest to nazwa POSPOLITa certyfikatu SSL. W przypadku określania wielu nazw hostów w odbiorniku lub używania symboli wieloznacznych należy wziąć pod uwagę następujące kwestie:
    *   Jeśli jest to symbol wieloznaczny, taki jak *. contoso.com, należy przekazać certyfikat z symbolem wieloznacznym z nazwą POSPOLITą, taką jak *. contoso.com
    *   Jeśli w tym samym odbiorniku wymieniono wiele nazw hostów, należy przekazać certyfikat sieci SAN (alternatywne nazwy podmiotu) przy użyciu CN zgodnych z wymienionymi nazwami hostów.
*   Nie można użyć wyrażenia regularnego do wspominania o nazwie hosta. Można używać symboli wieloznacznych, takich jak gwiazdka (*) i znak zapytania (?), aby utworzyć wzorzec nazwy hosta.
*   W przypadku kontroli kondycji zaplecza nie można skojarzyć wielu [niestandardowych sond](application-gateway-probe-overview.md) dla ustawień http. Zamiast tego można sondować jedną z witryn sieci Web w zapleczu lub użyć adresu "127.0.0.1" do sondowania hosta lokalnego serwera wewnętrznej bazy danych. Jednak w przypadku używania symboli wieloznacznych lub wielu nazw hostów w odbiorniku żądania dla wszystkich określonych wzorców domeny będą kierowane do puli zaplecza, w zależności od typu reguły (opartej na ścieżce podstawowej lub ścieżki).
*   Właściwości "hostname" przyjmuje jeden ciąg jako dane wejściowe, gdzie można wspomnieć tylko jedną nazwę domeny niezawierającą symboli wieloznacznych i "Hostnames" pobiera tablicę ciągów jako dane wejściowe, gdzie można wymienić do 5 nazw domen wieloznacznych. Ale obu właściwości nie można używać jednocześnie.
*   Nie można utworzyć reguły [przekierowania](redirect-overview.md) z odbiornikiem docelowym, który używa symboli wieloznacznych lub wielu nazw hostów.

Zobacz [Tworzenie wielowitryn przy użyciu Azure PowerShell](tutorial-multiple-sites-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](tutorial-multiple-sites-cli.md) , aby zapoznać się z przewodnikiem krok po kroku na temat konfigurowania nazw hostów symboli wieloznacznych.

## <a name="host-headers-and-server-name-indication-sni"></a>Nagłówki hosta i oznaczanie nazwy serwera (SNI, Server Name Indication)

Istnieją trzy popularne mechanizmy włączania hostingu wielu witryn w tej samej infrastrukturze.

1. Hostowanie wielu aplikacji internetowych — każda z nich na unikatowym adresie IP.
2. Użycie nazwy hosta do hostowania wielu aplikacji internetowych na tym samym adresie IP.
3. Użycie różnych portów do hostowania wielu aplikacji internetowych na tym samym adresie IP.

Obecnie Application Gateway obsługuje jeden publiczny adres IP, na którym nasłuchuje ruch. W związku z tym wiele aplikacji, z których każdy ma własny adres IP, nie jest obecnie obsługiwane. 

Application Gateway obsługuje wiele aplikacji, które nasłuchują na różnych portach, ale ten scenariusz wymaga, aby aplikacje akceptowały ruch na portach niestandardowych. Często nie jest to konfiguracja, której chcesz użyć.

Usługa Application Gateway bazuje na nagłówkach hosta HTTP 1.1 w celu hostowania więcej niż jednej witryny sieci Web na tym samym publicznym adresie IP i porcie. Lokacje hostowane w usłudze Application Gateway mogą również obsługiwać odciążanie protokołu TLS przy użyciu rozszerzenia TLS Oznaczanie nazwy serwera (SNI). Ten scenariusz oznacza, że przeglądarka i farma sieci Web zaplecza klienta muszą obsługiwać protokół HTTP/1.1 i rozszerzenie TLS zgodnie ze standardem RFC 6066.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować obsługę wielu witryn w Application Gateway
* [Korzystanie z witryny Azure Portal](create-multiple-sites-portal.md)
* [Korzystanie z programu Azure PowerShell](tutorial-multiple-sites-powershell.md) 
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](tutorial-multiple-sites-cli.md)

Możesz odwiedzić stronę [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) (Szablon usługi Resource Manager z zastosowaniem hostowania wielu witryn), aby zapoznać się z kompleksowym wdrożeniem opartym na szablonie.
