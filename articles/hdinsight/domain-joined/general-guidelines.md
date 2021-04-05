---
title: Ogólne wytyczne dotyczące zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
description: Niektóre najlepsze rozwiązania, które ułatwiają pakiet Enterprise Security wdrażania i zarządzania.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933564"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Ogólne informacje i wskazówki dotyczące zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

W przypadku wdrażania bezpiecznego klastra usługi HDInsight istnieją pewne najlepsze rozwiązania, które ułatwiają zarządzanie wdrażaniem i klastrem. Niektóre ogólne informacje i wskazówki zostały omówione tutaj.

## <a name="use-of-secure-cluster"></a>Korzystanie z bezpiecznego klastra

### <a name="recommended"></a>Zalecane

* Klaster będzie używany przez wielu użytkowników jednocześnie.
* Użytkownicy mają różne poziomy dostępu do tych samych danych.

### <a name="not-necessary"></a>Niepotrzebne

* Będzie można uruchamiać tylko zadania automatyczne (takie jak konto pojedynczego użytkownika), więc klaster standardowy jest wystarczająco dobry.
* Importowanie danych można wykonać przy użyciu klastra standardowego i korzystać z tego samego konta magazynu w innym bezpiecznym klastrze, w którym użytkownicy mogą uruchamiać zadania analityczne.

## <a name="use-of-local-account"></a>Użycie konta lokalnego

* Jeśli używasz konta użytkownika udostępnionego lub konta lokalnego, będzie trudno zidentyfikować, kto użył konta, aby zmienić konfigurację lub usługę.
* Korzystanie z kont lokalnych jest problematyczne, gdy użytkownicy nie są już częścią organizacji.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Zasady

* Domyślnie Ranger używa **odmowy** jako zasady.

* Gdy dostęp do danych odbywa się za pomocą usługi, w której włączono autoryzację:
  * Wtyczka autoryzacji Ranger jest wywoływana i ma kontekst żądania.
  * Ranger stosuje zasady skonfigurowane dla usługi. Jeśli zasady Ranger zakończą się niepowodzeniem, sprawdzanie dostępu zostanie odroczone do systemu plików. Niektóre usługi, takie jak MapReduce, sprawdzają, czy plik/folder należy do tego samego użytkownika, który przesyła żądanie. Usługi, takie jak Hive, sprawdzają zgodność własności lub odpowiednie uprawnienia systemu plików ( `rwx` ).

* W przypadku programu Hive oprócz uprawnień do wykonywania uprawnień do tworzenia/aktualizowania/usuwania użytkownik powinien mieć `rwx` uprawnienia do katalogu w magazynie i wszystkich podkatalogach.

* Zasady mogą być stosowane do grup (preferowany), a nie do poszczególnych użytkowników.

* Ranger autoryzowany oceni wszystkie zasady Ranger dla tej usługi dla każdego żądania. Ta ocena może mieć wpływ na czas zaakceptowania zadania lub zapytania.

### <a name="storage-access"></a>Dostęp do magazynu

* Jeśli typ magazynu to WASB, nie ma żadnego tokenu OAuth.
* Jeśli Ranger przeprowadził autoryzację, dostęp do magazynu odbywa się przy użyciu tożsamości zarządzanej.
* Jeśli Ranger nie wykonywał żadnej autoryzacji, dostęp do magazynu odbywa się przy użyciu tokenu OAuth użytkownika.

### <a name="hierarchical-name-space"></a>Hierarchiczna przestrzeń nazw

Gdy hierarchiczna przestrzeń nazw nie jest włączona:

* Brak dziedziczonych uprawnień.
* Tylko uprawnienie do systemu plików, które działa, to **dane magazynu xxxx** rolę platformy Azure, które mają być przypisane do użytkownika bezpośrednio w Azure Portal.

### <a name="default-hdfs-permissions"></a>Domyślne uprawnienia systemu HDFS

* Domyślnie użytkownicy nie mają dostępu do **/** folderu w systemie plików HDFS (muszą mieć rolę właściciela obiektu blob magazynu, aby uzyskać dostęp do programu).
* W przypadku katalogu przemieszczania dla MapReduce i innych elementów tworzony jest katalog specyficzny dla użytkownika i podano `sticky _wx` uprawnienia. Użytkownicy mogą tworzyć pliki i foldery poniżej, ale nie mogą przeglądać innych elementów.

### <a name="url-auth"></a>Uwierzytelnianie adresów URL

Jeśli jest włączone uwierzytelnianie adresu URL:

* Konfiguracja będzie zawierać informacje o tym, jakie prefiksy są objęte uwierzytelnianiem adresu URL (na przykład `adl://` ).
* Jeśli dostęp jest przeznaczony dla tego adresu URL, Ranger sprawdzi, czy użytkownik znajduje się na liście dozwolonych.
* Ranger nie sprawdza żadnych szczegółowych zasad.

## <a name="resource-groups"></a>Grupy zasobów

Użyj nowej grupy zasobów dla każdego klastra, aby można było rozróżnić zasoby klastra.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Sieciowych grup zabezpieczeń, zapory i Brama wewnętrzna

* Użyj sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do blokowania sieci wirtualnych.
* Używanie zapory do obsługi zasad dostępu wychodzącego.
* Użyj wewnętrznej bramy, która nie jest otwarta w publicznym Internecie.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft.

### <a name="policies"></a>Zasady

* Wyłącz zasady dostępu warunkowego przy użyciu zasad opartych na adresie IP. Wymaga to włączenia punktów końcowych usługi na sieci wirtualnych, w którym są wdrażane klastry. Jeśli używasz zewnętrznej usługi na potrzeby uwierzytelniania wieloskładnikowego (innego niż AAD), zasady oparte na adresie IP nie będą działały

* `AllowCloudPasswordValidation` zasady są wymagane dla użytkowników federacyjnych. Ponieważ Usługa HDInsight używa nazwy użytkownika/hasła bezpośrednio do uzyskiwania tokenów z usługi Azure AD, te zasady należy włączyć dla wszystkich użytkowników federacyjnych.

* Włącz punkty końcowe usługi, jeśli wymagane jest obejście dostępu warunkowego przy użyciu zaufanych adresów IP.

### <a name="groups"></a>Grupy

* Należy zawsze wdrażać klastry z grupą.
* Zarządzanie członkostwem w grupach przy użyciu usługi Azure AD (łatwiejsze niż próba zarządzania indywidualnymi usługami w klastrze).

### <a name="user-accounts"></a>Konta użytkowników

* Użyj unikatowego konta użytkownika dla każdego scenariusza. Na przykład użyj konta do zaimportowania, użyj innego dla zapytań lub innych zadań przetwarzania.
* Używaj opartych na grupach zasad Ranger zamiast poszczególnych zasad.
* Zaplanuj, jak zarządzać użytkownikami, którzy nie mają już dostępu do klastrów.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) oraz uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory.

Aby można było przyłączyć się do domeny, wymagane jest AD DS platformy Azure.
Usługa HDInsight nie może zależeć od lokalnych kontrolerów domeny ani niestandardowych kontrolerów domeny, ponieważ wprowadza zbyt wiele punktów błędów, udostępniania poświadczeń, uprawnień DNS i tak dalej. Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące usługi Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Wystąpienie usługi Azure AD DS

* Utwórz wystąpienie z `.onmicrosoft.com domain` . W ten sposób nie będzie można obsługiwać domeny wielu serwerów DNS.
* Utwórz certyfikat z podpisem własnym dla usług LDAPs i przekaż go do usługi Azure AD DS.
* Używanie równorzędnej sieci wirtualnej do wdrażania klastrów (w przypadku wielu zespołów, które wdrażają klastry usługi HDInsight ESP, będzie to pomocne). Dzięki temu nie trzeba otwierać portów (sieciowych grup zabezpieczeń) w sieci wirtualnej z kontrolerem domeny.
* Odpowiednio skonfiguruj system DNS dla sieci wirtualnej (nazwa domeny usługi Azure AD DS powinna zostać rozpoznana bez żadnych wpisów w pliku hosts).
* W przypadku ograniczenia ruchu wychodzącego upewnij się, że zainstalowano w usłudze [HDInsight obsługę zapory](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Właściwości zsynchronizowane z usługi Azure AD do usługi Azure AD DS

* Usługa Azure AD Connect synchronizuje się z lokalnego do usługi Azure AD.
* Usługa Azure AD DS synchronizuje się z usługą Azure AD.

Usługa Azure AD DS synchronizuje obiekty z usługi Azure AD okresowo. W bloku Azure AD DS w Azure Portal zostanie wyświetlony stan synchronizacji. Podczas każdego etapu synchronizacji unikatowe właściwości mogą ulec zmianie i zmienić ich nazwę. Zwróć uwagę na mapowanie właściwości z usługi Azure AD do AD DS platformy Azure.

Aby uzyskać więcej informacji, zobacz [populacja userPrincipalName usługi Azure AD](../../active-directory/hybrid/plan-connect-userprincipalname.md)i [sposób działania synchronizacji usługi Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Synchronizacja skrótów haseł

* Hasła są synchronizowane inaczej niż inne typy obiektów. Tylko nieodwracalne skróty haseł są synchronizowane w usłudze Azure AD i na platformie Azure AD DS
* Lokalna usługa Azure AD musi być włączona za pomocą programu AD Connect
* Usługa Azure AD do usługi Azure AD DS Sync jest automatyczna (opóźnienia są poniżej 20 minut).
* Skróty haseł są synchronizowane tylko w przypadku zmiany hasła. Po włączeniu synchronizacji skrótów haseł wszystkie istniejące hasła nie są synchronizowane automatycznie, ponieważ są one zapisywane nieodwracalnie. Gdy zmienisz hasło, skróty haseł są synchronizowane.

### <a name="computer-objects-location"></a>Lokalizacja obiektów komputera

Każdy klaster jest skojarzony z pojedynczą jednostką organizacyjną. Wewnętrzny użytkownik jest zainicjowany w jednostce organizacyjnej. Wszystkie węzły są przyłączone do tej samej jednostki organizacyjnej.

### <a name="active-directory-administrative-tools"></a>Active Directory narzędzia administracyjne

Aby uzyskać informacje na temat sposobu instalowania narzędzi administracyjnych Active Directory na maszynie wirtualnej z systemem Windows Server, zobacz [Instalowanie narzędzi do zarządzania](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cluster-creation-fails-repeatedly"></a>Tworzenie klastra kończy się niepowodzeniem

Najczęstsze przyczyny:

* Konfiguracja DNS jest nieprawidłowa, przyłączanie do domeny węzłów klastra kończy się niepowodzeniem.
* Sieciowych grup zabezpieczeń są zbyt restrykcyjne, uniemożliwiając przyłączanie do domeny.
* Tożsamość zarządzana nie ma wystarczających uprawnień.
* Nazwa klastra nie jest unikatowa w pierwszych sześciu znakach (z innym klastrem na żywo lub z usuniętym klastrem).

## <a name="authentication-setup-and-configuration"></a>Konfigurowanie i konfiguracja uwierzytelniania

### <a name="user-principal-name-upn"></a>Główna nazwa użytkownika (UPN)

* Użyj małych liter dla wszystkich usług — nazwy UPN nie uwzględniają wielkości liter w klastrach ESP, ale
* Prefiks nazwy UPN powinien pasować do obu wartości SAMAccountName w usłudze Azure AD — DS. Dopasowanie do pola poczty nie jest wymagane.

### <a name="ldap-properties-in-ambari-configuration"></a>Właściwości LDAP w konfiguracji Ambari

Aby uzyskać pełną listę właściwości Ambari, które wpływają na konfigurację klastra usługi HDInsight, zobacz [AMBARI LDAP Setup Authentication](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Następne kroki

* [pakiet Enterprise Security konfiguracji z Azure Active Directory Domain Services w usłudze HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Synchronizuj Azure Active Directory użytkowników z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
