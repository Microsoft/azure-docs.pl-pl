---
title: Jak skonfigurować serwer z systemem SMT dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jak skonfigurować serwer z systemem SMT dla SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 990274f34dac0234c2c2d521ca6a260f999671bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674529"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Konfigurowanie serwera SMT dla systemu SUSE Linux
Duże wystąpienia SAP HANA nie mają bezpośredniej łączności z Internetem. Nie jest to prosty proces rejestracji takiej jednostki u dostawcy systemu operacyjnego oraz pobierania i stosowania aktualizacji. Rozwiązaniem dla systemu SUSE Linux jest skonfigurowanie serwera w postaci SMT na maszynie wirtualnej platformy Azure. Hostowanie maszyny wirtualnej w sieci wirtualnej platformy Azure, która jest połączona z dużym wystąpieniem HANA. Dzięki takiemu serwerowi o rozmiarze SMT duże wystąpienie wystąpienia HANA może rejestrować i pobierać aktualizacje. 

Aby uzyskać więcej dokumentacji na temat SUSE, zobacz swoje [Narzędzia do zarządzania subskrypcjami dla SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Wymagania wstępne dotyczące instalowania serwera z systemem SMT, który spełnia zadanie dla dużych wystąpień platformy HANA, to:

- Sieć wirtualna platformy Azure, która jest połączona z obwodem ExpressRoute dużego wystąpienia HANA.
- Konto SUSE skojarzone z organizacją. Organizacja powinna mieć ważną subskrypcję SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalowanie serwera SMT na maszynie wirtualnej platformy Azure

Najpierw Zaloguj się do [centrum klient SUSE](https://scc.suse.com/).

Przejdź do pozycji  >  **poświadczenia organizacji** organizacji. W tej sekcji należy znaleźć poświadczenia niezbędne do skonfigurowania serwera SMT.

Następnie zainstaluj maszynę wirtualną w systemie SUSE Linux w sieci wirtualnej platformy Azure. Aby wdrożyć maszynę wirtualną, zapoznaj się z obrazem galerii SLES 12 z dodatkiem SP2 (wybierz pozycję obraz BYOS SUSE). W procesie wdrażania nie należy definiować nazwy DNS i nie należy używać statycznych adresów IP.

![Zrzut ekranu wdrożenia maszyny wirtualnej na serwerze SMT](./media/hana-installation/image3_vm_deployment.png)

Wdrożona maszyna wirtualna jest mniejsza i ma wewnętrzny adres IP w sieci wirtualnej platformy Azure 10.34.1.4. Nazwa maszyny wirtualnej to *smtserver*. Po zakończeniu instalacji zostanie sprawdzona łączność z jednostką lub jednostkami dużego wystąpienia HANA. W zależności od sposobu uporządkowania rozpoznawania nazw może być konieczne skonfigurowanie rozpoznawania jednostek dużego wystąpienia HANA w ramach etc/hosty maszyny wirtualnej platformy Azure. 

Dodaj dysk do maszyny wirtualnej. Ten dysk jest używany do przechowywania aktualizacji, a dysk rozruchowy może być za mały. W tym miejscu dysk został zainstalowany do/SRV/www/htdocs, jak pokazano na poniższym zrzucie ekranu. Wystarczy dysk 100 GB.

![Zrzut ekranu przedstawia dodany dysk w oknie.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Zaloguj się do jednostki lub jednostek dużego wystąpienia HANA, Zachowaj/etc/hosts i sprawdź, czy możesz dotrzeć do maszyny wirtualnej platformy Azure, która ma uruchamiać serwer SMT przez sieć.

Po tym sprawdzeniu Zaloguj się do maszyny wirtualnej platformy Azure, która powinna uruchamiać serwer SMT. Jeśli używasz polecenia "Utwórz", aby zalogować się do maszyny wirtualnej, uruchom następującą sekwencję poleceń w oknie bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Aby aktywować ustawienia, uruchom ponownie bash. Następnie zacznij YAST.

Połącz maszynę wirtualną (smtserver) z witryną SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Po nawiązaniu połączenia między maszyną wirtualną a lokacją SUSE Zainstaluj pakiety SMT. Użyj poniższego polecenia, aby zainstalować pakiety SMT.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Możesz również użyć narzędzia YAST, aby zainstalować pakiety SMT. W YAST, przejdź do obszaru **konserwacja oprogramowania** i Wyszukaj pozycję SMT. Wybierz opcję **SMT**, która automatycznie przełącza się do YaST2-SMT.

![Zrzut ekranu elementu SMT w YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Zaakceptuj wybór na potrzeby instalacji na smtserver. Po zakończeniu instalacji przejdź do konfiguracji serwera SMT. Wprowadź poświadczenia organizacji z centrum klient SUSE, które zostało pobrane wcześniej. Wprowadź również nazwę hosta maszyny wirtualnej platformy Azure jako adres URL serwera w formacie SMT. W tej prezentacji jest to https: \/ /smtserver.

![Zrzut ekranu przedstawiający konfigurację serwera w liczbie SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Teraz sprawdź, czy połączenie z centrum klient SUSE działa. Jak widać na poniższym zrzucie ekranu, w tym przykładzie demonstracyjnym zadziałała.

![Zrzut ekranu przedstawiający połączenie testowe z centrum klientów SUSE](./media/hana-installation/image7_test_connect.png)

Po uruchomieniu Instalatora elementu SMT Podaj hasło do bazy danych. Ponieważ jest to nowa instalacja, należy zdefiniować hasło, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający Definiowanie hasła dla bazy danych](./media/hana-installation/image8_define_db_passwd.PNG)

Następnym krokiem jest utworzenie certyfikatu.

![Zrzut ekranu przedstawiający tworzenie certyfikatu dla serwera z systemem SMT](./media/hana-installation/image9_certificate_creation.PNG)

Po zakończeniu konfiguracji uruchomienie sprawdzania synchronizacji może potrwać kilka minut. Po instalacji i konfiguracji serwera z opcją SMT należy znaleźć repozytorium katalogu w punkcie instalacji/SRV/www/htdocs/. Istnieją także podkatalogi w obszarze repozytorium. 

Uruchom ponownie serwer SMT i powiązane z nim usługi za pomocą tych poleceń.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Pobierz pakiety na serwer SMT

Po ponownym uruchomieniu wszystkich usług wybierz odpowiednie pakiety w obszarze zarządzania SMT przy użyciu YAST. Wybór pakietu zależy od obrazu systemu operacyjnego serwera dużego wystąpienia HANA. Wybór pakietu nie zależy od wersji SLES ani wersji maszyny wirtualnej, na której działa serwer SMT. Poniższy zrzut ekranu przedstawia przykład ekranu wyboru.

![Zrzut ekranu przedstawiający Wybieranie pakietów](./media/hana-installation/image10_select_packages.PNG)

Następnie uruchom początkową kopię wybranych pakietów na skonfigurowanym serwerze SMT. Ta kopia jest wyzwalana w powłoce przy użyciu polecenia SMT-Mirror.

![Zrzut ekranu przedstawiający pobieranie pakietów na serwer SMT](./media/hana-installation/image11_download_packages.PNG)

Pakiety powinny zostać skopiowane do katalogów utworzonych w punkcie instalacji/SRV/www/htdocs. Ten proces może potrwać godzinę lub dłużej, w zależności od liczby wybranych pakietów. Po zakończeniu tego procesu przejdź do konfiguracji klienta SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurowanie klienta SMT w jednostkach dużych wystąpień platformy HANA

Klient lub klienci w tym przypadku są jednostkami dużych wystąpień platformy HANA. Instalator serwera SMT skopiował skrypt clientSetup4SMT.sh na maszynę wirtualną platformy Azure. Skopiuj ten skrypt do jednostki dużego wystąpienia HANA, którą chcesz połączyć z serwerem SMT. Uruchom skrypt przy użyciu opcji-h i nadaj nazwę serwerowi SMT jako parametr. W tym przykładzie nazwą jest *smtserver*.

![Zrzut ekranu przedstawiający Konfigurowanie klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Istnieje możliwość, że ładowanie certyfikatu z serwera przez klienta powiedzie się, ale rejestracja nie powiedzie się, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający niepowodzenie rejestracji klienta](./media/hana-installation/image13_registration_failed.PNG)

Jeśli rejestracja nie powiedzie się, zobacz [dokument wsparcia](https://www.suse.com/de-de/support/kb/doc/?id=7006024)dla systemu SUSE i wykonaj kroki opisane tutaj.

> [!IMPORTANT] 
> W polu Nazwa serwera podaj nazwę maszyny wirtualnej (w tym przypadku *smtserver*) bez w pełni kwalifikowanej nazwy domeny. 

Po wykonaniu tych kroków uruchom następujące polecenie w jednostce dużego wystąpienia HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Zaczekaj kilka minut po tym kroku. W przypadku natychmiastowego uruchomienia clientSetup4SMT.sh może zostać wyświetlony komunikat o błędzie.

Jeśli wystąpi problem, który należy rozwiązać w zależności od kroków artykułu SUSE, należy ponownie uruchomić usługę clientSetup4SMT.sh w jednostce dużego wystąpienia platformy HANA. Teraz powinna zostać zakończona pomyślnie.

![Zrzut ekranu przedstawiający powodzenie rejestracji klienta](./media/hana-installation/image14_finish_client_config.PNG)

Skonfigurowano klienta SMT jednostki dużego wystąpienia HANA w celu nawiązania połączenia z serwerem SMT zainstalowanym na maszynie wirtualnej platformy Azure. Możesz teraz wykonać polecenie "użyciu narzędzia zypper up" lub "użyciu narzędzia zypper in", aby zainstalować aktualizacje systemu operacyjnego w dużych wystąpieniach platformy HANA, lub zainstalować dodatkowe pakiety. Aktualizacje pobrane wcześniej można pobrać tylko na serwerze SMT.

## <a name="next-steps"></a>Następne kroki
- [Instalacja platformy Hana na serwerze HLI](hana-example-installation.md).











