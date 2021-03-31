---
title: Kopiuj aplikacje i dane do węzłów puli
description: Dowiedz się, jak kopiować aplikacje i dane do węzłów puli.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703651"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Kopiuj aplikacje i dane do węzłów puli

Azure Batch obsługuje kilka sposobów pobierania danych i aplikacji do węzłów obliczeniowych, dzięki czemu są one dostępne do użycia przez zadania.

Wybrana metoda może zależeć od zakresu pliku lub aplikacji. Do uruchomienia całego zadania mogą być wymagane dane i aplikacje, dlatego należy je zainstalować na każdym węźle. Niektóre pliki lub aplikacje mogą być wymagane tylko w konkretnym zadaniu. Inne osoby mogą wymagać zainstalowania dla zadania, ale nie muszą znajdować się na każdym węźle. Zadanie wsadowe zawiera narzędzia dla każdego z tych scenariuszy.

## <a name="determine-the-scope-required-of-a-file"></a>Określanie zakresu wymaganego przez plik

Należy określić zakres pliku — jest to plik wymagany dla puli, zadania lub zadania. Pliki należące do zakresu puli powinny używać pakietów aplikacji puli lub zadania uruchamiania. Pliki należące do zakresu zadania powinny używać zadania przygotowania zadania. Dobrym przykładem plików objętych zakresem puli lub poziomu zadania są aplikacje. Pliki należące do zakresu zadania powinny używać plików zasobów zadania.

## <a name="pool-start-task-resource-files"></a>Pliki zasobów zadania uruchamiania puli

W przypadku aplikacji lub danych, które muszą być zainstalowane na każdym węźle w puli, Użyj plików zasobów zadanie uruchamiania puli. Użyj tej metody wraz z [pakietem aplikacji](batch-application-packages.md) lub kolekcją plików zasobów zadania podrzędnego, aby wykonać polecenie instalacji.  

Na przykład można użyć wiersza polecenia Uruchom zadanie, aby przenieść lub zainstalować aplikacje. Możesz również określić listę plików lub kontenerów na koncie usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Dodawanie # ResourceFile w dokumentacji REST](/rest/api/batchservice/pool/add#resourcefile).

Jeśli każde zadanie uruchamiane w puli uruchamia aplikację (. exe), która musi zostać najpierw zainstalowana z plikiem. msi, należy ustawić **wartość true** **dla** właściwości Uruchom zadanie uruchamiania. Aby uzyskać więcej informacji, zobacz [Dodawanie # StartTask w dokumentacji REST](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Odwołania do pakietu aplikacji

W przypadku aplikacji lub danych, które muszą być zainstalowane na każdym węźle w puli, należy rozważyć użycie [pakietów aplikacji](batch-application-packages.md). Nie istnieje polecenie instalacji skojarzone z pakietem aplikacji, ale można użyć zadania uruchamiania do uruchomienia dowolnego polecenia instalacji. Jeśli aplikacja nie wymaga instalacji lub składa się z dużej liczby plików, można użyć tej metody.

Pakiety aplikacji są przydatne, gdy masz dużą liczbę plików, ponieważ mogą one łączyć wiele odwołań do plików w małym ładunku. Jeśli spróbujesz dołączyć więcej niż 100 oddzielnych plików zasobów w jednym zadaniu, usługa Batch może zostać przydzielona do ograniczeń systemu wewnętrznego dla jednego zadania. Pakiety aplikacji są również przydatne, gdy masz wiele różnych wersji tej samej aplikacji i musisz wybrać między nimi.

## <a name="job-preparation-task-resource-files"></a>Pliki zasobów zadania przygotowania zadania

W przypadku aplikacji lub danych, które muszą być zainstalowane, aby zadanie zostało uruchomione, ale nie trzeba go instalować w całej puli, należy rozważyć użycie [plików zasobów zadania przygotowania zadania](./batch-job-prep-release.md).

Na przykład jeśli Pula ma wiele różnych typów zadań, a tylko jeden typ zadania wymaga pliku. msi do uruchomienia, warto umieścić krok instalacji w zadaniu przygotowania zadania.

## <a name="task-resource-files"></a>Pliki zasobów zadań

Pliki zasobów zadań są odpowiednie, gdy aplikacja lub dane mają zastosowanie tylko do pojedynczego zadania.

Na przykład może istnieć pięć zadań, każdy przetwarza inny plik, a następnie zapisuje dane wyjściowe do magazynu obiektów BLOB w tym przypadku, plik wejściowy należy określić w kolekcji plików zasobów zadania, ponieważ każde zadanie ma własny plik wejściowy.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Dodatkowe sposoby pobierania danych do węzłów

Ponieważ masz kontrolę nad węzłami Azure Batch i można uruchamiać niestandardowe pliki wykonywalne, możesz pobierać dane z dowolnej liczby źródeł niestandardowych. Upewnij się, że węzeł usługi Batch ma łączność z elementem docelowym i że masz poświadczenia do tego źródła w węźle.

Kilka przykładów metod transferu danych do węzłów wsadowych:

- Pobieranie danych z serwera SQL
- Pobieranie danych z innych usług sieci Web/lokalizacji niestandardowych
- Mapowanie udziału sieciowego

## <a name="azure-storage"></a>Azure Storage

Należy pamiętać, że magazyn obiektów BLOB ma elementy docelowe skalowalności. Elementy docelowe skalowalności udziału plików w usłudze Azure Storage są takie same jak w przypadku pojedynczego obiektu BLOB. Rozmiar będzie mieć wpływ na wymaganą liczbę węzłów i pul.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o używaniu [pakietów aplikacji w usłudze Batch](batch-application-packages.md).
- Dowiedz się więcej o [pracy z węzłami i pulami](nodes-and-pools.md).
