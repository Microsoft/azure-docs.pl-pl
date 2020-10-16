---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 10/14/2020
ms.author: olayemio
ms.custom: include file
ms.openlocfilehash: 3d5b57330775af60341cd65fddc65c10645f2573
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92116812"
---
Udostępniona Galeria obrazów to usługa, która ułatwia tworzenie struktury i organizacji na całym obrazie. Udostępnione Galerie obrazów zapewniają następujące:

- Replikacja globalna obrazów.
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania.
- Obrazy o wysokiej dostępności z kontami magazynu Strefowo nadmiarowego (ZRS) w regionach, które obsługują Strefy dostępności. ZRS zapewnia lepszą odporność na awarie stref.
- Wsparcie Premium Storage (Premium_LRS).
- Udostępnianie między subskrypcjami, a nawet między dzierżawami Active Directory (AD) przy użyciu RBAC.
- Skalowanie wdrożeń przy użyciu replik obrazów w każdym regionie.

Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usług lub grupom usługi AD w organizacji. Obrazy udostępnione mogą być replikowane do wielu regionów, co umożliwia szybsze skalowanie wdrożeń.

Obraz to kopia pełnej maszyny wirtualnej (w tym dowolnych dołączonych dysków danych) lub tylko dysku systemu operacyjnego, w zależności od sposobu jego tworzenia. Podczas tworzenia maszyny wirtualnej na podstawie obrazu kopia dysków VHD w obrazie jest używana do tworzenia dysków dla nowej maszyny wirtualnej. Obraz pozostaje w magazynie i może być ponownie używany do tworzenia nowych maszyn wirtualnych.

Jeśli masz dużą liczbę obrazów, które musisz zachować i chcesz udostępnić je w całej firmie, możesz użyć udostępnionej galerii obrazów jako repozytorium. 

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów:

| Zasób | Opis|
|----------|------------|
| **Źródło obrazu** | Jest to zasób, którego można użyć do utworzenia **wersji obrazu** w galerii obrazów. Źródłem obrazu może być istniejąca maszyna wirtualna platformy Azure, która jest [uogólniona lub wyspecjalizowana](#generalized-and-specialized-images), zarządzanym obrazem, migawką, dyskiem VHD lub wersją obrazu w innej galerii obrazów. |
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Definicje obrazów są tworzone w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Dotyczy to zarówno obrazu systemu Windows, jak i Linux, informacji o wersji oraz minimalnych i maksymalnych wymagań dotyczących pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |

<br>

![Ilustracja przedstawiająca sposób, w jaki można mieć wiele wersji obrazu w galerii](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definicje obrazu

Definicje obrazów są logiczną grupą dla wersji obrazu. Definicja obrazu zawiera informacje na temat przyczyny utworzenia obrazu, jego systemu operacyjnego i innych informacji na temat korzystania z obrazu. Definicja obrazu jest taka sama jak w przypadku planu dla wszystkich szczegółów dotyczących tworzenia określonego obrazu. Nie można wdrożyć maszyny wirtualnej na podstawie definicji obrazu, ale z wersji obrazu utworzonych na podstawie definicji.

Istnieją trzy parametry dla każdej definicji obrazu, które są używane w połączeniu **wydawcy**, **oferty** i **jednostki SKU**. Są one używane do znajdowania konkretnej definicji obrazu. Możesz mieć wersje obrazów, które współdzielą jeden lub dwa, ale nie wszystkie trzy wartości.  Na przykład poniżej przedstawiono trzy definicje obrazów i ich wartości:

|Definicja obrazu|Publisher|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finance|Zaplecze|
|myImage2|Contoso|Finance|Fronton|
|myImage3|Testowanie|Finance|Fronton|

Wszystkie trzy z nich mają unikatowe zestawy wartości. Ten format jest podobny do tego, jak obecnie można określić wydawcy, oferty i jednostki SKU dla [obrazów portalu Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) w Azure PowerShell, aby uzyskać najnowszą wersję obrazu portalu Marketplace. Każda definicja obrazu musi mieć unikatowy zestaw tych wartości.

Następujące parametry określają, które typy wersji obrazu mogą zawierać:

- Stan systemu operacyjnego — można ustawić [uogólniony lub wyspecjalizowany](#generalized-and-specialized-images)stan systemu operacyjnego. To pole jest wymagane.
- System operacyjny — może to być system Windows lub Linux. To pole jest wymagane.
-   Generacja funkcji Hyper-V — Określ, czy obraz został utworzony z dysku VHD funkcji Hyper-V generacji 1 lub [2](../articles/virtual-machines/generation-2.md) . Wartość domyślna to generacja 1.


Poniżej znajdują się inne parametry, które można ustawić w definicji obrazu, dzięki czemu można łatwiej śledzić zasoby:

- Opis — Użyj opisu, aby uzyskać bardziej szczegółowe informacje na temat tego, dlaczego istnieje definicja obrazu. Na przykład może istnieć definicja obrazu dla serwera frontonu, na którym jest wstępnie zainstalowana aplikacja.
- EULA — może służyć do wskazywania umowy licencyjnej użytkownika końcowego dotyczącej definicji obrazu.
- Zasady zachowania poufności informacji i informacje o wersji — przechowywanie informacji o wersji i zasad zachowania poufności informacji w usłudze Azure Storage oraz dostarczanie identyfikatora URI w celu uzyskania dostępu do nich w ramach definicji obrazu.
- Data zakończenia okresu użytkowania — Dołącz datę zakończenia okresu istnienia do definicji obrazu, aby można było użyć automatyzacji do usunięcia starych definicji obrazu.
- Tag — możesz dodać tagi podczas tworzenia definicji obrazu. Aby uzyskać więcej informacji na temat tagów, zobacz [Używanie tagów do organizowania zasobów](../articles/azure-resource-manager/management/tag-resources.md)
- Minimalne i maksymalne zalecenia dotyczące vCPU i pamięci — Jeśli obraz ma zalecenia vCPU i pamięci, możesz dołączyć te informacje do definicji obrazu.
- Niedozwolone typy dysków — można podać informacje o wymaganiach dotyczących magazynu dla maszyny wirtualnej. Na przykład jeśli obraz nie jest odpowiedni dla standardowych dysków DYSKowych, należy dodać je do listy nie Zezwalaj.
- Informacje o planach zakupu dla obrazów z witryny Marketplace — `-PurchasePlanPublisher` , `-PurchasePlanName` i `-PurchasePlanProduct` . Aby uzyskać więcej informacji na temat informacji o planie zakupu, zobacz [Znajdowanie obrazów w portalu Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage) i [dostarczanie informacji o planie zakupów w portalu Azure Marketplace podczas tworzenia obrazów](../articles/virtual-machines/marketplace-images.md).


## <a name="image-versions"></a>Wersje obrazu

**Wersja obrazu** jest używana do tworzenia maszyny wirtualnej. Dla danego środowiska można mieć wiele wersji obrazu. W przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** , wersja obrazu służy do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.

Właściwości wersji obrazu są następujące:

- Numer wersji. Ta wartość jest używana jako Nazwa wersji obrazu. Zawsze ma format: MajorVersion. MinorVersion. patch. Jeśli podczas tworzenia maszyny wirtualnej zostanie użyta **Najnowsza** z nich, najnowszy obraz jest wybierany na podstawie największej MajorVersion, a następnie MinorVersion, a następnie poprawki. 
- Zewnętrz. Źródłem może być maszyna wirtualna, dysk zarządzany, migawka, obraz zarządzany lub inna wersja obrazu. 
- Wyklucz z najnowszych. Możesz zachować wersję jako najnowszą wersję obrazu. 
- Data zakończenia okresu istnienia. Data, po której maszyny wirtualne nie mogą zostać utworzone na podstawie tego obrazu.


## <a name="generalized-and-specialized-images"></a>Uogólnione i wyspecjalizowane obrazy

Udostępnione galerii obrazów są obsługiwane przez dwa stany systemu operacyjnego. Zazwyczaj obrazy wymagają, aby maszyna wirtualna użyta do utworzenia obrazu była uogólniona przed pobraniem obrazu. Uogólnianie to proces, który usuwa informacje specyficzne dla komputera i użytkownika z maszyny wirtualnej. W przypadku systemu Windows jest używane narzędzie Sysprep. W przypadku systemu Linux można użyć [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` lub `-deprovision+user` parametrów.

Wyspecjalizowane maszyny wirtualne nie przechodzący przez proces usuwania konkretnych informacji i kont komputera. Ponadto maszyny wirtualne utworzone z wyspecjalizowanych obrazów nie są `osProfile` skojarzone z nimi. Oznacza to, że specjalne obrazy będą mieć pewne ograniczenia oprócz niektórych korzyści.

- Maszyny wirtualne i zestawy skalowania utworzone na podstawie wyspecjalizowanych obrazów mogą działać szybciej. Ponieważ są one tworzone na podstawie źródła, które zostało już w trakcie pierwszego uruchomienia, maszyny wirtualne utworzone na podstawie tych obrazów są szybsze.
- Konta, których można użyć do logowania się do maszyny wirtualnej, można również używać na dowolnej maszynie wirtualnej utworzonej przy użyciu obrazu wyspecjalizowanego tworzonego na podstawie tej maszyny wirtualnej.
- Maszyny wirtualne będą miały **nazwę komputera** maszyny wirtualnej, z której zrobiono obraz. Należy zmienić nazwę komputera, aby uniknąć kolizji.
- `osProfile`Jest to sposób przekazywania informacji poufnych do maszyny wirtualnej przy użyciu programu `secrets` . Może to powodować problemy przy użyciu magazynu kluczy, usługi WinRM i innych funkcji używanych `secrets` w programie `osProfile` . W niektórych przypadkach można użyć tożsamości usługi zarządzanej (MSI) do obejścia tych ograniczeń.

## <a name="regional-support"></a>Obsługa regionalna

Wszystkie regiony publiczne mogą być regionami docelowymi, ale w celu replikowania do Australii środkowe i Australii środkowej 2 należy dodać subskrypcję do listy dozwolonych. Aby zażądać dodania subskrypcji do listy dozwolonych, przejdź do: https://azure.microsoft.com/global-infrastructure/australia/contact/

## <a name="limits"></a>Limity 

Istnieją limity dla każdej subskrypcji dotyczące wdrażania zasobów przy użyciu udostępnionych galerii obrazów:
- 100 udostępnione Galerie obrazów na subskrypcję na region
- 1 000 definicji obrazów na subskrypcję na region
- 10 000 wersje obrazów na subskrypcję na region
- 10 replik wersji obrazu na subskrypcję na region
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

## <a name="access"></a>Access

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
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej na podstawie wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

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
* [Czy mogę zaktualizować zestaw skalowania maszyn wirtualnych utworzony przy użyciu obrazu zarządzanego, aby używać obrazów z galerii obrazów udostępnionych?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Jak można wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów w ramach subskrypcji?

Aby wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów między subskrypcjami, do których masz dostęp na Azure Portal, wykonaj następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przewiń w dół stronę i wybierz pozycję **wszystkie zasoby**.
1. Wybierz wszystkie subskrypcje, w ramach których chcesz wyświetlić listę wszystkich zasobów.
1. Wyszukaj zasoby typu **Shared Image Gallery**.
  
Aby wyświetlić listę wszystkich udostępnionych zasobów galerii obrazów między subskrypcjami, do których masz uprawnienia, użyj następującego polecenia w interfejsie wiersza polecenia platformy Azure:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Aby uzyskać więcej informacji, zobacz **Zarządzanie zasobami galerii** za pomocą [interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/update-image-resources-cli.md) lub [programu PowerShell](../articles/virtual-machines/update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Czy mogę przenieść istniejący obraz do galerii obrazów udostępnionych?
 
Tak. Istnieją 3 scenariusze na podstawie typów obrazów, które mogą mieć.

 Scenariusz 1. Jeśli masz obraz zarządzany, możesz utworzyć definicję obrazu i wersję obrazu. Aby uzyskać więcej informacji, zobacz **Migrowanie z obrazu zarządzanego do wersji obrazu** przy użyciu [interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/image-version-managed-image-cli.md) lub [programu PowerShell](../articles/virtual-machines/image-version-managed-image-powershell.md).

 Scenariusz 2. Jeśli masz obraz niezarządzany, możesz utworzyć z niego obraz zarządzany, a następnie utworzyć definicję obrazu i wersję obrazu. 

 Scenariusz 3. Jeśli masz dysk VHD w lokalnym systemie plików, musisz przekazać wirtualny dysk twardy do zarządzanego obrazu, a następnie utworzyć definicję obrazu i wersję obrazu.

- Jeśli wirtualny dysk twardy jest maszyną wirtualną z systemem Windows, zobacz [przekazywanie wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Jeśli wirtualny dysk twardy jest przeznaczony dla maszyny wirtualnej z systemem Linux, zobacz [przekazywanie wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Czy mogę utworzyć wersję obrazu z wyspecjalizowanego dysku?

Tak, program może utworzyć maszynę wirtualną na podstawie wyspecjalizowanego obrazu przy użyciu interfejsu [wiersza polecenia](../articles/virtual-machines/vm-specialized-image-version-cli.md), [programu PowerShell](../articles/virtual-machines/vm-specialized-image-version-powershell.md)lub interfejsu API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Czy mogę przenieść zasób udostępnionej galerii obrazów do innej subskrypcji po jej utworzeniu?

Nie, nie można przenieść zasobu udostępnionej galerii obrazów do innej subskrypcji. Możesz replikować wersje obrazu z galerii do innych regionów lub skopiować obraz z innej galerii przy użyciu [interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/image-version-another-gallery-cli.md) lub [programu PowerShell](../articles/virtual-machines/image-version-another-gallery-powershell.md).

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

Aby określić wspólną liczbę replik w interfejsie wiersza polecenia, użyj argumentu **--Replica-Count** w `az sig image-version create` poleceniu.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Czy można utworzyć galerię obrazów udostępnionych w innej lokalizacji niż ta dla definicji obrazu i wersji obrazu?

Tak, jest to możliwe. Jednak najlepszym rozwiązaniem jest utrzymywanie w tej samej lokalizacji grupy zasobów, galerii obrazów udostępnionych, definicji obrazu i wersji obrazu.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Jakie są opłaty za korzystanie z galerii obrazów udostępnionych?

Nie są naliczane opłaty za korzystanie z usługi Shared Image Gallery, z wyjątkiem opłat za magazyn do przechowywania wersji obrazów i opłat za wychodzące sieci na potrzeby replikowania wersji obrazu z regionu źródłowego do regionów docelowych.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Jakiej wersji interfejsu API należy użyć do utworzenia galerii obrazów udostępnionych i definicji obrazu i wersji obrazu?

Aby współpracować z udostępnionymi galeriami obrazów, definicjami obrazów i wersjami obrazów, zalecamy korzystanie z interfejsu API w wersji 2018-06-01. Magazyn strefowo nadmiarowy (ZRS) wymaga wersji 2019-03-01 lub nowszej.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Jakiej wersji interfejsu API należy użyć do utworzenia udostępnionej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych z wersji obrazu?

W przypadku wdrożeń maszyny wirtualnej i zestawu skalowania maszyn wirtualnych przy użyciu wersji obrazu zalecamy użycie interfejsu API w wersji 2018-04-01 lub nowszej.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Czy mogę zaktualizować zestaw skalowania maszyn wirtualnych utworzony przy użyciu obrazu zarządzanego, aby używać obrazów z galerii obrazów udostępnionych?

Tak, możesz zaktualizować odwołanie do obrazu zestawu skalowania z obrazu zarządzanego do obrazu udostępnionej galerii obrazów, o ile typ systemu operacyjnego, generacja funkcji Hyper-V i układ dysku danych dopasowuje się między obrazami. 
