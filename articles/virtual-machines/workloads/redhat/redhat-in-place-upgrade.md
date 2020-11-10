---
title: Uaktualnianie w miejscu Red Hat Enterprise Linux obrazów na platformie Azure
description: Znajdź kroki umożliwiające przeprowadzenie uaktualnienia w miejscu z obrazów systemu Red Hat Enterprise 7. x do najnowszej wersji 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447900"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux uaktualnień w miejscu

Ten artykuł zawiera instrukcje krok po kroku dotyczące przeprowadzania uaktualnienia w miejscu z Red Hat Enterprise Linux 7 do Red Hat Enterprise Linux 8 przy użyciu narzędzia "Leapp" na platformie Azure. Podczas uaktualniania w miejscu istniejący system operacyjny RHEL 7 jest zastępowany przez wersję RHEL 8.

>[!Note] 
> SQL Server w ofertach Red Hat Enterprise Linux nie obsługują uaktualnienia w miejscu na platformie Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Czego można oczekiwać podczas uaktualniania
System zostanie kilka razy uruchomiony ponownie podczas uaktualniania i jest normalny. Ostatnie ponowne uruchomienie spowoduje uaktualnienie maszyny wirtualnej do wersji RHEL 8 Najnowsza wersja pomocnicza.

## <a name="preparations-for-the-upgrade"></a>Przygotowania do uaktualnienia
Uaktualnienia w miejscu są oficjalnie zalecaną metodą Red Hat i platformą Azure, aby umożliwić klientom uaktualnienie systemu do następnej wersji głównej. Przed przeprowadzeniem uaktualnienia tutaj należy zwrócić uwagę i wziąć pod uwagę zagadnienia. 

>[!Important] 
> Zrób migawkę obrazu przed przeprowadzeniem uaktualnienia.

>[!NOTE]
> Polecenia w tym artykule muszą zostać uruchomione przy użyciu konta głównego

* Upewnij się, że używasz najnowszej wersji programu RHEL 7, która jest obecnie RHEL 7,9. Jeśli używasz zablokowanej wersji i nie możesz przeprowadzić uaktualnienia do RHEL 7,9, możesz użyć tych [kroków w celu przełączenia się na repozytorium, które nie jest EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Uruchom poniższe polecenie, aby dowiedzieć się, jak odbywa się uaktualnienie i czy zostanie ukończone. Polecenie powinno generować plik w ścieżce "/var/log/leapp/leapp-report.txt", który wyjaśnia proces i co jest wykonywane oraz czy uaktualnienie jest możliwe.
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Procedura przeprowadzania uaktualnienia

Wykonaj te kroki uważnie. Zdecydowanie zaleca się wypróbowanie uaktualnienia na maszynie testowej przed wystąpieniami produkcyjnymi.

1. Wykonaj aktualizację yum, aby pobrać najnowsze pakiety klienta.
    ```bash
    yum update
    ```

1. Zainstaluj pakiet leapp-Client-Package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Użyj pliku leapp-Data. tar. gz z repomap.csv i pes-events.json, obecny w [portalu RedHat](https://access.redhat.com/articles/3664871)i Wyodrębnij je. 
    1. Pobierz plik.
    1. Wyodrębnij zawartość i Usuń plik przy użyciu następującego polecenia:
    ```bash
     tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```
    


1. Dodaj plik "Answers" dla "Leapp".
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ```
    
1. Włącz PermitRootLogin w/etc/ssh/sshd_config
    1. Otwórz plik/etc/ssh/sshd_config
    1. Wyszukaj frazę "#PermitRootLogin Yes"
    1. Usuwanie komentarza "#"



1. Wykonaj uaktualnienie "Leapp".
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Uruchom ponownie usługę SSHD, aby zmiany zaczęły obowiązywać
    ```bash
    systemctl restart sshd
    ```
1. Dodaj komentarz do PermitRootLogin w/etc/ssh/sshd_config ponownie
    1. Otwórz plik/etc/ssh/sshd_config
    1. Wyszukaj frazę "#PermitRootLogin Yes"
    1. Dodaj znak "#" do komentarza

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obrazach Red Hat na platformie Azure](./redhat-images.md).
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md).
* Dowiedz się więcej o [ofercie BYOS RHEL](./byos.md).
* Informacje o procesach uaktualniania w miejscu w systemie Red Hat można znaleźć w dokumentacji Red Hat, [uaktualniając z RHEL 7 do RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .