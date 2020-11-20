---
title: Infrastruktura aktualizacji Red Hat | Microsoft Docs
description: Informacje o infrastrukturze aktualizacji Red Hat dla wystąpień Red Hat Enterprise Linux na żądanie w programie Microsoft Azure
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: d4af869a3bf4ba7f454ae8e5c9c9f4eb81f5939f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957456"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastruktura aktualizacji firmy Red Hat dla maszyn wirtualnych Red Hat Enterprise Linux na żądanie na platformie Azure
 Usługa [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umożliwia dostawcom usług w chmurze, takim jak Azure, duplikowanie zawartości repozytorium w systemie Red Hat, tworzenie niestandardowych repozytoriów z zawartością specyficzną dla platformy Azure i udostępnianie ich dla maszyn wirtualnych użytkownika końcowego.

Red Hat Enterprise Linux (RHEL) obrazy z opcją płatność zgodnie z rzeczywistym użyciem (PAYG) są wstępnie skonfigurowane do uzyskiwania dostępu do usługi Azure RHUI. Nie jest wymagana dodatkowa konfiguracja. Aby pobrać najnowsze aktualizacje, uruchom polecenie `sudo yum update` po przygotowaniu wystąpienia RHEL. Ta usługa jest uwzględniona w ramach opłat za oprogramowanie RHEL PAYG.

Dodatkowe informacje na temat obrazów RHEL na platformie Azure, w tym zasady publikowania i przechowywania, są dostępne [tutaj](./redhat-images.md).

Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

> [!IMPORTANT]
> RHUI jest przeznaczona tylko dla obrazów z opcją płatność zgodnie z rzeczywistym użyciem. W przypadku obrazów niestandardowych i odnoszących się znanych również jako "Przenieś własną subskrypcję" (BYOS) system musi być dołączony do RHSM lub satelity w celu uzyskania aktualizacji. Zobacz [artykuł Red Hat](https://access.redhat.com/solutions/253273) , aby uzyskać więcej szczegółów.


## <a name="important-information-about-azure-rhui"></a>Ważne informacje o usłudze Azure RHUI

* Azure RHUI to infrastruktura aktualizacji, która obsługuje wszystkie maszyny wirtualne RHEL PAYG utworzone na platformie Azure. Nie wyklucza to rejestrowania maszyn wirtualnych RHEL PAYG przy użyciu Menedżera subskrypcji ani satelity ani innego źródła aktualizacji, ale dzięki temu z maszyną wirtualną PAYG zostanie pośrednim podwójnym rozliczeniami. Aby uzyskać szczegółowe informacje, zobacz następujący punkt.
* Dostęp do RHUI hostowanego na platformie Azure jest dostępny w cenie obrazu RHEL PAYG. Jeśli wyrejestrujesz maszynę wirtualną PAYG RHEL z RHUI hostowanej na platformie Azure, która nie konwertuje maszyny wirtualnej na typ maszyny wirtualnej z systemem. Jeśli zarejestrujesz tę samą maszynę wirtualną z innym źródłem aktualizacji, możesz ponieść _pośrednie_ podwójne opłaty. Opłata zostanie naliczona za pierwszy czas na korzystanie z usługi Azure RHEL. Opłata jest naliczana za drugi czas dla subskrypcji Red Hat, które zostały wcześniej zakupione. Jeśli ciągle potrzebujesz użyć infrastruktury aktualizacji innej niż hostowana na platformie Azure RHUI, rozważ zarejestrowanie się w celu korzystania z [obrazów RHEL BYOS](./byos.md).

* RHEL SAP PAYG obrazy na platformie Azure (RHEL for SAP, RHEL for SAP HANA i RHEL for SAP Business Applications) są połączone z dedykowanymi kanałami RHUI, które pozostają w określonej wersji pomocniczej RHEL, zgodnie z wymaganiami certyfikacji SAP.

* Dostęp do RHUI hostowanych przez platformę Azure jest ograniczony do maszyn wirtualnych w ramach [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Jeśli chodzi o ruch związany z maszyną wirtualną za pośrednictwem lokalnej infrastruktury sieciowej, może być konieczne skonfigurowanie tras zdefiniowanych przez użytkownika dla maszyn wirtualnych RHEL PAYG w celu uzyskania dostępu do usługi Azure RHUI. W takim przypadku trasy zdefiniowane przez użytkownika będą musiały zostać dodane dla _wszystkich_ RHUI adresów IP.


## <a name="image-update-behavior"></a>Zachowanie aktualizacji obrazów

Od kwietnia 2019 platforma Azure oferuje obrazy RHEL, które są połączone z repozytoriami z obsługą aktualizacji rozszerzonych (EUS) domyślnie i obrazy RHEL, które są domyślnie połączone z regularnymi repozytoriami (nie EUS). Więcej szczegółów na temat RHEL EUS są dostępne w dokumentacji dotyczącej [cyklu życia](https://access.redhat.com/support/policy/updates/errata) systemu Red Hat i [dokumentacji EUS](https://access.redhat.com/articles/rhel-eus). Domyślne zachowanie programu `sudo yum update` będzie się różnić w zależności od tego, który obraz RHEL został zainicjowany, ponieważ różne obrazy są połączone z różnymi repozytoriami.

Aby uzyskać pełną listę obrazów, uruchom polecenie `az vm image list --publisher redhat --all` przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Obrazy połączone z repozytoriami nieEUS

Jeśli zainicjujesz maszynę wirtualną z obrazu RHEL, który jest połączony z repozytoriami nieEUS, podczas uruchamiania programu zostanie uaktualniona do najnowszej wersji pomocniczej RHEL `sudo yum update` . Na przykład jeśli zainicjujesz maszynę wirtualną na podstawie obrazu RHEL 7,4 PAYG i uruchamiania `sudo yum update` , będziesz kończyć maszynę wirtualną RHEL 7,8 (Najnowsza wersja pomocnicza w rodzinie RHEL7).

Obrazy połączone z repozytoriami nieEUS nie będą zawierać pomocniczego numeru wersji w jednostce SKU. Jednostka SKU jest trzecią częścią nazwy URN (pełna nazwa obrazu). Na przykład wszystkie następujące obrazy są dołączone do repozytoriów innych niż EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Należy pamiętać, że jednostki SKU to 7-LVM lub 7-RAW. Wersja pomocnicza jest wskazana w wersji (czwarty element w nazwie URN) tych obrazów.

### <a name="images-connected-to-eus-repositories"></a>Obrazy połączone z repozytoriami EUS

Jeśli zainicjujesz maszynę wirtualną z obrazu RHEL, który jest połączony z repozytoriami EUS, nie będziesz uaktualniać do najnowszej wersji pomocniczej RHEL podczas uruchamiania `sudo yum update` . Wynika to z faktu, że obrazy połączone z repozytoriami EUS są również zablokowane w wersji z określoną wersją pomocniczą.

Obrazy połączone z repozytoriami EUS będą zawierać pomocniczy numer wersji w jednostce SKU. Na przykład wszystkie następujące obrazy są dołączone do repozytoriów EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS i blokowanie wersji RHEL maszyny wirtualne

Repozytoria rozszerzonej pomocy technicznej aktualizacji (EUS) są dostępne dla klientów, którzy chcą chcieć zablokować maszyny wirtualne RHEL do pewnej wersji pomocniczej RHEL po aprowizacji maszyny wirtualnej. Aby zablokować maszynę wirtualną RHEL do określonej wersji pomocniczej, należy zaktualizować repozytoria w taki sposób, aby wskazywały repozytoria pomocy technicznej o rozszerzonej aktualizacji. Możesz również cofnąć operację blokowania wersji EUS.

>[!NOTE]
> EUS nie są obsługiwane w dodatkach RHEL. Oznacza to, że w przypadku instalowania pakietu, który jest zazwyczaj dostępny w kanale RHEL Extras, nie będzie można wykonać tego działania w EUS. Cykl życia produktu Red Hat jest szczegółowo opisany w [tym miejscu](https://access.redhat.com/support/policy/updates/extras/).

W czasie tego pisania EUS zakończyło się wsparcie dla RHEL <= 7,4. Aby uzyskać więcej informacji, zobacz sekcję "Red Hat Enterprise Linux Extended Maintenance" w [dokumentacji Red Hat](https://access.redhat.com/support/policy/updates/errata/#Long_Support) .
* RHEL 7,4 EUS support zakończyła się 31 sierpnia 2019
* RHEL 7,5 EUS — 30 kwietnia 2020
* RHEL 7,6 EUS support do 31 maja 2021
* RHEL 7,7 EUS support to 30 sierpnia 2021

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>Przełącz maszynę wirtualną RHEL 7. x do EUS (wersja Zablokuj do określonej wersji pomocniczej)
Wykonaj poniższe instrukcje, aby zablokować maszynę wirtualną RHEL 7. x do określonej pomocniczej wersji (Uruchom jako root):

>[!NOTE]
> Dotyczy to tylko wersji RHEL 7. x, dla których EUS jest dostępna. W czasie tego pisania obejmuje to RHEL 7.2-7.7. Więcej szczegółów można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

1. Wyłącz repozytoria inne niż EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Dodaj repozytoria EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Zablokuj `releasever` zmienną (Uruchom jako root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Powyższa instrukcja spowoduje zablokowanie wersji pomocniczej RHEL w bieżącej wersji pomocniczej. Wprowadź konkretną wersję pomocniczą, jeśli zamierzasz uaktualnić i zablokować nowszą wersję pomocniczą, która nie jest najnowsza. Na przykład `echo 7.5 > /etc/yum/vars/releasever` program zablokuje wersję RHEL do RHEL 7,5.

1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>Przełącz maszynę wirtualną RHEL w wersji 8. x do EUS (wersja Lock z określoną wersją pomocniczą)
Skorzystaj z poniższych instrukcji, aby zablokować maszynę wirtualną RHEL 8. x do określonej pomocniczej wersji (Uruchom jako root):

>[!NOTE]
> Dotyczy to tylko wersji RHEL 8. x, dla których EUS jest dostępna. W czasie tego pisania obejmuje to RHEL 8.1-8.2. Więcej szczegółów można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

1. Wyłącz repozytoria inne niż EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. Pobierz plik konfiguracji repozytoriów EUS:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. Dodaj repozytoria EUS:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. Zablokuj `releasever` zmienną (Uruchom jako root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Powyższa instrukcja spowoduje zablokowanie wersji pomocniczej RHEL w bieżącej wersji pomocniczej. Wprowadź konkretną wersję pomocniczą, jeśli zamierzasz uaktualnić i zablokować nowszą wersję pomocniczą, która nie jest najnowsza. Na przykład `echo 8.1 > /etc/yum/vars/releasever` program zablokuje wersję RHEL do RHEL 8,1.

    >[!NOTE]
    > Jeśli wystąpiły problemy z uprawnieniami dostępu do releasever, można edytować plik za pomocą polecenia "nano/etc/yum/Vars/releaseve" i dodać szczegóły wersji obrazu i zapisać ("Ctrl + o", a następnie nacisnąć klawisz ENTER, a następnie klawisze "Ctrl + x").  

1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>Przełącz maszynę wirtualną RHEL 7. x z powrotem do innej niż EUS (Usuń blokadę wersji)
Uruchom następujący element jako główny:
1. Usuń `releasever` plik:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Wyłącz repozytoria EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurowanie maszyny wirtualnej RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>Przełącz maszynę wirtualną RHEL 8. x z powrotem do innej niż EUS (Usuń blokadę wersji)
Uruchom następujący element jako główny:
1. Usuń `releasever` plik:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Wyłącz repozytoria EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. Pobierz plik konfiguracyjny regularnego repozytoriów:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. Dodaj repozytoria EUS:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Adresy IP dla serwerów dostarczania zawartości RHUI

RHUI jest dostępny we wszystkich regionach, w których dostępne są obrazy na żądanie RHEL. Obejmuje ona obecnie wszystkie regiony publiczne wymienione na stronie [pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status/) , Administracja usa i Microsoft Azure (Niemcy) regionów.

Jeśli używasz konfiguracji sieci, aby dodatkowo ograniczyć dostęp z maszyn wirtualnych RHEL PAYG, upewnij się, że następujące adresy IP są dozwolone `yum update` do pracy w zależności od używanego środowiska:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```
>[!NOTE]
>Nowe obrazy dla instytucji rządowych usługi Azure USA, od stycznia 2020, będą używać publicznego adresu IP wymienionego powyżej nagłówka globalnego platformy Azure.

>[!NOTE]
>Należy również pamiętać, że platforma Azure (Niemcy) jest przestarzała na rzecz publicznych regionów Niemiec. Zalecenie dla klientów korzystających z platformy Azure (Niemcy) to rozpoczęcie od RHUI [publicznego.](#manual-update-procedure-to-use-the-azure-rhui-servers)

## <a name="azure-rhui-infrastructure"></a>Infrastruktura usługi Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizacja wygasłego certyfikatu klienta RHUI na maszynie wirtualnej

Jeśli używasz starszego obrazu maszyny wirtualnej RHEL, na przykład RHEL 7,4 (URN do obrazu: `RedHat:RHEL:7.4:7.4.2018010506` ), wystąpią problemy z połączeniem z usługą RHUI z powodu wygasłego certyfikatu klienta TLS/SSL. Wyświetlany błąd może wyglądać podobnie do _"element równorzędny protokołu SSL odrzucił certyfikat jako wygasły"_ lub _"błąd: nie można pobrać metadanych repozytorium (repomd.xml) dla repozytorium:... Sprawdź swoją ścieżkę i spróbuj ponownie "_. Aby rozwiązać ten problem, zaktualizuj pakiet klienta RHUI na maszynie wirtualnej przy użyciu następującego polecenia:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternatywnie, uruchomienie programu `sudo yum update` może także aktualizować pakiet certyfikatów klienta (w zależności od wersji RHEL), pomimo błędu "wygasły certyfikat SSL" będzie widoczny dla innych repozytoriów. Jeśli ta aktualizacja zakończyła się pomyślnie, należy przywrócić normalne połączenie z innymi repozytoriami RHUI, aby umożliwić `sudo yum update` pomyślne uruchomienie.

Jeśli podczas uruchamiania programu wystąpi błąd 404 `yum update` , spróbuj wykonać następujące czynności, aby odświeżyć pamięć podręczną Yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Rozwiązywanie problemów z połączeniami z usługą Azure RHUI
Jeśli masz problemy z nawiązywaniem połączenia z usługą Azure RHUI z poziomu maszyny wirtualnej Azure RHEL PAYG, wykonaj następujące kroki:

1. Sprawdź konfigurację maszyny wirtualnej dla punktu końcowego usługi Azure RHUI:

    1. Sprawdź, czy `/etc/yum.repos.d/rh-cloud.repo` plik zawiera odwołanie do `rhui-[1-3].microsoft.com` w `baseurl` `[rhui-microsoft-azure-rhel*]` sekcji pliku. Jeśli tak, używasz nowej usługi Azure RHUI.

    1. Jeśli wskazuje lokalizację z następującym wzorcem, `mirrorlist.*cds[1-4].cloudapp.net` wymagana jest aktualizacja konfiguracji. Używasz starej migawki maszyny wirtualnej i musisz ją zaktualizować tak, aby wskazywała nową usługę Azure RHUI.

1. Dostęp do RHUI hostowanych przez platformę Azure jest ograniczony do maszyn wirtualnych w ramach [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Jeśli używasz nowej konfiguracji, Zweryfikuj, że maszyna wirtualna nawiązuje połączenie z zakresem adresów IP platformy Azure i nadal nie można nawiązać połączenia z usługą Azure RHUI, Zapisz przypadek pomocy technicznej z firmą Microsoft lub Red Hat.

### <a name="infrastructure-update"></a>Aktualizacja infrastruktury

We wrześniu 2016 został wdrożony zaktualizowany RHUI platformy Azure. W kwietniu 2017 zamykamy starą usługę Azure RHUI. Jeśli używasz obrazów RHEL PAYG (lub ich migawek) od września 2016 lub nowszych, nastąpi automatyczne połączenie z nowym RHUIem platformy Azure. Jeśli jednak na maszynach wirtualnych masz starsze migawki, należy ręcznie zaktualizować ich konfigurację, aby uzyskać dostęp do usługi Azure RHUI, zgodnie z opisem w poniższej sekcji.

Nowe serwery usługi Azure RHUI są wdrażane przy użyciu [usługi azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). W Traffic Manager pojedynczy punkt końcowy (rhui-1.microsoft.com) może być używany przez dowolną maszynę wirtualną niezależnie od regionu.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Ręczna procedura aktualizacji do korzystania z serwerów usługi Azure RHUI
Ta procedura jest dostępna wyłącznie do celów referencyjnych. Obrazy RHEL PAYG mają już poprawną konfigurację, aby nawiązać połączenie z usługą Azure RHUI. Aby ręcznie zaktualizować konfigurację tak, aby korzystała z serwerów usługi Azure RHUI, wykonaj następujące czynności:

- Dla RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Dla RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Dla RHEL 8:
    1. Utwórz plik konfiguracji:
        ```bash
        vi rhel8.config
        ```
    1. Dodaj następującą zawartość do pliku konfiguracji:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Zapisz plik i uruchom następujące polecenie:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Aktualizowanie maszyny wirtualnej
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Następne kroki
* Aby utworzyć Red Hat Enterprise Linux maszynę wirtualną na podstawie obrazu z witryny Azure Marketplace PAYG i korzystać z RHUI hostowanej na platformie Azure, przejdź do [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6).
* Aby dowiedzieć się więcej na temat obrazów Red Hat na platformie Azure, przejdź do [strony dokumentacji](./redhat-images.md).
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
