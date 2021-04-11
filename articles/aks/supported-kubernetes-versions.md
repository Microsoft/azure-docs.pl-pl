---
title: Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service
description: Informacje na temat zasad obsługi wersji Kubernetes i cyklu życia klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: ba75e11a067a257c659f8c659f68bb2bba6fa2e0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012091"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Obsługiwane wersje rozwiązania Kubernetes w usłudze Azure Kubernetes Service)

Społeczność Kubernetes zwalnia drobne wersje co trzy miesiące. Ostatnio społeczność Kubernetes [zwiększyła okno pomocy technicznej dla każdej wersji z 9 miesięcy do 12 miesięcy](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), zaczynając od wersji 1,19. 

Wersje pomocnicze zawierają nowe funkcje i ulepszenia. Wersje poprawek są częstsze (czasami cotygodniowo) i są przeznaczone dla krytycznych poprawek błędów w wersji pomocniczej. Wersje poprawek obejmują poprawki dotyczące luk w zabezpieczeniach lub poważnych usterek.

## <a name="kubernetes-versions"></a>Wersje Kubernetes

Kubernetes używa standardowego schematu przechowywania [wersji](https://semver.org/) dla każdej wersji:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Każda liczba w wersji wskazuje ogólną zgodność z poprzednią wersją:

* **Wersje główne** zmieniają się, gdy niezgodne aktualizacje interfejsu API lub zgodność z poprzednimi wersjami mogą zostać uszkodzone.
* **Wersje pomocnicze** zmieniają się, gdy wprowadzane są aktualizacje funkcjonalności, które są wstecznie zgodne z innymi wersjami pomocniczymi.
* **Wersje poprawek** zmieniają się w przypadku wprowadzenia poprawek błędów zgodnych z poprzednimi wersjami.

Zastąp, aby uruchomić najnowszą wersję poprawki wersji pomocniczej, która jest uruchomiona. Na przykład klaster produkcyjny jest włączony **`1.17.7`** . **`1.17.8`** to najnowsza dostępna wersja poprawek dostępna dla serii *1,17* . Należy uaktualnić do programu **`1.17.8`** tak szybko, jak to możliwe, aby upewnić się, że klaster jest w pełni objęty poprawką i jest obsługiwany.

## <a name="kubernetes-version-support-policy"></a>Zasady obsługi wersji Kubernetes

AKS definiuje ogólnie dostępną wersję jako wersję włączoną we wszystkich pomiarach SLO lub SLA i dostępne we wszystkich regionach. AKS obsługuje trzy drobne wersje pomocnicze Kubernetes:

* Najnowsza wersja pomocnicza systemu, wydawana w AKS (którą będziemy odnosić jako N).
* Dwie wcześniejsze wersje pomocnicze.
    * Każda obsługiwana wersja pomocnicza obsługuje również maksymalnie dwa (2) stabilne poprawki.

AKS mogą również obsługiwać wersje w wersji zapoznawczej, które są jawnie oznaczone etykietami i podlegają zapewnieniu warunków [i postanowień][preview-terms].

> [!NOTE]
> AKS używa bezpiecznych praktyk wdrażania, które obejmują stopniowe wdrażanie regionów. Oznacza to, że może upłynąć do 10 dni roboczych, aby nowe wydanie lub Nowa wersja była dostępna we wszystkich regionach.

Obsługiwane okno wersji Kubernetes w AKS jest znane jako "N-2": (N (Najnowsza wersja) — 2 (wersje pomocnicze)).

Na przykład jeśli AKS wprowadza *1.17. a* dzisiaj, pomoc techniczna jest świadczona dla następujących wersji:

Nowa wersja pomocnicza    |    Lista obsługiwanych wersji
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Gdzie ". litera" jest reprezentatywna dla wersji poprawki.

Po wprowadzeniu nowej wersji pomocniczej, najstarsza wersja pomocnicza i wersje poprawek są przestarzałe i usunięte. Na przykład Aktualna lista obsługiwanych wersji to:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS wersje 1,18. \* , usuwanie wszystkich wersji 1,15. z \* pomocy technicznej w ciągu 30 dni.

> [!NOTE]
> Jeśli klienci korzystają z nieobsługiwanej wersji programu Kubernetes, zostanie poproszony o uaktualnienie w przypadku żądania pomocy technicznej dotyczącej klastra. Klastry z nieobsługiwanymi wersjami Kubernetes nie są objęte [zasadami obsługi AKS](./support-policies.md).

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

### <a name="supported-kubectl-versions"></a>Obsługiwane `kubectl` wersje

Możesz użyć jednej wersji pomocniczej starszej lub nowszej w `kubectl` odniesieniu do wersji *polecenia-apiserver* , spójnej z [zasadami pomocy technicznej Kubernetes dla polecenia kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Na przykład jeśli Twój *polecenia-apiserver* ma *1,17*, możesz użyć wersji *1,16* do *1,18* z `kubectl` *polecenia-apiserver*.

Aby zainstalować lub zaktualizować wersję programu `kubectl` , uruchom polecenie `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Proces wydania i wycofania

Możesz odwoływać się do nadchodzących wersji i zaniecheń w [kalendarzu wydania AKS Kubernetes](#aks-kubernetes-release-calendar).

Nowe wersje **pomocnicze** programu Kubernetes:
  * AKS publikuje wstępne zawiadomienie o planowanej dacie nowej wersji, a odpowiednia stara wersja jest przestarzała w [informacjach o wersji AKS](https://aka.ms/aks/releasenotes) co najmniej 30 dni przed usunięciem.
  * AKS publikuje [powiadomienie o kondycji usługi](../service-health/service-health-overview.md) dostępne dla wszystkich użytkowników z dostępem do AKS i portalu i wysyła wiadomość e-mail do administratorów subskrypcji z datami usunięcia planowanej wersji.

    ````
    To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
    ````
  * Użytkownicy mają **30 dni** od usunięcia wersji, aby przeprowadzić uaktualnienie do obsługiwanej wersji pomocniczej, aby nadal otrzymywać pomoc techniczną.

Nowe wersje **poprawek** Kubernetes:
  * Ze względu na pilną naturę wersji poprawek, można je wprowadzić do usługi w miarę ich udostępniania.
  * Ogólnie rzecz biorąc, AKS nie komunikuje się w szerokim stopniu o wydaniu nowych wersji poprawki. Jednak AKS stale monitoruje i sprawdza dostępność dostępnych poprawek w CVE, aby zapewnić ich obsługę w AKS w odpowiednim czasie. Jeśli zostanie znaleziona poprawka krytyczna lub wymagana jest akcja użytkownika, AKS będzie powiadamiać użytkowników o konieczności uaktualnienia do nowo dostępnej poprawki.
  * Użytkownicy mają **30 dni** od usunięcia poprawki z AKS, aby uaktualnić do obsługiwanej poprawki i nadal otrzymywać pomoc techniczną.

### <a name="supported-versions-policy-exceptions"></a>Obsługiwane wyjątki zasad wersji

AKS zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji z co najmniej jednym krytycznym problemem produkcyjnym lub problemami z zabezpieczeniami bez wcześniejszego powiadomienia.

Określone wersje poprawek mogą zostać pominięte lub przyspieszone, w zależności od ważności usterki lub problemu z zabezpieczeniami.

## <a name="azure-portal-and-cli-versions"></a>Wersje Azure Portal i interfejsu wiersza polecenia

W przypadku wdrażania klastra AKS w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure domyślnie klaster jest wersją pomocniczą N-1 i ostatnią poprawką. Na przykład jeśli AKS obsługuje *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e* i *1.15. f*, wybrana wersja domyślna to *1.16. c*.

Aby dowiedzieć się, jakie wersje są obecnie dostępne dla Twojej subskrypcji i regionu, użyj polecenia [AZ AKS Get-Versions][az-aks-get-versions] . Poniższy przykład zawiera listę dostępnych wersji Kubernetes dla regionu *wschodniego* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Kalendarz wydania AKS Kubernetes

Aby uzyskać historię wcześniejszych wersji, zobacz [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Wersja K8s | Wersja nadrzędna  | AKS wersja zapoznawcza  | AKS GA  | Koniec okresu istnienia |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Gru-09-19  | Sty 2019   | Lip 2020  | 1,20 GA | 
| 1,18  | Mar-23-20  | Maj 2020 r.   | 2020 sie  | 1,21 GA | 
| 1,19  | Sie-04-20  | Wrz 2020   | Lis 2020  | 1,22 GA | 
| 1.20  | Gru-08-20  | Sty 2021   | Mar 2021  | 1,23 GA |
| 1,21  | Kwi-08-21 * | Maj 2021 r.   | Jun 2021  | 1,24 GA |

\* Wersja Kubernetes 1,21 nadrzędnego może ulec zmianie jako kalendarz nadrzędny, ponieważ jest jeszcze gotowa.


## <a name="faq"></a>Często zadawane pytania

**Jak często należy oczekiwać, aby uaktualnienie wersji Kubernetes było pozostawać w pomocy technicznej?**

Począwszy od Kubernetes 1,19, [społeczność "open source" ma rozszerzoną obsługę na 1 rok](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS zatwierdza, aby włączyć poprawki i obsłużyć zgodność z zobowiązaniami nadrzędnymi. W przypadku klastrów AKS w systemie 1,19 i nowszych będzie można przeprowadzić uaktualnienie co najmniej raz na rok, aby zachować dostęp do obsługiwanej wersji. 

W przypadku wersji 1,18 lub niższych okno pomocy technicznej pozostaje w ciągu 9 miesięcy, co wymaga uaktualnienia co 9 miesięcy, aby zachować dostęp do obsługiwanej wersji. Regularne testowanie nowych wersji i przygotowanie się do uaktualnienia do nowszych wersji w celu przechwycenia najnowszych stabilnych ulepszeń w programie Kubernetes.

**Co się stanie, gdy użytkownik uaktualnia klaster Kubernetes z nieobsługiwaną wersją pomocniczą?**

Jeśli korzystasz z wersji *n-3* lub starszej, oznacza to, że użytkownik jest poza pomocą techniczną i zostanie poproszony o uaktualnienie. Gdy uaktualnienie z wersji n-3 do n-2 powiedzie się, nastąpi powrót do naszych zasad pomocy technicznej. Na przykład:

- Jeśli najstarsza obsługiwana wersja AKS to *1.15. a* i jesteś w wersji *1.14. b* lub starszej, jesteś poza pomocą techniczną.
- Po pomyślnym uaktualnieniu z wersji *1.14. b* do *1.15. a* lub nowszej, nastąpi powrót do naszych zasad pomocy technicznej.

Obniżenie wersji nie jest obsługiwane.

**Co oznacza "poza pomocą techniczną"**

"Poza pomocą techniczną" oznacza, że:
* Uruchomiona wersja znajduje się poza listą obsługiwanych wersji.
* Przed zażądaniem pomocy technicznej użytkownik zostanie poproszony o uaktualnienie klastra do obsługiwanej wersji, chyba że w ciągu 30-dniowego okresu prolongaty od momentu wycofania wersji. 

Ponadto AKS nie wykonuje żadnego środowiska uruchomieniowego ani innych gwarancji dla klastrów poza listą obsługiwanych wersji.

**Co się stanie, gdy użytkownik skaluje klaster Kubernetes z nieobsługiwaną wersją pomocniczą?**

W przypadku wersji pomocniczych nieobsługiwanych przez AKS skalowanie w górę lub w dół powinno być nadal wykonywane. Ponieważ nie ma żadnych gwarancji Quality of Service, zalecamy uaktualnienie programu w celu przywrócenia obsługi klastra.

**Czy użytkownik może korzystać z wersji Kubernetes bez ograniczeń?**

Jeśli klaster jest nieobsługiwany przez więcej niż trzy (3) wersje pomocnicze i został uznany za zagrożenie bezpieczeństwa, platforma Azure aktywnie kontaktuje się z tym, aby uaktualnić klaster. Jeśli nie podejmujesz dalszych działań, platforma Azure zastrzega sobie prawo do automatycznego uaktualniania klastra w Twoim imieniu.

**Jaka wersja jest obsługiwana przez płaszczyznę kontroli, jeśli Pula węzłów nie znajduje się w jednej z obsługiwanych wersji AKS?**

Płaszczyzna kontroli musi znajdować się w oknie wersji ze wszystkich pul węzłów. Aby uzyskać szczegółowe informacje na temat uaktualniania płaszczyzny kontroli lub pul węzłów, zapoznaj się z dokumentacją dotyczącą [uaktualniania pul węzłów](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Czy mogę pominąć wiele wersji AKS podczas uaktualniania klastra?**

W przypadku uaktualniania obsługiwanego klastra AKS nie można pominąć wersji pomocniczych Kubernetes. Na przykład uaktualnienia między programem:
  * *1.12. x*  ->  *1.13. x*: dozwolone.
  * *1.13. x*  ->  *1.14. x*: dozwolone.
  * *1.12. x*  ->  *1.14. x*: niedozwolone.

Aby uaktualnić z wersji *1.12. x*  ->  *1.14. x*:
1. Uaktualnij z wersji *1.12. x*  ->  *1.13. x*.
1. Uaktualnij z *1.13. x*  ->  *1.14. x*.

Pomijanie wielu wersji można wykonać tylko w przypadku uaktualniania z nieobsługiwanej wersji z powrotem do obsługiwanej wersji. Na przykład można przeprowadzić uaktualnienie z nieobsługiwanej wersji *1.10. x* do obsługiwanego *1.15. x*.

**Czy można utworzyć nowy klaster 1. XX. x w okresie 30-dniowego wsparcia?**

Nie. Gdy wersja zostanie wycofana/usunięta, nie można utworzyć klastra z tą wersją. Po rozpoczęciu zmiany zostanie wyświetlona stara wersja została usunięta z listy wersji. Ten proces może trwać do dwóch tygodni od ogłoszenia, stopniowo według regionów.

**Korzystam ze świeżo przestarzałej wersji, można nadal dodawać nowe pule węzłów? Lub czy konieczne jest uaktualnienie?**

Nie. Nie będzie można dodawać pul węzłów przestarzałej wersji do klastra. Można dodać pule węzłów nowej wersji. Może to jednak wymagać najpierw aktualizacji płaszczyzny kontroli. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat uaktualniania klastra, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
