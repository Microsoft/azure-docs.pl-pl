---
title: Typowe scenariusze wdrażania dla Azure AD Domain Services | Microsoft Docs
description: Zapoznaj się z typowymi scenariuszami i przypadkami użycia Azure Active Directory Domain Services, aby zapewnić wartość i spełnić wymagania biznesowe.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619032"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Typowe przypadki użycia i scenariusze dla Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) oraz uwierzytelnianie Kerberos/NTLM. Usługa Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD, co umożliwia użytkownikom logowanie się przy użyciu istniejących poświadczeń. Te usługi domenowe są używane bez konieczności wdrażania i poprawiania kontrolerów domeny w chmurze oraz zarządzania nimi, co zapewnia płynne podniesienie i przesunięcia zasobów lokalnych na platformę Azure.

W tym artykule opisano niektóre typowe scenariusze biznesowe, w których usługa Azure AD DS zapewnia wartość i spełnia te wymagania.

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

## <a name="azure-ad-ds-for-hybrid-organizations"></a>AD DS platformy Azure dla organizacji hybrydowych

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
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Nie jest obsługiwane Instalowanie Azure AD Connect w domenie zarządzanej w celu synchronizacji obiektów z powrotem do usługi Azure AD.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>AD DS platformy Azure dla organizacji tylko w chmurze

Dzierżawa usługi Azure AD tylko w chmurze nie ma lokalnego źródła tożsamości. Konta użytkowników i członkostwa w grupach, na przykład, są tworzone i zarządzane bezpośrednio w usłudze Azure AD.

Teraz przyjrzyjmy się przykładowi do firmy Contoso, która jest oparta na chmurze, która używa usługi Azure AD do tożsamości. Wszystkie tożsamości użytkowników, ich poświadczenia i członkostwa w grupach są tworzone i zarządzane w usłudze Azure AD. Nie ma dodatkowej konfiguracji Azure AD Connect do synchronizowania informacji o tożsamości z katalogu lokalnego.

![Azure Active Directory Domain Services organizacji tylko w chmurze bez synchronizacji lokalnej](./media/overview/cloud-only-tenant.png)

* Obciążenia aplikacji i serwera, które wymagają usług domenowych, są wdrażane w sieci wirtualnej na platformie Azure.
* Zespół IT firmy Contoso umożliwia korzystanie z platformy Azure AD DS w ramach tej dzierżawy usługi Azure AD w ramach tej lub komunikacji równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w usłudze Azure Virtual Network mogą następnie używać funkcji AD DS platformy Azure, takich jak przyłączanie do domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

## <a name="secure-administration-of-azure-virtual-machines"></a>Bezpieczna Administracja maszyn wirtualnych platformy Azure

Aby można było używać jednego zestawu poświadczeń usługi AD, maszyny wirtualne platformy Azure można przyłączyć do domeny zarządzanej AD DS platformy Azure. Takie podejście zmniejsza problemy związane z zarządzaniem poświadczeniami, takie jak obsługa kont administratorów lokalnych na każdej maszynie wirtualnej lub oddzielne konta i hasła między środowiskami.

Maszyny wirtualne, które są przyłączone do domeny zarządzanej, mogą być również administrowane i zabezpieczone przy użyciu zasad grupy. Wymagane linie bazowe zabezpieczeń mogą być stosowane do maszyn wirtualnych w celu zablokowania ich zgodnie z wytycznymi dotyczącymi zabezpieczeń firmy. Można na przykład użyć funkcji zarządzania zasadami grupy, aby ograniczyć typy aplikacji, które można uruchomić na maszynie wirtualnej.

![Usprawnione administrowanie maszynami wirtualnymi platformy Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Przyjrzyjmy się typowym przykładowym scenariuszom. Ponieważ serwery i inne infrastruktury osiągają koniec życia, firma Contoso chce przenieść aplikacje aktualnie hostowane lokalnie do chmury. Ich bieżąca wersja standardu IT, która umożliwia serwerom hostującym aplikacje firmowe, musi być przyłączona do domeny i zarządzana przy użyciu zasad grupy.

Administrator IT firmy Contoso woli przyłączyć do domeny maszyny wirtualne wdrożone na platformie Azure, aby ułatwić administrację, ponieważ użytkownicy mogą logować się przy użyciu swoich poświadczeń firmowych. Po przyłączeniu do domeny maszyny wirtualne można również skonfigurować tak, aby spełniały wymagane linie bazowe zabezpieczeń przy użyciu obiektów zasad grupy (GPO). Firma Contoso woli nie wdrażać i monitorować własnych kontrolerów domeny na platformie Azure oraz zarządzać nimi.

Usługa Azure AD DS to doskonałe rozwiązanie dla tego przypadku użycia. Domena zarządzana umożliwia przyłączanie do domeny maszyn wirtualnych, korzystanie z jednego zestawu poświadczeń i stosowanie zasad grupy. I ponieważ jest to domena zarządzana, nie trzeba samodzielnie konfigurować kontrolerów domeny i nimi zarządzać.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Domeny zarządzane domyślnie korzystają z jednej, płaskiej struktury jednostki organizacyjnej (OU). Wszystkie maszyny wirtualne przyłączone do domeny znajdują się w jednej jednostce organizacyjnej. W razie potrzeby można utworzyć [niestandardowe jednostki organizacyjne][custom-ou].
* Usługa Azure AD DS używa wbudowanego obiektu zasad grupy dla kontenerów Użytkownicy i komputery. Aby uzyskać dodatkową kontrolę, można [utworzyć niestandardowe obiekty zasad grupy][create-gpo] i kierować je do niestandardowych jednostek organizacyjnych.
* Usługa Azure AD DS obsługuje podstawowy schemat obiektów komputera usługi AD. Nie można rozłożyć schematu obiektu komputera.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplikacje lokalne podnoszenia i przesunięcia korzystające z uwierzytelniania powiązania LDAP

W przykładowym scenariuszu firma Contoso ma aplikację lokalną, która została zakupiona przez wiele lat temu. Aplikacja jest obecnie w trybie konserwacji przez niezależnego dostawcę oprogramowania i żądanie zmian w aplikacji jest niezwykle drogie. Ta aplikacja ma oparty na sieci Web fronton, który zbiera poświadczenia użytkownika przy użyciu formularza sieci Web, a następnie uwierzytelnia użytkowników, wykonując powiązanie LDAP z lokalnym środowiskiem AD DS.

![Powiązanie LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Firma Contoso chce przeprowadzić migrację tej aplikacji na platformę Azure. Aplikacja powinna nadal działać w niezmienionej postaci, bez konieczności wprowadzania zmian. Ponadto użytkownicy powinni mieć możliwość uwierzytelniania przy użyciu istniejących poświadczeń firmowych i bez dodatkowego szkolenia. Powinna być widoczna dla użytkowników końcowych, na których działa aplikacja.

W tym scenariuszu usługa Azure AD DS umożliwia aplikacjom wykonywanie powiązań LDAP w ramach procesu uwierzytelniania. Starsze aplikacje lokalne mogą podnieść i przeshift do platformy Azure i nadal bezproblemowo uwierzytelniać użytkowników bez wprowadzania zmian w konfiguracji ani środowisku użytkownika.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Upewnij się, że aplikacja nie musi modyfikować/zapisywać w katalogu. Dostęp do zapisu LDAP do domeny zarządzanej nie jest obsługiwany.
* Nie można zmienić haseł bezpośrednio w domenie zarządzanej. Użytkownicy końcowi mogą zmieniać swoje hasła przy użyciu samoobsługowego [mechanizmu zmiany hasła w usłudze Azure AD][sspr] lub w katalogu lokalnym. Te zmiany są następnie automatycznie synchronizowane i dostępne w domenie zarządzanej.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplikacje lokalne, które używają odczytu z protokołu LDAP do uzyskiwania dostępu do katalogu

Podobnie jak w przypadku poprzedniego przykładowego scenariusza Załóżmy, że firma Contoso ma lokalną aplikację biznesową (LOB), która została opracowana niemal dekady temu. Ta aplikacja jest świadoma katalogu i została zaprojektowana pod kątem używania protokołu LDAP do odczytywania informacji/atrybutów o użytkownikach z AD DS. Aplikacja nie modyfikuje atrybutów ani nie zapisuje ich w innym katalogu.

Firma Contoso chce przeprowadzić migrację tej aplikacji na platformę Azure i wycofać sprzęt lokalny, który aktualnie obsługuje tę aplikację. Nie można zapisać aplikacji w celu używania nowoczesnych interfejsów API usługi, takich jak interfejs API Microsoft Graph oparty na protokole REST. Wymagana jest opcja podnoszenia i przesunięcia, w której można przeprowadzić migrację aplikacji do działania w chmurze bez modyfikowania kodu lub ponownego zapisywania aplikacji.

Aby pomóc w tym scenariuszu, usługa Azure AD DS umożliwia aplikacjom wykonywanie operacji odczytu protokołu LDAP dla domeny zarządzanej w celu uzyskania potrzebnych informacji o atrybutach. Aplikacja nie musi być ponownie zapisywana, więc przekazanie i przesunięcia na platformę Azure umożliwi użytkownikom dalsze korzystanie z aplikacji bez konieczności wprowadzania zmian w lokalizacji, w której działa.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Upewnij się, że aplikacja nie musi modyfikować/zapisywać w katalogu. Dostęp do zapisu LDAP do domeny zarządzanej nie jest obsługiwany.
* Upewnij się, że aplikacja nie wymaga niestandardowego/rozszerzonego schematu Active Directory. Rozszerzenia schematu nie są obsługiwane w usłudze Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrowanie lokalnej usługi lub aplikacji demona na platformę Azure

Niektóre aplikacje obejmują wiele warstw, w których jedna z warstw musi wykonać uwierzytelnione wywołania do warstwy zaplecza, takiej jak baza danych. Konta usług AD są często używane w tych scenariuszach. Po podnoszenia i przesunięciu aplikacji na platformę Azure usługa Azure AD DS umożliwia dalsze korzystanie z kont usług w taki sam sposób. Można wybrać użycie tego samego konta usługi, które jest synchronizowane z katalogu lokalnego do usługi Azure AD, lub utworzyć niestandardową jednostkę organizacyjną, a następnie utworzyć osobne konto usługi w tej jednostce organizacyjnej. W obu przypadkach aplikacje nadal działają tak samo jak w przypadku połączeń uwierzytelnianych z innymi warstwami i usługami.

![Konto usługi korzystające z WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

W tym przykładowym scenariuszu firma Contoso ma utworzoną niestandardową aplikację magazynu oprogramowania, która obejmuje fronton sieci Web, program SQL Server i serwer FTP zaplecza. Uwierzytelnianie zintegrowane systemu Windows przy użyciu kont usługi uwierzytelnia fronton sieci Web na serwerze FTP. Fronton internetowy jest skonfigurowany do uruchamiania jako konto usługi. Serwer wewnętrznej bazy danych jest skonfigurowany tak, aby autoryzować dostęp z konta usługi dla frontonu sieci Web. Firma Contoso nie chce wdrażać własnych maszyn wirtualnych kontrolera domeny w chmurze i zarządzać nimi, aby przenieść tę aplikację na platformę Azure.

W tym scenariuszu serwery obsługujące fronton internetowy, program SQL Server i serwer FTP można migrować do maszyn wirtualnych platformy Azure i przyłączyć się do domeny zarządzanej. Maszyny wirtualne mogą następnie używać tego samego konta usługi w katalogu lokalnym w celu uwierzytelnienia aplikacji, która jest synchronizowana za pośrednictwem usługi Azure AD przy użyciu Azure AD Connect.

### <a name="deployment-notes"></a>Uwagi dotyczące wdrażania

Następujące zagadnienia dotyczące wdrażania dotyczą tego przykładowego przypadku użycia:

* Upewnij się, że aplikacje używają nazwy użytkownika i hasła na potrzeby uwierzytelniania. Certyfikat lub uwierzytelnianie oparte na karcie inteligentnej nie są obsługiwane przez usługę Azure AD DS.
* Nie można zmienić haseł bezpośrednio w domenie zarządzanej. Użytkownicy końcowi mogą zmieniać swoje hasła przy użyciu samoobsługowego [mechanizmu zmiany hasła w usłudze Azure AD][sspr] lub w katalogu lokalnym. Te zmiany są następnie automatycznie synchronizowane i dostępne w domenie zarządzanej.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Wdrożenia usług pulpitu zdalnego systemu Windows Server na platformie Azure

Usługa Azure AD DS umożliwia udostępnianie usług domenowych zarządzanej na serwerach usług pulpitu zdalnego wdrożonych na platformie Azure.

Aby uzyskać więcej informacji o tym scenariuszu wdrażania, zobacz [Jak zintegrować Azure AD Domain Services z wdrożeniem usług pulpitu zdalnego][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Przyłączone do domeny klastry usługi HDInsight

Można skonfigurować klaster usługi Azure HDInsight, który jest przyłączony do domeny zarządzanej z włączonym platformą Apache Ranger. Korzystając z narzędzi opartych na ODBC, takich jak Excel lub Tableau, można tworzyć i stosować zasady usługi Hive za pośrednictwem usługi Apache Ranger. Nadal pracujemy nad dodaniem innych obciążeń, takich jak HBase, Spark i burza, do usługi HDInsight przyłączonego do domeny.

Aby uzyskać więcej informacji o tym scenariuszu wdrażania, zobacz [jak skonfigurować przyłączone do domeny klastry usługi HDInsight][hdinsight]

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds