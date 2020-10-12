---
title: 'Azure AD Connect Sync: Omówienie konfiguracji domyślnej | Microsoft Docs'
description: W tym artykule opisano konfigurację domyślną w Azure AD Connect synchronizacji.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295230"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej
W tym artykule wyjaśniono, jakie są reguły konfiguracji. Dokumentuje reguły i sposób, w jaki te reguły wpływają na konfigurację. Przedstawiono w nim również domyślną konfigurację synchronizacji Azure AD Connect. Celem jest to, że czytelnik rozumie, jak model konfiguracji o nazwie deklaracyjne Inicjowanie obsługi działa w świecie rzeczywistym. W tym artykule przyjęto założenie, że już zainstalowano i skonfigurowano synchronizację Azure AD Connect przy użyciu Kreatora instalacji.

Aby zapoznać się ze szczegółami dotyczącymi modelu konfiguracji, przeczytaj artykuł [Omówienie aprowizacji deklaracyjnej](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Wbudowane reguły z lokalizacji lokalnej do usługi Azure AD
Poniższe wyrażenia można znaleźć w konfiguracji wstępnej.

### <a name="user-out-of-box-rules"></a>Wstępnie zdefiniowane reguły użytkownika
Te reguły mają zastosowanie również do typu obiektu iNetOrgPerson.

Obiekt użytkownika musi spełniać następujące wymagania, aby można było synchronizować:

* Musi mieć element sourceAnchor.
* Po utworzeniu obiektu w usłudze Azure AD nie można zmienić sourceAnchor. Jeśli wartość zostanie zmieniona lokalnie, obiekt zatrzyma synchronizację do momentu zmiany sourceAnchor z powrotem na poprzednią wartość.
* Musi mieć atrybut accountEnabled (konta użytkownika) wypełniony. W przypadku Active Directory lokalnego ten atrybut jest zawsze obecny i wypełniany.

Następujące obiekty użytkownika **nie** są zsynchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, że wiele obiektów out-of-Box w Active Directory, takich jak wbudowane konto administratora, nie są zsynchronizowane.
* `IsPresent([sAMAccountName]) = False`. Upewnij się, że obiekty użytkownika bez atrybutu sAMAccountName nie są zsynchronizowane. W takim przypadku praktycznie występuje tylko w domenie uaktualnionej z systemu NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Nie należy synchronizować konta usługi używanego przez Azure AD Connect Sync i jego wcześniejsze wersje.
* Nie należy synchronizować kont programu Exchange, które nie działają w usłudze Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nie należy synchronizować obiektów, które nie działają w usłudze Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ta maska bitowa (&H21C07000) odfiltruje następujące obiekty:
  * Folder publiczny z włączoną obsługą poczty (w wersji zapoznawczej jako wersja 1.1.524.0)
  * Skrzynka pocztowa System Attendant
  * Skrzynka pocztowa skrzynek pocztowych (Skrzynka pocztowa systemu)
  * Uniwersalna Grupa zabezpieczeń (nie dotyczy użytkownika, ale jest obecna ze względu na starsze przyczyny)
  * Grupa nieuniwersalna (nie dotyczy użytkownika, ale występuje ze względu na starsze przyczyny)
  * Plan skrzynki pocztowej
  * Skrzynka pocztowa odnajdywania
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie Synchronizuj żadnych obiektów ofiary replikacji.

Mają zastosowanie następujące reguły atrybutów:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Atrybut sourceAnchor nie został wniesiony z połączonej skrzynki pocztowej. Przyjęto założenie, że jeśli zostanie znaleziona połączona Skrzynka pocztowa, rzeczywiste konto zostanie przyłączone później.
* Atrybuty powiązane z programem Exchange są synchronizowane tylko wtedy, gdy atrybut **mailNickname** ma wartość.
* Jeśli istnieje wiele lasów, atrybuty są używane w następującej kolejności:
  1. Atrybuty związane z logowaniem (na przykład userPrincipalName) są współtworzone z lasu z włączonym kontem.
  2. Atrybuty, które można znaleźć w tabeli programu Exchange (globalna lista adresów), są tworzone z lasu z skrzynek pocztowych programu Exchange.
  3. Jeśli nie można znaleźć żadnej skrzynki pocztowej, te atrybuty mogą pochodzić z dowolnego lasu.
  4. Atrybuty powiązane z programem Exchange (atrybuty techniczne, które nie są widoczne w tabeli "DB"), są tworzone w lesie, w którym `mailNickname ISNOTNULL` .
  5. Jeśli istnieje wiele lasów, które będą spełniały jedną z tych reguł, kolejność tworzenia (Data/godzina) łączników (lasów) jest używana do określenia, który Las współużytkuje atrybuty. Pierwszy Las połączony będzie pierwszym lasem do synchronizacji. 

### <a name="contact-out-of-box-rules"></a>Skontaktuj się z regułami dotyczącymi obecności
Obiekt Contact musi spełniać następujące wymagania, aby można było synchronizować:

* Kontakt musi mieć włączoną obsługę poczty. Jest on weryfikowany z następującymi regułami:
  * `IsPresent([proxyAddresses]) = True)`. Atrybut proxyAddresses musi zostać wypełniony.
  * Podstawowy adres e-mail można znaleźć w atrybucie proxyAddresses lub w atrybucie mail. Obecność \@ jest używana do sprawdzenia, czy zawartość jest adresem e-mail. Dla jednej z tych dwóch reguł należy oszacować wartość true.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Czy istnieje wpis z ciągiem "SMTP:" i jeśli istnieje, można \@ go znaleźć w ciągu?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Czy atrybut mail jest wypełniony i jeśli jest, można \@ go znaleźć w ciągu?

Następujące obiekty Contact **nie** są zsynchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, że żadne obiekty Contact nie są oznaczone jako krytyczne są zsynchronizowane. Nie powinno być żadnej konfiguracji domyślnej.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Te obiekty nie działają w usłudze Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie Synchronizuj żadnych obiektów ofiary replikacji.

### <a name="group-out-of-box-rules"></a>Grupowanie reguł wbudowanych
Obiekt grupy musi spełniać następujące wymagania, aby można było synchronizować:

* Musi mieć mniej niż 50 000 członków. Ta liczba jest liczbą członków w grupie lokalnej.
  * Jeśli ma więcej elementów członkowskich przed rozpoczęciem synchronizacji po raz pierwszy, Grupa nie zostanie zsynchronizowana.
  * Jeśli liczba członków rośnie od momentu jego pierwszego utworzenia, wówczas gdy osiągnie 50 000 elementów członkowskich, zatrzyma synchronizację do momentu, aż liczba członkostwa będzie mniejsza od 50 000.
  * Uwaga: liczba członkostwa 50 000 jest również wymuszana przez usługę Azure AD. Nie można synchronizować grup z większą liczbą członków nawet w przypadku zmodyfikowania lub usunięcia tej reguły.
* Jeśli grupa jest **grupą dystrybucyjną**, musi również mieć włączoną opcję Poczta. Sprawdź [,](#contact-out-of-box-rules) czy dla tej reguły są wymuszane reguły z nieaktualnym systemem.

Następujące obiekty grupy **nie** są zsynchronizowane z usługą Azure AD:

* `IsPresent([isCriticalSystemObject])`. Upewnij się, że wiele obiektów out-of-Box w Active Directory, takich jak wbudowana grupa Administratorzy, nie są zsynchronizowane.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Starsza Grupa używana przez narzędzie DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupa ról.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Nie Synchronizuj żadnych obiektów ofiary replikacji.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal reguły out-of-Box
FSPs są przyłączone do obiektu "any" ( \* ) w obiekcie Metaverse. W rzeczywistości ten sprzężenie występuje tylko w przypadku użytkowników i grup zabezpieczeń. Ta konfiguracja zapewnia, że członkostwa między lasami są rozpoznawane i poprawnie reprezentowane w usłudze Azure AD.

### <a name="computer-out-of-box-rules"></a>Komputery z nieaktualnymi regułami
Obiekt komputera musi spełniać następujące wymagania, aby można było synchronizować:

* `userCertificate ISNOTNULL`. Tylko komputery z systemem Windows 10 wypełniają ten atrybut. Wszystkie obiekty komputerów o wartości w tym atrybucie są synchronizowane.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Zrozumienie scenariusza reguł out-of-Box
W tym przykładzie używamy wdrożenia z jednym lasem konta (A), jednego lasu zasobów (R) i jednego katalogu usługi Azure AD.

![Obraz przedstawiający opis scenariusza](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

W tej konfiguracji zakłada się, że w lesie konta jest włączone konto i wyłączone konto w lesie zasobów z połączoną skrzynką pocztową.

Naszym celem jest konfiguracja domyślna:

* Atrybuty związane z logowaniem są synchronizowane z lasu z włączonym kontem.
* Atrybuty, które mogą znajdować się w spisie (globalna lista adresów), są synchronizowane z lasu za pomocą skrzynki pocztowej. Jeśli żadna Skrzynka pocztowa nie zostanie znaleziona, używany jest inny Las.
* Jeśli zostanie znaleziona połączona Skrzynka pocztowa, należy odnaleźć połączone konto, które ma zostać wyeksportowane do usługi Azure AD.

### <a name="synchronization-rule-editor"></a>Edytor reguł synchronizacji
Konfigurację można przeglądać i zmieniać przy użyciu edytora reguł synchronizacji narzędzi (SRE), a skrót do niego można znaleźć w menu Start.

![Ikona edytora reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE jest narzędziem zestawu Resource Kit i jest instalowany z Azure AD Connect Sync. Aby móc go uruchomić, musisz być członkiem grupy ADSyncAdmins. Po rozpoczęciu zostanie wyświetlony następujący element:

![Reguły synchronizacji przychodzące](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

W tym okienku są wyświetlane wszystkie reguły synchronizacji utworzone dla danej konfiguracji. Każdy wiersz w tabeli jest jedną regułą synchronizacji. Z lewej strony w obszarze typy zasad wyświetlane są dwa różne typy: przychodzące i wychodzące. Ruch przychodzący i wychodzący pochodzi z widoku Metaverse. Zamierzasz głównie skupić się na regułach ruchu przychodzącego w tym omówieniu. Rzeczywista lista reguł synchronizacji zależy od wykrytego schematu w usłudze AD. Na powyższym rysunku Las konta (fabrikamonline.com) nie ma żadnych usług, takich jak Exchange i Lync, i nie utworzono żadnych reguł synchronizacji dla tych usług. Jednak w lesie zasobów (res.fabrikamonline.com) znajdziesz reguły synchronizacji dla tych usług. Zawartość reguł jest różna w zależności od wykrytej wersji. Na przykład w przypadku wdrożenia z programem Exchange 2013 istnieje więcej przepływów atrybutów skonfigurowanych niż w programie Exchange 2010/2007.

### <a name="synchronization-rule"></a>Reguła synchronizacji
Reguła synchronizacji jest obiektem konfiguracji z zestawem atrybutów przepływających po spełnieniu warunku. Służy również do opisywania, jak obiekt w obszarze łącznika jest powiązany z obiektem w obiekcie Metaverse, nazywanym **sprzężeniem** lub **dopasowaniem**. Reguły synchronizacji mają wartość pierwszeństwa wskazującą, w jaki sposób są one powiązane ze sobą. Reguła synchronizacji o niższej wartości liczbowej ma wyższy priorytet i w konflikcie przepływu atrybutów ma wyższy priorytet w przypadku rozwiązywania konfliktów przez usługę WINS.

Na przykład zapoznaj się z regułą synchronizacji **w programie z usługi AD — User AccountEnabled**. Oznacz ten wiersz w SRE i wybierz pozycję **Edytuj**.

Ponieważ ta reguła jest regułą, podczas otwierania reguły zostanie wyświetlone ostrzeżenie. Nie należy wprowadzać żadnych [zmian do reguł out-of-Box](how-to-connect-sync-best-practices-changing-default-configuration.md), więc zostanie wyświetlony monit o zamiar. W tym przypadku chcesz tylko wyświetlić regułę. Wybierz pozycję **Nie**.

![Ostrzeżenie o regułach synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Reguła synchronizacji zawiera cztery sekcje konfiguracji: Opis, filtr zakresu, reguły sprzężenia i przekształcenia.

#### <a name="description"></a>Opis
Pierwsza sekcja zawiera podstawowe informacje, takie jak nazwa i opis.

![Karta opis w Edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Możesz również znaleźć informacje o tym, z którym połączonym systemem jest związana ta reguła, który typ obiektu w połączonym systemie ma zastosowanie i typ obiektu Metaverse. Obiekt typu Metaverse jest zawsze osobą niezależnie, gdy typem obiektu źródłowego jest użytkownik, Klasa iNetOrgPerson lub kontakt. Typ obiektu metaverse nie powinien się nigdy zmieniać, dlatego jest tworzony jako typ ogólny. Typ łącza może być ustawiony na przyłączenie, StickyJoin lub inicjowanie obsługi administracyjnej. To ustawienie działa w połączeniu z sekcją reguły sprzężenia i jest omówione później.

Możesz również sprawdzić, czy ta reguła synchronizacji jest używana do synchronizacji haseł. Jeśli użytkownik znajduje się w zakresie dla tej reguły synchronizacji, hasło jest synchronizowane z lokalizacji lokalnej do chmury (przy założeniu, że funkcja synchronizacji haseł została włączona).

#### <a name="scoping-filter"></a>Filtr zakresu
Sekcja filtr zakresu służy do konfigurowania czasu, w którym ma zostać zastosowana reguła synchronizacji. Ponieważ nazwa reguły synchronizacji, którą przeglądasz, wskazuje, że powinna być stosowana tylko dla włączonych użytkowników, zakres jest skonfigurowany, **dlatego ATRYBUT AD** kontroli konta użytkownika nie może mieć zestawu bit 2. Gdy aparat synchronizacji odnajdzie użytkownika w usłudze AD, stosuje tę regułę synchronizacji **, gdy dla** konta użytkownika jest ustawiona wartość dziesiętna 512 (włączony normalny użytkownik). Nie stosuje reguły, jeśli **użytkownik ma** ustawioną wartość 514 (wyłączony normalny użytkownik).

![Zrzut ekranu przedstawiający sekcję "filtr określania zakresu" w oknie "Edytowanie reguły synchronizacji ruchu przychodzącego".](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Filtr zakresu ma grupy i klauzule, które mogą być zagnieżdżane. Aby reguła synchronizacji została zastosowana, należy spełnić wszystkie klauzule wewnątrz grupy. Jeśli zdefiniowano wiele grup, należy spełnić co najmniej jedną grupę, aby można było zastosować regułę. Oznacza to, że logiczne lub jest oceniane między grupami, a wartość logiczna jest obliczana w grupie. Przykład tej konfiguracji można znaleźć w przystawce reguła synchronizacji ruchu wychodzącego **do usługi AAD — Grupa**. Istnieje kilka grup filtru synchronizacji, na przykład jeden dla grup zabezpieczeń ( `securityEnabled EQUAL True` ) i jeden dla grup dystrybucyjnych ( `securityEnabled EQUAL False` ).

![Karta Określanie zakresu w Edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Ta reguła służy do definiowania grup, które powinny być obsługiwane w usłudze Azure AD. Grupy dystrybucji muszą mieć włączoną opcję Poczta, aby można było synchronizować z usługą Azure AD, ale dla grup zabezpieczeń nie jest wymagana poczta e-mail.

#### <a name="join-rules"></a>Reguły dołączania
Trzecia sekcja służy do konfigurowania sposobu, w jaki obiekty w przestrzeni łącznika odnoszą się do obiektów w obiekcie Metaverse. Zasada, którą oglądasz wcześniej, nie ma żadnych konfiguracji reguł dołączania, dlatego należy się przyjrzeć do **usługi AD — przyłączenie użytkownika**.

![Karta reguły sprzężenia w Edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Zawartość reguły sprzężenia zależy od pasującej opcji wybranej w Kreatorze instalacji. Dla reguły ruchu przychodzącego Ocena zaczyna się od obiektu w przestrzeni łącznika źródłowego, a każda grupa w regułach sprzężenia jest oceniana w sekwencji. Jeśli obiekt źródłowy jest oceniany tak, aby odpowiadał dokładnie jednemu obiektowi w obiekcie Metaverse przy użyciu jednej z reguł sprzężenia, obiekty są przyłączone. Jeśli wszystkie reguły zostały ocenione i nie są zgodne, zostanie użyty typ linku na stronie opis. Jeśli ta konfiguracja jest ustawiona na wartość aprowizacji, nowy obiekt jest tworzony w obiekcie **docelowym, Metaverse**, jeśli co najmniej jeden atrybut w kryteriach sprzężenia jest obecny (ma wartość). Aby zainicjować obsługę nowego obiektu w obiekcie Metaverse, jest on również znany jako **projekt** obiektu do Metaverse.

Reguły sprzężenia są oceniane tylko raz. Gdy obiekt przestrzeni łącznika i obiekt Metaverse są przyłączone, pozostaną one dołączone, dopóki zakres reguły synchronizacji jest nadal spełniony.

Podczas oceniania reguł synchronizacji tylko jedna reguła synchronizacji ze zdefiniowanymi regułami sprzężenia musi znajdować się w zakresie. W przypadku znalezienia wielu reguł synchronizacji z regułami sprzężenia dla jednego obiektu zostanie zgłoszony błąd. Z tego powodu najlepszym rozwiązaniem jest posiadanie tylko jednej reguły synchronizacji z przyłączaniem zdefiniowanym, gdy wiele reguł synchronizacji znajduje się w zakresie dla obiektu. W konfiguracji wstępnej dla Azure AD Connect synchronizacji te reguły można znaleźć, sprawdzając nazwę i znajdź je za pomocą **sprzężenia** wyrazu na końcu nazwy. Reguła synchronizacji bez zdefiniowanej reguły sprzężenia stosuje przepływy atrybutu, gdy inna reguła synchronizacji została dołączona do obiektów razem lub zainicjowano nowy obiekt w miejscu docelowym.

Jeśli zobaczysz zdjęcie powyżej, zobaczysz, że reguła próbuje dołączyć do **objectSID** z **msExchMasterAccountSid** (Exchange) i **msRTCSIP-OriginatorSid** (Lync), co jest oczekiwane w topologii lasu zasobów konta. Tę samą regułę znajdziesz na wszystkich lasach. Przyjęto założenie, że każdy Las może być kontem lub lasem zasobów. Ta konfiguracja działa również w przypadku kont, które znajdują się w jednym lesie i nie muszą być połączone.

#### <a name="transformations"></a>Przekształcenia
Sekcja przekształcenia definiuje wszystkie przepływy atrybutów, które są stosowane do obiektu docelowego, gdy obiekty są przyłączone, a filtr zakresu jest spełniony. Wróć do programu z reguły synchronizacji **usługi AD — User AccountEnabled** , aby znaleźć następujące przekształcenia:

![Karta przekształcenia w Edytorze reguł synchronizacji](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Aby wprowadzić tę konfigurację w kontekście, w ramach wdrożenia lasu Account-Resource należy znaleźć włączone konto w lesie konta i wyłączone konto w lesie zasobów z ustawieniami programu Exchange i Lync. Reguła synchronizacji, która jest wyświetlana, zawiera atrybuty wymagane do logowania, a te atrybuty powinny przepływać z lasu, w którym jest włączone konto. Wszystkie te przepływy atrybutów są umieszczane w jednej regule synchronizacji.

Transformacja może mieć różne typy: stałe, bezpośrednie i wyrażenie.

* Stały przepływ zawsze przepływie wartości stałe. W powyższym przypadku zawsze ustawia wartość **true** w atrybucie Metaverse o nazwie **accountEnabled**.
* Bezpośredni przepływ zawsze przenosi wartość atrybutu w źródle do atrybutu docelowego w postaci, w jakiej jest.
* Typ trzeciego przepływu jest wyrażeniem, co pozwala na bardziej zaawansowane konfiguracje.

Język wyrażenia to VBA (Visual Basic for Applications), dzięki czemu użytkownicy korzystający z Microsoft Office lub VBScript będą rozpoznawali format. Atrybuty są ujęte w nawiasy kwadratowe [attributeName]. W nazwach atrybutów i nazwach funkcji jest rozróżniana wielkość liter, ale Edytor reguł synchronizacji oblicza wyrażenia i wyświetla ostrzeżenie, jeśli wyrażenie jest nieprawidłowe. Wszystkie wyrażenia są wyrażane w pojedynczym wierszu z zagnieżdżonymi funkcjami. Aby wyświetlić możliwości języka konfiguracji, Oto przepływ dla pwdLastSet, ale z dodatkowymi komentarzami:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Aby uzyskać więcej informacji na temat języka wyrażeń dla przepływów atrybutów, zobacz artykuł [Omówienie wyrażeń aprowizacji deklaracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Pierwszeństwo
Zostały już wyszukane poszczególne reguły synchronizacji, ale reguły współpracują ze sobą w konfiguracji. W niektórych przypadkach wartość atrybutu jest współtworzona z wielu reguł synchronizacji do tego samego atrybutu docelowego. W takim przypadku pierwszeństwo atrybutu służy do określenia, który atrybut WINS. Na przykład poszukaj atrybutu sourceAnchor. Ten atrybut jest ważnym atrybutem, aby można było zalogować się do usługi Azure AD. Istnieje możliwość znalezienia przepływu atrybutu dla tego atrybutu w dwóch różnych regułach synchronizacji **w programie z usługi AD — User AccountEnabled** i **from z usługi AD — wspólne dla użytkownika**. Ze względu na pierwszeństwo reguły synchronizacji atrybut sourceAnchor jest współtworzony z lasu z włączonym kontem, gdy istnieje kilka obiektów przyłączonych do obiektu Metaverse. Jeśli nie ma włączonych kont, aparat synchronizacji korzysta z reguły synchronizacji catch-all **w programie z usługi AD — wspólne przez użytkownika**. Ta konfiguracja gwarantuje, że nawet w przypadku kont, które są wyłączone, nadal istnieje sourceAnchor.

![Reguły synchronizacji przychodzące](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Pierwszeństwo reguł synchronizacji są ustawiane w grupach przez Kreatora instalacji. Wszystkie reguły w grupie mają taką samą nazwę, ale są połączone z różnymi połączonymi katalogami. Kreator instalacji udziela reguły **z usługi AD — przyłączanie użytkownika** do najwyższego priorytetu i wykonuje iterację wszystkich podłączonych katalogów usługi AD. Następnie kontynuuje pracę z następnymi grupami reguł we wstępnie zdefiniowanej kolejności. Wewnątrz grupy są dodawane reguły w kolejności, w jakiej łączniki zostały dodane w kreatorze. Jeśli inny łącznik zostanie dodany za pomocą kreatora, reguły synchronizacji są zmieniane, a w każdej grupie są wstawiane reguły nowego łącznika.

### <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Teraz wiemy wystarczająco więcej o regułach synchronizacji, aby zrozumieć, jak konfiguracja działa z różnymi regułami synchronizacji. Jeśli zobaczysz użytkownika i atrybuty, które są współtworzone do Metaverse, reguły są stosowane w następującej kolejności:

| Nazwa | Komentarz |
|:--- |:--- |
| W programie z usługi AD — dołączanie użytkownika |Reguła sprzęgania obiektów przestrzeni łącznika z funkcją Metaverse. |
| W programie z usługi AD — kontoużytkownika |Atrybuty wymagane do logowania się do usługi Azure AD i Microsoft 365. Chcemy, aby te atrybuty zostały włączone. |
| W programie z usługi AD — wspólne dla użytkownika z programu Exchange |Atrybuty znalezione na globalnej liście adresowej. Przyjęto, że jakość danych jest Najlepsza w lesie, w którym znaleźliśmy skrzynkę pocztową użytkownika. |
| W programie z usługi AD — wspólne dla użytkowników |Atrybuty znalezione na globalnej liście adresowej. Na wypadek, gdyby nie znaleźć skrzynki pocztowej, każdy inny połączony obiekt może współtworzyć wartość atrybutu. |
| W programie z usługi AD — wymiana użytkownika |Istnieje tylko w przypadku wykrycia programu Exchange. Przepływa wszystkie atrybuty wymiany infrastruktury. |
| W programie z usługi AD — Lync użytkownika |Istnieje tylko w przypadku wykrycia programu Lync. Wszystkie atrybuty programu Lync infrastruktury są przepływane. |

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat modelu konfiguracji można znaleźć w temacie [Inicjowanie obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Przeczytaj więcej na temat języka wyrażeń w temacie [Opis wyrażeń aprowizacji deklaracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Kontynuuj odczytywanie, jak konfiguracja out-of-Box działa w [zrozumieniu użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md)
* Zobacz, jak wprowadzić praktyczną zmianę przy użyciu aprowizacji deklaracyjnej, [Aby wprowadzić zmianę konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md).

**Tematy dotyczące omówienia**

* [Azure AD Connect Sync: omówienie i dostosowanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

