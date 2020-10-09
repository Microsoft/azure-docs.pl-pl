---
title: Synchronizacja LDAP w Ranger i Apache Ambari w usłudze Azure HDInsight
description: Zaadresuj synchronizację LDAP w Ranger i Ambari i podaj ogólne wytyczne.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77463221"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronizacja LDAP w Ranger i Apache Ambari w usłudze Azure HDInsight

Klastry usługi HDInsight pakiet Enterprise Security (ESP) używają Ranger do autoryzacji. Usługi Apache Ambari i Ranger jednocześnie synchronizują użytkowników i grupy oraz działają nieco inaczej. Ten artykuł ma na celu rozwiązanie synchronizacji LDAP w Ranger i Ambari.

## <a name="general-guidelines"></a>Ogólne wskazówki

* Zawsze Wdrażaj klastry przy użyciu grup.
* Zamiast zmieniać filtry grupy w Ambari i Ranger, spróbuj zarządzać wszystkimi nimi w usłudze Azure AD i korzystać z grup zagnieżdżonych w celu przyłączenia wymaganych użytkowników.
* Gdy użytkownik zostanie zsynchronizowany, nie zostanie usunięty, nawet jeśli użytkownik nie jest częścią grup.
* Jeśli musisz bezpośrednio zmienić filtry LDAP, użyj interfejsu użytkownika w pierwszej kolejności, w jaki zawiera kilka walidacji.

## <a name="users-are-synced-separately"></a>Użytkownicy są synchronizowane osobno

Ambari i Ranger nie współdzielą bazy danych użytkownika, ponieważ służą one do dwóch różnych celów. Jeśli użytkownik musi korzystać z interfejsu użytkownika Ambari, należy synchronizować użytkownika z Ambari. Jeśli użytkownik nie jest synchronizowany z usługą Ambari, interfejs użytkownika/interfejs API usługi Ambari odrzuci ten element, ale inne części systemu będą działały (są one chronione przez Ranger lub Menedżer zasobów, a nie Ambari). Jeśli chcesz uwzględnić użytkownika w zasadach Ranger, zsynchronizuj użytkownika z Ranger.

Po wdrożeniu bezpiecznego klastra elementy członkowskie grupy są synchronizowane przechodniie (wszystkie podgrupy i ich członkowie) zarówno do Ambari, jak i Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Ambari i Konfiguracja użytkownika

W przypadku węzłów głównych zadanie firmy CRONUS `/opt/startup_scripts/start_ambari_ldap_sync.py` jest uruchamiane co godzinę w celu zaplanowania synchronizacji użytkownika. Zadanie firmy CRONUS wywołuje interfejsy API REST Ambari w celu wykonania synchronizacji. Skrypt przesyła listę użytkowników i grup do zsynchronizowania (ponieważ użytkownicy nie mogą należeć do określonych grup, oba są określone indywidualnie). Ambari synchronizuje sAMAccountName jako nazwę użytkownika i wszystkich członków grupy, przechodniie.

Dzienniki powinny znajdować się w `/var/log/ambari-server/ambari-server.log` . Aby uzyskać więcej informacji, zobacz [Konfigurowanie poziomu rejestrowania Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

W klastrach Data Lake punkt zaczepienia tworzenia użytkownika służy do tworzenia folderów macierzystych dla synchronizowanych użytkowników i są ustawiani jako właściciele folderów macierzystych. Jeśli użytkownik nie jest synchronizowany do Ambari prawidłowo, może to oznaczać błędy podczas uzyskiwania dostępu do przemieszczania i innych folderów tymczasowych.

### <a name="update-groups-to-be-synced-to-ambari"></a>Aktualizowanie grup do zsynchronizowania z Ambari

Jeśli nie możesz zarządzać członkostwem w grupach w usłudze Azure AD, masz dwie opcje:

* Wykonaj jednorazową synchronizację, tak jak opisano to w pełni w temacie [synchronizowanie użytkowników i grup LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Przy każdej zmianie członkostwa w grupie należy wykonać tę synchronizację ponownie.

* Napisz zadanie firmy CRONUS, aby [okresowo wywoływać interfejs API Ambari](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) przy użyciu nowych grup.

## <a name="ranger-user-sync-and-configuration"></a>Ranger i Konfiguracja użytkownika

Ranger ma wbudowany aparat synchronizacji, który jest uruchamiany co godzinę w celu synchronizacji użytkowników. Baza danych użytkownika nie jest współużytkowana z Ambari. Usługa HDInsight konfiguruje filtr wyszukiwania, aby synchronizować użytkownika administracyjnego, użytkownika licznika alarmowego i członków grupy określonej podczas tworzenia klastra. Elementy członkowskie grupy zostaną zsynchronizowane przechodniie:

* Wyłącz synchronizację przyrostową.
* Włącz mapę synchronizacji grupy użytkowników.
* Określ filtr wyszukiwania do uwzględnienia przechodnich elementów członkowskich grupy.
* Synchronizuj sAMAccountName dla użytkowników i nazw atrybutów dla grup.

### <a name="group-or-incremental-sync"></a>Grupa lub synchronizacja przyrostowa

Ranger obsługuje opcję synchronizacji grup, ale działa jako część wspólna z filtrem użytkownika. Nie jest to Unia między członkostwami w grupach a filtrem użytkownika. Typowy przypadek użycia filtru synchronizacji grupy w Ranger to-Group Filter: (DN = clusteradmingroup), filtr użytkownika: (Miasto = Seattle).

Synchronizacja przyrostowa działa tylko dla użytkowników, którzy są już zsynchronizowani (po raz pierwszy). Przyrostowy nie będzie synchronizować żadnych nowych użytkowników dodanych do grup po synchronizacji początkowej.

### <a name="update-ranger-sync-filter"></a>Aktualizuj filtr synchronizacji Ranger

Filtr LDAP można znaleźć w interfejsie użytkownika Ambari w sekcji Konfiguracja użytkownika Ranger. Istniejący filtr będzie miał postać `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Upewnij się, że dodasz predykat na końcu i testujesz filtr, używając `net ads` polecenia Search lub ldp.exe lub czegoś podobnego.

## <a name="ranger-user-sync-logs"></a>Ranger dzienniki synchronizacji użytkowników

Ranger synchronizacji użytkownika może się zdarzyć z jednej z węzłów głównych. Dzienniki znajdują się w `/var/log/ranger/usersync/usersync.log` . Aby zwiększyć szczegółowość dzienników, wykonaj następujące czynności:

1. Zaloguj się do Ambari.
1. Przejdź do sekcji Konfiguracja Ranger.
1. Przejdź do sekcji Advanced **usersync-Log4J** .
1. Zmień `log4j.rootLogger` poziom na `DEBUG` (po zmianie powinien wyglądać tak `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Zapisz konfigurację i uruchom ponownie Ranger.

## <a name="next-steps"></a>Następne kroki

* [Problemy z uwierzytelnianiem w usłudze Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronizowanie użytkowników usługi Azure AD z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
