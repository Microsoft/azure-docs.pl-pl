---
title: Izolacja w chmurze publicznej platformy Azure | Microsoft Docs
description: Dowiedz się, jak platforma Azure zapewnia izolację zarówno złośliwych, jak i niezłośliwych użytkowników, a także oferuje różne opcje izolacji dla architektów.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: 42582c9474647c4c203bd0cafae0be664398ba41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533907"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolacja w chmurze publicznej platformy Azure

System Azure umożliwia uruchamianie aplikacji i maszyn wirtualnych w ramach udostępnionej infrastruktury fizycznej. Jedną z ekonomicznych motywacji do uruchamiania aplikacji w środowisku chmury jest możliwość dystrybucji kosztów zasobów udostępnionych między wieloma klientami. Ta metoda korzystania z wielu dzierżawców zwiększa wydajność dzięki możliwości multipleksowania zasobów między różnymi klientami przy niskich kosztach. Niestety, wprowadza również ryzyko związane z udostępnianiem serwerów fizycznych i innych zasobów infrastruktury w celu uruchamiania poufnych aplikacji i maszyn wirtualnych, które mogą należeć do dowolnego lub potencjalnie złośliwego użytkownika.

W tym artykule opisano, jak platforma Azure zapewnia izolację zarówno złośliwych, jak i niezłośliwych użytkowników, oraz służy jako przewodnik tworzenia rozwiązań w chmurze, oferując różne opcje izolacji dla architektów.

## <a name="tenant-level-isolation"></a>Izolacja poziomu dzierżawy

Jedną z głównych korzyści związanych z chmurą obliczeniową jest koncepcja współdzielonej wspólnej infrastruktury na wielu klientach jednocześnie, co prowadzi do oszczędności skali. Koncepcja ta jest nazywana wielodostępem. Firma Microsoft działa w sposób ciągły, aby zapewnić, że architektura wielodostępności platformy Microsoft Cloud Azure obsługuje standardy zabezpieczeń, poufności, prywatności, integralności i dostępności.

W przypadku miejsca pracy w chmurze dzierżawę można zdefiniować jako klienta lub organizację, do której należy określone wystąpienie danej usługi w chmurze. Korzystając z platformy tożsamości zapewnianej przez Microsoft Azure, dzierżawa jest po prostu dedykowanym wystąpieniem Azure Active Directory (Azure AD), które organizacja otrzymuje i jest właścicielem po zarejestrowaniu się w usłudze w chmurze firmy Microsoft.

Każdy katalog usługi Azure AD jest odrębny i oddzielony od innych katalogów usługi Azure AD. Podobnie jak budynek biurowy, który jest zabezpieczonym zasobem przeznaczonym tylko dla Twojej organizacji, katalog Azure AD został zaprojektowany jako zabezpieczony zasób do użytku tylko Twojej organizacji. Architektura usługi Azure AD chroni dane klientów i informacje o tożsamości przez zmieszaniem. Oznacza to, że użytkownicy i administratorzy jednego katalogu usługi Azure AD nie mogą przypadkowo ani złośliwie uzyskać dostępu do danych w innym katalogu.

### <a name="azure-tenancy"></a>Dzierżawa platformy Azure

Dzierżawa platformy Azure (subskrypcja platformy Azure) odnosi się do relacji "klient/rozliczenia" i unikatowej [dzierżawy](../../active-directory/develop/quickstart-create-new-tenant.md) w [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Izolacja poziomu dzierżawy w Microsoft Azure jest realizowana przy użyciu Azure Active Directory i [formantów opartych na rolach](../../role-based-access-control/overview.md) . Każda subskrypcja platformy Azure jest skojarzona z jednym katalogiem Azure Active Directory (AD).

Użytkownicy, grupy i aplikacje z tego katalogu mogą zarządzać zasobami w ramach subskrypcji platformy Azure. Te prawa dostępu można przypisywać przy użyciu Azure Portal, narzędzi wiersza polecenia platformy Azure i interfejsów API zarządzania platformy Azure. Dzierżawa usługi Azure AD jest logicznie izolowana przy użyciu granic zabezpieczeń, dzięki czemu klient nie może uzyskać dostępu do ani naruszać współdzierżawców, złośliwie lub przypadkowo. Usługa Azure AD działa na serwerach "bez systemu operacyjnego" izolowanych w podzielonym segmencie sieci, w których filtrowanie pakietów na poziomie hosta i Zapora systemu Windows blokują niechciane połączenia i ruch.

- Dostęp do danych w usłudze Azure AD wymaga uwierzytelnienia użytkownika za pośrednictwem usługi tokenu zabezpieczającego (STS). Informacje o istnieniu, stanie włączonym i roli użytkownika są używane przez system autoryzacji w celu ustalenia, czy żądany dostęp do dzierżawy docelowej jest autoryzowany dla tego użytkownika w tej sesji.

![Dzierżawa platformy Azure](./media/isolation-choices/azure-isolation-fig1.png)

- Dzierżawy są kontenerami dyskretnymi i nie ma żadnych relacji między nimi.

- Brak dostępu między dzierżawcami, chyba że administrator dzierżawy przyznał go za pośrednictwem kont użytkowników Federacji lub inicjowania obsługi administracyjnej z innych dzierżawców.

- Dostęp fizyczny do serwerów wchodzących w skład usługi Azure AD i bezpośredni dostęp do systemów zaplecza usługi Azure AD jest ograniczony.

- Użytkownicy usługi Azure AD nie mają dostępu do fizycznych zasobów ani lokalizacji, dlatego nie jest możliwe, że nie mogą oni obejść testów logicznych zasad RBAC określonych poniżej.

W przypadku potrzeb diagnostycznych i konserwacyjnych wymagany jest model operacyjny, który korzysta z systemu podniesienia uprawnień just-in-Time. Azure AD Privileged Identity Management (PIM) wprowadza koncepcję uprawnionych administratorów. [uprawnieni Administratorzy](../../active-directory/privileged-identity-management/pim-configure.md) powinni być użytkownikami, którzy potrzebują uprzywilejowanego dostępu, a następnie, ale nie codziennie. Rola pozostaje nieaktywna, a gdy użytkownik potrzebuje dostępu, przechodzi proces aktywacji i staje się aktywnym administratorem na określony z góry czas.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory hostuje każdą dzierżawę w swoim własnym chronionym kontenerze, przy użyciu zasad i uprawnień do i w kontenerze, który jest własnością i jest zarządzany przez dzierżawcę.

Koncepcje kontenerów dzierżawców są głęboko przegrane w usłudze katalogowej na wszystkich warstwach, od wszystkich portalów do magazynu trwałego.

Nawet wtedy, gdy metadane z wielu dzierżawców Azure Active Directory są przechowywane na tym samym dysku fizycznym, nie istnieje żadna relacja między kontenerami innymi niż zdefiniowane przez usługę katalogową, która z kolei jest podyktowana przez administratora dzierżawy.

### <a name="azure-role-based-access-control-azure-rbac"></a>Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia udostępnianie różnych składników dostępnych w ramach subskrypcji platformy Azure przez zapewnienie szczegółowych informacji o zarządzaniu dostępem na platformie Azure. Funkcja RBAC systemu Azure umożliwia rozdzielenie obowiązków w organizacji i udzielanie dostępu na podstawie tego, co użytkownicy potrzebują do wykonywania swoich zadań. Zamiast udzielać wszystkim nieograniczonych uprawnień w ramach subskrypcji lub zasobów platformy Azure, można zezwolić tylko na niektóre akcje.

Usługa Azure RBAC ma trzy podstawowe role, które mają zastosowanie do wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów, w tym prawa do delegowania dostępu innym osobom.

- **Współautor** może tworzyć wszystkie typy zasobów platformy Azure i zarządzać nimi, ale nie mogą przyznawać dostępu innym osobom.

- **Czytelnik** może wyświetlać istniejące zasoby platformy Azure.

![Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

Pozostałe role platformy Azure na platformie Azure umożliwiają zarządzanie określonymi zasobami platformy Azure. Na przykład rola współautora maszyny wirtualnej pozwala użytkownikom na tworzenie maszyn wirtualnych i zarządzanie nimi. Nie daje im dostępu do Virtual Network platformy Azure lub podsieci, z którą nawiąże połączenie maszyna wirtualna.

[Role wbudowane RBAC](../../role-based-access-control/built-in-roles.md) mają listę ról dostępnych na platformie Azure. Określa operacje i zakres, które każda wbudowana rola przyznaje użytkownikom. Jeśli chcesz zdefiniować własne role, aby jeszcze bardziej kontrolować, zobacz jak tworzyć [role niestandardowe na platformie Azure RBAC](../../role-based-access-control/custom-roles.md).

Niektóre inne możliwości Azure Active Directory obejmują:

- Usługa Azure AD umożliwia logowanie jednokrotne do aplikacji SaaS, niezależnie od lokalizacji hostowania. Niektóre aplikacje są sfederowane z usługą Azure AD, a inne korzystają z logowania jednokrotnego z użyciem hasła. Aplikacje federacyjne mogą również obsługiwać obsługę administracyjną użytkowników i przechowywanie [haseł](https://www.techopedia.com/definition/31415/password-vault).

- Dostęp do danych w usłudze [Azure Storage](https://azure.microsoft.com/services/storage/) jest kontrolowany za pośrednictwem uwierzytelniania. Każde konto magazynu ma klucz podstawowy ([klucz konta magazynu](../../storage/common/storage-create-storage-account.md)lub SAK) oraz pomocniczy klucz tajny (Sygnatura dostępu współdzielonego lub SAS).

- Usługa Azure AD zapewnia tożsamość jako usługę za pośrednictwem Federacji przy użyciu [Active Directory Federation Services](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md), synchronizacji i replikacji z katalogami lokalnymi.

- [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) to usługa wieloskładnikowego uwierzytelniania, która wymaga od użytkowników weryfikacji logowania przy użyciu aplikacji mobilnej, połączenia telefonicznego lub wiadomości SMS. Może być używany z usługą Azure AD, aby pomóc w zabezpieczeniu zasobów lokalnych za pomocą serwera usługi Azure Multi-Factor Authentication, a także z niestandardowymi aplikacjami i katalogami przy użyciu zestawu SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umożliwia dołączanie maszyn wirtualnych platformy Azure do domeny Active Directory bez wdrażania kontrolerów domeny. Możesz zalogować się na tych maszynach wirtualnych za pomocą poświadczeń firmowych Active Directory i zarządzać maszynami wirtualnymi przyłączonymi do domeny, używając zasady grupy, aby wymusić podstawy zabezpieczeń na wszystkich maszynach wirtualnych platformy Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) zapewnia usługi zarządzania tożsamościami o wysokiej dostępności dla aplikacji przeznaczonych dla klientów, które są skalowane do setek milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą zalogować się do wszystkich aplikacji za pośrednictwem dostosowywalnych środowisk przy użyciu istniejących kont społecznościowych lub tworząc poświadczenia.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolacja od administratorów firmy Microsoft & usuwania danych

Firma Microsoft podejmuje silne środki w celu ochrony danych przed niewłaściwym dostępem lub użyciem przez nieautoryzowanych osób. Te procesy operacyjne i kontrolki są obsługiwane przez [warunki usług online](https://aka.ms/Online-Services-Terms), które oferują zobowiązania umowne, które regulują dostęp do danych.

- Inżynierowie firmy Microsoft nie mają domyślnego dostępu do Twoich danych w chmurze. Zamiast tego uzyskuje dostęp, w obszarze nadzoru nad zarządzaniem, tylko w razie potrzeby. Ten dostęp jest starannie kontrolowany i rejestrowany i odwoływany, gdy nie jest już potrzebne.
- Firma Microsoft może wynajmu inne firmy do świadczenia ograniczonych usług w jej imieniu. Podwykonawcy mogą uzyskiwać dostęp do danych klienta tylko w celu świadczenia usług, dla których firma Microsoft zleca ich dostarczenie i nie wolno ich używać do innych celów. Ponadto są one umownie powiązane z zachowaniem poufności informacji naszych klientów.

Usługi biznesowe z przeprowadzonymi certyfikatami, takimi jak ISO/IEC 27001, są regularnie weryfikowane przez firmę Microsoft i akredytowanych przez nich firm, które przeprowadzają inspekcję przykładową w celu zaświadczania dostępu, tylko w przypadku uzasadnionych celów firmy. Zawsze możesz uzyskiwać dostęp do własnych danych klienta w dowolnym momencie i z dowolnego powodu.

W przypadku usunięcia jakichkolwiek danych Microsoft Azure usuwa dane, w tym wszystkie kopie w pamięci podręcznej lub kopii zapasowej. W przypadku usług należących do zakresu, to usunięcie zostanie przeprowadzone w ciągu 90 dni od zakończenia okresu przechowywania. (Usługi w zakresie są zdefiniowane w sekcji warunki przetwarzania danych w tematach dotyczących [usług online](https://aka.ms/Online-Services-Terms)).

Jeśli stacja dysków używana na potrzeby magazynu spada na awarię sprzętową, zostanie bezpiecznie [wymazana lub zniszczona](https://microsoft.com/trustcenter/privacy/you-own-your-data) , zanim firma Microsoft zwróci ją producentowi do zastąpienia lub naprawy. Dane na dysku są zastępowane, aby upewnić się, że dane nie mogą zostać odzyskane w żaden sposób.

## <a name="compute-isolation"></a>Izolacja obliczeniowa

Microsoft Azure oferuje różne usługi obliczeniowe oparte na chmurze, które obejmują szeroki wybór wystąpień obliczeniowych & usług, które mogą być automatycznie skalowane w górę i w dół w celu spełnienia wymagań aplikacji lub przedsiębiorstwa. Te wystąpienia obliczeniowe i usługa są izolowane na wielu poziomach w celu zabezpieczania danych bez ograniczania elastyczności konfiguracji wymaganej przez klientów.

### <a name="isolated-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych izolowanych

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedykowane hosty

Oprócz izolowanych hostów opisanych w poprzedniej sekcji platforma Azure oferuje również dedykowane hosty. Dedykowane hosty na platformie Azure to usługa udostępniająca serwery fizyczne, które mogą hostować co najmniej jedną maszynę wirtualną, które są przeznaczone dla jednej subskrypcji platformy Azure. Dedykowane hosty zapewniają izolację sprzętową na poziomie serwera fizycznego. Żadne inne maszyny wirtualne nie zostaną umieszczone na hostach. Dedykowane hosty są wdrażane w tych samych centrach danych i korzystają z tej samej sieci i podstawowej infrastruktury magazynu, co inne, nieizolowane hosty. Aby uzyskać więcej informacji, Zobacz szczegółowe omówienie [dedykowanych hostów platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Izolacja głównej systemu operacyjnego funkcji Hyper-V & między głównymi maszynami wirtualnymi & Gości

Platforma obliczeniowa platformy Azure jest oparta na wirtualizacji maszyn, co oznacza, że cały kod klienta jest wykonywany na maszynie wirtualnej funkcji Hyper-V. W każdym węźle platformy Azure (lub w sieci punkt końcowy) istnieje funkcja hypervisor, która jest uruchamiana bezpośrednio przez sprzęt i dzieli węzeł na zmienną liczbę Virtual Machines Gości (VM).

![Izolacja głównej systemu operacyjnego funkcji Hyper-V & między głównymi maszynami wirtualnymi & Gości](./media/isolation-choices/azure-isolation-fig4.jpg)

Każdy węzeł ma również jedną specjalną główną maszynę wirtualną, w której jest uruchomiony system operacyjny hosta. Granica krytyczna to izolacja głównej maszyny wirtualnej z maszyn wirtualnych gościa i maszyn wirtualnych gościa ze sobą, zarządzanych przez funkcję hypervisor i główny system operacyjny. Parowanie funkcji hypervisor/głównej systemu operacyjnego korzysta z firmy Microsoft przed bezpieczeństwem systemu operacyjnego i najnowszych informacji z funkcji Hyper-V firmy Microsoft w celu zapewnienia silnej izolacji maszyn wirtualnych gościa.

Platforma Azure korzysta ze środowiska zwirtualizowanego. Wystąpienia użytkownika działają jako autonomiczne maszyny wirtualne, które nie mają dostępu do fizycznego serwera hosta.

Funkcja hypervisor platformy Azure działa jak mikrojądra i przekazuje wszystkie żądania dostępu do sprzętu z maszyn wirtualnych gościa do hosta w celu przetworzenia przy użyciu interfejsu pamięci współdzielonej o nazwie VMBus. Uniemożliwia to użytkownikom uzyskiwanie dostępu do nieprzetworzonego odczytu/zapisu/wykonania w systemie i zmniejsza ryzyko związane z udostępnianiem zasobów systemowych.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Zaawansowany algorytm umieszczania maszyn wirtualnych & ochronę przed atakami z kanału bocznego

Każdy atak między MASZYNami wirtualnymi obejmuje dwie czynności: umieszczenie maszyny wirtualnej z kontrolą atakującej na tym samym hoście co jedna z maszyn wirtualnych poszkodowanych, a następnie naruszenie granicy izolacji w celu kradzieży poufnych informacji ofiary lub wpływu na jej wydajność w przypadku Greed lub Vandalism. Microsoft Azure zapewnia ochronę w obu krokach przy użyciu zaawansowanego algorytmu umieszczania maszyn wirtualnych i ochrony przed wszystkimi znanymi atakami kanału po stronie, w tym zakłóceniami maszyn wirtualnych sąsiadów.

### <a name="the-azure-fabric-controller"></a>Kontroler sieci szkieletowej platformy Azure

Kontroler sieci szkieletowej platformy Azure jest odpowiedzialny za przydzielanie zasobów infrastruktury do obciążeń dzierżawców i zarządza jednokierunkową komunikację z hosta do maszyn wirtualnych. Algorytm umieszczania maszyn wirtualnych kontrolera sieci szkieletowej platformy Azure jest wysoce zaawansowany i prawie nie można go przewidzieć jako poziomu hosta fizycznego.

![Kontroler sieci szkieletowej platformy Azure](./media/isolation-choices/azure-isolation-fig5.png)

Funkcja hypervisor platformy Azure wymusza separację pamięci i procesów między maszynami wirtualnymi, a w sposób bezpieczny kieruje ruch sieciowy do dzierżawców systemu operacyjnego gościa. Eliminuje to prawdopodobieństwo ataku z kanału po stronie na poziomie maszyny wirtualnej.

Na platformie Azure główna maszyna wirtualna jest specjalna: działa z zaostrzonym systemem operacyjnym o nazwie główny system operacyjny, który obsługuje agenta sieci szkieletowej (FA). FAs są używane w celu zarządzania agentami gościa (GA) w systemach operacyjnych gościa na maszynach wirtualnych klientów. FAs również zarządzanie węzłami magazynu.

Kolekcja funkcji hypervisor platformy Azure, głównego systemu operacyjnego/FA i maszyn wirtualnych/gazu klienta obejmuje węzeł obliczeniowy. FAs są zarządzane przez kontroler sieci szkieletowej (FC), który istnieje poza węzłami obliczeniowymi i magazynującymi (Klastry obliczeniowe i magazynowe są zarządzane przez osobną FCs). Jeśli klient zaktualizuje plik konfiguracji aplikacji podczas jego działania, FC komunikuje się z FA, a następnie kontaktuje się gazem, który powiadamia o zmianie konfiguracji. W przypadku awarii sprzętowej FC automatycznie odnajdzie dostępny sprzęt i ponownie uruchamia maszynę wirtualną.

![Kontroler sieci szkieletowej platformy Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

Komunikacja między kontrolerem sieci szkieletowej a agentem jest jednokierunkowe. Agent implementuje usługę chronioną za pośrednictwem protokołu SSL, która odpowiada tylko na żądania z kontrolera. Nie można zainicjować połączeń z kontrolerem lub innymi węzłami wewnętrznymi z uprawnieniami. FC traktuje wszystkie odpowiedzi tak, jakby były niezaufane.

![Kontroler sieci szkieletowej](./media/isolation-choices/azure-isolation-fig7.png)

Izolacja rozciąga się od głównej maszyny wirtualnej z maszyn wirtualnych gościa i od siebie maszyn wirtualnych gościa. Węzły obliczeniowe są również izolowane od węzłów magazynu w celu zwiększenia ochrony.

Funkcja hypervisor i system operacyjny hosta zapewniają filtry pakietów sieciowych w celu zagwarantowania, że niezaufane maszyny wirtualne nie mogą generować sfałszowanego ruchu ani odbierać do nich ruchu, skierować ruch do chronionych punktów końcowych infrastruktury lub Wyślij/Odbierz niewłaściwy ruch emisji.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Dodatkowe reguły skonfigurowane przez agenta kontrolera sieci szkieletowej do izolowania maszyny wirtualnej

Domyślnie cały ruch jest blokowany podczas tworzenia maszyny wirtualnej, a następnie Agent kontrolera sieci szkieletowej konfiguruje filtr pakietów w celu dodania reguł i wyjątków w celu zezwolenia na autoryzowany ruch.

Istnieją dwie kategorie reguł, które są programowane:

- **Konfiguracja komputera lub reguły infrastruktury:** Domyślnie cała komunikacja jest blokowana. Istnieją wyjątki umożliwiające wysyłanie i odbieranie ruchu DHCP i DNS przez maszynę wirtualną. Maszyny wirtualne mogą również wysyłać ruch do "publicznego" Internetu i wysyłać ruch do innych maszyn wirtualnych w ramach tej samej usługi Azure Virtual Network i serwera aktywacji systemu operacyjnego. Lista maszyn wirtualnych z dozwolonych wychodzących miejsc docelowych nie obejmuje podsieci routera platformy Azure, zarządzania platformy Azure i innych właściwości firmy Microsoft.
- **Plik konfiguracji roli:** Definiuje on listy Access Control przychodzących (ACL) w oparciu o model usługi dzierżawców.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN

Każdy klaster zawiera trzy sieci VLAN:

![Izolacja sieci VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)

- Główna sieć VLAN — połączenia węzłów niezaufanych klientów
- Sieć VLAN FC — zawiera zaufany system FCs i systemy pomocnicze
- Urządzenie VLAN — zawiera zaufane sieci i inne urządzenia infrastruktury

Komunikacja jest dozwolona z sieci VLAN FC do głównej sieci VLAN, ale nie może zostać zainicjowana z głównej sieci VLAN do sieci VLAN FC. Komunikacja jest również blokowana z głównej sieci VLAN do sieci VLAN urządzenia. Gwarantuje to, że nawet w przypadku naruszenia zabezpieczeń w węźle, w którym jest uruchamiany kod klienta, nie można zaatakować węzłów w sieci VLAN lub na urządzeniach.

## <a name="storage-isolation"></a>Izolacja magazynu

### <a name="logical-isolation-between-compute-and-storage"></a>Izolacja logiczna między COMPUTE i magazynem

W ramach podstawowego projektu, Microsoft Azure oddziela obliczenia oparte na maszynach wirtualnych z magazynu. Takie Separacja umożliwia niezależne skalowanie i przechowywanie danych, co ułatwia zapewnienie wielu dzierżawców i izolację.

W związku z tym usługa Azure Storage działa na osobnym sprzęcie bez łączności sieciowej z usługą Azure COMPUTE, z wyjątkiem logicznego. Oznacza to, że po utworzeniu dysku wirtualnego miejsce na dysku nie jest przydzielono do całej pojemności. Zamiast tego tworzona jest tabela, która mapuje adresy na dysku wirtualnym do obszarów na dysku fizycznym, a ta tabela jest początkowo pusta. **Po raz pierwszy klient zapisuje dane na dysku wirtualnym, przydzieli miejsce na dysku fizycznym, a wskaźnik do niego jest umieszczany w tabeli.**

### <a name="isolation-using-storage-access-control"></a>Izolacja przy użyciu kontroli dostępu do magazynu

**Access Control w usłudze Azure Storage** ma prosty model kontroli dostępu. Każda subskrypcja platformy Azure może utworzyć co najmniej jedno konto magazynu. Każde konto magazynu ma jeden klucz tajny, który służy do kontrolowania dostępu do wszystkich danych na tym koncie magazynu.

![Izolacja przy użyciu kontroli dostępu do magazynu](./media/isolation-choices/azure-isolation-fig9.png)

**Dostęp do danych usługi Azure Storage (w tym tabel)** można kontrolować za pomocą tokenu sygnatury [dostępu współdzielonego](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) , który zapewnia dostęp z zakresu. Sygnatura dostępu współdzielonego jest tworzona za pomocą szablonu zapytania (URL) podpisanego przy użyciu [SAK (klucza konta magazynu)](https://msdn.microsoft.com/library/azure/ee460785.aspx). [Podpisany adres URL](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) można podać innemu procesowi (delegowanemu), który następnie może wypełnić szczegóły zapytania i przekazać żądanie usługi magazynu. Sygnatura dostępu współdzielonego umożliwia przyznanie klientom dostęp oparty na czasie bez ujawniania klucza tajnego konta magazynu.

Sygnatura dostępu współdzielonego oznacza, że można udzielić klientowi ograniczonych uprawnień do obiektów w naszym koncie magazynu przez określony czas i z określonym zestawem uprawnień. Firma Microsoft może przyznać te ograniczone uprawnienia bez konieczności udostępniania kluczy dostępu do konta.

### <a name="ip-level-storage-isolation"></a>Izolacja magazynu na poziomie IP

Można nawiązywać zapory i definiować zakres adresów IP dla zaufanych klientów. Z zakresem adresów IP tylko klienci, którzy mają adres IP w zdefiniowanym zakresie, mogą łączyć się z [usługą Azure Storage](../../storage/blobs/security-recommendations.md).

Dane magazynu IP mogą być chronione przed nieautoryzowanymi użytkownikami za pomocą mechanizmu sieciowego, który jest używany do przydzielania dedykowanego lub dedykowanego tunelu ruchu do magazynu IP.

### <a name="encryption"></a>Szyfrowanie

Platforma Azure oferuje następujące typy szyfrowania do ochrony danych:

- Szyfrowanie podczas transferu
- Szyfrowanie w spoczynku

#### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Szyfrowanie podczas przesyłania jest mechanizmem ochrony danych przesyłanych między sieciami. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:

- [Szyfrowanie na poziomie transportu](../../storage/blobs/security-recommendations.md), takie jak https, podczas transferu danych do usługi Azure Storage lub z niej.
- [Szyfrowanie przewodowe](../../storage/blobs/security-recommendations.md), takie jak szyfrowanie SMB 3,0 dla udziałów plików platformy Azure.
- [Szyfrowanie po stronie klienta](../../storage/blobs/security-recommendations.md)umożliwia szyfrowanie danych przed ich przesłaniem do magazynu oraz odszyfrowanie danych po ich przeniesieniu poza magazyn.

#### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

W przypadku wielu organizacji [szyfrowanie danych w spoczynku](isolation-choices.md) stanowi obowiązkowy krok w kierunku prywatności, zgodności i suwerenności danych. Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowanie danych "w spoczynku":

- [Szyfrowanie usługi Storage](../../storage/blobs/security-recommendations.md) pozwala na zażądanie, aby usługa magazynu automatycznie szyfruje dane podczas ich zapisywania do usługi Azure Storage.
- [Szyfrowanie po stronie klienta](../../storage/blobs/security-recommendations.md) udostępnia również funkcję szyfrowania w stanie spoczynku.
- [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

#### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) dla maszyn wirtualnych pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji przez szyfrowanie dysków maszyny wirtualnej (w tym dysków rozruchowych i danych) przy użyciu kluczy i zasad, które można kontrolować w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Rozwiązanie do szyfrowania dysków dla systemu Windows jest oparte na [szyfrowanie dysków funkcją BitLocker firmy Microsoft](https://technet.microsoft.com/library/cc732774.aspx), a rozwiązanie Linux opiera się na usłudze [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, gdy są one włączone w Microsoft Azure:

- Integracja z usługą Azure Key Vault
- Maszyny wirtualne w warstwie Standardowa: A, D, DS, G, GS i tak dalej, maszyny wirtualne IaaS serii
- Włączanie szyfrowania na maszynach wirtualnych z systemem Windows i Linux IaaS
- Wyłączanie szyfrowania na dyskach systemu operacyjnego i danych dla maszyn wirtualnych z systemem Windows IaaS
- Wyłączanie szyfrowania na dyskach danych dla maszyn wirtualnych z systemem Linux IaaS
- Włączanie szyfrowania na maszynach wirtualnych IaaS, na których działa klient systemu operacyjnego Windows
- Włączanie szyfrowania woluminów z ścieżkami instalacji
- Włączanie szyfrowania na maszynach wirtualnych z systemem Linux, które są skonfigurowane przy użyciu funkcji rozkładania dysku (RAID) za pomocą [mdadm](https://en.wikipedia.org/wiki/Mdadm)
- Włączanie szyfrowania na maszynach wirtualnych z systemem Linux przy użyciu programu [LVM (Menedżer woluminów logicznych)](https://msdn.microsoft.com/library/windows/desktop/bb540532) dla dysków danych
- Włączanie szyfrowania na maszynach wirtualnych z systemem Windows skonfigurowanych przy użyciu funkcji miejsca do magazynowania
- Wszystkie publiczne regiony platformy Azure są obsługiwane

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii w wersji:

- Maszyny wirtualne w warstwie Podstawowa IaaS
- Wyłączanie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych z systemem Linux IaaS
- IaaS maszyny wirtualne, które są tworzone przy użyciu klasycznej metody tworzenia maszyny wirtualnej
- Integracja z lokalną usługą zarządzania kluczami
- Azure Files (udostępniony system plików), sieciowy system plików (NFS), woluminy dynamiczne i maszyny wirtualne z systemem Windows skonfigurowane z systemami RAID opartymi na oprogramowaniu

## <a name="sql-database-isolation"></a>Izolacja SQL Database

Usługa SQL Database jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na wiodącym na rynku aparacie programu Microsoft SQL Server. Może ona obsługiwać obciążenia o znaczeniu krytycznym. SQL Database oferuje przewidywalną izolację danych na poziomie konta, lokalizacji geograficznej/regionie i w oparciu o sieć — wszystko z administracją niemal od zera.

### <a name="sql-database-application-model"></a>Model aplikacji SQL Database

[Microsoft SQL Database](../../azure-sql/database/single-database-create-quickstart.md) jest usługą relacyjnej bazy danych opartą na chmurze wbudowaną na SQL Server technologii. Zapewnia wysoką dostępność, skalowalną i wielodostępną usługę bazy danych hostowaną przez firmę Microsoft w chmurze.

Z perspektywy aplikacji SQL Database udostępnia następujące hierarchie: każdy poziom zawiera jeden do wielu poziomów poniżej.

![Model aplikacji SQL Database](./media/isolation-choices/azure-isolation-fig10.png)

Konto i subskrypcja są Microsoft Azure koncepcje platformy dotyczące kojarzenia rozliczeń i zarządzania nimi.

Logiczne serwery i bazy danych SQL są pojęciami specyficznymi dla SQL Database i są zarządzane przy użyciu SQL Database, pod warunkiem, że interfejsy OData i TSQL lub za pośrednictwem Azure Portal.

Serwery w SQL Database nie są wystąpieniami fizycznymi ani MASZYNami wirtualnymi, a nie są kolekcjami baz danych, współużytkowania i zasad zabezpieczeń, które są przechowywane w taki sam sposób zwane "logicznymi bazami danych".

![Baza danych SQL](./media/isolation-choices/azure-isolation-fig11.png)

Logiczne główne bazy danych to:

- Logowania SQL używane do nawiązywania połączenia z serwerem
- Reguły zapory

Informacje dotyczące rozliczeń i użycia dla baz danych z tego samego serwera nie są gwarantowane w tym samym wystąpieniu fizycznym w klastrze, zamiast tego aplikacje muszą podawać nazwę docelowej bazy danych podczas nawiązywania połączenia.

Z punktu widzenia klienta serwer jest tworzony w regionie geograficznym, podczas gdy rzeczywiste Tworzenie serwera odbywa się w jednym z klastrów w regionie.

### <a name="isolation-through-network-topology"></a>Izolacja przy użyciu topologii sieci

Po utworzeniu serwera i zarejestrowaniu jego nazwy DNS nazwa DNS wskazuje na tak zwane adres VIP bramy w konkretnym centrum danych, w którym został umieszczony serwer.

Za adresem VIP (wirtualny adres IP) istnieje kolekcja usług bezstanowych bramy. Ogólnie rzecz biorąc, bramy są włączane, gdy wymagana jest koordynacja między wieloma źródłami danych (baza danych Master, baza danych użytkownika itp.). Usługi bramy implementują następujące czynności:

- **Proxy połączeń TDS.** Obejmuje to lokalizowanie bazy danych użytkowników w klastrze zaplecza, implementację sekwencji logowania, a następnie przekazanie pakietów TDS do zaplecza i z powrotem.
- **Zarządzanie bazami danych.** Obejmuje to implementację kolekcji przepływów pracy do wykonywania operacji tworzenia/modyfikowania/usuwania baz danych. Operacje bazy danych mogą być wywoływane przez wykrywanie pakietów TDS lub jawne interfejsy API OData.
- Utwórz/Zmień/upuść operacje logowania/użytkownika
- Operacje zarządzania serwerem za pośrednictwem interfejsu API OData

![Izolacja przy użyciu topologii sieci](./media/isolation-choices/azure-isolation-fig12.png)

Warstwa za bramami jest nazywana "zapleczem". Jest to miejsce, w którym wszystkie dane są przechowywane w sposób wysokiej dostępności. Każdy element danych jest określany jako należący do "partycji" lub "jednostki trybu failover", z których każdy ma co najmniej trzy repliki. Repliki są przechowywane i replikowane przez aparat SQL Server i zarządzane przez system pracy awaryjnej często określany jako "Sieć szkieletowa".

Ogólnie system zaplecza nie komunikuje się z innymi systemami jako środkiem bezpieczeństwa. Jest to zarezerwowane dla systemów w warstwie frontonu (bramy). Maszyny z warstwą bramy mają ograniczone uprawnienia na maszynach zaplecza, aby zminimalizować obszar ataków jako mechanizm obrony szczegółowej.

### <a name="isolation-by-machine-function-and-access"></a>Izolacja według funkcji maszyny i dostępu

SQL Database (składa się z usług działających na różnych funkcjach maszyn. SQL Database jest podzielony na "zaplecze" bazy danych w chmurze i "frontonu" (Brama/zarządzanie), z ogólną zasadą ruchu tylko do zaplecza, a nie na zewnątrz. Środowisko frontonu może komunikować się z zewnętrznymi innymi usługami i generalnie ma tylko ograniczone uprawnienia w zapleczu (wystarczy do wywołania punktów wejścia potrzebnych do wywołania).

## <a name="networking-isolation"></a>Izolacja sieci

Wdrożenie platformy Azure ma wiele warstw izolacji sieci. Na poniższym diagramie przedstawiono różne warstwy izolacji sieci systemu Azure udostępnia klientom. Te warstwy są zarówno natywne na platformie Azure, jak i funkcje zdefiniowane przez klienta. Ruch przychodzący z Internetu, usługa Azure DDoS zapewnia izolację przed atakami na dużą skalę na platformie Azure. Kolejną warstwą izolacji są zdefiniowane przez klienta publiczne adresy IP (punkty końcowe), które służą do określenia, który ruch może przechodzić przez usługę w chmurze do sieci wirtualnej. Natywna izolacja sieci wirtualnej platformy Azure zapewnia pełną izolację ze wszystkich innych sieci, a ruch przepływów odbywa się tylko przez ścieżki i metody skonfigurowane przez użytkownika. Te ścieżki i metody są następną warstwą, w której urządzenia wirtualne sieciowych grup zabezpieczeń, UDR i Network mogą służyć do tworzenia granic izolacji, aby chronić wdrożenia aplikacji w chronionej sieci.

![Izolacja sieci](./media/isolation-choices/azure-isolation-fig13.png)

**Izolacja ruchu:** [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md) to granica izolacji ruchu na platformie Azure. Maszyny wirtualne w jednej sieci wirtualnej nie mogą komunikować się bezpośrednio z maszynami wirtualnymi w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta. Izolacja jest właściwością krytyczną, która zapewnia, że maszyny wirtualne klienta i komunikacja pozostają prywatne w ramach sieci wirtualnej.

[Podsieć](../../virtual-network/virtual-networks-overview.md) oferuje dodatkową warstwę izolacji w sieci wirtualnej na podstawie zakresu adresów IP. Adresy IP w sieci wirtualnej można podzielić na wiele podsieci w celu zapewnienia organizacji i zabezpieczeń. Maszyny wirtualne i wystąpienia ról PaaS wdrożone w podsieciach (tych samych lub różnych) w ramach sieci wirtualnej mogą komunikować się ze sobą bez dodatkowego konfigurowania. Możesz również skonfigurować [grupę zabezpieczeń sieci (sieciowych grup zabezpieczeń)](../../virtual-network/virtual-networks-overview.md) , aby zezwolić na ruch sieciowy do wystąpienia maszyny wirtualnej lub go zabronić na podstawie reguł skonfigurowanych na liście kontroli dostępu (ACL) sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci.

## <a name="next-steps"></a>Następne kroki

- Informacje [na temat opcji izolacji sieciowej dla maszyn w sieciach wirtualnych platformy Microsoft Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Obejmuje to klasyczny scenariusz frontonu i zaplecza, w którym maszyny w określonej sieci wewnętrznej lub podsieci mogą zezwalać tylko określonym klientom lub innym komputerom na łączenie się z konkretnym punktem końcowym na podstawie listy dozwolonych adresów IP.

- Dowiedz się więcej o [izolacji maszyny wirtualnej na platformie Azure](../../virtual-machines/isolation.md). Usługa Azure COMPUTE oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta.
