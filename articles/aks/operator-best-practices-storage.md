---
title: Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi magazynu, szyfrowania danych i kopii zapasowych w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 722fe393ad7637be20360463a4c3b6234224a036
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653974"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia klastrów i zarządzania nimi w usłudze Azure Kubernetes Service (AKS) aplikacje często potrzebują magazynu. Należy zapoznać się z wymaganiami dotyczącymi wydajności i metodami dostępu dla zasobników, aby zapewnić odpowiedni magazyn dla aplikacji. Rozmiar węzła AKS może mieć wpływ na te opcje magazynu. Należy również zaplanować sposoby tworzenia kopii zapasowych i testowania procesu przywracania dołączonego magazynu.

W tym artykule dotyczącym najlepszych rozwiązań przedstawiono zagadnienia dotyczące magazynu dla operatorów klastrów. W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Jakie typy magazynów są dostępne
> * Jak poprawnie zmienić rozmiar węzłów AKS na potrzeby wydajności magazynu
> * Różnice między dynamiczną i statyczną obsługą woluminów
> * Sposoby tworzenia kopii zapasowych i zabezpieczania woluminów danych

## <a name="choose-the-appropriate-storage-type"></a>Wybierz odpowiedni typ magazynu

**Wskazówki dotyczące najlepszych** rozwiązań — zapoznaj się z potrzebami aplikacji, aby wybrać odpowiedni magazyn. Użyj wysokiej wydajności magazynu z magazynem dysków SSD dla obciążeń produkcyjnych. Zaplanuj Magazyn oparty na sieci, gdy istnieje potrzeba wielu jednoczesnych połączeń.

Aplikacje często wymagają różnych typów i szybkości magazynowania. Czy aplikacje potrzebują magazynu, który łączy się z poszczególnymi zasobnikami lub są udostępniane przez wiele zasobników? Czy magazyn jest dostęp tylko do odczytu do danych lub w celu zapisywania dużych ilości danych strukturalnych? Te wymagania dotyczące magazynu określają najbardziej odpowiedni typ magazynu do użycia.

W poniższej tabeli przedstawiono dostępne typy magazynów i ich możliwości:

| Przypadek użycia | Wtyczka woluminu | Odczyt/zapis jednokrotny | Tylko do odczytu | Odczyt/zapis wielu | Obsługa kontenerów systemu Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Konfiguracja udostępniona       | Azure Files   | Tak | Tak | Tak | Tak |
| Dane aplikacji ze strukturą        | Azure Disks   | Tak | Nie  | Nie  | Tak |
| Dane bez struktury, operacje systemu plików | [BlobFuse][blobfuse] | Tak | Tak | Tak | Nie |

Dwa podstawowe typy magazynów udostępnione dla woluminów w AKS są obsługiwane przez dyski lub Azure Files platformy Azure. Aby zwiększyć bezpieczeństwo, oba typy magazynów używają domyślnie usługi Azure szyfrowanie usługi Storage (SSE), która szyfruje dane przechowywane w spoczynku. Dysków nie można obecnie zaszyfrować przy użyciu Azure Disk Encryption na poziomie węzła AKS.

Zarówno Azure Files, jak i dyski platformy Azure są dostępne w warstwach wydajności warstwy Standardowa i Premium:

- Dyski w *warstwie Premium* są obsługiwane przez dyski półprzewodnikowe o wysokiej wydajności (dysków SSD). Dyski w warstwie Premium są zalecane w przypadku wszystkich obciążeń produkcyjnych.
- Dyski *standardowe* są obsługiwane przez regularne wirowanie dysków (HDD) i są dobre dla archiwizowania lub rzadko używanych danych.

Zapoznaj się z wymaganiami dotyczącymi wydajności aplikacji i wzorcami dostępu w celu wybrania odpowiedniej warstwy magazynowania. Aby uzyskać więcej informacji na temat rozmiarów Managed Disks i warstw wydajności, zobacz [Omówienie usługi Azure Managed disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tworzenie i używanie klas magazynu do definiowania potrzeb aplikacji

Używany typ magazynu jest definiowany przy użyciu *klas magazynu* Kubernetes. Następnie Klasa magazynu jest przywoływana w specyfikacji pod lub wdrożenia. Te definicje współpracują ze sobą, aby utworzyć odpowiedni magazyn i połączyć go z zasobnikami. Aby uzyskać więcej informacji, zobacz [klasy magazynu w AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Rozmiar węzłów dla potrzeb magazynu

**Wskazówki dotyczące najlepszych** rozwiązań — każdy rozmiar węzła obsługuje maksymalną liczbę dysków. Różne rozmiary węzłów zapewniają również różne ilości magazynu lokalnego i przepustowości sieci. Zaplanuj zapotrzebowanie aplikacji, aby wdrożyć odpowiedni rozmiar węzłów.

Węzły AKS są uruchamiane jako maszyny wirtualne platformy Azure. Dostępne są różne typy i rozmiary maszyn wirtualnych. Każdy rozmiar maszyny wirtualnej zapewnia różną ilość zasobów podstawowych, takich jak procesor CPU i pamięć. Te rozmiary maszyn wirtualnych mają maksymalną liczbę dysków, które można dołączać. Wydajność magazynu różni się również między rozmiarami maszyn wirtualnych w przypadku maksymalnej liczby operacji we/wy na sekundę podłączonych do dysku (wejścia/wyjścia w dół).

Jeśli aplikacje wymagają dysków platformy Azure jako rozwiązania magazynu, Zaplanuj i wybierz odpowiedni rozmiar maszyny wirtualnej węzła. Wielkość procesora CPU i pamięci nie jest jedynym czynnikiem w przypadku wybrania rozmiaru maszyny wirtualnej. Funkcje magazynu są również ważne. Na przykład rozmiary maszyn wirtualnych *Standard_B2ms* i *Standard_DS2_v2* obejmują podobną ilość zasobów procesora CPU i pamięci. Ich potencjalna wydajność magazynu jest inna, jak pokazano w poniższej tabeli:

| Typ i rozmiar węzła | Procesor wirtualny | Pamięć (GiB) | Maks. liczba dysków danych | Maksymalna liczba operacji we/wy dysku w pamięci podręcznej | Maksymalna przepływność w pamięci podręcznej (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

W tym miejscu *Standard_DS2_v2* umożliwia podwójnej liczby dołączonych dysków i oferuje trzy do cztery razy większą liczbę operacji we/wy na sekundę. Jeśli oglądasz tylko podstawowe zasoby obliczeniowe i porównane koszty, możesz wybrać *Standard_B2ms* rozmiar maszyny wirtualnej i mieć niską wydajność i ograniczenia magazynu. Skontaktuj się z zespołem programistycznym aplikacji, aby poznać ich pojemność magazynu i wydajność. Wybierz odpowiedni rozmiar maszyny wirtualnej dla węzłów AKS, aby spełnić lub przekroczyć ich potrzeby związane z wydajnością. Regularne aplikacje bazowe umożliwiające dostosowanie rozmiaru maszyny wirtualnej zgodnie z wymaganiami.

Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamicznie zastrzegaj woluminy

**Wskazówki dotyczące najlepszych** rozwiązań — aby zmniejszyć koszty związane z zarządzaniem i umożliwić skalowanie, nie można statycznie tworzyć i przypisywać woluminów trwałych. Użyj dynamicznej obsługi administracyjnej. W klasach magazynu Zdefiniuj odpowiednie zasady odzyskiwania w celu zminimalizowania niepotrzebnych kosztów magazynu po usunięciu z nich.

Jeśli musisz dołączyć magazyn do zasobników, użyj woluminów trwałych. Te woluminy trwałe można utworzyć ręcznie lub dynamicznie. Ręczne tworzenie woluminów trwałych zwiększa koszty zarządzania i ogranicza możliwości skalowania. Dynamiczne trwałe Inicjowanie obsługi woluminów pozwala uprościć zarządzanie magazynem i umożliwia skalowanie aplikacji w miarę potrzeb.

![Trwałe oświadczenia woluminów w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Trwałego żądania woluminu (PVC) umożliwia dynamiczne tworzenie magazynu zgodnie z wymaganiami. Bazowe dyski platformy Azure są tworzone jako żądania dla nich. W definicji poniżej zażądano utworzenia woluminu i dołączenia go do określonej ścieżki instalacji.

Aby uzyskać informacje na temat sposobu dynamicznego tworzenia i używania woluminów, zobacz [oświadczenia dotyczące woluminów trwałych][aks-concepts-storage-pvcs].

Aby wyświetlić te woluminy, zobacz sposób dynamicznego tworzenia i używania woluminu trwałego z dyskami lub [Azure Files][dynamic-files] [platformy Azure][dynamic-disks] .

W ramach definicji klasy magazynu Ustaw odpowiednie *reclaimPolicy*. Ta reclaimPolicy kontroluje zachowanie bazowego zasobu usługi Azure Storage, gdy jest on usunięty, a wolumin trwały może nie być już wymagany. Podstawowy zasób magazynu można usunąć lub zachować do użycia w przyszłości. ReclaimPolicy można ustawić, aby *zachować* lub *usunąć*. Zapoznaj się z wymaganiami dotyczącymi aplikacji i zaimplementuj regularne sprawdzanie magazynu, który jest zachowywany, aby zminimalizować ilość nieużywanych magazynów, które są używane i rozliczane.

Aby uzyskać więcej informacji o opcjach klasy magazynu, zobacz [zasady odzyskiwania magazynu][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpieczanie i tworzenie kopii zapasowych danych

**Wskazówki dotyczące najlepszych** rozwiązań — tworzenie kopii zapasowych danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero lub Azure Backup. Sprawdź integralność i bezpieczeństwo tych kopii zapasowych.

Gdy aplikacje przechowują i zużywają dane utrwalane na dyskach lub w plikach, należy wykonywać regularne kopie zapasowe lub migawki tych danych. Na dyskach platformy Azure można używać wbudowanych technologii migawek. Przed wykonaniem operacji migawki może być konieczne wyszukanie aplikacji w celu opróżnienia zapisu na dysku. [Velero][velero] może tworzyć kopie zapasowe woluminów trwałych wraz z dodatkowymi zasobami i konfiguracjami klastra. Jeśli nie możesz [usunąć stanu z aplikacji][remove-state], Utwórz kopię zapasową danych z woluminów trwałych i regularnie Przetestuj operacje przywracania, aby zweryfikować integralność danych i wymagane procesy.

Zapoznaj się z ograniczeniami różnych metod tworzenia kopii zapasowych danych i, jeśli chcesz, aby dane były w stanie spoczynku przed migawką. Kopie zapasowe danych nie zawsze umożliwiają przywrócenie środowiska aplikacji wdrożenia klastra. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii w programie AKS][best-practices-multi-region].

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na najlepszych rozwiązaniach dotyczących magazynu w programie AKS. Aby uzyskać więcej informacji na temat podstawy magazynu w programie Kubernetes, zobacz [pojęcia dotyczące magazynu dla aplikacji w AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
