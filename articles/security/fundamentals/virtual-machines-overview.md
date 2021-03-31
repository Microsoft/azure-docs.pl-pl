---
title: Funkcje zabezpieczeń używane z maszynami wirtualnymi platformy Azure
titleSuffix: Azure security
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z usługą Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 036a1d3f8cad4ba9cda03a9001cfec1bb7de86b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715041"
---
# <a name="azure-virtual-machines-security-overview"></a>Omówienie zabezpieczeń usługi Azure Virtual Machines
Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z maszynami wirtualnymi.

Możesz użyć usługi Azure Virtual Machines do wdrożenia szerokiego zakresu rozwiązań obliczeniowych w sposób Agile. Usługa obsługuje systemy Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i Azure BizTalk Services. Pozwala to na wdrożenie dowolnego obciążenia i dowolnego języka w prawie każdym systemie operacyjnym.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działania maszyny wirtualnej. Możesz tworzyć i wdrażać aplikacje z gwarancją, że dane są chronione i bezpieczne w wysoce zabezpieczonych centrach danych.

Na platformie Azure można tworzyć rozwiązania zgodne z ulepszonymi zabezpieczeniami, które:

* Chroń maszyny wirtualne przed wirusami i złośliwym oprogramowaniem.
* Szyfruj poufne dane.
* Bezpieczny ruch sieciowy.
* Identyfikowanie i wykrywanie zagrożeń.
* Spełnianie wymagań dotyczących zgodności.  

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Na platformie Azure możesz używać oprogramowania chroniącego przed złośliwym kodem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky pomogą. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, programami reklamujące i innymi zagrożeniami.

Firma Microsoft chroniąca przed złośliwym kodem Cloud Services i Virtual Machines to funkcja ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania.  Program Microsoft chroniący przed złośliwym kodem dla systemu Azure udostępnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w swoich systemach platformy Azure.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla systemu Azure to rozwiązanie o pojedynczej agencie dla aplikacji i środowisk dzierżawców. Jest ona przeznaczona do uruchamiania w tle bez interwencji człowieka. Ochronę można wdrożyć na podstawie potrzeb obciążeń aplikacji z podstawową, bezpieczną lub zaawansowaną konfiguracją niestandardową, w tym z monitorowaniem złośliwego oprogramowania.

Dowiedz się więcej o [programie Microsoft chroniącym przed złośliwym kodem dla platformy Azure](antimalware.md) i dostępnych podstawowych funkcjach.

Dowiedz się więcej o oprogramowaniu chroniącym przed złośliwym kodem, aby pomóc w ochronie maszyn wirtualnych:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak zainstalować i skonfigurować Trend Micro głębokiego zabezpieczenia jako usługi na maszynie wirtualnej z systemem Windows](/previous-versions/azure/virtual-machines/extensions/trend)
* [Jak zainstalować i skonfigurować Endpoint Protection firmy Symantec na maszynie wirtualnej z systemem Windows](../../virtual-machines/extensions/symantec.md)
* [Rozwiązania zabezpieczeń w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

W celu zapewnienia jeszcze bardziej zaawansowanej ochrony należy rozważyć użycie funkcji [zaawansowanej ochrony przed zagrożeniami w usłudze Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Usługa Windows Defender ATP zapewnia następujące korzyści:

* [Zmniejszanie obszaru ataków](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Ochrona nowej generacji](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection i odpowiedź](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Zautomatyzowane badanie i korygowanie](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Wskaźnik bezpieczeństwa](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Zaawansowany łowiectwo](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Zarządzanie i interfejsy API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Więcej informacji:

* [Wprowadzenie do WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Przegląd możliwości WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Zwiększenie bezpieczeństwa klucza może zwiększyć ochronę szyfrowania i uwierzytelniania. Można uprościć zarządzanie i zabezpieczanie kluczowych kluczy tajnych i kluczy, przechowując je w Azure Key Vault.

Key Vault udostępnia opcję przechowywania kluczy w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) certyfikowanych do standardów standardu FIPS 140-2 Level 2. Klucze szyfrowania SQL Server na potrzeby tworzenia kopii zapasowej lub [przezroczystego szyfrowania danych](/sql/relational-databases/security/encryption/transparent-data-encryption) mogą być przechowywane w Key Vault z użyciem dowolnych kluczy lub wpisów tajnych aplikacji. Uprawnienia i dostęp do tych chronionych elementów są zarządzane za poorednictwem [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Więcej informacji:

* [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
* [Blog Azure Key Vault](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Szyfrowanie dysku maszyny wirtualnej

Azure Disk Encryption to nowa funkcja szyfrowania dysków maszyn wirtualnych z systemami Windows i Linux. Azure Disk Encryption używa standardowej funkcji [funkcji BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych.

Rozwiązanie jest zintegrowane z Azure Key Vault, które ułatwiają sterowanie kluczami szyfrowania dysków i wpisami tajnymi w ramach subskrypcji magazynu kluczy oraz zarządzanie nimi. Gwarantuje to, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage.

Więcej informacji:

* [Azure Disk Encryption maszyn wirtualnych IaaS](./azure-disk-encryption-vms-vmss.md)
* [Szybki start: szyfrowanie maszyny wirtualnej IaaS z systemem Windows za pomocą programu Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej

Azure Backup to skalowalne rozwiązanie, które pomaga chronić dane aplikacji bez konieczności inwestowania kapitału i minimalnych kosztów operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą wprowadzać usterki do aplikacji. W przypadku Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

Więcej informacji:

* [Co to jest Azure Backup?](../../backup/backup-overview.md)
* [Często zadawane pytania dotyczące usługi Azure Backup](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Istotną częścią strategii BCDR organizacji jest ustalenie, jak utrzymywać obciążenia i aplikacje firmowe w przypadku wystąpienia planowanych i nieplanowanych przestojów. Azure Site Recovery pomaga organizować replikację, pracę w trybie failover oraz odzyskiwanie obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja główna ulegnie awarii.

Site Recovery:

* **Upraszcza strategię BCDR**: Site Recovery ułatwia obsługę replikacji, trybu failover i odzyskiwania wielu obciążeń i aplikacji firmowych z jednej lokalizacji. Site Recovery organizuje replikację i pracę w trybie failover, ale nie przechwytuje danych aplikacji ani nie zawiera żadnych informacji o nim.
* **Zapewnia elastyczną replikację**: korzystając z Site Recovery, można replikować obciążenia uruchomione na maszynach wirtualnych funkcji Hyper-V, maszynach wirtualnych VMware oraz serwerach fizycznych z systemem Windows/Linux.
* **Obsługuje tryb failover i odzyskiwanie**: Site Recovery umożliwia testowanie pracy w trybie failover w celu obsługi szczegółów odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Możesz również uruchomić planowane tryby failover (brak utraty danych) w przypadku przewidywanych przerw w działaniu lub nieplanowane tryby failover (minimalna utrata danych, zależna od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. Po przejściu w tryb failover można wrócić do lokacji głównych. Usługa Site Recovery zapewnia plany odzyskiwania, które mogą uwzględniać skrypty i skoroszyty automatyzacji platformy Azure. Dzięki nim możesz dostosować tryb failover i odzyskiwanie dla aplikacji wielowarstwowych.
* **Eliminuje dodatkowe centra** danych: można przeprowadzić replikację do dodatkowej lokacji lokalnej lub do platformy Azure. Korzystanie z platformy Azure jako miejsca docelowego do odzyskiwania po awarii eliminuje koszt i złożoność utrzymywania lokacji dodatkowej. Zreplikowane dane są przechowywane w usłudze Azure Storage.
* **Integruje się z istniejącymi technologiami BCDR**: Partnerzy Site Recovery z funkcjami BCDR innych aplikacji. Na przykład można użyć Site Recovery, aby pomóc w ochronie SQL Server zaplecza obciążeń firmowych. Obejmuje to natywną obsługę usługi SQL Server, która jest zawsze włączona, aby zarządzać trybem failover grup dostępności.

Więcej informacji:

* [Co to jest Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [W jaki sposób działa usługa Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Jakie obciążenia są chronione przez Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Sieć wirtualna

Maszyny wirtualne muszą mieć łączność sieciową. Aby zapewnić obsługę tego wymagania, platforma Azure wymaga, aby maszyny wirtualne były połączone z siecią wirtualną platformy Azure.

Usługa Azure Virtual Network to konstrukcja logiczna oparta na fizycznej sieci szkieletowej platformy Azure. Każda logiczna Sieć wirtualna platformy Azure jest odizolowana od wszystkich innych sieci wirtualnych platformy Azure. Ta izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępny dla innych klientów Microsoft Azure.

Więcej informacji:

* [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)
* [Przegląd Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [Funkcje sieciowe i partnerstwo dla scenariuszy dla przedsiębiorstw](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie

Usługa Azure Security Center pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Security Center zapewnia większą widoczność i kontrolę nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i działać z szerokim ekosystemem rozwiązań zabezpieczających.

Security Center pomaga zoptymalizować i monitorować zabezpieczenia maszyn wirtualnych, wykonując następujące działania:

* Zapewnianie [zaleceń dotyczących zabezpieczeń](../../security-center/security-center-recommendations.md) dla maszyn wirtualnych. Przykładowe zalecenia: Zastosuj aktualizacje systemu, skonfiguruj punkty końcowe list kontroli dostępu, Włącz ochronę przed złośliwym kodem, Włącz grupy zabezpieczeń sieci i Zastosuj szyfrowanie dysków.
* Monitorowanie stanu maszyn wirtualnych.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../../security-center/security-center-introduction.md)
* [Azure Security Center często zadawane pytania](../../security-center/faq-general.md)
* [Azure Security Center planowanie i operacje](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Zgodność

Usługa Azure Virtual Machines ma certyfikat dla FISMA, FedRAMP, HIPAA, PCI DSS poziomu 1 i innych programów do oceny zgodności. Ten certyfikat ułatwia korzystanie z własnych aplikacji platformy Azure w celu spełnienia wymagań dotyczących zgodności oraz dla Twojej firmy.

Więcej informacji:

* [Centrum zaufania firmy Microsoft: zgodność](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Zaufana chmura: Microsoft Azure zabezpieczenia, prywatność i zgodność](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Poufne przetwarzanie

Chociaż dane poufne nie są technicznie częścią zabezpieczeń maszyn wirtualnych, temat zabezpieczeń maszyn wirtualnych należy do podmiotu zabezpieczeń "COMPUTE" wyższego poziomu. Przetwarzanie poufne należy do kategorii zabezpieczeń "obliczeniowe".

Ochrona danych poufnych gwarantuje, że gdy dane są "jasne", co jest wymagane do wydajnego przetwarzania, dane są chronione wewnątrz zaufanego środowiska wykonawczego  https://en.wikipedia.org/wiki/Trusted_execution_environment (tee-znanego również jako enklawy), przykładem, który pokazano na poniższym rysunku.  

TEEs upewnij się, że nie ma sposobu wyświetlania danych lub wykonywania operacji wewnątrz poza programem, nawet z debugerem. Nawet zapewniają, że dostęp do danych jest dozwolony tylko w autoryzowanym kodzie. Jeśli kod zostanie zmieniony lub naruszony, operacje są odrzucane i środowisko wyłączone. TEE wymusza te zabezpieczenia w czasie wykonywania kodu.

Więcej informacji:

* [Wprowadzenie do rozwiązania do przetwarzania poufnego platformy Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Poufne przetwarzanie na platformie Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Następne kroki

Poznaj [najlepsze rozwiązania](iaas.md) w zakresie zabezpieczeń dotyczące maszyn wirtualnych i systemów operacyjnych.