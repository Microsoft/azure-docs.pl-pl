---
title: 'Prywatna Komunikacja równorzędna Azure ExpressRoute: Konfigurowanie trybu transportu IPsec — hosty systemu Windows'
description: Jak włączyć tryb transportu IPsec między maszynami wirtualnymi systemu Windows Azure i lokalnymi hostami systemu Windows za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute za pomocą obiektów zasad grupy i jednostek organizacyjnych
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/17/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 2dcb8489d94b9afc3ae4df829b37dd9785383d85
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "92208247"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurowanie trybu transportu IPsec dla prywatnej komunikacji równorzędnej ExpressRoute

Ten artykuł ułatwia tworzenie tuneli IPsec w trybie transportu za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute między maszynami wirtualnymi platformy Azure z systemem Windows i lokalnymi hostami systemu Windows. Kroki opisane w tym artykule tworzą tę konfigurację przy użyciu obiektów zasad grupy. Chociaż istnieje możliwość utworzenia tej konfiguracji bez korzystania z jednostek organizacyjnych (OU) i obiektów zasad grupy (GPO), kombinacja jednostek organizacyjnych i GPO będzie uprościć kontrolę nad zasadami zabezpieczeń i umożliwia szybkie skalowanie w górę. W krokach w tym artykule przyjęto założenie, że masz już konfigurację Active Directory i wiesz, że korzystasz z jednostek organizacyjnych i obiektów zasad grupy.

## <a name="about-this-configuration"></a>Informacje o tej konfiguracji

Konfiguracja w poniższych krokach korzysta z pojedynczej sieci wirtualnej platformy Azure z prywatną równorzędną ExpressRoute. Taka konfiguracja może jednak obejmować większą liczbę sieci wirtualnych platformy Azure i sieci lokalnych. Ten artykuł pomoże zdefiniować zasady szyfrowania IPsec i zastosować je do grupy maszyn wirtualnych platformy Azure i hostów lokalnych, które są częścią tej samej jednostki organizacyjnej. Można skonfigurować szyfrowanie między maszynami wirtualnymi platformy Azure (VM1 i VM2) oraz lokalną usługą host1 tylko dla ruchu HTTP z portem docelowym 8080. Na podstawie Twoich wymagań można utworzyć różne typy zasad protokołu IPsec.

### <a name="working-with-ous"></a>Praca z jednostkami organizacyjnymi 

Zasady zabezpieczeń skojarzone z jednostką organizacyjną są wypychane do komputerów za pośrednictwem obiektu zasad grupy. Kilka zalet korzystania z jednostek organizacyjnych zamiast stosowania zasad do jednego hosta:

* Skojarzenie zasad z jednostką organizacyjną gwarantuje, że komputery należące do tej samej jednostki organizacyjnej uzyskają te same zasady.
* Zmiana zasad zabezpieczeń skojarzonych z jednostką organizacyjną spowoduje zastosowanie zmian do wszystkich hostów w jednostce organizacyjnej.

### <a name="diagrams"></a>Schematów

Na poniższym diagramie przedstawiono połączenie i przypisaną przestrzeń adresów IP. Na maszynach wirtualnych platformy Azure i na hoście lokalnym jest uruchomiony system Windows 2016. Maszyny wirtualne platformy Azure i lokalne usługi host1 są częścią tej samej domeny. Maszyny wirtualne platformy Azure i hosty lokalne mogą rozpoznawać nazwy przy użyciu systemu DNS.

[![1]][1]

Ten diagram przedstawia tunele IPsec do przesyłania w prywatnej komunikacji równorzędnej ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Praca z zasadami protokołu IPsec

W systemie Windows szyfrowanie jest skojarzone z zasadami protokołu IPsec. Zasady IPsec określają, który ruch IP jest zabezpieczony i mechanizm zabezpieczeń stosowany do pakietów IP.
**Zasady protokołu IPSec** składają się z następujących elementów: **list filtrów** , **akcji filtrowania** i **reguł zabezpieczeń**.

Podczas konfigurowania zasad protokołu IPsec ważne jest zapoznanie się z następującą terminologią dotyczącą zasad IPsec:

* **Zasady protokołu IPSec:** Kolekcja reguł. Tylko jedna zasada może być aktywna ("przypisana") w dowolnym konkretnym czasie. Każda zasada może mieć co najmniej jedną regułę, która może być aktywna jednocześnie. W danym momencie można przypisać do komputera tylko jedną aktywną zasadę IPsec. Jednak w ramach zasad protokołu IPsec można zdefiniować wiele akcji, które mogą być wykonywane w różnych sytuacjach. Każdy zestaw reguł IPsec jest skojarzony z listą filtrów, która ma wpływ na typ ruchu sieciowego, do którego ma zastosowanie reguła.

* **Listy filtrów:** Listy filtrów są pakietami jednego lub wielu filtrów. Jedna lista może zawierać wiele filtrów. Filtr określa, czy komunikacja jest dozwolona, zabezpieczona lub zablokowana, zgodnie z zakresami adresów IP, protokołami lub nawet portami określonego protokołu. Każdy filtr pasuje do określonego zestawu warunków; na przykład pakiety wysyłane z określonej podsieci do określonego komputera na określonym porcie docelowym. Gdy warunki sieci są zgodne z co najmniej jednym z tych filtrów, zostanie uaktywniona Lista filtrów. Każdy filtr jest zdefiniowany wewnątrz określonej listy filtrów. Filtry nie mogą być współużytkowane przez listy filtrów. Daną listę filtrów można jednak włączyć do kilku zasad protokołu IPsec. 

* **Akcje filtrowania:** Metoda zabezpieczeń definiuje zestaw algorytmów zabezpieczeń, protokołów i kluczy oferowanych przez komputer podczas negocjacji IKE. Akcje filtrowania to listy metod zabezpieczeń uporządkowane według kolejności preferencji.  Gdy komputer negocjuje sesję IPsec, akceptuje lub wysyła propozycje na podstawie ustawienia zabezpieczeń przechowywanego na liście akcje filtru.

* **Reguły zabezpieczeń:** Zasady określają, jak i kiedy zasady IPsec chronią komunikację. Za pomocą **listy filtrów** i **akcji filtrowania** można utworzyć regułę IPSec w celu utworzenia połączenia IPSec. Każda zasada może mieć co najmniej jedną regułę, która może być aktywna jednocześnie. Każda reguła zawiera listę filtrów IP i zbiór akcji zabezpieczeń, które mają miejsce w przypadku dopasowania z tą listą filtrów:
  * Akcje filtrowania adresów IP
  * Metody uwierzytelniania
  * Ustawienia tunelu IP
  * Typy połączeń

[![5]][5]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że spełniono następujące wymagania wstępne:

* Aby zaimplementować ustawienia zasady grupy, należy mieć działającą Active Directory konfigurację. Aby uzyskać więcej informacji o obiektach zasad grupy, zobacz [zasady grupy obiektów](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Aby uzyskać informacje na temat tworzenia obwodu usługi ExpressRoute, zobacz [Tworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Sprawdź, czy obwód jest włączony przez dostawcę połączenia. 
  * Sprawdź, czy masz skonfigurowaną prywatną komunikację równorzędną Azure dla obwodu. Instrukcje dotyczące routingu można znaleźć w artykule [Konfigurowanie routingu](expressroute-howto-routing-arm.md) . 
  * Upewnij się, że masz utworzoną sieć wirtualną i bramę sieci wirtualnej i została ona w pełni zainicjowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa elementu Gatewaytype "ExpressRoute", a nie sieci VPN.

* Brama sieci wirtualnej ExpressRoute musi być podłączona do obwodu ExpressRoute. Aby uzyskać więcej informacji, zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Sprawdź, czy maszyny wirtualne z systemem Windows Azure są wdrożone w sieci wirtualnej.

* Upewnij się, że istnieje łączność między hostami lokalnymi i maszynami wirtualnymi platformy Azure.

* Sprawdź, czy maszyny wirtualne z systemem Windows Azure i hosty lokalne mogą używać systemu DNS do prawidłowego rozpoznawania nazw.

### <a name="workflow"></a>Przepływ pracy

1. Utwórz obiekt zasad grupy i skojarz go z jednostką organizacyjną.
2. Zdefiniuj **akcję filtru** IPSec.
3. Zdefiniuj **listę filtrów** IPSec.
4. Utwórz zasady protokołu IPsec z **regułami zabezpieczeń**.
5. Przypisz obiekt zasad grupy IPsec do jednostki organizacyjnej.

### <a name="example-values"></a>Przykładowe wartości

* **Nazwa domeny:** ipsectest.com

* **Jednostka organizacyjna:** IPSecOU

* **Lokalny komputer z systemem Windows:** host1

* **Maszyny wirtualne platformy Azure z systemem Windows:** VM1, VM2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Tworzenie obiektu zasad grupy

1. Aby utworzyć nowy obiekt zasad grupy połączony z jednostką organizacyjną, Otwórz przystawkę Zarządzanie zasady grupy i Znajdź jednostkę organizacyjną, do której zostanie połączony obiekt zasad grupy. W przykładzie jednostka organizacyjna nosi nazwę **IPSecOU**. 

   [![9]][9]
2. W przystawce Zarządzanie zasady grupy wybierz jednostkę organizacyjną, a następnie kliknij prawym przyciskiem myszy. Na liście rozwijanej kliknij pozycję " **Utwórz obiekt zasad grupy w tej domenie i umieść tu link...** ".

   [![10]][10]
3. Nadaj nazwę obiektowi zasad grupy intuicyjną nazwę, aby można było go łatwo znaleźć w dalszej części. Kliknij przycisk **OK** , aby utworzyć i połączyć obiekt zasad grupy.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Włącz łącze obiektu zasad grupy

Aby zastosować obiekt zasad grupy do jednostki organizacyjnej, obiekt zasad grupy nie może być połączony tylko z jednostką organizacyjną, ale łącze musi również być włączone.

1. Zlokalizuj utworzony obiekt zasad grupy, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Edytuj** z listy rozwijanej.
2. Aby zastosować obiekt zasad grupy do jednostki organizacyjnej, wybierz pozycję **link włączony**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Zdefiniuj akcję filtrowania adresów IP

1. Z listy rozwijanej kliknij prawym przyciskiem myszy pozycję **zasady zabezpieczeń IP na Active Directory** , a następnie kliknij pozycję **Zarządzaj listami filtrów IP i akcjami filtrowania..**..

   [![15]][15]
2. Na karcie " **Zarządzanie akcjami filtrowania** " kliknij przycisk **Dodaj**.

   [![16]][16]

3. W **Kreatorze akcji filtrowania zabezpieczeń IP** kliknij przycisk **dalej**.

   [![17]][17]
4. Nadaj nazwę filtrowi intuicyjnej nazwy, aby można było go później znaleźć. W tym przykładzie Akcja filtrowania jest nazywana. **myEncryption** Możesz również dodać opis. Następnie kliknij przycisk **dalej**.

   [![18]][18]
5. Wartość **Negocjuj zabezpieczenia** pozwala zdefiniować zachowanie, jeśli nie można nawiązać protokołu IPSec z innym komputerem. Wybierz pozycję **Negocjuj zabezpieczenia** , a następnie kliknij przycisk **dalej**.

   [![19]][19]
6. Na stronie **komunikowanie się z komputerami, które nie obsługują protokołu IPSec** , wybierz opcję nie **Zezwalaj na niezabezpieczoną komunikację** , a następnie kliknij przycisk **dalej**.

   [![20]][20]
7. Na stronie **ruch i zabezpieczenia IP** wybierz pozycję **niestandardowe** , a następnie kliknij pozycję **Ustawienia.**

   [![21]][21]
8. Na stronie **Ustawienia niestandardowej metody zabezpieczeń** wybierz opcję **integralność danych i szyfrowanie (ESP): SHA1, 3DES**. Następnie kliknij przycisk **OK**.

   [![22]][22]
9. Na stronie **Zarządzanie akcjami filtrowania** można zobaczyć, że filtr **odszyfrowania** został pomyślnie dodany. Kliknij przycisk **Zamknij**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Definiowanie listy filtrów IP

Utwórz listę filtrów, która określa zaszyfrowany ruch HTTP z portem docelowym 8080.

1. Aby zakwalifikować, które typy ruchu muszą być szyfrowane, użyj **listy filtrów IP**. Na karcie **Zarządzanie listami filtrów IP** kliknij przycisk **Dodaj** , aby dodać nową listę filtrów adresów IP.

   [![24]][24]
2. W polu **name (nazwa** ) wpisz nazwę listy filtrów adresów IP. Na przykład **Azure-onpremises-HTTP8080**. Następnie kliknij przycisk **Add** (Dodaj).

   [![25]][25]
3. Na stronie **Opis filtrów IP i dublowany Właściwość** wybierz opcję **dublowany**. Ustawienie dublowane dopasowuje pakiety w obu kierunkach, co umożliwia komunikację dwukierunkową. Następnie kliknij przycisk **Dalej**.

   [![26]][26]
4. Na stronie **Źródło ruchu IP** , na liście rozwijanej **adres źródłowy:** wybierz **konkretny adres IP lub podsieć**. 

   [![27]][27]
5. Określ **adres IP lub podsieć** adresu IP, a następnie kliknij przycisk **dalej**.

   [![28]][28]
6. Określ **adres docelowy:** adres IP lub podsieć. Następnie kliknij przycisk **dalej**.

   [![29]][29]
7. Na stronie **Typ protokołu IP** wybierz opcję **TCP**. Następnie kliknij przycisk **dalej**.

   [![30]][30]
8. Na stronie **port protokołu IP** wybierz **z dowolnego portu** i **do tego portu:**. W polu tekstowym wpisz **8080** . Te ustawienia określają tylko ruch HTTP na porcie docelowym 8080 będzie szyfrowany. Następnie kliknij przycisk **dalej**.

   [![31]][31]
9. Wyświetl listę filtrów IP.  Konfiguracja listy filtrów IP **Azure-onpremises-HTTP8080** wyzwala szyfrowanie dla całego ruchu zgodnego z następującymi kryteriami:

   * Dowolny adres źródłowy w 10.0.1.0/24 (Azure podsieci subnet2)
   * Dowolny adres docelowy w 10.2.27.0/25 (lokalna podsieć)
   * Protokół TCP
   * Port docelowy 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Edytowanie listy filtrów IP

Aby zaszyfrować ten sam typ ruchu w odwrotnym kierunku (od hosta lokalnego do maszyny wirtualnej platformy Azure), potrzebny jest drugi filtr IP. Proces konfigurowania nowego filtru to ten sam proces, który został użyty do skonfigurowania pierwszego filtru IP. Jedyne różnice to źródłowa i docelowa podsieć.

1. Aby dodać nowy filtr IP do listy filtrów IP, wybierz pozycję **Edytuj**.

   [![33]][33]
2. Na stronie **Lista filtrów IP** kliknij przycisk **Dodaj**.

   [![34]][34]
3. Utwórz drugi filtr IP przy użyciu ustawień w następującym przykładzie:

   [![35]][35]
4. Po utworzeniu drugiego filtru IP Lista filtrów IP będzie wyglądać następująco:

   [![36]][36]

Jeśli wymagane jest szyfrowanie między lokalizacją lokalną a podsiecią platformy Azure w celu ochrony aplikacji, zamiast modyfikowania istniejącej listy filtrów IP można dodać nową listę filtrów adresów IP. Skojarzenie 2 list filtrów IP z tymi samymi zasadami protokołu IPsec zapewnia lepszą elastyczność, ponieważ w dowolnym momencie można zmodyfikować lub usunąć określoną listę filtrów IP bez wpływu na inne listy filtrów IP.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Tworzenie zasad zabezpieczeń protokołu IPsec 

Utwórz zasady protokołu IPsec z regułami zabezpieczeń.

1. Wybierz **zasady "IPSecurity" w usłudze Active Directory** skojarzonej z JEDNOSTKą organizacyjną. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Utwórz zasady zabezpieczeń IP**.

   [![37]][37]
2. Nazwij zasady zabezpieczeń. Na przykład **zasady-Azure-onpremises**. Następnie kliknij przycisk **dalej**.

   [![38]][38]
3. Kliknij przycisk **dalej** bez zaznaczania pola wyboru.

   [![39]][39]
4. Sprawdź, czy pole wyboru **Edytuj właściwości** jest zaznaczone, a następnie kliknij przycisk **Zakończ**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Edytowanie zasad zabezpieczeń protokołu IPsec

Dodaj do zasad IPsec **listy filtrów IP** i **akcji filtrowania** , które zostały wcześniej skonfigurowane.

1. Na karcie **reguły** właściwości zasad http kliknij przycisk **Dodaj**.

   [![41]][41]
2. Na stronie powitalnej kliknij przycisk **Dalej**.

   [![42]][42]
3. Reguła zawiera opcję definiowania trybu protokołu IPsec: tryb tunelu lub Tryb transportu.

   * W trybie tunelowania oryginalny pakiet jest hermetyzowany przez zestaw nagłówków IP. Tryb tunelowania chroni informacje routingu wewnętrznego przez zaszyfrowanie nagłówka IP oryginalnego pakietu. Tryb tunelowania jest szeroko zaimplementowany między bramami w scenariuszach sieci VPN typu lokacja-lokacja. Tryb tunelowania jest w większości przypadków używany do kompleksowego szyfrowania między hostami.

   * Tryb transportu szyfruje tylko ładunek i przyczepę ESP; Nagłówek IP oryginalnego pakietu nie jest szyfrowany. W trybie transportu Źródło IP i docelowe adresy IP pakietów nie są zmieniane.

   Wybierz opcję **Ta reguła nie określa tunelu** , a następnie kliknij przycisk **dalej**.

   [![43]][43]
4. **Typ sieci** definiuje, które połączenie sieciowe jest skojarzone z zasadami zabezpieczeń. Wybierz pozycję **wszystkie połączenia sieciowe** , a następnie kliknij przycisk **dalej**.

   [![44]][44]
5. Wybierz utworzoną wcześniej listę filtrów IP,  **Azure-onpremises-HTTP8080** , a następnie kliknij przycisk **dalej**.

   [![45]][45]
6. Wybierz istniejącą **wcześniej akcję filtrowania** .

   [![46]][46]
7. System Windows obsługuje cztery różne typy uwierzytelniania: Kerberos, certyfikaty, NTLMv2 i klucz wstępny. Ponieważ pracujemy z hostami przyłączonymi do domeny, wybierz opcję **Active Directory domyślne (protokół Kerberos V5)** , a następnie kliknij przycisk **dalej**.

   [![47]][47]
8. Nowe zasady tworzą regułę zabezpieczeń: **Azure-onpremises-HTTP8080**. Kliknij przycisk **OK**.

   [![48]][48]

Zasady IPsec wymagają, aby wszystkie połączenia HTTP na porcie docelowym 8080 używały trybu transportu IPsec. Ponieważ protokół HTTP jest protokołem tekstu jednotekstowego, przy włączonej zasad zabezpieczeń zapewnia, że dane są szyfrowane, gdy są transferowane za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute. Zasady zabezpieczeń IP dla Active Directory są bardziej skomplikowane do konfigurowania niż Zapora systemu Windows z zabezpieczeniami zaawansowanymi, ale pozwala na dodatkowe dostosowanie połączenia IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Przypisz obiekt zasad grupy IPsec do jednostki organizacyjnej

1. Wyświetl zasady. Zasady grupy zabezpieczeń są zdefiniowane, ale nie zostały jeszcze przypisane.

   [![49]][49]
2. Aby przypisać zasady grupy zabezpieczeń do jednostki organizacyjnej **IPSecOU** , kliknij prawym przyciskiem myszy zasady zabezpieczeń i wybierz pozycję **Przypisz**.
   Wszystkie komputery THT należące do jednostki organizacyjnej będą mieć przypisane zasady grupy zabezpieczeń.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Sprawdź szyfrowanie ruchu

Aby sprawdzić obiekt zasad grupy szyfrowania zastosowany w jednostce organizacyjnej, zainstaluj usługi IIS na wszystkich maszynach wirtualnych platformy Azure i w usłudze host1. Wszystkie usługi IIS są dostosowane do odpowiedzi na żądania HTTP na porcie 8080.
Aby sprawdzić szyfrowanie, można zainstalować szperacz sieciowy (na przykład Wireshark) na wszystkich komputerach w jednostce organizacyjnej.
Skrypt programu PowerShell działa jako klient HTTP do generowania żądań HTTP na porcie 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Poniższe przechwytywanie sieci pokazuje wyniki dla lokalnego programu host1 z filtrem wyświetlania ESP, aby dopasować tylko do zaszyfrowanego ruchu:

[![51]][51]

Po uruchomieniu skryptu programu PowerShell w premisies (klient HTTP) przechwytywanie sieci na maszynie wirtualnej platformy Azure pokazuje podobne śledzenie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "Diagram sieciowy tryb transportu IPSec za poorednictwem ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "interesujący ruch związany z protokołem IPSec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "zasad IPSec systemu Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "jednostka organizacyjna w zasady grupy"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "Tworzenie obiektu zasad grupy skojarzonego z jednostką organizacyjną"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "przypisanie nazwy do obiektu zasad grupy skojarzonego z jednostką organizacyjną"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Edytowanie obiektu zasad grupy"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png. "Zarządzanie listami filtrów IP i akcjami filtrowania"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Akcja dodania filtru"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Kreator akcji" 17
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Nazwa akcji filtrowania"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Akcja filtrowania"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Określ zachowanie jest nawiązywane niezabezpieczone połączenie"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mechanizm zabezpieczeń"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Metoda zabezpieczeń niestandardowych"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lista akcji filtrowania"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Dodaj nową listę filtrów IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Dodawanie ruchu HTTP do filtru IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "pakietów dopasowania w obu kierunkach"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "wybór podsieci źródłowej"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Sieć źródłowa"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Sieć docelowa"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protokół" 30
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "port źródłowy i port docelowy"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Lista filtrów" 32
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "lista filtrów IP 33 z ruchem http"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "dodanie drugiego filtru IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "lista filtrów IP 35 — drugi wpis"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "lista filtrów IP 36 — drugi wpis"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "Tworzenie zasad zabezpieczeń IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "Nazwa zasad protokołu IPSec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png— "Kreator zasad protokołu IPSec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "Edycja zasad IPSec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "dodanie nowej reguły zabezpieczeń do zasad protokołu IPSec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "Tworzenie nowej reguły zabezpieczeń"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Tryb transportu" 43
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Typ sieci" 44
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "wybór istniejącej listy filtrów IP"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "wybór istniejącej akcji filtrowania"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "Wybór metody uwierzytelniania"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "zakończenie procesu tworzenia zasad zabezpieczeń"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "zasady protokołu IPSec połączone z obiektem zasad grupy, ale nie są przypisane"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "zasady protokołu IPSec przypisane do obiektu zasad grupy"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "przechwytywanie ruchu szyfrowanego protokołu IPSec"