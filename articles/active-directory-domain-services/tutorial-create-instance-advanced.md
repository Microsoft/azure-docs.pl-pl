---
title: Samouczek — tworzenie wystąpienia usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować wystąpienie usług domenowych usługi active directory platformy Azure i określić zaawansowane opcje konfiguracji przy użyciu witryny Azure portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: f2d7f1725623dcc031f3c2b36bacd6dbc9ad339d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639949"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Samouczek: Tworzenie i konfigurowanie wystąpienia usług domenowych usługi active directory platformy Azure z zaawansowanymi opcjami konfiguracji

Usługi domenowe Usługi Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, uwierzytelnianie LDAP, Kerberos/NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Te usługi domeny są używane bez samodzielnego wdrażania, zarządzania i instalowania poprawek kontrolerów domeny. Usługi Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych i można użyć istniejących grup i kont użytkowników, aby zabezpieczyć dostęp do zasobów.

[Domenę zarządzaną][tutorial-create-instance] można utworzyć przy użyciu domyślnych opcji konfiguracji dla sieci i synchronizacji lub ręcznie zdefiniować te ustawienia. W tym samouczku pokazano, jak zdefiniować te zaawansowane opcje konfiguracji, aby utworzyć i skonfigurować wystąpienie usług Azure AD DS przy użyciu witryny Azure portal.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie ustawień DNS i sieci wirtualnej dla domeny zarządzanej
> * Tworzenie wystąpienia usługi Azure AD DS
> * Dodawanie użytkowników administracyjnych do zarządzania domeną
> * Włączanie synchronizacji skrótów haseł

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Aby włączyć usługi Azure AD DS, potrzebujesz uprawnień *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usług Azure AD DS, potrzebujesz uprawnień *współautora* w ramach subskrypcji platformy Azure.

Chociaż nie jest to wymagane dla usług Azure AD DS, zaleca się [skonfigurowanie samoobsługowego resetowania haseł (SSPR)][configure-sspr] dla dzierżawy usługi Azure AD. Użytkownicy mogą zmienić swoje hasło bez sspru, ale SSPR pomaga, jeśli zapomną hasła i trzeba je zresetować.

> [!IMPORTANT]
> Po utworzeniu domeny zarządzanej usług Azure AD DS nie można przenieść wystąpienia do innej grupy zasobów, sieci wirtualnej, subskrypcji itp. Podczas wdrażania wystąpienia usług Azure AD DS należy dopilnować, aby wybrać najbardziej odpowiednią subskrypcję, grupę zasobów, region i sieć wirtualną.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku należy utworzyć i skonfigurować wystąpienie usług Azure AD DS przy użyciu witryny Azure portal. Aby rozpocząć, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Tworzenie wystąpienia i konfigurowanie podstawowych ustawień

Aby uruchomić **kreatora Włącz usługi domenowe usługi AD Azure,** wykonaj następujące kroki:

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. Wprowadź *usługi domenowe* na pasku wyszukiwania, a następnie wybierz pozycję *Usługi domenowe usługi Azure AD* z sugestii wyszukiwania.
1. Na stronie Usługi domenowe usługi azure AD wybierz pozycję **Utwórz**. Zostanie uruchomiony Kreator włącz usługi **domenowe usługi azure** ad.
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć domenę zarządzana.
1. Wybierz **grupę Zasobów,** do której powinna należeć domena zarządzana. Wybierz pozycję **Utwórz nową** lub wybierz istniejącą grupę zasobów.

Podczas tworzenia wystąpienia usług Azure AD DS należy określić nazwę DNS. Po wybraniu tej nazwy DNS należy wziąć pod uwagę pewne kwestie:

* **Wbudowana nazwa domeny:** Domyślnie używana jest wbudowana nazwa domeny katalogu (sufiks *.onmicrosoft.com).* Jeśli chcesz włączyć bezpieczny dostęp LDAP do domeny zarządzanej przez Internet, nie możesz utworzyć certyfikatu cyfrowego w celu zabezpieczenia połączenia z tą domeną domyślną. Firma Microsoft jest właścicielem domeny *onmicrosoft.com,* więc urząd certyfikacji (CA) nie wystawi certyfikatu.
* **Niestandardowe nazwy domen:** Najczęstszym podejściem jest określenie niestandardowej nazwy domeny, zazwyczaj tej, która jest już właścicielem i jest rutowalna. Korzystając z domeny niestandardowej z rutowalnymi, ruch może poprawnie przepływać zgodnie z potrzebami do obsługi aplikacji.
* **Sufiksy domen nienastępnych do routingu:** Ogólnie zaleca się unikanie sufiksu nazwy domeny nierutowalnej, takiego jak *contoso.local*. Sufiks *.local* nie jest rutowalny i może powodować problemy z rozpoznawaniem DNS.

> [!TIP]
> Jeśli utworzysz niestandardową nazwę domeny, zadbaj o istniejące przestrzenie nazw DNS. Zaleca się używanie nazwy domeny oddzielnie od istniejącego obszaru nazw dns platformy Azure lub lokalnego.
>
> Na przykład, jeśli masz istniejący odstęp nazw DNS *contoso.com,* utwórz domenę zarządzaną usługą Azure AD DS o niestandardowej nazwie domeny *aaddscontoso.com*. Jeśli musisz użyć bezpiecznego protokołu LDAP, musisz zarejestrować i posiadać tę niestandardową nazwę domeny, aby wygenerować wymagane certyfikaty.
>
> Może być konieczne utworzenie dodatkowych rekordów DNS dla innych usług w twoim środowisku lub warunkowych usług przesyłania dalej DNS między istniejącymi spaniami nazw DNS w twoim środowisku. Na przykład po uruchomieniu serwera sieci Web, który obsługuje witrynę przy użyciu głównej nazwy DNS, mogą istnieć konflikty nazw, które wymagają dodatkowych wpisów DNS.
>
> W tych samouczkach i artykułach instruktażowych domena niestandardowa *aaddscontoso.com* jest używana jako krótki przykład. We wszystkich poleceniach określ własną nazwę domeny.

Obowiązują również następujące ograniczenia nazw DNS:

* **Ograniczenia prefiksu domeny:** Nie można utworzyć domeny zarządzanej z prefiksem dłuższym niż 15 znaków. Prefiks określonej nazwy domeny (na przykład *aaddscontoso* w *aaddscontoso.com* nazwa domeny) musi zawierać 15 lub mniej znaków.
* **Konflikty nazw sieciowych:** Nazwa domeny DNS domeny zarządzanej nie powinna już istnieć w sieci wirtualnej. W szczególności sprawdź następujące scenariusze, które mogłyby prowadzić do konfliktu nazw:
    * Jeśli masz już domenę usługi Active Directory o tej samej nazwie domeny DNS w sieci wirtualnej platformy Azure.
    * Jeśli sieć wirtualna, w której planujesz włączyć domenę zarządzaną, ma połączenie sieci VPN z siecią lokalną. W tym scenariuszu upewnij się, że nie masz domeny o tej samej nazwie domeny DNS w sieci lokalnej.
    * Jeśli masz istniejącą usługę w chmurze platformy Azure o tej nazwie w sieci wirtualnej platformy Azure.

Wypełnij pola w oknie *Podstawy witryny* Azure Portal, aby utworzyć wystąpienie usług Azure AD DS:

1. Wprowadź **nazwę domeny DNS** dla domeny zarządzanej, biorąc pod uwagę poprzednie punkty.
1. Wybierz **lokalizację** platformy Azure, w której ma zostać utworzona domena zarządzana. Jeśli wybierzesz region, który obsługuje strefy dostępności, zasoby usługi Azure AD DS są dystrybuowane między strefami w celu uzyskania dodatkowej redundancji.

    Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach.

    Nie ma nic do skonfigurowania dla usługi Azure AD DS, które mają być dystrybuowane w różnych strefach. Platforma Azure automatycznie obsługuje dystrybucji strefy zasobów. Aby uzyskać więcej informacji i wyświetlić dostępność regionu, zobacz [Co to są strefy dostępności na platformie Azure?][availability-zones]

1. Jednostka **SKU** określa wydajność, częstotliwość tworzenia kopii zapasowych i maksymalną liczbę relacji zaufania lasu, które można utworzyć. Jednostkę SKU można zmienić po utworzeniu domeny zarządzanej, jeśli twoja firma wymaga lub zmieni wymagania. Aby uzyskać więcej informacji, zobacz [pojęcia jednostek SKU usługi Azure AD DS][concepts-sku].

    W tym samouczku wybierz *standardową* jednostkę SKU.
1. *Las* jest konstrukcją logiczną używaną przez Usługi domenowe Active Directory do grupowania jednej lub kilku domen. Domyślnie domena zarządzana usługą Azure AD DS jest tworzona jako las *użytkownika.* Ten typ lasu synchronizuje wszystkie obiekty z usługi Azure AD, w tym wszystkie konta użytkowników utworzone w lokalnym środowisku usług AD DS. Las *zasobów* synchronizuje tylko użytkowników i grupy utworzone bezpośrednio w usłudze Azure AD. Lasy zasobów są obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat lasów *zasobów,* w tym dlaczego można ich używać i jak tworzyć relacje zaufania lasów za pomocą lokalnych domen usług AD DS, zobacz [omówienie lasów zasobów usługi Azure AD DS][resource-forests].

    W tym samouczku wybierz pozycję Utwórz las *użytkownika.*

    ![Konfigurowanie podstawowych ustawień wystąpienia usług domenowych usługi Azure AD](./media/tutorial-create-instance-advanced/basics-window.png)

1. Aby ręcznie skonfigurować dodatkowe opcje, wybierz pozycję **Dalej - Sieć**. W przeciwnym razie wybierz **pozycję Przejrzyj + utwórz,** aby zaakceptować domyślne opcje konfiguracji, a następnie przejdź do sekcji [Wdrażanie domeny zarządzanej](#deploy-the-managed-domain). Po wybraniu tej opcji tworzenia skonfigurowano następujące wartości domyślne:

    * Tworzy sieć wirtualną o nazwie *aadds-vnet,* która używa zakresu adresów IP *10.0.1.0/24*.
    * Tworzy podsieć o nazwie *aadds-podsieci* przy użyciu zakresu adresów IP *10.0.1.0/24*.
    * Synchronizuje *wszystkich* użytkowników z usługi Azure AD do domeny zarządzanej usług Azure AD DS.

## <a name="create-and-configure-the-virtual-network"></a>Tworzenie i konfigurowanie sieci wirtualnej

Aby zapewnić łączność, potrzebna jest sieć wirtualna platformy Azure i dedykowana podsieć. Usługi Azure AD DS jest włączona w tej podsieci sieci wirtualnej. W tym samouczku utworzysz sieć wirtualną, ale zamiast tego można wybrać użycie istniejącej sieci wirtualnej. W obu podejściach należy utworzyć dedykowaną podsieć do użytku przez usługi Azure AD DS.

Niektóre zagadnienia dotyczące tej dedykowanej podsieci sieci wirtualnej obejmują następujące obszary:

* Podsieć musi mieć co najmniej 3-5 dostępnych adresów IP w zakresie adresów do obsługi zasobów usług Azure AD DS.
* Nie wybieraj *podsieci bramy* do wdrażania usług Azure AD DS. Wdrażanie usług Azure AD DS w podsieci *bramy* nie jest obsługiwane.
* Nie wdrażaj żadnych innych maszyn wirtualnych w podsieci. Aplikacje i maszyny wirtualne często używają sieciowych grup zabezpieczeń do zabezpieczania łączności. Uruchomienie tych obciążeń w oddzielnej podsieci umożliwia stosowanie tych sieciowych grup zabezpieczeń bez zakłócania łączności z domeną zarządzaną.
* Nie można przenieść domeny zarządzanej do innej sieci wirtualnej po włączeniu usług Azure AD DS.

Aby uzyskać więcej informacji na temat planowania i konfigurowania sieci wirtualnej, zobacz [zagadnienia dotyczące sieci usług domenowych Active Directory platformy Azure][network-considerations].

Wypełnij pola w oknie *Sieć* w następujący sposób:

1. Z **Network** menu rozwijanego wybierz sieć wirtualną, w, w niej można wdrożyć usługi Azure AD DS, lub wybierz pozycję **Utwórz nowy**.
    1. Jeśli zdecydujesz się utworzyć sieć wirtualną, wprowadź nazwę sieci wirtualnej, takiej jak *myVnet,* a następnie podaj zakres adresów, taki jak *10.0.1.0/24*.
    1. Utwórz dedykowaną podsieć o wyraźnej nazwie, na przykład *DomainServices*. Podaj zakres adresów, na przykład *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Upewnij się, że wybierz zakres adresów, który mieści się w zakresie prywatnych adresów IP. Zakresy adresów IP, których nie jesteś właścicielem, które znajdują się w przestrzeni adresów publicznych, powodują błędy w usługach Azure AD DS.

1. Wybierz podsieć sieci wirtualnej, na przykład *DomainServices*.
1. Gdy będzie gotowy, wybierz pozycję **Dalej - Administracja**.

## <a name="configure-an-administrative-group"></a>Konfigurowanie grupy administracyjnej

Do zarządzania domeną usług Azure AD DS używana jest specjalna grupa administracyjna o nazwie *Administratorzy kontrolera domeny kontrolera domeny usługi AAD.* Członkowie tej grupy otrzymują uprawnienia administracyjne do maszyn wirtualnych, które są przyłączone do domeny zarządzanej. Na maszynach wirtualnych przyłączonych do domeny ta grupa jest dodawana do grupy administratorów lokalnych. Członkowie tej grupy mogą również używać pulpitu zdalnego do łączenia się zdalnie z maszynami wirtualnymi przyłączanym do domeny.

Nie masz uprawnień *Administratora domeny* ani *Administratora przedsiębiorstwa* w domenie zarządzanej przy użyciu usług Azure AD DS. Te uprawnienia są zarezerwowane przez usługę i nie są udostępniane użytkownikom w dzierżawie. Zamiast tego grupa *Administratorzy kontrolera domeny usługi AAD* umożliwia wykonywanie niektórych uprzywilejowanych operacji. Operacje te obejmują przynależność do grupy administracyjnej na maszynach wirtualnych przyłączonych do domeny i konfigurowanie zasad grupy.

Kreator automatycznie tworzy grupę *Administratorzy kontrolera domeny usługi AAD* w katalogu usługi Azure AD. Jeśli masz istniejącą grupę o tej nazwie w katalogu usługi Azure AD, kreator wybiera tę grupę. Opcjonalnie można dodać dodatkowych użytkowników do tej grupy *Administratorzy kontrolera domeny usługi AAD* podczas procesu wdrażania. Te kroki można wykonać później.

1. Aby dodać dodatkowych użytkowników do tej grupy *Administratorzy kontrolera domeny usługi AAD,* wybierz pozycję **Zarządzaj członkostwem w grupie**.

    ![Konfigurowanie członkostwa w grupach administratorzy kontrolera domeny usługi AAD](./media/tutorial-create-instance-advanced/admin-group.png)

1. Wybierz przycisk **Dodaj członków,** a następnie wyszukaj i wybierz użytkowników z katalogu usługi Azure AD. Na przykład wyszukaj własne konto i dodaj je do grupy *Administratorzy kontrolera domeny usługi AAD.*
1. W razie potrzeby zmień lub dodaj dodatkowych adresatów powiadomień, gdy w domenie zarządzanej usług Azure AD DS znajdują się alerty wymagające uwagi.
1. Gdy będzie gotowy, wybierz pozycję **Dalej - Synchronizacja**.

## <a name="configure-synchronization"></a>Konfigurowanie synchronizacji

Usługi Azure AD DS umożliwia synchronizację *wszystkich* użytkowników i grup dostępnych w usłudze Azure AD lub synchronizacji o *określonym zakresie* tylko określonych grup. Jeśli zdecydujesz się zsynchronizować *wszystkich* użytkowników i grupy, nie można później wybrać tylko wykonać synchronizację o określonym zakresie. Aby uzyskać więcej informacji na temat synchronizacji o określonym zakresie, zobacz [Synchronizacja zakresu usług domenowych usługi Azure AD][scoped-sync].

1. W tym samouczku wybierz opcję synchronizacji **wszystkich** użytkowników i grup. Ten wybór synchronizacji jest opcją domyślną.

    ![Wykonywanie pełnej synchronizacji użytkowników i grup z usługi Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Wybierz pozycję **Przegląd + utwórz**.

## <a name="deploy-the-managed-domain"></a>Wdrażanie domeny zarządzanej

Na stronie **Podsumowanie** kreatora przejrzyj ustawienia konfiguracji domeny zarządzanej. Możesz wrócić do dowolnego kroku kreatora, aby wprowadzić zmiany. Aby ponownie wdrożyć domenę zarządzaną usługą Azure AD DS do innej dzierżawy usługi Azure AD w spójny sposób przy użyciu tych opcji konfiguracji, można również **pobrać szablon do automatyzacji.**

1. Aby utworzyć domenę zarządzana, wybierz pozycję **Utwórz**. Zostanie wyświetlona uwaga, że po utworzeniu usługi Azure AD DS nie można zmienić niektórych opcji konfiguracji, takich jak nazwa DNS lub sieć wirtualna. Aby kontynuować, wybierz **przycisk OK**.
1. Proces inicjowania obsługi administracyjnej domeny zarządzanej może potrwać do godziny. Powiadomienie jest wyświetlane w portalu, który pokazuje postęp wdrożenia usług Azure AD DS. Wybierz powiadomienie, aby wyświetlić szczegółowy postęp wdrożenia.

    ![Powiadomienie w witrynie Azure portal o trakcie wdrażania](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Wybierz grupę zasobów, taką jak *myResourceGroup,* a następnie wybierz wystąpienie usług Azure AD DS z listy zasobów platformy Azure, takich jak *aaddscontoso.com*. Karta **Przegląd** pokazuje, że domena zarządzana jest obecnie *wdrażana*. Nie można skonfigurować domeny zarządzanej, dopóki nie zostanie w pełni aprowizowana.

    ![Stan usług domenowych podczas obsługi administracyjnej](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Gdy domena zarządzana jest w pełni aprowizowana, na karcie **Przegląd** jest wyświetlany stan domeny jako *Uruchomiony*.

    ![Stan usług domenowych po pomyślnym zainicjowaniu obsługi administracyjnej](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

Domena zarządzana jest skojarzona z dzierżawą usługi Azure AD. Podczas procesu inicjowania obsługi administracyjnej usługa Azure AD DS tworzy dwie aplikacje enterprise o nazwie *Domain Controller Services* i *AzureActiveDirectoryDomainControllerServices* w dzierżawie usługi Azure AD. Te aplikacje dla przedsiębiorstw są potrzebne do obsługi domeny zarządzanej. Nie usuwaj tych aplikacji.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure

Po pomyślnym wdrożeniu usługi Azure AD DS skonfiguruj sieć wirtualną, aby umożliwić innym połączonym maszynom wirtualnym i aplikacjom korzystanie z domeny zarządzanej. Aby zapewnić tę łączność, zaktualizuj ustawienia serwera DNS sieci wirtualnej, aby wskazywały dwa adresy IP, na których wdrożono usługi Azure AD DS.

1. Na karcie **Przegląd** domeny zarządzanej przedstawiono kilka **wymaganych kroków konfiguracji**. Pierwszym krokiem konfiguracji jest zaktualizowanie ustawień serwera DNS dla sieci wirtualnej. Po prawidłowym skonfigurowaniu ustawień DNS ten krok nie jest już wyświetlany.

    Wymienione adresy są kontrolerami domeny do użycia w sieci wirtualnej. W tym przykładzie te adresy to *10.1.0.4* i *10.1.0.5*. Te adresy IP można później znaleźć na karcie **Właściwości.**

    ![Konfigurowanie ustawień DNS sieci wirtualnej przy pomocy adresów IP usług domenowych usługi Azure AD](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Aby zaktualizować ustawienia serwera DNS dla sieci wirtualnej, wybierz przycisk **Konfiguruj.** Ustawienia DNS są automatycznie konfigurowane dla sieci wirtualnej.

> [!TIP]
> Jeśli w poprzednich krokach wybrano istniejącą sieć wirtualną, wszystkie maszyny wirtualne podłączone do sieci otrzymają nowe ustawienia DNS dopiero po ponownym uruchomieniu. Maszyny wirtualne można ponownie uruchomić przy użyciu witryny Azure portal, usługi Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Włączanie kont użytkowników usług Ad DS

Aby uwierzytelnić użytkowników w domenie zarządzanej, usługi Azure AD DS potrzebuje skrótów haseł w formacie odpowiednim dla programu NT LAN Manager (NTLM) i uwierzytelniania Kerberos. Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie włączysz usług Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD również nie przechowuje żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie wygenerować tych skrótów haseł NTLM lub Kerberos na podstawie istniejących poświadczeń użytkowników.

> [!NOTE]
> Po odpowiedniej konfiguracji skróty haseł, które można zastosować, są przechowywane w domenie zarządzanej usług Azure AD DS. Jeśli usuniesz domenę zarządzaną usługą Azure AD DS, wszystkie skróty haseł przechowywane w tym momencie są również usuwane. Zsynchronizowanych informacji o poświadczeniach w usłudze Azure AD nie można ponownie użyć, jeśli później utworzysz domenę zarządzaną usługą Azure AD DS — należy ponownie skonfigurować synchronizację skrótów haseł, aby ponownie przechowywać skróty haseł. Wcześniej przyłączone do domeny maszyny wirtualne lub użytkownicy nie będą mogli natychmiast uwierzytelniać — usługa Azure AD musi generować i przechowywać skróty haseł w nowej domenie zarządzanej usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [Proces synchronizacji skrótów haseł dla usług Azure AD DS i usługi Azure AD Connect][password-hash-sync-process].

Kroki generowania i przechowywania tych skrótów haseł są różne dla kont użytkowników tylko w chmurze utworzonych w usłudze Azure AD w porównaniu z kontami użytkowników, które są synchronizowane z katalogu lokalnego przy użyciu usługi Azure AD Connect. Konto użytkownika tylko w chmurze to konto, które zostało utworzone w katalogu usługi Azure AD przy użyciu witryny Azure Portal lub poleceń cmdlet programu Azure AD PowerShell. Te konta użytkowników nie są synchronizowane z katalogu lokalnego. W tym samouczku pracujmy z podstawowym kontem użytkownika tylko w chmurze. Aby uzyskać więcej informacji na temat dodatkowych kroków wymaganych do korzystania z usługi Azure AD Connect, zobacz [Synchronizowanie skrótów haseł dla kont użytkowników synchronizowanych z lokalnej usługi AD z domeną zarządzaną.][on-prem-sync]

> [!TIP]
> Jeśli dzierżawa usługi Azure AD ma kombinację użytkowników i użytkowników tylko w chmurze z lokalnej usługi AD, należy wykonać oba zestawy kroków.

W przypadku kont użytkowników tylko w chmurze użytkownicy muszą zmienić swoje hasła, zanim będą mogli korzystać z usług Azure AD DS. Ten proces zmiany hasła powoduje, że skróty haseł dla uwierzytelniania Kerberos i NTLM mają być generowane i przechowywane w usłudze Azure AD. Konto nie jest synchronizowane z usługi Azure AD do usług Azure AD DS, dopóki hasło nie zostanie zmienione. Wygasają hasła dla wszystkich użytkowników chmury w dzierżawie, którzy muszą używać usług Azure AD DS, co wymusza zmianę hasła przy następnym logowaniu, lub poinstruować użytkowników chmury, aby ręcznie zmienili swoje hasła. W tym samouczku zmieńmy ręcznie hasło użytkownika.

Aby użytkownik mógł zresetować swoje hasło, dzierżawa usługi Azure AD musi być [skonfigurowana do samoobsługowego resetowania hasła.][configure-sspr]

Aby zmienić hasło dla użytkownika tylko w chmurze, użytkownik musi wykonać następujące kroki:

1. Przejdź do strony Panelu dostępu [https://myapps.microsoft.com](https://myapps.microsoft.com)usługi Azure AD pod adresem .
1. W prawym górnym rogu wybierz swoje imię i nazwisko, a następnie wybierz **pozycję Profil** z menu rozwijanego.

    ![Wybieranie profilu](./media/tutorial-create-instance-advanced/select-profile.png)

1. Na stronie **Profil** wybierz pozycję **Zmień hasło**.
1. Na stronie **Zmienianie hasła** wprowadź istniejące (stare) hasło, a następnie wprowadź i potwierdź nowe hasło.
1. Wybierz pozycję **Prześlij**.

Minęło kilka minut po zmianie hasła, aby nowe hasło było użyteczne w usługach Azure AD DS i pomyślne logowanie się do komputerów przyłączonych do domeny zarządzanej.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie ustawień DNS i sieci wirtualnej dla domeny zarządzanej
> * Tworzenie wystąpienia usługi Azure AD DS
> * Dodawanie użytkowników administracyjnych do zarządzania domeną
> * Włączanie kont użytkowników usług Ad DS i generowanie skrótów haseł

Aby wyświetlić tę domenę zarządzaną w akcji, utwórz i dołącz maszynę wirtualną do domeny.

> [!div class="nextstepaction"]
> [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
