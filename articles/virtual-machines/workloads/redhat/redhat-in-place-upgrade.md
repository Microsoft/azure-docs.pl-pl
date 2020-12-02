---
title: Uaktualnianie w miejscu Red Hat Enterprise Linux obrazów na platformie Azure
description: Dowiedz się, jak wykonać uaktualnienie w miejscu z obrazów systemu Red Hat Enterprise 7. x do najnowszej wersji 8. x.
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 9b35a3cbe23af91f7f0b8aceecfb8ba3c9720461
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484417"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux uaktualnień w miejscu

Ten artykuł zawiera instrukcje dotyczące wykonywania uaktualnienia w miejscu z programu Red Hat Enterprise Linux (RHEL) 7 do Red Hat Enterprise Linux 8. Instrukcje używają `leapp` Narzędzia na platformie Azure. Podczas uaktualniania w miejscu istniejący system operacyjny RHEL 7 jest zastępowany przez wersję RHEL 8.

>[!Note] 
> Oferty SQL Server na Red Hat Enterprise Linux nie obsługują uaktualnień w miejscu na platformie Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Czego można oczekiwać podczas uaktualniania
Podczas uaktualniania system uruchamia się kilka razy. Końcowe ponowne uruchomienie uaktualnia maszynę wirtualną do najnowszej wersji programu RHEL 8. 

Proces uaktualniania może trwać od 20 minut do 2 godzin. Łączny czas zależy od kilku czynników, takich jak rozmiar maszyny wirtualnej i liczba zainstalowanych pakietów w systemie.

## <a name="preparations"></a>Tworów
W systemach Red Hat i Azure zaleca się użycie uaktualnienia w miejscu w celu przejścia systemu do kolejnej wersji głównej. 

Przed rozpoczęciem uaktualniania należy pamiętać o następujących kwestiach. 

>[!Important] 
> Utwórz migawkę obrazu przed rozpoczęciem uaktualniania.

* Upewnij się, że używasz najnowszej wersji programu RHEL 7. Obecnie Najnowsza wersja to RHEL 7,9. Jeśli używasz zablokowanej wersji i nie możesz przeprowadzić uaktualnienia do RHEL 7,9, wykonaj następujące [kroki, aby przełączyć się na repozytorium inne niż EUS (rozszerzona obsługa aktualizacji)](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Uruchom następujące polecenie, aby sprawdzić uaktualnienie i sprawdzić, czy zostanie zakończone pomyślnie. Polecenie powinno generować plik */var/log/leapp/leapp-report.txt* . Ten plik wyjaśnia proces, co się dzieje, i czy możliwe jest uaktualnienie.

    >[!NOTE]
    > Użyj konta głównego, aby uruchomić polecenia z tego artykułu. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Upewnij się, że konsola szeregowa jest funkcjonalna. Ta konsola będzie używana do monitorowania podczas procesu uaktualniania.

* Włącz dostęp do katalogu głównego SSH w */etc/ssh/sshd_config*:
    1. Otwórz plik */etc/ssh/sshd_config*.
    1. Wyszukaj ciąg `#PermitRootLogin yes`.
    1. Usuń znak numeru ( `#` ), aby usunąć komentarz do ciągu.

## <a name="upgrade-steps"></a>Kroki uaktualniania

Wykonaj te kroki uważnie. Zalecamy podjęcie próby uaktualnienia na maszynie testowej przed jej wypróbą w wystąpieniach produkcyjnych.

1. Wykonaj `yum` aktualizację, aby pobrać najnowsze pakiety klienta.
    ```bash
    yum update -y
    ```

1. Zainstaluj program `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. W [portalu Red Hat](https://access.redhat.com/articles/3664871)uzyskaj plik *leapp-Data. tar. gz* zawierający *repomap.csv* i *pes-events.json*. Wyodrębnij plik *leapp-Data. tar. gz* .
    1. Pobierz plik *leapp-Data. tar. gz* .
    1. Wyodrębnij zawartość i Usuń plik. Użyj następującego polecenia:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Dodaj `answers` plik dla `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Rozpocznij uaktualnienie.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Po `leapp upgrade` pomyślnym zakończeniu działania polecenia ręcznie uruchom ponownie system, aby ukończyć proces. System jest niedostępny, gdy zostanie ponownie uruchomiony kilka razy. Monitorowanie procesu przy użyciu konsoli szeregowej.

1.  Sprawdź, czy uaktualnienie zostało zakończone pomyślnie.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Po zakończeniu uaktualniania Usuń główny dostęp SSH:
    1. Otwórz plik */etc/ssh/sshd_config*.
    1. Wyszukaj ciąg `#PermitRootLogin yes`.
    1. Dodaj znak numeru ( `#` ), aby skomentować ciąg.

1. Uruchom ponownie usługę SSHD, aby zastosować zmiany.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Typowe problemy

Następujące błędy występują często, gdy `leapp preupgrade` proces zakończy się niepowodzeniem lub `leapp upgrade` proces kończy się niepowodzeniem:

* **Błąd**: nie znaleziono dopasowań dla następujących wyłączonych wzorców wtyczki.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Rozwiązanie**: Wyłącz wtyczkę usługi Subscription-Manager. Wyłącz ją, edytując plik */etc/yum/pluginconf.d/Subscription-Manager.conf* i zmieniając `enabled` na `enabled=0` .

    Ten błąd występuje `yum` , gdy włączona wtyczka Menedżera subskrypcji nie jest używana w przypadku `PAYG` maszyn wirtualnych.

* **Błąd**: możliwe problemy z logowaniem zdalnym przy użyciu elementu głównego.

    Ten błąd może pojawić się w przypadku `leapp preupgrade` niepowodzenia:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Rozwiązanie**: Włącz dostęp do katalogu głównego w */etc/sshd_config*.

    Ten błąd występuje, gdy główny dostęp SSH nie jest włączony w */etc/sshd_config*. Aby uzyskać więcej informacji, zobacz sekcję [przygotowania](#preparations) w tym artykule. 


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obrazach Red Hat na platformie Azure](./redhat-images.md).
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md).
* Dowiedz się więcej o [ofercie BYOS RHEL](./byos.md).
* Aby dowiedzieć się więcej na temat procesów uaktualniania w miejscu Red Hat, zobacz [Uaktualnianie z RHEL 7 do RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) w dokumentacji Red Hat.
* Aby dowiedzieć się więcej na temat zasad pomocy technicznej systemu Red Hat dla wszystkich wersji programu RHEL, zobacz [Red Hat Enterprise Linux cykl życia](https://access.redhat.com/support/policy/updates/errata) w dokumentacji Red Hat.