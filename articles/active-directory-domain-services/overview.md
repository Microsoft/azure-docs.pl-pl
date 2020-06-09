---
title: Przegląd Azure Active Directory Domain Services | Microsoft Docs
description: W tym przeglądzie dowiesz się, co zapewnia Azure Active Directory Domain Services i jak korzystać z niego w organizacji w celu zapewnienia usług tożsamości dla aplikacji i usług w chmurze.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: c8a29f95e7b987af2b408ec24bc390a7fd162263
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554868"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co to jest Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) oraz uwierzytelnianie Kerberos/NTLM. Te usługi domenowe są używane bez konieczności wdrażania i poprawiania kontrolerów domeny w chmurze oraz zarządzania nimi.

Platforma Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta Integracja umożliwia użytkownikom logowanie się do usługi i aplikacji podłączonych do domeny zarządzanej przy użyciu istniejących poświadczeń. Do zabezpieczenia dostępu do zasobów można także używać istniejących grup i kont użytkowników. Te funkcje zapewniają płynne podniesienie i przesunięcia zasobów lokalnych na platformę Azure.

> [!div class="nextstepaction"]
> [Aby rozpocząć, Utwórz domenę zarządzaną platformy Azure AD DS przy użyciu Azure Portal][tutorial-create]

Usługa Azure AD DS replikuje informacje o tożsamościach z usługi Azure AD, więc współpracuje z dzierżawami usługi Azure AD, które są przeznaczone tylko do chmury, lub zsynchronizowane ze środowiskiem lokalnym Active Directory Domain Services (AD DS). Ten sam zestaw funkcji platformy Azure AD DS istnieje w obu środowiskach.

* Jeśli masz istniejące lokalne środowisko AD DS, możesz synchronizować informacje o kontach użytkowników, aby zapewnić użytkownikom spójną tożsamość. Aby dowiedzieć się więcej, zobacz [jak obiekty i poświadczenia są synchronizowane w domenie zarządzanej][synchronization].
* W przypadku środowisk opartych tylko na chmurze nie jest wymagane tradycyjne lokalne środowisko AD DS do używania scentralizowanych usług tożsamości usługi Azure AD DS.

Aby dowiedzieć się, jak administratorować domenę zarządzaną, zobacz [pojęcia dotyczące zarządzania kontami użytkowników, hasłami i administracją w usłudze Azure AD DS][administration-concepts].

Poniższy klip wideo zawiera Omówienie integracji usługi Azure AD DS z aplikacjami i obciążeniami w celu zapewnienia usług tożsamości w chmurze:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Typowe sposoby dostarczania rozwiązań do obsługi tożsamości w chmurze

Podczas migrowania istniejących obciążeń do chmury aplikacje obsługujące katalogi mogą używać protokołu LDAP do odczytu lub zapisu w lokalnym katalogu AD DS. Aplikacje uruchamiane w systemie Windows Server są zwykle wdrażane na maszynach wirtualnych przyłączonych do domeny, aby umożliwić ich bezpieczne zarządzanie przy użyciu zasady grupy. W celu uwierzytelnienia użytkowników końcowych aplikacje mogą również polegać na uwierzytelnianiu zintegrowanym z systemem Windows, takim jak uwierzytelnianie Kerberos lub NTLM.

Administratorzy IT często używają jednego z następujących rozwiązań, aby zapewnić usłudze tożsamości aplikacje działające na platformie Azure:

* Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między obciążeniami uruchomionymi na platformie Azure a lokalnym środowiskiem AD DS.
    * Lokalne kontrolery domeny zapewniają uwierzytelnianie za pośrednictwem połączenia sieci VPN.
* Utwórz repliki kontrolerów domeny za pomocą usługi Azure Virtual Machines, aby zwiększyć AD DS domeny/lasu z lokalnego.
    * Kontrolery domeny działające na maszynach wirtualnych platformy Azure zapewniają uwierzytelnianie i replikujeją informacje katalogu między środowiskiem lokalnym AD DS.
* Wdróż autonomiczne środowisko AD DS na platformie Azure przy użyciu kontrolerów domeny działających na maszynach wirtualnych platformy Azure.
    * Kontrolery domeny działające na maszynach wirtualnych platformy Azure zapewniają uwierzytelnianie, ale nie ma informacji o katalogu replikowanych z lokalnego środowiska AD DS.

Korzystając z tych metod, połączenia sieci VPN z katalogiem lokalnym sprawiają, że aplikacje są podatne na przejściowe awarie sieciowe lub przestoje. W przypadku wdrażania kontrolerów domeny przy użyciu maszyn wirtualnych na platformie Azure zespół IT musi zarządzać maszynami wirtualnymi, a następnie zabezpieczać, poprawiać, monitorować, tworzyć kopie zapasowe i rozwiązywać problemy.

Usługa Azure AD DS oferuje alternatywy dla potrzeb tworzenia połączeń sieci VPN z powrotem do lokalnego środowiska AD DS lub uruchamiania maszyn wirtualnych i zarządzania nimi na platformie Azure w celu zapewnienia usług tożsamości. Jako usługa zarządzana platforma Azure AD DS zmniejsza złożoność tworzenia zintegrowanego rozwiązania do obsługi tożsamości zarówno dla środowisk hybrydowych, jak i chmurowych.

> [!div class="nextstepaction"]
> [Porównanie AD DS platformy Azure z usługą Azure AD i samozarządzanym AD DS na maszynach wirtualnych platformy Azure lub w środowisku lokalnym][compare]

## <a name="how-does-azure-ad-ds-work"></a>Jak działa usługa Azure AD DS?

Aby zapewnić usługi tożsamości, platforma Azure tworzy wystąpienie AD DS w wybranej sieci wirtualnej. W tle jest tworzona para kontrolerów domeny systemu Windows Server, która jest uruchamiana na maszynach wirtualnych platformy Azure. Nie musisz zarządzać, konfigurować ani aktualizować tych kontrolerów domeny. Platforma Azure zarządza kontrolerami domeny w ramach usługi AD DS platformy Azure.

Domena zarządzana AD DS Azure jest skonfigurowana tak, aby przeprowadzać jednokierunkową synchronizację z usługi Azure AD w celu zapewnienia dostępu do centralnego zestawu użytkowników, grup i poświadczeń. Zasoby można tworzyć bezpośrednio w domenie zarządzanej AD DS platformy Azure, ale nie są one synchronizowane z powrotem z usługą Azure AD. Aplikacje, usługi i maszyny wirtualne na platformie Azure, które łączą się z tą siecią wirtualną, mogą używać typowych funkcji AD DS, takich jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM.

W środowisku hybrydowym z lokalnym środowiskiem AD DS [Azure AD Connect][azure-ad-connect] synchronizuje informacje o tożsamości z usługą Azure AD, które są następnie synchronizowane z usługą Azure AD DS.

![Synchronizacja w Azure AD Domain Services z usługą Azure AD i lokalnym Active Directory Domain Services przy użyciu programu AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Aby zobaczyć AD DS platformy Azure w działaniu, przyjrzyjmy się kilku przykładom:

* [AD DS platformy Azure dla organizacji hybrydowych](#azure-ad-ds-for-hybrid-organizations)
* [AD DS platformy Azure dla organizacji tylko w chmurze](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>AD DS platformy Azure dla organizacji hybrydowych

Wiele organizacji uruchamia infrastrukturę hybrydową obejmującą zarówno obciążenia aplikacji w chmurze, jak i lokalne. Starsze aplikacje migrowane do platformy Azure w ramach strategii podnoszenia i przesunięcia mogą używać tradycyjnych połączeń LDAP do dostarczania informacji o tożsamości. Aby zapewnić obsługę tej infrastruktury hybrydowej, informacje o tożsamościach z lokalnego środowiska AD DS można synchronizować z dzierżawą usługi Azure AD. Usługa Azure AD DS następnie udostępnia te starsze aplikacje na platformie Azure przy użyciu źródła tożsamości, bez konieczności konfigurowania i zarządzania łącznością aplikacji z powrotem do lokalnych usług katalogowych.

Przyjrzyjmy się przykładowi do litware Corporation — organizacji hybrydowej, która działa zarówno lokalnie, jak i zasoby platformy Azure:

![Azure Active Directory Domain Services organizacji hybrydowej, która obejmuje synchronizację lokalną](./media/overview/synced-tenant.png)

* Obciążenia aplikacji i serwera, które wymagają usług domenowych, są wdrażane w sieci wirtualnej na platformie Azure.
    * Może to obejmować starsze aplikacje migrowane do platformy Azure w ramach strategii podnoszenia i przesunięcia.
* Aby synchronizować informacje o tożsamości z katalogu lokalnego do swojej dzierżawy usługi Azure AD, firma litware Corporation wdraża [Azure AD Connect][azure-ad-connect].
    * Synchronizowane informacje o tożsamości obejmują konta użytkowników i członkostwa w grupach.
* Zespół IT litware umożliwia korzystanie z platformy AD DS Azure w ramach tej dzierżawy usługi Azure AD w ramach tej lub komunikacji równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w usłudze Azure Virtual Network mogą następnie używać funkcji AD DS platformy Azure, takich jak przyłączanie do domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Instalowanie Azure AD Connect w domenie zarządzanej AD DS platformy Azure nie jest obsługiwane do synchronizowania obiektów z powrotem do usługi Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>AD DS platformy Azure dla organizacji tylko w chmurze

Dzierżawa usługi Azure AD tylko w chmurze nie ma lokalnego źródła tożsamości. Konta użytkowników i członkostwa w grupach, na przykład, są tworzone i zarządzane bezpośrednio w usłudze Azure AD.

Teraz przyjrzyjmy się przykładowi do firmy Contoso, która jest oparta na chmurze, która używa usługi Azure AD do tożsamości. Wszystkie tożsamości użytkowników, ich poświadczenia i członkostwa w grupach są tworzone i zarządzane w usłudze Azure AD. Nie ma dodatkowej konfiguracji Azure AD Connect do synchronizowania informacji o tożsamości z katalogu lokalnego.

![Azure Active Directory Domain Services organizacji tylko w chmurze bez synchronizacji lokalnej](./media/overview/cloud-only-tenant.png)

* Obciążenia aplikacji i serwera, które wymagają usług domenowych, są wdrażane w sieci wirtualnej na platformie Azure.
* Zespół IT firmy Contoso umożliwia korzystanie z platformy Azure AD DS w ramach tej dzierżawy usługi Azure AD w ramach tej lub komunikacji równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w usłudze Azure Virtual Network mogą następnie używać funkcji AD DS platformy Azure, takich jak przyłączanie do domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

## <a name="azure-ad-ds-features-and-benefits"></a>Funkcje i zalety platformy Azure AD DS

Aby zapewnić obsługę tożsamości dla aplikacji i maszyn wirtualnych w chmurze, platforma Azure AD DS jest w pełni zgodna z tradycyjnym środowiskiem AD DS dla operacji takich jak przyłączanie do domeny, bezpieczne LDAP (LDAPs), zasady grupy, zarządzanie usługą DNS i obsługa odczytu i odczytywanie. Obsługa zapisu LDAP jest dostępna dla obiektów utworzonych w domenie zarządzanej AD DS platformy Azure, ale nie zasobów synchronizowanych z usługi Azure AD.

Aby dowiedzieć się więcej o opcjach tożsamości, [PORÓWNAJ usługę azure AD DS z usługą Azure AD, Active Directory Domain Services na maszynach wirtualnych platformy Azure i Active Directory Domain Services lokalnie][compare].

Następujące funkcje platformy Azure AD DS upraszczają operacje wdrażania i zarządzania:

* **Uproszczone środowisko wdrażania:** Usługa Azure AD DS jest włączona dla dzierżawy usługi Azure AD przy użyciu jednego kreatora w Azure Portal.
* **Integracja z usługą Azure AD:** Konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne z dzierżawy usługi Azure AD. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawy usługi Azure AD lub lokalnego środowiska AD DS są automatycznie synchronizowane z usługą Azure AD DS.
    * Konta w katalogach zewnętrznych połączone z usługą Azure AD nie są dostępne w usłudze Azure AD DS. Poświadczenia nie są dostępne dla tych katalogów zewnętrznych, dlatego nie można ich synchronizować z domeną zarządzaną AD DS platformy Azure.
* **Użyj poświadczeń/haseł firmowej:** Hasła użytkowników w usłudze Azure AD DS są takie same jak w dzierżawie usługi Azure AD. Użytkownicy mogą używać swoich poświadczeń firmowych do przyłączania do domeny komputerów, logować się interaktywnie lub za pośrednictwem pulpitu zdalnego i uwierzytelniać się w domenie zarządzanej platformy Azure AD DS.
* **Uwierzytelnianie NTLM i Kerberos:** Dzięki obsłudze uwierzytelniania NTLM i Kerberos można wdrażać aplikacje korzystające z uwierzytelniania zintegrowanego z systemem Windows.
* **Wysoka dostępność:** Usługa Azure AD DS obejmuje wiele kontrolerów domeny, które zapewniają wysoką dostępność dla domeny zarządzanej. Ta wysoka dostępność gwarantuje czas pracy usługi i odporność na awarie.
    * W regionach, które obsługują [strefy dostępności platformy Azure][availability-zones], te kontrolery domeny są również dystrybuowane między strefami w celu uzyskania dodatkowej odporności.

Niektóre kluczowe aspekty domeny zarządzanej platformy Azure AD DS są następujące:

* Domena zarządzana AD DS platformy Azure jest domeną autonomiczną. Nie jest to rozszerzenie domeny lokalnej.
    * W razie potrzeby można utworzyć jednokierunkowe zaufanie lasu wychodzącego z usługi Azure AD DS do środowiska AD DS lokalnych. Aby uzyskać więcej informacji, zobacz temat [zagadnienia i funkcje lasu zasobów dla platformy Azure AD DS][ forest-trusts].
* Twój zespół IT nie musi zarządzać, poprawiać ani monitorować kontrolerów domeny dla tej domeny zarządzanej platformy Azure AD DS.

W przypadku środowisk hybrydowych, które są uruchamiane AD DS lokalnie, nie trzeba zarządzać replikacją usługi AD do domeny zarządzanej AD DS platformy Azure. Konta użytkowników, członkostwa w grupach i poświadczenia z katalogu lokalnego są synchronizowane z usługą Azure AD za pośrednictwem [Azure AD Connect][azure-ad-connect]. Te konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne w ramach domeny zarządzanej AD DS platformy Azure.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o platformie Azure AD DS porównać z innymi rozwiązaniami do tworzenia tożsamości i jak działa synchronizacja, zobacz następujące artykuły:

* [Porównanie AD DS platformy Azure z usługą Azure AD, Active Directory Domain Services na maszynach wirtualnych platformy Azure i Active Directory Domain Services lokalnych][compare]
* [Dowiedz się, jak Azure AD Domain Services synchronizuje się z katalogiem usługi Azure AD][synchronization]

Aby rozpocząć, [Utwórz domenę zarządzaną platformy Azure AD DS przy użyciu Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
