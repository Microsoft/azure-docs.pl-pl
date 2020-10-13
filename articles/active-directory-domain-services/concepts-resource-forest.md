---
title: Pojęcia dotyczące lasu zasobów dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, w jaki sposób Las zasobów jest w Azure Active Directory Domain Services i jak korzyści dla organizacji w środowisku hybrydowym z ograniczonymi opcjami uwierzytelniania użytkowników lub problemami z bezpieczeństwem.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 88069c6ac90fce7e33cac6ed3c30accb18ded8bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396403"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Koncepcje i funkcje lasu zasobów dla Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) oferuje środowisko logowania dla starszych, lokalnych aplikacji biznesowych. Użytkownicy, grupy i skróty haseł użytkowników lokalnych i w chmurze są synchronizowane z domeną zarządzaną platformy Azure AD DS. Te zsynchronizowane skróty haseł udostępniają użytkownikom pojedynczy zestaw poświadczeń, które mogą być używane do lokalnego AD DS, Microsoft 365 i Azure Active Directory.

Chociaż bezpieczeństwo i zapewnia dodatkowe korzyści z zabezpieczeń, niektóre organizacje nie mogą synchronizować tych skrótów haseł użytkowników z usługą Azure AD lub Azure AD DS. Użytkownicy w organizacji mogą nie znać hasła, ponieważ korzystają tylko z uwierzytelniania za pomocą kart inteligentnych. Te ograniczenia uniemożliwiają niektórym organizacjom korzystanie z usługi Azure AD DS w celu podnoszenia i przesunięcia lokalnych aplikacji klasycznych na platformę Azure.

Aby rozwiązać te wymagania i ograniczenia, można utworzyć domenę zarządzaną, która używa lasu zasobów. W tym artykule opisano, jakie lasy są i jak ufają innym zasobom w celu zapewnienia bezpiecznej metody uwierzytelniania.

## <a name="what-are-forests"></a>Co to są lasy?

*Las* to logiczna konstrukcja używana przez Active Directory Domain Services (AD DS) do grupowania jednej lub wielu *domen*. Domeny następnie przechowują obiekty dla użytkowników lub grup i zapewniają usługi uwierzytelniania.

W domenie zarządzanej AD DS platformy Azure Las zawiera tylko jedną domenę. Lokalne lasy AD DS często zawierają wiele domen. W dużych organizacjach, szczególnie po fuzjach i przejęciach, może się okazać, że istnieje wiele lasów lokalnych, które każdy z nich będzie zawierać wiele domen.

Domyślnie domena zarządzana jest tworzona jako Las *użytkownika* . Ten typ lasu służy do synchronizowania wszystkich obiektów z usługi Azure AD, w tym wszystkich kont użytkowników utworzonych w środowisku lokalnym AD DS. Konta użytkowników mogą być uwierzytelniane bezpośrednio względem domeny zarządzanej, na przykład w celu zalogowania się do maszyny wirtualnej przyłączonej do domeny. Las użytkownika działa, gdy można synchronizować skróty haseł, a użytkownicy nie korzystają z takich samych metod logowania, jak uwierzytelnianie karty inteligentnej.

W lesie *zasobów* domeny zarządzanej użytkownicy uwierzytelniają się za pośrednictwem jednokierunkowego *zaufania* lasu od ich AD DS lokalnych. W tym podejściu skróty do obiektów użytkownika i haseł nie są synchronizowane z domeną zarządzaną. Obiekty użytkownika i poświadczenia znajdują się tylko w AD DS lokalnej. Takie podejście umożliwia przedsiębiorstwom hostowanie zasobów i platform aplikacji na platformie Azure, które zależą od klasycznego uwierzytelniania, takich jak LDAPs, Kerberos lub NTLM, ale wszelkie problemy z uwierzytelnianiem lub wątpliwości są usuwane.

Lasy zasobów zapewniają również możliwość podnoszenia i przenoszenia aplikacji po jednym składniku jednocześnie. Wiele starszych aplikacji lokalnych jest w wielu warstwach, często przy użyciu serwera sieci Web lub frontonu i wielu składników związanych z bazą danych. Te warstwy sprawiają, że trudno jest podnieść i przenieść całą aplikację do chmury w jednym kroku. W przypadku lasów zasobów można podnieść swoją aplikację do chmury w podejściu fazowym, co ułatwia przenoszenie aplikacji na platformę Azure.

## <a name="what-are-trusts"></a>Co to są relacje zaufania?

Organizacje, które mają więcej niż jedną domenę, często potrzebują użytkownikom dostępu do udostępnionych zasobów w innej domenie. Dostęp do tych udostępnionych zasobów wymaga, aby użytkownicy w jednej domenie uwierzytelniali się w innej domenie. Aby zapewnić te możliwości uwierzytelniania i autoryzacji między klientami i serwerami w różnych domenach, między tymi dwiema domenami musi istnieć *relacja zaufania* .

W przypadku relacji zaufania z domeną mechanizmy uwierzytelniania dla każdej domeny ufają uwierzytelnianiu pochodzącemu z innej domeny. Relacje zaufania pomagają zapewnić kontrolowany dostęp do udostępnionych zasobów w domenie zasobów (domenie *ufającej* ) przez zweryfikowanie, że przychodzące żądania uwierzytelniania pochodzą z zaufanego urzędu ( *zaufanej* domeny). Relacje zaufania działają jako mosty, które umożliwiają tylko zweryfikowane żądania uwierzytelnienia, aby poruszać się między domenami.

Sposób, w jaki zaufanie przekazuje żądania uwierzytelniania, zależy od tego, w jaki sposób jest skonfigurowany. Relacje zaufania można skonfigurować w jeden z następujących sposobów:

* **Jednokierunkowe — zapewnia** dostęp z zaufanej domeny do zasobów w domenie ufającej.
* **Dwukierunkowa** — zapewnia dostęp z poszczególnych domen do zasobów w innej domenie.

Relacje zaufania są również skonfigurowane do obsługi dodatkowych relacji zaufania w jeden z następujących sposobów:

* **Nieprzechodnie** — zaufanie istnieje tylko między dwiema domenami partnerskimi partnera.
* **Przechodnie** — zaufanie jest automatycznie rozszerzane do wszystkich innych domen, które ufają partnerom.

W niektórych przypadkach relacje zaufania są automatycznie ustanawiane podczas tworzenia domen. Czasami musisz wybrać typ zaufania i jawnie ustanowić odpowiednie relacje. Określone typy relacji zaufania i struktura tych relacji zaufania zależą od tego, jak katalog AD DS jest zorganizowany i czy różne wersje systemu Windows współdziałają w sieci.

## <a name="trusts-between-two-forests"></a>Relacje zaufania między dwoma lasami

Zaufania domen można rozciągnąć w jednym lesie do innego lasu przez ręczne utworzenie jednokierunkowe lub dwukierunkowej relacji zaufania między lasami. Zaufanie lasu jest zaufaniem przechodnim, które istnieje tylko między domeną główną lasu a drugą domeną główną lasu.

* Jednokierunkowe zaufanie lasu pozwala wszystkim użytkownikom w jednym lesie ufać wszystkim domenom w innym lesie.
* Zaufanie lasu dwukierunkowego tworzy przechodnią relację zaufania między każdą domeną w obu lasach.

Przechodnie relacje zaufania lasów są ograniczone do dwóch partnerów lasu. Zaufanie lasu nie obejmuje dodatkowych lasów zaufanych przez jednego z partnerów.

![Diagram zaufania lasów z usługi Azure AD DS do lokalnego AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

W zależności od struktury AD DS organizacji można utworzyć różne konfiguracje zaufania domen i lasów. Usługa Azure AD DS obsługuje tylko jednokierunkowe zaufanie lasu. W tej konfiguracji zasoby w domenie zarządzanej mogą ufać wszystkim domenom w lesie lokalnym.

## <a name="supporting-technology-for-trusts"></a>Obsługa technologii zaufania

Relacje zaufania wykorzystują różne usługi i funkcje, takie jak usługa DNS do lokalizowania kontrolerów domeny w lasach partnerskich. Relacje zaufania są również zależne od protokołów uwierzytelniania NTLM i Kerberos oraz mechanizmów autoryzacji i kontroli dostępu opartej na systemie Windows w celu zapewnienia bezpiecznej infrastruktury komunikacyjnej w AD DS domenach i lasach. Poniższe usługi i funkcje pomagają zapewnić pomyślne relacje zaufania.

### <a name="dns"></a>DNS

AD DS wymaga systemu DNS dla lokalizacji kontrolera domeny (DC) i nazewnictwa. Do pomyślnego działania AD DS podano pomoc techniczną z usługi DNS:

* Usługa rozpoznawania nazw, która umożliwia hostom i usługom sieciowym lokalizowanie kontrolerów domen.
* Struktura nazewnictwa, która pozwala przedsiębiorstwom odzwierciedlać swoją strukturę organizacyjną w nazwach domen usługi katalogowej.

Przestrzeń nazw domeny DNS jest zwykle wdrażana, która odzwierciedla obszar nazw domeny AD DS. Jeśli istnieje istniejąca przestrzeń nazw DNS przed wdrożeniem AD DS, przestrzeń nazw DNS jest zwykle partycjonowana dla AD DS, a poddomena DNS i delegowanie dla katalogu głównego lasu AD DS. Dodatkowe nazwy domen DNS są następnie dodawane dla każdej AD DS domeny podrzędnej.

Serwer DNS jest również używany do obsługi lokalizacji AD DS kontrolerami domeny. Strefy DNS są wypełniane rekordami zasobów DNS, które umożliwiają hostom i usługom sieciowym lokalizowanie AD DS kontrolerów domeny.

### <a name="applications-and-net-logon"></a>Aplikacje i logowanie do sieci

Obie aplikacje i usługa Logowanie do sieci są składnikami modelu kanału zabezpieczeń rozproszonych systemu Windows. Aplikacje zintegrowane z systemem Windows Server i AD DS używają protokołów uwierzytelniania do komunikowania się z usługą logowanie do sieci, aby można było ustalić bezpieczną ścieżkę, w której może wystąpić uwierzytelnianie.

### <a name="authentication-protocols"></a>Protokoły uwierzytelniania

AD DS kontrolery domeny uwierzytelniają użytkowników i aplikacje przy użyciu jednego z następujących protokołów:

* **Protokół uwierzytelniania Kerberos w wersji 5**
    * Protokół Kerberos w wersji 5 jest domyślnym protokołem uwierzytelniania używanym przez lokalne komputery z systemem Windows i obsługujący systemy operacyjne innych firm. Ten protokół jest określony w dokumencie RFC 1510 i jest w pełni zintegrowany z AD DS, bloku komunikatów serwera (SMB), protokołu HTTP i zdalnego wywołania procedury (RPC), a także aplikacji klienta i serwera, które używają tych protokołów.
    * Gdy używany jest protokół Kerberos, serwer nie musi skontaktować się z kontrolerem domeny. Zamiast tego klient otrzymuje bilet dla serwera, żądając jednego z kontrolerów domeny w domenie konta serwera. Następnie serwer sprawdza poprawność biletu bez konsultacji z żadnym innym Urzędem.
    * Jeśli którykolwiek z komputerów należących do transakcji nie obsługuje protokołu Kerberos w wersji 5, używany jest protokół NTLM.

* **Protokół uwierzytelniania NTLM**
    * Protokół NTLM to klasyczny protokół uwierzytelniania sieciowego używany przez starsze systemy operacyjne. Ze względów zgodności są używane przez domeny AD DS do przetwarzania żądań uwierzytelniania sieciowego, które pochodzą z aplikacji przeznaczonych dla starszych klientów i serwerów z systemem Windows, oraz systemów operacyjnych innych firm.
    * Gdy protokół NTLM jest używany między klientem a serwerem, serwer musi skontaktować się z usługą uwierzytelniania domeny na kontrolerze domeny, aby zweryfikować poświadczenia klienta. Serwer uwierzytelnia klienta, przekazując poświadczenia klienta do kontrolera domeny w domenie konta klienta.
    * Gdy dwie AD DS domeny lub lasy są połączone z zaufaniem, żądania uwierzytelniania wykonywane przy użyciu tych protokołów mogą być kierowane w celu zapewnienia dostępu do zasobów w obu lasach.

## <a name="authorization-and-access-control"></a>Autoryzacja i kontrola dostępu

Technologie autoryzacji i zaufania współpracują ze sobą w celu zapewnienia bezpiecznej infrastruktury komunikacyjnej w AD DS domenach lub lasach. Autoryzacja określa poziom dostępu użytkownika do zasobów w domenie. Relacje zaufania ułatwiają użytkownikom autoryzację między domenami, zapewniając ścieżkę do uwierzytelniania użytkowników w innych domenach, dzięki czemu ich żądania do udostępnionych zasobów w tych domenach mogą być autoryzowane.

W przypadku zweryfikowania żądania uwierzytelnienia w domenie ufającej przez domenę zaufaną zostanie ona przeniesiona do zasobu docelowego. Następnie zasób docelowy decyduje o tym, czy należy autoryzować określone żądanie wykonywane przez użytkownika, usługę lub komputer w zaufanej domenie w oparciu o jego konfigurację kontroli dostępu.

Relacje zaufania zapewniają ten mechanizm do weryfikowania żądań uwierzytelniania, które są przesyłane do domeny ufającej. Mechanizmy kontroli dostępu na komputerze zasobów określają końcowy poziom dostępu przyznany do obiektu żądającego w zaufanej domenie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat relacji zaufania, zobacz [jak działają relacje zaufania lasów na platformie Azure AD DS?][concepts-trust]

Aby rozpocząć tworzenie domeny zarządzanej z lasem zasobów, zobacz [Tworzenie i Konfigurowanie domeny zarządzanej AD DS platformy Azure][tutorial-create-advanced]. Następnie można [utworzyć zaufanie lasu wychodzącego do domeny lokalnej][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
