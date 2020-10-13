---
title: Jak działają relacje zaufania dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się więcej o tym, jak działa zaufanie lasu Azure AD Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 50b400ffa047d3865a9df77912da187de1ce9cc9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962619"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Jak działają relacje zaufania dla lasów zasobów w Azure Active Directory Domain Services

Active Directory Domain Services (AD DS) zapewnia zabezpieczenia w wielu domenach lub lasach za pośrednictwem relacji zaufania domeny i lasu. Aby umożliwić uwierzytelnianie w ramach relacji zaufania, system Windows musi najpierw sprawdzić, czy domena zażądana przez użytkownika, komputer lub usługę ma relację zaufania z domeną żądającego konta.

Aby sprawdzić, czy ta relacja zaufania, system zabezpieczeń systemu Windows obliczy ścieżkę zaufania między kontrolerem domeny (DC) dla serwera, który odbiera żądanie i kontrolera domeny w domenie konta żądającego.

Mechanizmy kontroli dostępu zapewniane przez AD DS i model zabezpieczeń rozproszonych systemu Windows zapewniają środowisko dla operacji zaufania domen i lasów. Aby te zaufania działały prawidłowo, każdy zasób lub komputer musi mieć bezpośrednią ścieżkę zaufania do kontrolera domeny w domenie, w której się znajduje.

Ścieżka zaufania jest implementowana przez usługę logowania sieciowego przy użyciu uwierzytelnionego połączenia zdalnego wywołania procedury (RPC) z zaufanym urzędem domeny. Bezpieczny kanał rozciąga się również do innych domen AD DS za pośrednictwem relacji zaufania między domenami. Ten zabezpieczony kanał służy do uzyskiwania i weryfikowania informacji o zabezpieczeniach, w tym identyfikatorów zabezpieczeń (SID) dla użytkowników i grup.

Aby zapoznać się z omówieniem sposobu stosowania relacji zaufania do usługi Azure AD DS, zobacz temat [zagadnienia i funkcje lasu zasobów][create-forest-trust].

Aby rozpocząć korzystanie z relacji zaufania w usłudze Azure AD DS, [Utwórz domenę zarządzaną, która używa relacji zaufania lasu][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Przepływy relacji zaufania

Przepływ komunikacji zabezpieczonej przez relacje zaufania określa elastyczność zaufania. Sposób tworzenia lub konfigurowania zaufania decyduje o tym, jak daleko komunikacja mieści się w lasach lub między lasami.

Przepływ komunikacji za pośrednictwem relacji zaufania jest określany przez kierunek zaufania. Relacje zaufania mogą być jednokierunkowe lub dwukierunkowe i mogą być przechodnie lub nieprzechodnie.

Na poniższym diagramie pokazano, że domyślnie wszystkie domeny w *drzewie 1* i *2* mają przechodnie relacje zaufania. W związku z tym użytkownicy w *drzewie 1* mogą uzyskać dostęp do zasobów w domenach w *drzewie 2* , a użytkownicy w *drzewie 1* mogą uzyskać dostęp do zasobów w *drzewie 2*, gdy odpowiednie uprawnienia są przypisane do zasobu.

![Diagram relacji zaufania między dwoma lasami](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Jednokierunkowe i dwukierunkowe relacje zaufania

Relacje zaufania umożliwiają dostęp do zasobów, które mogą być jednokierunkowe lub dwukierunkowo.

Jednokierunkowe zaufanie jest jednokierunkową ścieżką uwierzytelniania utworzoną między dwiema domenami. W jednokierunkowe zaufanie między *domeną* a i *domeną B*użytkownicy w *domenie a* mogą uzyskać dostęp do zasobów w *domenie B*. Jednak użytkownicy w *domenie B* nie mogą uzyskać dostępu do zasobów w *domenie A*.

Niektóre zaufania jednokierunkowe mogą być nieprzechodnie lub przechodnie w zależności od typu tworzonego zaufania.

W dwukierunkowej relacji zaufania domena *a* ufa *domenie B* i *domena b* ufa *domenie a*. Ta konfiguracja oznacza, że żądania uwierzytelniania mogą być przesyłane między dwiema domenami w obu kierunkach. Niektóre relacje dwukierunkowe mogą być nieprzechodnie lub przechodnie w zależności od typu tworzonego zaufania.

Wszystkie relacje zaufania domen w lesie AD DS są dwukierunkowe, przechodnie zaufania. Po utworzeniu nowej domeny podrzędnej automatycznie tworzone jest dwukierunkowe zaufanie przechodnie między nową domeną podrzędną a domeną nadrzędną.

### <a name="transitive-and-non-transitive-trusts"></a>Zaufania przechodnie i nieprzechodnie

Przechodniość określa, czy można rozszerzyć zaufanie poza dwie domeny, z których zostało utworzone.

* Zaufania przechodniego można użyć do rozszerania relacji zaufania z innymi domenami.
* Zaufania nieprzechodniego można użyć do odmowy relacji zaufania z innymi domenami.

Za każdym razem, gdy tworzysz nową domenę w lesie, zostanie automatycznie utworzona Dwukierunkowa relacja zaufania przechodniego między nową domeną a jej domeną nadrzędną. Jeśli domeny podrzędne są dodawane do nowej domeny, ścieżka zaufania przepływa w górę przez hierarchię domeny, rozszerzając początkową ścieżkę zaufania utworzoną między nową domeną a jej domeną nadrzędną. Przechodnie relacje zaufania przepływają w górę przez drzewo domeny w miarę tworzenia, tworząc relacje zaufania przechodniego między wszystkimi domenami w drzewie domen.

Żądania uwierzytelniania są zgodne z tymi ścieżkami zaufania, więc konta z dowolnej domeny w lesie mogą być uwierzytelniane przez dowolną inną domenę w lesie. W przypadku logowania jednokrotnego konta z odpowiednimi uprawnieniami mogą uzyskiwać dostęp do zasobów w dowolnej domenie w lesie.

## <a name="forest-trusts"></a>Relacje zaufania lasów

Relacje zaufania lasów pomagają w zarządzaniu segmentacją AD DS infrastrukturą i obsługiwać dostęp do zasobów i innych obiektów w wielu lasach. Relacje zaufania lasów są przydatne w przypadku dostawców usług, firm, które przechodzą do fuzji lub przejęć, współpracy ekstranetów firmy i firm poszukujących nieautonomii administracyjnej.

Przy użyciu relacji zaufania lasów można połączyć dwa różne lasy, aby utworzyć jednokierunkowe lub dwukierunkową relację zaufania przechodniego. Zaufanie lasu pozwala administratorom połączyć dwa AD DS lasy z jedną relacją zaufania, aby zapewnić bezproblemową obsługę uwierzytelniania i autoryzacji w lasach.

Zaufanie lasu można utworzyć tylko między domeną główną lasu w jednym lesie i domeną katalogu głównego lasu w innym lesie. Relacje zaufania lasów mogą być tworzone tylko między dwoma lasami i nie można ich niejawnie rozszerzyć do trzeciego lasu. Takie zachowanie oznacza, że jeśli relacja zaufania lasów jest tworzona między *lasem 1* i *2*, a inne zaufanie lasu jest tworzone między *lasem 2* i *lasem 3*, *Las 1* nie ma niejawnego zaufania z *lasem 3*.

Na poniższym diagramie przedstawiono dwie oddzielne relacje zaufania lasów między trzema AD DS lasami w jednej organizacji.

![Diagram relacji zaufania lasów w ramach jednej organizacji](./media/concepts-forest-trust/forest-trusts-diagram.png)

Ta Przykładowa konfiguracja zapewnia następujący dostęp:

* Użytkownicy w *lesie 2* mogą uzyskać dostęp do zasobów w dowolnej domenie w *lesie 1* lub *3* .
* Użytkownicy w *lesie 3* mogą uzyskać dostęp do zasobów w dowolnej domenie w *lesie 2*
* Użytkownicy w *lesie 1* mogą uzyskać dostęp do zasobów w dowolnej domenie w *lesie 2*

Ta konfiguracja nie zezwala użytkownikom w *lesie 1* na dostęp do zasobów w *lesie 3* lub odwrotnie. Aby umożliwić użytkownikom w *lasach 1* i *3* udostępnianie zasobów, należy utworzyć dwukierunkowe zaufanie przechodnie między dwoma lasami.

W przypadku utworzenia jednokierunkowej relacji zaufania między dwoma lasami elementy członkowskie lasu zaufanego mogą korzystać z zasobów znajdujących się w lesie ufającym. Jednak zaufanie działa tylko w jednym kierunku.

Na przykład w przypadku utworzenia jednokierunkowe zaufania lasu między *lasem 1* (Las zaufany) i *lasu 2* (Las ufający):

* Członkowie *lasu 1* mogą uzyskać dostęp do zasobów znajdujących się w *lesie 2*.
* Członkowie *lasu 2* nie mogą uzyskać dostępu do zasobów znajdujących się w *lesie 1* przy użyciu tego samego zaufania.

> [!IMPORTANT]
> Las zasobów Azure AD Domain Services obsługuje jednokierunkowe zaufanie lasu do Active Directory lokalnych.

### <a name="forest-trust-requirements"></a>Wymagania zaufania lasów

Aby można było utworzyć relację zaufania lasu, należy sprawdzić, czy jest zainstalowana właściwa infrastruktura systemu nazw domen (DNS). Relacje zaufania lasów można utworzyć tylko wtedy, gdy jest dostępna jedna z następujących konfiguracji DNS:

* Pojedynczy główny serwer DNS jest głównym serwerem DNS dla obu przestrzeni nazw DNS lasu — Strefa główna zawiera delegowania dla każdej przestrzeni nazw DNS i główne wskazówki wszystkich serwerów DNS obejmują główny serwer DNS.
* Jeśli nie istnieje udostępniony główny serwer DNS i główne serwery DNS w każdym z nazw DNS lasu, użyj warunkowego przesyłania dalej DNS dla każdej przestrzeni nazw DNS, aby kierować zapytania dla nazw w innej przestrzeni nazw.

    > [!IMPORTANT]
    > Las zasobów Azure AD Domain Services musi korzystać z tej konfiguracji DNS. Hostowanie przestrzeni nazw DNS innej niż przestrzeń nazw DNS lasu zasobów nie jest funkcją Azure AD Domain Services. Usługa przesyłania dalej warunkowego jest poprawną konfiguracją.

* Jeśli nie istnieje udostępniony główny serwer DNS, a główne serwery DNS w każdym obszarze nazw DNS lasu są używane strefy pomocnicze DNS są konfigurowane w każdym obszarze nazw DNS, aby kierować zapytania dla nazw w innej przestrzeni nazw.

Aby utworzyć zaufanie lasu, musisz być członkiem grupy Administratorzy domeny (w domenie głównej lasu) lub grupy Administratorzy przedsiębiorstwa w Active Directory. Każdemu zaufania przypisano hasło, które muszą znać Administratorzy w obu lasach. Członkowie grupy Administratorzy przedsiębiorstwa w obu lasach mogą tworzyć relacje zaufania w obu lasach jednocześnie i w tym scenariuszu hasło, które jest kryptograficznie losowo, jest automatycznie generowane i zapisywane w obu lasach.

W Azure Portal zostanie utworzone zaufanie lasu wychodzącego dla Azure AD Domain Services. Nie utworzysz ręcznie relacji zaufania z domeną zarządzaną. Zaufanie lasu przychodzącego musi być skonfigurowane przez użytkownika z uprawnieniami wcześniej zanotowanymi w Active Directory lokalnym.

## <a name="trust-processes-and-interactions"></a>Zaufanie procesów i interakcji

Wiele transakcji międzydomenowych i między lasami zależy od relacji zaufania domeny lub lasu, aby można było wykonać różne zadania. W tej sekcji opisano procesy i interakcje, które występują w miarę uzyskiwania dostępu do zasobów między zaufaniami i odwołania do uwierzytelniania są oceniane.

### <a name="overview-of-authentication-referral-processing"></a>Omówienie przetwarzania odwołań do uwierzytelniania

Gdy żądanie uwierzytelnienia jest odwołaniem do domeny, kontroler domeny w tej domenie musi określić, czy istnieje relacja zaufania z domeną, z której pochodzi żądanie. Kierunek zaufania oraz to, czy relacja zaufania jest przechodnia, czy nieprzechodnia, musi również zostać określona przed uwierzytelnieniem użytkownika w celu uzyskania dostępu do zasobów w domenie. Proces uwierzytelniania występujący między domenami zaufanymi różni się w zależności od używanego protokołu uwierzytelniania. Protokoły Kerberos V5 i NTLM przetwarzają odwołania w celu uwierzytelniania w domenie inaczej

### <a name="kerberos-v5-referral-processing"></a>Przetwarzanie odwołań Kerberos V5

Protokół uwierzytelniania Kerberos V5 zależy od usługi logowania do sieci na kontrolerach domeny na potrzeby uwierzytelniania klientów i informacji o autoryzacji. Protokół Kerberos nawiązuje połączenie z usługą centrum dystrybucji kluczy online (KDC) i magazynem kont Active Directory dla biletów sesji.

Protokół Kerberos używa również relacji zaufania dla usług przyznawania biletów na wiele obszarów (TGS) i do weryfikowania certyfikatów atrybutów uprawnień (PACs) w zabezpieczonym kanale. Protokół Kerberos wykonuje uwierzytelnianie między obszarami tylko z obszarami Kerberos systemu operacyjnego nienależącymi do marki, takimi jak obszar MIT Kerberos i nie musi współdziałać z usługą logowanie do sieci.

Jeśli klient używa protokołu Kerberos V5 do uwierzytelniania, żąda biletu do serwera w domenie docelowej z kontrolera domeny w domenie konta. Centrum dystrybucji kluczy działa jako zaufany pośrednik między klientem a serwerem i udostępnia klucz sesji, który umożliwia obu stronom wzajemne uwierzytelnianie. Jeśli domena docelowa różni się od bieżącej domeny, centrum dystrybucji kluczy postępuje zgodnie z procesem logicznym w celu ustalenia, czy można odwołać się do żądania uwierzytelniania:

1. Czy bieżąca domena jest zaufana bezpośrednio przez domenę żądanego serwera?
    * Jeśli tak, Wyślij klientowi odwołanie do żądanej domeny.
    * Jeśli nie, przejdź do następnego kroku.

2. Czy istnieje relacja zaufania przechodniego między bieżącą domeną a następną domeną w ścieżce zaufania?
    * Jeśli tak, Wyślij odwołanie klienta do następnej domeny w ścieżce zaufania.
    * Jeśli nie, Wyślij klientowi komunikat o odmowie logowania.

### <a name="ntlm-referral-processing"></a>Przetwarzanie odwołań NTLM

Protokół uwierzytelniania NTLM zależy od usługi logowania do sieci na kontrolerach domeny na potrzeby uwierzytelniania klientów i informacji o autoryzacji. Ten protokół uwierzytelnia klientów, którzy nie korzystają z uwierzytelniania Kerberos. Protokół NTLM używa relacji zaufania do przekazywania żądań uwierzytelniania między domenami.

Jeśli klient używa uwierzytelniania NTLM, początkowe żądanie uwierzytelniania przechodzi bezpośrednio z klienta do serwera zasobów w domenie docelowej. Ten serwer tworzy wyzwanie, na które odpowiada klient. Serwer wysyła następnie odpowiedź użytkownika do kontrolera domeny w domenie kont komputera. Ten kontroler domeny sprawdza konto użytkownika w bazie danych kont zabezpieczeń.

Jeśli konto nie istnieje w bazie danych, kontroler domeny określa, czy przeprowadzać uwierzytelnianie przekazywane, przekazywać żądanie, czy odrzucać żądanie, korzystając z następującej logiki:

1. Czy bieżąca domena ma bezpośrednią relację zaufania z domeną użytkownika?
    * Jeśli tak, kontroler domeny wysyła poświadczenia klienta do kontrolera domeny w domenie użytkownika na potrzeby uwierzytelniania przekazywanego.
    * Jeśli nie, przejdź do następnego kroku.

2. Czy bieżąca domena ma przechodnią relację zaufania z domeną użytkownika?
    * Jeśli tak, Przekaż żądanie uwierzytelnienia do następnej domeny w ścieżce zaufania. Ten kontroler domeny powtarza proces, sprawdzając poświadczenia użytkownika względem własnej bazy danych kont zabezpieczeń.
    * Jeśli nie, Wyślij klientowi komunikat o odmowie logowania.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Przetwarzanie żądań uwierzytelniania opartych na protokole Kerberos przez zaufania lasów

Gdy dwa lasy są połączone zaufaniem lasu, żądania uwierzytelniania wysyłane przy użyciu protokołu Kerberos V5 lub NTLM mogą być kierowane między lasami w celu zapewnienia dostępu do zasobów w obu lasach.

Po pierwszym ustanowieniu zaufania lasu każdy Las zbiera wszystkie zaufane przestrzenie nazw w lesie partnerskim i zapisuje informacje w [obiekcie zaufanej domeny](#trusted-domain-object). Zaufane przestrzenie nazw obejmują nazwy drzewa domen, sufiksy głównej nazwy użytkownika (UPN), sufiksy głównej nazwy usługi (SPN) i przestrzenie nazw zabezpieczeń (SID) używane w innym lesie. Obiekty TDO są replikowane do wykazu globalnego.

Aby protokoły uwierzytelniania mogły wystąpić po ścieżce zaufania lasu, nazwa główna usługi (SPN) komputera zasobu musi zostać rozpoznana do lokalizacji w innym lesie. Nazwa SPN może być jedną z następujących nazw:

* Nazwa DNS hosta.
* Nazwa DNS domeny.
* Nazwa wyróżniająca obiektu punktu połączenia z usługą.

Gdy stacja robocza w jednym lesie próbuje uzyskać dostęp do danych na komputerze zasobów w innym lesie, proces uwierzytelniania protokołu Kerberos kontaktuje się z kontrolerem domeny w celu uzyskania biletu usługi do nazwy SPN komputera zasobu. Gdy kontroler domeny wyśle zapytanie do wykazu globalnego i ustali, że nazwa SPN nie znajduje się w tym samym lesie co kontroler domeny, kontroler domeny wysyła odwołanie do domeny nadrzędnej z powrotem do stacji roboczej. W tym momencie stacja robocza wysyła zapytanie do domeny nadrzędnej o bilet usługi i kontynuuje śledzenie odwołań do momentu, aż osiągnie domenę, w której znajduje się zasób.

Poniższy diagram i kroki zawierają szczegółowy opis procesu uwierzytelniania Kerberos, który jest używany, gdy komputery z systemem Windows próbują uzyskać dostęp do zasobów z komputera znajdującego się w innym lesie.

![Diagram procesu Kerberos w ramach zaufania lasu](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *Użytkownik1* logowanie do *Workstation1* przy użyciu poświadczeń z domeny *Europe.tailspintoys.com* . Użytkownik próbuje uzyskać dostęp do zasobu udostępnionego w *FileServer1* znajdującym się w lesie *USA.wingtiptoys.com* .

2. *Workstation1* kontaktuje się z centrum dystrybucji kluczy Kerberos na kontrolerze domeny w swojej domenie, *ChildDC1*i żąda biletu usługi dla *FileServer1* SPN.

3. *ChildDC1* nie znajduje nazwy SPN w swojej bazie danych domeny i wysyła zapytanie do wykazu globalnego, aby sprawdzić, czy Dowolna domena w lesie *tailspintoys.com* zawiera tę nazwę SPN. Ponieważ wykaz globalny jest ograniczony do własnego lasu, nie można odnaleźć nazwy SPN.

    Następnie wykaz globalny sprawdza swoją bazę danych w celu uzyskania informacji o relacjach zaufania lasów ustanowionych ze swoim lasem. Jeśli ta wartość zostanie znaleziona, porównuje sufiksy nazw wymienione w zaufanym obiekcie domeny zaufania lasu (TDO) do sufiksu docelowej nazwy SPN, aby znaleźć dopasowanie. Po znalezieniu dopasowania wykazu globalnego zapewnia wskazówkę routingu z powrotem do *ChildDC1*.

    Wskazówki dotyczące routingu pomagają skierować żądania uwierzytelniania do lasu docelowego. Wskazówki są używane tylko wtedy, gdy wszystkie tradycyjne kanały uwierzytelniania, takie jak lokalny kontroler domeny, a następnie wykaz globalny, nie można zlokalizować nazwy SPN.

4. *ChildDC1* wysyła odwołanie do domeny nadrzędnej z powrotem do *Workstation1*.

5. *Workstation1* kontaktuje się z kontrolerem domeny w *ForestRootDC1* (domeną nadrzędną) o odwołanie do kontrolera domeny (*ForestRootDC2*) w domenie głównej lasu lasu *wingtiptoys.com* .

6. *Workstation1* kontakty *ForestRootDC2* w lesie *wingtiptoys.com* w celu uzyskania biletu usługi do wymaganej usługi.

7. *ForestRootDC2* kontaktuje się z wykazem globalnym, aby znaleźć nazwę SPN, a wykaz globalny znajduje dopasowanie dla nazwy SPN i wysyła ją z powrotem do *ForestRootDC2*.

8. Następnie *ForestRootDC2* wysyła odwołanie do *USA.wingtiptoys.com* z powrotem do *Workstation1*.

9. *Workstation1* kontaktuje się z centrum dystrybucji kluczy w usłudze *ChildDC2* i negocjuje bilet dla programu *Użytkownik1* , aby uzyskać dostęp do usługi *FileServer1*.

10. Gdy *Workstation1* ma bilet usługi, wysyła bilet usługi do *FileServer1*, który odczytuje poświadczenia zabezpieczeń *Użytkownik1*i odpowiednio konstruuje token dostępu.

## <a name="trusted-domain-object"></a>Obiekt zaufanej domeny

Każde zaufanie domeny lub lasu w organizacji jest reprezentowane przez obiekt zaufanej domeny (TDO) przechowywany w kontenerze *system* w swojej domenie.

### <a name="tdo-contents"></a>Zawartość TDO

Informacje zawarte w obiekcie TDO różnią się w zależności od tego, czy obiekt TDO został utworzony przez zaufanie domeny, czy przez relację zaufania lasu.

Po utworzeniu zaufania domeny atrybuty takie jak nazwa domeny DNS, identyfikator SID domeny, typ zaufania, przechodni zaufania i nazwa domeny wzajemnej są reprezentowane w obiekcie TDO. Zaufanie lasu TDOs przechowywać dodatkowe atrybuty, aby identyfikować wszystkie zaufane przestrzenie nazw z lasu partnera. Te atrybuty obejmują nazwy drzewa domen, sufiksy głównej nazwy użytkownika (UPN), sufiksy głównej nazwy usługi (SPN) i przestrzenie nazw zabezpieczeń (SID).

Ponieważ relacje zaufania są przechowywane w Active Directory jako TDOs, wszystkie domeny w lesie mają znajomość relacji zaufania, które są stosowane w całym lesie. Podobnie, gdy dwa lub więcej lasów są sprzężone za pośrednictwem relacji zaufania lasu, domeny główne lasu w każdym lesie mają wiedzę na temat relacji zaufania, które są stosowane w całej domenie zaufanych lasów.

### <a name="tdo-password-changes"></a>Zmiany hasła TDO

Obie domeny w relacji zaufania współdzielą hasło, które jest przechowywane w obiekcie TDO w Active Directory. W ramach procesu konserwacji konta co 30 dni ufający kontroler domeny zmienia hasło przechowywane w obiekcie TDO. Ze względu na to, że relacje zaufania dwukierunkowego są faktycznie 2 1 zaufania między nimi, proces występuje dwa razy w przypadku relacji zaufania dwukierunkowego.

Zaufanie ma zaufanie zaufania i zaufanego. Po stronie zaufanej można użyć dowolnego zapisywalnego kontrolera domeny dla procesu. Po stronie ufającej emulator kontrolera PDC wykonuje zmianę hasła.

Aby zmienić hasło, kontrolery domeny wykonaj następujący proces:

1. Emulator podstawowego kontrolera domeny (PDC) w domenie ufającej tworzy nowe hasło. Kontroler domeny w domenie zaufanej nigdy nie inicjuje zmiany hasła. Jest on zawsze inicjowany przez Emulator PDC domeny ufającej.

2. Emulator kontrolera PDC w domenie ufającej ustawia pole *OldPassword* obiektu TDO na bieżące pole *NoweHasło* .

3. Emulator kontrolera PDC w domenie ufającej ustawia pole *NoweHasło* obiektu TDO na nowe hasło. Przechowywanie kopii poprzedniego hasła umożliwia przywrócenie starego hasła, Jeśli kontroler domeny w zaufanej domenie nie może odebrać zmiany lub jeśli zmiana nie zostanie zreplikowana przed wysłaniem żądania, które korzysta z nowego hasła zaufania.

4. Emulator kontrolera PDC w domenie ufającej nawiązuje zdalne wywołanie do kontrolera domeny w domenie zaufanej z prośbą o ustawienie hasła na koncie zaufania na nowe hasło.

5. Kontroler domeny w domenie zaufanej zmienia hasło zaufania na nowe hasło.

6. Po każdej stronie zaufania aktualizacje są replikowane do innych kontrolerów domeny w domenie. W domenie ufającej zmiana wyzwala pilną replikację obiektu domeny zaufanej.

Hasło jest teraz zmieniane na obu kontrolerach domeny. Replikacja zwykła dystrybuuje obiekty TDO do innych kontrolerów domeny w domenie. Jednak kontroler domeny w domenie ufającej może zmienić hasło bez pomyślnej aktualizacji kontrolera domeny w zaufanej domenie. Ten scenariusz może wystąpić, ponieważ bezpieczny kanał, który jest wymagany do przetwarzania zmiany hasła, nie może zostać ustanowiony. Istnieje również możliwość, że kontroler domeny w domenie zaufanej może być niedostępny w pewnym momencie procesu i może nie otrzymać zaktualizowanego hasła.

Aby zaradzić sobie z sytuacją, w której zmiana hasła nie powiodła się, kontroler domeny w domenie ufającej nigdy nie zmienia nowego hasła, chyba że zostanie pomyślnie uwierzytelniony (Skonfiguruj bezpieczny kanał) przy użyciu nowego hasła. To zachowanie powoduje, że stare i nowe hasła są przechowywane w obiekcie TDO domeny ufającej.

Zmiana hasła nie zostanie zakończona do momentu pomyślnego uwierzytelnienia przy użyciu hasła. Starego zapisanego hasła można użyć za pośrednictwem bezpiecznego kanału do momentu, aż kontroler domeny w domenie zaufanej odbierze nowe hasło, umożliwiając w ten sposób włączenie niezakłóconej usługi.

Jeśli uwierzytelnianie przy użyciu nowego hasła nie powiedzie się, ponieważ hasło jest nieprawidłowe, kontroler domeny ufającej próbuje przeprowadzić uwierzytelnienie przy użyciu starego hasła. W przypadku pomyślnego uwierzytelnienia przy użyciu starego hasła wznawia proces zmiany hasła w ciągu 15 minut.

Aktualizacje hasła zaufania muszą być replikowane do kontrolerów domeny obu stron relacji zaufania w ciągu 30 dni. Jeśli hasło zaufania zostanie zmienione po upływie 30 dni, a kontroler domeny ma tylko hasło N-2, nie może on używać zaufania po stronie ufającej i nie może utworzyć bezpiecznego kanału po stronie zaufanej.

## <a name="network-ports-used-by-trusts"></a>Porty sieciowe używane przez relacje zaufania

Ponieważ relacje zaufania muszą zostać wdrożone w różnych granicach sieci, mogą one obejmować jedną lub więcej zapór. W takim przypadku można albo tunelować ruch zaufania przez zaporę, albo otworzyć określone porty w zaporze, aby umożliwić przekazywanie ruchu.

> [!IMPORTANT]
> Active Directory Domain Services nie obsługuje ograniczania Active Directory ruchu RPC do określonych portów.

Przeczytaj sekcję dotyczącą **wersji systemu Windows Server 2008 i nowszych** w artykule pomoc techniczna firmy Microsoft [jak skonfigurować zaporę dla Active Directory domen i relacji zaufania](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) , aby dowiedzieć się więcej o portach wymaganych do zaufania lasu.

## <a name="supporting-services-and-tools"></a>Obsługa usług i narzędzi

Do obsługi relacji zaufania i uwierzytelniania są używane pewne dodatkowe funkcje i narzędzia do zarządzania.

### <a name="net-logon"></a>Logowanie do sieci

Usługa Logowanie do sieci utrzymuje zabezpieczony kanał z komputera z systemem Windows do kontrolera domeny. Jest on również używany w następujących procesach związanych z relacją zaufania:

* Konfiguracja i Zarządzanie zaufaniem — logowanie do sieci pomaga zachować hasła zaufania, gromadzić informacje o zaufaniu i sprawdzać relacje zaufania, współpracując z procesem LSA i domeną TDO.

    W przypadku relacji zaufania między lasami informacje zaufania zawierają rekord informacji zaufania lasu (*FTInfo*), który obejmuje zestaw przestrzeni nazw, które są zarządzane przez zaufanego lasu, z adnotacją z polem wskazującym, czy każde oświadczenie jest zaufane przez las ufający.

* Uwierzytelnianie — dostarcza poświadczenia użytkownika przez zabezpieczony kanał do kontrolera domeny i zwraca identyfikatory SID domeny i prawa użytkownika dla użytkownika.

* Lokalizacja kontrolera domeny — ułatwia znajdowanie lub lokalizowanie kontrolerów domeny w domenie lub w różnych domenach.

* Walidacja przekazująca — poświadczenia użytkowników w innych domenach są przetwarzane przez logowanie do sieci. Gdy Domena ufająca musi zweryfikować tożsamość użytkownika, przekazuje poświadczenia użytkownika za pomocą polecenia net Logon do domeny zaufanej w celu weryfikacji.

* Weryfikacja certyfikatu atrybutu uprawnień (PAC) — gdy serwer korzystający z protokołu Kerberos do uwierzytelniania musi zweryfikować certyfikat PAC w bilet usługi, wysyła certyfikat PAC w bezpiecznym kanale do kontrolera domeny w celu weryfikacji.

### <a name="local-security-authority"></a>Urząd zabezpieczeń lokalnych

Urząd zabezpieczeń lokalnych (LSA) to chroniony podsystem, który przechowuje informacje o wszystkich aspektach zabezpieczeń lokalnych w systemie. Zgodnie z zasadami zabezpieczeń lokalnych, urząd LSA udostępnia różne usługi do tłumaczenia między nazwami i identyfikatorami.

Podsystem zabezpieczeń LSA udostępnia usługi zarówno w trybie jądra, jak i w trybie użytkownika do sprawdzania poprawności dostępu do obiektów, sprawdzania uprawnień użytkowników i generowania komunikatów inspekcji. Urząd LSA jest odpowiedzialny za sprawdzenie poprawności wszystkich biletów sesji prezentowanych przez usługi w zaufanych lub niezaufanych domenach.

### <a name="management-tools"></a>Narzędzia do zarządzania

Administratorzy mogą używać *domen Active Directory i relacji zaufania*, *netdom* i *Nltest* do udostępniania, tworzenia, usuwania i modyfikowania relacji zaufania.

* *Active Directory domeny i relacje zaufania* to program Microsoft Management Console (MMC), który służy do administrowania zaufaniami domen, poziomów funkcjonalności domeny i lasu oraz sufiksów głównych nazw użytkowników.
* Narzędzia wiersza polecenia *netdom* i *Nltest* mogą służyć do znajdowania, wyświetlania, tworzenia i zarządzania relacjami zaufania. Te narzędzia komunikują się bezpośrednio z urzędem LSA na kontrolerze domeny.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o lasach zasobów, zobacz [jak działają relacje zaufania lasów na platformie Azure AD DS?][concepts-trust]

Aby rozpocząć tworzenie domeny zarządzanej z lasem zasobów, zobacz [Tworzenie i Konfigurowanie domeny zarządzanej AD DS platformy Azure][tutorial-create-advanced]. Następnie można [utworzyć zaufanie lasu wychodzącego do domeny lokalnej][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
