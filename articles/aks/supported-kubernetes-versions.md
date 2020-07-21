---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Informacje na temat zasad obsługi wersji Kubernetes i cyklu życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 019ae80020dafb54f2c06dd504797f21069914ae
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507067"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service)

Społeczność Kubernetes zwalnia drobne wersje co trzy miesiące. Te wersje obejmują nowe funkcje i ulepszenia. Wersje poprawek są częstsze (czasami cotygodniowo) i są przeznaczone tylko dla krytycznych poprawek błędów w wersji pomocniczej. Te wersje poprawek obejmują poprawki dotyczące luk w zabezpieczeniach lub poważnych usterek.

## <a name="kubernetes-versions"></a>Wersje Kubernetes

Kubernetes [używa standardowego schematu przechowywania wersji,](https://semver.org/) co oznacza, że każda wersja Kubernetes jest zgodna z tym schematem numeracji:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Każda liczba w wersji wskazuje ogólną zgodność z poprzednią wersją:

* Wersje główne zmieniają się, gdy niezgodne zmiany interfejsu API lub zgodność z poprzednimi wersjami mogą zostać uszkodzone.
* Wersje pomocnicze zmieniają się, gdy wprowadzane są zmiany funkcjonalności, które są wstecznie zgodne z innymi wersjami pomocniczymi.
* Wersje poprawek zmieniają się w przypadku wprowadzenia poprawek błędów zgodnych z poprzednimi wersjami.

Użytkownicy powinni korzystać z najnowszej wersji poprawki wersji pomocniczej, na przykład jeśli klaster produkcyjny jest włączony **`1.17.7`** i **`1.17.8`** jest najnowszą dostępną wersją poprawki dla serii *1,17* , należy przeprowadzić uaktualnienie do programu **`1.17.8`** tak szybko, jak to możliwe, aby upewnić się, że klaster jest w pełni objęty poprawką i jest obsługiwany.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji Kubernetes

AKS definiuje ogólnie dostępną wersję, jako wersję włączoną we wszystkich pomiarach SLO lub SLA oraz kiedy są dostępne we wszystkich regionach. AKS obsługuje trzy drobne wersje pomocnicze Kubernetes:

* Najnowsza wersja pomocnicza systemu, wydawana w AKS (którą będziemy odnosić jako N). 
* Dwie wcześniejsze wersje pomocnicze. 
* Każda obsługiwana wersja pomocnicza obsługuje również maksymalnie dwa (2) stabilne poprawki.
* AKS mogą również obsługiwać wersje w wersji zapoznawczej, które są jawnie oznaczone etykietami i podlegają zapewnieniu warunków [i postanowień][preview-terms].

> [!NOTE]
> AKS używa bezpiecznych praktyk wdrażania, które obejmują stopniowe wdrażanie regionów. Oznacza to, że może upłynąć do 10 dni roboczych, aby nowe wydanie lub Nowa wersja była dostępna we wszystkich regionach.

Obsługiwane okno wersji Kubernetes w AKS jest znane jako "N-2": (N (Najnowsza wersja) — 2 (wersje pomocnicze)).

Na przykład jeśli AKS wprowadza *1.17. a* dzisiaj, pomoc techniczna jest świadczona dla następujących wersji:

Nowa wersja pomocnicza    |    Lista obsługiwanych wersji
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Gdzie ". litera" jest reprezentatywna dla wersji poprawki.

Po wprowadzeniu nowej wersji pomocniczej, najstarsza wersja pomocnicza i wersje poprawek są przestarzałe i usunięte. Na przykład jeśli aktualna lista obsługiwanych wersji to:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

I AKS wersje 1,18. \* oznacza to, że wszystkie 1,15. \* wersje zostaną usunięte i nie będą obsługiwane w ciągu 30 dni.

> [!NOTE]
> Należy pamiętać, że jeśli klienci korzystają z nieobsługiwanej wersji programu Kubernetes, zostanie poproszony o uaktualnienie w przypadku żądania pomocy technicznej dotyczącej klastra. Klastry z nieobsługiwanymi wersjami Kubernetes nie są objęte [zasadami obsługi AKS](./support-policies.md).

Oprócz powyższych AKS obsługuje maksymalnie dwie wersje **poprawek** danej wersji pomocniczej. W związku z tym następujące obsługiwane wersje:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Jeśli AKS wersje `1.17.9` i `1.16.11` , starsze wersje poprawek są przestarzałe i usuwane, a lista obsługiwanych wersji staje się:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

## <a name="release-and-deprecation-process"></a>Proces wydania i wycofania

Możesz odwoływać się do nadchodzących wersji i zaniecheń w [kalendarzu wydania AKS Kubernetes](#aks-kubernetes-release-calendar).

Nowe wersje **pomocnicze** programu Kubernetes
1. AKS publikuje wstępne zawiadomienie o planowanej dacie nowej wersji, a odpowiednia stara wersja jest przestarzała w [informacjach o wersji AKS](https://aka.ms/aks/releasenotes) co najmniej 30 dni przed usunięciem.
2. AKS publikuje [powiadomienie o kondycji usługi](../service-health/service-health-overview.md) dostępne dla wszystkich użytkowników z dostępem do AKS i portalu i wysyła wiadomość e-mail do administratorów subskrypcji z datami usunięcia planowanej wersji.
3. Użytkownicy mają **30 dni** od usunięcia wersji, aby przeprowadzić uaktualnienie do obsługiwanej wersji pomocniczej, aby nadal otrzymywać pomoc techniczną.

Nowe wersje **poprawek** Kubernetes
  * Ze względu na pilną naturę wersji poprawek można je wprowadzić do usługi w miarę ich udostępniania.
  * Ogólnie rzecz biorąc, AKS nie zapewnia szerokiej komunikacji w celu wydania nowych wersji poprawki. Jednak AKS stale monitoruje i sprawdza dostępność dostępnych poprawek w CVE, aby zapewnić ich obsługę w AKS w odpowiednim czasie. Jeśli zostanie znaleziona poprawka krytyczna lub wymagana jest akcja użytkownika, AKS będzie powiadamiać użytkowników o konieczności uaktualnienia do nowo dostępnej poprawki.
  * Użytkownicy mają **30 dni** od momentu usunięcia poprawki z programu AKS, aby uaktualnić do obsługiwanej poprawki i nadal otrzymywać pomoc techniczną.

### <a name="supported-versions-policy-exceptions"></a>Obsługiwane wyjątki zasad wersji

AKS zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji, które zostały zidentyfikowane, aby mieć co najmniej jeden krytyczny wpływ na błędy lub problemy z zabezpieczeniami bez wcześniejszego powiadomienia.

Określone wersje poprawek mogą zostać pominięte lub przyspieszone wdrożenia w zależności od ważności usterki lub problemu z zabezpieczeniami.

## <a name="azure-portal-and-cli-versions"></a>Wersje Azure Portal i interfejsu wiersza polecenia

W przypadku wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster jest domyślnie przystosowany do wersji pomocniczej N-1 i najnowszej poprawki. Na przykład jeśli AKS obsługuje *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e*i *1.15. f*, wybrana wersja domyślna to *1.16. c*.

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji i regionu, użyj polecenia [AZ AKS Get-Versions][az-aks-get-versions] . Poniższy przykład zawiera listę dostępnych wersji Kubernetes dla regionu *wschodniego* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```


## <a name="aks-kubernetes-release-calendar"></a>Kalendarz wydania AKS Kubernetes

Aby uzyskać historię wcześniejszych wersji, zobacz [tutaj](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Wersja K8s | Wersja nadrzędna  | AKS wersja zapoznawcza  | AKS GA  | Koniec okresu istnienia |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Gru-09-19  | 19 stycznia   | 20 lipca  | 1,20 GA | 
| 1,18  | Mar-23-20  | 20 maja   | 20 sierpnia  | 1,21 GA | 
| 1,19  | Sie-04-20  | 20 sierpnia   | 20 list  | 1,22 GA | 
| 1,20  | * Lis 20    | * Gru 20   | * Sty 21  | 1,23 GA | 

\*Oczekiwanie na potwierdzenie daty wydania nadrzędnego.

## <a name="faq"></a>Najczęściej zadawane pytania

**Co się stanie, gdy użytkownik uaktualnia klaster Kubernetes z nieobsługiwaną wersją pomocniczą?**

Jeśli korzystasz z wersji *n-3* lub starszej, oznacza to, że użytkownik jest poza pomocą techniczną i zostanie poproszony o uaktualnienie. Gdy uaktualnienie z wersji n-3 do n-2 powiedzie się, nastąpi powrót do naszych zasad pomocy technicznej. Na przykład:

- Jeśli najstarsza obsługiwana wersja AKS to *1.15. a* i jesteś w wersji *1.14. b* lub starszej, jesteś poza pomocą techniczną.
- Gdy uaktualnienie z wersji *1.14. b* do *1.15. a* lub nowsze, nastąpi powrót do naszych zasad pomocy technicznej.

Obniżenie wersji nie jest obsługiwane.

**Co oznacza "poza pomocą techniczną"**

"Poza działem pomocy technicznej" oznacza, że uruchomiona wersja znajduje się poza listą obsługiwanych wersji, a użytkownik zostanie poproszony o uaktualnienie klastra do obsługiwanej wersji, o ile nie znajduje się w 30-dniowym okresie prolongaty po zakończeniu korzystania z wersji. Ponadto AKS nie wykonuje żadnego środowiska uruchomieniowego ani innych gwarancji dla klastrów poza listą obsługiwanych wersji.

**Co się stanie, gdy użytkownik skaluje klaster Kubernetes z nieobsługiwaną wersją pomocniczą?**

W przypadku wersji pomocniczych nieobsługiwanych przez AKS skalowanie w poziomie lub na zewnątrz powinno być nadal wykonywane, ale nie ma żadnych gwarancji Quality of Service, dlatego zdecydowanie zaleca się uaktualnienie programu w celu powrotu klastra do pomocy technicznej.

**Czy użytkownik może korzystać z wersji Kubernetes bez ograniczeń?**

Jeśli klaster jest nieobsługiwany przez więcej niż trzy (3) wersje pomocnicze i został uznany za zagrożenie bezpieczeństwa, platforma Azure skontaktuje się z administratorem w celu aktywnego uaktualnienia klastra. Jeśli nie podejmujesz dalszych działań, platforma Azure zastrzega sobie prawo do automatycznego uaktualniania klastra w Twoim imieniu.

**Jaka wersja jest obsługiwana przez płaszczyznę kontroli, jeśli Pula węzłów nie znajduje się w jednej z obsługiwanych wersji AKS?**

Płaszczyzna kontroli musi znajdować się w oknie wersji ze wszystkich pul węzłów. Aby uzyskać szczegółowe informacje na temat uaktualniania płaszczyzny kontroli lub pul węzłów, zapoznaj się z dokumentacją dotyczącą [uaktualniania pul węzłów](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Czy mogę pominąć wersję podczas uaktualniania?**

Nie, zgodnie z najlepszymi rozwiązaniami Kubernetes, AKS zezwala tylko na uaktualnienia do natychmiastowej następnej wersji poprawki lub pomocniczej. W Azure Portal będą wyświetlane tylko te wersje, które można uaktualnić do programu, a w interfejsie wiersza polecenia można uruchomić polecenie, `az aks get-upgrades -n MyAKSCluster -g MyResourceGroup` Aby wyświetlić dostępne uaktualnienia z bieżącej wersji.

**Jak można przeprowadzić uaktualnienie do obsługiwanej wersji, jeśli mam wiele wersji za najnowszą obsługiwaną wersją?**

Aby pozostała w ramach pomocy technicznej, należy unikać przeniesieniu ich do wielu wersji z obecnie obsługiwanej listy, ale jeśli w tym przypadku jest to sytuacja, AKS zawsze zezwoli na uaktualnienie do minimalnej obsługiwanej wersji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
