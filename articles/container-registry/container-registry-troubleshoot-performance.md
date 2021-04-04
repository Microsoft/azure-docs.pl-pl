---
title: Rozwiązywanie problemów z wydajnością rejestru
description: Objawy, przyczyny i rozwiązywanie typowych problemów z wydajnością rejestru
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148406"
---
# <a name="troubleshoot-registry-performance"></a>Rozwiązywanie problemów z wydajnością rejestru

Ten artykuł pomaga w rozwiązywaniu problemów, które mogą wystąpić w przypadku wydajności usługi Azure Container Registry. 

## <a name="symptoms"></a>Objawy

Może zawierać co najmniej jedną z następujących czynności:

* Pobieranie lub wypychanie obrazów za pomocą interfejsu wiersza polecenia platformy Docker trwa dłużej niż oczekiwano
* Wdrażanie obrazów w usłudze, takiej jak usługa Azure Kubernetes, trwa dłużej niż oczekiwano
* Nie można wykonać dużej liczby równoczesnych operacji ściągania lub wypychania w oczekiwanym czasie
* Operacje ściągania lub wypychania w rejestrze z replikacją geograficzną trwają dłużej niż oczekiwano lub wypychanie kończy się niepowodzeniem z powodu błędu `Error writing blob` lub `Error writing manifest`

## <a name="causes"></a>Przyczyny

* Szybkość połączenia sieciowego może spowalniać operacje na rejestrze — [rozwiązanie](#check-expected-network-speed)
* Kompresja lub wyodrębnianie warstwy obrazu może być powolne w [rozwiązaniu](#check-client-hardware) klienta  
* Zbliżasz się do skonfigurowanego limitu w warstwie usług rejestru lub środowisku — [rozwiązanie](#review-configured-limits)
* Rejestr z replikacją geograficzną ma repliki w pobliżu regionów — [rozwiązanie](#configure-geo-replicated-registry)
* Ściąganie z geograficznie odległej repliki rejestru — [rozwiązanie](#configure-dns-for-geo-replicated-registry)

Jeśli w tym miejscu nie rozwiążesz problemu, zapoznaj się z tematem [Zaawansowane rozwiązywanie problemów](#advanced-troubleshooting) i [następne kroki](#next-steps) dla innych opcji.

## <a name="potential-solutions"></a>Potencjalne rozwiązania

### <a name="check-expected-network-speed"></a>Sprawdź oczekiwaną szybkość sieci

Sprawdź szybkość przekazywania i pobierania Internetu albo użyj narzędzia, takiego jak AzureSpeed, aby przetestować [przekazywanie](https://www.azurespeed.com/Azure/Uploadß) i [pobieranie](https://www.azurespeed.com/Azure/Download) z usługi Azure Blob Storage, która hostuje warstwy obrazu rejestru.

Sprawdź rozmiar obrazu pod kątem maksymalnego obsługiwanego rozmiaru i obsługiwanego pobierania lub przekazywania przepustowości dla warstwy usług rejestru. Jeśli rejestr znajduje się w warstwie Podstawowa lub standardowa, rozważ uaktualnienie, aby zwiększyć wydajność. 

W przypadku wdrażania obrazów do innych usług Sprawdź regiony, w których znajduje się rejestr i miejsce docelowe. Aby zwiększyć wydajność, należy rozważyć zlokalizowanie rejestru i celu wdrożenia w regionach lub w pobliżu sieci.

Powiązane linki:

* [Azure Container Registry warstw usług](container-registry-skus.md)    
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)
* [Cele dotyczące wydajności i skalowalności dla usługi Azure Blob Storage](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Sprawdź sprzęt klienta

Typ dysku i procesor CPU na kliencie platformy Docker mogą mieć wpływ na szybkość wyodrębniania lub kompresowania warstw obrazu na kliencie w ramach operacji ściągania lub wypychania. Na przykład wyodrębnianie warstwy na dysku twardym zajmie więcej czasu niż na dysku SSD. Porównanie operacji ściągania w celu uzyskania porównywalnych obrazów z usługi Azure Container Registry oraz rejestru publicznego, takiego jak Docker Hub.

### <a name="review-configured-limits"></a>Przejrzyj skonfigurowane limity

Jeśli wykonujesz współbieżne wypychanie lub ściąganie wielu lub wielu obrazów z wieloma warstwami do rejestru, przejrzyj obsługiwane limity ReadOps i WriteOps dla warstwy usług rejestru. Jeśli rejestr znajduje się w warstwie Podstawowa lub standardowa, rozważ uaktualnienie programu w celu zwiększenia limitów. Skontaktuj się również z dostawcą sieci dotyczącym ograniczania sieci, które mogą wystąpić w wielu współbieżnych operacjach. 

Przejrzyj konfigurację demona platformy Docker, aby uzyskać maksymalne współbieżne operacje przekazywania lub pobierania dla każdej operacji wypychania lub ściągania na kliencie. W razie konieczności Skonfiguruj wyższe limity.

Ponieważ każda warstwa obrazu wymaga oddzielnej operacji odczytu lub zapisu rejestru, sprawdź liczbę warstw w obrazie. Należy rozważyć strategie, aby zmniejszyć liczbę warstw obrazu.

Powiązane linki:

* [Azure Container Registry warstw usług](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Konfigurowanie rejestru z replikacją geograficzną

Klient platformy Docker, który wypycha obraz do rejestru replikowanego geograficznie, może nie wypchnąć wszystkich warstw obrazu i jego manifestu do jednego zreplikowanego regionu. Taka sytuacja może wystąpić, ponieważ usługa Azure Traffic Manager kieruje żądania rejestru do najbliższego rejestru replikowanego w sieci. Jeśli rejestr ma dwa regiony replikacji w pobliżu, warstwy obrazu i manifest mogą być dystrybuowane do dwóch lokacji, a operacja push kończy się niepowodzeniem po sprawdzeniu poprawności manifestu.

W celu zoptymalizowania rozpoznawania nazw DNS do najbliższej repliki podczas wypychania obrazów Skonfiguruj rejestr z replikacją geograficzną w tych samych regionach platformy Azure jako źródło operacji wypychania lub najbliższy Region podczas pracy poza platformą Azure.

Aby rozwiązać problemy z rejestrem z replikacją geograficzną, można również tymczasowo wyłączyć routing Traffic Manager do co najmniej jednej replikacji.

Powiązane linki:

* [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Konfigurowanie usługi DNS dla rejestru z replikacją geograficzną

Jeśli operacje ściągnięcia z rejestru z replikacją geograficzną wyglądają wolniej, Konfiguracja DNS na kliencie może zostać rozpoznana jako geograficznie odległy serwer DNS. W takim przypadku Traffic Manager mogą być żądaniami routingu do repliki, która jest bliska sieci do serwera DNS, ale z odległego od klienta. Uruchom narzędzie, takie jak `nslookup` lub `dig` (w systemie Linux), aby określić replikę, która Traffic Manager kieruje żądania rejestru do programu. Na przykład:

```console
nslookup myregistry.azurecr.io
```

Potencjalnym rozwiązaniem jest skonfigurowanie bliższego serwera DNS.

Powiązane linki:

* [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-geo-replication.md)
* [Rozwiązywanie problemów z operacjami wypychania z rejestrami replikowanymi geograficznie](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Tymczasowe wyłączenie routingu do replikacji](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager często zadawane pytania](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Jeśli masz uprawnienia do zasobów rejestru Zezwalaj, [Sprawdź kondycję środowiska rejestru](container-registry-check-health.md). Jeśli są raportowane błędy, przejrzyj [Informacje o błędach](container-registry-health-error-reference.md) dla potencjalnych rozwiązań.

Jeśli [Kolekcja dzienników zasobów](container-registry-diagnostics-audit-logs.md) jest włączona w rejestrze, przejrzyj dziennik ContainterRegistryRepositoryEvents. W tym dzienniku są przechowywane informacje dotyczące operacji, takich jak wypychanie lub ściąganie zdarzeń. Zbadaj dziennik pod kątem [niepowodzeń operacji na poziomie repozytorium](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Powiązane linki:

* [Dzienniki dotyczące oceny i inspekcji diagnostyki](container-registry-diagnostics-audit-logs.md)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)
* [Najlepsze rozwiązania dla usługi Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Następne kroki

Jeśli w tym miejscu nie rozwiążesz problemu, zapoznaj się z następującymi opcjami.

* Inne tematy dotyczące rozwiązywania problemów z rejestrem obejmują:
  * [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md)
  * [Rozwiązywanie problemów z siecią przy użyciu rejestru](container-registry-troubleshoot-access.md)
* Opcje [pomocy technicznej społeczności](https://azure.microsoft.com/support/community/)
* [Pytania i odpowiedzi Microsoft](/answers/products/)
* [Otwieranie biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/)