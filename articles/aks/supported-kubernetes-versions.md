---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Informacje na temat zasad obsługi wersji Kubernetes i cyklu życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593448"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service)

Społeczność Kubernetes zwalnia drobne wersje co trzy miesiące. Te wersje obejmują nowe funkcje i ulepszenia. Wersje poprawek są częstsze (czasami cotygodniowo) i są przeznaczone tylko dla krytycznych poprawek błędów w wersji pomocniczej. Te wersje poprawek obejmują poprawki dotyczące luk w zabezpieczeniach lub poważnych usterek, które mają wpływ na dużą liczbę klientów i produktów działających w środowisku produkcyjnym w oparciu o Kubernetes.

AKS mają na celu certyfikowanie i wydanie nowych wersji Kubernetes w ciągu 30 dni od wydania nadrzędnego, z zastrzeżeniem stabilności wersji.

## <a name="kubernetes-versions"></a>Wersje Kubernetes

Kubernetes używa standardowego schematu obsługi wersji [semantycznej](https://semver.org/) . Oznacza to, że każda wersja programu Kubernetes jest zgodna z tym schematem numeracji:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Każda liczba w wersji wskazuje ogólną zgodność z poprzednią wersją:

* Wersje główne zmieniają się, gdy niezgodne zmiany interfejsu API lub zgodność z poprzednimi wersjami mogą zostać uszkodzone.
* Wersje pomocnicze zmieniają się, gdy wprowadzane są zmiany funkcjonalności, które są wstecznie zgodne z innymi wersjami pomocniczymi.
* Wersje poprawek zmieniają się w przypadku wprowadzenia poprawek błędów zgodnych z poprzednimi wersjami.

Użytkownicy powinni korzystać z najnowszej wersji poprawki wersji pomocniczej, na przykład jeśli klaster produkcyjny jest w systemie *1.12.14* , a *1.12.15* to najnowsza dostępna wersja poprawki dostępna dla serii *1,12* , należy przeprowadzić uaktualnienie do *1.12.15* , gdy tylko zaistnieje możliwość zapewnienia, że klaster jest w pełni objęty poprawką i jest obsługiwany.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji Kubernetes

AKS obsługuje trzy drobne wersje Kubernetes:

* Bieżąca wersja pomocnicza wydana w AKS (N)
* Dwie wcześniejsze wersje pomocnicze. Każda obsługiwana wersja pomocnicza obsługuje również dwie stabilne poprawki.

Jest to tzw. "N-2": (N (Najnowsza wersja) — 2 (wersje pomocnicze)).

Na przykład jeśli AKS wprowadza *1.15. a* dzisiaj, pomoc techniczna jest świadczona dla następujących wersji:

Nowa wersja pomocnicza    |    Lista obsługiwanych wersji
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Gdzie ". litera" jest reprezentatywna dla wersji poprawki.

Aby uzyskać szczegółowe informacje na temat komunikacji dotyczącej zmian wersji i oczekiwań, zobacz "komunikacja" poniżej.

Po wprowadzeniu nowej wersji pomocniczej, najstarsza wersja pomocnicza i wersje poprawek są przestarzałe i usunięte. Na przykład jeśli aktualna lista obsługiwanych wersji to:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

I AKS wersje 1,16. *oznacza to, że 1,13.* wersje (wszystkie wersje 1,13) zostaną usunięte i nie będą obsługiwane.

> [!NOTE]
> Należy pamiętać, że jeśli klienci korzystają z nieobsługiwanej wersji programu Kubernetes, zostanie poproszony o uaktualnienie w przypadku żądania pomocy technicznej dotyczącej klastra. Klastry z nieobsługiwanymi wersjami Kubernetes nie są objęte [zasadami obsługi AKS](https://docs.microsoft.com/azure/aks/support-policies).

Oprócz powyższych w wersjach pomocniczych program AKS obsługuje dwie najnowsze wersje **poprawek** danej wersji pomocniczej. Na przykład, mając następujące obsługiwane wersje:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Jeśli Kubernetes 1.15.3 i 1.14.6 i AKS wystawia te wersje, najstarsze wersje poprawek są przestarzałe i usunięte, a lista obsługiwanych wersji staje się:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Komunikacja

* Nowe wersje **pomocnicze** programu Kubernetes
  * Wszyscy użytkownicy są powiadamiani publicznie o nowej wersji i o wersji, która zostanie usunięta.
  * Po wydaniu nowej wersji poprawki najstarsza wersja poprawki jest usuwana w tym samym czasie.
  * Klienci mają **30 dni** od publicznej daty powiadomienia, aby przeprowadzić uaktualnienie do obsługiwanej wersji pomocniczej.
* Nowe wersje **poprawek** Kubernetes
  * Wszyscy użytkownicy otrzymają powiadomienie o wydaniu nowej wersji poprawki i przeprowadź uaktualnienie do najnowszej wersji poprawki.
  * Użytkownicy mają **30 dni** , aby przeprowadzić uaktualnienie do nowszej obsługiwanej wersji poprawki przed usunięciem najstarszego.

AKS definiuje "wydaną wersję" jako ogólnie dostępne wersje, włączone we wszystkich pomiarach SLO/Quality of Service i dostępne we wszystkich regionach. AKS mogą również obsługiwać wersje w wersji zapoznawczej, które są jawnie oznaczone etykietami i podlegają zapewnieniu warunków i postanowień.

#### <a name="notification-channels-for-aks-changes"></a>Kanały powiadomień dla zmian AKS

AKS publikuje regularne aktualizacje usługi, które podsumowują nowe wersje Kubernetes, zmiany usług i aktualizacje składników, które zostały wydane w usłudze w serwisie [GitHub](https://github.com/Azure/AKS/releases).

Te zmiany są przerzucane wszystkim klientom w ramach regularnej konserwacji, która jest oferowana w ramach usługi zarządzanej, a niektóre wymagają jawnych uaktualnień, podczas gdy inne nie wymagają żadnej akcji.

Powiadomienia są również wysyłane za pośrednictwem:

* [Informacje o wersji AKS](https://aka.ms/aks/releasenotes)
* Powiadomienia w witrynie Azure Portal
* [Kanał aktualizacji platformy Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Obsługiwane wyjątki zasad wersji

AKS zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji, które zostały zidentyfikowane, aby mieć co najmniej jeden krytyczny wpływ na błędy lub problemy z zabezpieczeniami bez wcześniejszego powiadomienia.

Określone wersje poprawek mogą zostać pominięte lub przyspieszone wdrożenia w zależności od ważności usterki lub problemu z zabezpieczeniami.

### <a name="azure-portal-and-cli-default-versions"></a>Azure Portal i domyślne wersje interfejsu wiersza polecenia

W przypadku wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster jest domyślnie przystosowany do wersji pomocniczej N-1 i najnowszej poprawki. Na przykład jeśli AKS obsługuje *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*i *1.13. f*, wybrana wersja domyślna to *1.14. c*.

AKS wybiera domyślną wartość N-1, aby zapewnić klientom domyślnie znaną, stabilną i zaznaczoną dla wersji.

## <a name="list-currently-supported-versions"></a>Wyświetl listę obecnie obsługiwanych wersji

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji i regionu, użyj polecenia [AZ AKS Get-Versions][az-aks-get-versions] . Poniższy przykład zawiera listę dostępnych wersji Kubernetes dla regionu *wschodniego* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Często zadawane pytania

**Co się stanie, gdy klient uaktualnia klaster Kubernetes z nieobsługiwaną wersją pomocniczą?**

Jeśli korzystasz z wersji *n-3* , jesteś poza pomocą techniczną i zostanie wyświetlony monit o uaktualnienie. Jeśli uaktualnienie z wersji n-3 do n-2 powiedzie się, jesteś teraz w naszych zasadach pomocy technicznej. Przykład:

- Jeśli najstarsza obsługiwana wersja AKS to *1.13. a* i jesteś w wersji *1.12. b* lub starszej, jesteś poza pomocą techniczną.
- Jeśli uaktualnienie z programu *1.12. b* do *1.13. a* lub nowsze powiedzie się, nastąpi powrót do naszych zasad pomocy technicznej.

Uaktualnienia do wersji starszych niż obsługiwane okno *N-2* nie są obsługiwane. W takich przypadkach zalecamy utworzenie nowych klastrów AKS i ponowne wdrożenie ich obciążeń z wersjami w obsługiwanym oknie.

**Co oznacza "poza pomocą techniczną"**

"Poza obsługą" oznacza, że uruchomiona wersja znajduje się poza listą obsługiwanych wersji, a podczas żądania pomocy technicznej zostanie wyświetlony monit o uaktualnienie klastra do obsługiwanej wersji. Ponadto AKS nie wykonuje żadnego środowiska uruchomieniowego ani innych gwarancji dla klastrów spoza listy obsługiwanych wersji.

**Co się stanie, gdy klient skaluje klaster Kubernetes z wersją pomocniczą, która nie jest obsługiwana?**

W przypadku wersji pomocniczych nieobsługiwanych przez AKS skalowanie w poziomie lub na zewnątrz powinno być nadal wykonywane, ale zdecydowanie zaleca się uaktualnienie, aby zapewnić obsługę klastra z powrotem.

**Czy klient może nadal korzystać z wersji Kubernetes?**

Tak. Jeśli jednak klaster nie znajduje się w jednej z wersji obsługiwanej przez AKS, klaster jest spoza zasad obsługi AKS. Platforma Azure nie uaktualnia automatycznie klastra ani nie usuwa go.

**Jaka wersja jest obsługiwana przez płaszczyznę kontroli, jeśli Pula węzłów nie znajduje się w jednej z obsługiwanych wersji AKS?**

Płaszczyzna kontroli musi znajdować się w oknie wersji ze wszystkich pul węzłów. Aby uzyskać szczegółowe informacje na temat uaktualniania płaszczyzny kontroli lub pul węzłów, zapoznaj się z dokumentacją dotyczącą [uaktualniania pul węzłów](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
