---
title: Wdrażanie avere vFXT dla platformy Azure
description: Dowiedz się, jak za pomocą Kreatora wdrażania dostępnego w portalu Azure Marketplace wdrożyć klaster z programem avere vFXT for Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 110fc658ffc3b72de91bbf1c9562f3efeecaf55b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342420"
---
# <a name="deploy-the-vfxt-cluster"></a>Wdrażanie klastra vFXT

Ta procedura przeprowadzi Cię przez użytkownika za pomocą Kreatora wdrażania dostępnego w portalu Azure Marketplace. Kreator automatycznie wdraża klaster przy użyciu szablonu Azure Resource Manager. Po wprowadzeniu parametrów w formularzu i kliknięciu przycisku **Utwórz** platforma Azure automatycznie uzupełnia następujące zadania:

* Tworzy kontroler klastra, który jest podstawową maszyną wirtualną, która zawiera oprogramowanie wymagające wdrożenia klastra i zarządzania nim.
* Konfiguruje grupę zasobów i infrastrukturę sieci wirtualnej, w tym tworzenie nowych elementów.
* Tworzy maszyny wirtualne węzła klastra i konfiguruje je jako klaster avere.
* Jeśli jest to wymagane, program tworzy nowy kontener obiektów blob platformy Azure i konfiguruje go jako podstawowy plik klastra.

Po wykonaniu instrukcji przedstawionych w tym dokumencie będzie dostępna sieć wirtualna, podsieć, kontroler klastra i klaster vFXT, jak pokazano na poniższym diagramie. Ten diagram przedstawia opcjonalny plik usługi Azure Blob Core, który obejmuje nowy kontener usługi BLOB Storage (na nowym koncie magazynu, niepokazywany) i punkt końcowy usług magazynu Microsoft Storage w podsieci.

![Diagram przedstawiający trzy prostokąty koncentryczne ze składnikami klastra avere. Zewnętrzny prostokąt ma etykietę "Grupa zasobów" i zawiera sześciokąt oznaczony etykietą "BLOB Storage (opcjonalnie)". Następny prostokąt w jest oznaczony etykietą "Sieć wirtualna: 10.0.0.0/16" i nie zawiera żadnych unikatowych składników. Wewnętrzny prostokąt ma etykietę "Subnet: 10.0.0.0/24" i zawiera maszynę wirtualną o nazwie "kontroler klastra", stos trzech maszyn wirtualnych z etykietą "vFXT nodes (vFXT cluster)" i "punkt końcowy usługi". Istnieje strzałka łącząca punkt końcowy usługi (znajdujący się wewnątrz podsieci) i magazyn obiektów BLOB (znajdujący się poza podsiecią i siecią wirtualną w grupie zasobów). Strzałka przechodzi przez granice podsieci i sieci wirtualnej.](media/avere-vfxt-deployment.png)

Przed użyciem szablonu tworzenia upewnij się, że zostały spełnione następujące wymagania wstępne:  

* [Nowa subskrypcja](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Uprawnienia właściciela subskrypcji](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Przydział dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Punkt końcowy usługi magazynu (w razie potrzeby)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) — jest to wymagane w przypadku wdrożeń korzystających z istniejącej sieci wirtualnej i tworzenia magazynu obiektów BLOB

Aby uzyskać więcej informacji o krokach i planowaniu wdrożenia klastra, przeczytaj artykuł Planowanie systemu i [wdrożenia](avere-vfxt-deploy-overview.md) [programu avere vFXT](avere-vfxt-deploy-plan.md) .

## <a name="create-the-avere-vfxt-for-azure"></a>Tworzenie avere vFXT dla platformy Azure

Aby uzyskać dostęp do szablonu tworzenia w Azure Portal, należy wyszukać avere i wybrać pozycję "avere vFXT dla szablonu Azure ARM".

![Okno przeglądarki zawierające Azure Portal z chleb Crumbs "nowe > Marketplace > wszystko". Na stronie wszystko, w polu wyszukiwania znajduje się termin "avere" i drugi wynik, "avere vFXT for Azure ARM template" został wyróżniony kolorem czerwonym, aby go zaznaczyć.](media/avere-vfxt-template-choose.png)

Po zapoznaniu się ze szczegółowymi informacjami na stronie szablon avere vFXT for Azure ARM kliknij przycisk **Utwórz** , aby rozpocząć.

![Portal Azure Marketplace z pierwszą stroną szablonu wdrożenia](media/avere-vfxt-deploy-first.png)

Szablon jest podzielony na cztery kroki — dwie strony zbierające informacje, a także kroki weryfikacji i potwierdzenia.

* Strona 1 zbiera ustawienia dla maszyny wirtualnej kontrolera klastra.
* Druga strona zbiera parametry służące do tworzenia klastra i dodatkowych zasobów, takich jak podsieci i magazyn.
* Na stronie trzy są zestawione wybrane opcje i sprawdzanie poprawności konfiguracji.
* Strona cztery objaśnia warunki użytkowania oprogramowania i umożliwia rozpoczęcie procesu tworzenia klastra.

## <a name="page-one-parameters---cluster-controller-information"></a>Strona z jednym parametrem — informacje o kontrolerze klastra

Pierwsza strona szablonu wdrożenia koncentruje się na kontrolerze klastra.

![Pierwsza strona szablonu wdrożenia](media/avere-vfxt-deploy-1.png)

Wprowadź następujące informacje:

* **Nazwa kontrolera klastra** — Ustaw nazwę maszyny wirtualnej kontrolera klastra.

* **Nazwa użytkownika kontrolera** — Ustaw nazwę użytkownika root dla maszyny wirtualnej kontrolera klastra.

* **Typ uwierzytelniania** — wybierz opcję hasło lub uwierzytelnianie klucza publicznego SSH w celu nawiązania połączenia z kontrolerem. Zalecana jest metoda klucza publicznego SSH; Jeśli potrzebujesz pomocy, przeczytaj artykuł [jak utworzyć klucze SSH i jak z nich korzystać](../virtual-machines/linux/ssh-from-windows.md) .

* **Hasło** lub **klucz publiczny SSH** — w zależności od wybranego typu uwierzytelniania należy podać klucz publiczny RSA lub hasło w następnych polach. To poświadczenie jest używane z podaną wcześniej nazwą użytkownika.

* **Subskrypcja** — wybierz subskrypcję usługi avere vFXT.

* **Grupa zasobów** — wybierz istniejącą pustą grupę zasobów dla klastra avere vFXT lub kliknij pozycję "Utwórz nową" i wprowadź nową nazwę grupy zasobów.

* **Lokalizacja** — wybierz lokalizację platformy Azure dla klastra i zasobów.

Po zakończeniu kliknij przycisk **OK** .

> [!NOTE]
> Jeśli chcesz, aby kontroler klastra miał publiczny adres IP, Utwórz nową sieć wirtualną dla klastra zamiast wybierania istniejącej sieci. To ustawienie znajduje się na stronie dwie.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Strona dwa parametry — informacje o klastrze vFXT

Druga strona szablonu wdrożenia umożliwia ustawienie rozmiaru klastra, typu węzła, rozmiaru pamięci podręcznej i parametrów magazynu między innymi ustawieniami.

![Druga strona szablonu wdrożenia](media/avere-vfxt-deploy-2.png)

* **Liczba węzłów klastra avere vFXT** — wybierz liczbę węzłów w klastrze. Minimalna wartość to trzy węzły, a wartość maksymalna to 12.

* **Hasło do administrowania klastrem** — Utwórz hasło do administrowania klastrem. To hasło jest używane z nazwą użytkownika ```admin``` do logowania się do panelu sterowania klastra, w którym można monitorować klaster i konfigurować ustawienia klastra.

* **Avere vFXT Nazwa klastra** — nadaj klastrowi unikatową nazwę.

* **Rozmiar** — Ta sekcja zawiera typ maszyny wirtualnej, który będzie używany w węzłach klastra. Mimo że istnieje tylko jedna zalecana opcja, link **Zmień rozmiar** otwiera tabelę ze szczegółowymi informacjami o tym typie wystąpienia i linkiem do kalkulatora cen.

* **Rozmiar pamięci podręcznej na węzeł** — pamięć podręczna klastra jest rozłożona na węzły klastra, więc łączny rozmiar pamięci podręcznej w klastrze avere vFXT będzie ten rozmiar pomnożony przez liczbę węzłów.

  Zalecana konfiguracja: Użyj 4 TB na węzeł dla węzłów Standard_E32s_v3.

* **Sieć wirtualna** — Zdefiniuj nową sieć wirtualną, w której znajduje się klaster, lub wybierz istniejącą sieć spełniającą wymagania wstępne opisane w temacie [Planowanie systemu avere vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > W przypadku utworzenia nowej sieci wirtualnej kontroler klastra będzie miał publiczny adres IP, aby można było uzyskać dostęp do nowej sieci prywatnej. W przypadku wybrania istniejącej sieci wirtualnej kontroler klastra zostanie skonfigurowany bez publicznego adresu IP.
  >
  > Publicznie widoczny adres IP na kontrolerze klastra zapewnia łatwiejszy dostęp do klastra vFXT, ale tworzy niewielkie zagrożenie bezpieczeństwa.
  >* Publiczny adres IP na kontrolerze klastra umożliwia korzystanie z niego jako hosta skoku w celu nawiązania połączenia z klastrem usługi avere vFXT spoza podsieci prywatnej.
  >* Jeśli na kontrolerze nie masz publicznego adresu IP, potrzebujesz innego hosta skoku, połączenia sieci VPN lub ExpressRoute, aby uzyskać dostęp do klastra. Na przykład Użyj istniejącej sieci wirtualnej, dla której skonfigurowano już połączenie sieci VPN.
  >* Jeśli tworzysz kontroler z publicznym adresem IP, należy chronić maszynę wirtualną kontrolera przy użyciu sieciowej grupy zabezpieczeń. Domyślnie wdrożenie avere vFXT for Azure tworzy sieciową grupę zabezpieczeń, która ogranicza dostęp do ruchu przychodzącego tylko do portu 22 dla kontrolerów z publicznymi adresami IP. Aby zapewnić lepszą ochronę systemu, można zablokować dostęp do zakresu adresów IP źródła, czyli zezwalać na połączenia tylko z maszyn, które mają być używane do dostępu do klastra.

  Nowa sieć wirtualna jest również konfigurowana z punktem końcowym usługi magazynu dla magazynu obiektów blob platformy Azure i z zablokowaną kontrolą dostępu do sieci, aby zezwalać tylko na adresy IP z podsieci klastra.

* **Podsieć** — wybierz podsieć lub Utwórz nową.

* **Tworzenie i Używanie magazynu obiektów BLOB** — wybierz **wartość true** , aby utworzyć nowy kontener obiektów blob platformy Azure i skonfigurować go jako magazyn zaplecza dla nowego klastra avere vFXT. Ta opcja powoduje także utworzenie nowego konta magazynu w grupie zasobów klastra i utworzenie punktu końcowego usługi magazynu firmy Microsoft w podsieci klastra.
  
  W przypadku podania istniejącej sieci wirtualnej przed utworzeniem klastra musi ona mieć punkt końcowy usługi magazynu. (Aby uzyskać więcej informacji, przeczytaj temat [Planowanie systemu avere vFXT](avere-vfxt-deploy-plan.md)).

  Jeśli nie chcesz tworzyć nowego kontenera, należy ustawić **wartość false** dla tego pola. W takim przypadku należy dołączyć i skonfigurować magazyn po utworzeniu klastra. Przeczytaj temat [Konfigurowanie magazynu](avere-vfxt-add-storage.md) , aby uzyskać instrukcje.

* **(Nowe) konto magazynu** — Jeśli tworzysz nowy kontener obiektów blob platformy Azure, wprowadź nazwę nowego konta magazynu.

## <a name="validation-and-purchase"></a>Sprawdzanie poprawności i zakup

Na trzy strony podsumowuje konfigurację i sprawdza poprawność parametrów. Po pomyślnym zakończeniu walidacji sprawdź Podsumowanie i kliknij przycisk **OK** .

> [!TIP]
> Ustawienia tworzenia klastra można zapisać, klikając link **Pobierz szablon i parametry** obok przycisku **OK** . Te informacje mogą być przydatne, jeśli trzeba utworzyć podobny klaster później — na przykład, aby utworzyć zastępczy klaster w scenariuszu odzyskiwania po awarii. (Przeczytaj [wskazówki dotyczące odzyskiwania po awarii](disaster-recovery.md) , aby dowiedzieć się więcej).

![Trzecia strona szablonu wdrożenia — Walidacja](media/avere-vfxt-deploy-3.png)

Strona cztery zawiera warunki użytkowania oraz linki do informacji o prywatności i cenach.

Wprowadź brakujące informacje kontaktowe, a następnie kliknij przycisk **Utwórz** , aby zaakceptować warunki i utworzyć avere vFXT dla klastra platformy Azure.

![Czwarta Strona szablonu wdrożenia — warunki i postanowienia, przycisk Utwórz](media/avere-vfxt-deploy-4.png)

Wdrożenie klastra trwa 15-20 minut.

## <a name="gather-template-output"></a>Zbierz dane wyjściowe szablonu

Po zakończeniu tworzenia klastra przez szablon avere vFXT należy zwrócić ważne informacje o nowym klastrze.

> [!TIP]
> Upewnij się, że **adres IP zarządzania** jest kopiowany z szablonu wyjściowego. Ten adres jest potrzebny do administrowania klastrem.

Aby znaleźć informacje:

1. Przejdź do grupy zasobów dla kontrolera klastra.

1. Po lewej stronie kliknij pozycje **wdrożenia**, a następnie pozycję **Microsoft-avere. vfxt-Template**.

   ![Strona portalu grupy zasobów z wdrożeniami wybranymi po lewej stronie i Microsoft-avere. vfxt-Template pokazanych w tabeli pod nazwą wdrożenia](media/avere-vfxt-outputs-deployments.png)

1. Po lewej stronie kliknij pozycję dane **wyjściowe**. Skopiuj wartości w każdym z pól.

   ![Strona danych wyjściowych pokazująca wartości SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs i MGMT_IP w polach z prawej strony etykiet](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy klaster działa i znasz jego adres IP zarządzania, Połącz się z [narzędziem konfiguracji klastra](avere-vfxt-cluster-gui.md).

Użyj interfejsu konfiguracji, aby dostosować klaster, łącznie z następującymi zadaniami instalacji:

* [Włącz obsługę](avere-vfxt-enable-support.md)
* [Dodaj magazyn](avere-vfxt-add-storage.md) (w razie konieczności)