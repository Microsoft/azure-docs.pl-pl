---
title: Pojęcia dotyczące zarządzania dla Azure AD Domain Services | Microsoft Docs
description: Informacje na temat administrowania Azure Active Directory Domain Services domeną zarządzaną oraz zachowania kont użytkowników i haseł
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: justinha
ms.openlocfilehash: 76af477af8290634d2658c58574b8f96cff7b1fb
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620345"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Pojęcia dotyczące zarządzania kontami użytkowników, hasłami i administracją w programie Azure Active Directory Domain Services

Podczas tworzenia i uruchamiania domeny zarządzanej Azure Active Directory Domain Services (AD DS) istnieją pewne różnice w działaniu w porównaniu do tradycyjnego środowiska lokalnego AD DS. Używasz tych samych narzędzi administracyjnych na platformie Azure AD DS jako domeny samozarządzanej, ale nie możesz bezpośrednio uzyskać dostępu do kontrolerów domeny (DC). Istnieją także pewne różnice w zachowaniu zasad haseł i skrótów haseł w zależności od źródła tworzenia konta użytkownika.

W tym artykule koncepcyjnym szczegółowo opisano sposób administrowania domeną zarządzaną oraz inne zachowanie kont użytkowników w zależności od sposobu ich tworzenia.

## <a name="domain-management"></a>Zarządzanie domeną

Domena zarządzana jest przestrzenią nazw DNS i pasującym katalogiem. W domenie zarządzanej kontrolery domeny, które zawierają wszystkie zasoby, takie jak użytkownicy i grupy, poświadczenia i zasady, są częścią usługi zarządzanej. W przypadku nadmiarowości dwa kontrolery domeny są tworzone w ramach domeny zarządzanej. Nie można zalogować się do tych kontrolerów domeny w celu wykonywania zadań zarządzania. Zamiast tego należy utworzyć maszynę wirtualną zarządzania dołączoną do domeny zarządzanej, a następnie zainstalować regularne narzędzia do zarządzania AD DS. Możesz na przykład użyć przystawek Centrum administracyjne usługi Active Directory lub programu Microsoft Management Console (MMC), takich jak obiekty DNS lub zasady grupy.

## <a name="user-account-creation"></a>Tworzenie konta użytkownika

Konta użytkowników można tworzyć w domenie zarządzanej na wiele sposobów. Większość kont użytkowników jest synchronizowana z usługą Azure AD, która może również obejmować konto użytkownika zsynchronizowane z lokalnego środowiska AD DS. Możesz również ręcznie utworzyć konta bezpośrednio w domenie zarządzanej. Niektóre funkcje, takie jak wstępnej synchronizacji haseł lub zasad haseł, zachowują się inaczej w zależności od tego, jak i gdzie są tworzone konta użytkowników.

* Konto użytkownika można synchronizować z poziomu usługi Azure AD. Dotyczy to również kont użytkowników tylko w chmurze utworzonych bezpośrednio w usłudze Azure AD i hybrydowych kont użytkowników synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect.
    * Większość kont użytkowników w domenie zarządzanej jest tworzona za pośrednictwem procesu synchronizacji z usługi Azure AD.
* Konto użytkownika można utworzyć ręcznie w domenie zarządzanej i nie istnieje w usłudze Azure AD.
    * Jeśli musisz utworzyć konta usług dla aplikacji, które są uruchamiane tylko w domenie zarządzanej, możesz je utworzyć ręcznie w domenie zarządzanej. Ponieważ synchronizacja jest jednym ze sposobów z usługi Azure AD, konta użytkowników utworzone w domenie zarządzanej nie są synchronizowane z powrotem do usługi Azure AD.

## <a name="password-policy"></a>Zasady dotyczące haseł

Usługa Azure AD DS obejmuje domyślne zasady haseł, które definiują ustawienia dla elementów, takich jak blokada konta, maksymalny wiek hasła i złożoność hasła. Ustawienia, takie jak zasady blokady konta, mają zastosowanie do wszystkich użytkowników w domenie zarządzanej, niezależnie od sposobu utworzenia użytkownika, jak opisano w poprzedniej sekcji. Niektóre ustawienia, takie jak minimalna długość hasła i złożoność hasła, mają zastosowanie tylko do użytkowników utworzonych bezpośrednio w domenie zarządzanej.

Możesz utworzyć własne niestandardowe zasady haseł, aby zastąpić zasady domyślne w domenie zarządzanej. Te zasady niestandardowe można następnie zastosować do określonych grup użytkowników zgodnie z wymaganiami.

Aby uzyskać więcej informacji na temat różnic w sposobie stosowania zasad haseł w zależności od źródła tworzenia użytkowników, zobacz [zasady blokowania haseł i kont w domenach zarządzanych][password-policy].

## <a name="password-hashes"></a>Skróty haseł

Aby można było uwierzytelniać użytkowników w domenie zarządzanej, usługa Azure AD DS musi używać skrótów haseł w formacie, który jest odpowiedni dla protokołu NT LAN Manager (NTLM) i uwierzytelniania Kerberos. Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie zostanie włączona usługa Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie generować tych skrótów uwierzytelniania NTLM lub Kerberos w oparciu o istniejące poświadczenia użytkownika.

W przypadku kont użytkowników tylko w chmurze użytkownicy muszą zmienić swoje hasła, zanim będą mogli korzystać z domeny zarządzanej. Ten proces zmiany haseł powoduje generowanie w usłudze Azure AD skrótów haseł dla uwierzytelniania Kerberos i NTLM. Konto nie jest synchronizowane z usługą Azure AD do usługi Azure AD DS, dopóki hasło nie zostanie zmienione.

W przypadku użytkowników synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect, należy [włączyć synchronizację skrótów haseł][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synchronizuje tylko starsze skróty haseł po włączeniu AD DS platformy Azure dla dzierżawy usługi Azure AD. Starsze skróty haseł nie są używane, jeśli Azure AD Connect do synchronizowania środowiska lokalnego AD DS z usługą Azure AD.
>
> Jeśli starsze aplikacje nie korzystają z uwierzytelniania NTLM lub prostych powiązań LDAP, zalecamy wyłączenie synchronizacji skrótów haseł NTLM dla AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [wyłączanie słabych mechanizmów szyfrowania i synchronizacji skrótów poświadczeń NTLM][secure-domain].

Po odpowiednim skonfigurowaniu skróty do przydatnych haseł są przechowywane w domenie zarządzanej. Jeśli usuniesz domenę zarządzaną, wszystkie skróty haseł przechowywane w tym punkcie również zostaną usunięte. Nie można ponownie użyć synchronizowanych informacji poświadczeń w usłudze Azure AD, jeśli później utworzysz kolejną domenę zarządzaną — musisz zmienić konfigurację synchronizacji skrótów haseł w celu ponownego przechowywania skrótów haseł. Wcześniej przyłączone do domeny maszyny wirtualne lub użytkownicy nie będą mogli od razu przeprowadzić uwierzytelniania — usługa Azure AD musi generować i przechowywać skróty haseł w nowej domenie zarządzanej. Aby uzyskać więcej informacji, zobacz [proces synchronizacji skrótów haseł dla platformy Azure AD DS i Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Nie jest obsługiwane Instalowanie Azure AD Connect w domenie zarządzanej w celu synchronizacji obiektów z powrotem do usługi Azure AD.

## <a name="forests-and-trusts"></a>Lasy i relacje zaufania

*Las* to logiczna konstrukcja używana przez Active Directory Domain Services (AD DS) do grupowania jednej lub wielu *domen*. Domeny następnie przechowują obiekty dla użytkowników lub grup i zapewniają usługi uwierzytelniania.

W usłudze Azure AD DS Las zawiera tylko jedną domenę. Lokalne lasy AD DS często zawierają wiele domen. W dużych organizacjach, szczególnie po fuzjach i przejęciach, może się okazać, że istnieje wiele lasów lokalnych, które każdy z nich będzie zawierać wiele domen.

Domyślnie domena zarządzana jest tworzona jako Las *użytkownika* . Ten typ lasu służy do synchronizowania wszystkich obiektów z usługi Azure AD, w tym wszystkich kont użytkowników utworzonych w środowisku lokalnym AD DS. Konta użytkowników mogą być uwierzytelniane bezpośrednio względem domeny zarządzanej, na przykład w celu zalogowania się do maszyny wirtualnej przyłączonej do domeny. Las użytkownika działa, gdy można synchronizować skróty haseł, a użytkownicy nie korzystają z takich samych metod logowania, jak uwierzytelnianie karty inteligentnej.

W lesie *zasobów* AD DS platformy Azure użytkownicy uwierzytelniają się za pośrednictwem jednokierunkowego *zaufania* lasu z AD DS lokalnych. W tym podejściu skróty do obiektów użytkownika i haseł nie są synchronizowane z usługą Azure AD DS. Obiekty użytkownika i poświadczenia znajdują się tylko w AD DS lokalnej. Takie podejście umożliwia przedsiębiorstwom hostowanie zasobów i platform aplikacji na platformie Azure, które zależą od klasycznego uwierzytelniania, takich jak LDAPs, Kerberos lub NTLM, ale wszelkie problemy z uwierzytelnianiem lub wątpliwości są usuwane.

Aby uzyskać więcej informacji na temat typów lasów w usłudze Azure AD DS, zobacz [co to są lasy zasobów?][concepts-forest] czy [relacje zaufania lasów działają w usłudze Azure AD DS?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Jednostki SKU AD DS platformy Azure

Na platformie Azure AD DS dostępna wydajność i funkcje są oparte na jednostkach SKU. Podczas tworzenia domeny zarządzanej można wybrać jednostkę SKU i zmienić jej wymagania biznesowe po wdrożeniu domeny zarządzanej. W poniższej tabeli przedstawiono dostępne jednostki SKU i różnice między nimi:

| Nazwa jednostki SKU   | Maksymalna liczba obiektów | Częstotliwość wykonywania kopii zapasowych | Maksymalna liczba wychodzących relacji zaufania lasów |
|------------|----------------------|------------------|----|
| Standardowa   | Nieograniczona liczba            | Co 7 dni     | 0  |
| Enterprise | Nieograniczona liczba            | Co 3 dni     | 5  |
| Premium    | Nieograniczona liczba            | Codziennie            | 10 |

Przed rozpoczęciem korzystania z tych jednostek SKU AD DS platformy Azure zostanie użyty model rozliczeń oparty na liczbie obiektów (konta użytkowników i komputerów) w domenie zarządzanej. Nie ma już zmiennych cenowych na podstawie liczby obiektów w domenie zarządzanej.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Wydajność domeny zarządzanej

Wydajność domeny zależy od sposobu implementacji uwierzytelniania dla aplikacji. Dodatkowe zasoby obliczeniowe mogą pomóc poprawić czas odpowiedzi kwerendy i skrócić czas spędzony na operacjach synchronizacji. Po zwiększeniu poziomu jednostki SKU zasoby obliczeniowe dostępne dla domeny zarządzanej są zwiększane. Monitorowanie wydajności aplikacji i planowanie wymaganych zasobów.

Jeśli Twoja firma lub aplikacja wymaga zmiany i potrzebujesz dodatkowej mocy obliczeniowej dla domeny zarządzanej, możesz przełączyć się do innej jednostki SKU.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Częstotliwość tworzenia kopii zapasowych określa, jak często jest wykonywana migawka domeny zarządzanej. Kopie zapasowe są zautomatyzowanymi procesami zarządzanymi przez platformę Azure. W przypadku problemu z domeną zarządzaną pomoc techniczna systemu Azure może pomóc w przywracaniu z kopii zapasowej. Ponieważ synchronizacja odbywa się tylko jeden sposób *z* usługi Azure AD, wszelkie problemy w domenie zarządzanej nie wpłyną na usługę Azure AD ani lokalne środowiska i funkcjonalność AD DS.

W miarę wzrostu poziomu jednostki SKU częstotliwość tych migawek kopii zapasowych rośnie. Przejrzyj wymagania biznesowe i cel punktu odzyskiwania (RPO), aby określić wymaganą częstotliwość tworzenia kopii zapasowych dla domeny zarządzanej. Jeśli wymagania biznesowe lub dotyczące aplikacji są zmieniane i potrzebne są kopie zapasowe, możesz przełączyć się do innej jednostki SKU.

### <a name="outbound-forest-trusts"></a>Wychodzące zaufania lasów

W poprzedniej sekcji szczegółowo opisano jednokierunkowe zaufanie lasu wychodzącego z domeny zarządzanej do środowiska lokalnego AD DS. Jednostka SKU określa maksymalną liczbę relacji zaufania lasu, które można utworzyć dla domeny zarządzanej. Zapoznaj się z wymaganiami biznesowymi i aplikacjami, aby określić, ile relacji zaufania rzeczywiście potrzebujesz, i wybierz odpowiednią jednostkę SKU platformy Azure AD DS. Ponownie, jeśli wymagania biznesowe zmienią się i konieczne jest utworzenie dodatkowych relacji zaufania lasu, możesz przełączyć się do innej jednostki SKU.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [Utwórz domenę zarządzaną platformy Azure AD DS][create-instance].

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
