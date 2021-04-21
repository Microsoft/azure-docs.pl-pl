---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Informacje o zasadach obsługi wersji rozwiązania Kubernetes i cyklu życia klastrów w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 43b0304ec6ac4266c3727990bf013c3dcfe523a3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750252"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service)

Społeczność kubernetes wydaje wersje pomocnicze mniej więcej co trzy miesiące. Niedawno społeczność kubernetes zwiększyła okno pomocy technicznej dla każdej wersji z 9 do [12](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)miesięcy, począwszy od wersji 1.19. 

Wersje pomocnicze zawierają nowe funkcje i ulepszenia. Wersje poprawek są częstsze (czasami co tydzień) i są przeznaczone do krytycznych poprawek błędów w wersji pomocniczej. Wersje poprawek zawierają poprawki dotyczące luk w zabezpieczeniach lub głównych usterek.

## <a name="kubernetes-versions"></a>Wersje kubernetes

W przypadku każdej wersji na platformie Kubernetes jest używany standardowy schemat [wersji](https://semver.org/) semantycznych:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Każdy numer w wersji wskazuje ogólną zgodność z poprzednią wersją:

* **Wersje główne zmieniają** się, gdy niezgodne aktualizacje interfejsu API lub zgodność z poprzednimi wersjami mogą zostać uszkodzone.
* **Wersje pomocnicze zmieniają** się w przypadku aktualizacji funkcjonalności, które są wstecznie zgodne z innymi wersjami pomocniczymi.
* **Wersje poprawek** zmieniają się po wprowadzono poprawki błędów zgodne z poprzednimi wersjami.

Staraj się uruchomić najnowszą wersję poprawkową wersji pomocniczej, która jest uruchomiona. Na przykład klaster produkcyjny znajduje się **`1.17.7`** w . **`1.17.8`** to najnowsza dostępna wersja poprawki dostępna dla *serii 1.17.* Należy uaktualnić klaster do programu tak szybko, jak to możliwe, aby upewnić się, że **`1.17.8`** klaster jest w pełni poprawiony i obsługiwany.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji kubernetes

Usługę AKS definiuje ogólnie dostępną wersję jako wersję włączoną we wszystkich pomiarach poziomu usług lub umowy SLA i dostępną we wszystkich regionach. AKS obsługuje trzy pomocnicze wersje gachodniej wersji kubernetes:

* Najnowsza wersja pomocnicza, która jest wydana w UKS (którą będziemy oznaczać N).
* Dwie poprzednie wersje pomocnicze.
    * Każda obsługiwana wersja pomocnicza obsługuje również maksymalnie dwie (2) stabilne poprawki.

AKS może również obsługiwać wersje zapoznawcze, które są jawnie oznaczone i podlegają warunkami [i postanowieniami wersji zapoznawczej.][preview-terms]

> [!NOTE]
> W u usługach AKS są używane rozwiązania bezpiecznego wdrażania, które obejmują stopniowe wdrażanie w regionie. Oznacza to, że może upłynieć do 10 dni roboczych, aż nowa wersja lub nowa wersja będzie dostępna we wszystkich regionach.

Okno obsługiwanych wersji rozwiązania Kubernetes w UKS jest nazywane "N-2": (N (najnowsza wersja) — 2 (wersje pomocnicze)).

Jeśli na przykład obecnie w u usługach AKS wprowadzono wersję *1.17.a,* pomoc techniczna jest zapewniana dla następujących wersji:

Nowa wersja pomocnicza    |    Lista obsługiwanych wersji
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Gdzie ".letter" jest reprezentatywne dla wersji poprawek.

Po wprowadzeniu nowej wersji pomocniczej najstarsza obsługiwana wersja pomocnicza i wersje poprawek są przestarzałe i usuwane. Na przykład bieżąca lista obsługiwanych wersji to:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS wydaje wersję 1.18. , usuwając wszystkie \* wersje 1.15. z obsługi \* technicznej w ciągu 30 dni.

> [!NOTE]
> Jeśli klienci mają nieobsługiwaną wersję usługi Kubernetes, zostaną proszeni o uaktualnienie podczas żądania obsługi klastra. Klastry z nieobsługiwanymi wersjami usługi Kubernetes nie są objęte zasadami obsługi [usługi AKS.](./support-policies.md)

Oprócz powyższych, program AKS obsługuje maksymalnie **dwie** wersje poprawek danej wersji pomocniczej. W związku z tym, biorąc pod uwagę następujące obsługiwane wersje:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Jeśli aKS wyda wersje i , najstarsze wersje poprawek zostaną wycofane i `1.17.9` `1.16.11` usunięte, a lista obsługiwanych wersji stanie się:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Obsługiwane `kubectl` wersje

Możesz użyć jednej wersji pomocniczej starszej lub nowszej względem wersji `kubectl` *kube-apiserver,* zgodnie z zasadami obsługi platformy [Kubernetes dla kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Jeśli na przykład twój *serwer kube-apiserver* ma wersję *1.17,* możesz użyć wersji *od 1.16* do *1.18* tego serwera `kubectl` *kube-apiserver.*

Aby zainstalować lub zaktualizować wersję `kubectl` programu , uruchom . `az aks install-cli`

## <a name="release-and-deprecation-process"></a>Proces wydania i cofania przetwarzania

Możesz odwołać się do kolejnych wersji i wersji, które mają zostać cofania, w kalendarzu wydania [kubernetes aks.](#aks-kubernetes-release-calendar)

W przypadku **nowych wersji** pomocniczych kubernetes:
  * W informacjach o wersji usługi AKS co najmniej 30 dni przed usunięciem usługi [AKS](https://aka.ms/aks/releasenotes) jest publikowane wstępne ogłoszenie z zaplanowaną datą nowej wersji i odpowiednim terminem wycofania starej wersji.
  * Usługa AKS [używa Azure Advisor,](https://docs.microsoft.com/azure/advisor/advisor-overview) aby powiadamiać użytkowników, jeśli nowa wersja spowoduje problemy w klastrze z powodu przestarzałych interfejsów API. Azure Advisor jest również używana do powiadamiania użytkownika, jeśli obecnie nie ma pomocy technicznej.
  * Usługa AKS [](../service-health/service-health-overview.md) publikuje powiadomienie o kondycji usługi dostępne dla wszystkich użytkowników z dostępem do usługi AKS i portalu, a następnie wysyła wiadomość e-mail do administratorów subskrypcji z datami planowanego usunięcia wersji.

    > [!NOTE]
    > Aby dowiedzieć się, kto jest administratorem subskrypcji, lub aby go zmienić, zapoznaj się z [tematem Zarządzanie subskrypcjami platformy Azure.](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)
    
  * Użytkownicy mają **30 dni od** usunięcia wersji do uaktualnienia do obsługiwanej wersji pomocniczej, aby nadal otrzymywać pomoc techniczną.

W przypadku **nowych wersji** poprawek kubernetes:
  * Ze względu na pilny charakter wersji poprawek można je wprowadzać do usługi, gdy tylko staną się dostępne.
  * Ogólnie rzecz biorąc, aKS nie informuje ogólnie o wydaniu nowych wersji poprawek. Jednak stale monitoruje i weryfikuje dostępne poprawki CVE w celu ich obsługi w UKS w terminowy sposób. Jeśli zostanie znaleziona poprawka krytyczna lub wymagana jest akcja użytkownika, usługę AKS powiadomi użytkowników o uaktualnieniu do nowo dostępnej poprawki.
  * Użytkownicy mają **30 dni** od usunięcia wydania poprawki z aks, aby uaktualnić ją do obsługiwanej poprawki i nadal otrzymywać pomoc techniczną.

### <a name="supported-versions-policy-exceptions"></a>Obsługiwane wersje wyjątków zasad

AKS zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji z co najmniej jednym krytycznym błędem lub problemami z zabezpieczeniami, które mają wpływ na środowisko produkcyjne, bez wcześniejszego powiadomienia.

Określone wersje poprawek mogą być pomijane lub przyspieszane w zależności od ważności usterki lub problemu z zabezpieczeniami.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal i interfejsu wiersza polecenia

Podczas wdrażania klastra usługi AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster domyślnie jest w wersji pomocniczej N-1 i najnowszej poprawce. Jeśli na przykład usługi AKS obsługują *wersję 1.17.a,* *1.17.b,* *1.16.c,* *1.16.d,* *1.15.e* i *1.15.f,* wybrana domyślna wersja to *1.16.c.*

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji i regionu, użyj [polecenia az aks get-versions.][az-aks-get-versions] Poniższy przykład zawiera listę dostępnych wersji kubernetes dla *regionu EastUS:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Kalendarz wydania kubernetes dla programu AKS

Aby uzyskać informacje o poprzedniej historii wydań, zobacz [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Wersja K8s | Wydanie nadrzędne  | AKS (wersja zapoznawcza)  | AKS (ga ga)  | Koniec okresu eksploatacji |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | 23 marca 2010 r.  | Maj 2020 r.   | Sierpień 2020 r.  | 1.21 (gała) | 
| 1.19  | 04 sierpnia 2010 r.  | Wrz 2020 r.   | Listopad 2020 r.  | 1.22 GA | 
| 1.20  | Dec-08-20  | Sty 2021 r.   | Mar 2021  | 1.23 GA |
| 1.21  | Kwi-08-21 | Maj 2021 r.   | Czerwiec 2021 r.  | 1.24 GA |



## <a name="faq"></a>Często zadawane pytania

**Jak firma Microsoft powiadamia mnie o nowych wersjach platformy Kubernetes?**

Zespół usługi AKS publikuje wstępne ogłoszenia z zaplanowanymi datami nowych wersji platformy Kubernetes w naszej dokumentacji, naszej witrynie [GitHub,](https://github.com/Azure/AKS/releases) a także wiadomości e-mail do administratorów subskrypcji, którzy są właścicielami klastrów, które nie będą już dostępne.  Oprócz anonsów usługa AKS używa również usługi Azure Advisor do powiadamiania klienta w witrynie Azure Portal, aby powiadamiać użytkowników, jeśli nie mają pomocy technicznej, [a](https://docs.microsoft.com/azure/advisor/advisor-overview) także ostrzegać ich o przestarzałych interfejsach API, które będą mieć wpływ na ich proces tworzenia aplikacji lub procesów. 

**Jak często należy oczekiwać uaktualnienia wersji kubernetes w celu pozostania w pomocy technicznej?**

Począwszy od środowiska Kubernetes 1.19, społeczność open source rozszerzyła wsparcie [do 1 roku.](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/) AKS zobowiązuje się do włączania poprawek i obsługi dopasowywania zobowiązań nadrzędnych. W przypadku klastrów usługi AKS w wersji 1.19 lub większej będzie można uaktualnić program co najmniej raz w roku, aby pozostać w obsługiwanej wersji. 

W przypadku wersji 1.18 lub starszej okres wsparcia technicznego wynosi 9 miesięcy, co wymaga uaktualnienia co 9 miesięcy, aby zachować obsługiwaną wersję. Regularnie testuj nowe wersje i przygotuj się do uaktualnienia do nowszej wersji w celu przechwycenia najnowszych stabilnych ulepszeń w ramach rozwiązania Kubernetes.

**Co się stanie, gdy użytkownik uaktualni klaster Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

Jeśli używasz wersji *n-3* lub starszej, oznacza to, że nie masz pomocy technicznej i zostanie poproszony o uaktualnienie. Po zakończeniu uaktualniania z wersji n-3 do n-2 wrócisz do naszych zasad pomocy technicznej. Na przykład:

- Jeśli najstarsza obsługiwana wersja usługi AKS to *1.15.a* i masz wersję *1.14.b* lub starszą, nie masz pomocy technicznej.
- Po pomyślnym uaktualnieniu z *wersji 1.14.b* do *wersji 1.15.a* lub wyższej wrócisz do naszych zasad pomocy technicznej.

Obniżanie poziomu nie jest obsługiwane.

**Co oznacza "poza wsparciem"**

"Poza pomocą techniczną" oznacza, że:
* Wersja, która jest uruchomiona, znajduje się poza listą obsługiwanych wersji.
* Podczas żądania pomocy technicznej zostanie poproszony o uaktualnienie klastra do obsługiwanej wersji, chyba że po zakończeniu obsługi wersji zostanie określony 30-dniowy okres prolongaty. 

Ponadto usługa AKS nie zapewnia żadnych gwarancji środowiska uruchomieniowego ani innych gwarancji dla klastrów spoza listy obsługiwanych wersji.

**Co się stanie, gdy użytkownik skaluje klaster Kubernetes przy użyciu wersji pomocniczej, która nie jest obsługiwana?**

W przypadku wersji pomocniczych, które nie są obsługiwane przez usługę AKS, skalowanie do zewnątrz lub do zewnątrz powinno nadal działać. Ponieważ nie ma żadnych Quality of Service, zalecamy uaktualnienie w celu zapewnienia obsługi klastra.

**Czy użytkownik może pozostać w wersji kubernetes na zawsze?**

Jeśli klaster nie obsługuje więcej niż trzech (3) wersji pomocniczych i znaleziono ryzyko związane z zabezpieczeniami, platforma Azure proaktywnie kontaktuje się z Tobem w celu uaktualnienia klastra. Jeśli nie podejmiesz dalszych działań, platforma Azure zastrzega sobie prawo do automatycznego uaktualnienia klastra w Twoim imieniu.

**Jaka wersja jest obsługiwana przez płaszczyznę sterowania, jeśli pula węzłów nie znajduje się w jednej z obsługiwanych wersji AKS?**

Płaszczyzna sterowania musi znajdować się w oknie wersji ze wszystkich pul węzłów. Szczegółowe informacje na temat uaktualniania płaszczyzny sterowania lub pul węzłów można znaleźć w dokumentacji dotyczącej [uaktualniania pul węzłów.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

**Czy można pominąć wiele wersji usługi AKS podczas uaktualniania klastra?**

Podczas uaktualniania obsługiwanego klastra usługi AKS nie można pominąć wersji pomocniczej kubernetes. Na przykład uaktualnienia między:
  * *1.12.x*  ->  *1.13.x:* dozwolone.
  * *1.13.x*  ->  *1.14.x:* dozwolone.
  * *1.12.x*  ->  *1.14.x:* niedozwolone.

Aby uaktualnić z *wersji 1.12.x*  ->  *1.14.x:*
1. Uaktualnij *system z wersji 1.12.x*  ->  *1.13.x.*
1. Uaktualnij *z wersji 1.13.x*  ->  *1.14.x.*

Pomijanie wielu wersji można wykonać tylko podczas uaktualniania z nieobsługiwanej wersji z powrotem do obsługiwanej wersji. Na przykład można uaktualnić z nieobsługiwanej wersji *1.10.x* do obsługiwanej *wersji 1.15.x.*

**Czy mogę utworzyć nowy klaster 1.xx.x w 30-dniowym oknie pomocy technicznej?**

Nie. Gdy wersja jest przestarzała/usunięta, nie można utworzyć klastra z tej wersji. Wraz ze zmianą zaczniesz widzieć starą wersję usuniętą z listy wersji. Ten proces może potrwać do dwóch tygodni od ogłoszenia, stopniowo według regionu.

**Jestem w nowej, przestarzałej wersji. Czy nadal mogę dodawać nowe pule węzłów? Czy będzie trzeba uaktualnić?**

Nie. Nie będzie można dodawać pul węzłów w przestarzałej wersji do klastra. Możesz dodać pule węzłów nowej wersji. Może to jednak wymagać najpierw zaktualizowania płaszczyzny sterowania. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Upgrade an Azure Kubernetes Service (AKS) cluster (Uaktualnianie klastra usługi Azure Kubernetes Service (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
