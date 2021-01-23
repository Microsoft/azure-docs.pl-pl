---
title: Wprowadzenie do narzędzia migawek spójnych w aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera Przewodnik dotyczący instalowania narzędzia migawek spójnych w aplikacji Azure, którego można używać z Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736366"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Wprowadzenie do narzędzia migawek spójnych w aplikacji platformy Azure (wersja zapoznawcza)

Ten artykuł zawiera Przewodnik dotyczący instalowania narzędzia do tworzenia migawek spójnych w aplikacji Azure, których można użyć z Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>Pobieranie narzędzi do tworzenia migawek

Zaleca się, aby klienci mogli pobrać najnowszą wersję [Instalatora AzAcSnap](https://aka.ms/azacsnapdownload) z firmy Microsoft.

Plik samoinstalacyjnego ma skojarzony [plik sygnatury Instalatora AzAcSnap](https://aka.ms/azacsnapdownloadsignature) , który jest podpisany przy użyciu klucza publicznego firmy Microsoft, aby umożliwić weryfikację GPG pobranego instalatora.

Po zakończeniu tych operacji pobierania postępuj zgodnie z instrukcjami w tym przewodniku, aby zainstalować program.

### <a name="verifying-the-download"></a>Weryfikowanie pobierania

Instalator, który można pobrać z góry, ma skojarzony plik sygnatury PGP z `.asc` rozszerzeniem nazwy pliku. Ten plik może służyć do upewnienia się, że pobrany Instalator jest zweryfikowanym plikiem dostarczonym przez firmę Microsoft. Klucz publiczny Microsoft PGP używany do podpisywania pakietów systemu Linux jest dostępny tutaj ( <https://packages.microsoft.com/keys/microsoft.asc> ) i został użyty do podpisania pliku podpisu.

Klucz publiczny Microsoft PGP można zaimportować do lokalnego użytkownika w następujący sposób:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Następujące polecenia ufają kluczowi publicznemu PGP firmy Microsoft:

1. Wyświetl listę kluczy w sklepie.
2. Edytuj klucz firmy Microsoft.
3. Zaznacz odcisk palca `fpr`
4. Podpisz klucz, aby go zaufać.

```bash
gpg --list-keys
```

Wymienione klucze:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Dane wyjściowe z `gpg` podpisywania sesji interaktywnej firmy Microsoft:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Plik podpisu PGP dla Instalatora można sprawdzić w następujący sposób:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Aby uzyskać więcej informacji o korzystaniu z GPG, zobacz [Podręcznik programu GNU Privacy](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Narzędzia migawek można używać w następujących scenariuszach.

- Pojedynczy identyfikator SID
- Wiele identyfikatorów SID
- HSR
- Skalowanie w poziomie
- MDC (obsługiwana jest tylko jedna dzierżawa)
- Jeden kontener
- System operacyjny SUSE
- System operacyjny RHEL
- TYP JEDNOSTKI SKU I
- TYP JEDNOSTKI SKU II

Zobacz [obsługiwane scenariusze dla dużych wystąpień Hana](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Macierz obsługi migawek z oprogramowania SAP

W poniższej macierzy przedstawiono wskazówki dotyczące wersji SAP HANA obsługiwanych przez oprogramowanie SAP na potrzeby tworzenia kopii zapasowych migawek magazynu.

| Wersje bazy danych       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Baza danych jednego kontenera| √       | √      | -      | -      | -      | -      |
|MDC pojedynczej dzierżawy        | -       | -      | √      | √      | √      | √      |
|MDC wielu dzierżawców     | -       | -      | -      | -      | -      | √      |
> √ = <small>obsługiwane przez SAP dla migawek magazynu</small>

## <a name="important-things-to-remember"></a>Ważne rzeczy do zapamiętania

- Po zakończeniu instalacji narzędzi do tworzenia migawek stale Monitoruj dostępne miejsce do magazynowania i w razie potrzeby usuń stare migawki, aby uniknąć wypełniania miejsca w magazynie.
- Zawsze używaj najnowszych narzędzi do tworzenia migawek.
- Użyj tej samej wersji narzędzi do tworzenia migawek na poziomie.
- Przetestuj narzędzia migawek i poproś o parametry wymagane i wyjściowe polecenia przed użyciem w systemie produkcyjnym.
- Podczas konfigurowania użytkownika platformy HANA na potrzeby tworzenia kopii zapasowych (szczegóły poniżej w tym dokumencie) należy skonfigurować użytkownika dla każdego wystąpienia platformy HANA. Utwórz konto użytkownika SAP HANA, aby uzyskać dostęp do wystąpienia platformy HANA w SYSTEMDB (a nie w bazie danych SID) dla MDC. W środowisku jednego kontenera można go skonfigurować w ramach bazy danych dzierżawy.
- Klienci muszą podać klucz publiczny SSH dla dostępu do magazynu. Ta akcja musi zostać wykonana raz na węzeł i dla każdego użytkownika, w którym jest wykonywane polecenie.
- Liczba migawek na wolumin jest ograniczona do 250.
- Jeśli ręcznie edytujesz plik konfiguracji, zawsze używaj edytora tekstów systemu Linux, takiego jak "VI", a nie edytorów Windows, takich jak Notatnik. Użycie edytora systemu Windows może spowodować uszkodzenie formatu pliku.
  - Skonfiguruj `hdbuserstore` SAP HANA użytkownika do komunikowania się z SAP HANA.
- W przypadku odzyskiwania po awarii: narzędzia migawek muszą zostać przetestowane w węźle DR przed skonfigurowaniem usługi DR.
- Regularnie Monitoruj miejsce na dysku, automatyczne usuwanie dzienników jest zarządzane z `--trim` opcją   `azacsnap -c backup` dla SAP HANA 2 i nowszych wersji.
- **Ryzyko braku migawek** — narzędzia migawek współpracują z węzłem systemu SAP HANA określonym w pliku konfiguracji.  Jeśli ten węzeł przestanie być dostępny, nie ma mechanizmu automatycznego uruchamiania komunikacji z innym węzłem.  
  - W przypadku **SAP HANA Scale-Out ze scenariuszem gotowości** typowym jest zainstalowanie i skonfigurowanie narzędzi migawek w węźle głównym. Jeśli jednak węzeł główny stanie się niedostępny, węzeł w stanie wstrzymania przejdzie przez rolę węzła głównego. W takim przypadku zespół implementacji powinien skonfigurować narzędzia migawek w obu węzłach (Master i sub), aby uniknąć brakujących migawek. W normalnym stanie węzeł główny zajmie się migawkami HANA zainicjowanymi przez crontab, ale po przejściu węzła głównego do trybu failover te migawki będą musiały zostać wykonane z innego węzła, takiego jak nowy węzeł główny (poprzedni stan gotowości). Aby osiągnąć ten wynik, w węźle w stanie wstrzymania będzie wymagane zainstalowanie narzędzia Snapshot Tool, włączona jest funkcja komunikacji magazynu, hdbuserstore skonfigurowane, `azacsnap.json` skonfigurowane i crontab polecenia z wyprzedzeniem.
  - W przypadku scenariusza **SAP HANA ha HSR** zaleca się zainstalowanie, skonfigurowanie i zaplanowanie narzędzi migawek na obu węzłach (podstawowych i pomocniczych). Następnie, jeśli węzeł podstawowy przestanie być dostępny, węzeł pomocniczy przejdzie z migawek na pomocniczym. W normalnym stanie węzeł podstawowy zajmie się migawkami HANA zainicjowanymi przez crontab, a węzeł pomocniczy podejmie próbę wykonania migawek, ale nie powiedzie się, ponieważ podstawowy działa poprawnie.  Jednak po przejściu do trybu failover węzła podstawowego migawki te zostaną wykonane z węzła pomocniczego. Aby osiągnąć ten wynik, węzeł pomocniczy wymaga zainstalowanego narzędzia Snapshot, komunikacji magazynu, `hdbuserstore` skonfigurowania, azacsnap.jsna skonfigurowane i crontab z wyprzedzeniem.

## <a name="guidance-provided-in-this-document"></a>Wskazówki zawarte w tym dokumencie

Poniższe wskazówki pomogą zilustrować użycie narzędzi do tworzenia migawek.

### <a name="taking-snapshot-backups"></a>Tworzenie kopii zapasowych migawek

- [Jakie są wymagania wstępne dla migawki magazynu](azacsnap-installation.md#prerequisites-for-installation)
  - [Włączanie komunikacji z magazynem](azacsnap-installation.md#enable-communication-with-storage)
  - [Włącz komunikację z SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Jak ręcznie wykonać migawki](azacsnap-tips.md#take-snapshots-manually)
- [Jak skonfigurować automatyczną kopię zapasową migawek](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Monitorowanie migawek](azacsnap-tips.md#monitor-the-snapshots)
- [Jak usunąć migawkę?](azacsnap-tips.md#delete-a-snapshot)
- [Jak przywrócić migawkę](azacsnap-tips.md#restore-a-snapshot)
- [Jak przywrócić `boot` migawkę](azacsnap-tips.md#restore-a-boot-snapshot)
- [Co to są kluczowe fakty dotyczące migawek](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Migawki są testowane pod kątem pojedynczego identyfikatora SID i identyfikatora wieloidentyfikatorowego.

### <a name="performing-disaster-recovery"></a>Odzyskiwanie po awarii

- [Jakie są wymagania wstępne dotyczące instalacji programu DR](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Jak skonfigurować odzyskiwanie po awarii](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Jak monitorować replikację danych z lokacji podstawowej do usługi DR](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Jak przeprowadzić pracę w trybie failover w lokacji DR?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Następne kroki

- [Zainstaluj narzędzie migawki spójnej aplikacji platformy Azure](azacsnap-installation.md)