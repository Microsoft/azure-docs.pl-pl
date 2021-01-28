---
title: Synchronizacja LDAP w Ranger i Apache Ambari w usłudze Azure HDInsight
description: Zaadresuj synchronizację LDAP w Ranger i Ambari i podaj ogólne wytyczne.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933419"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronizacja LDAP w Ranger i Apache Ambari w usłudze Azure HDInsight

Klastry usługi HDInsight pakiet Enterprise Security (ESP) używają Ranger do autoryzacji. Usługi Apache Ambari i Ranger jednocześnie synchronizują użytkowników i grupy oraz działają nieco inaczej. Ten artykuł ma na celu rozwiązanie synchronizacji LDAP w Ranger i Ambari.

## <a name="general-guidelines"></a>Ogólne wskazówki

* Należy zawsze wdrażać klastry z co najmniej jedną grupą.
* Jeśli chcesz używać więcej grup w klastrze, sprawdź, czy warto zaktualizować członkostwa w grupach w Azure Active Directory (Azure AD).
* Jeśli chcesz zmienić grupy klastrów, możesz zmienić filtry synchronizacji przy użyciu Ambari.
* Wszystkie zmiany członkostwa w grupie w usłudze Azure AD są odzwierciedlone w klastrze podczas kolejnych synchronizacji. Zmiany muszą zostać najpierw zsynchronizowane w celu Azure AD Domain Services (Azure AD DS), a następnie do klastrów.
* Klastry usługi HDInsight używają oprogramowania Samba/winbind do projekcji członkostw w grupach w węzłach klastra.
* Elementy członkowskie grupy są synchronizowane przechodniie (wszystkie podgrupy i ich członkowie) zarówno w Ambari, jak i w Ranger. 

## <a name="users-are-synced-separately"></a>Użytkownicy są synchronizowane osobno

 * Ambari i Ranger nie współdzielą bazy danych użytkownika, ponieważ służą one do dwóch różnych celów. 
   * Jeśli użytkownik musi korzystać z interfejsu użytkownika Ambari, użytkownik musi zostać zsynchronizowany z Ambari. 
   * Jeśli użytkownik nie zostanie zsynchronizowany z usługą Ambari, interfejs użytkownika Ambari/interfejs API zostanie odrzucony, ale inne części systemu będą działały (są chronione przez Ranger lub Menedżer zasobów, a nie przez Ambari).
   * Aby uwzględnić użytkowników lub grupy w zasadach Ranger, podmioty zabezpieczeń muszą być jawnie zsynchronizowane w Ranger.

## <a name="ambari-user-sync-and-configuration"></a>Ambari i Konfiguracja użytkownika

W przypadku węzłów głównych zadanie firmy CRONUS `/opt/startup_scripts/start_ambari_ldap_sync.py` jest uruchamiane co godzinę w celu zaplanowania synchronizacji użytkownika. Zadanie firmy CRONUS wywołuje interfejsy API REST Ambari w celu wykonania synchronizacji. Skrypt przesyła listę użytkowników i grup do zsynchronizowania (ponieważ użytkownicy nie mogą należeć do określonych grup, oba są określone indywidualnie). Ambari synchronizuje sAMAccountName jako nazwę użytkownika i wszystkich członków grupy, przechodniie.

Dzienniki powinny znajdować się w `/var/log/ambari-server/ambari-server.log` . Aby uzyskać więcej informacji, zobacz [Konfigurowanie poziomu rejestrowania Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

W klastrach Data Lake punkt zaczepienia tworzenia użytkownika służy do tworzenia folderów macierzystych dla synchronizowanych użytkowników i są ustawiani jako właściciele folderów macierzystych. Jeśli użytkownik nie zostanie zsynchronizowany w celu poprawnego Ambari, może to oznaczać błędy w uruchomionych zadaniach, ponieważ folder macierzysty może nie być poprawnie skonfigurowany.

## <a name="ranger-user-sync-and-configuration"></a>Ranger i Konfiguracja użytkownika

Ranger ma wbudowany aparat synchronizacji, który jest uruchamiany co godzinę w celu synchronizacji użytkowników. Baza danych użytkownika nie jest współużytkowana z Ambari. Usługa HDInsight konfiguruje filtr wyszukiwania, aby synchronizować użytkownika administracyjnego, użytkownika licznika alarmowego i członków grupy określonej podczas tworzenia klastra. Elementy członkowskie grupy zostaną zsynchronizowane przechodniie:

1. Wyłącz synchronizację przyrostową.
1. Włącz mapę synchronizacji grupy użytkowników.
1. Określ filtr wyszukiwania do uwzględnienia przechodnich elementów członkowskich grupy.
1. Zsynchronizuj atrybut sAMAccountName dla użytkowników i atrybut nazwy dla grup.

### <a name="group-or-incremental-sync"></a>Grupa lub synchronizacja przyrostowa

Ranger obsługuje opcję synchronizacji grup, ale działa jako część wspólna z filtrem użytkownika, a nie jako Unię między członkostwami w grupach i filtrem użytkownika. Typowy przypadek użycia filtru synchronizacji grupy w Ranger to-Group Filter: (DN = clusteradmingroup), filtr użytkownika: (Miasto = Seattle).

Synchronizacja przyrostowa działa tylko dla użytkowników, którzy są już zsynchronizowani (po raz pierwszy). Przyrostowy nie będzie synchronizować żadnych nowych użytkowników dodanych do grup po synchronizacji początkowej.

### <a name="update-ranger-sync-filter"></a>Aktualizuj filtr synchronizacji Ranger

Filtr LDAP można znaleźć w interfejsie użytkownika Ambari w sekcji Konfiguracja użytkownika Ranger. Istniejący filtr będzie miał postać `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Upewnij się, że dodasz predykat na końcu i testujesz filtr, używając `net ads` polecenia Search lub ldp.exe lub czegoś podobnego.

## <a name="ranger-user-sync-logs"></a>Ranger dzienniki synchronizacji użytkowników

Ranger synchronizacji użytkownika może się zdarzyć z jednej z węzłów głównych. Dzienniki znajdują się w `/var/log/ranger/usersync/usersync.log` . Aby zwiększyć szczegółowość dzienników, wykonaj następujące czynności:

1. Zaloguj się do Ambari.
1. Przejdź do sekcji Konfiguracja Ranger.
1. Przejdź do sekcji Advanced **usersync-Log4J** .
1. Zmień `log4j.rootLogger` poziom na `DEBUG` . (Po jej zmianie powinna wyglądać jak `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Zapisz konfigurację i uruchom ponownie Ranger.

## <a name="known-issues-with-ranger-user-sync"></a>Znane problemy związane z synchronizacją użytkownika Ranger
* Jeśli nazwa grupy zawiera znaki Unicode, synchronizacja Ranger nie powiedzie się. Jeśli użytkownik należy do grupy, która ma znaki międzynarodowe, Ranger synchronizuje członkostwo w grupie częściowej
* Nazwa użytkownika (sAMAccountName) i nazwa grupy (nazwa) muszą mieć co najwyżej 20 znaków. Jeśli nazwa grupy jest dłuższa, wówczas użytkownik będzie traktowany jak, jeśli nie należą do grupy, podczas obliczania uprawnień.

## <a name="next-steps"></a>Następne kroki

* [Problemy z uwierzytelnianiem w usłudze Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronizowanie użytkowników usługi Azure AD z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
