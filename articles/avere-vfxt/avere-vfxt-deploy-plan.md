---
title: Planowanie systemu avere vFXT na platformie Azure
description: Zaplanuj avere vFXT dla klastra platformy Azure, który odpowiada Twoim potrzebom. Zapoznaj się z pytaniami przed przejściem do portalu Azure Marketplace lub tworzeniem maszyn wirtualnych.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342403"
---
# <a name="plan-your-avere-vfxt-system"></a>Planowanie systemu Avere vFXT

W tym artykule wyjaśniono, jak zaplanować nowy avere vFXT dla klastra platformy Azure, który jest ustalany odpowiednio do potrzeb.

Przed przejściem do portalu Azure Marketplace lub utworzeniem jakichkolwiek maszyn wirtualnych należy wziąć pod uwagę następujące szczegóły:

* Jak działa klaster z innymi zasobami platformy Azure?
* Gdzie powinny znajdować się elementy klastra w sieciach prywatnych i podsieciach?
* Jakiego typu magazyn zaplecza będzie używany i w jaki sposób klaster będzie miał do niego dostęp?
* Jak wydajnie węzły klastra muszą być obsługiwane w ramach przepływu pracy?

Przeczytaj, aby dowiedzieć się więcej.

## <a name="learn-the-components-of-the-system"></a>Informacje o składnikach systemu

Po rozpoczęciu planowania warto zrozumieć składniki avere vFXT for Azure system.

* Węzły klastra — klaster składa się z co najmniej trzech maszyn wirtualnych skonfigurowanych jako węzły klastra. Więcej węzłów zapewnia wyższą przepływność systemu i większą pamięć podręczną.

* Pamięć podręczna — pojemność pamięci podręcznej jest dzielona równomiernie między węzłami klastra. Ustawianie rozmiaru pamięci podręcznej na węzeł podczas tworzenia klastra; rozmiary węzłów są dodawane, aby stała się łącznym rozmiarem pamięci podręcznej.

* Kontroler klastra — kontroler klastra jest dodatkową maszyną wirtualną znajdującą się w tej samej podsieci co węzły klastra. Kontroler jest wymagany do utworzenia klastra i bieżących zadań zarządzania.

* Magazyn zaplecza — dane, które mają być buforowane, są przechowywane w długim czasie w sprzętowym systemie pamięci masowej lub kontenera obiektów blob platformy Azure. Magazyn można dodać po utworzeniu avere vFXT dla klastra platformy Azure lub w przypadku korzystania z magazynu obiektów BLOB można dodać i skonfigurować kontener podczas tworzenia klastra.

* Klienci — komputery klienckie, które używają plików w pamięci podręcznej, łączą się z klastrem przy użyciu ścieżki pliku wirtualnego, zamiast bezpośrednio uzyskiwać dostęp do systemów magazynowych. (Więcej informacji można znaleźć w temacie [Instalowanie klastra avere vFXT](avere-vfxt-mount-clients.md)).

## <a name="subscription-resource-group-and-network-infrastructure"></a>Subskrypcja, Grupa zasobów i infrastruktura sieci

Rozważ, gdzie będą znajdować się elementy avere vFXT dla wdrożenia platformy Azure. Na poniższym diagramie przedstawiono możliwe rozmieszczenie składników avere vFXT for Azure:

![Diagram przedstawiający kontroler klastra i maszyny wirtualne klastra w jednej podsieci. Wokół granicy podsieci jest granicą sieci wirtualnej. Wewnątrz sieci wirtualnej to sześciokąt reprezentujący punkt końcowy usługi magazynu; jest ona połączona z kreskowaną strzałką do magazynu obiektów blob poza siecią wirtualną.](media/avere-vfxt-components-option.png)

Postępuj zgodnie z poniższymi wskazówkami podczas planowania infrastruktury sieciowej klastra avere vFXT:

* Utwórz nową subskrypcję dla każdego avere vFXT dla wdrożenia platformy Azure. Zarządzaj wszystkimi składnikami w tej subskrypcji.

  Zalety korzystania z nowej subskrypcji dla każdego wdrożenia obejmują:
  * Prostsze śledzenie kosztów — umożliwia wyświetlanie i inspekcję wszystkich kosztów zasobów, infrastruktury i cykli obliczeniowych w ramach jednej subskrypcji.
  * Łatwiejsze czyszczenie — po zakończeniu pracy z projektem można usunąć całą subskrypcję.
  * Wygodne partycjonowanie przydziałów zasobów — Izoluj klientów i klaster avere vFXT w ramach jednej subskrypcji, aby chronić inne krytyczne obciążenia przed możliwym ograniczeniem zasobów. Ta separacja zapobiega konfliktom podczas pobierania dużej liczby klientów do przepływu pracy obliczeniowej o wysokiej wydajności.

* Znajdź systemy obliczeniowe klienta blisko klastra vFXT. Magazyn zaplecza może być bardziej zdalny.  

* Zlokalizuj klaster vFXT oraz maszynę wirtualną kontrolera klastra, w tym:

  * W tej samej sieci wirtualnej
  * W tej samej grupie zasobów
  * Korzystanie z tego samego konta magazynu
  
  Szablon tworzenia klastra obsługuje tę konfigurację w większości sytuacji.

* Klaster musi znajdować się w własnej podsieci, aby uniknąć konfliktów adresów IP z klientami lub innymi zasobami obliczeniowymi.

* Użyj szablonu tworzenia klastra, aby utworzyć większość zasobów infrastruktury wymaganych dla klastra, w tym grup zasobów, sieci wirtualnych, podsieci i kont magazynu.

  Jeśli chcesz użyć już istniejących zasobów, upewnij się, że spełniają one wymagania zawarte w tej tabeli.

  | Zasób | Użyć istniejącej? | Wymagania |
  |----------|-----------|----------|
  | Grupa zasobów | Tak, jeśli wartość jest pusta | Musi być pusty|
  | Konto magazynu | **Tak** w przypadku łączenia istniejącego kontenera obiektów BLOB po utworzeniu klastra <br/>  **Nie** w przypadku tworzenia nowego kontenera obiektów BLOB podczas tworzenia klastra | Istniejący kontener obiektów BLOB musi być pusty <br/> &nbsp; |
  | Sieć wirtualna | Tak | Musi zawierać punkt końcowy usługi magazynu, jeśli tworzysz nowy kontener obiektów blob platformy Azure |
  | Podsieć | Tak | Nie może zawierać innych zasobów |

## <a name="ip-address-requirements"></a>Wymagania dotyczące adresów IP

Upewnij się, że podsieć klastra ma wystarczająco duży zakres adresów IP, aby obsługiwał klaster.

Klaster avere vFXT używa następujących adresów IP:

* Jeden adres IP zarządzania klastrem. Ten adres może przechodzić z węzła do węzła w klastrze w miarę potrzeby, aby był on zawsze dostępny. Użyj tego adresu, aby nawiązać połączenie z narzędziem konfiguracji panelu sterowania avere.
* Dla każdego węzła klastra:
  * Co najmniej jeden adres IP skierowany na klienta. (Wszystkie adresy związane z klientem są zarządzane przez *vserver*klastra, co może przenosić adresy IP między węzłami w razie konieczności).
  * Jeden adres IP do komunikacji z klastrem
  * Jeden adres IP wystąpienia (przypisany do maszyny wirtualnej)

W przypadku korzystania z usługi Azure Blob Storage mogą być również wymagane adresy IP z sieci wirtualnej klastra:  

* Konto magazynu obiektów blob platformy Azure wymaga co najmniej pięciu adresów IP. Należy pamiętać o tym wymaganiu, jeśli zlokalizujesz magazyn obiektów BLOB w tej samej sieci wirtualnej co klaster.
* Jeśli używasz magazynu obiektów blob platformy Azure, który znajduje się poza siecią wirtualną klastra, Utwórz punkt końcowy usługi magazynu w sieci wirtualnej. Punkt końcowy nie używa adresu IP.

Istnieje możliwość lokalizowania zasobów sieciowych i magazynu obiektów BLOB (jeśli są używane) w różnych grupach zasobów z klastra.

## <a name="vfxt-node-size"></a>rozmiar węzła vFXT

Maszyny wirtualne służące jako węzły klastra określają przepływność żądań i pojemność pamięci podręcznej. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Każdy węzeł vFXT będzie identyczny. Oznacza to, że jeśli utworzysz klaster z trzema węzłami, będziesz mieć trzy maszyny wirtualne o tym samym typie i rozmiarze.

| Typ wystąpienia | Procesory wirtualne | Pamięć  | Lokalny magazyn SSD  | Maks. liczba dysków danych | Przepływność dysku w pamięci podręcznej | Karta sieciowa (licznik) |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | LICZBA OPERACJI WE/WY 51 200 <br/> 768 MB/s | 16 000 MB/s (8)  |

Pamięć podręczna dysku na węzeł można skonfigurować i próg od 1000 GB do 8000 GB. Zalecany rozmiar pamięci podręcznej dla węzłów Standard_E32s_v3 to 4 TB na węzeł.

Aby uzyskać dodatkowe informacje na temat tych maszyn wirtualnych, Przeczytaj dokumentację Microsoft Azure: [rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](../virtual-machines/sizes-memory.md)

## <a name="account-quota"></a>Limit przydziału konta

Upewnij się, że Twoja subskrypcja ma pojemność do uruchomienia klastra avere vFXT, a także wszystkich używanych systemów obliczeniowych lub klienckich. Aby uzyskać szczegółowe informacje, zapoznaj [się z limitem przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) .

## <a name="back-end-data-storage"></a>Magazyn danych zaplecza

Systemy magazynowania zaplecza umożliwiają dostarczanie plików do pamięci podręcznej klastra, a także odbieranie zmienionych danych z pamięci podręcznej. Zdecyduj, czy zestaw roboczy będzie przechowywany w długim czasie w nowym kontenerze obiektów blob, czy w istniejącym systemie magazynu (chmurze lub sprzętu). Te systemy magazynu zaplecza są nazywane *plikami podstawowymi*.

### <a name="hardware-core-filers"></a>Podstawowe pliki sprzętowe

Po utworzeniu klastra Dodaj systemy magazynu sprzętowego do klastra vFXT. Można użyć różnych popularnych systemów sprzętowych, w tym systemów lokalnych, tak długo, jak można osiągnąć system magazynu z podsieci klastra.

Przeczytaj temat [Konfigurowanie magazynu](avere-vfxt-add-storage.md) , aby uzyskać szczegółowe instrukcje dotyczące sposobu dodawania istniejącego systemu magazynu do klastra avere vFXT.

### <a name="cloud-core-filers"></a>Pliki podstawowe w chmurze

Avere vFXT for Azure system może używać pustych kontenerów obiektów BLOB dla magazynu zaplecza. Kontenery muszą być puste po dodaniu do klastra — system vFXT musi mieć możliwość zarządzania jego magazynem obiektów bez konieczności zachowywania istniejących danych.

> [!TIP]
> Jeśli chcesz użyć magazynu obiektów blob platformy Azure dla zaplecza, Utwórz nowy kontener w ramach tworzenia klastra vFXT. Szablon tworzenia klastra może utworzyć i skonfigurować nowy kontener obiektów blob, aby był gotowy do użycia, gdy tylko klaster będzie dostępny. Późniejsze dodanie kontenera jest bardziej skomplikowane.
>
> Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem tworzenie avere vFXT dla platformy Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) .

Po dodaniu pustego kontenera magazynu obiektów BLOB jako podstawowego pliku programu można skopiować do niego dane za pomocą klastra. Użyj równoległego mechanizmu kopiowania wielowątkowego. Aby dowiedzieć się, jak kopiować dane do nowego kontenera klastra przy użyciu komputerów klienckich i pamięci podręcznej vFXT avere, przeczytaj artykuł dotyczący [przeniesienia danych do klastra vFXT](avere-vfxt-data-ingest.md) .

## <a name="cluster-access"></a>Dostęp do klastra

Avere vFXT dla klastra platformy Azure znajduje się w podsieci prywatnej, a klaster nie ma publicznego adresu IP. Musisz mieć pewien sposób, aby uzyskać dostęp do podsieci prywatnej w celu administrowania klastrami i połączeń klientów.

Opcje dostępu obejmują:

* Przeskocz hosta — Przypisz publiczny adres IP do osobnej maszyny wirtualnej w sieci prywatnej i użyj jej do utworzenia tunelu protokołu TLS w węzłach klastra.

  > [!TIP]
  > Jeśli ustawisz publiczny adres IP na kontrolerze klastra, możesz go użyć jako hosta skoku. Aby uzyskać więcej informacji, Odczytaj [kontroler klastra jako hosta skoku](#cluster-controller-as-jump-host) .

* Wirtualna sieć prywatna (VPN) — Konfigurowanie sieci VPN typu punkt-lokacja lub lokacja-lokacja między siecią prywatną w systemie Azure i sieciami firmowymi.

* Azure ExpressRoute — skonfiguruj połączenie prywatne za pomocą partnera ExpressRoute.

Aby uzyskać szczegółowe informacje na temat tych opcji, Przeczytaj dokumentację dotyczącą [platformy Azure Virtual Network](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kontroler klastra jako hosta skoku

W przypadku ustawienia publicznego adresu IP na kontrolerze klastra można go użyć jako hosta skoku, aby skontaktować się z klastrem usługi avere vFXT spoza podsieci prywatnej. Niemniej jednak, ponieważ kontroler ma uprawnienia dostępu do modyfikowania węzłów klastra, powoduje to utworzenie małego zagrożenia bezpieczeństwa.

W celu zwiększenia bezpieczeństwa kontrolera z publicznym adresem IP skrypt wdrażania automatycznie tworzy sieciową grupę zabezpieczeń, która ogranicza dostęp przychodzący tylko do portu 22. Aby zapewnić lepszą ochronę systemu, można zablokować dostęp do zakresu adresów IP źródła, czyli zezwalać na połączenia tylko z maszyn, które mają być używane do dostępu do klastra.

Podczas tworzenia klastra można określić, czy ma zostać utworzony publiczny adres IP na kontrolerze klastra.

* Jeśli utworzysz **nową sieć wirtualną** lub **nową podsieć**, do kontrolera klastra zostanie przypisany **publiczny** adres IP.
* W przypadku wybrania istniejącej sieci wirtualnej i podsieci kontroler klastra będzie miał tylko **prywatne** adresy IP.

## <a name="vm-access-roles"></a>Role dostępu do maszyny wirtualnej

Platforma Azure używa [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/index.yml) na platformie Azure w celu autoryzowania maszyn wirtualnych klastra do wykonywania określonych zadań. Na przykład kontroler klastra wymaga autoryzacji do tworzenia i konfigurowania maszyn wirtualnych węzłów klastra. Węzły klastra muszą mieć możliwość przypisywania lub ponownego przypisywania adresów IP do innych węzłów klastra.

W przypadku maszyn wirtualnych avere vFXT są używane dwie wbudowane role platformy Azure:

* Kontroler klastra używa wbudowanego [współautora avere](../role-based-access-control/built-in-roles.md#avere-contributor)roli.
* Węzły klastra używają wbudowanego [operatora avere](../role-based-access-control/built-in-roles.md#avere-operator)roli.

Jeśli musisz dostosować role dostępu dla składników avere vFXT, musisz zdefiniować własną rolę, a następnie przypisać ją do maszyn wirtualnych w momencie ich tworzenia. Nie można użyć szablonu wdrożenia w portalu Azure Marketplace. Skontaktuj się z działem obsługi klienta i pomocy technicznej firmy Microsoft, otwierając bilet w Azure Portal zgodnie z opisem w artykule [Uzyskiwanie pomocy dotyczącej systemu](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Następne kroki

[Omówienie wdrażania](avere-vfxt-deploy-overview.md) zawiera widok Big-Picture kroków wymaganych do utworzenia avere vFXT dla systemu Azure i przygotowania do obsługi danych.