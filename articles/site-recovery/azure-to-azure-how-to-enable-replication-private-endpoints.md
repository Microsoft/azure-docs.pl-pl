---
title: Włącz replikację prywatnych punktów końcowych w Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych z prywatnymi punktami końcowymi z jednego regionu świadczenia usługi Azure do innego przy użyciu Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 37784c4a294ccf296818f2afb1a8a345cb9d813e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658259"
---
# <a name="replicate-machines-with-private-endpoints"></a>Replikowanie maszyn z prywatnymi punktami końcowymi

Azure Site Recovery umożliwia używanie prywatnych punktów końcowych prywatnego [linku platformy Azure](../private-link/private-endpoint-overview.md) do replikowania maszyn z wewnątrz izolowanej sieci wirtualnej. Dostęp prywatnego punktu końcowego do magazynu odzyskiwania jest obsługiwany we wszystkich regionach instytucji rządowych & handlowych platformy Azure.

Ten artykuł zawiera instrukcje dotyczące wykonywania następujących czynności:

- Utwórz magazyn Recovery Services Azure Backup, aby chronić maszyny.
- Włącz zarządzaną tożsamość magazynu i przyznaj wymagane uprawnienia dostępu do kont magazynu klienta, aby replikować ruch ze źródła do lokalizacji docelowych. Zarządzany dostęp do tożsamości dla magazynu jest wymagany podczas konfigurowania prywatnego dostępu do magazynu.
- Wprowadź zmiany DNS wymagane dla prywatnych punktów końcowych
- Tworzenie i zatwierdzanie prywatnych punktów końcowych dla magazynu w sieci wirtualnej
- Utwórz prywatne punkty końcowe dla kont magazynu. W razie potrzeby można nadal zezwalać na dostęp do magazynu publicznego lub przez zaporę. Tworzenie prywatnego punktu końcowego do uzyskiwania dostępu do magazynu nie jest obowiązkowe dla Azure Site Recovery.
  
Poniżej znajduje się Architektura referencyjna, w jaki przepływ pracy replikacji zmienia się z prywatnymi punktami końcowymi.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

## <a name="prerequisites-and-caveats"></a>Wymagania wstępne i zastrzeżenia

- Prywatne punkty końcowe można tworzyć tylko dla nowych magazynów Recovery Services, które nie mają żadnych elementów zarejestrowanych w magazynie. W związku z tym należy utworzyć prywatne punkty końcowe **przed dodaniem jakichkolwiek elementów do magazynu**. Zapoznaj się ze strukturą cenową [prywatnych punktów końcowych](https://azure.microsoft.com/pricing/details/private-link/).
- Po utworzeniu prywatnego punktu końcowego dla magazynu magazyn jest zablokowany i **nie jest dostępny z sieci innych niż te sieci, które mają prywatne punkty końcowe**.
- Azure Active Directory obecnie nie obsługuje prywatnych punktów końcowych. W związku z tym adresy IP i w pełni kwalifikowane nazwy domen wymagane do Azure Active Directory pracy w regionie muszą mieć dozwolony dostęp wychodzący z zabezpieczonej sieci. Można również użyć tagów grup zabezpieczeń sieci "Azure Active Directory" i tagów zapory platformy Azure, aby umożliwić dostęp do Azure Active Directory, zgodnie z wymaganiami.
- **Co najmniej siedem adresów IP jest wymaganych** w podsieciach obu maszyn źródłowych i maszyn odzyskiwania. Podczas tworzenia prywatnego punktu końcowego dla magazynu Site Recovery tworzy pięć prywatnych linków, aby uzyskać dostęp do jego mikrousług. Ponadto po włączeniu replikacji dodawane są dwa dodatkowe linki prywatne dla parowania obszaru źródłowego i docelowego.
- **Jeden dodatkowy adres IP jest wymagany** zarówno w podsieci źródłowej, jak i odzyskiwania. Ten adres IP jest wymagany tylko wtedy, gdy musisz używać prywatnych punktów końcowych do łączenia się z kontami magazynu pamięci podręcznej.
  Prywatne punkty końcowe dla magazynu można utworzyć tylko w przypadku typu Ogólnego przeznaczenia v2. Zapoznaj się ze strukturą cenową [transferu danych w witrynie GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Tworzenie i używanie prywatnych punktów końcowych dla Site Recovery

 W tej sekcji omówiono kroki związane z tworzeniem i używaniem prywatnych punktów końcowych w celu Azure Site Recovery wewnątrz sieci wirtualnych.

> [!NOTE]
> Zdecydowanie zaleca się wykonanie tych kroków w tej samej kolejności, w jakiej zostały podane. Niewykonanie tej czynności może prowadzić do tego, że magazyn, który jest renderowany, nie może używać prywatnych punktów końcowych i wymaga ponownego uruchomienia procesu z nowym magazynem.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services jest jednostką, która zawiera informacje o replikacji maszyn i służy do wyzwalania operacji Site Recovery. Aby uzyskać więcej informacji, zobacz [Tworzenie magazynu Recovery Services](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Włącz zarządzaną tożsamość magazynu.

[Zarządzana tożsamość](../active-directory/managed-identities-azure-resources/overview.md) zezwala magazynowi na uzyskanie dostępu do kont magazynu klienta. Site Recovery musi uzyskać dostęp do magazynu źródłowego, magazynu docelowego i kont magazynu pamięci podręcznej/dziennika w zależności od wymagań scenariusza.
Dostęp do tożsamości zarządzanej jest istotny, gdy korzystasz z usługi linki prywatne dla magazynu.

1. Przejdź do magazynu Recovery Services. W obszarze _Ustawienia_wybierz pozycję **tożsamość** .

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

1. Zmień **stan** na _włączone_ i wybierz pozycję **Zapisz**.

1. Generowany jest **Identyfikator obiektu** wskazujący, że magazyn jest teraz zarejestrowany w Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Tworzenie prywatnych punktów końcowych dla magazynu Recovery Services

Aby włączyć tryb failover i powrotu po awarii dla usługi Azure Virtual Machines, potrzebne są dwa prywatne punkty końcowe dla magazynu. Jeden prywatny punkt końcowy ochrony maszyn w sieci źródłowej, a druga w przypadku maszyn w sieci odzyskiwania w trybie failover.

Upewnij się, że sieć wirtualna odzyskiwania jest tworzona w regionie docelowym, a także w trakcie tego procesu instalacji.

Utwórz pierwszy prywatny punkt końcowy dla swojego magazynu w źródłowej sieci wirtualnej przy użyciu prywatnego centrum linku w portalu lub za pośrednictwem [Azure PowerShell](../private-link/create-private-endpoint-powershell.md). Utwórz drugi prywatny punkt końcowy dla magazynu w ramach sieci odzyskiwania. Poniżej przedstawiono procedurę tworzenia prywatnego punktu końcowego w sieci źródłowej. Powtórz te same wskazówki, aby utworzyć drugi prywatny punkt końcowy.

1. Na pasku wyszukiwania Azure Portal Wyszukaj i wybierz pozycję "link prywatny". Ta akcja spowoduje przejście do prywatnego centrum linków.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

1. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **prywatne punkty końcowe**. Na stronie prywatne punkty końcowe wybierz pozycję ** \+ Dodaj** , aby rozpocząć tworzenie prywatnego punktu końcowego dla magazynu.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi." wymagane jest określenie szczegółów dotyczących tworzenia połączenia prywatnego punktu końcowego.

   1. **Podstawowe**: Podaj podstawowe informacje o prywatnych punktach końcowych. Region powinien być taki sam jak maszyny źródłowe.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

   1. **Zasób**: na tej karcie należy wspomnieć zasób platformy jako usługi, dla którego chcesz utworzyć połączenie. Wybierz pozycję _Microsoft. RecoveryServices/magazyny_ z **typu zasobu** dla wybranej subskrypcji. Następnie wybierz nazwę magazynu Recovery Services dla **zasobu** i ustaw _Azure Site Recovery_ jako **docelowy zasób podrzędny**.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

   1. **Konfiguracja**: w obszarze Konfiguracja Określ sieć wirtualną i podsieć, w której ma zostać utworzony prywatny punkt końcowy. Ta sieć wirtualna jest siecią, w której znajduje się maszyna wirtualna. Włącz integrację z prywatną strefą DNS, wybierając opcję **tak**. Wybierz już utworzoną strefę DNS lub Utwórz nową. Wybranie opcji **tak** automatycznie łączy strefę ze źródłową siecią wirtualną i dodaje rekordy DNS, które są wymagane do rozpoznawania nazw DNS nowych adresów IP i w pełni kwalifikowanych nazwy domen utworzonych dla prywatnego punktu końcowego.

      Upewnij się, że wybrano opcję utworzenia nowej strefy DNS dla każdego nowego prywatnego punktu końcowego łączącego się z tym samym magazynem. W przypadku wybrania istniejącej prywatnej strefy DNS poprzednie rekordy CNAME są zastępowane. Przed kontynuowaniem zapoznaj się ze [wskazówkami dotyczącymi prywatnych punktów końcowych](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Jeśli środowisko ma model koncentratora i szprychy, musisz tylko jeden prywatny punkt końcowy i tylko jedną prywatną strefę DNS dla całej konfiguracji, ponieważ wszystkie sieci wirtualne mają już włączone komunikację równorzędną między nimi. Aby uzyskać więcej informacji, zobacz [prywatny punkt końcowy usługi DNS](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Aby ręcznie utworzyć prywatną strefę DNS, wykonaj kroki opisane w temacie [tworzenie prywatnych stref DNS i ręczne Dodawanie rekordów DNS](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

   1. **Tagi**: Opcjonalnie możesz dodać Tagi dla prywatnego punktu końcowego.

   1. **Przegląd \+ tworzenia**: po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.

Po utworzeniu prywatnego punktu końcowego do prywatnego punktu końcowego dodawane są pięć w pełni kwalifikowanych nazw domen. Te linki umożliwiają komputerom w sieci wirtualnej uzyskanie dostępu do wszystkich wymaganych Site Recovery mikrousług w kontekście magazynu. Później po włączeniu replikacji do tego samego prywatnego punktu końcowego dodawane są dwa dodatkowe, w pełni kwalifikowane nazwy domen.

Pięć nazw domen jest sformatowanych przy użyciu następującego wzorca:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Zatwierdź prywatne punkty końcowe dla Site Recovery

Jeśli użytkownik tworzący prywatny punkt końcowy jest również właścicielem magazynu Recovery Services, prywatny punkt końcowy utworzony powyżej zostanie zaakceptowany w ciągu kilku minut. W przeciwnym razie właściciel magazynu musi zatwierdzić prywatny punkt końcowy przed jego użyciem. Aby zatwierdzić lub odrzucić żądane połączenie prywatnego punktu końcowego, przejdź do pozycji **prywatne połączenia punktów końcowych** w obszarze "Ustawienia" na stronie Magazyn odzyskiwania.

Aby sprawdzić stan połączenia przed kontynuowaniem, można przejść do zasobu prywatnego punktu końcowego.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Obowiązkowe Utwórz prywatne punkty końcowe dla konta magazynu pamięci podręcznej

Można użyć prywatnego punktu końcowego w usłudze Azure Storage. Tworzenie prywatnych punktów końcowych dostępu do magazynu jest _opcjonalne_ dla replikacji Azure Site Recovery. Podczas tworzenia prywatnego punktu końcowego dla magazynu są stosowane następujące wymagania:

- Potrzebujesz prywatnego punktu końcowego dla konta magazynu pamięci podręcznej/dziennika w źródłowej sieci wirtualnej.
- Potrzebny jest drugi prywatny punkt końcowy w czasie ponownej ochrony maszyn przełączonej w tryb failover w sieci odzyskiwania. Ten prywatny punkt końcowy jest przeznaczony dla nowego konta magazynu utworzonego w regionie docelowym.

> [!NOTE]
> Prywatny punkt końcowy dla magazynu można utworzyć tylko na kontach magazynu **ogólnego przeznaczenia v2** . Aby uzyskać informacje o cenach, zobacz [standardowe ceny obiektów BLOB na stronie](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Postępuj zgodnie ze [wskazówkami dotyczącymi tworzenia prywatnego magazynu](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) , aby utworzyć konto magazynu z prywatnym punktem końcowym. Upewnij się, że wybierz opcję **tak** , aby przeprowadzić integrację z prywatną strefą DNS. Wybierz już utworzoną strefę DNS lub Utwórz nową.

## <a name="grant-required-permissions-to-the-vault"></a>Przyznawanie wymaganych uprawnień do magazynu

Jeśli maszyny wirtualne używają dysków zarządzanych, należy przyznać zarządzanym kontom tylko do kont magazynu pamięci podręcznej. Na wypadek, gdy maszyny wirtualne korzystają z dysków niezarządzanych, należy przyznać zarządzanym kontom magazynów dla źródłowych, pamięci podręcznej i docelowych kont magazynu. W takim przypadku należy wcześniej utworzyć docelowe konto magazynu.

Przed włączeniem replikacji maszyn wirtualnych, zarządzana tożsamość magazynu musi mieć następujące uprawnienia roli w zależności od typu konta magazynu:

- Konta magazynu oparte na Menedżer zasobów (typ standardowy):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor)
  - [Współautor danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Konta magazynu oparte na Menedżer zasobów (typ warstwy Premium):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor)
  - [Właściciel danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasyczne konta magazynu:
  - [Współautor klasycznego konta magazynu](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Rola usługi operatora kluczy klasycznego konta magazynu](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

W poniższych krokach opisano, jak dodać przypisanie roli do kont magazynu, pojedynczo:

1. Przejdź do konta magazynu i przejdź do obszaru **kontroli dostępu (IAM)** w lewej części strony.

1. Po włączeniu **kontroli dostępu (IAM)** w polu "Dodaj przypisanie roli" Wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi." Wybierz rolę z powyższej listy na liście rozwijanej **rola** . Wprowadź **nazwę** magazynu i wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi." w **wyjątkach**.

## <a name="protect-your-virtual-machines"></a>Ochrona maszyn wirtualnych

Po zakończeniu wszystkich powyższych konfiguracji Kontynuuj włączanie replikacji dla maszyn wirtualnych. Wszystkie Site Recovery operacje działają bez żadnych dodatkowych czynności, jeśli podczas tworzenia prywatnych punktów końcowych w magazynie użyto integracji usługi DNS. Jeśli jednak strefy DNS są ręcznie tworzone i konfigurowane, należy wykonać dodatkowe czynności, aby dodać określone rekordy DNS w źródłowej i docelowej strefie DNS po włączeniu replikacji. Aby uzyskać szczegółowe informacje i kroki, zobacz [tworzenie prywatnych stref DNS i Dodawanie rekordów DNS ręcznie](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Utwórz prywatne strefy DNS i ręcznie Dodaj rekordy DNS

Jeśli nie wybrano opcji integracji z prywatną strefą DNS w momencie tworzenia prywatnego punktu końcowego dla magazynu, wykonaj kroki opisane w tej sekcji.

Utwórz jedną prywatną strefę DNS, aby zezwolić agentowi mobilności na rozpoznawanie prywatnych adresów IP w pełni kwalifikowanych nazw domen.

1. Tworzenie prywatnej strefy DNS

   1. Wyszukaj ciąg "Prywatna strefa DNS Zone" na pasku wyszukiwania **wszystkie usługi** i wybierz pozycję "strefy prywatna strefa DNS" z listy rozwijanej.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi." Wprowadź wymagane szczegóły. Wprowadź nazwę prywatnej strefy DNS jako `privatelink.siterecovery.windowsazure.com` . Możesz wybrać dowolną grupę zasobów i dowolną subskrypcję, aby ją utworzyć.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

   1. Przejdź do karty **Recenzja \+ Create (Tworzenie** ), aby przejrzeć i utworzyć strefę DNS.

1. Połącz prywatną strefę DNS z siecią wirtualną

   Prywatne strefy DNS utworzone powyżej muszą teraz być połączone z siecią wirtualną, w której obecnie znajdują się serwery. Należy również połączyć prywatną strefę DNS z zaawansowaną docelową siecią wirtualną.

   1. Przejdź do prywatnej strefy DNS, która została utworzona w poprzednim kroku, i przejdź do pozycji **linki sieci wirtualnej** w lewej części strony. Po wybraniu tej opcji wybierz przycisk ** \+ Dodaj** .

   1. Wprowadź wymagane szczegóły. Pola **subskrypcja** i **Sieć wirtualna** muszą zawierać odpowiednie szczegóły dotyczące sieci wirtualnej, w której znajdują się serwery. Pozostałe pola muszą pozostać w postaci, w jakiej jest.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi." w temacie **Omówienie**. Jak pokazano w poniższym przykładzie, pierwsza w pełni kwalifikowana nazwa domeny z prywatnego punktu końcowego jest dodawana do zestawu rekordów w prywatnej strefie DNS.

      Te w pełni kwalifikowane nazwy domen pasują do wzorca: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Architektura referencyjna dla Site Recovery z prywatnymi punktami końcowymi.":::

   > [!NOTE]
   > Po włączeniu replikacji do prywatnych punktów końcowych w obu regionach są tworzone dwa więcej w pełni kwalifikowanych nazw domen. Upewnij się, że dodano rekordy DNS dla tych nowo utworzonych w pełni kwalifikowanych nazw domen.

## <a name="next-steps"></a>Następne kroki

Teraz, po włączeniu prywatnych punktów końcowych dla replikacji maszyny wirtualnej, zobacz te inne strony, aby uzyskać dodatkowe i pokrewne informacje:

- [Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure](./azure-to-azure-how-to-enable-replication.md)
- [Samouczek: Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](./azure-to-azure-tutorial-enable-replication.md)