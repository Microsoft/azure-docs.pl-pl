---
title: Co to jest narzędzie migawek spójnych w aplikacji Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera wprowadzenie do narzędzia do tworzenia migawek spójnych w aplikacji Azure, których można używać z Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632824"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Co to jest narzędzie do tworzenia migawek spójnych w aplikacji Azure (wersja zapoznawcza)

Narzędzie do tworzenia migawek spójnych w aplikacji Azure (AzAcSnap) to narzędzie wiersza polecenia, które umożliwia uproszczenie ochrony danych dla baz danych innych firm (SAP HANA) w środowiskach systemu Linux (na przykład SUSE i RHEL).  

## <a name="benefits-of-using-azacsnap"></a>Zalety korzystania z usługi AzAcSnap

AzAcSnap korzysta z funkcji migawek woluminu i replikacji w Azure NetApp Files i dużym wystąpieniu platformy Azure.  Zapewnia następujące korzyści:

- **Ochrona danych spójna** na poziomie aplikacji AzAcSnap to scentralizowane rozwiązanie do tworzenia kopii zapasowych krytycznych plików bazy danych. Zapewnia to spójność bazy danych przed wykonaniem migawki woluminu magazynu. Z tego powodu gwarantuje to, że migawka woluminu magazynu może być używana do odzyskiwania bazy danych.
- **Zarządzanie katalogiem bazy danych** W przypadku korzystania z programu AzAcSnap z bazą danych, która ma wbudowany wykaz kopii zapasowych, rekordy znajdujące się w wykazie są przechowywane na bieżąco z migawkami magazynu.  Ta funkcja umożliwia administratorowi bazy danych wyświetlanie działania tworzenia kopii zapasowej.
- **Ochrona woluminów ad hoc** Ta funkcja jest przydatna w przypadku woluminów nieopartych na bazie danych, które nie wymagają przełączania aplikacji w stan spoczynku przed rozpoczęciem tworzenia migawki magazynu.  Przykłady SAP HANA obejmują woluminy z SAPTRANS dzienników lub woluminów kopii zapasowych.
- **Klonowanie woluminów magazynu** Ta funkcja zapewnia wydajną przestrzeń dyskową klonowania na potrzeby tworzenia i testowania.
- **Obsługa odzyskiwania po awarii** AzAcSnap wykorzystuje replikację woluminu magazynu w celu zapewnienia opcji odzyskiwania zreplikowanych migawek spójnych na poziomie aplikacji w lokacji zdalnej.

AzAcSnap to pojedynczy plik binarny.  Nie potrzebują dodatkowych agentów ani wtyczek do współdziałania z bazą danych ani magazynem (Azure NetApp Files za pośrednictwem Azure Resource Manager i dużego wystąpienia platformy Azure za pośrednictwem protokołu SSH).  AzAcSnap musi być zainstalowany w systemie, który ma łączność z bazą danych i magazynem.  Jednak elastyczność instalacji i konfiguracji umożliwia zainstalowanie jednej centralnej instalacji lub w pełni rozproszonej instalacji z kopiami zainstalowanymi w każdej instalacji bazy danych.

## <a name="architecture-overview"></a>Omówienie architektury

AzAcSnap można zainstalować na tym samym hoście co baza danych (SAP HANA) lub można go zainstalować w scentralizowanym systemie.  Należy jednak mieć łączność sieciową z serwerami baz danych i zapleczem magazynu (Azure Resource Manager dla Azure NetApp Files lub protokołu SSH dla dużych wystąpień platformy Azure).

AzAcSnap to lekkie aplikacje, które zwykle są wykonywane z zewnętrznego harmonogramu.  W większości systemów Linux ta operacja jest `cron` , na której będzie koncentrować się dokumentacja.  Jednak harmonogram może być alternatywnym narzędziem, o ile może zaimportować `azacsnap` profil powłoki użytkownika.  Zaimportowanie ustawień środowiska użytkownika gwarantuje, że ścieżki i uprawnienia plików są inicjowane prawidłowo.

## <a name="command-synopsis"></a>Polecenie streszczenia

Ogólny format poleceń jest następujący: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Opcje polecenia

Opcje polecenia są następujące, jak w przypadku poleceń jako główne punktory i skojarzone podpolecenia jako wcięcia wcięć:

- **`-h`** zawiera rozszerzoną pomoc wiersza polecenia z przykładami dotyczącymi użycia AzAcSnap.
- **`-c configure`** To polecenie udostępnia interaktywną p&interfejsem stylu do tworzenia lub modyfikowania `azacsnap` pliku konfiguracji (domyślnie = `azacsnap.json` ).
  - **`--configuration new`** utworzy nowy plik konfiguracji.
  - **`--configuration edit`** zmodyfikuje istniejący plik konfiguracji.
  - Zapoznaj się z tematem [Konfigurowanie odwołania do polecenia](azacsnap-cmd-ref-configure.md).
- **`-c test`** sprawdza poprawność pliku konfiguracji i testowania łączności.
  - **`--test hana`**  testuje połączenie z wystąpieniem SAP HANA.
  - **`--test storage`** testuje komunikację z podstawowym interfejsem magazynu, tworząc tymczasową migawkę magazynu na wszystkich skonfigurowanych `data` woluminach, a następnie usuwając je.
  - **`--test all`** wykonuje zarówno `hana` testy, jak i `storage` sekwencję.
  - Zapoznaj się z informacjami dotyczącymi [poleceń testowych](azacsnap-cmd-ref-test.md).
- **`-c backup`** jest podstawowym poleceniem do wykonywania migawek magazynu spójnego z bazą danych dla danych (SAP HANA woluminów danych) & innych (na przykład udostępnione, kopie zapasowe dzienników lub rozruch) woluminów.
  - **`--volume data`** Aby wykonać migawkę wszystkich woluminów w `dataVolume` Stanza pliku konfiguracyjnego.
  - **`--volume other`** Aby wykonać migawkę wszystkich woluminów w `otherVolume` Stanza pliku konfiguracyjnego.
  - Zapoznaj się z informacjami dotyczącymi [poleceń kopii zapasowych](azacsnap-cmd-ref-backup.md).
- **`-c details`** zawiera informacje dotyczące migawek lub replikacji.
  - **`--details snapshots`** Zawiera listę podstawowych informacji na temat migawek dla każdego skonfigurowanego woluminu.
  - **`--details replication`** Zawiera podstawowe informacje o stanie replikacji z lokacji produkcyjnej do lokacji odzyskiwania po awarii.
  - Zapoznaj się z [informacjami dotyczącymi poleceń szczegóły](azacsnap-cmd-ref-details.md).
- **`-c delete`** To polecenie powoduje usunięcie migawki magazynu lub zestawu migawek. Można użyć SAP HANA identyfikator kopii zapasowej, który znajduje się w programie HANA Studio lub nazwie migawki magazynu. Identyfikator kopii zapasowej jest powiązany tylko z `hana` migawkami, które są tworzone dla woluminów danych i udostępnionych. W przeciwnym razie, jeśli nazwa migawki zostanie wprowadzona, szuka wszystkich migawek, które pasują do podanej nazwy migawki.
  - Zobacz [delete](azacsnap-cmd-ref-delete.md).
- **`-c restore`** Program udostępnia dwie metody przywracania migawki do woluminu przez utworzenie nowego woluminu na podstawie migawki lub wycofanie woluminu do stanu wersji zapoznawczej.
  - **`--restore snaptovol`** Tworzy nowy wolumin oparty na najnowszej migawce na woluminie docelowym.
  - **`-c restore --restore revertvolume`** Przywraca wolumin docelowy do poprzedniego stanu w oparciu o najnowszą migawkę.
  - Zapoznaj się z dokumentacją [polecenia przywracania](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** Opcjonalny parametr wiersza polecenia, aby podać inną nazwę pliku konfiguracji JSON.  Jest to szczególnie przydatne w przypadku tworzenia oddzielnego pliku konfiguracji dla identyfikatora SID (np `--configfile H80.json` .).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do narzędzia migawek spójnych z aplikacją platformy Azure](azacsnap-get-started.md)
