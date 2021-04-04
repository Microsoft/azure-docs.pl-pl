---
title: Tworzenie i Używanie zasad haseł w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak i dlaczego należy używać szczegółowych zasad haseł do zabezpieczania i kontrolowania haseł kont w domenie zarządzanej AD DS platformy Azure.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619236"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Zasady blokowania haseł i kont w Azure Active Directory Domain Services domenach zarządzanych

Aby zarządzać zabezpieczeniami użytkowników w Azure Active Directory Domain Services (Azure AD DS), można zdefiniować szczegółowe zasady haseł kontrolujące ustawienia blokady konta lub minimalną długość i złożoność hasła. Domyślne szczegółowe zasady haseł są tworzone i stosowane dla wszystkich użytkowników w domenie zarządzanej AD DS platformy Azure. Aby zapewnić szczegółową kontrolę i spełnić określone potrzeby biznesowe lub dotyczące zgodności, można utworzyć dodatkowe zasady i zastosować je do określonych grup użytkowników.

W tym artykule opisano sposób tworzenia i konfigurowania szczegółowych zasad haseł na platformie Azure AD DS przy użyciu Centrum administracyjne usługi Active Directory.

> [!NOTE]
> Zasady dotyczące haseł są dostępne tylko dla domen zarządzanych utworzonych przy użyciu modelu wdrażania Menedżer zasobów. W przypadku starszych domen zarządzanych utworzonych przy użyciu klasycznego, należy [przeprowadzić migrację z klasycznego modelu sieci wirtualnej do Menedżer zasobów][migrate-from-classic].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
  * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
  * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
  * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
  * Domena zarządzana musi zostać utworzona przy użyciu Menedżer zasobów model wdrażania. W razie potrzeby [Przeprowadź migrację z klasycznego modelu sieci wirtualnej do Menedżer zasobów][migrate-from-classic].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest przyłączona do domeny zarządzanej.
  * W razie potrzeby Ukończ samouczek, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="default-password-policy-settings"></a>Domyślne ustawienia zasad haseł

Szczegółowe zasady haseł (FGPPs) pozwalają stosować określone ograniczenia dotyczące zasad blokowania haseł i kont dla różnych użytkowników w domenie. Na przykład aby zabezpieczyć konta uprzywilejowane, można zastosować bardziej rygorystyczne ustawienia blokady konta niż regularne konta bez uprawnień. Można utworzyć wiele FGPPs w domenie zarządzanej i określić kolejność ich stosowania dla użytkowników.

Aby uzyskać więcej informacji na temat zasad haseł i korzystania z centrum administracyjnego Active Directory, zobacz następujące artykuły:

* [Więcej informacji na temat szczegółowych zasad haseł](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurowanie szczegółowych zasad haseł przy użyciu Centrum administracyjne usługi AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

Zasady są dystrybuowane za pomocą skojarzenia grupy w domenie zarządzanej, a wszelkie wprowadzone zmiany są stosowane podczas następnego logowania użytkownika. Zmiana zasad nie powoduje odblokowania konta użytkownika, które jest już zablokowane.

Zasady haseł działają nieco inaczej w zależności od tego, jak konto użytkownika, do którego są stosowane, zostało utworzone. Istnieją dwa sposoby tworzenia konta użytkownika w usłudze Azure AD DS:

* Konto użytkownika można synchronizować z poziomu usługi Azure AD. Dotyczy to również kont użytkowników tylko w chmurze utworzonych bezpośrednio na platformie Azure i hybrydowych kont użytkowników synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect.
    * Większość kont użytkowników w usłudze Azure AD DS jest tworzona za pośrednictwem procesu synchronizacji w usłudze Azure AD.
* Konto użytkownika można utworzyć ręcznie w domenie zarządzanej i nie istnieje w usłudze Azure AD.

Wszyscy użytkownicy, niezależnie od sposobu ich tworzenia, mają następujące zasady blokady konta stosowane w ramach domyślnych zasad haseł w usłudze Azure AD DS:

* **Czas trwania blokady konta:** 30
* **Liczba dozwolonych nieudanych prób logowania:** 5
* **Resetowanie liczby nieudanych prób logowania nie powiodło się po:** 30 minut
* **Maksymalny wiek hasła (Lifetime):** 90 dni

W przypadku tych domyślnych ustawień konta użytkowników są blokowane przez 30 minut, jeśli pięć nieprawidłowych haseł zostało użytych w ciągu 2 minut. Konta są automatycznie odblokowywane po 30 minutach.

Blokada konta występuje tylko w domenie zarządzanej. Konta użytkowników są blokowane tylko na platformie Azure AD DS i tylko z powodu nieudanych prób logowania do domeny zarządzanej. Konta użytkowników, które zostały zsynchronizowane z usługą Azure AD lub lokalnie, nie są blokowane w katalogach źródłowych tylko w usłudze Azure AD DS.

Jeśli masz zasady haseł usługi Azure AD, które określają maksymalny wiek hasła większy niż 90 dni, ten wiek hasła zostanie zastosowany do domyślnych zasad na platformie Azure AD DS. Można skonfigurować niestandardowe zasady haseł w celu zdefiniowania innego maksymalnego wieku hasła w usłudze Azure AD DS. Weź pod uwagę, że masz krótszy maksymalny wiek hasła skonfigurowany w zasadach haseł usługi Azure AD DS niż w przypadku usługi Azure AD lub lokalnego środowiska AD DS. W tym scenariuszu hasło użytkownika może wygasnąć na platformie Azure AD DS zanim zostanie wyświetlony monit o zmianę w usłudze Azure AD lub w środowisku lokalnym AD DS.

W przypadku kont użytkowników utworzonych ręcznie w domenie zarządzanej są również stosowane następujące dodatkowe ustawienia hasła. Te ustawienia nie dotyczą kont użytkowników synchronizowanych z usługą Azure AD, ponieważ użytkownik nie może zaktualizować hasła bezpośrednio w usłudze Azure AD DS.

* **Minimalna długość hasła (w znakach):** 7
* **Hasła muszą spełniać wymagania dotyczące złożoności**

Nie można zmodyfikować ustawień blokady konta lub hasła w domyślnych zasadach haseł. Zamiast tego, członkowie grupy *Administratorzy domeny usługi AAD* mogą tworzyć niestandardowe zasady haseł i konfigurować je w celu przesłonięcia (pierwszeństwo) domyślnych zasad, jak pokazano w następnej sekcji.

## <a name="create-a-custom-password-policy"></a>Tworzenie niestandardowych zasad haseł

Podczas kompilowania i uruchamiania aplikacji na platformie Azure można skonfigurować niestandardowe zasady haseł. Można na przykład utworzyć zasady, aby ustawić inne ustawienia zasad blokady konta.

Niestandardowe zasady haseł są stosowane do grup w domenie zarządzanej. Ta konfiguracja skutecznie zastępuje zasady domyślne.

Aby utworzyć niestandardowe zasady haseł, użyj Active Directory narzędzi administracyjnych z maszyny wirtualnej przyłączonej do domeny. Centrum administracyjne usługi Active Directory pozwala wyświetlać, edytować i tworzyć zasoby w domenie zarządzanej, w tym jednostki organizacyjne.

> [!NOTE]
> Aby utworzyć niestandardowe zasady haseł w domenie zarządzanej, należy zalogować się na konto użytkownika, które jest członkiem grupy *administratorów kontrolera domeny usługi AAD* .

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
1. Aby utworzyć jednostki organizacyjne i zarządzać nimi, wybierz **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Otwórz kontener **system** , a następnie **Kontener ustawień haseł**.

    Zostanie wyświetlona wbudowana zasada haseł dla domeny zarządzanej. Nie można modyfikować tych wbudowanych zasad. Zamiast tego Utwórz niestandardowe zasady haseł, aby zastąpić zasady domyślne.

    ![Utwórz zasady dotyczące haseł w Centrum administracyjne usługi Active Directory](./media/password-policy/create-password-policy-adac.png)

1. W panelu **zadania** po prawej stronie wybierz pozycję **nowe ustawienia hasła >**.
1. W oknie dialogowym **Tworzenie ustawień hasła** wprowadź nazwę zasad, na przykład *MyCustomFGPP*.
1. W przypadku istnienia wielu zasad haseł zasady o najwyższym priorytecie lub priorytecie są stosowane do użytkownika. Im niższa wartość, tym wyższy priorytet. Domyślne zasady haseł mają priorytet *200*.

    Ustaw pierwszeństwo dla niestandardowych zasad haseł, aby zastąpić wartości domyślne, takie jak *1*.

1. Edytuj inne ustawienia zasad haseł zgodnie z potrzebami. Należy pamiętać o następujących najważniejszych punktach:

    * Ustawienia, takie jak złożoność hasła, wiek lub czas wygaśnięcia, są tworzone ręcznie tylko dla użytkowników w domenie zarządzanej.
    * Ustawienia blokady konta mają zastosowanie do wszystkich użytkowników, ale zaczną obowiązywać tylko w domenie zarządzanej, a nie w samej usłudze Azure AD.

    ![Tworzenie niestandardowych zasad haseł szczegółowych](./media/password-policy/custom-fgpp.png)

1. Usuń zaznaczenie pola **Chroń przed przypadkowym usunięciem**. W przypadku wybrania tej opcji nie można zapisać szczegółowych zasad haseł.
1. W sekcji **bezpośrednio stosuje się do** wybierz przycisk **Dodaj** . W oknie dialogowym **Wybieranie użytkowników lub grup** wybierz przycisk **lokalizacje** .

    ![Wybierz użytkowników i grupy, do których mają zostać zastosowane zasady haseł](./media/password-policy/fgpp-applies-to.png)

1. Zasady dotyczące haseł mogą być stosowane tylko do grup. W oknie dialogowym **lokalizacje** rozwiń nazwę domeny, na przykład *aaddscontoso.com*, a następnie wybierz jednostkę organizacyjną, taką jak **AADDC users**. Jeśli masz niestandardową jednostkę organizacyjną zawierającą grupę użytkowników, których chcesz użyć, wybierz tę jednostkę organizacyjną.

    ![Wybierz jednostkę organizacyjną, do której należy Grupa](./media/password-policy/fgpp-container.png)

1. Wpisz nazwę grupy, do której chcesz zastosować zasady, a następnie wybierz pozycję **Sprawdź nazwy** , aby sprawdzić, czy grupa istnieje.

    ![Wyszukaj i wybierz grupę, która ma zostać zastosowana szczegółowych zasad haseł](./media/password-policy/fgpp-apply-group.png)

1. Po wybraniu nazwy grupy, która została **zastosowana bezpośrednio, ma zastosowanie do** sekcji, wybierz **przycisk OK** , aby zapisać niestandardowe zasady haseł.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zasad haseł i korzystania z centrum administracyjnego Active Directory, zobacz następujące artykuły:

* [Więcej informacji na temat szczegółowych zasad haseł](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurowanie szczegółowych zasad haseł przy użyciu Centrum administracyjne usługi AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
