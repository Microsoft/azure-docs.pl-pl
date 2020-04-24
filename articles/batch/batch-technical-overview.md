---
title: Azure Batch uruchamia duże zadania równoległe w chmurze
description: Opis korzystania z usługi Azure Batch na potrzeby dużych obciążeń równoległych oraz HPC
ms.topic: overview
ms.date: 01/19/2018
ms.openlocfilehash: f2bdaeec47b50b715920b27d6adf6e078a354964
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116353"
---
# <a name="what-is-azure-batch"></a>Co to jest Azure Batch?

Usługa Azure Batch umożliwia wydajne uruchamianie równoległych zadań wsadowych oraz zadań wsadowych obliczeń o wysokiej wydajności na platformie Azure. Usługa Azure Batch tworzy pulę węzłów obliczeniowych (maszyn wirtualnych) i zarządza nią, instaluje aplikacje, które chcesz uruchamiać, i tworzy harmonogram zadań do uruchomienia na węzłach. Nie ma żadnego oprogramowania klastra lub harmonogramu zadań, które trzeba instalować i skalować oraz którym trzeba zarządzać. Zamiast tego korzysta się z [interfejsu API usługi Batch i narzędzi](batch-apis-tools.md), skryptów wiersza polecenia lub witryny Azure Portal, aby konfigurować i monitorować zadania oraz zarządzać nimi. 

Deweloperzy mogą używać usługi Batch jako usługi platformy do tworzenia aplikacji SaaS lub aplikacji klienckich, dla których wymagane jest wykonywanie na dużą skalę. Przykładowo za pomocą usługi Batch można utworzyć usługę do symulowania ryzyka metodą Monte Carlo dla firmy świadczącej usługi finansowe lub usługę do przetwarzania dużej liczby obrazów.

Za korzystanie z usługi Batch nie są naliczane dodatkowe opłaty. Płaci się wyłącznie za wykorzystane zasoby bazowe, takie jak maszyny wirtualne, magazyn i zasoby sieciowe.

Aby uzyskać porównanie między usługą Batch i innymi opcjami rozwiązania HPC na platformie Azure, zobacz [wydajność obliczeń o wysokiej wydajności (HPC) na platformie Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).

## <a name="run-parallel-workloads"></a>Uruchamianie równoległych obciążeń
Z usługą Batch działają dobrze obciążenia wewnętrznie równoległe (zwane również „zaskakująco równoległymi”). Obciążenia wewnętrznie równoległe to te, w przypadku których aplikacje mogą być uruchamiane równolegle i każde wystąpienie wykonuje część pracy. Aplikacje w trakcie wykonywania mogą uzyskiwać dostęp do niektórych wspólnych danych, ale nie komunikują się z innymi wystąpieniami aplikacji. Z tego względu obciążenia wewnętrznie równoległe mogą być uruchamiane na dużą skalę, zależną od ilości zasobów obliczeniowych dostępnych do jednoczesnego uruchamiania aplikacji.

Oto przykłady obciążeń wewnętrznie równoległych, które można uruchamiać w usłudze Batch:

* Modelowanie ryzyka finansowego za pomocą symulacji Monte Carlo
* Renderowanie filmowych efektów specjalnych i obrazów 3D
* Analiza i przetwarzanie obrazów
* Transkodowanie multimediów
* Analiza sekwencji genetycznych
* Optyczne rozpoznawanie znaków (OCR)
* Pozyskiwanie i przetwarzanie danych oraz operacje ETL
* Wykonywanie testów oprogramowania

Usługi Batch można również używać do [uruchamiania ściśle sprzężonych obciążeń](batch-mpi.md). Są to obciążenia, w których uruchamiane aplikacje muszą komunikować się ze sobą, co jest przeciwieństwem uruchamiania niezależnego. Ściśle sprzężone aplikacje zwykle korzystają z interfejsu API o nazwie Message Passing Interface (MPI). Ściśle sprzężone obciążenia można uruchamiać w usłudze Batch za pomocą interfejsu [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) lub Intel MPI. Wydajność aplikacji można zwiększyć, używając specjalizowanych rozmiarów maszyn wirtualnych, przeznaczonych do obliczeń [HPC](../virtual-machines/linux/sizes-hpc.md) i [zoptymalizowanych pod kątem procesora GPU](../virtual-machines/linux/sizes-gpu.md).

Przykłady ściśle sprzężonych obciążeń:
* Analiza elementów skończonych
* Dynamika cieczy
* Wielowęzłowe szkolenie sztucznej inteligencji

Wiele ściśle sprzężonych zadań można uruchamiać równolegle za pomocą usługi Batch. Można na przykład przeprowadzić wiele symulacji cieczy płynącej przez rurę przy różnych średnicach rury.

## <a name="additional-batch-capabilities"></a>Dodatkowe możliwości usługi Batch

Usługa Azure Batch cechuje się również możliwościami wyższego poziomu zależnymi od obciążenia:
* Usługa Batch obsługuje [obciążenia renderowania](batch-rendering-service.md) na dużą skalę przy użyciu narzędzi do renderowania, takich jak Autodesk Maya, 3ds Max, Arnold i V-Ray. 
* Użytkownicy języka R mogą instalować [pakiet języka R doAzureParallel](https://github.com/Azure/doAzureParallel), aby łatwo skalować w poziomie algorytmy języka R na pule usługi Batch.

Zadania usługi Batch można też uruchamiać jako część większego przepływu pracy platformy Azure do przekształcania danych, który jest zarządzany przez narzędzia takie jak [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md).


## <a name="how-it-works"></a>Jak to działa
Typowy scenariusz korzystania z usługi Batch obejmuje skalowanie w poziomie pracy wewnętrznie równoległej — np. renderowania obrazów dla scen 3D — w puli węzłów obliczeniowych. Ta pula węzłów obliczeniowych może być „farmą renderowania”, która udostępnia dziesiątki, setki lub nawet tysiące rdzeni do zadania renderowania.

Na poniższym diagramie przedstawiono kroki typowego przepływu pracy usługi Batch, w którym aplikacja kliencka lub usługa hostowana używa usługi Batch do uruchamiania obciążenia równoległego.

![Przewodnik po rozwiązaniu usługi Batch](./media/batch-technical-overview/tech_overview_03.png)


|Krok  |Opis  |
|---------|---------|
|1. Przekaż **pliki wejściowe** i **aplikacje** , aby przetworzyć te pliki na konto usługi Azure Storage.     |Pliki wejściowe mogą zawierać dowolne dane, które aplikacja może przetworzyć, np. dane modelowania finansowego lub pliki wideo do transkodowania. Pliki aplikacji mogą obejmować skrypty lub aplikacje przetwarzające dane, na przykład transkoder multimediów.|
|2. Utwórz **pulę** wsadową węzłów obliczeniowych na koncie wsadowym, **zadanie** uruchamiania obciążenia puli i **zadania** podrzędne w ramach zadania.     | Węzły puli to maszyny wirtualne wykonujące zadania podrzędne. Określ właściwości, takie jak liczba i rozmiar węzłów, obraz maszyny wirtualnej z systemem Windows lub Linux oraz aplikacja do zainstalowania, gdy węzły zostaną przyłączone do puli. Zarządzaj kosztem i rozmiarem puli, używając [maszyn wirtualnych o niskim priorytecie](batch-low-pri-vms.md) lub [automatycznego skalowania](batch-automatic-scaling.md) liczby węzłów w miarę zmian obciążenia. <br/><br/>Po dodaniu podzadań do zadania usługa Batch automatycznie planuje wykonanie podzadań w węzłach obliczeniowych w puli. Każde podzadanie używa przekazanej aplikacji w celu przetwarzania plików wejściowych. |
|3. pobieranie **plików wejściowych** i **aplikacji** do partii     |Zanim każde zadanie podrzędne zostanie wykonane, może pobrać dane wejściowe, które ma przetworzyć, do przypisanego węzła obliczeniowego. Jeśli aplikacja jeszcze nie została zainstalowana na węzłach puli, można ją pobrać w tym miejscu. Po zakończeniu operacji pobierania z usługi Azure Storage zadanie podrzędne jest wykonywane na przypisanym węźle.|
|4. Monitoruj **wykonywanie zadań**     |Po uruchomieniu zadań podrzędnych wykonaj zapytanie do usługi Batch w celu monitorowania postępu zadania oraz jego zadań podrzędnych. Aplikacja lub usługa kliencka komunikuje się z usługą Batch za pośrednictwem protokołu HTTPS. Ponieważ monitorowane mogą być tysiące podzadań uruchomionych w ramach tysięcy węzłów obliczeniowych, upewnij się, że [zapytanie względem usługi Batch jest wydajne](batch-efficient-list-queries.md).|
|5. przekazywanie **danych wyjściowych zadania**     |Gdy podzadania zostaną ukończone, mogą przekazać dane wynikowe do usługi Azure Storage. Możesz również pobrać pliki bezpośrednio z systemu plików w ramach węzła obliczeniowego.|
|6. pobieranie **plików wyjściowych**     |Jeśli podczas monitorowania zostanie wykryte, że zadania podrzędne w zadaniu zostały ukończone, aplikacja lub usługa kliencka może pobrać dane wyjściowe do dalszego przetwarzania.|




Pamiętaj, że jest to tylko jeden sposób korzystania z usługi Batch, a w tym scenariuszu opisano tylko część jej dostępnych funkcji. Na przykład można uruchamiać [wiele zadań podrzędnych równolegle](batch-parallel-node-tasks.md) na każdym węźle obliczeniowym. Można też użyć [zadań podrzędnych przygotowywania i kończenia zadań](batch-job-prep-release.md), aby przygotować węzły dla swoich zadań, a po wszystkim je wyczyścić. 

Zobacz [Omówienie usługi Batch dla deweloperów](batch-api-basics.md), aby zapoznać się z bardziej szczegółowymi informacjami o pulach, węzłach, zadaniach i zadaniach podrzędnych oraz wielu funkcjach API, których można używać podczas tworzenia aplikacji usługi Batch. Zobacz też najnowsze [aktualizacje usługi Batch](https://azure.microsoft.com/updates/?product=batch).

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z usługą Azure Batch, korzystając z jednego z tych przewodników Szybki start:
* [Uruchamianie pierwszego zadania usługi Batch za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
* [Uruchamianie pierwszego zadania usługi Batch w witrynie Azure Portal](quick-create-portal.md)
* [Uruchamianie pierwszego zadania usługi Batch za pomocą interfejsu .NET API](quick-run-dotnet.md)
* [Uruchamianie pierwszego zadania usługi Batch za pomocą interfejsu Python API](quick-run-python.md)

