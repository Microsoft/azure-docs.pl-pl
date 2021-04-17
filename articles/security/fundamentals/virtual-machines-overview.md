---
title: Funkcje zabezpieczeń używane z maszynami wirtualnych platformy Azure
titleSuffix: Azure security
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, których można używać z usługą Azure Virtual Machines.
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
ms.openlocfilehash: 60f67ea618746c9f2b0cd65a9fbc7fb2b5fbfe86
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520007"
---
# <a name="azure-virtual-machines-security-overview"></a>Omówienie zabezpieczeń Virtual Machines Azure
Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, których można używać z maszynami wirtualnymi.

Usługa Azure Virtual Machines umożliwia elastyczne wdrażanie szerokiej gamy rozwiązań obliczeniowych. Usługa obsługuje systemy Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i Azure BizTalk Services. Dzięki czemu można wdrożyć dowolne obciążenie i dowolny język w niemal dowolnym systemie operacyjnym.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działania maszyny wirtualnej. Aplikacje można tworzyć i wdrażać z gwarancją, że dane są chronione i bezpieczne w wysoce bezpiecznych centrach danych.

Platforma Azure umożliwia tworzenie zgodnych rozwiązań z rozszerzonymi zabezpieczeniami, które:

* Chroń maszyny wirtualne przed wirusami i złośliwym oprogramowaniem.
* Szyfruj poufne dane.
* Zabezpieczanie ruchu sieciowego.
* Identyfikowanie i wykrywanie zagrożeń.
* Spełnianie wymagań dotyczących zgodności.  

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Na platformie Azure można używać oprogramowania do ochrony przed złośliwym kodem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Symantec. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, komputerami i innymi zagrożeniami.

Microsoft Antimalware dla Azure Cloud Services i Virtual Machines to funkcja ochrony w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie.  Microsoft Antimalware platformy Azure udostępnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w systemach platformy Azure.

Microsoft Antimalware platformy Azure to rozwiązanie z jednym agentem dla aplikacji i środowisk dzierżaw. Jest ona przeznaczona do uruchamiania w tle bez interwencji człowieka. Ochronę można wdrożyć w zależności od potrzeb obciążeń aplikacji, korzystając z podstawowej, domyślnej lub zaawansowanej konfiguracji niestandardowej, w tym monitorowania oprogramowania przed złośliwym kodem.

Dowiedz się więcej [o Microsoft Antimalware platformy Azure](antimalware.md) i dostępnych podstawowych funkcjach.

Dowiedz się więcej o oprogramowaniu chroniącym przed złośliwym oprogramowaniem, które pomaga chronić maszyny wirtualne:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak zainstalować i skonfigurować zabezpieczenia trend micro deep jako usługę na maszynie wirtualnej z systemem Windows](/previous-versions/azure/virtual-machines/extensions/trend)
* [Jak zainstalować i skonfigurować program Symantec Endpoint Protection na maszynie wirtualnej z systemem Windows](../../virtual-machines/extensions/symantec.md)
* [Rozwiązania w zakresie zabezpieczeń w Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Aby uzyskać jeszcze bardziej zaawansowaną ochronę, rozważ użycie [Windows Defender Advanced Threat Protection.](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) Dzięki Windows Defender ATP otrzymasz:

* [Zmniejszanie obszaru ataków](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Ochrona nowej generacji](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Ochrona i reagowanie dotyczące punktów końcowych](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Zautomatyzowane badanie i korygowanie](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Wskaźnik bezpieczeństwa](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Zaawansowane wyszukiwanie](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Zarządzanie i interfejsy API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Więcej informacji:

* [Rozpocznij z usługą WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Omówienie możliwości aplikacji WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Zwiększenie bezpieczeństwa klucza może zwiększyć bezpieczeństwo szyfrowania i uwierzytelniania. Możesz uprościć zarządzanie krytycznymi wpisami tajnymi i kluczami oraz ich bezpieczeństwo, przechowując je w Azure Key Vault.

Key Vault udostępnia opcję przechowywania kluczy w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziom 2. Klucze SQL Server do tworzenia kopii [](/sql/relational-databases/security/encryption/transparent-data-encryption) zapasowych lub przezroczystego szyfrowania danych mogą być przechowywane w u Key Vault przy użyciu dowolnych kluczy lub wpisów tajnych z aplikacji. Uprawnienia i dostęp do tych chronionych elementów są zarządzane za [pośrednictwem Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Więcej informacji:

* [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
* [Azure Key Vault bloga](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Szyfrowanie dysków maszyny wirtualnej

Azure Disk Encryption to nowa funkcja szyfrowania dysków maszyn wirtualnych z systemami Windows i Linux. Azure Disk Encryption używa standardowej w branży funkcji [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych.

Rozwiązanie jest zintegrowane z usługą Azure Key Vault, aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych oraz zarządzanie nimi w ramach subskrypcji magazynu kluczy. Gwarantuje to, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane podczas magazynowania w usłudze Azure Storage.

Więcej informacji:

* [Azure Disk Encryption maszyn wirtualnych IaaS](./azure-disk-encryption-vms-vmss.md)
* [Szybki start: szyfrowanie maszyny wirtualnej IaaS z systemem Windows za pomocą programu Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej

Azure Backup to skalowalne rozwiązanie, które pomaga chronić dane aplikacji bez inwestycji kapitałowych i minimalnych kosztów operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą wprowadzać błędy do aplikacji. Dzięki Azure Backup maszyny wirtualne z systemami Windows i Linux są chronione.

Więcej informacji:

* [Co to jest Azure Backup?](../../backup/backup-overview.md)
* [usługa Azure Backup — często zadawane pytania](../../backup/backup-azure-backup-faq.yml)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Ważną częścią strategii BCDR organizacji jest ustalenie, jak zapewnić działanie obciążeń i aplikacji firmowych w przypadku wystąpienia planowanych i nieplanowanych przestojów. Azure Site Recovery organizowanie replikacji, trybu failover i odzyskiwania obciążeń i aplikacji tak, aby były dostępne z lokalizacji pomocniczej, jeśli lokalizacja podstawowa nie działa.

Site Recovery:

* **Upraszcza strategię BCDR:** Site Recovery ułatwia obsługę replikacji, trybu failover i odzyskiwania wielu obciążeń biznesowych i aplikacji z jednej lokalizacji. Site Recovery replikacji i trybu failover, ale nie przechwytuje danych aplikacji ani nie ma żadnych informacji na ich temat.
* **Zapewnia elastyczną replikację:** przy Site Recovery można replikować obciążenia uruchomione na maszynach wirtualnych funkcji Hyper-V, maszynach wirtualnych VMware i serwerach fizycznych z systemem Windows/Linux.
* **Obsługuje tryb failover i odzyskiwanie:** Site Recovery testowe przechodzenie w tryb failover w celu obsługi próbnego odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Możesz również uruchomić planowane tryby failover (brak utraty danych) w przypadku przewidywanych przerw w działaniu lub nieplanowane tryby failover (minimalna utrata danych, zależna od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. Po zakończeniu pracy w trybu failover można wrócić po awarii do lokacji głównych. Usługa Site Recovery zapewnia plany odzyskiwania, które mogą uwzględniać skrypty i skoroszyty automatyzacji platformy Azure. Dzięki nim możesz dostosować tryb failover i odzyskiwanie dla aplikacji wielowarstwowych.
* **Eliminuje pomocnicze centra danych:** replikację można replikować do dodatkowej lokacji lokalnej lub na platformę Azure. Korzystanie z platformy Azure jako miejsca docelowego odzyskiwania po awarii eliminuje koszt i złożoność konserwacji lokacji dodatkowej. Replikowane dane są przechowywane w usłudze Azure Storage.
* **Integruje się z istniejącymi technologiami BCDR:** Site Recovery partnerów z funkcjami BCDR innych aplikacji. Możesz na przykład użyć funkcji Site Recovery, aby chronić SQL Server obciążenia firmowe. Obejmuje to natywną obsługę SQL Server zawsze wł. w celu zarządzania trybem failover grup dostępności.

Więcej informacji:

* [Co to jest Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [W jaki sposób działa usługa Azure Site Recovery?](../../site-recovery/azure-to-azure-architecture.md)
* [Jakie obciążenia są chronione przez Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Sieć wirtualna

Maszyny wirtualne muszą mieć łączność sieciową. Aby obsługiwać to wymaganie, platforma Azure wymaga, aby maszyny wirtualne łączyły się z siecią wirtualną platformy Azure.

Sieć wirtualna platformy Azure to konstrukcja logiczna zbudowana na podstawie fizycznej sieci szkieletowej platformy Azure. Każda logiczna sieć wirtualna platformy Azure jest odizolowana od wszystkich innych sieci wirtualnych platformy Azure. Ta izolacja pomaga zapewnić, że ruch sieciowy we wdrożeniach nie jest dostępny dla innych Microsoft Azure klientów.

Więcej informacji:

* [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)
* [Virtual Network omówienie](../../virtual-network/virtual-networks-overview.md)
* [Funkcje sieciowe i partnerstwa dla scenariuszy dla przedsiębiorstw](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie

Usługa Azure Security Center pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Security Center zapewnia zwiększony wgląd i kontrolę nad zabezpieczeniami zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w subskrypcjach platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby zostać niezauwagowane, i współpracuje z szerokim ekosystemem rozwiązań w zakresie zabezpieczeń.

Security Center optymalizację i monitorowanie zabezpieczeń maszyn wirtualnych przez:

* Dostarczanie [zaleceń dotyczących](../../security-center/security-center-recommendations.md) zabezpieczeń maszyn wirtualnych. Przykładowe zalecenia obejmują: stosowanie aktualizacji systemu, konfigurowanie punktów końcowych ACL, włączanie ochrony przed złośliwym kodem, włączanie sieciowych grup zabezpieczeń i stosowanie szyfrowania dysków.
* Monitorowanie stanu maszyn wirtualnych.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../../security-center/security-center-introduction.md)
* [Azure Security Center często zadawanych pytań](../../security-center/faq-general.md)
* [Azure Security Center planowania i operacji](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Zgodność

Usługa Azure Virtual Machines ma certyfikat FISMA, FedRAMP, HIPAA, PCI DSS poziom 1 i innych kluczowych programów zgodności. Ten certyfikat ułatwia aplikacjom platformy Azure spełnianie wymagań dotyczących zgodności oraz spełnianie szerokiego zakresu krajowych i międzynarodowych wymagań prawnych.

Więcej informacji:

* [Centrum zaufania firmy Microsoft: zgodność](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Zaufana chmura: Microsoft Azure bezpieczeństwo, prywatność i zgodność](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Poufne przetwarzanie

Poufne przetwarzanie nie jest technicznie częścią zabezpieczeń maszyny wirtualnej, ale temat zabezpieczeń maszyny wirtualnej należy do tematu "obliczeń" wyższego poziomu. Poufne przetwarzanie należy do kategorii zabezpieczeń "obliczenia".

Poufne przetwarzanie zapewnia, że gdy dane są "w jasnej postaci", co jest wymagane do wydajnego przetwarzania, dane są chronione w zaufanym środowisku wykonywania (TEE — nazywanym również enklawą), co przedstawiono na poniższym  https://en.wikipedia.org/wiki/Trusted_execution_environment rysunku.  

TeE zapewniają, że nie ma możliwości wyświetlania danych ani operacji z zewnątrz, nawet za pomocą debugera. Zapewniają nawet, że tylko autoryzowany kod może uzyskać dostęp do danych. Jeśli kod zostanie zmieniony lub naruszony, operacje zostaną odrzucone i środowisko zostanie wyłączone. TeE wymusza te zabezpieczenia podczas wykonywania kodu w nim.

Więcej informacji:

* [Wprowadzenie do poufnego przetwarzania na platformie Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Poufne przetwarzanie na platformie Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Następne kroki

Dowiedz się [więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń](iaas.md) maszyn wirtualnych i systemów operacyjnych.