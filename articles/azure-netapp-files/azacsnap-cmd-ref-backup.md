---
title: Tworzenie kopii zapasowej za pomocą narzędzia migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera Przewodnik dotyczący uruchamiania polecenia Backup narzędzia migawek spójnej na platformie Azure, którego można używać z Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730942"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Tworzenie kopii zapasowej za pomocą narzędzia migawek spójnej na platformie Azure (wersja zapoznawcza)

Ten artykuł zawiera Przewodnik dotyczący uruchamiania polecenia Backup narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.

## <a name="introduction"></a>Wprowadzenie

Kopia zapasowa oparta na migawce magazynu jest uruchamiana za pomocą `azacsnap -c backup` polecenia.  To polecenie wykonuje aranżację migawki magazynu spójnego na bazie danych w woluminach danych i migawkę magazynu (bez konfiguracji spójności bazy danych) na innych woluminach.  

W przypadku woluminów danych `azacsnap` zostanie przygotowana baza danych dla migawki magazynu, a następnie przejdzie ona do migawki magazynu dla wszystkich skonfigurowanych woluminów, a na koniec zostanie poinformowana baza danych, że migawka została ukończona.  Spowoduje to również zarządzanie wszystkimi wpisami bazy danych, które rejestrują aktywność kopii zapasowej migawki (np. SAP HANA wykaz kopii zapasowych).

## <a name="command-options"></a>Opcje polecenia

`-c backup`Polecenie przyjmuje następujące argumenty:

- `--volume=` Typ woluminu do migawki, ten parametr może zawierać `data` lub `other`
  - `data` migawki woluminów w Stanza datavolume w pliku konfiguracji.
  - `other` migawki woluminów w otherVolume Stanza pliku konfiguracyjnego.
  
  > [!NOTE]
  > Tworząc oddzielny plik konfiguracji z woluminem rozruchowym jako otherVolume, możliwe jest wykonanie `boot` migawek na całkowicie innym harmonogramie (na przykład codziennie).

- `--prefix=` prefiks migawki klienta dla nazwy migawki. Ten parametr ma dwa cele. W pierwszej kolejności należy podać unikatową nazwę grupowania migawek. Drugi raz, aby określić `--retention` liczbę migawek magazynu, które są przechowywane dla określonego `--prefix` .

    > [!IMPORTANT]
    > Dozwolone są tylko znaki alfanumeryczne ("A-Z, A-z, 0-9"), podkreślenia ("_") i myślnika ("-").

- `--retention` liczba migawek zdefiniowanych `--prefix` do przechowywania. Wszystkie dodatkowe migawki zostaną usunięte po wykonaniu nowej migawki `--prefix` .

- `--trim` Ta opcja jest dostępna dla SAP HANA v2 i nowszych, ponieważ umożliwia zachowanie wykazu kopii zapasowych i wykazu dysku oraz kopii zapasowych dzienników. Liczba wpisów, które mają być przechowywane w wykazie kopii zapasowych `--retention` , zależy od powyższej opcji i usuwa starsze wpisy dla zdefiniowanego prefiksu ( `--prefix` ) z wykazu kopii zapasowych i powiązane kopie zapasowe dzienników fizycznych. Usuwa także wszystkie wpisy kopii zapasowych dziennika, które są starsze niż najstarsza wersja niebędąca w dzienniku. Dzięki tym operacjom kopie zapasowe dzienników nie mogą korzystać ze wszystkich dostępnych miejsc na dysku.

  > [!NOTE]
  > Poniższe przykładowe polecenie spowoduje zachowanie 9 migawek magazynu i upewnienie się, że wykaz kopii zapasowych jest ciągle przycięty, aby dopasować do zachowanych migawek magazynu.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` opcjonalny parametr, który definiuje metodę szyfrowania używaną do komunikowania się z SAP HANA, albo `openssl` lub `commoncrypto` . Jeśli jest zdefiniowana, `azacsnap -c backup` polecenie oczekuje na znalezienie dwóch plików w tym samym katalogu. te pliki muszą być nazwane po odpowiednim identyfikatorze SID. Zapoznaj się z artykułem [Używanie protokołu SSL w celu komunikacji z usługą SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). Poniższy przykład wykonuje `hana` migawkę typu z prefiksem `hana_TEST` i będzie prowadziła `9` komunikację z SAP HANA przy użyciu protokołu SSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` jest opcjonalnym parametrem umożliwiającym stosowanie niestandardowych nazw plików konfiguracji.

## <a name="snapshot-backups-are-fast"></a>Kopie zapasowe migawek są szybkie

Czas trwania tworzenia kopii zapasowej migawki jest niezależny od rozmiaru woluminu, a wolumin o pojemności 10 TB jest przypięty w tym samym przybliżonym czasie co wolumin 10 GB.  

Podstawowe czynniki wpływające na całkowity czas wykonywania to liczba woluminów do utworzenia migawki i wszelkie zmiany w `--retention` parametrze (w przypadku których zmniejszenie może zwiększyć czas wykonywania w miarę usuwania nadmiarowych migawek).

W przykładowej konfiguracji powyżej (w przypadku **dużego wystąpienia platformy Azure**) migawki dla dwóch woluminów trwały mniej niż 5 sekund. W przypadku **Azure NetApp Files** migawki dla dwóch woluminów zajmują około 60 sekund.

> [!NOTE]
> Jeśli `--retention` zostanie znacznie zredukowany od poprzedniego czasu `azacsnap` , zostanie uruchomiony (na przykład z `--retention 50` do `--retention 5` ), czas trwania zostanie zwiększony w miarę `azacsnap` potrzeb w celu usunięcia dodatkowych migawek.

## <a name="example-with-data-parameter"></a>Przykład z `data` parametrem

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

Polecenie nie wyprowadza danych wyjściowych do konsoli, ale zapisuje je w pliku dziennika, pliku wynikowym i `/var/log/messages` .

*Plik dziennika* składa się z nazwy polecenia + opcja-c + nazwa pliku konfiguracji. Domyślnie nazwa pliku dziennika dla `-c backup` przebiegu z domyślną nazwą pliku konfiguracji `azacsnap-backup-azacsnap.log` .

Plik *wynikowy* ma taką samą nazwę bazową jak plik dziennika, z użyciem `.result` jako sufiksu, na przykład, `azacsnap-backup-azacsnap.result` który zawiera następujące dane wyjściowe:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages`Plik zawiera te same dane wyjściowe co `.result` plik. Zobacz następujący przykład (Uruchom jako główny):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Przykład z `other` parametrem

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

Polecenie nie wyprowadza danych wyjściowych do konsoli, ale tylko zapisuje w pliku dziennika.  _Nie zapisuje w_ pliku wynikowym ani `/var/log/messages` .

*Plik dziennika* składa się z nazwy polecenia + opcja-c + nazwa pliku konfiguracji. Domyślnie nazwa pliku dziennika dla `-c backup` przebiegu z domyślną nazwą pliku konfiguracji `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Przykład z `other` parametrem (aby utworzyć kopię zapasową systemu operacyjnego hosta)

> [!NOTE]
> Użycie innego pliku konfiguracji ( `--configfile bootVol.json` ), który zawiera tylko woluminy rozruchowe.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

Polecenie nie wyprowadza danych wyjściowych do konsoli, ale tylko zapisuje w pliku dziennika.  _Nie zapisuje w_ pliku wynikowym ani `/var/log/messages` .

Nazwa *pliku dziennika* jest w tym przykładzie `azacsnap-backup-bootVol.log` .

> [!NOTE]
> Nazwa pliku dziennika składa się z "(nazwa polecenia — ( `-c` opcja) — (plik konfiguracyjny)".  Jeśli na przykład używasz `-c backup` opcji z nazwą pliku dziennika `h80.json` , zostanie wywołany plik dziennika `azacsnap-backup-h80.log` .  Lub jeśli używasz `-c test` opcji z tym samym plikiem konfiguracji, zostanie wywołany plik dziennika `azacsnap-test-h80.log` .

- Typ dużego wystąpienia HANA: Istnieją dwie prawidłowe wartości z `TYPEI` lub `TYPEII` od jednostki dużego wystąpienia Hana.
- Zobacz [dostępne jednostki SKU dla dużych wystąpień Hana](../virtual-machines/workloads/sap/hana-available-skus.md) , aby potwierdzić dostępne jednostki SKU.

## <a name="next-steps"></a>Następne kroki

- [Pobierz szczegóły migawki](azacsnap-cmd-ref-details.md)
- [Usuwanie migawek](azacsnap-cmd-ref-delete.md)