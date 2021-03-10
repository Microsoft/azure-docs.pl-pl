---
title: Informacje o usłudze Cloud-init
description: Głębokie szczegółowe na potrzeby poznania udostępniania maszyny wirtualnej platformy Azure przy użyciu funkcji Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 94dd57310375febb4bc9a55efa704a5fbf4e80e8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559447"
---
# <a name="diving-deeper-into-cloud-init"></a>Dokładniejsze umieszczenie w usłudze Cloud-init
Aby dowiedzieć się więcej o programie [Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) lub rozwiązywaniu problemów z nim na wyższym poziomie, należy zrozumieć, jak działa. W tym dokumencie przedstawiono ważne części i wyjaśniono specyficzne dla platformy Azure.

Gdy usługa Cloud-init jest zawarta w uogólnionym obrazie, a maszyna wirtualna jest tworzona na podstawie tego obrazu, będzie ona przetwarzać konfiguracje i działać przez 5 etapów podczas początkowego rozruchu. Te etapy przedstawiają, jak pokazuje, w jaki sposób program Cloud-init będzie stosował konfiguracje. 


## <a name="understand-cloud-init-configuration"></a>Informacje o konfiguracji Cloud-Init
Konfigurowanie maszyny wirtualnej do uruchamiania na platformie, oznacza, że usługa Cloud-init musi zastosować wiele konfiguracji, jako odbiorca obrazu, podstawowe konfiguracje, z którymi będziesz korzystać, to `User data` (CustomData), które obsługują wiele formatów, zostały opisane [tutaj](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). Istnieje również możliwość dodawania i uruchamiania skryptów (/var/lib/Cloud/scripts) w celu uzyskania dodatkowej konfiguracji, poniżej omawia się to bardziej szczegółowo.

Niektóre konfiguracje są już rozszerzania do obrazów w portalu Azure Marketplace, które są dostarczane z usługą Cloud-init, na przykład:

1. **Źródło danych w chmurze** — Usługa Cloud-init zawiera kod, który może współistnieć z platformami w chmurze. są one nazywane "DataSources". Po utworzeniu maszyny wirtualnej na podstawie obrazu z inicjowania chmury na [platformie Azure usługa](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)Cloud-init ładuje źródło danych platformy Azure, które będzie współdziałać z punktami końcowymi metadanych platformy Azure w celu uzyskania konfiguracji specyficznej dla maszyny wirtualnej.
2. **Konfiguracja środowiska uruchomieniowego** (/Run/Cloud-init)
3. **Konfiguracja obrazu** (/etc/Cloud), taka jak `/etc/cloud/cloud.cfg` , `/etc/cloud/cloud.cfg.d/*.cfg` . Przykładem użycia tej funkcji na platformie Azure jest to, że w przypadku obrazów systemu operacyjnego Linux, które są dostępne w usłudze Cloud-init, można używać dyrektywy DataSource platformy Azure, która informuje chmurę o tym, jakie źródła danych powinny być używane, zapisuje czas inicjowania chmury:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Etapy rozruchowe w chmurze i init (Konfiguracja przetwarzania)

W przypadku inicjowania obsługi administracyjnej przy użyciu usługi Cloud-init istnieje 5 etapów rozruchu, w których Konfiguracja procesu jest wyświetlana w dziennikach.

1. [Etap generatora](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): systemowy generator w chmurze jest uruchamiany i określa, że funkcja Cloud-init powinna zostać uwzględniona w celach rozruchu, a jeśli tak, to umożliwia włączenie funkcji Cloud-init. 

2. Usługa Cloud [-init Local Stage](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): w tym miejscu Cloud-init będzie szukać lokalnego źródła danych "Azure", które umożliwi interfejsowi Cloud-init korzystanie z platformy Azure, a także zastosowanie konfiguracji sieci, w tym powrotu.

3. [Etap inicjowania usługi Cloud-init (Sieć)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): sieć powinna być w trybie online, a informacje o karcie sieciowej i tabeli tras powinny zostać wygenerowane. Na tym etapie zostaną uruchomione moduły wymienione w `cloud_init_modules` /etc/Cloud/Cloud.cfg. Maszyna wirtualna na platformie Azure zostanie zainstalowana, dysk tymczasowych zostanie sformatowany, nazwa hosta zostanie ustawiona wraz z innymi zadaniami.

   Oto niektóre z następujących elementów `cloud_init_modules` :
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Po tym etapie Usługa Cloud-init będzie sygnalizować platformę Azure, że maszyna wirtualna została pomyślnie zainicjowana. Niektóre moduły mogły się nie powiodło, nie wszystkie błędy modułów spowodują niepowodzenie aprowizacji.

4. [Etap konfiguracji usługi Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): na tym etapie `cloud_config_modules` zostaną uruchomione moduły zdefiniowane i wymienione w/etc/Cloud/Cloud.cfg.


5. [Cloud-init Final etap](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): na tym ostatnim etapie zostaną uruchomione moduły w `cloud_final_modules` , wymienione w/etc/Cloud/Cloud.cfg. W tym miejscu moduły, które muszą być uruchamiane w trybie opóźnionym w przebiegu procesu rozruchu, takie jak instalacje pakietów i uruchamianie skryptów itp. 

   -   Na tym etapie można uruchamiać skrypty, umieszczając je w katalogach w obszarze `/var/lib/cloud/scripts` :
   - `per-boot` -skrypty w tym katalogu, uruchamiane przy każdym ponownym uruchomieniu
   - `per-instance` -skrypty w tym katalogu są uruchamiane podczas pierwszego uruchomienia nowego wystąpienia
   - `per-once` -skrypty w tym katalogu są uruchamiane tylko raz

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów z usługą Cloud-init](cloud-init-troubleshooting.md).
