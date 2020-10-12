---
title: Kopiuj aplikacje i dane do węzłów puli
description: Dowiedz się, jak kopiować aplikacje i dane do węzłów puli.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954897"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Kopiuj aplikacje i dane do węzłów puli

Azure Batch obsługuje kilka sposobów pobierania danych i aplikacji do węzłów obliczeniowych, dzięki czemu dane i aplikacje są dostępne do użycia przez zadania. Do uruchomienia całego zadania mogą być wymagane dane i aplikacje, dlatego należy je zainstalować na każdym węźle. Niektóre mogą być wymagane tylko dla konkretnego zadania lub muszą być zainstalowane dla zadania, ale nie muszą znajdować się w każdym węźle. Zadanie wsadowe zawiera narzędzia dla każdego z tych scenariuszy.

- **Pliki zasobów zadania uruchamiania puli**: dla aplikacji lub danych, które muszą być zainstalowane na każdym węźle w puli. Użyj tej metody wraz z pakietem aplikacji lub kolekcją plików zasobów zadania podrzędnego, aby wykonać polecenie instalacji.  

Przykłady: 
- Użyj wiersza polecenia Uruchom zadanie, aby przenieść lub zainstalować aplikacje

- Określ listę określonych plików lub kontenerów na koncie usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Dodawanie # resourcefile w dokumentacji REST](/rest/api/batchservice/pool/add#resourcefile)

- Każde zadanie uruchamiane w puli działa MyApplication.exe, które należy najpierw zainstalować przy użyciu MyApplication.msi. W przypadku korzystania z tego mechanizmu należy ustawić **wartość true** **dla** właściwości Uruchom zadanie uruchamiania. Aby uzyskać więcej informacji, zobacz [Dodawanie # startTask w dokumentacji REST](/rest/api/batchservice/pool/add#starttask).

- **Odwołania do pakietu aplikacji** w puli: dla aplikacji lub danych, które muszą być zainstalowane na każdym węźle w puli. Nie istnieje polecenie instalacji skojarzone z pakietem aplikacji, ale można użyć zadania uruchamiania do uruchomienia dowolnego polecenia instalacji. Jeśli aplikacja nie wymaga instalacji lub składa się z dużej liczby plików, można użyć tej metody. Pakiety aplikacji są dobrze dopasowane dla dużej liczby plików, ponieważ łączą wiele odwołań do plików w małym ładunku. Jeśli spróbujesz dołączyć więcej niż 100 oddzielnych plików zasobów w jednym zadaniu, usługa Batch może zostać przydzielona do ograniczeń systemu wewnętrznego dla jednego zadania. Ponadto Użyj pakietów aplikacji, jeśli masz rygorystyczne wymagania dotyczące wersji, w przypadku których może istnieć wiele różnych wersji tej samej aplikacji i musisz wybrać między nimi. Aby uzyskać więcej informacji, przeczytaj artykuł [wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch](./batch-application-packages.md).

- **Pliki zasobów zadania przygotowania zadania**: dla aplikacji lub danych, które muszą być zainstalowane, aby zadanie zostało uruchomione, ale nie trzeba go instalować w całej puli. Na przykład: Jeśli pula ma wiele różnych typów zadań, a tylko jeden typ zadania wymaga MyApplication.msi, warto wprowadzić krok instalacji do zadania przygotowania zadania. Aby uzyskać więcej informacji o zadaniach przygotowania zadania [, zobacz Uruchamianie zadań przygotowania i zwolnienia zadań w węzłach obliczeniowych usługi Batch](./batch-job-prep-release.md).

- **Pliki zasobów zadania**: w przypadku, gdy aplikacja lub dane mają zastosowanie tylko do pojedynczego zadania. Na przykład: masz pięć zadań, każdy przetwarza inny plik, a następnie zapisuje dane wyjściowe do magazynu obiektów BLOB.  W takim przypadku plik wejściowy powinien zostać określony w kolekcji **plików zasobów zadań** , ponieważ każde zadanie ma własny plik wejściowy.

## <a name="determine-the-scope-required-of-a-file"></a>Określanie zakresu wymaganego przez plik

Należy określić zakres pliku — jest to plik wymagany dla puli, zadania lub zadania. Pliki należące do zakresu puli powinny używać pakietów aplikacji puli lub zadania uruchamiania. Pliki należące do zakresu zadania powinny używać zadania przygotowania zadania. Dobrym przykładem plików objętych zakresem puli lub poziomu zadania są aplikacje. Pliki należące do zakresu zadania powinny używać plików zasobów zadania.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Inne sposoby pobierania danych do węzłów obliczeniowych w usłudze Batch

Istnieją inne sposoby pobierania danych do węzłów obliczeniowych usługi Batch, które nie są oficjalnie zintegrowane z interfejsem API REST usługi Batch. Ponieważ masz kontrolę nad węzłami Azure Batch i można uruchamiać niestandardowe pliki wykonywalne, możesz pobierać dane z dowolnej liczby źródeł niestandardowych, o ile węzeł wsadowy ma łączność z obiektem docelowym i masz poświadczenia do tego źródła w węźle Azure Batch. Oto kilka typowych przykładów:

- Pobieranie danych z serwera SQL
- Pobieranie danych z innych usług sieci Web/lokalizacji niestandardowych
- Mapowanie udziału sieciowego

### <a name="azure-storage"></a>Azure Storage

Magazyn obiektów BLOB ma elementy docelowe skalowalności. Elementy docelowe skalowalności udziału plików w usłudze Azure Storage są takie same jak w przypadku pojedynczego obiektu BLOB. Rozmiar będzie mieć wpływ na wymaganą liczbę węzłów i pul.

