---
title: Wymagania dotyczące dołączania do SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Wymagania dotyczące dołączania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675617"
---
# <a name="onboarding-requirements"></a>Wymagania dotyczące dołączania

Ta lista składa wymagania dotyczące uruchamiania SAP HANA na platformie Azure (duże wystąpienia).

**Microsoft Azure**

- Subskrypcja platformy Azure, która może być połączona z SAP HANA na platformie Azure (duże wystąpienia).
- Umowa dotycząca pomocy technicznej Premier firmy Microsoft. Aby uzyskać szczegółowe informacje dotyczące uruchamiania oprogramowania SAP na platformie Azure, zobacz [uwagi dotyczące pomocy technicznej sap #2015553 — SAP on Microsoft Azure: support — wymagania wstępne](https://launchpad.support.sap.com/#/notes/2015553). W przypadku używania dużych wystąpień platformy HANA z 384 i więcej procesorów CPU należy również zwiększyć umowę pomocy technicznej Premier, aby uwzględnić Azure Rapid Response.
- Świadomość potrzebnych magazynów dużych wystąpień platformy HANA, które są potrzebne po dokonaniu zmiany rozmiarów przy użyciu oprogramowania SAP.

**Łączność sieciowa**

- ExpressRoute między środowiskiem lokalnym i platformą Azure: Aby połączyć lokalne centrum danych z platformą Azure, upewnij się, że zawarto zamówienie od usługodawcy internetowego. Łączność między jednostkami dużej instancji HANA i platformą Azure korzysta również z technologii ExpressRoute. To połączenie ExpressRoute między jednostkami dużej wystąpienia HANA i platformą Azure jest uwzględniane w cenie jednostek dużego wystąpienia HANA, w tym wszystkich opłat za transfer danych przychodzących i wychodzących dla tego konkretnego obwodu usługi ExpressRoute. W związku z tym klient nie napotyka dodatkowych kosztów poza linkiem ExpressRoute między środowiskiem lokalnym i platformą Azure.

**System operacyjny**

- Licencje dla SUSE Linux Enterprise Server 12 dla aplikacji SAP.

   > [!NOTE] 
   > System operacyjny dostarczany przez firmę Microsoft nie jest zarejestrowany w systemie SUSE. Nie jest on połączony z wystąpieniem narzędzia do zarządzania subskrypcjami.

- Narzędzie do zarządzania subskrypcjami systemu SUSE Linux wdrożone na platformie Azure na maszynie wirtualnej. To narzędzie udostępnia możliwości SAP HANA na platformie Azure (duże wystąpienia), które mają zostać zarejestrowane i odpowiednio zaktualizowane przez SUSE. (W centrum danych dużych wystąpień HANA nie ma dostępu do Internetu). 
- Licencje dla Red Hat Enterprise Linux 6,7 lub 7. x dla SAP HANA.

   > [!NOTE]
   > System operacyjny dostarczany przez firmę Microsoft nie jest zarejestrowany w systemie Red Hat. Nie jest on połączony z wystąpieniem programu Red Hat Subscription Manager.

- Red Hat Subscription Manager wdrożony na platformie Azure na maszynie wirtualnej. Program Red Hat Subscription Manager oferuje możliwość SAP HANA na platformie Azure (duże wystąpienia), które mają zostać zarejestrowane i odpowiednio zaktualizowane przez firmę Red Hat. (Nie ma bezpośredniego dostępu do Internetu z dzierżawy wdrożonej w sygnaturze dużej instancji platformy Azure).
- SAP wymaga również posiadania kontraktu pomocy technicznej dotyczącej dostawcy systemu Linux. To wymaganie nie jest usuwane przez rozwiązanie dużego wystąpienia HANA lub fakt, że jest uruchamiany system Linux na platformie Azure. W przeciwieństwie do niektórych obrazów z galerii systemu Linux Azure opłata za usługę *nie* jest uwzględniana w ofercie rozwiązania dla dużych wystąpień Hana. Ponosisz odpowiedzialność za spełnienie wymagań SAP dotyczących umów pomocy technicznej z dystrybutorem systemu Linux. 
   - W przypadku systemu SUSE Linux zapoznaj się z wymaganiami umów pomocy technicznej w temacie [SAP uwaga #1984787-SUSE Linux Enterprise Server 12: uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787) i [SAP Uwaga #1056161-SUSE Priority support for SAP Applications](https://launchpad.support.sap.com/#/notes/1056161).
   - W systemie Red Hat Linux należy dysponować prawidłowymi poziomami subskrypcji obejmującymi aktualizacje pomocy technicznej i usługi do systemów operacyjnych z dużym wystąpieniem usługi HANA. Red Hat zaleca Red Hat Enterprise Linux subskrypcję rozwiązania SAP. Odwołaj się do https://access.redhat.com/solutions/3082481 . 

Aby uzyskać macierz pomocy technicznej dla różnych wersji SAP HANA z różnymi wersjami systemu Linux, zobacz [uwagi dotyczące oprogramowania SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Aby uzyskać informacje na temat macierzy zgodności systemów operacyjnych i oprogramowania sprzętowego//////lub sterownika programu//lub wersji, zobacz [uaktualnienie systemu operacyjnego dla](os-upgrade-hana-large-instance.md)


> [!IMPORTANT] 
> W tym momencie jest obsługiwana tylko wersja systemu operacyjnego SLES 12 SP2. 


**Database** (Baza danych)

- Licencje i składniki instalacji oprogramowania dla SAP HANA (platforma lub Enterprise Edition).

**Aplikacje**

- Licencje i składniki instalacji oprogramowania dla wszystkich aplikacji SAP, które łączą się z SAP HANA i powiązane kontrakty pomocy technicznej SAP.
- Licencje i składniki instalacji oprogramowania dla wszystkich aplikacji innych niż SAP używanych z SAP HANA na platformie Azure (duże wystąpienia) i powiązane kontrakty pomocy technicznej.

**Umiejętności**

- Środowisko i znajomość usługi Azure IaaS oraz jej składników.
- Środowisko i znajomość sposobu wdrażania obciążeń SAP na platformie Azure.
- SAP HANA certyfikat instalacji osobistej.
- Umiejętności architekta SAP do projektowania wysokiej dostępności i odzyskiwania po awarii na SAP HANA.

**SAP**

- Oczekujemy, że jesteś klientem SAP i masz umowę na pomoc techniczną dotyczącą oprogramowania SAP.
- Szczególnie w przypadku implementacji klasy typu II z magazynów dużych wystąpień usługi HANA zapoznaj się z programem SAP w wersjach SAP HANA i ostatecznej konfiguracji na potrzeby sprzętu skalowalnego w poziomie.

**Następne kroki**
- Rozpoznaj [architekturę SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)