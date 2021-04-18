---
title: Funkcje wysokiej dostępności dla Oracle na platformie Azure BareMetal
description: Dowiedz się więcej o funkcjach dostępnych w programie BareMetal dla bazy danych Oracle.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/16/2021
ms.openlocfilehash: b27dc4b857d553be791528cbd91aee70b2294a92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600220"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Funkcje wysokiej dostępności dla Oracle na platformie Azure BareMetal

W tym artykule przyjrzymy się kluczowym funkcjiom wysokiej dostępności i odzyskiwania po awarii firmy Oracle.

Firma Oracle oferuje wiele funkcji do tworzenia odpornej platformy do uruchamiania baz danych Oracle. Chociaż żadna pojedyncza funkcja nie zapewnia pokrycia dla każdego typu awarii, łączenie technologii w sposób warstwowy tworzy system o wysokiej dostępnej jakości. Nie każda funkcja jest wymagana do zachowania dostępności. Jednak połączenie strategii zapewnia najlepszą ochronę przed różnego rodzaju awariami. 

## <a name="flashback-database"></a>Flashback Database

Funkcja [Flashback Database](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) jest dostępna w Oracle Database Enterprise Edition. Flashback Database (Flashback Database) przewija bazę danych do określonego punktu w czasie. Ta funkcja różni się od odzyskiwania do punktu [w Recovery Manager (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) tym, że przewija się do tyłu od bieżącego czasu, a nie z wiatrem do przodu po przywróceniu. W rezultacie flashback database zapewnia znacznie krótszy czas ukończenia.
 
Tej funkcji można używać razem z [funkcją Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590) Flashback Database umożliwia administratorowi bazy danych ponowne tworzenie bazy danych, która zakończyła się niepowodzeniem, z powrotem do konfiguracji ochrony danych bez pełnego przywracania i odzyskiwania przy pomocy RMAN. Ta funkcja umożliwia znacznie szybsze przywracanie możliwości odzyskiwania po awarii (oraz wszelkich odciążanych korzyści związanych z raportowaniem i tworzeniem kopii zapasowych za pomocą funkcji Active Data Guard).
 
Tej funkcji można używać zamiast ponownego, opóźnionego w czasie ponownego użycia w rezerwowej bazie danych. Rezerwowa baza danych może zostać z powrotem flashowana do punktu, zanim problem się pojawił.
 
Ta Oracle Database przechowuje dzienniki flashback w obszarze szybkiego odzyskiwania (FRA). Te dzienniki są oddzielone od dzienników ponownego ponownego logowania i wymagają więcej miejsca w ramach modelu FRA. Domyślnie są przechowywane 24 godziny dzienników flashback, ale możesz zmienić to ustawienie na swoje wymagania.

## <a name="oracle-real-application-clusters"></a>Oracle Real Application Clusters

[Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) umożliwia wielu połączonym serwerom pojawianie się jako jedna usługa bazy danych dla użytkowników końcowych i aplikacji. Ta funkcja usuwa wiele punktów awarii i jest rozpoznawanym aktywnym/aktywnym rozwiązaniem o wysokiej dostępności dla baz danych Oracle.

Jak pokazano na poniższej ilustracji z tematu High [Availability Overview and Best Practices](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)(Omówienie wysokiej dostępności i najlepsze rozwiązania firmy Oracle), pojedyncza baza danych RAC jest przedstawiana w warstwie aplikacji. Aplikacje łączą się z odbiornikem SCAN, który kieruje ruch do określonego wystąpienia bazy danych. Mechanizm RAC kontroluje dostęp z wielu wystąpień w celu zachowania spójności danych między oddzielnymi węzłami obliczeniowymi.

![Diagram przedstawiający przegląd architektury oprogramowania Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Jeśli jedno wystąpienie ulegnie awarii, usługa będzie kontynuować pracę we wszystkich pozostałych wystąpieniach. Każda baza danych wdrożona w rozwiązaniu będzie w konfiguracji RAC n+1, gdzie n to minimalna moc przetwarzania wymagana do obsługi usługi.

Oracle Database są używane do zezwalania na połączenia w celu trybu fail over między węzłami, gdy wystąpienie ulegnie awarii w sposób niewidoczny. Takie awarie mogą być planowane lub nieplanowane. Praca z aplikacją (zdarzenia szybkiego powiadamiania o aplikacji), gdy wystąpienie jest niedostępne, usługa jest przenoszony do ocalałego węzła. Usługa przechodzi do węzła określonego w konfiguracji usługi jako preferowany lub dostępny.

Kolejną kluczową funkcją Oracle Database usług jest uruchamianie usługi tylko w zależności od jej roli. Ta funkcja jest używana w przypadku trybu failover funkcji Data Guard. Wszystkie wzorce wdrożone przy użyciu funkcji Data Guard są wymagane do połączenia usługi bazy danych z rolą funkcji Data Guard.

Na przykład można utworzyć dwie usługi: MY \_ DB APP i MY DB \_ \_ \_ AS. Usługa APP MOJA BAZA DANYCH jest uruchomiona tylko wtedy, gdy wystąpienie bazy danych jest uruchomione z \_ \_ rolą Ochrony danych PRIMARY. Stan \_ MY DB AS jest uruchomiony tylko \_ wtedy, gdy rola funkcji Data Guard jest W STANIE \_ WSTRZYMANIA FIZYCZNEGO. Ta konfiguracja umożliwia aplikacjom wskazywanie usługi APP, a także raportowanie, które można odciążyć do stanu wstrzymania aktywnego i \_ wskazać na \_ usługę AS.

## <a name="oracle-data-guard"></a>Oracle Data Guard

Za pomocą ochrony danych można zachować identyczną kopię bazy danych na oddzielnym sprzęcie fizycznym. Najlepiej, aby sprzęt został geograficznie usunięty z podstawowej bazy danych. Ochrona danych nie ogranicza odległości, chociaż odległość ma wpływ na tryby ochrony. Zwiększona odległość zwiększa opóźnienie między lokacjami, co może spowodować, że niektóre opcje (takie jak replikacja synchroniczna) nie będą już możliwe.

Ochrona danych oferuje przewagę nad replikacją na poziomie magazynu:

- Ponieważ replikacja ma świadomość bazy danych, replikowany jest tylko odpowiedni ruch.
- Niektóre obciążenia mogą generować duże ilości danych wejściowych/wyjściowych w tymczasowych przestrzeniach tabel, które nie są wymagane w stanie wstrzymania i dlatego nie są replikowane.
- Walidacja replikowanych bloków odbywa się w rezerwowej bazie danych, więc fizyczne uszkodzenia podstawowej bazy danych nie są replikowane do rezerwowej bazy danych.
- Zapobiega logicznym uszkodzeniem wewnątrz bloku i uszkodzeniem utraconego zapisu. Eliminuje to również ryzyko błędów popełnione przez administratorów magazynu z replikacji do stanu wstrzymania.
Ponowne wynoszenie może być opóźnione o wstępnie określony okres, więc błędy użytkownika nie są natychmiast replikowane do stanu wstrzymania.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files migawki

Rozwiązanie magazynu NetApp Files używane w programie BareMetal umożliwia tworzenie migawek woluminów. Migawki umożliwiają szybkie przywrócenie systemu plików do określonego punktu w czasie. Technologie migawek umożliwiają przywracanie kopii zapasowej bazy danych przez ułamek czasu potrzebnego do przywrócenia celu czasu odzyskiwania (RTO, recovery time objective).

Funkcja migawek dla baz danych Oracle jest dostępna za pośrednictwem programu Azure NetApp SnapCenter. Program SnapCenter umożliwia tworzenie migawek dla kopii zapasowych, usługa SnapVault udostępnia magazyn w trybie offline, a funkcja przyciągania umożliwia przywracanie samoobsługowe i wykonywanie innych operacji.

## <a name="recovery-manager"></a>Recovery Manager

Recovery Manager (RMAN) jest preferowanym narzędziem do tworzenia kopii zapasowych fizycznej bazy danych. Usługa RMAN współdziała z plikiem kontroli bazy danych (lub scentralizowanym wykazem odzyskiwania) w celu ochrony różnych podstawowych składników bazy danych, w tym:

- Pliki danych bazy danych
- Zarchiwizowane dzienniki ponownego śledzenia
- Pliki sterujące bazy danych
- Pliki inicjowania bazy danych (spfile)

Funkcja RMAN umożliwia wykonywanie kopii zapasowych gorącej lub zimnej bazy danych. Za pomocą tych kopii zapasowych można tworzyć rezerwowe bazy danych lub duplikować bazy danych w celu klonowania środowisk. RMAN ma również funkcję weryfikacji przywracania. Ta funkcja odczytuje zestaw kopii zapasowych i określa, czy można go użyć do odzyskania bazy danych do określonego punktu w czasie.

Ponieważ RMAN jest narzędziem dostarczanym przez firmę Oracle, odczytuje wewnętrzną strukturę plików bazy danych. Umożliwia to uruchamianie fizycznych i logicznych kontroli uszkodzeń podczas operacji tworzenia kopii zapasowej i przywracania. Można również odzyskać pliki danych bazy danych i przywrócić poszczególne pliki danych i przestrzenie tabel do określonego punktu w czasie. Są to zalety ofert RMAN w skojarzeń z migawkami magazynu. Kopie zapasowe RMAN zapewniają ostatnią linię obrony przed pełną utratą danych, gdy nie można używać migawek.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o opcjach i zaleceniach dotyczących optymalizacji ochrony i wydajności środowiska Oracle w infrastrukturze BareMetal:

> [!div class="nextstepaction"]
> [Opcje dla serwerów infrastruktury Oracle BareMetal](options-considerations-high-availability.md)
