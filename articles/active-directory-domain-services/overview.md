---
title: Przegląd Azure Active Directory Domain Services | Microsoft Docs
description: W tym przeglądzie dowiesz się, co zapewnia Azure Active Directory Domain Services i jak korzystać z niego w organizacji w celu zapewnienia usług tożsamości dla aplikacji i usług w chmurze.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661001"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co to są usługi Azure Active Directory Domain Services?

Azure Active Directory Domain Services (AD DS) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) oraz uwierzytelnianie Kerberos/NTLM. Te usługi domenowe są używane bez konieczności wdrażania kontrolerów domeny (DC) w chmurze i zarządzania nimi.

Domena zarządzana AD DS platformy Azure umożliwia uruchamianie starszych aplikacji w chmurze, które nie mogą korzystać z nowoczesnych metod uwierzytelniania, lub w przypadku, gdy nie chcesz, aby przeszukiwania katalogów były zawsze przywracane do lokalnego środowiska AD DS. Te starsze aplikacje można przekształcać i przenoszone ze środowiska lokalnego do domeny zarządzanej bez konieczności zarządzania środowiskiem AD DS w chmurze.

Platforma Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta Integracja umożliwia użytkownikom logowanie się do usług i aplikacji połączonych z domeną zarządzaną za pomocą istniejących poświadczeń. Do zabezpieczenia dostępu do zasobów można także używać istniejących grup i kont użytkowników. Te funkcje zapewniają płynne podniesienie i przesunięcia zasobów lokalnych na platformę Azure.

> [!div class="nextstepaction"]
> [Aby rozpocząć, Utwórz domenę zarządzaną platformy Azure AD DS przy użyciu Azure Portal][tutorial-create]

Zapoznaj się z naszym krótkim wideo, aby dowiedzieć się więcej o usłudze Azure AD DS.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Jak działa usługa Azure AD DS?

Podczas tworzenia domeny zarządzanej AD DS platformy Azure należy zdefiniować unikatową przestrzeń nazw. Ta przestrzeń nazw jest nazwą domeny, taką jak *aaddscontoso.com*. Dwa kontrolery domeny systemu Windows Server (DC) są następnie wdrażane w wybranym regionie platformy Azure. To wdrożenie kontrolerów domeny jest znane jako zestaw replik.

Nie musisz zarządzać, konfigurować ani aktualizować tych kontrolerów domeny. Platforma Azure obsługuje kontrolery domen w ramach domeny zarządzanej, w tym kopie zapasowe i szyfrowanie przy użyciu Azure Disk Encryption.

Domena zarządzana jest skonfigurowana w taki sposób, aby przeprowadzać jednokierunkową synchronizację z usługi Azure AD w celu zapewnienia dostępu do centralnego zestawu użytkowników, grup i poświadczeń. Zasoby można tworzyć bezpośrednio w domenie zarządzanej, ale nie są one synchronizowane z powrotem z usługą Azure AD. Aplikacje, usługi i maszyny wirtualne na platformie Azure, które łączą się z domeną zarządzaną, mogą następnie używać typowych funkcji AD DS, takich jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM.

W środowisku hybrydowym z lokalnym środowiskiem AD DS [Azure AD Connect][azure-ad-connect] synchronizuje informacje o tożsamości z usługą Azure AD, która jest następnie synchronizowana z domeną zarządzaną.

![Synchronizacja w Azure AD Domain Services z usługą Azure AD i lokalnym AD DS przy użyciu programu AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Usługa Azure AD DS replikuje informacje o tożsamościach z usługi Azure AD, więc współpracuje z dzierżawami usługi Azure AD, które są przeznaczone tylko do chmury, lub zsynchronizowane ze środowiskiem lokalnym AD DS. Ten sam zestaw funkcji platformy Azure AD DS istnieje w obu środowiskach.

* Jeśli masz istniejące lokalne środowisko AD DS, możesz synchronizować informacje o kontach użytkowników, aby zapewnić użytkownikom spójną tożsamość. Aby dowiedzieć się więcej, zobacz [jak obiekty i poświadczenia są synchronizowane w domenie zarządzanej][synchronization].
* W przypadku środowisk opartych tylko na chmurze nie jest wymagane tradycyjne lokalne środowisko AD DS do używania scentralizowanych usług tożsamości usługi Azure AD DS.

Można rozszerzyć domenę zarządzaną, aby mieć więcej niż jeden zbiór replik dla dzierżawy usługi Azure AD. Zestawy replik można dodawać do dowolnej komunikacji równorzędnej sieci wirtualnej w dowolnym regionie świadczenia usługi Azure, który obsługuje usługę Azure AD DS. Dodatkowe zestawy replik w różnych regionach platformy Azure zapewniają geograficzne odzyskiwanie po awarii dla starszych aplikacji, jeśli region platformy Azure przejdzie w tryb offline. Zestawy replik są obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz temat [zestawy replik i funkcje dla domen zarządzanych][concepts-replica-sets].

Poniższy klip wideo zawiera Omówienie integracji usługi Azure AD DS z aplikacjami i obciążeniami w celu zapewnienia usług tożsamości w chmurze:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Aby zobaczyć scenariusze wdrażania AD DS platformy Azure w działaniu, można zapoznać się z poniższymi przykładami:

* [AD DS platformy Azure dla organizacji hybrydowych](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [AD DS platformy Azure dla organizacji tylko w chmurze](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Funkcje i zalety platformy Azure AD DS

Aby zapewnić obsługę tożsamości dla aplikacji i maszyn wirtualnych w chmurze, platforma Azure AD DS jest w pełni zgodna z tradycyjnym środowiskiem AD DS dla operacji takich jak przyłączanie do domeny, bezpieczne LDAP (LDAPs), zasady grupy, zarządzanie usługą DNS i obsługa odczytu i odczytywanie. Obsługa zapisu LDAP jest dostępna dla obiektów utworzonych w domenie zarządzanej, ale nie zasobów synchronizowanych z usługi Azure AD.

Aby dowiedzieć się więcej o opcjach tożsamości, [PORÓWNAJ usługę azure AD DS z usługą Azure AD, AD DS na maszynach wirtualnych platformy Azure i AD DS lokalnie][compare].

Następujące funkcje platformy Azure AD DS upraszczają operacje wdrażania i zarządzania:

* **Uproszczone środowisko wdrażania:** Usługa Azure AD DS jest włączona dla dzierżawy usługi Azure AD przy użyciu jednego kreatora w Azure Portal.
* **Integracja z usługą Azure AD:** Konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne z dzierżawy usługi Azure AD. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawy usługi Azure AD lub lokalnego środowiska AD DS są automatycznie synchronizowane z usługą Azure AD DS.
    * Konta w katalogach zewnętrznych połączone z usługą Azure AD nie są dostępne w usłudze Azure AD DS. Poświadczenia nie są dostępne dla tych katalogów zewnętrznych, dlatego nie można ich zsynchronizować w domenie zarządzanej.
* **Użyj poświadczeń/haseł firmowej:** Hasła użytkowników w usłudze Azure AD DS są takie same jak w dzierżawie usługi Azure AD. Użytkownicy mogą używać swoich poświadczeń firmowych do przyłączania do domeny komputerów, logować się interaktywnie lub za pośrednictwem pulpitu zdalnego i uwierzytelniać się w domenie zarządzanej.
* **Uwierzytelnianie NTLM i Kerberos:** Dzięki obsłudze uwierzytelniania NTLM i Kerberos można wdrażać aplikacje korzystające z uwierzytelniania zintegrowanego z systemem Windows.
* **Wysoka dostępność:** Usługa Azure AD DS obejmuje wiele kontrolerów domeny, które zapewniają wysoką dostępność dla domeny zarządzanej. Ta wysoka dostępność gwarantuje czas pracy usługi i odporność na awarie.
    * W regionach, które obsługują [strefy dostępności platformy Azure][availability-zones], te kontrolery domeny są również dystrybuowane między strefami w celu uzyskania dodatkowej odporności.
    * [Zestawy replik][concepts-replica-sets] mogą również służyć do zapewnienia geograficznego odzyskiwania po awarii dla starszych aplikacji, jeśli region platformy Azure przejdzie w tryb offline.

Niektóre kluczowe aspekty domeny zarządzanej obejmują następujące elementy:

* Domena zarządzana jest domeną autonomiczną. Nie jest to rozszerzenie domeny lokalnej.
    * W razie potrzeby można utworzyć jednokierunkowe zaufanie lasu wychodzącego z usługi Azure AD DS do środowiska AD DS lokalnych. Aby uzyskać więcej informacji, zobacz temat [zagadnienia i funkcje lasu zasobów dla platformy Azure AD DS][ forest-trusts].
* Twój zespół IT nie musi zarządzać, poprawiać ani monitorować kontrolerów domeny dla tej domeny zarządzanej.

W przypadku środowisk hybrydowych, które są uruchamiane AD DS lokalnie, nie trzeba zarządzać replikacją usługi AD do domeny zarządzanej. Konta użytkowników, członkostwa w grupach i poświadczenia z katalogu lokalnego są synchronizowane z usługą Azure AD za pośrednictwem [Azure AD Connect][azure-ad-connect]. Te konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne w ramach domeny zarządzanej.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o platformie Azure AD DS porównać z innymi rozwiązaniami do tworzenia tożsamości i jak działa synchronizacja, zobacz następujące artykuły:

* [Porównanie AD DS platformy Azure z usługą Azure AD, Active Directory Domain Services na maszynach wirtualnych platformy Azure i Active Directory Domain Services lokalnych][compare]
* [Dowiedz się, jak Azure AD Domain Services synchronizuje się z katalogiem usługi Azure AD][synchronization]
* Aby dowiedzieć się, jak administratorować domenę zarządzaną, zobacz [pojęcia dotyczące zarządzania kontami użytkowników, hasłami i administracją w usłudze Azure AD DS][administration-concepts].

Aby rozpocząć, [Utwórz domenę zarządzaną przy użyciu Azure Portal][tutorial-create].

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
[concepts-replica-sets]: concepts-replica-sets.md
