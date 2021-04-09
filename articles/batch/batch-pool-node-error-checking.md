---
title: Sprawdź, czy występują błędy puli i węzłów
description: W tym artykule opisano operacje w tle, które mogą wystąpić, a także błędy do sprawdzenia i sposoby ich unikania podczas tworzenia pul i węzłów.
author: mscurrell
ms.author: markscu
ms.date: 03/15/2021
ms.topic: how-to
ms.openlocfilehash: 86ea4ce4d596875e455d7b86250882713a14337f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720155"
---
# <a name="check-for-pool-and-node-errors"></a>Sprawdź, czy występują błędy puli i węzłów

Podczas tworzenia pul Azure Batch i zarządzania nimi, niektóre operacje są wykonywane natychmiast. Wykrywanie błędów dla tych operacji jest zwykle bezpośrednie, ponieważ są zwracane bezpośrednio przez interfejs API, interfejsu wiersza polecenia lub interfejsu użytkownika. Niektóre operacje są jednak asynchroniczne i uruchamiane w tle, co trwa kilka minut.

Sprawdź, czy ustawiono aplikacje, aby zaimplementować kompleksowe sprawdzanie błędów, szczególnie w przypadku operacji asynchronicznych. Może to ułatwić natychmiastowe zidentyfikowanie i zdiagnozowanie problemów.

W tym artykule opisano sposób wykrywania i unikania niepowodzeń w operacjach w tle, które mogą wystąpić w przypadku pul i węzłów puli.

## <a name="pool-errors"></a>Błędy puli

### <a name="resize-timeout-or-failure"></a>Zmień limit czasu lub niepowodzenie

Podczas tworzenia nowej puli lub zmiany rozmiarów istniejącej puli należy określić docelową liczbę węzłów. Operacja tworzenia lub zmiany rozmiaru zostanie zakończona natychmiast, ale rzeczywista alokacja nowych węzłów lub usunięcie istniejących węzłów może potrwać kilka minut. Limit czasu można określić w interfejsie API [tworzenia](/rest/api/batchservice/pool/add) lub [zmiany rozmiaru](/rest/api/batchservice/pool/resize) . Jeśli w usłudze Batch nie można uzyskać docelowej liczby węzłów w okresie limitu czasu, Pula przechodzi w stan stały i raporty błędów zmieniania rozmiaru.

Właściwość [ResizeError](/rest/api/batchservice/pool/get#resizeerror) dla najnowszej oceny zawiera listę błędów, które wystąpiły.

Typowe przyczyny błędów zmiany rozmiaru obejmują:

- Limit czasu zmiany rozmiaru jest za krótki
  - W większości przypadków domyślny limit czasu wynoszący 15 minut jest wystarczająco długi dla węzłów puli do przydzielenia lub usunięcia.
  - W przypadku przydzielania dużej liczby węzłów zalecamy ustawienie limitu czasu na 30 minut. Na przykład po zmianie wielkości na więcej niż 1 000 węzłów z obrazu portalu Azure Marketplace lub do ponad 300 węzłów z niestandardowego obrazu maszyny wirtualnej.
- Niewystarczający przydział rdzeni
  - Konto w usłudze Batch jest ograniczone do liczby rdzeni, które można przydzielić między wszystkimi pulami. Zadanie wsadowe przerywa alokowanie węzłów po osiągnięciu limitu przydziału. [Możesz zwiększyć](./batch-quota-limit.md) limit przydziału rdzeni, aby zadanie wsadowe mogły przydzielić więcej węzłów.
- Za mało adresów IP podsieci, gdy [Pula znajduje się w sieci wirtualnej](./batch-virtual-network.md)
  - Podsieć sieci wirtualnej musi mieć wystarczającą liczbę nieprzypisanych adresów IP do przydzielenia do każdego żądanego węzła puli. W przeciwnym razie nie można utworzyć węzłów.
- Niewystarczające zasoby, gdy [Pula znajduje się w sieci wirtualnej](./batch-virtual-network.md)
  - Możesz tworzyć zasoby, takie jak moduły równoważenia obciążenia, publiczne adresy IP i sieciowe grupy zabezpieczeń w tej samej subskrypcji, co konto usługi Batch. Sprawdź, czy limity przydziału subskrypcji są wystarczające dla tych zasobów.
- Duże pule z niestandardowymi obrazami maszyn wirtualnych
  - Duże pule, które używają niestandardowych obrazów maszyn wirtualnych, mogą być dłużej przydzielane i zmieniać limity czasu.  Zapoznaj się z tematem [Tworzenie puli za pomocą galerii obrazów udostępnionych](batch-sig-images.md) w celu uzyskania zaleceń dotyczących ograniczeń i konfiguracji.

### <a name="automatic-scaling-failures"></a>Automatyczne skalowanie błędów

Można ustawić Azure Batch, aby automatycznie skalować liczbę węzłów w puli. Należy zdefiniować parametry dla [formuły automatycznego skalowania dla puli](./batch-automatic-scaling.md). Usługa Batch użyje formuły, aby okresowo oszacować liczbę węzłów w puli i ustawić nowy numer docelowy.

W przypadku korzystania z automatycznego skalowania mogą wystąpić następujące typy problemów:

- Obliczanie automatycznego skalowania kończy się niepowodzeniem.
- Operacja powodująca zmianę rozmiaru kończy się niepowodzeniem i zostanie przeprowadzona.
- Problem z formułą automatycznego skalowania prowadzi do nieprawidłowych wartości docelowych węzła. Zmiana rozmiaru jest wykonywana lub przeprowadzona w czasie.

Aby uzyskać informacje na temat ostatniej oceny automatycznego skalowania, należy użyć właściwości [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) . Ta właściwość służy do raportowania czasu oceny, wartości i wyniku oraz ewentualnych błędów wydajności.

[Zdarzenie ukończenia zmiany rozmiaru puli](./batch-pool-resize-complete-event.md) przechwytuje informacje o wszystkich obliczeniach.

### <a name="pool-deletion-failures"></a>Błędy usuwania puli

Po usunięciu puli zawierającej węzły, pierwsza partia danych Usuwa węzły. Może to potrwać kilka minut. Po wykonaniu tej operacji partia usuwa obiekt puli.

Wsadowe ustawia [stan puli](/rest/api/batchservice/pool/get#poolstate) do **usunięcia** podczas procesu usuwania. Aplikacja wywołująca może wykryć, czy usunięcie puli trwa zbyt długo przy użyciu właściwości **State** i **stateTransitionTime** .

Jeśli pula trwa dłużej niż oczekiwano, przetwarzanie wsadowe będzie ponawiać próbę w celu pomyślnego usunięcia puli. W niektórych przypadkach opóźnienie jest spowodowane awarią usługi platformy Azure lub innymi tymczasowymi problemami. Inne czynniki, które mogą uniemożliwiać pomyślne usunięcie puli, mogą wymagać podjęcia działań w celu rozwiązania problemu. Są to następujące czynniki:

- Blokady zasobów zostały umieszczone w zasobach utworzonych wsadowo lub w zasobach sieciowych używanych przez program Batch.
- Utworzone zasoby są zależne od zasobu utworzonego wsadowo. Jeśli na przykład [utworzysz pulę w sieci wirtualnej](batch-virtual-network.md), usługa Batch tworzy sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń), publiczny adres IP i moduł równoważenia obciążenia. W przypadku używania tych zasobów poza pulą nie można usunąć puli, dopóki ta zależność nie zostanie usunięta.
- Dostawca zasobów Microsoft.Batch został wyrejestrowany z subskrypcji zawierającej daną pulę.
- "Microsoft Azure Batch" nie ma już [roli współautor ani właściciela](batch-account-create-portal.md#allow-azure-batch-to-access-the-subscription-one-time-operation) do subskrypcji zawierającej pulę (dla kont usługi Batch w trybie subskrypcji użytkownika).

## <a name="node-errors"></a>Błędy węzła

Nawet w przypadku pomyślnego przydzielenia węzłów w puli różne problemy mogą spowodować, że niektóre węzły są w złej kondycji i nie mogą uruchamiać zadań. W tych węzłach nadal są naliczane opłaty, dlatego ważne jest, aby wykrywać problemy, aby uniknąć płacenia za węzły, które nie mogą być używane. Oprócz typowych błędów węzłów, wiedząc, że bieżący [stan zadania](/rest/api/batchservice/job/get#jobstate) jest przydatny do rozwiązywania problemów.

### <a name="start-task-failures"></a>Błędy uruchamiania zadań

Może być konieczne określenie opcjonalnego zadania podrzędnego [uruchamiania](/rest/api/batchservice/pool/add#starttask) dla puli. Podobnie jak w przypadku każdego zadania, można użyć wiersza polecenia i plików zasobów do pobrania z magazynu. Zadanie uruchamiania jest uruchamiane dla każdego węzła po jego uruchomieniu. Właściwość **waitForSuccess** określa, czy zadanie wsadowe ma czekać do momentu zakończenia zadania uruchamiania przed zaplanowaniem wykonywania zadań w węźle.

Co zrobić, jeśli węzeł został skonfigurowany tak, aby poczekać na zakończenie zadania uruchamiania, ale zadanie uruchamiania zakończy się niepowodzeniem? W takim przypadku węzeł nie będzie użyteczny, ale nadal będzie naliczane opłaty.

Można wykryć nieudane zadania uruchamiania przy użyciu właściwości [wynik](/rest/api/batchservice/computenode/get#taskexecutionresult) i  [FailureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) właściwości węzła [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) najwyższego poziomu.

Zadanie uruchamiania zakończone niepowodzeniem powoduje także ustawienie [stanu](/rest/api/batchservice/computenode/get#computenodestate) węzła na **Starttaskfailed** , jeśli  **waitForSuccess** został ustawiony na **wartość true**.

Podobnie jak w przypadku każdego zadania, może istnieć wiele przyczyn niepowodzenia zadania uruchamiania. Aby rozwiązać problem, sprawdź plik stdout, stderr i wszelkie dalsze pliki dzienników specyficzne dla zadania.

Zadania uruchamiania muszą być ponownie współużytkowane, ponieważ możliwe jest uruchamianie zadania uruchamiania wiele razy w tym samym węźle. zadanie uruchamiania jest uruchamiane po odinstalowaniu lub ponownym uruchomieniu węzła. W rzadkich przypadkach zadanie uruchamiania zostanie uruchomione po zdarzeniu powodującym ponowne uruchomienie węzła, gdzie jeden z systemów operacyjnych lub dysków tymczasowych został odtworzony, a drugi nie. Ponieważ zadania uruchamiania wsadowego (takie jak wszystkie zadania wsadowe) są uruchamiane z dysku tymczasowych, nie jest to zazwyczaj problemem, ale w niektórych przypadkach, gdy zadanie uruchamiania instaluje aplikację na dysku systemu operacyjnego i przechowując inne dane na dysku tymczasowych, może to spowodować problemy, ponieważ elementy nie są zsynchronizowane. Odpowiednio Chroń aplikację, jeśli używasz obu dysków.

### <a name="application-package-download-failure"></a>Niepowodzenie pobierania pakietu aplikacji

Dla puli można określić jeden lub więcej pakietów aplikacji. Program Batch pobiera określone pliki pakietu do każdego węzła i dekompresuje pliki po rozpoczęciu działania węzła, ale przed zaplanowaniem zadań. Często należy używać wiersza polecenia Uruchom zadanie w połączeniu z pakietami aplikacji. Na przykład, aby skopiować pliki do innej lokalizacji lub uruchomić Instalatora.

Właściwość [Błędy](/rest/api/batchservice/computenode/get#computenodeerror) węzła zgłasza niepowodzenie pobrania i cofnięcia kompresji pakietu aplikacji; stan węzła jest ustawiony na **niezdatny do użytku**.

### <a name="container-download-failure"></a>Niepowodzenie pobierania kontenera

W puli można określić co najmniej jedno odwołanie do kontenera. Wsadowe pobiera określone kontenery do każdego węzła. Właściwość [Błędy](/rest/api/batchservice/computenode/get#computenodeerror) węzła zgłasza niepowodzenie pobrania kontenera i ustawia stan węzła na **niezdatny do użytku**.

### <a name="node-os-updates"></a>Aktualizacje węzła systemu operacyjnego

W przypadku pul systemu Windows program `enableAutomaticUpdates` jest domyślnie ustawiony na wartość `true` . Zezwalanie na aktualizacje automatyczne jest zalecane, ale może przerywać postęp zadania, zwłaszcza jeśli zadania są długotrwałe. Tę wartość można ustawić na `false` , jeśli chcesz mieć pewność, że aktualizacja systemu operacyjnego nie wystąpi nieoczekiwanie.

### <a name="node-in-unusable-state"></a>Węzeł w stanie niezdatnym do użytku

Azure Batch może ustawić [stan węzła](/rest/api/batchservice/computenode/get#computenodestate) na **niezdatny do użytku** z wielu powodów. Gdy stan węzła jest ustawiony na **niezdatny do użytku**, zadania nie mogą być zaplanowane do węzła, ale nadal są naliczane opłaty.

Węzły w stanie **niezdatnym do użytku** , ale bez [błędów](/rest/api/batchservice/computenode/get#computenodeerror) oznacza, że partia nie może komunikować się z maszyną wirtualną. W takim przypadku program Batch zawsze próbuje odzyskać maszynę wirtualną. Funkcja Batch nie będzie automatycznie podejmować prób odzyskania maszyn wirtualnych, których nie udało się zainstalować pakietów lub kontenerów aplikacji, nawet jeśli ich stan nie **nadaje się do użytku**.

Jeśli partia może ustalić przyczynę, właściwość [Błędy](/rest/api/batchservice/computenode/get#computenodeerror) węzła zgłasza ją.

Dodatkowe przykłady przyczyn **nieużytecznych** węzłów obejmują:

- Niestandardowy obraz maszyny wirtualnej jest nieprawidłowy. Na przykład obraz, który nie jest prawidłowo przygotowany.
- Maszyna wirtualna została przeniesiona z powodu awarii infrastruktury lub uaktualnienia niskiego poziomu. Partia odzyskuje węzeł.
- Obraz maszyny wirtualnej został wdrożony na sprzęcie, który go nie obsługuje. Na przykład próba uruchomienia obrazu CentOS HPC na maszynie wirtualnej [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) .
- Maszyny wirtualne znajdują się w [sieci wirtualnej platformy Azure](batch-virtual-network.md), a ruch został zablokowany do kluczowych portów.
- Maszyny wirtualne znajdują się w sieci wirtualnej, ale ruch wychodzący do usługi Azure Storage jest blokowany.
- Maszyny wirtualne znajdują się w sieci wirtualnej z konfiguracją DNS klienta, a serwer DNS nie może rozpoznać usługi Azure Storage.

### <a name="node-agent-log-files"></a>Pliki dziennika agenta węzła

Proces agenta wsadowego, który jest uruchamiany w poszczególnych węzłach puli, może dostarczyć pliki dziennika, które mogą być przydatne, jeśli trzeba skontaktować się z pomocą techniczną dotyczącą problemu z węzłem puli. Pliki dziennika dla węzła można przekazać za pomocą Azure Portal, Batch Explorer lub [interfejsu API](/rest/api/batchservice/computenode/uploadbatchservicelogs). Warto przekazać i zapisać pliki dziennika. Następnie można usunąć węzeł lub pulę, aby zaoszczędzić koszt uruchomionych węzłów.

### <a name="node-disk-full"></a>Dysk węzła jest pełny

Dysk tymczasowy dla maszyny wirtualnej węzła puli jest używany przez partię dla plików zadań, plików zadań i plików udostępnionych, takich jak następujące:

- Pliki pakietów aplikacji
- Pliki zasobów zadań
- Pliki specyficzne dla aplikacji pobrane do jednego z folderów wsadowych
- Pliki stdout i stderr dla każdego zadania wykonywania aplikacji
- Pliki wyjściowe specyficzne dla aplikacji

Niektóre z tych plików są zapisywane tylko raz podczas tworzenia węzłów puli, takich jak pakiety aplikacji puli lub pliki zasobów zadania uruchamiania puli. Nawet jeśli tylko raz podczas tworzenia węzła, jeśli te pliki są zbyt duże, mogą wypełniać dysk tymczasowy.

Inne pliki są zapisywane dla każdego zadania uruchamianego w węźle, na przykład stdout i stderr. Jeśli duża liczba zadań uruchomionych w tym samym węźle i/lub pliki zadań są zbyt duże, może to spowodować wypełnienie dysku tymczasowego.

Ponadto po rozpoczęciu węzła na dysku systemu operacyjnego potrzeba niewielkiej ilości miejsca, aby utworzyć użytkowników.

Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Należy wziąć pod uwagę podczas wybierania rozmiaru maszyny wirtualnej, aby zapewnić, że dysk tymczasowy ma wystarczającą ilość miejsca na potrzeby planowanego obciążenia.

- W Azure Portal podczas dodawania puli zostanie wyświetlona pełna lista rozmiarów maszyn wirtualnych, w której jest dostępna kolumna "rozmiar dysku zasobu".
- Artykuły opisujące wszystkie rozmiary maszyn wirtualnych mają tabele z kolumną "temp Storage"; na przykład [rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń](../virtual-machines/sizes-compute.md)

W przypadku plików pisanych przez każde zadanie można określić czas przechowywania dla każdego zadania, które określa, jak długo pliki zadań są przechowywane przed automatycznym wyczyszczeniem. Czas przechowywania można zmniejszyć, aby zmniejszyć wymagania dotyczące magazynu.

Jeśli dysk tymczasowy lub system operacyjny wyczerpie miejsce (lub jest bardzo blisko wolnego miejsca), węzeł przejdzie w stan [niezdatny do użytku](/rest/api/batchservice/computenode/get#computenodestate) i zostanie zgłoszony błąd węzła, informując o zapełnieniu dysku.

Jeśli nie masz pewności, co zajmuje miejsce w węźle, spróbuj przeprowadzić komunikację zdalną z węzłem i przebadać ręcznie miejsce, gdzie został usunięty. Można również użyć [interfejsu API plików list usługi Batch](/rest/api/batchservice/file/listfromcomputenode) do sprawdzenia plików w folderach zarządzanych wsadowo (na przykład w danych wyjściowych zadania). Należy zauważyć, że ten interfejs API wyświetla listę tylko plików w katalogach zarządzanych przez usługi Batch. Jeśli zadania utworzone w innym miejscu, nie będą widoczne.

Upewnij się, że wszystkie dane, które są potrzebne, zostały pobrane z węzła lub przekazane do magazynu trwałego, a następnie Usuń dane zgodnie z potrzebami, aby zwolnić miejsce.

Istnieje możliwość usunięcia starych zadań zakończonych lub starych ukończonych zadań, których dane zadań nadal znajdują się w węzłach. Poszukaj w [kolekcji RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) na węźle lub w [plikach w węźle](/rest/api/batchservice/file/listfromcomputenode). Usunięcie zadania spowoduje usunięcie wszystkich zadań z zadania; usunięcie zadań z zadania spowoduje wyzwolenie danych w katalogach zadań w węźle, który ma zostać usunięty, w ten sposób Zwolnij miejsce. Po zwolnieniu wystarczającej ilości miejsca, uruchom ponownie węzeł i nie powinna zostać przeniesiona z stanu "bez użycia" do "bezczynne".

Aby odzyskać węzeł niezdatny do użytku w pulach [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) , można usunąć węzeł z puli za pomocą [interfejsu API usuwania węzłów](/rest/api/batchservice/pool/removenodes). Następnie można ponownie zwiększyć pulę, aby zastąpić zły węzeł nowym. W przypadku pul [CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) można odtworzyć węzeł przy użyciu [interfejsu API ponownego obrazu usługi Batch](/rest/api/batchservice/computenode/reimage). Spowoduje to wyczyszczenie całego dysku. Ponowne tworzenie obrazu nie jest obecnie obsługiwane w przypadku pul [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sprawdzaniu błędów zadań i zadań](batch-job-task-error-checking.md).
- Poznaj [najlepsze rozwiązania](best-practices.md) dotyczące pracy z Azure Batchami.
