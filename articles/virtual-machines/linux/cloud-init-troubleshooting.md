---
title: Rozwiązywanie problemów przy użyciu funkcji Cloud-init
description: Rozwiązywanie problemów z obsługą maszyny wirtualnej platformy Azure przy użyciu funkcji Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 842107245fe26155d53866bf95e11b08d7593ad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582157"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Rozwiązywanie problemów z obsługą maszyny wirtualnej za pomocą funkcji Cloud-init

W przypadku tworzenia uogólnionych obrazów niestandardowych przy użyciu funkcji Cloud-init do inicjowania obsługi administracyjnej, ale znaleziono, że maszyna wirtualna nie została prawidłowo utworzona, należy rozwiązać problem z obrazami niestandardowymi.

Przykłady problemów dotyczących aprowizacji:
- Maszyna wirtualna jest zablokowana przy "tworzeniu" przez 40 minut, a Tworzenie maszyny wirtualnej zostanie oznaczone jako zakończone niepowodzeniem
- `CustomData` nie przetworzono
- Nie można zainstalować dysku tymczasowych
- Użytkownicy nie zostaną utworzeni lub występują problemy z dostępem użytkowników
- Sieć nie jest prawidłowo skonfigurowana
- Zamiana błędów pliku lub partycji

W tym artykule opisano sposób rozwiązywania problemów z usługą Cloud-init. Aby uzyskać bardziej szczegółowe informacje, zobacz [Cloud-init głębokie szczegółowe](./cloud-init-deep-dive.md).

## <a name="step-1-test-the-deployment-without-customdata"></a>Krok 1. Testowanie wdrożenia bez `customData`

Usługa Cloud-init może akceptować `customData` , która jest przenoszona do niego podczas tworzenia maszyny wirtualnej. Najpierw należy upewnić się, że nie powoduje to problemów z wdrożeniami. Spróbuj zainicjować maszynę wirtualną bez przechodzenia do żadnej konfiguracji. Jeśli okaże się, że nie można zainicjować obsługi administracyjnej maszyny wirtualnej, wykonaj poniższe czynności, jeśli okaże się, że przekazanie konfiguracji nie jest stosowane, przejdź do [kroku 4](). 

## <a name="step-2-review-image-requirements"></a>Krok 2. Przegląd wymagań dotyczących obrazu
Główną przyczyną niepowodzenia aprowizacji maszyny wirtualnej jest obraz systemu operacyjnego, który nie spełnia wymagań wstępnych dotyczących uruchamiania na platformie Azure. Upewnij się, że obrazy są prawidłowo przygotowane przed próbą udostępnienia ich na platformie Azure. 


W poniższych artykułach przedstawiono kroki, które należy wykonać, aby przygotować różne dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

- [dystrybucje oparte na systemie CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES i openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Inne: dystrybucje niezatwierdzone](create-upload-generic.md)

W przypadku [obsługiwanych obrazów usługi Azure Cloud-init dystrybucji systemu](./using-cloud-init.md)Linux mają już wszystkie wymagane pakiety i konfiguracje umożliwiające prawidłowe udostępnienie obrazu na platformie Azure. Jeśli okaże się, że maszyna wirtualna nie zostanie utworzona na podstawie własnego obrazu nadzorowanego, wypróbuj obsługiwany obraz portalu Azure Marketplace, który jest już skonfigurowany dla funkcji Cloud-init z opcjonalnym `customData` . Jeśli `customData` działa prawidłowo z obrazem portalu Azure Marketplace, prawdopodobnie wystąpił problem z obrazem nadzorowanym.

## <a name="step-3-collect--review-vm-logs"></a>Krok 3. zbieranie & przegląd dzienników maszyn wirtualnych

Gdy nie można zainicjować obsługi administracyjnej maszyny wirtualnej, platforma Azure wyświetli stan "Tworzenie", przez 20 minut, a następnie uruchomi ponownie maszynę wirtualną, a następnie poczekaj na inne 20 minut przed oznaczeniem wdrożenia maszyny wirtualnej jako nieudaną, przed oznaczeniem go z `OSProvisioningTimedOut` powodu błędu.

Gdy maszyna wirtualna jest uruchomiona, potrzebne będą dzienniki z maszyny wirtualnej, aby zrozumieć, dlaczego Inicjowanie obsługi nie powiodło się.  Aby zrozumieć, dlaczego Inicjowanie obsługi maszyny wirtualnej nie powiodło się, nie należy zatrzymać maszyny wirtualnej. Pozostaw uruchomioną maszynę wirtualną. W celu zbierania dzienników należy zachować niedziałającą maszynę wirtualną w stanie uruchomienia. Aby zebrać dzienniki, użyj jednej z następujących metod:

- [Konsola szeregowa](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- [Włącz diagnostykę rozruchu](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) przed utworzeniem maszyny wirtualnej, a następnie [Wyświetl](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) je w trakcie rozruchu.

- [Uruchom polecenie AZ VM Repair](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands) , aby dołączyć i zainstalować dysk systemu operacyjnego, co umożliwi zbieranie tych dzienników:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Aby rozpocząć wstępne Rozwiązywanie problemów, Zacznij od dzienników usługi Cloud-init i zapoznaj się z informacjami o tym, gdzie wystąpił błąd, a następnie użyj innych dzienników do głębokiej szczegółowe i Udostępnij dodatkowe szczegółowe informacje. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Dzienniki seryjne/rozruchowe

W obszarze wszystkie dzienniki Rozpocznij wyszukiwanie ciągu "Niepowodzenie", "ostrzeżenie", "WARN", "ERR", "Error", "ERROR". Ustawienie konfiguracji do ignorowania wyszukiwania z uwzględnieniem wielkości liter jest zalecane. 

> [!TIP]
> W przypadku rozwiązywania problemów z niestandardowym obrazem należy rozważyć dodanie użytkownika podczas tego obrazu. Jeśli Inicjowanie obsługi nie powiodło się, możesz zalogować się do systemu operacyjnego.

## <a name="analyzing-the-logs"></a>Analizowanie dzienników

Poniżej znajdują się szczegółowe informacje o tym, co należy wyszukać w każdym dzienniku usługi Cloud-init.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Domyślnie wszystkie zdarzenia z chmury i init z priorytetem debugowania lub nowszym są zapisywane w `/var/log/cloud-init.log` . Zapewnia to pełne dzienniki każdego zdarzenia, które wystąpiło podczas inicjowania usługi Cloud-init. 

Na przykład:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Po znalezieniu błędu lub ostrzeżenia Odczytaj do tyłu w dzienniku Cloud-init, aby dowiedzieć się, co próbowano wykonać w chmurze-init przed wystąpieniem błędu lub ostrzeżenia. W wielu przypadkach usługa Cloud-init będzie uruchamiać polecenia systemu operacyjnego lub wykonywać operacje inicjowania obsługi przed błędem, co może zapewnić wgląd w informacje o tym, dlaczego pojawiły się błędy w dziennikach. Poniższy przykład pokazuje, że usługa Cloud-init podjęła próbę zainstalowania urządzenia bezpośrednio przed wystąpieniem błędu.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Jeśli masz dostęp do [konsoli szeregowej](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode), możesz spróbować ponownie uruchomić polecenie, że usługa Cloud-init podjęła próbę uruchomienia.

Rejestrowanie dla programu `/var/log/cloud-init.log` można także zmienić w programie/etc/cloud/cloud.cfg.d/05_logging. cfg. Więcej szczegółów dotyczących rejestrowania w usłudze Cloud-init można znaleźć w [dokumentacji usługi Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Możesz uzyskać informacje z i w `stdout` `stderr` trakcie [etapów działania usługi Cloud-init](cloud-init-deep-dive.md). Zwykle obejmuje to informacje o tabeli routingu, informacje o sieci, informacje weryfikacyjne klucza hosta SSH `stdout` i `stderr` dla każdego etapu inicjowania usługi Cloud-init oraz sygnaturę czasową dla każdego etapu. W razie potrzeby `stderr` `stdout` można zmienić konfigurację rejestrowania z programu `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Dzienniki seryjne/rozruchowe 

Usługa Cloud-init ma wiele zależności, które zostały opisane w wymaganiach wstępnych dotyczących obrazów na platformie Azure, takich jak sieć, magazyn, możliwość instalacji obrazu ISO oraz Instalowanie i formatowanie dysku tymczasowego. Każdy z tych może zgłosić błędy i spowodować niepowodzenie działania funkcji Cloud-init. Na przykład jeśli maszyna wirtualna nie może uzyskać dzierżawy DHCP, inicjowanie nie powiedzie się.

Jeśli nadal nie można wyizolować przyczyny niepowodzenia aprowizacji przez funkcję Cloud-init, należy zrozumieć, jakie etapy inicjowania chmury i kiedy moduły są uruchamiane. Aby uzyskać więcej informacji, zobacz artykuł szczegółowe informacje [w usłudze Cloud-init](cloud-init-deep-dive.md) .


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Krok 4. Badanie przyczyny niestosowania konfiguracji
Nie każdy błąd w usłudze Cloud-init powoduje niepowodzenie inicjowania obsługi krytycznej. Na przykład jeśli używasz `runcmd` modułu w konfiguracji Cloud-init, niezerowy kod zakończenia z uruchomionego polecenia spowoduje niepowodzenie aprowizacji maszyny wirtualnej. Dzieje się tak, ponieważ jest ono uruchamiane po podstawowej funkcji inicjowania obsługi administracyjnej, która występuje w pierwszych 3 etapach inicjowania usługi Cloud-init. Aby rozwiązać problem z tym, dlaczego konfiguracja nie została zastosowana, Przejrzyj dzienniki w kroku 3 i moduły Cloud-init ręcznie. Na przykład:

- `runcmd` -czy skrypty są uruchamiane bez błędów? Uruchom konfigurację ręcznie z poziomu terminalu, aby upewnić się, że działają one zgodnie z oczekiwaniami.
- Instalowanie pakietów — czy maszyna wirtualna ma dostęp do repozytoriów pakietów?
- Należy również sprawdzić `customData` konfigurację danych dostarczoną do maszyny wirtualnej, która znajduje się w temacie `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Następne kroki

Jeśli nadal nie można izolować, dlaczego usługa Cloud-init nie uruchomiła konfiguracji, należy dokładniej zapoznać się z informacjami o tym, co się dzieje w każdym etapie inicjowania chmury i po uruchomieniu modułów. Aby uzyskać więcej informacji, Zobacz szczegółowe informacje o [konfiguracji usługi Cloud-init](./cloud-init-deep-dive.md) .