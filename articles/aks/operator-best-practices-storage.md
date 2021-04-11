---
title: Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi magazynu, szyfrowania danych i kopii zapasowych w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104919"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia klastrów i zarządzania nimi w usłudze Azure Kubernetes Service (AKS) aplikacje często potrzebują magazynu. Upewnij się, że rozumiesz wymagania dotyczące wydajności i metody dostępu, aby można było wybrać najlepszy magazyn dla aplikacji. Rozmiar węzła AKS może mieć wpływ na wybór magazynu. Zaplanuj sposoby tworzenia kopii zapasowych i testowania procesu przywracania dołączonego magazynu.

W tym artykule dotyczącym najlepszych rozwiązań przedstawiono zagadnienia dotyczące magazynu dla operatorów klastrów. W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Jakie typy magazynów są dostępne.
> * Jak poprawnie zmienić rozmiar węzłów AKS na potrzeby wydajności magazynu.
> * Różnice między dynamiczną i statyczną obsługą woluminów.
> * Sposoby tworzenia kopii zapasowych i zabezpieczania woluminów danych.

## <a name="choose-the-appropriate-storage-type"></a>Wybierz odpowiedni typ magazynu

> **Wskazówki dotyczące najlepszych rozwiązań**
> 
> Zapoznaj się z potrzebami aplikacji, aby wybrać odpowiedni magazyn. Użyj wysokiej wydajności magazynu z magazynem dysków SSD dla obciążeń produkcyjnych. Zaplanuj Magazyn oparty na sieci, gdy potrzebujesz wielu jednoczesnych połączeń.

Aplikacje często wymagają różnych typów i szybkości magazynowania. Określ odpowiedni typ magazynu, zadając poniższe pytania. 
* Czy aplikacje potrzebują magazynu, który nawiązuje połączenie z indywidualnymi zasobnikami?
* Czy aplikacje potrzebują magazynu współdzielonego przez wiele zasobników? 
* Czy magazyn jest dostęp tylko do odczytu do danych?
* Czy magazyn będzie używany do zapisywania dużych ilości danych strukturalnych? 

W poniższej tabeli przedstawiono dostępne typy magazynów i ich możliwości:

| Przypadek użycia | Wtyczka woluminu | Odczyt/zapis jednokrotny | Tylko do odczytu | Odczyt/zapis wielu | Obsługa kontenerów systemu Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Konfiguracja udostępniona       | Azure Files   | Tak | Tak | Tak | Tak |
| Dane aplikacji ze strukturą        | Azure Disks   | Tak | Nie  | Nie  | Tak |
| Dane bez struktury, operacje systemu plików | [BlobFuse][blobfuse] | Tak | Tak | Tak | Nie |

AKS udostępnia dwa podstawowe typy bezpiecznego magazynu dla woluminów objętych usługą Azure disks lub Azure Files. Oba te elementy korzystają z domyślnej usługi Azure szyfrowanie usługi Storage (SSE), która szyfruje dane przechowywane w spoczynku. Dysków nie można zaszyfrować przy użyciu Azure Disk Encryption na poziomie węzła AKS.

Zarówno Azure Files, jak i dyski platformy Azure są dostępne w warstwach wydajności warstwy Standardowa i Premium:

- Dyski w *warstwie Premium*
    - Obsługiwane przez dyski półprzewodnikowe o wysokiej wydajności (dysków SSD). 
    - Zalecane dla wszystkich obciążeń produkcyjnych.
- Dyski *standardowe*
    - Obsługiwane przez regularne wirowanie dysków (HDD).
    - Dobre dla archiwizowania lub rzadko używanych danych.

Zapoznaj się z wymaganiami dotyczącymi wydajności aplikacji i wzorcami dostępu w celu wybrania odpowiedniej warstwy magazynowania. Aby uzyskać więcej informacji na temat rozmiarów Managed Disks i warstw wydajności, zobacz [Omówienie usługi Azure Managed disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tworzenie i używanie klas magazynu do definiowania potrzeb aplikacji

Zdefiniuj typ magazynu, który ma być używany przez *klasy magazynu* Kubernetes. Następnie Klasa magazynu jest przywoływana w specyfikacji pod lub wdrożenia. Definicje klas magazynu współpracują ze sobą, aby utworzyć odpowiedni magazyn i połączyć go z zasobnikami. 

Aby uzyskać więcej informacji, zobacz [klasy magazynu w AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Rozmiar węzłów dla potrzeb magazynu

> **Wskazówki dotyczące najlepszych rozwiązań**
> 
> Każdy rozmiar węzła obsługuje maksymalną liczbę dysków. Różne rozmiary węzłów zapewniają również różne ilości magazynu lokalnego i przepustowości sieci. Zaplanuj odpowiednio do potrzeb aplikacji, aby wdrożyć właściwy rozmiar węzłów.

Węzły AKS działają jako różne typy i rozmiary maszyn wirtualnych platformy Azure. Każdy rozmiar maszyny wirtualnej zapewnia:
* Inna ilość zasobów podstawowych, takich jak procesor CPU i pamięć. 
* Maksymalna liczba dysków, które można dołączać. 

Wydajność magazynu różni się również między rozmiarami maszyn wirtualnych w przypadku maksymalnej liczby operacji we/wy na sekundę podłączonych do dysku (wejścia/wyjścia w dół).

Jeśli aplikacje wymagają dysków platformy Azure jako rozwiązania magazynu, ułożeniu odpowiedni rozmiar maszyny wirtualnej węzła. Możliwości magazynu i zasoby procesora i pamięci odgrywają główną rolę podczas wybierania rozmiaru maszyny wirtualnej. 

Na przykład, chociaż rozmiary maszyn wirtualnych *Standard_B2ms* i *Standard_DS2_v2* obejmują podobną ilość zasobów procesora CPU i pamięci, ich potencjalna wydajność magazynu jest różna:

| Typ i rozmiar węzła | Procesor wirtualny | Pamięć (GiB) | Maks. liczba dysków danych | Maksymalna liczba operacji we/wy dysku w pamięci podręcznej | Maksymalna przepływność w pamięci podręcznej (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

W tym przykładzie *Standard_DS2_v2* oferuje dwa razy więcej dołączonych dysków, a trzy do czterech razy większa liczba operacji we/wy na dysku. Jeśli porównano tylko podstawowe zasoby obliczeniowe i porównano koszty, być może wybrano rozmiar maszyny wirtualnej *Standard_B2ms* z niską wydajnością magazynu i ograniczeniami. 

Skontaktuj się z zespołem programistycznym aplikacji, aby poznać ich pojemność magazynu i wydajność. Wybierz odpowiedni rozmiar maszyny wirtualnej dla węzłów AKS, aby spełnić lub przekroczyć ich potrzeby związane z wydajnością. Regularne aplikacje bazowe umożliwiające dostosowanie rozmiaru maszyny wirtualnej zgodnie z wymaganiami.

Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamicznie zastrzegaj woluminy

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Aby zmniejszyć koszty związane z zarządzaniem i włączyć skalowanie, należy unikać statycznego tworzenia i przypisywania woluminów trwałych. Użyj dynamicznej obsługi administracyjnej. W klasach magazynu Zdefiniuj odpowiednie zasady odzyskiwania w celu zminimalizowania niepotrzebnych kosztów magazynu po usunięciu z nich.

Aby dołączyć magazyn do zasobników, użyj woluminów trwałych. Woluminy trwałe można tworzyć ręcznie lub dynamicznie. Tworzenie woluminów trwałych ręcznie dodaje narzuty związane z zarządzaniem i ogranicza możliwość skalowania. Zamiast tego należy dynamicznie udostępniać wolumin trwały, aby uprościć zarządzanie magazynem i umożliwić aplikacjom rozwój i skalowanie w razie potrzeby.

![Trwałe oświadczenia woluminów w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Trwałego żądania woluminu (PVC) umożliwia dynamiczne tworzenie magazynu zgodnie z wymaganiami. Podstawowe dyski platformy Azure są tworzone jako żądania dla nich. W definicji poniżej Zażądaj utworzenia woluminu i dołączenia go do określonej ścieżki instalacji.

Aby uzyskać informacje na temat sposobu dynamicznego tworzenia i używania woluminów, zobacz [oświadczenia dotyczące woluminów trwałych][aks-concepts-storage-pvcs].

Aby wyświetlić te woluminy, zobacz sposób dynamicznego tworzenia i używania woluminu trwałego z dyskami lub [Azure Files][dynamic-files] [platformy Azure][dynamic-disks] .

W ramach definicji klasy magazynu Ustaw odpowiednie *reclaimPolicy*. Ta reclaimPolicy kontroluje zachowanie bazowego zasobu usługi Azure Storage po usunięciu elementu. Podstawowy zasób magazynu można usunąć lub zachować do użytku w przyszłości. Ustaw reclaimPolicy, aby *zachować* lub *usunąć*. 

Zapoznaj się z wymaganiami aplikacji i zaimplementuj regularne sprawdzanie magazynu przechowywanego w celu zminimalizowania ilości niewykorzystanego i rozliczanego magazynu.

Aby uzyskać więcej informacji o opcjach klasy magazynu, zobacz [zasady odzyskiwania magazynu][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpieczanie i tworzenie kopii zapasowych danych

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Utwórz kopię zapasową danych przy użyciu odpowiedniego narzędzia dla typu magazynu, takiego jak Velero lub Azure Backup. Sprawdź integralność i bezpieczeństwo tych kopii zapasowych.

Gdy aplikacje przechowują i zużywają dane utrwalane na dyskach lub w plikach, należy wykonywać regularne kopie zapasowe lub migawki tych danych. Na dyskach platformy Azure można używać wbudowanych technologii migawek. Aby można było wykonać operację migawki, aplikacje mogą wymagać opróżnienia zapisów na dysku. [Velero][velero] może tworzyć kopie zapasowe woluminów trwałych wraz z dodatkowymi zasobami i konfiguracjami klastra. Jeśli nie możesz [usunąć stanu z aplikacji][remove-state], Utwórz kopię zapasową danych z woluminów trwałych i regularnie Przetestuj operacje przywracania, aby zweryfikować integralność danych i wymagane procesy.

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
