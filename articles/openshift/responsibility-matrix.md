---
title: Azure Red Hat OpenShift przypisania odpowiedzialności
description: Dowiedz się więcej na temat własności obowiązków dotyczących działania Azure Red Hat OpenShift klastra
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, support
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537014"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Omówienie obowiązków Azure Red Hat OpenShift

Ten dokument zawiera opis obowiązków firmy Microsoft, firmy Red Hat i klientów w zakresie Azure Red Hat OpenShift klastrach. Aby uzyskać więcej informacji na Azure Red Hat OpenShift i jego składników, zobacz definicję Azure Red Hat OpenShift Service.

Firmy Microsoft i Red Hat zarządzają usługą Azure Red Hat OpenShift, ale klient ponosi odpowiedzialność za funkcjonalność swojego klastra. Klastry Azure Red Hat OpenShift są hostowane w zasobach platformy Azure w subskrypcjach platformy Azure klienta, ale dostęp do nich jest uzyskiwany zdalnie. Podstawową platformą i zabezpieczeniami danych są firmy Microsoft i Red Hat.

## <a name="overview"></a>Omówienie
<table>
  <tr>
   <td><strong>Zasobów</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Zarządzanie zdarzeniami i operacjami</a></strong>
   </td>
   <td><strong><a href="#change-management">Zarządzanie zmianami</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Zarządzanie tożsamościami i dostępem</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Zgodność z zabezpieczeniami i przepisami</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Dane klienta</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Aplikacje dla klientów</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Usługi dla deweloperów </a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td>Monitorowanie platformy </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Rejestrowanie </td>
   <td>Microsoft i Red Hat </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
  </tr>
  <tr>
   <td>Sieć aplikacji </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Sieć klastrów </td>
   <td>Microsoft i Red Hat </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Sieć wirtualna </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
   <td>Udostępniona </td>
  </tr>
  <tr>
   <td>Węzły płaszczyzny sterowania </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Węzły procesu roboczego </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Wersja klastra </td>
   <td>Microsoft i Red Hat </td>
   <td>Udostępniona </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Zarządzanie pojemnością </td>
   <td>Microsoft i Red Hat </td>
   <td>Udostępniona </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Magazyn wirtualny </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
  <tr>
   <td>Infrastruktura fizyczna i zabezpieczenia </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
   <td>Microsoft i Red Hat </td>
  </tr>
</table>


Tabela 1. Obowiązki według zasobu


## <a name="tasks-for-shared-responsibilities-by-area"></a>Zadania dotyczące wspólnych obowiązków według obszaru 

### <a name="incident-and-operations-management"></a>Zarządzanie zdarzeniami i operacjami 

Klient oraz firma Microsoft i Red Hat współdzielą odpowiedzialność za monitorowanie i konserwację Azure Red Hat OpenShift klastra. Klient jest odpowiedzialny za zarządzanie [](#customer-data-and-applications) zdarzeniami i operacjami danych aplikacji klienta oraz za wszelkie niestandardowe sieci skonfigurowane przez klienta.

<table>
  <tr>
   <td><strong>Zasobów</strong>
   </td>
   <td><strong>Obowiązki firmy Microsoft i firmy Red Hat</strong>
   </td>
   <td><strong>Obowiązki klienta</strong>
   </td>
  </tr>
  <tr>
   <td>Sieć aplikacji </td>
   <td>
<ul>

<li>Monitorowanie usług równoważenia obciążenia w chmurze i natywnej usługi routera OpenShift oraz reagowanie na alerty.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorowanie kondycji punktów końcowych usługi równoważenia obciążenia.

<li>Monitorowanie kondycji tras aplikacji i punktów końcowych, za nimi.

<li>Zgłoś outages to Microsoft and Red Hat.Report outages to Microsoft and Red Hat (Zgłaszaj 300 yjść do firmy Microsoft
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć wirtualna
   </td>
   <td>
<ul>

<li>Monitoruj usługi równoważenia obciążenia w chmurze, podsieci i składniki chmury platformy Azure niezbędne do obsługi sieci domyślnej platformy i reagowanie na alerty.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitoruj ruch sieciowy, który jest opcjonalnie konfigurowany za pośrednictwem połączenia między sieciami wirtualnymi, połączenia sieci VPN lub połączenia sieci Private Link pod uwagę potencjalnych problemów lub zagrożeń bezpieczeństwa.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 2. Wspólna odpowiedzialność za zarządzanie zdarzeniami i operacjami


### <a name="change-management"></a>Zarządzanie zmianami

Firmy Microsoft i Red Hat są odpowiedzialne za włączanie zmian w infrastrukturze klastra i usługach, które klient będzie kontrolować, a także obsługę wersji dostępnych dla węzłów głównych, usług infrastruktury i węzłów procesu roboczego. Klient jest odpowiedzialny za inicjowanie zmian w infrastrukturze oraz instalowanie i utrzymywanie opcjonalnych usług i konfiguracji sieciowych w klastrze, a także za wszystkie zmiany w danych klienta i aplikacjach klientów.


<table>
  <tr>
   <td><strong>Zasobów</strong>
   </td>
   <td><strong>Obowiązki firmy Microsoft i firmy Red Hat</strong>
   </td>
   <td><strong>Obowiązki klienta</strong>
   </td>
  </tr>
  <tr>
   <td>Rejestrowanie </td>
   <td>
<ul>

<li>Centralne agregowanie i monitorowanie dzienników inspekcji platformy.

<li>Udostępnij dokumentację dla klienta, aby umożliwić rejestrowanie aplikacji przy użyciu usługi Log Analytics za pośrednictwem Azure Monitor dla kontenerów.

<li>Podaj dzienniki inspekcji na żądanie klienta.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Zainstaluj opcjonalny domyślny operator rejestrowania aplikacji w klastrze.

<li>Zainstaluj, skonfiguruj i utrzymuj wszelkie opcjonalne rozwiązania do rejestrowania aplikacji, takie jak kontenery rejestrowania sidecar lub aplikacje rejestrowania innych firm.

<li>Dostosuj rozmiar i częstotliwość tworzenia dzienników aplikacji przez aplikacje klienta, jeśli wpływają one na stabilność klastra.

<li>Zażądaj dzienników inspekcji platformy za pośrednictwem zgłoszenia do pomocy technicznej w celu badania określonych zdarzeń.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć aplikacji
   </td>
   <td>
<ul>

<li>Konfigurowanie usług równoważenia obciążenia w chmurze publicznej

<li>Skonfiguruj natywną usługę routera OpenShift. Zapewnij możliwość ustawienia routera jako prywatnego i dodaj do jednego dodatkowego fragmentu routera.

<li>Instalowanie, konfigurowanie i obsługa składników openshift SDN dla domyślnego ruchu zasobników wewnętrznych.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Skonfiguruj inne niż domyślne uprawnienia sieci zasobników dla sieci projektu i zasobników, ruchu wychodzącego zasobników i zasobników przy użyciu obiektów NetworkPolicy.

<li>Zażądaj i skonfiguruj dodatkowe usługi równoważenia obciążenia dla określonych usług.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć klastrów
   </td>
   <td>
<ul>

<li>Skonfiguruj składniki zarządzania klastrem, takie jak publiczne lub prywatne punkty końcowe usługi, oraz niezbędną integrację ze składnikami sieci wirtualnej.

<li>Skonfiguruj wewnętrzne składniki sieciowe wymagane do wewnętrznej komunikacji klastra między procesem roboczym a węzłami głównymi.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Podaj opcjonalne, inne niż domyślne zakresy adresów IP dla maszyny CIDR, CIDR usługi i zasobnika CIDR, jeśli są potrzebne, za pośrednictwem menedżera klastra OpenShift podczas aprowizowania klastra.

<li>Zażądaj, aby punkt końcowy usługi interfejsu API był publiczny lub prywatny podczas tworzenia klastra lub po utworzeniu klastra za pomocą interfejsu wiersza polecenia platformy Azure.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć wirtualna
   </td>
   <td>
<ul>

<li>Skonfiguruj i skonfiguruj składniki sieci wirtualnej wymagane do aprowizowania klastra, w tym wirtualną chmurę prywatną, podsieci, usługi równoważenia obciążenia, bramy internetowe, bramy nat itp.

<li>Zapewnij klientowi możliwość zarządzania łącznością sieci VPN z zasobami lokalnymi, łącznością między sieciami wirtualną i łącznością sieci Private Link zgodnie z wymaganiami za pośrednictwem menedżera klastrów OpenShift.

<li>Umożliwienie klientom tworzenia i wdrażania usług równoważenia obciążenia w chmurze publicznej do użycia z usługami równoważenia obciążenia.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Skonfiguruj i obsługuj opcjonalne składniki sieciowe w chmurze publicznej, takie jak połączenie między sieciami wirtualnmi, połączenie sieci VPN lub Private Link wirtualne.

<li>Zażądaj i skonfiguruj wszelkie dodatkowe usługi równoważenia obciążenia dla określonych usług.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Wersja klastra
   </td>
   <td>
<ul>

<li>Przekaż harmonogram i stan uaktualnień dla wersji pomocniczej i wersji konserwacji

<li>Publikowanie dzienników zmian i informacji o wersji w przypadku drobnych uaktualnień i konserwacji
</li>
</ul>
   </td>
   <td>
<ul>

<li>Inicjowanie uaktualniania klastra

<li>Testowanie aplikacji klienta w wersji pomocniczej i konserwacji w celu zapewnienia zgodności
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Zarządzanie pojemnością
   </td>
   <td>
<ul>

<li>Monitorowanie wykorzystania płaszczyzny sterowania (węzłów głównych)

<li>Skalowanie i/lub zmiana rozmiaru węzłów płaszczyzny sterowania w celu utrzymania jakości usługi

<li>Monitoruj wykorzystanie zasobów klienta, w tym sieci, magazynu i pojemności obliczeniowej. Tam, gdzie funkcje skalowania automatycznego nie są włączone, powiadamiaj klienta o wszelkich zmianach wymaganych do zasobów klastra (np. nowe węzły obliczeniowe do skalowania, dodatkowy magazyn itp.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Użyj podanych kontrolek OpenShift Cluster Manager, aby dodać lub usunąć dodatkowe węzły procesu roboczego zgodnie z potrzebami.

<li>Odpowiadanie na powiadomienia firmy Microsoft i systemu Red Hat dotyczące wymagań dotyczących zasobów klastra.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 3. Wspólna odpowiedzialność za zarządzanie zmianami


### <a name="identity-and-access-management"></a>Zarządzanie tożsamością i dostępem 

Zarządzanie tożsamościami i dostępem obejmuje wszystkie obowiązki w zakresie zapewniania, że tylko odpowiednie osoby mają dostęp do zasobów klastra, aplikacji i infrastruktury. Obejmuje to zadania, takie jak zapewnianie mechanizmów kontroli dostępu, uwierzytelnianie, autoryzacja i zarządzanie dostępem do zasobów.


<table>
  <tr>
   <td><strong>Zasobów</strong>
   </td>
   <td><strong>Obowiązki firmy Microsoft i firmy Red Hat</strong>
   </td>
   <td><strong>Obowiązki klienta</strong>
   </td>
  </tr>
  <tr>
   <td>Rejestrowanie </td>
   <td>
<ul>

<li>Przestrzegaj opartego na standardach branżowych, warstwowego procesu dostępu wewnętrznego dla dzienników inspekcji platformy.

<li>Zapewnianie natywnych możliwości kontroli RBAC natywnej usługi OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Skonfiguruj kontrolę dostępu RBAC na platformie OpenShift, aby kontrolować dostęp do projektów i przez rozszerzenie dzienników aplikacji projektu.

<li>W przypadku rozwiązań rejestrowania aplikacji innych firm lub aplikacji niestandardowych klient jest odpowiedzialny za zarządzanie dostępem.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć aplikacji
   </td>
   <td>
<ul>

<li>Zapewnianie natywnych możliwości kontroli RBAC natywnej usługi OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Skonfiguruj kontrolę dostępu RBAC na potrzeby usługi OpenShift, aby kontrolować dostęp do konfiguracji trasy zgodnie z potrzebami.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć klastrów
   </td>
   <td>
<ul>

<li>Zapewnianie natywnych możliwości kontroli RBAC natywnej usługi OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Zarządzanie członkostwem w organizacji Red Hat kont Red Hat.

<li>Manage Org Admins for Red Hat organization to grant access to OpenShift Cluster Manager.

<li>Skonfiguruj kontrolę dostępu RBAC na potrzeby usługi OpenShift, aby kontrolować dostęp do konfiguracji trasy zgodnie z potrzebami.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć wirtualna
   </td>
   <td>
<ul>

<li>Zapewnianie kontroli dostępu klientów za pośrednictwem usługi OpenShift Cluster Manager.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Zarządzanie opcjonalnym dostępem użytkowników do składników chmury publicznej za pomocą usługi OpenShift Cluster Manager.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 4. Wspólna odpowiedzialność za zarządzanie tożsamościami i dostępem


### <a name="security-and-regulation-compliance"></a>Zgodność z zabezpieczeniami i przepisami 

Zabezpieczenia i zgodność obejmują wszelkie obowiązki i mechanizmy kontroli, które zapewniają zgodność z odpowiednimi przepisami, zasadami i przepisami.


<table>
  <tr>
   <td><strong>Zasobów</strong>
   </td>
   <td><strong>Obowiązki firmy Microsoft i firmy Red Hat</strong>
   </td>
   <td><strong>Obowiązki klienta</strong>
   </td>
  </tr>
  <tr>
   <td>Rejestrowanie </td>
   <td>
<ul>

<li>Wysyłanie dzienników inspekcji klastra do rozwiązania Microsoft i Red Hat SIEM w celu analizowania zdarzeń zabezpieczeń. Zachowaj dzienniki inspekcji przez zdefiniowany okres, aby obsługiwać analizę śledczych.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analizowanie dzienników aplikacji pod celu analizy zdarzeń zabezpieczeń. Wysyłanie dzienników aplikacji do zewnętrznego punktu końcowego za pośrednictwem kontenerów sidecar rejestrowania lub aplikacji rejestrowania innych firm, jeśli jest wymagane dłuższe przechowywanie niż jest oferowane przez domyślny stos rejestrowania.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sieć wirtualna
   </td>
   <td>
<ul>

<li>Monitoruj składniki sieci wirtualnej pod adresem potencjalnych problemów i zagrożeń bezpieczeństwa.

<li>Użyj dodatkowych publicznych narzędzi microsoft i red hat platformy Azure w celu dodatkowego monitorowania i ochrony.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitoruj opcjonalnie skonfigurowane składniki sieci wirtualnej pod adresem potencjalnych problemów i zagrożeń bezpieczeństwa.

<li>Skonfiguruj wszelkie niezbędne reguły zapory lub ochronę centrum danych zgodnie z potrzebami.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 5. Wspólna odpowiedzialność za bezpieczeństwo i zgodność z przepisami


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Obowiązki klienta podczas korzystania z Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Dane i aplikacje klienta

Klient jest odpowiedzialny za aplikacje, obciążenia i dane, które wdraża w Azure Red Hat OpenShift. Firmy Microsoft i Red Hat oferują jednak różne narzędzia, które ułatwiają klientom zarządzanie danymi i aplikacjami na platformie.


<table>
  <tr>
   <td><strong>Zasobów</strong>
   </td>
   <td><strong>Jak pomagają firmy Microsoft i Red Hat</strong>
   </td>
   <td><strong>Obowiązki klienta</strong>
   </td>
  </tr>
  <tr>
   <td>Dane klienta </td>
   <td>
<ul>

<li>Utrzymuj standardy na poziomie platformy dotyczące szyfrowania danych zdefiniowane przez branżowe standardy zabezpieczeń i zgodności. 

<li>Udostępnij składniki openshift, które ułatwiają zarządzanie danymi aplikacji, takimi jak wpisy tajne.

<li>Włącz integrację z usługami danych innych firm (takimi jak Azure SQL), aby przechowywać dane i zarządzać nimi poza klastrem i/lub firmą Microsoft i Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Zachowaj odpowiedzialność za wszystkie dane klientów przechowywane na platformie oraz sposób, w jaki aplikacje klienta zużywają i uwidoczniają te dane.

<li>Szyfrowanie Etcd
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Aplikacje dla klientów
   </td>
   <td>
<ul>

<li>Aprowizowanie klastrów z zainstalowanymi składnikami OpenShift, dzięki czemu klienci mogą uzyskać dostęp do interfejsów API OpenShift i Kubernetes w celu wdrażania aplikacji konteneryzowanych i zarządzania nimi.

<li>Zapewnianie dostępu do interfejsów API OpenShift, których klient może użyć do skonfigurowania operatorów w celu dodania usług społeczności, innych firm, Microsoft i Red Hat oraz Red Hat do klastra. 

<li>Udostępnij klasy magazynu i wtyczki do obsługi trwałych woluminów do użytku z aplikacjami klienta.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Zachowaj odpowiedzialność za aplikacje klienta i inne firmy, dane oraz ich pełny cykl życia.

<li>Jeśli klient dodaje do klastra usług Red Hat, Community, Third Party, własne lub inne usługi za pomocą operatorów lub obrazów zewnętrznych, odpowiada za te usługi i współpracę z odpowiednim dostawcą (w tym Red Hat) w celu rozwiązania wszelkich problemów.

<li>Użyj dostarczonych narzędzi i funkcji do <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">konfigurowania i wdrażania programu</a>; <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">bądź na bieżąco;</a> <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">skonfiguruj żądania zasobów i limity;</a> <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">rozmiar klastra, aby mieć wystarczającą ilość zasobów do uruchamiania aplikacji;</a> <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">konfigurowanie uprawnień;</a> integracja z innymi usługami; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">zarządzać strumieniami obrazów lub szablonami wdrażanych przez klienta;</a> <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">zewnętrznie obsługiwać ;</a> zapisywanie, kopii zapasowej i przywracanie danych; i w inny sposób zarządzać obciążeniami o wysokiej dostępnej i odpornej na błędy.

<li>Zachowanie odpowiedzialności za monitorowanie aplikacji uruchamianych na Azure Red Hat OpenShift; w tym instalowania i obsługi oprogramowania w celu zbierania metryk i tworzenia alertów.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 7. Obowiązki klienta dotyczące danych klienta, aplikacji i usług klienta
