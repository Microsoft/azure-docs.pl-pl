---
title: Samouczek — tworzenie wystąpienia usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować wystąpienie usług domenowych usługi active directory platformy Azure przy użyciu witryny Azure portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 7652bacdebec19f8a5d55874cfb903e8748cef4d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639719"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Samouczek: Tworzenie i konfigurowanie wystąpienia usług domenowych active directory platformy Azure

Usługi domenowe Usługi Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, uwierzytelnianie LDAP, Kerberos/NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Te usługi domeny są używane bez samodzielnego wdrażania, zarządzania i instalowania poprawek kontrolerów domeny. Usługi Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych i można użyć istniejących grup i kont użytkowników, aby zabezpieczyć dostęp do zasobów.

Domenę zarządzaną można utworzyć przy użyciu domyślnych opcji konfiguracji sieci i synchronizacji lub [ręcznie zdefiniować te ustawienia][tutorial-create-instance-advanced]. W tym samouczku pokazano, jak używać opcji domyślnych do tworzenia i konfigurowania wystąpienia usług Azure AD DS przy użyciu witryny Azure Portal.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Opis wymagań dotyczących systemu DNS dla domeny zarządzanej
> * Tworzenie wystąpienia usługi Azure AD DS
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

## <a name="create-an-instance"></a>Tworzenie wystąpienia

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

    ![Konfigurowanie podstawowych ustawień wystąpienia usług domenowych usługi Azure AD](./media/tutorial-create-instance/basics-window.png)

Aby szybko utworzyć domenę zarządzaną usługą Azure AD DS, można wybrać **opcję Przejrzyj + utwórz,** aby zaakceptować dodatkowe domyślne opcje konfiguracji. Po wybraniu tej opcji tworzenia skonfigurowano następujące wartości domyślne:

* Tworzy sieć wirtualną o nazwie *aadds-vnet,* która używa zakresu adresów IP *10.0.2.0/24*.
* Tworzy podsieć o nazwie *aadds-podsieci* przy użyciu zakresu adresów IP *10.0.2.0/24*.
* Synchronizuje *wszystkich* użytkowników z usługi Azure AD do domeny zarządzanej usług Azure AD DS.

Wybierz **opcję Przejrzyj + utwórz,** aby zaakceptować te domyślne opcje konfiguracji.

## <a name="deploy-the-managed-domain"></a>Wdrażanie domeny zarządzanej

Na stronie **Podsumowanie** kreatora przejrzyj ustawienia konfiguracji domeny zarządzanej. Możesz wrócić do dowolnego kroku kreatora, aby wprowadzić zmiany. Aby ponownie wdrożyć domenę zarządzaną usługą Azure AD DS do innej dzierżawy usługi Azure AD w spójny sposób przy użyciu tych opcji konfiguracji, można również **pobrać szablon do automatyzacji.**

1. Aby utworzyć domenę zarządzana, wybierz pozycję **Utwórz**. Zostanie wyświetlona uwaga, że po utworzeniu zarządzanego przez usługę Azure AD DS nie można zmienić niektórych opcji konfiguracji, takich jak nazwa DNS lub sieć wirtualna. Aby kontynuować, wybierz **przycisk OK**.
1. Proces inicjowania obsługi administracyjnej domeny zarządzanej może potrwać do godziny. Powiadomienie jest wyświetlane w portalu, który pokazuje postęp wdrożenia usług Azure AD DS. Wybierz powiadomienie, aby wyświetlić szczegółowy postęp wdrożenia.

    ![Powiadomienie w witrynie Azure portal o trakcie wdrażania](./media/tutorial-create-instance/deployment-in-progress.png)

1. Strona zostanie załadowana z aktualizacjami procesu wdrażania, w tym tworzenie nowych zasobów w katalogu.
1. Wybierz grupę zasobów, taką jak *myResourceGroup,* a następnie wybierz wystąpienie usług Azure AD DS z listy zasobów platformy Azure, takich jak *aaddscontoso.com*. Karta **Przegląd** pokazuje, że domena zarządzana jest obecnie *wdrażana*. Nie można skonfigurować domeny zarządzanej, dopóki nie zostanie w pełni aprowizowana.

    ![Stan usług domenowych podczas obsługi administracyjnej](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Gdy domena zarządzana jest w pełni aprowizowana, na karcie **Przegląd** jest wyświetlany stan domeny jako *Uruchomiony*.

    ![Stan usług domenowych po pomyślnym zainicjowaniu obsługi administracyjnej](./media/tutorial-create-instance/successfully-provisioned.png)

Domena zarządzana jest skojarzona z dzierżawą usługi Azure AD. Podczas procesu inicjowania obsługi administracyjnej usługa Azure AD DS tworzy dwie aplikacje enterprise o nazwie *Domain Controller Services* i *AzureActiveDirectoryDomainControllerServices* w dzierżawie usługi Azure AD. Te aplikacje dla przedsiębiorstw są potrzebne do obsługi domeny zarządzanej. Nie usuwaj tych aplikacji.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure

Po pomyślnym wdrożeniu usługi Azure AD DS skonfiguruj sieć wirtualną, aby umożliwić innym połączonym maszynom wirtualnym i aplikacjom korzystanie z domeny zarządzanej. Aby zapewnić tę łączność, zaktualizuj ustawienia serwera DNS sieci wirtualnej, aby wskazywały dwa adresy IP, na których wdrożono usługi Azure AD DS.

1. Na karcie **Przegląd** domeny zarządzanej przedstawiono kilka **wymaganych kroków konfiguracji**. Pierwszym krokiem konfiguracji jest zaktualizowanie ustawień serwera DNS dla sieci wirtualnej. Po prawidłowym skonfigurowaniu ustawień DNS ten krok nie jest już wyświetlany.

    Wymienione adresy są kontrolerami domeny do użycia w sieci wirtualnej. W tym przykładzie adresy te są *10.0.2.4* i *10.0.2.5*. Te adresy IP można później znaleźć na karcie **Właściwości.**

    ![Konfigurowanie ustawień DNS sieci wirtualnej przy pomocy adresów IP usług domenowych usługi Azure AD](./media/tutorial-create-instance/configure-dns.png)

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

    ![Wybieranie profilu](./media/tutorial-create-instance/select-profile.png)

1. Na stronie **Profil** wybierz pozycję **Zmień hasło**.
1. Na stronie **Zmienianie hasła** wprowadź istniejące (stare) hasło, a następnie wprowadź i potwierdź nowe hasło.
1. Wybierz pozycję **Prześlij**.

Minęło kilka minut po zmianie hasła, aby nowe hasło było użyteczne w usługach Azure AD DS i pomyślne logowanie się do komputerów przyłączonych do domeny zarządzanej.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Opis wymagań dotyczących systemu DNS dla domeny zarządzanej
> * Tworzenie wystąpienia usługi Azure AD DS
> * Dodawanie użytkowników administracyjnych do zarządzania domeną
> * Włączanie kont użytkowników usług Ad DS i generowanie skrótów haseł

Przed dołączeniem do maszyn wirtualnych w domenie i wdrożeniem aplikacji korzystających z domeny zarządzanej usług Azure AD DS należy skonfigurować sieć wirtualną platformy Azure pod kątem obciążeń aplikacji.

> [!div class="nextstepaction"]
> [Konfigurowanie sieci wirtualnej platformy Azure dla obciążeń aplikacji w celu używania domeny zarządzanej](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
