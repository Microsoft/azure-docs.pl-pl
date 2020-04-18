---
title: Pojęcia dotyczące zarządzania usługami domenowymi usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak administrować domeną zarządzaną Usług domenowych Usługi Active Directory platformy Azure oraz zachowanie kont użytkowników i haseł
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: ba281ffb30801e0ae10cab10ceb95c0a3bffde2d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640021"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Pojęcia dotyczące zarządzania kontami użytkowników, hasłami i administracją w usługach domenowych Active Directory platformy Azure

Podczas tworzenia i uruchamiania domeny zarządzanej usług domenowych Active Directory platformy Azure (AD DS) istnieją pewne różnice w zachowaniu w porównaniu do tradycyjnego lokalnego środowiska usług AD DS. Te same narzędzia administracyjne w usługach Azure AD DS są używane jako domena zarządzana samodzielnie, ale nie można uzyskać bezpośredniego dostępu do kontrolerów domeny. Istnieją również pewne różnice w zachowaniu zasad haseł i skrótów haseł w zależności od źródła tworzenia konta użytkownika.

W tym artykule koncepcyjnym opisano sposób administrowania domeną zarządzaną usługą Azure AD DS i różne zachowanie kont użytkowników w zależności od sposobu ich tworzenia.

## <a name="domain-management"></a>Zarządzanie domenami

W usługach AD DS usługi Azure kontrolery domeny (KONTROLERY domeny), które zawierają wszystkie zasoby, takie jak użytkownicy i grupy, poświadczenia i zasady są częścią usługi zarządzanej. W przypadku nadmiarowości dwa kontrolery domeny są tworzone jako część domeny zarządzanej usług Ad DS platformy Azure. Nie można zalogować się do tych kontrolerów domeny do wykonywania zadań zarządzania. Zamiast tego należy utworzyć maszynę wirtualną zarządzania, która jest przyłączona do domeny zarządzanej usług Azure AD DS, a następnie zainstalować regularne narzędzia do zarządzania usługami AD DS. Można na przykład użyć przystawek Centrum administracyjnego usługi Active Directory lub programu Microsoft Management Console (MMC), takich jak obiekty DNS lub Zasady grupy.

## <a name="user-account-creation"></a>Tworzenie konta użytkownika

Konta użytkowników można tworzyć w usługach Azure AD DS na wiele sposobów. Większość kont użytkowników są synchronizowane z usługi Azure AD, która może również zawierać konto użytkownika zsynchronizowane z lokalnego środowiska usług AD DS. Można również ręcznie utworzyć konta bezpośrednio w usługach Azure AD DS. Niektóre funkcje, takie jak początkowa synchronizacja haseł lub zasady haseł, zachowują się inaczej w zależności od sposobu i miejsca tworzenia kont użytkowników.

* Konto użytkownika można zsynchronizować z usługi Azure AD. Obejmuje to konta użytkowników tylko w chmurze utworzone bezpośrednio w usłudze Azure AD i konta użytkowników hybrydowych zsynchronizowane z lokalnego środowiska usług AD DS przy użyciu usługi Azure AD Connect.
    * Większość kont użytkowników w usługach Azure AD DS są tworzone za pomocą procesu synchronizacji z usługi Azure AD.
* Konto użytkownika można ręcznie utworzyć w domenie zarządzanej usług Azure AD DS i nie istnieje w usłudze Azure AD.
    * Jeśli musisz utworzyć konta usługi dla aplikacji, które są uruchamiane tylko w usługach Azure AD DS, możesz ręcznie utworzyć je w domenie zarządzanej. Ponieważ synchronizacja jest jednym ze sposobów usługi Azure AD, konta użytkowników utworzone w usługach Azure AD DS nie są synchronizowane z powrotem do usługi Azure AD.

## <a name="password-policy"></a>Zasady haseł

Usługa Azure AD DS zawiera domyślną zasadę haseł, która definiuje ustawienia dla takich rzeczy, jak blokada konta, maksymalny wiek haseł i złożoność hasła. Ustawienia, takie jak zasady blokady konta mają zastosowanie do wszystkich użytkowników w usługach Azure AD DS, niezależnie od tego, jak użytkownik został utworzony zgodnie z opisem w poprzedniej sekcji. Kilka ustawień, takich jak minimalna długość hasła i złożoność hasła, mają zastosowanie tylko do użytkowników utworzonych bezpośrednio w usługach Azure AD DS.

Można utworzyć własne niestandardowe zasady haseł, aby zastąpić domyślne zasady w usługach Azure AD DS. Te zasady niestandardowe można następnie zastosować do określonych grup użytkowników w razie potrzeby.

Aby uzyskać więcej informacji na temat różnic w sposobie stosowania zasad haseł w zależności od źródła tworzenia użytkownika, zobacz [Zasady blokowania haseł i kont w domenach zarządzanych][password-policy].

## <a name="password-hashes"></a>Skróty haseł

Aby uwierzytelnić użytkowników w domenie zarządzanej, usługi Azure AD DS potrzebuje skrótów haseł w formacie odpowiednim dla programu NT LAN Manager (NTLM) i uwierzytelniania Kerberos. Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie włączysz usług Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD również nie przechowuje żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie wygenerować tych skrótów haseł NTLM lub Kerberos na podstawie istniejących poświadczeń użytkowników.

W przypadku kont użytkowników tylko w chmurze użytkownicy muszą zmienić swoje hasła, zanim będą mogli korzystać z usług Azure AD DS. Ten proces zmiany hasła powoduje, że skróty haseł dla uwierzytelniania Kerberos i NTLM mają być generowane i przechowywane w usłudze Azure AD. Konto nie jest synchronizowane z usługi Azure AD do usług Azure AD DS, dopóki hasło nie zostanie zmienione.

W przypadku użytkowników zsynchronizowanych z lokalnego środowiska usług AD DS przy użyciu usługi Azure AD Connect [należy włączyć synchronizację skrótów haseł][hybrid-phs].

> [!IMPORTANT]
> Usługa Azure AD Connect synchronizuje tylko starsze skróty haseł po włączeniu usług Azure AD DS dla dzierżawy usługi Azure AD. Starsze skróty haseł nie są używane, jeśli używasz tylko usługi Azure AD Connect do synchronizowania lokalnego środowiska usług AD DS z usługą Azure AD.
>
> Jeśli starsze aplikacje nie używają uwierzytelniania NTLM lub prostych powiązań LDAP, zaleca się wyłączenie synchronizacji skrótów haseł NTLM dla usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [Wyłączanie słabych zestawów szyfrowania i synchronizacji skrótów poświadczeń NTLM][secure-domain].

Po odpowiedniej konfiguracji skróty haseł, które można zastosować, są przechowywane w domenie zarządzanej usług Azure AD DS. Jeśli usuniesz domenę zarządzaną usługą Azure AD DS, wszystkie skróty haseł przechowywane w tym momencie są również usuwane. Zsynchronizowanych informacji o poświadczeniach w usłudze Azure AD nie można ponownie zużyć, jeśli później utworzysz domenę zarządzaną usługą Azure AD DS — należy ponownie skonfigurować synchronizację skrótów haseł, aby ponownie przechowywać skróty haseł. Wcześniej przyłączone do domeny maszyny wirtualne lub użytkownicy nie będą mogli natychmiast uwierzytelniać — usługa Azure AD musi generować i przechowywać skróty haseł w nowej domenie zarządzanej usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [Proces synchronizacji skrótów haseł dla usług Azure AD DS i usługi Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Usługa Azure AD Connect powinna być instalowana i konfigurowana tylko do synchronizacji z lokalnymi środowiskami usług AD DS. Nie jest obsługiwana instalacja usługi Azure AD Connect w domenie zarządzanej usług Azure AD DS w celu synchronizowania obiektów z powrotem do usługi Azure AD.

## <a name="forests-and-trusts"></a>Lasy i relacje zaufania

*Las* jest konstrukcją logiczną używaną przez Usługi domenowe Active Directory (AD DS) do grupowania jednej lub kilku *domen*. Domeny następnie przechowywać obiekty dla użytkowników lub grup i świadczenia usług uwierzytelniania.

W usłudze Azure AD DS las zawiera tylko jedną domenę. Lokalne lasy usług AD DS często zawierają wiele domen. W dużych organizacjach, zwłaszcza po fuzjach i przejęciach, może się to skończyć z wieloma lasami lokalnymi, z których każda zawiera wiele domen.

Domyślnie domena zarządzana usługą Azure AD DS jest tworzona jako las *użytkownika.* Ten typ lasu synchronizuje wszystkie obiekty z usługi Azure AD, w tym wszystkie konta użytkowników utworzone w lokalnym środowisku usług AD DS. Konta użytkowników można bezpośrednio uwierzytelniać względem domeny zarządzanej usług Azure AD DS, na przykład do logowania się do maszyny Wirtualnej przyłączone do domeny. Las użytkownika działa, gdy skróty haseł mogą być synchronizowane, a użytkownicy nie używają wyłącznych metod logowania, takich jak uwierzytelnianie za pomocą karty inteligentnej.

W lesie *zasobów usług* Azure AD DS użytkownicy uwierzytelniają się za pomocą jednokierunkowego *zaufania* lasu z lokalnych usług AD DS. Dzięki takiemu podejściu obiekty użytkownika i skróty haseł nie są synchronizowane z usługą Azure AD DS. Obiekty użytkownika i poświadczenia istnieją tylko w lokalnych usługach AD DS. Takie podejście umożliwia przedsiębiorstwom hostować zasoby i platformy aplikacji na platformie Azure, które zależą od uwierzytelniania klasycznego, takiego jak LDAPS, Kerberos lub NTLM, ale wszelkie problemy z uwierzytelnianiem lub problemy są usuwane. Lasy zasobów usług Azure AD DS są obecnie w wersji zapoznawczej.

Aby uzyskać więcej informacji na temat typów lasów w usługach Azure AD DS, zobacz Co [to][concepts-trust] [są lasy zasobów?][concepts-forest]

## <a name="azure-ad-ds-skus"></a>Jednostki SKU usługi Azure AD DS

W usłudze Azure AD DS dostępne wyniki i funkcje są oparte na jednostce SKU. Podczas tworzenia domeny zarządzanej wybierasz jednostkę SKU i można przełączać jednostki SKU, ponieważ wymagania biznesowe zmieniają się po wdrożeniu domeny zarządzanej. W poniższej tabeli przedstawiono dostępne jednostki SKU i różnice między nimi:

| Nazwa jednostki SKU   | Maksymalna liczba obiektów | Częstotliwość wykonywania kopii zapasowych | Maksymalna liczba wychodzących relacji zaufania lasu |
|------------|----------------------|------------------|----|
| Standardowa   | Nieograniczona liczba            | Co 7 dni     | 0  |
| Enterprise | Nieograniczona liczba            | Co 3 dni     | 5  |
| Premium    | Nieograniczona liczba            | Dzienna            | 10 |

Przed tymi jednostkami SKU usługi Azure AD DS użyto modelu rozliczeń opartego na liczbie obiektów (kont użytkowników i komputerów) w domenie zarządzanej usług Azure AD DS. Nie ma już zmiennych cen na podstawie liczby obiektów w domenie zarządzanej.

Aby uzyskać więcej informacji, zobacz [stronę cennika usług Usługi AD DS][pricing].

### <a name="managed-domain-performance"></a>Wydajność domeny zarządzanej

Wydajność domeny różni się w zależności od sposobu implementowania uwierzytelniania dla aplikacji. Dodatkowe zasoby obliczeniowe mogą pomóc w skróceniu czasu odpowiedzi na kwerendy i skróceniu czasu spędzonego na operacjach synchronizacji. Wraz ze wzrostem poziomu jednostki SKU zwiększa się zasoby obliczeniowe dostępne dla domeny zarządzanej. Monitoruj wydajność aplikacji i planuj wymagane zasoby.

Jeśli twoja firma lub aplikacja wymaga zmiany i potrzebujesz dodatkowej mocy obliczeniowej dla domeny zarządzanej usług Azure AD DS, możesz przełączyć się na inną jednostkę SKU.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Częstotliwość tworzenia kopii zapasowej określa, jak często jest pobierana migawka domeny zarządzanej. Kopie zapasowe to zautomatyzowany proces zarządzany przez platformę Azure. W przypadku wystąpienia problemu z domeną zarządzaną pomoc techniczna platformy Azure może pomóc w przywracaniu z kopii zapasowej. Ponieważ synchronizacja odbywa się tylko w jedną stronę *z* usługi Azure AD, wszelkie problemy w domenie zarządzanej usług Azure AD DS nie będą miały wpływu na usługi Azure AD lub lokalne środowiska i funkcje usług AD DS.

Wraz ze wzrostem poziomu jednostki SKU zwiększa się częstotliwość tych migawek kopii zapasowej. Przejrzyj wymagania biznesowe i cel punktu odzyskiwania (RPO), aby określić wymaganą częstotliwość tworzenia kopii zapasowych dla domeny zarządzanej. Jeśli wymagania firmy lub aplikacji ulegną zmianie i potrzebujesz częstszych kopii zapasowych, możesz przełączyć się na inną jednostkę SKU.

### <a name="outbound-forests"></a>Lasy wychodzące

W poprzedniej sekcji szczegółowe jednokierunkowe wychodzące relacje zaufania lasu z domeny zarządzanej usług Azure AD DS do lokalnego środowiska usług AD DS (obecnie w wersji zapoznawczej). Jednostka SKU określa maksymalną liczbę relacji zaufania lasu, które można utworzyć dla domeny zarządzanej usług Azure AD DS. Przejrzyj wymagania biznesowe i wymagania dotyczące aplikacji, aby określić, ile relacji zaufania jest rzeczywiście potrzebnych, i wybierz odpowiednią jednostkę SKU usługi Azure AD DS. Ponownie, jeśli wymagania biznesowe ulegną zmianie i trzeba utworzyć dodatkowe relacje zaufania lasu, można przełączyć się do innej jednostki SKU.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [utwórz domenę zarządzaną usługą Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
