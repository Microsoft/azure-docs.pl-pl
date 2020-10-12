---
title: Włącz replikację dla maszyn lokalnych z prywatnymi punktami końcowymi
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn lokalnych przy użyciu prywatnych punktów końcowych w Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 3d15f4039da85dfa926e7bc9ab96b2c48965d5f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658807"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Replikowanie maszyn lokalnych za pomocą prywatnych punktów końcowych

Azure Site Recovery umożliwia używanie prywatnych punktów końcowych prywatnego [linku platformy Azure](../private-link/private-endpoint-overview.md) do replikowania maszyn lokalnych do sieci wirtualnej na platformie Azure. Dostęp prywatnego punktu końcowego do magazynu odzyskiwania jest obsługiwany we wszystkich regionach instytucji rządowych & handlowych platformy Azure.

W tym artykule opisano sposób wykonywania następujących czynności:

- Utwórz magazyn Recovery Services Azure Backup, aby chronić maszyny.
- Włącz zarządzaną tożsamość magazynu. Przyznaj uprawnienia wymagane do uzyskania dostępu do kont magazynu, aby umożliwić replikację ruchu z zasobów lokalnych do lokalizacji docelowej platformy Azure. Zarządzany dostęp do tożsamości dla magazynu jest wymagany do prywatnego połączenia z magazynem.

- Wprowadź zmiany DNS, które są wymagane dla prywatnych punktów końcowych.
- Utwórz i zatwierdź prywatne punkty końcowe dla magazynu w sieci wirtualnej.
- Utwórz prywatne punkty końcowe dla kont magazynu. W razie potrzeby można nadal zezwalać na dostęp do magazynu publicznego lub przez zaporę. Tworzenie prywatnego punktu końcowego w celu uzyskania dostępu do magazynu nie jest wymagane dla Azure Site Recovery.
  
Na poniższym diagramie przedstawiono przepływ pracy replikacji dla hybrydowego odzyskiwania po awarii z prywatnymi punktami końcowymi. Nie można tworzyć prywatnych punktów końcowych w sieci lokalnej. Aby można było korzystać z linków prywatnych, należy utworzyć sieć wirtualną platformy Azure (nazywaną *siecią obejścia* w tym artykule), ustanowić połączenie prywatne między siecią lokalną i pominięciem, a następnie utworzyć prywatne punkty końcowe w sieci pomijania. Możesz wybrać dowolną formę połączenia prywatnego.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

## <a name="prerequisites-and-caveats"></a>Wymagania wstępne i zastrzeżenia

- Linki prywatne są obsługiwane w Site Recovery 9,35 i nowszych.
- Prywatne punkty końcowe można tworzyć tylko dla nowych magazynów Recovery Services, które nie mają zarejestrowanych żadnych elementów. Dlatego przed dodaniem elementów do magazynu należy utworzyć prywatne punkty końcowe. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/) .
- W przypadku tworzenia prywatnego punktu końcowego dla magazynu magazyn jest zablokowany. Dostęp do niego można uzyskać tylko z sieci z prywatnymi punktami końcowymi.
- Azure Active Directory nie obsługuje obecnie prywatnych punktów końcowych. W związku z tym musisz zezwolić na dostęp wychodzący z zabezpieczonej sieci wirtualnej platformy Azure do adresów IP i w pełni kwalifikowanych nazw domen, które są wymagane, aby Azure Active Directory działały w regionie.
  W razie potrzeby można również użyć tagów grup zabezpieczeń sieci "Azure Active Directory" i Tagi zapory platformy Azure, aby umożliwić dostęp do Azure Active Directory.
- W sieci pomijania, w której tworzysz prywatny punkt końcowy, są wymagane pięć adresów IP. Podczas tworzenia prywatnego punktu końcowego dla magazynu Site Recovery tworzy pięć prywatnych linków, aby uzyskać dostęp do jego mikrousług.
- Jeden dodatkowy adres IP jest wymagany w sieci pomijanie łączności z prywatnym punktem końcowym z kontem magazynu pamięci podręcznej. Możesz użyć dowolnej metody łączności między środowiskiem lokalnym a punktem końcowym konta magazynu. Można na przykład użyć Internetu lub Azure [ExpressRoute](../expressroute/index.yml). Nawiązywanie połączenia prywatnego jest opcjonalne. Prywatne punkty końcowe dla magazynu można tworzyć tylko na kontach Ogólnego przeznaczenia v2. Zobacz [Cennik obiektów blob platformy Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) , aby uzyskać informacje o cenach transferu danych na kontach ogólnego przeznaczenia v2.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Tworzenie i używanie prywatnych punktów końcowych na potrzeby usługi Site Recovery

 W poniższych sekcjach opisano kroki, które należy wykonać, aby utworzyć i używać prywatnych punktów końcowych usługi Site Recovery w sieciach wirtualnych.

> [!NOTE]
> Zalecamy wykonanie poniższych kroków w podanej kolejności. Jeśli tego nie zrobisz, możesz nie być w stanie używać prywatnych punktów końcowych w magazynie i konieczne może być ponowne uruchomienie procesu z nowym magazynem.

### <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services zawiera informacje o replikacji maszyn. Służy do wyzwalania operacji Site Recovery. Aby uzyskać informacje dotyczące sposobu tworzenia magazynu Recovery Services w regionie świadczenia usługi Azure, w którym chcesz przejść do trybu failover w przypadku awarii, zobacz [Tworzenie magazynu Recovery Services](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Włącz zarządzaną tożsamość magazynu

[Zarządzana tożsamość](../active-directory/managed-identities-azure-resources/overview.md) umożliwia magazynowi dostęp do kont magazynu. Site Recovery może być konieczne uzyskanie dostępu do magazynu docelowego i kont magazynu pamięci podręcznej/dziennika, w zależności od wymagań. Dostęp do tożsamości zarządzanej jest wymagany w przypadku korzystania z usługi link prywatny dla magazynu.

1. Przejdź do magazynu Recovery Services. Wybierz **tożsamość** w obszarze **Ustawienia**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

1. Zmień **stan** na **włączone** i wybierz pozycję **Zapisz**.

   Identyfikator obiektu jest generowany. Magazyn jest teraz zarejestrowany w Azure Active Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Tworzenie prywatnych punktów końcowych dla magazynu Recovery Services

Aby chronić maszyny w lokalnej sieci źródłowej, musisz mieć jeden prywatny punkt końcowy dla magazynu w sieci pomijania. Utwórz prywatny punkt końcowy przy użyciu prywatnego centrum linków w Azure Portal lub przy użyciu [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. W polu wyszukiwania Azure Portal Wyszukaj ciąg "link prywatny". Wybierz **link prywatny** , aby przejść do prywatnego centrum linków:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

1. W lewym okienku wybierz pozycję **prywatne punkty końcowe**. Na stronie **prywatne punkty końcowe** wybierz pozycję **Dodaj** , aby rozpocząć tworzenie prywatnego punktu końcowego dla magazynu:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

1. Na stronie **Tworzenie prywatnego punktu końcowego** Określ szczegóły, aby utworzyć połączenie prywatnego punktu końcowego.

   1. **Podstawowe informacje**. Podaj podstawowe informacje o prywatnych punktach końcowych. Użyj regionu, który został użyty dla sieci pomijania:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

   1. **Zasób**. Na tej karcie należy określić zasób platformy jako usługi, dla którego chcesz utworzyć połączenie. W obszarze **Typ zasobu** dla wybranej subskrypcji wybierz pozycję **Microsoft. RecoveryServices/magazyny**. Wybierz nazwę magazynu Recovery Services w obszarze **zasób**. Wybierz **Azure Site Recovery** jako **docelowy zasób podrzędny**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

   1. **Konfiguracja**. Na tej karcie określ opcję Pomijaj sieć i podsieć, w której ma zostać utworzony prywatny punkt końcowy. 

      Aby włączyć integrację z prywatną strefą DNS, wybierz pozycję **tak**.
      Wybierz istniejącą strefę DNS lub Utwórz nową. Wybranie opcji **tak** powoduje automatyczne dołączenie strefy do sieci pomijania. Ta akcja powoduje również dodanie rekordów DNS, które są wymagane do rozpoznawania nazw DNS nowych adresów IP i w pełni kwalifikowanych nazwy domen utworzonych dla prywatnego punktu końcowego.

      Upewnij się, że wybrano opcję utworzenia nowej strefy DNS dla każdego nowego prywatnego punktu końcowego łączącego się z tym samym magazynem. W przypadku wybrania istniejącej prywatnej strefy DNS poprzednie rekordy CNAME są zastępowane. Przed kontynuowaniem Zobacz informacje [dotyczące prywatnych punktów końcowych](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Jeśli środowisko ma model Hub i szprych, do całej konfiguracji jest wymagany tylko jeden prywatny punkt końcowy i tylko jedna prywatna strefa DNS. Wynika to z faktu, że wszystkie sieci wirtualne mają już włączone komunikację równorzędną między nimi. Aby uzyskać więcej informacji, zobacz [prywatny punkt końcowy usługi DNS](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Aby ręcznie utworzyć prywatną strefę DNS, wykonaj kroki opisane w temacie [tworzenie prywatnych stref DNS i ręczne Dodawanie rekordów DNS](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

   1. **Tagi**. Opcjonalnie możesz dodać Tagi dla prywatnego punktu końcowego.

   1. **Przejrzyj temat \+ Tworzenie**. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.

Po utworzeniu prywatnego punktu końcowego do prywatnego punktu końcowego dodawane są pięć w pełni kwalifikowanych nazw domen (FQDN). Te linki umożliwiają dostęp do maszyn w sieci lokalnej za pośrednictwem sieci pomijania wszystkich wymaganych Site Recovery mikrousług w kontekście magazynu. Możesz użyć tego samego prywatnego punktu końcowego do ochrony dowolnej maszyny platformy Azure w sieci pomijania i wszystkich komunikacji równorzędnej.

W tym wzorcu są sformatowane pięć nazw domen:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Zatwierdzanie prywatnych punktów końcowych usługi Site Recovery

Jeśli utworzysz prywatny punkt końcowy i jesteś również właścicielem magazynu Recovery Services, utworzony wcześniej prywatny punkt końcowy zostanie automatycznie zatwierdzony w ciągu kilku minut. W przeciwnym razie właściciel magazynu musi zatwierdzić prywatny punkt końcowy, aby można było go użyć. Aby zatwierdzić lub odrzucić żądane połączenie prywatnego punktu końcowego, przejdź do pozycji **prywatne połączenia punktu końcowego** w obszarze **Ustawienia** na stronie Magazyn odzyskiwania.

Aby sprawdzić stan połączenia przed kontynuowaniem, można przejść do zasobu prywatnego punktu końcowego:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Obowiązkowe Utwórz prywatne punkty końcowe dla konta magazynu pamięci podręcznej

Do usługi Azure Storage można użyć prywatnego punktu końcowego. Tworzenie prywatnych punktów końcowych dostępu do magazynu jest opcjonalne dla replikacji Azure Site Recovery. Jeśli utworzysz prywatny punkt końcowy dla magazynu, potrzebujesz prywatnego punktu końcowego dla konta magazynu pamięci podręcznej/dziennika w sieci wirtualnej pomijania.

> [!NOTE]
> Prywatne punkty końcowe dla magazynu można utworzyć tylko na kontach magazynu Ogólnego przeznaczenia v2. Aby uzyskać informacje o cenach, zobacz [Cennik obiektów blob platformy Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Postępuj zgodnie ze [wskazówkami dotyczącymi tworzenia prywatnego magazynu](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) , aby utworzyć konto magazynu przy użyciu prywatnego punktu końcowego. Upewnij się, że wybrano opcję **tak** w obszarze **integracja z prywatną strefą DNS**. Wybierz istniejącą strefę DNS lub Utwórz nową.

### <a name="grant-required-permissions-to-the-vault"></a>Przyznawanie wymaganych uprawnień do magazynu

W zależności od konfiguracji może być wymagane co najmniej jedno konto magazynu w docelowym regionie platformy Azure. Następnie Udziel uprawnień zarządzanej tożsamości dla wszystkich kont magazynu pamięci podręcznej/dzienników wymaganych przez Site Recovery. W takim przypadku należy wcześniej utworzyć wymagane konta magazynu.

Przed włączeniem replikacji maszyn wirtualnych, zarządzana tożsamość magazynu musi mieć następujące uprawnienia roli, w zależności od typu konta magazynu.

- Konta magazynu oparte na Menedżer zasobów (typ standardowy):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor)
  - [Współautor danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Konta magazynu oparte na Menedżer zasobów (typ Premium):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor)
  - [Właściciel danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasyczne konta magazynu:
  - [Współautor klasycznego konta magazynu](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Rola usługi operatora kluczy klasycznego konta magazynu](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

W tych krokach opisano sposób dodawania przypisania roli do konta magazynu:

1. Przejdź do konta magazynu. W lewym okienku wybierz pozycję **Kontrola dostępu (IAM)** .

1. W sekcji **Dodawanie przypisania roli** wybierz pozycję **Dodaj**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

1. Na stronie **Dodawanie przypisania roli** na liście **rola** wybierz rolę z listy na początku tej sekcji. Wprowadź nazwę magazynu, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

Po dodaniu tych uprawnień musisz zezwolić na dostęp do zaufanych usług firmy Microsoft. Przejdź do obszaru **zapory i sieci wirtualne** , a następnie wybierz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w **wyjątkach**.

### <a name="protect-your-virtual-machines"></a>Ochrona maszyn wirtualnych

Po zakończeniu powyższych zadań przejdź do konfiguracji infrastruktury lokalnej. Wykonaj jedną z następujących czynności: 

- [Wdrażanie serwera konfiguracji dla oprogramowania VMware i maszyn fizycznych](./vmware-azure-deploy-configuration-server.md)
- [Konfigurowanie środowiska Hyper-V na potrzeby replikacji](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Po zakończeniu instalacji Włącz replikację dla maszyn źródłowych. Nie należy konfigurować infrastruktury do momentu utworzenia prywatnych punktów końcowych dla magazynu w sieci pomijania.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Utwórz prywatne strefy DNS i ręcznie Dodaj rekordy DNS

Jeśli nie wybrano opcji integracji z prywatną strefą DNS podczas tworzenia prywatnego punktu końcowego dla magazynu, wykonaj kroki opisane w tej sekcji.

Utwórz jedną prywatną strefę DNS, aby zezwolić dostawcy Site Recovery (dla maszyn funkcji Hyper-V) lub serwerowi przetwarzania (dla maszyn wirtualnych VMware/Physical) na rozpoznawanie prywatnych nazw FQDN do prywatnych adresów IP.

1. Utwórz prywatną strefę DNS.

   1. Wyszukaj frazę "prywatna strefa DNS" w polu wyszukiwania **wszystkie usługi** , a następnie w wynikach wybierz pozycję **prywatna strefa DNS Zone** :

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

   1. Na stronie **strefy prywatna strefa DNS** wybierz przycisk **Dodaj** , aby rozpocząć tworzenie nowej strefy.

   1. Na stronie **Tworzenie prywatnego strefy DNS** wprowadź wymagane szczegóły. Wprowadź **privatelink.siterecovery.windowsazure.com** dla nazwy prywatnej strefy DNS. Możesz wybrać dowolną grupę zasobów i dowolną subskrypcję.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

   1. Przejdź do karty **Recenzja \+ Create (Tworzenie** ), aby przejrzeć i utworzyć strefę DNS.

1. Połącz prywatną strefę DNS z siecią wirtualną.

   Teraz musisz połączyć prywatną strefę DNS utworzoną w ramach obejścia.

   1. Przejdź do prywatnej strefy DNS, która została utworzona w poprzednim kroku, a następnie przejdź do pozycji **linki sieci wirtualnej** w lewym okienku. Wybierz pozycję **Dodaj**.

   1. Wprowadź wymagane szczegóły. Na liście **subskrypcja** i **Sieć wirtualna** wybierz Szczegóły, które odpowiadają sieci pomijania. W innych polach pozostaw wartości domyślne.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

1. Dodawanie rekordów DNS.

   Po utworzeniu wymaganej prywatnej strefy DNS i prywatnego punktu końcowego należy dodać rekordy DNS do strefy DNS.

   > [!NOTE]
   > Jeśli używasz niestandardowej prywatnej strefy DNS, pamiętaj o wprowadzeniu podobnych wpisów zgodnie z opisem w poniższym kroku.

   W tym kroku należy wprowadzić wpisy dla każdej nazwy FQDN w prywatnym punkcie końcowym do prywatnej strefy DNS.

   1. Przejdź do prywatnej strefy DNS, a następnie przejdź do sekcji **Przegląd** w okienku po lewej stronie. Wybierz pozycję **zestaw rekordów** , aby rozpocząć Dodawanie rekordów.

   1. Na stronie **Dodawanie zestawu rekordów** Dodaj wpis dla każdej w pełni kwalifikowanej nazwy domeny i prywatnego adresu IP jako rekord **typu.** Listę w pełni kwalifikowanych nazw domen i adresów IP można uzyskać na stronie **prywatnego punktu końcowego** w temacie **Omówienie**. Jak widać na poniższym zrzucie ekranu, pierwsza w pełni kwalifikowana nazwa domeny z prywatnego punktu końcowego jest dodawana do zestawu rekordów w prywatnej strefie DNS.

      Te w pełni kwalifikowane nazwy domen pasują do tego wzorca: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Diagram przedstawiający architekturę Azure Site Recovery i prywatnych punktów końcowych.":::

## <a name="next-steps"></a>Następne kroki

Po włączeniu prywatnych punktów końcowych dla replikacji maszyny wirtualnej zapoznaj się z innymi artykułami, aby uzyskać dodatkowe informacje i pokrewne:

- [Wdrażanie lokalnego serwera konfiguracji](./vmware-azure-deploy-configuration-server.md)
- [Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure](./hyper-v-azure-tutorial.md)