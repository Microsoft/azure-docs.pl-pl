---
title: Informacje o siatkach usług
description: Uzyskaj przegląd siatek usług, ich architektury i możliwości oraz kryteriów, które należy wziąć pod uwagę podczas wybierania jednego do wdrożenia.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77594315"
---
# <a name="about-service-meshes"></a>Informacje o siatkach usług

Siatka usług zapewnia takie możliwości jak zarządzanie ruchem, odporność, zasady, zabezpieczenia, silna tożsamość i przestrzeganie obciążeń. Aplikacja jest oddzielona od tych możliwości operacyjnych, a siatka usług przenosi je z warstwy aplikacji, a w dół do warstwy infrastruktury.

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, które mogą być włączone dla obciążeń podczas korzystania z sieci usługi:

- **Szyfruj cały ruch w klastrze** — Włącz wzajemną TLS między określonymi usługami w klastrze. Można to rozszerzyć na ruch przychodzący i wychodzący w sieci obwodowej. Zapewnia bezpieczną opcję zabezpieczeń, bez konieczności wprowadzania zmian w kodzie i infrastrukturze aplikacji.

- **Fazy Kanaryjskie i etapy etapowe** — Określ warunki dla podzestawu ruchu, który ma być kierowany do zestawu nowych usług w klastrze. Po pomyślnym przeprowadzeniu testu w wersji Kanaryjskich należy usunąć pozycję kierowanie i stopniowe zwiększanie% całego ruchu do nowej usługi. Ostatecznie cały ruch będzie kierowany do nowej usługi.

- **Zarządzanie ruchem i manipulowanie** — Tworzenie zasad w usłudze, które będą ograniczać ruch do wersji usługi z określonego źródła. Lub zasad, które stosują strategię ponawiania, do klas błędów między określonymi usługami. Duplikowanie ruchu na żywo do nowych wersji usług podczas migracji lub debugowania problemów. Wsuń błędy między usługami w środowisku testowym w celu przetestowania odporności.

-  Wgląd w szczegółowe informacje na temat sposobu połączenia usług z ruchem między nimi. Uzyskiwanie metryk, dzienników i śladów dla całego ruchu w klastrze oraz danych przychodzących/wychodzących. Dodawanie rozproszonych możliwości śledzenia do aplikacji.

## <a name="architecture"></a>Architektura

Siatka usług zwykle składa się z płaszczyzny kontroli i płaszczyzny danych.

**Płaszczyzna kontroli** zawiera wiele składników, które obsługują zarządzanie siatką usług. Zwykle obejmuje to interfejs zarządzania, który może być interfejsem użytkownika lub interfejsem API. Zwykle są również składniki zarządzające regułami i definicjami zasad, które definiują sposób implementacji przez siatkę usług określonych funkcji. Istnieją także składniki zarządzające aspektami zabezpieczeń, takimi jak mocna tożsamość i certyfikaty dla mTLS. Siatki usług również zwykle mają metryki lub składniki do zaobserwowania, które zbierają i agregują metryki i dane telemetryczne z obciążeń.

**Płaszczyzna danych** zwykle składa się z serwera proxy, który jest w sposób niewidoczny w sposób przezroczysty jako Przyczepka do obciążeń. Ten serwer proxy jest skonfigurowany tak, aby kontrolować cały ruch sieciowy do i z obszaru, w którym znajdują się obciążenia. Dzięki temu serwer proxy ma być skonfigurowany do zabezpieczenia ruchu przez mTLS, dynamicznego kierowania ruchu, stosowania zasad do ruchu sieciowego oraz zbierania metryk i informacji śledzenia. 

![Typowa architektura sieci usługi](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Możliwości

Każda z siatek usług ma naturalną dopasowanie i koncentruje się na obsłudze określonych scenariuszy, ale zazwyczaj okaże się, że większość będzie implementować kilka, jeśli nie wszystkie, z następujących możliwości.

### <a name="traffic-management"></a>Zarządzanie ruchem 

- **Protokół** — warstwa 7 (http, GRPC)
- **Routing dynamiczny** — warunkowe, ważenie, dublowanie
- **Odporność** — przekroczenia limitu czasu, ponawianie prób, wyłączniki
- **Zasady** — kontrola dostępu, limity szybkości, przydziały
- **Testowanie** — iniekcja błędów

### <a name="security"></a>Zabezpieczenia

- **Szyfrowanie** — mTLS, zarządzanie certyfikatami, zewnętrzny urząd certyfikacji
- **Silna tożsamość** — SPIFFE lub podobne
- **AUTH** — uwierzytelnianie, autoryzacja

### <a name="observability"></a>Wgląd w informacje

- **Metryki** — metryki złota, Prometheus, grafana
- **Śledzenie** — ślady w ramach obciążeń
- Ruch — klaster, transfery przychodzące/ **wychodzące**

### <a name="mesh"></a>Siatka

- **Obsługiwane obliczenia** — Kubernetes, maszyny wirtualne
- Bramy **wieloklastrowe** , Federacja federacyjna

## <a name="selection-criteria"></a>Kryteria wyboru

Przed wybraniem siatki usługi upewnij się, że rozumiesz wymagania i przyczynę instalacji siatki usług. Spróbuj zadać następujące pytania.

- **Czy kontroler transferu danych przychodzących jest wystarczający dla moich potrzeb?** — Czasami posiadanie możliwości, takich jak/b testowanie lub podział ruchu na ruch przychodzący, jest wystarczające do obsługi wymaganego scenariusza. Nie dodawaj złożoności do środowiska bez żadnych samych siebie.

- **Czy moje obciążenia i środowisko mogą tolerować dodatkowe nadrzuty?** — Wszystkie dodatkowe składniki wymagane do obsługi siatki usług wymagają dodatkowych zasobów, takich jak procesor CPU i pamięć. Ponadto wszystkie serwery proxy i powiązane z nimi zasady sprawdzają opóźnienie dla ruchu. W przypadku obciążeń, które są bardzo ważne dla opóźnień lub nie można udostępnić dodatkowych zasobów, aby pokryć składniki sieci usługi, a następnie ponownie rozważyć.

- **Czy to zwiększa niepotrzebną złożoność?** — Jeśli powodem instalacji siatki usługi jest uzyskanie możliwości, która nie musi być istotna dla zespołów firmy lub operacyjnych, należy rozważyć, czy dodatkowa złożoność instalacji, konserwacji i konfiguracji jest taka sama.

- **Czy można to przyjąć przy użyciu podejścia przyrostowego?** — Niektóre sieci dotyczące usługi, które zapewniają wiele możliwości, można przyjąć bardziej przyrostowe podejście. Zainstaluj tylko składniki potrzebne do zapewnienia sukcesu. Gdy jesteś bardziej pewny i potrzebujesz dodatkowych możliwości, zapoznaj się z nimi. Odporne na zainstalowanie *wszystkiego* od samego początku.

Jeśli po starannym uwzględnieniu należy wybrać siatkę usługi, aby zapewnić wymagane możliwości, a następnie następną decyzję o *sieci usług?*

Należy wziąć pod uwagę następujące obszary i, które z nich są najbardziej dostosowane do Twoich wymagań. Przeprowadzisz Cię do najlepszego dopasowania do środowiska i obciążeń. Sekcja [następne kroki](#next-steps) przeprowadzi Cię do dalszych szczegółowych informacji o określonych oczkach usługi i sposobach ich mapowania do tych obszarów.

- Zarządzanie ruchem **technicznym** , zasady, zabezpieczenia, przestrzeganie

- **Business** — komercyjne wsparcie, fundament (CNCF), licencja OSS, zarządzanie

- **Operacyjna** — instalacja/uaktualnienia, wymagania dotyczące zasobów, wymagania dotyczące wydajności, integracje (metryki, dane telemetryczne, pulpity nawigacyjne, narzędzia, SMI), obciążenia mieszane (pule węzłów systemu Linux i Windows), obliczeniowe (Kubernetes, maszyny wirtualne), wiele klastrów

- **Zabezpieczenia** — uwierzytelnianie, tożsamość, zarządzanie certyfikatami i rotacja, podłączany zewnętrzny urząd certyfikacji


## <a name="next-steps"></a>Następne kroki

Poniższa dokumentacja zawiera więcej informacji o siatkach usług, które można wypróbować w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Istio...][istio-about]

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat konsolidatora...][linkerd-about]

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Consul...][consul-about]

Warto również zapoznać się z interfejsem usługi Service siatk (SMI), standardowym interfejsem dla siatek usług w Kubernetes:

- [Interfejs sieci usługi (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md