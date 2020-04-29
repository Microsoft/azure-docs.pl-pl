---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80117000"
---
Udostępniona Galeria obrazów to usługa, która ułatwia tworzenie struktury i organizacji na całym zarządzanym obrazie. Udostępnione Galerie obrazów zapewniają następujące:

- Zarządza replikacją globalną obrazów.
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania.
- Obrazy o wysokiej dostępności z kontami magazynu Strefowo nadmiarowego (ZRS) w regionach, które obsługują Strefy dostępności. ZRS zapewnia lepszą odporność na awarie stref.
- Udostępnianie między subskrypcjami, a nawet między dzierżawami Active Directory (AD) przy użyciu RBAC.
- Skalowanie wdrożeń przy użyciu replik obrazów w każdym regionie.

Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usług lub grupom usługi AD w organizacji. Obrazy udostępnione mogą być replikowane do wielu regionów, co umożliwia szybsze skalowanie wdrożeń.

Obraz zarządzany to kopia pełnej maszyny wirtualnej (w tym dowolnych dołączonych dysków danych) lub tylko dysku systemu operacyjnego, w zależności od sposobu tworzenia obrazu. Podczas tworzenia maszyny wirtualnej na podstawie obrazu kopia dysków VHD w obrazie jest używana do tworzenia dysków dla nowej maszyny wirtualnej. Zarządzany obraz pozostaje w magazynie i może być ponownie używany do tworzenia nowych maszyn wirtualnych.

Jeśli masz dużą liczbę zarządzanych obrazów, które chcesz zachować i chcesz udostępnić je w całej firmie, możesz użyć udostępnionej galerii obrazów jako repozytorium, która ułatwia udostępnianie obrazów. 

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Podstawowy obraz, który może być używany samodzielnie lub do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie [uogólnionych](#generalized-and-specialized-images) maszyn wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do utworzenia wersji obrazu udostępnionego. |
| **Zdjęcie** | Kopia dysku VHD, której można użyć do udostępnienia **wersji obrazu**. Migawki mogą być pobierane z [wyspecjalizowanej](#generalized-and-specialized-images) maszyny wirtualnej (która nie została uogólniona), a następnie używana samodzielnie lub z migawkami dysków danych, aby utworzyć wyspecjalizowaną wersję obrazu.
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są zdefiniowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go w organizacji. Można dołączyć informacje takie jak uogólnione lub wyspecjalizowane obrazy, system operacyjny, minimalne i maksymalne wymagania dotyczące pamięci oraz informacje o wersji. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |

<br>

![Ilustracja przedstawiająca sposób, w jaki można mieć wiele wersji obrazu w galerii](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definicje obrazu

Definicje obrazów są logiczną grupą dla wersji obrazu. Definicja obrazu zawiera informacje na temat przyczyny utworzenia obrazu, jego systemu operacyjnego oraz informacji o korzystaniu z obrazu. Definicja obrazu jest taka sama jak w przypadku planu dla wszystkich szczegółów dotyczących tworzenia określonego obrazu. Nie można wdrożyć maszyny wirtualnej na podstawie definicji obrazu, ale z wersji obrazu utworzonej na podstawie definicji.

Istnieją trzy parametry dla każdej definicji obrazu, które są używane w połączeniu **wydawcy**, **oferty** i **jednostki SKU**. Są one używane do znajdowania konkretnej definicji obrazu. Możesz mieć wersje obrazów, które współdzielą jeden lub dwa, ale nie wszystkie trzy wartości.  Na przykład poniżej przedstawiono trzy definicje obrazów i ich wartości:

|Definicja obrazu|Wydawca|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finanse|Zaplecze|
|myImage2|Contoso|Finanse|Fronton|
|myImage3|Testowanie|Finanse|Fronton|

Wszystkie trzy z nich mają unikatowe zestawy wartości. Ten format jest podobny do tego, jak obecnie można określić wydawcy, oferty i jednostki SKU dla [obrazów portalu Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) w Azure PowerShell, aby uzyskać najnowszą wersję obrazu portalu Marketplace. Każda definicja obrazu musi mieć unikatowy zestaw tych wartości.

Poniżej znajdują się inne parametry, które można ustawić w definicji obrazu, dzięki czemu można łatwiej śledzić zasoby:

* Stan systemu operacyjnego — można ustawić [uogólniony lub wyspecjalizowany](#generalized-and-specialized-images)stan systemu operacyjnego.
* System operacyjny — może to być system Windows lub Linux.
* Opis — Użyj opisu, aby uzyskać bardziej szczegółowe informacje na temat tego, dlaczego istnieje definicja obrazu. Na przykład może istnieć definicja obrazu dla serwera frontonu, na którym jest wstępnie zainstalowana aplikacja.
* EULA — może służyć do wskazywania umowy licencyjnej użytkownika końcowego dotyczącej definicji obrazu.
* Zasady zachowania poufności informacji i informacje o wersji — przechowywanie informacji o wersji i zasad zachowania poufności informacji w usłudze Azure Storage oraz dostarczanie identyfikatora URI w celu uzyskania dostępu do nich w ramach definicji obrazu.
* Data zakończenia okresu użytkowania — Dołącz datę zakończenia okresu istnienia do definicji obrazu, aby można było użyć automatyzacji do usunięcia starych definicji obrazu.
* Tag — możesz dodać tagi podczas tworzenia definicji obrazu. Aby uzyskać więcej informacji na temat tagów, zobacz [Używanie tagów do organizowania zasobów](../articles/azure-resource-manager/management/tag-resources.md)
* Minimalne i maksymalne zalecenia dotyczące vCPU i pamięci — Jeśli obraz ma zalecenia vCPU i pamięci, możesz dołączyć te informacje do definicji obrazu.
* Niedozwolone typy dysków — można podać informacje o wymaganiach dotyczących magazynu dla maszyny wirtualnej. Na przykład jeśli obraz nie jest odpowiedni dla standardowych dysków DYSKowych, należy dodać je do listy nie Zezwalaj.

## <a name="generalized-and-specialized-images"></a>Uogólnione i wyspecjalizowane obrazy

Udostępnione galerii obrazów są obsługiwane przez dwa stany systemu operacyjnego. Zazwyczaj obrazy wymagają, aby maszyna wirtualna użyta do utworzenia obrazu była uogólniona przed pobraniem obrazu. Uogólnianie to proces, który usuwa informacje specyficzne dla komputera i użytkownika z maszyny wirtualnej. W przypadku systemu Windows jest używany program Sysprep. W przypadku systemu Linux można użyć [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` lub `-deprovision+user` parametrów.

Wyspecjalizowane maszyny wirtualne nie przechodzący przez proces usuwania konkretnych informacji i kont komputera. Ponadto maszyny wirtualne utworzone z wyspecjalizowanych obrazów nie są `osProfile` skojarzone z nimi. Oznacza to, że specjalne obrazy mają pewne ograniczenia.

- Konta, których można użyć do logowania się do maszyny wirtualnej, można również używać na dowolnej maszynie wirtualnej utworzonej przy użyciu obrazu wyspecjalizowanego tworzonego na podstawie tej maszyny wirtualnej.
- Maszyny wirtualne będą miały **nazwę komputera** maszyny wirtualnej, z której zrobiono obraz. Należy zmienić nazwę komputera, aby uniknąć kolizji.
- `osProfile` Jest to sposób przekazywania informacji poufnych do maszyny wirtualnej przy użyciu programu `secrets`. Może to powodować problemy przy użyciu magazynu kluczy, usługi WinRM i innych funkcji `secrets` używanych w `osProfile`programie. W niektórych przypadkach można użyć tożsamości usługi zarządzanej (MSI) do obejścia tych ograniczeń.

> [!IMPORTANT]
> Wyspecjalizowane obrazy są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Ograniczenia znanej wersji zapoznawczej** Maszyny wirtualne można tworzyć tylko z wyspecjalizowanych obrazów przy użyciu portalu lub interfejsu API. Program nie obsługuje interfejsu wiersza polecenia lub programu PowerShell dla wersji zapoznawczej.


## <a name="regional-support"></a>Obsługa regionalna

Regiony źródłowe są wymienione w poniższej tabeli. Wszystkie regiony publiczne mogą być regionami docelowymi, ale w celu replikowania do Australii Środkowej i Australii środkowej 2 trzeba mieć subskrypcję listy dozwolonych. Aby zażądać listy dozwolonych, przejdź do:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regiony źródłowe        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australia Środkowa     | Chiny Wschodnie        | Indie Południowe        | Europa Zachodnia        |
| Australia Środkowa 2   | Chiny Wschodnie 2      | Azja Południowo-Wschodnia     | Południowe Zjednoczone Królestwo           |
| Australia Wschodnia        | Chiny Północne       | Japonia Wschodnia         | Zachodnie Zjednoczone Królestwo            |
| Australia Południowo-Wschodnia   | Chiny Północne 2     | Japonia Zachodnia         | US DoD (region środkowy)     |
| Brazylia Południowa          | Azja Wschodnia         | Korea Środkowa      | US DoD (region wschodni)        |
| Kanada Środkowa        | Wschodnie stany USA           | Korea Południowa        | US Gov Arizona     |
| Kanada Wschodnia           | Wschodnie stany USA 2         | Północno-środkowe stany USA   | US Gov Teksas       |
| Indie Środkowe         | Wschodnie stany USA 2 — EUAP    | Europa Północna       | US Gov Wirginia    |
| Środkowe stany USA            | Francja Środkowa    | Południowo-środkowe stany USA   | Indie Zachodnie         |
| Środkowe stany USA — EUAP       | Francja Południowa      | Zachodnio-środkowe stany USA    | Zachodnie stany USA            |
|                       |                   |                    | Zachodnie stany USA 2          |



## <a name="limits"></a>Limity 

Istnieją limity dla każdej subskrypcji dotyczące wdrażania zasobów przy użyciu udostępnionych galerii obrazów:
- 100 udostępnione Galerie obrazów na subskrypcję na region
- 1 000 definicji obrazów na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region
- Dowolny dysk dołączony do obrazu musi być mniejszy lub równy 1 TB

Aby uzyskać więcej informacji, zobacz [Sprawdzanie użycia zasobów względem limitów](https://docs.microsoft.com/azure/networking/check-usage-against-limits) na potrzeby przykładów sprawdzania bieżącego użycia.
 
## <a name="scaling"></a>Skalowanie
Udostępniona Galeria obrazów pozwala określić liczbę replik, które mają być przechowywane na platformie Azure. Pomaga to w scenariuszach wdrażania wielu maszyn wirtualnych, ponieważ wdrożenia maszyn wirtualnych można rozmieścić w różnych replikach, co zmniejsza prawdopodobieństwo ograniczenia przetwarzania wystąpienia z powodu przeciążenia pojedynczej repliki.

Korzystając z galerii obrazów udostępnionych, można teraz wdrożyć do 1 000 wystąpień maszyn wirtualnych w zestawie skalowania maszyn wirtualnych (do maksymalnie 600 z obrazami zarządzanymi). Repliki obrazów zapewniają lepszą wydajność, niezawodność i spójność wdrożenia. Można ustawić inną liczbę replik w każdym regionie docelowym na podstawie potrzeb skali dla regionu. Ponieważ każda replika to głęboka kopia obrazu, to ułatwia skalowanie wdrożeń liniowo z każdą dodatkową repliką. Wiemy, że nie ma żadnych dwóch obrazów ani regionów, poniżej przedstawiono ogólne wytyczne dotyczące korzystania z replik w regionie:

- W przypadku wdrożeń z zestawu skalowania maszyn wirtualnych (VMSS) — dla każdej 20 maszyn wirtualnych, które tworzysz współbieżnie, zalecamy zachowanie jednej repliki. Na przykład jeśli tworzysz maszyny wirtualne 120 na bieżąco przy użyciu tego samego obrazu w regionie, sugerujemy zachowywanie co najmniej 6 replik obrazu. 
- W przypadku wdrożeń zestawu skalowania maszyn wirtualnych (VMSS) — dla każdego wdrożenia zestawu skalowania z maksymalnie 600 wystąpieniami zalecamy zachowanie co najmniej jednej repliki. Na przykład, jeśli tworzysz 5 zestawów skalowania współbieżnie, z których każda korzysta z 600 wystąpień maszyn wirtualnych przy użyciu tego samego obrazu w jednym regionie, sugerujemy zachowanie co najmniej 5 replik obrazu. 

Zawsze zalecamy przeprowadzenie aprowizacji liczby replik ze względu na takie czynniki jak rozmiar obrazu, zawartość i typ systemu operacyjnego.

![Ilustracja przedstawiająca sposób skalowania obrazów](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Zapewnianie wysokiej dostępności obrazów

[Magazyn strefowo nadmiarowy (ZRS) platformy Azure](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) zapewnia odporność na awarię strefy dostępności w regionie. Korzystając z ogólnej dostępności galerii obrazów udostępnionych, można wybrać przechowywanie obrazów na kontach ZRS w regionach, w których Strefy dostępności. 

Możesz również wybrać typ konta dla każdego regionu docelowego. Domyślny typ konta magazynu to Standard_LRS, ale można wybrać Standard_ZRS dla regionów z Strefy dostępności. Sprawdź regionalną dostępność ZRS [tutaj](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Ilustracja przedstawiająca ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikacja
Udostępniona Galeria obrazów umożliwia również automatyczne replikowanie obrazów do innych regionów platformy Azure. Każda udostępniona wersja obrazu może być replikowana do różnych regionów, w zależności od tego, co jest sensowne dla Twojej organizacji. Przykładem jest zawsze replikowanie najnowszego obrazu w wielu regionach, a wszystkie starsze wersje są dostępne tylko w 1 regionie. Może to pomóc w zapisaniu kosztów magazynu dla udostępnionych wersji obrazu. 

Regiony, w których jest replikowana wersja udostępnionego obrazu, mogą zostać zaktualizowane po upływie czasu utworzenia. Czas potrzebny na replikację do różnych regionów zależy od ilości kopiowanych danych i liczby regionów, do których jest replikowana wersja. W niektórych przypadkach może to potrwać kilka godzin. Podczas replikacji można wyświetlić stan replikacji na region. Po zakończeniu replikacji obrazu w regionie można wdrożyć maszynę wirtualną lub zestaw skalowania przy użyciu tej wersji obrazu w regionie.

![Ilustracja przedstawiająca sposób replikowania obrazów](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Dostęp

Jako Galeria obrazów udostępnionych, definicja obrazu i wersja obrazu są wszystkie zasoby, które można udostępniać przy użyciu wbudowanych kontrolek kontroli RBAC platformy Azure. Za pomocą RBAC można udostępniać te zasoby innym użytkownikom, podmiotom usługi i grupom. Możesz nawet udostępnić dostęp osobom spoza dzierżawy, w ramach której zostały utworzone. Gdy użytkownik ma dostęp do udostępnionej wersji obrazu, może wdrożyć maszynę wirtualną lub zestaw skalowania maszyn wirtualnych.  Oto macierz udostępniania, która pomaga zrozumieć, do czego użytkownik uzyskuje dostęp:

| Udostępnione użytkownikowi     | Galeria obrazów udostępnionych | Definicja obrazu | Wersja obrazu |
|----------------------|----------------------|--------------|----------------------|
| Galeria obrazów udostępnionych | Tak                  | Tak          | Tak                  |
| Definicja obrazu     | Nie                   | Tak          | Tak                  |

Zalecamy udostępnianie na poziomie galerii w celu uzyskania najlepszego środowiska. Nie zaleca się udostępniania poszczególnych wersji obrazu. Aby uzyskać więcej informacji na temat RBAC, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Obrazy mogą być również udostępniane na dużą skalę, nawet między dzierżawcami przy użyciu rejestracji aplikacji wielodostępnej. Aby uzyskać więcej informacji o udostępnianiu obrazów między dzierżawcami, zobacz [udostępnianie obrazów maszyn wirtualnych z galerii w ramach dzierżawców platformy Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Rozliczenia
Za korzystanie z usługi udostępnionej galerii obrazów nie są naliczane żadne dodatkowe opłaty. Zostanie naliczona opłata za następujące zasoby:
- Koszty magazynowania przechowywania wersji obrazu udostępnionego. Koszt zależy od liczby replik wersji obrazu oraz liczby regionów, do których jest replikowana wersja. Na przykład jeśli masz 2 obrazy i obie są replikowane do 3 regionów, zostanie naliczona opłata za 6 dysków zarządzanych na podstawie ich rozmiaru. Aby uzyskać więcej informacji, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Opłaty za ruch wychodzący z sieci są naliczane za replikację pierwszej wersji obrazu z regionu źródłowego do replikowanych regionów. Kolejne repliki są obsługiwane w regionie, więc nie są naliczane żadne dodatkowe opłaty. 

## <a name="updating-resources"></a>Aktualizowanie zasobów

Po utworzeniu można wprowadzić pewne zmiany zasobów galerii obrazów. Są one ograniczone do:
 
Galeria obrazów udostępnionych:
- Opis

Definicja obrazu:
- Zalecane procesorów wirtualnych vCPU
- Zalecana pamięć
- Opis
- Data zakończenia okresu istnienia

Wersja obrazu:
- Liczba replik regionalnych
- Regiony docelowe
- Wyklucz z najnowszych
- Data zakończenia okresu istnienia

## <a name="sdk-support"></a>Obsługa zestawu SDK

Następujące zestawy SDK obsługują Tworzenie udostępnionych galerii obrazów:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Przejdź](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Szablony

Możesz utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

* [Jak można wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów w ramach subskrypcji?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Czy mogę przenieść istniejący obraz do galerii obrazów udostępnionych?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Czy mogę utworzyć wersję obrazu z wyspecjalizowanego dysku?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Czy mogę przenieść zasób udostępnionej galerii obrazów do innej subskrypcji po jej utworzeniu?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Czy można replikować moje wersje obrazów między chmurami, takimi jak Azure Chiny 21Vianet czy Azure (Niemcy) czy Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Czy można replikować wersje obrazów między subskrypcjami?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Czy mogę udostępniać wersje obrazów w dzierżawach usługi Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Jak długo trwa replikowanie wersji obrazów w regionach docelowych?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Jaka jest różnica między regionem źródłowym a regionem docelowym?](#what-is-the-difference-between-source-region-and-target-region)
* [Jak mogę określić regionu źródłowego podczas tworzenia wersji obrazu?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Jak mogę określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Czy można utworzyć galerię obrazów udostępnionych w innej lokalizacji niż ta dla definicji obrazu i wersji obrazu?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Jakie są opłaty za korzystanie z galerii obrazów udostępnionych?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Jakiej wersji interfejsu API należy użyć do utworzenia galerii obrazów udostępnionych i definicji obrazu i wersji obrazu?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Jakiej wersji interfejsu API należy użyć do utworzenia udostępnionej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych z wersji obrazu?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Jak można wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów w ramach subskrypcji?

Aby wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów między subskrypcjami, do których masz dostęp na Azure Portal, wykonaj następujące czynności:

1. Otwórz [witrynę Azure Portal](https://portal.azure.com).
1. Przejdź do pozycji **wszystkie zasoby**.
1. Wybierz wszystkie subskrypcje, w ramach których chcesz wyświetlić listę wszystkich zasobów.
1. Wyszukaj zasoby typu **Galeria prywatna**.
 
   Aby wyświetlić definicje obrazu i wersje obrazu, należy również wybrać **Pokaż ukryte typy**.
 
   Aby wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów między subskrypcjami, do których masz uprawnienia, użyj następującego polecenia w interfejsie wiersza polecenia platformy Azure:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Czy mogę przenieść istniejący obraz do galerii obrazów udostępnionych?
 
Tak. Istnieją 3 scenariusze na podstawie typów obrazów, które mogą mieć.

 Scenariusz 1. Jeśli masz zarządzany obraz w tej samej subskrypcji co SIG, możesz utworzyć definicję obrazu i wersję obrazu.

 Scenariusz 2. Jeśli masz niezarządzany obraz w tej samej subskrypcji co rekord SIG, możesz utworzyć zarządzany obraz z tego elementu, a następnie utworzyć definicję obrazu i wersję obrazu. 

 Scenariusz 3. Jeśli masz dysk VHD w lokalnym systemie plików, musisz przekazać wirtualny dysk twardy do zarządzanego obrazu, a następnie utworzyć definicję obrazu i wersję obrazu.

- Jeśli wirtualny dysk twardy jest maszyną wirtualną z systemem Windows, zobacz [przekazywanie wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Jeśli wirtualny dysk twardy jest przeznaczony dla maszyny wirtualnej z systemem Linux, zobacz [przekazywanie wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Czy mogę utworzyć wersję obrazu z wyspecjalizowanego dysku?

Tak, obsługa wyspecjalizowanych dysków jako obrazów jest w wersji zapoznawczej. Maszynę wirtualną można utworzyć tylko na podstawie wyspecjalizowanego obrazu przy użyciu portalu ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) lub [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) i interfejsu API. Brak obsługi programu PowerShell na potrzeby wersji zapoznawczej.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Czy mogę przenieść zasób udostępnionej galerii obrazów do innej subskrypcji po jej utworzeniu?

Nie, nie można przenieść zasobu udostępnionej galerii obrazów do innej subskrypcji. Jednak będzie można replikować wersje obrazu w galerii do innych regionów zgodnie z wymaganiami.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Czy można replikować moje wersje obrazów między chmurami, takimi jak Azure Chiny 21Vianet czy Azure (Niemcy) czy Azure Government Cloud?

Nie, nie można replikować wersji obrazu w chmurach.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Czy można replikować wersje obrazów między subskrypcjami?

Nie, możesz replikować wersje obrazów między regionami w ramach subskrypcji i używać ich w innych subskrypcjach za pośrednictwem RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Czy mogę udostępniać wersje obrazów w dzierżawach usługi Azure AD? 

Tak, możesz użyć RBAC, aby udostępnić osobom w dzierżawach. Jednak aby udostępnić je na dużą skalę, zobacz "udostępnianie obrazów galerii w ramach dzierżawców platformy Azure" przy użyciu [programu PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) lub [interfejsu wiersza polecenia](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Jak długo trwa replikowanie wersji obrazów w regionach docelowych?

Czas replikacji wersji obrazu jest w całości zależny od rozmiaru obrazu i liczby regionów, do których jest replikowana. Jednak najlepszym rozwiązaniem jest zapewnienie małego obrazu, a regiony źródłowe i docelowe zamykają się w celu uzyskania najlepszych wyników. Stan replikacji można sprawdzić przy użyciu flagi-ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Jaka jest różnica między regionem źródłowym a regionem docelowym?

Region źródłowy to region, w którym zostanie utworzona wersja obrazu, a regiony docelowe to regiony, w których będzie przechowywana kopia wersji obrazu. Dla każdej wersji obrazu można mieć tylko jeden region źródłowy. Upewnij się również, że lokalizacja regionu źródłowego została przekazana jako jeden z regionów docelowych podczas tworzenia wersji obrazu.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Jak mogę określić regionu źródłowego podczas tworzenia wersji obrazu?

Podczas tworzenia wersji obrazu można użyć znacznika **--Location** w interfejsie wiersza polecenia i tagu **lokalizacji** w programie PowerShell, aby określić region źródłowy. Upewnij się, że zarządzany obraz, którego używasz jako obrazu podstawowego, aby utworzyć wersję obrazu, znajduje się w tej samej lokalizacji co lokalizacja, w której ma zostać utworzona wersja obrazu. Upewnij się również, że lokalizacja regionu źródłowego została przekazana jako jeden z regionów docelowych podczas tworzenia wersji obrazu.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Jak mogę określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie?

Istnieją dwa sposoby, w których można określić liczbę replik wersji obrazu, które mają zostać utworzone w każdym regionie:
 
1. Liczba replik regionalnych, która określa liczbę replik, które chcesz utworzyć na region. 
2. Typowa liczba replik, która jest wartością domyślną dla poszczególnych regionów w przypadku, gdy nie określono liczby replik regionalnych. 

Aby określić liczbę replik regionalnych, Przekaż lokalizację wraz z liczbą replik, które chcesz utworzyć w tym regionie: "Południowo-środkowe stany USA = 2". 

Jeśli liczba replik regionalnych nie jest określona dla każdej lokalizacji, domyślną liczbą replik będzie określona liczba znanych replik. 

Aby określić wspólną liczbę replik w interfejsie wiersza `az sig image-version create` polecenia, użyj argumentu **--Replica-Count** w poleceniu.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Czy można utworzyć galerię obrazów udostępnionych w innej lokalizacji niż ta dla definicji obrazu i wersji obrazu?

Tak, jest to możliwe. Jednak najlepszym rozwiązaniem jest utrzymywanie w tej samej lokalizacji grupy zasobów, galerii obrazów udostępnionych, definicji obrazu i wersji obrazu.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Jakie są opłaty za korzystanie z galerii obrazów udostępnionych?

Nie są naliczane opłaty za korzystanie z usługi Shared Image Gallery, z wyjątkiem opłat za magazyn do przechowywania wersji obrazów i opłat za wychodzące sieci na potrzeby replikowania wersji obrazu z regionu źródłowego do regionów docelowych.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Jakiej wersji interfejsu API należy użyć do utworzenia galerii obrazów udostępnionych i definicji obrazu i wersji obrazu?

Aby współpracować z udostępnionymi galeriami obrazów, definicjami obrazów i wersjami obrazów, zalecamy korzystanie z interfejsu API w wersji 2018-06-01. Magazyn strefowo nadmiarowy (ZRS) wymaga wersji 2019-03-01 lub nowszej.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Jakiej wersji interfejsu API należy użyć do utworzenia udostępnionej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych z wersji obrazu?

W przypadku wdrożeń maszyny wirtualnej i zestawu skalowania maszyn wirtualnych przy użyciu wersji obrazu zalecamy użycie interfejsu API w wersji 2018-04-01 lub nowszej.
