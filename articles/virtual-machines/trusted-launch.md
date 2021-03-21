---
title: 'Wersja zapoznawcza: zaufane uruchomienia dla maszyn wirtualnych platformy Azure'
description: Dowiedz się więcej na temat zaufanego uruchamiania maszyn wirtualnych platformy Azure.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 01c5d4aaa3896e05bc743be309df050471ece5ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582055"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Zaufane uruchomienia dla maszyn wirtualnych platformy Azure (wersja zapoznawcza)

Platforma Azure oferuje zaufaną metodę uruchamiania jako bezproblemowe rozwiązanie do ulepszania zabezpieczeń maszyn wirtualnych [2. generacji](generation-2.md) . Zaufane uruchomienia chronią przed zaawansowanymi i trwałymi technikami ataków. Zaufane uruchomienia składają się z kilku rozwiązań infrastruktury skoordynowanej, które mogą być włączone niezależnie. Każda technologia zapewnia inną warstwę obrony przed wyrafinowanymi zagrożeniami.

> [!IMPORTANT]
> Zaufane uruchomienie wymaga utworzenia nowych maszyn wirtualnych. Nie można włączyć zaufanego uruchamiania na istniejących maszynach wirtualnych, które zostały pierwotnie utworzone bez niej.
>
> Zaufane uruchomienie jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
>
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Korzyści 

- Bezpiecznie Wdrażaj maszyny wirtualne przy użyciu zweryfikowanych obciążeń rozruchowych, jądra systemu operacyjnego i sterowników.
- Bezpieczne Ochrona kluczy, certyfikatów i wpisów tajnych na maszynach wirtualnych.
- Uzyskaj wgląd w szczegółowe dane i zapewnij integralność całego łańcucha rozruchowego.
- Upewnij się, że obciążenia są zaufane i możliwe do zweryfikowania.

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

**Obsługa rozmiaru**: wszystkie rozmiary maszyn wirtualnych [generacji 2](generation-2.md) , z wyjątkiem:

- Seria Lsv2 
- Seria M 
- Seria Mv2 
- Seria NDv4 
- Seria NVv4

**Obsługa systemu operacyjnego**:
- RedHat Enterprise Linux 8,3
- SUSE 15 Z DODATKIEM SP2
- Ubuntu 20,04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Regiony**: 
- South Central US
- Europa Północna

**Cennik**: brak dodatkowych kosztów dla istniejących cen maszyn wirtualnych.

**Następujące funkcje nie są obsługiwane w tej wersji zapoznawczej**:
- Backup
- Azure Site Recovery
- Galeria obrazów udostępnionych
- Dysk tymczasowych systemów operacyjnych
- Dysk udostępniony
- Obraz zarządzany
- Azure Dedicated Host 

## <a name="secure-boot"></a>Bezpieczny rozruch

W katalogu głównym zaufanego uruchamiania jest Bezpieczny rozruch dla maszyny wirtualnej. Ten tryb, który jest implementowany w oprogramowaniu układowym platformy, chroni przed instalacją rootkits i rozruchowych opartych na złośliwym oprogramowaniu. Bezpieczny rozruch działa tak, aby umożliwić rozruch tylko podpisanych systemów operacyjnych i sterowników. Ustanawia "korzeń zaufania" dla stosu oprogramowania na maszynie wirtualnej. Po włączeniu bezpiecznego rozruchu wszystkie składniki rozruchu systemu operacyjnego (ładujący rozruchu, jądra, Sterowniki jądra) muszą być podpisane przez zaufanych wydawców. System Windows i wybrane dystrybucje systemu Linux obsługują bezpieczny rozruch. W przypadku niepowodzenia uwierzytelniania przez bezpieczny rozruch obrazu, który został podpisany przez zaufanego wydawcę, maszyna wirtualna nie będzie mogła przeprowadzić rozruchu. Więcej informacji zawiera temat [Bezpieczny rozruch](/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Zaufane uruchomienia również przedstawiają vTPM dla maszyn wirtualnych platformy Azure. Jest to zwirtualizowana wersja sprzętu [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview)zgodna ze specyfikacją TPM 2.0. Służy jako dedykowany, bezpieczny magazyn dla kluczy i pomiarów. Zaufane uruchomienie zapewnia swoją maszynę wirtualną z własnym dedykowanym wystąpieniem modułu TPM, działającą w bezpiecznym środowisku poza zasięgiem dowolnej maszyny wirtualnej. VTPM umożliwia [zaświadczanie](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) przez zmierzenie całego łańcucha rozruchowego maszyny wirtualnej (UEFI, OS, system i sterowniki). 

Zaufane uruchomienie używa vTPM do wykonania zdalnego zaświadczania przez chmurę. Służy do sprawdzania kondycji platformy i podejmowania decyzji opartych na zaufaniu. W ramach kontroli kondycji zaufane uruchomienie może zaświadczać, że maszyna wirtualna została prawidłowo uruchomiona. Jeśli proces nie powiedzie się, prawdopodobnie dlatego, że na maszynie wirtualnej jest uruchomiony nieautoryzowany składnik, Azure Security Center będą wystawiać alerty integralności. Alerty zawierają szczegółowe informacje na temat tego, które składniki nie przechodzą kontroli integralności.

## <a name="virtualization-based-security"></a>Zabezpieczenia oparte na wirtualizacji

[Zabezpieczenia oparte na wirtualizacji](/windows-hardware/design/device-experiences/oem-vbs) (vbs) używają funkcji hypervisor do tworzenia bezpiecznego i izolowanego regionu pamięci. System Windows używa tych regionów do uruchamiania różnych rozwiązań zabezpieczeń z lepszą ochroną przed lukami i złośliwymi programami wykorzystującymi luki w zabezpieczeniach. Funkcja zaufanego uruchamiania pozwala włączyć funkcję integralności kodu funkcji hypervisor (zasady wymagające WYMUSZONEJ) i usługę Windows Defender Credential Guard.

ZASADY wymagające WYMUSZONEJ to zaawansowane środki zaradcze, które chronią procesy trybu jądra systemu Windows przed iniekcją i wykonywaniem złośliwych lub niezweryfikowanych kodów. Sprawdza sterowniki trybu jądra i pliki binarne przed ich uruchomieniem, uniemożliwiając załadowanie niepodpisanych plików do pamięci. Gwarantuje to, że ten kod wykonywalny nie może być modyfikowany, gdy będzie można go załadować. Aby uzyskać więcej informacji na temat VBS i zasady wymagające WYMUSZONEJ, zobacz [zabezpieczenia oparte na wirtualizacji (vbs) i funkcja hypervisor (zasady wymagające wymuszonej)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Za pomocą zaufanych funkcji uruchamiania i VBS można włączyć funkcję Windows Defender Credential Guard. Ta funkcja izoluje i chroni klucze tajne, tak aby tylko uprzywilejowane oprogramowanie systemowe mogły uzyskać do nich dostęp. Pomaga zapobiegać nieautoryzowanemu dostępowi do poufnych informacji i ataków kradzieży poświadczeń, takich jak ataki typu Pass-the-hash (PtH). Aby uzyskać więcej informacji, zobacz [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Integracja Security Center

Zaufane uruchomienie jest zintegrowane z Azure Security Center, aby upewnić się, że maszyny wirtualne są prawidłowo skonfigurowane. Azure Security Center będą stale oceniać zgodne maszyny wirtualne i wystawiać odpowiednie zalecenia.

- **Zalecenie dotyczące włączania bezpiecznego rozruchu** — to zalecenie dotyczy tylko maszyn wirtualnych obsługujących zaufane uruchomienie. Azure Security Center będzie identyfikować maszyny wirtualne, które mogą włączyć bezpieczny rozruch, ale wyłączać je. Zostanie wydane zalecenie dotyczące niskiej ważności, aby je włączyć.
- **Zalecenie dotyczące włączania vTPM** — Jeśli maszyna wirtualna ma włączone vTPM, Azure Security Center może użyć jej do zaświadczania gościa i zidentyfikowania zaawansowanych wzorców zagrożeń. Jeśli Azure Security Center identyfikuje maszyny wirtualne obsługujące zaufane uruchomienie i vTPM wyłączone, spowoduje to wystawienie rekomendacji o niskiej ważności. 
- **Ocena kondycji zaświadczania** — Jeśli maszyna wirtualna ma włączone vTPM, rozszerzenie Azure Security Center może zdalnie sprawdzić, czy maszyna wirtualna została uruchomiona w dobrej kondycji. Jest to tzw. zaświadczanie zdalne. Azure Security Center wystawia ocenę, wskazując stan zdalnego zaświadczania.

## <a name="azure-defender-integration"></a>Integracja z usługą Azure Defender

Jeśli maszyny wirtualne są prawidłowo skonfigurowane przy użyciu zaufanego uruchomienia, usługa Azure Defender może wykrywać problemy z kondycją maszyny wirtualnej i wysyłać do nich alerty.

- **Alert niepowodzenia zaświadczania maszyny wirtualnej** — usługa Azure Defender będzie okresowo wykonywać zaświadczanie na maszynach wirtualnych. Dzieje się to również po uruchomieniu maszyny wirtualnej. Jeśli zaświadczanie nie powiedzie się, zostanie wyzwolony alert o średniej ważności.
    Zaświadczanie maszyny wirtualnej może zakończyć się niepowodzeniem z następujących powodów:
    - Informacje zaświadczone, w tym dziennik rozruchowy, odbiegają od zaufanej linii bazowej. Może to wskazywać na załadowanie niezaufanych modułów i naruszenie bezpieczeństwa systemu operacyjnego.
    - Nie można zweryfikować oferty zaświadczania z vTPM zaświadczonej maszyny wirtualnej. Może to wskazywać, że złośliwe oprogramowanie jest obecne i może przechwycić ruch do vTPM.
    - Rozszerzenie zaświadczania na maszynie wirtualnej nie odpowiada. Może to oznaczać atak typu "odmowa usługi" przez złośliwe oprogramowanie lub administratora systemu operacyjnego.

    > [!NOTE]
    >  Ten alert jest dostępny dla maszyn wirtualnych z włączonym vTPM oraz z zainstalowanym rozszerzeniem zaświadczania. Aby zaświadczać, należy włączyć bezpieczny rozruch. Zaświadczanie nie powiedzie się, jeśli bezpieczny rozruch jest wyłączony. Jeśli musisz wyłączyć bezpieczny rozruch, możesz pominąć ten alert, aby uniknąć fałszywie dodatnich.

- **Alert dotyczący niezaufanego modułu jądra systemu Linux** — w przypadku zaufanego uruchamiania z włączonym bezpiecznym rozruchem można przeprowadzić rozruch maszyny wirtualnej nawet w przypadku niepowodzenia weryfikacji sterownika jądra i zabronionego ładowania. W takim przypadku usługa Azure Defender wystawia alert o niskiej ważności. Chociaż nie ma żadnego bezpośredniego zagrożenia, ponieważ niezaufany sterownik nie został załadowany, należy zbadać te zdarzenia. Rozważ następujące źródła:
    - Którego sterownika jądra nie powiodło się? Czy znasz ten sterownik i oczekujesz na jego załadowanie?
    - Czy to jest dokładna wersja sterownika, którego oczekuje? Czy pliki binarne sterownika są nienaruszone? Jeśli jest to sterownik innej firmy, czy dostawca przeszedł testy zgodności systemu operacyjnego w celu uzyskania podpisu?


## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczące zaufanego uruchamiania.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Dlaczego należy używać zaufanych uruchomień? Dla czego ma być chroniona ochrona przed uruchomieniem?

Zaufane zabezpieczenia uruchamiania dotyczące zestawów rozruchowych, rootkits i złośliwego oprogramowania na poziomie jądra. Te zaawansowane typy złośliwego oprogramowania działają w trybie jądra i pozostają ukryte dla użytkowników. Na przykład:
- Oprogramowanie układowe rootkits: te zestawy zastępują oprogramowanie układowe systemu BIOS maszyny wirtualnej, dzięki czemu można uruchomić programów typu rootkit przed systemem operacyjnym. 
- Zestawy rozruchowe: te zestawy zastępują program inicjujący systemu operacyjnego, aby maszyna wirtualna ładowała zestaw rozruchowy przed systemem operacyjnym.
- Rootkits jądra: te zestawy zastępują część jądra systemu operacyjnego, dzięki czemu programów typu rootkit może być uruchamiany automatycznie podczas ładowania systemu operacyjnego.
- Rootkits sterownika: te zestawy poudawać są jednym ze zaufanych sterowników używanych przez system operacyjny do komunikowania się ze składnikami maszyny wirtualnej.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Jakie są różnice między bezpiecznym rozruchem i mierzonym rozruchem?

W łańcuchu bezpieczny rozruch każdy krok w procesie rozruchu sprawdza podpis kryptograficzny kolejnych kroków. Na przykład system BIOS sprawdzi sygnaturę w module ładującym, a moduł ładujący sprawdzi sygnatury we wszystkich obiektach jądra, które ładuje, i tak dalej. Jeśli którykolwiek z obiektów zostanie naruszony, podpis nie będzie zgodny, a maszyna wirtualna nie zostanie uruchomiona. Więcej informacji zawiera temat [Bezpieczny rozruch](/windows-hardware/design/device-experiences/oem-secure-boot). Mierzony rozruch nie zatrzymuje procesu rozruchu, mierzy lub oblicza skrót kolejnych obiektów w łańcuchu i zapisuje skróty w rejestrach konfiguracji platformy (PCR) w vTPM. Mierzone rekordy rozruchowe są używane do monitorowania integralności rozruchowej.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Co się stanie w przypadku wykrycia błędu integralności?

Zaufane uruchomienia usługi Azure Virtual Machines są monitorowane w przypadku zaawansowanych zagrożeń. W przypadku wykrycia takich zagrożeń zostanie wyzwolony alert. Alerty są dostępne tylko w [warstwie standardowa](../security-center/security-center-pricing.md) Azure Security Center.
Azure Security Center okresowo przeprowadza zaświadczania. Jeśli zaświadczanie nie powiedzie się, zostanie wyzwolony alert o średniej ważności. Zaświadczanie o zaufaniu uruchamiania może zakończyć się niepowodzeniem z następujących powodów: 
- Informacje zaświadczone, które obejmują dziennik zaufanej bazy danych (TCB), odbiegają od zaufanej linii bazowej (na przykład gdy włączono bezpieczny rozruch). Może to wskazywać na załadowanie niezaufanych modułów i naruszenie bezpieczeństwa systemu operacyjnego.
- Nie można zweryfikować oferty zaświadczania z vTPM zaświadczonej maszyny wirtualnej. Może to wskazywać, że złośliwe oprogramowanie jest obecne i może przechwycić ruch do modułu TPM. 
- Rozszerzenie zaświadczania na maszynie wirtualnej nie odpowiada. Może to oznaczać atak typu "odmowa usługi" przez złośliwe oprogramowanie lub administratora systemu operacyjnego.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Jak działa zaufane uruchomienie w porównaniu z maszyną wirtualną z osłoną funkcji Hyper-V?
Chroniona maszyna wirtualna funkcji Hyper-V jest obecnie dostępna tylko w funkcji Hyper-V. [Chroniona maszyna wirtualna funkcji Hyper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) jest zwykle wdrażana w połączeniu z chronioną siecią szkieletową. Chroniona sieć szkieletowa składa się z usługi Ochrona hosta, co najmniej jednego hosta chronionego oraz zestawu chronionych maszyn wirtualnych. Maszyny wirtualne z osłoną funkcji Hyper-V są przeznaczone do użytku w sieciach szkieletowych, w których dane i stan maszyny wirtualnej muszą być chronione zarówno przez administratorów sieci szkieletowej, jak i niezaufane oprogramowanie, które mogą być uruchomione na hostach funkcji Hyper-V. Zaufane uruchomienia z drugiej strony można wdrożyć jako autonomiczną maszynę wirtualną lub zestawy skalowania maszyn wirtualnych na platformie Azure bez dodatkowego wdrażania i zarządzania usługą Ochrona hosta. Wszystkie funkcje zaufanego uruchamiania można włączyć przy użyciu prostej zmiany kodu wdrożenia lub pola wyboru na Azure Portal.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Jak można przekonwertować istniejące maszyny wirtualne na zaufane uruchomienia?
W przypadku maszyny wirtualnej 2. generacji ścieżka migracji do funkcji zaufanego uruchamiania jest przeznaczona do ogólnej dostępności.

## <a name="next-steps"></a>Następne kroki

Wdróż [zaufaną maszynę wirtualną uruchamiania przy użyciu portalu](trusted-launch-portal.md).