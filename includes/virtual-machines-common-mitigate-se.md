---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: a9146099951aba223a7b201c1613e1ec0ba617d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509484"
---
**Ostatnia aktualizacja dokumentu**: 12 listopada 2019 10:00 am.

Ujawnienie [nowej klasy luk w zabezpieczeniach procesora](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) znanych jako ataki z kanału po stronie wykonywania spekulacyjnego spowodowało problemy od klientów poszukujących większej przejrzystości.  

Firma Microsoft wdrożyła środki zaradcze dla wszystkich naszych usług w chmurze. Infrastruktura działająca na platformie Azure i izolowanie obciążeń klientów od innych użytkowników jest chroniona. Oznacza to, że potencjalni osoba atakująca korzystająca z tej samej infrastruktury nie może zaatakować aplikacji przy użyciu tych luk w zabezpieczeniach.

Platforma Azure korzysta z konserwowanej [pamięci](../articles/virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#maintenance-that-doesnt-require-a-reboot) , jeśli jest to możliwe, aby zminimalizować wpływ klienta i wyeliminować konieczność ponownego uruchomienia. Platforma Azure będzie nadal korzystać z tych metod podczas systemowo aktualizacji hosta i ochrony naszych klientów.

Więcej informacji o tym, jak zabezpieczenia są zintegrowane z każdym aspektem platformy Azure, jest dostępny w witrynie [dokumentacji zabezpieczeń platformy Azure](../articles/security/index.yml) . 

> [!NOTE] 
> Ponieważ ten dokument został po raz pierwszy opublikowany, ujawniono wiele wariantów tej klasy luk w zabezpieczeniach. Firma Microsoft jest w dalszym ciągu silnie zainwestowana w ochronę naszych klientów i zapewnia wskazówki. Ta strona zostanie zaktualizowana, ponieważ będziemy nadal mogli zwolnić dalsze poprawki. 
> 
> 12 listopada 2019, [Intel opublikował](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) Poradnik techniczny dotyczący luki w zabezpieczeniach rozszerzenia synchronizacji transakcyjnej Intel® (Intel® TSX) (TAA), do których przypisano [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135). Ta luka w zabezpieczeniach dotyczy procesorów Intel® Core® i procesorów Intel® Xeon®.  W Microsoft Azure wydano aktualizacje systemu operacyjnego i wdrażamy nowe włączenia mikrokodu, ponieważ są one udostępniane przez firmę Intel, w ramach naszej floty do ochrony naszych klientów przed nowymi lukami w zabezpieczeniach.   Platforma Azure ściśle współpracuje z technologią Intel, aby testować i weryfikować nowe włączenia mikrokodu przed opublikowaniem jej oficjalną wersją na platformie. 
>
> **Klienci, którzy uruchamiali niezaufany kod w ramach maszyny wirtualnej,** muszą podejmować działania w celu ochrony przed tymi usterkami, czytając poniżej, aby uzyskać dodatkowe wskazówki dotyczące wszystkich luk w zabezpieczeniach w kanale zwykłego wykonania (Microsoft advisor ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)i [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Inni klienci powinni przeznaczyć te luki w zabezpieczeniach w perspektywie z głębokością i wziąć pod uwagę wpływ wybranej konfiguracji na bezpieczeństwo i wydajność.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Utrzymywanie Aktualności systemów operacyjnych

Chociaż Aktualizacja systemu operacyjnego nie jest wymagana do izolowania aplikacji działających na platformie Azure od innych klientów platformy Azure, najlepszym rozwiązaniem jest zapewnienie aktualności oprogramowania. Najnowsze pakiety zbiorcze zabezpieczeń dla systemu Windows zawierają środki zaradcze dla kilku luk w zabezpieczeniach kanału po stronie wykonywania. Podobnie dystrybucje systemu Linux wydano wiele aktualizacji, aby rozwiązać te luki w zabezpieczeniach. Oto zalecane czynności do zaktualizowania systemu operacyjnego:

| Oferta | Zalecana akcja  |
|----------|---------------------|
| usług Azure Cloud Services  | Włącz funkcję [autoaktualizowania](../articles/cloud-services/cloud-services-how-to-configure-portal.md) lub upewnij się, że korzystasz z najnowszego systemu operacyjnego gościa. |
| Linux Virtual Machines platformy Azure | Zainstaluj aktualizacje z dostawcy systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Linux](#linux) w dalszej części tego dokumentu. |
| Windows Virtual Machines platformy Azure  | Zainstaluj najnowszą wersję zbiorczą zabezpieczeń.
| Inne usługi Azure PaaS | W przypadku klientów korzystających z tych usług nie jest wymagana żadna akcja. Platforma Azure automatycznie zachowuje aktualność wersji systemu operacyjnego. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Dodatkowe wskazówki w przypadku uruchamiania niezaufanego kodu 

Klienci, którzy zezwalają niezaufanym użytkownikom na wykonywanie dowolnego kodu, mogą chcieć zaimplementować niektóre dodatkowe funkcje zabezpieczeń w ramach usługi Azure Virtual Machines lub Cloud Services. Te funkcje chronią przed wektorami ujawniania wewnątrz procesów, które opisują kilka luk w zabezpieczeniach związanych z wykonywaniem.

Przykładowe scenariusze, w których zalecane są dodatkowe funkcje zabezpieczeń:

- Możesz zezwolić na kod, który nie jest zaufany do uruchamiania wewnątrz maszyny wirtualnej.  
    - Na *przykład możesz zezwolić jednemu z klientów na przekazywanie pliku binarnego lub skryptu, który następnie można wykonać w aplikacji*. 
- Zezwalasz użytkownikom, którym nie ufasz, zaloguj się do maszyny wirtualnej przy użyciu kont z niskimi uprawnieniami.   
    - Na *przykład użytkownik może zalogować się do jednej z maszyn wirtualnych przy użyciu pulpitu zdalnego lub protokołu SSH*.  
- Zezwalasz niezaufanym użytkownikom na dostęp do maszyn wirtualnych wdrożonych za pośrednictwem wirtualizacji zagnieżdżonej.  
    - *Na przykład można kontrolować hosta funkcji Hyper-V, ale przydzielać maszyny wirtualne niezaufanym użytkownikom*. 

Klienci, którzy nie implementują scenariusza obejmującego niezaufany kod, nie muszą włączać tych dodatkowych funkcji zabezpieczeń. 

## <a name="enabling-additional-security"></a>Włączanie dodatkowych zabezpieczeń 

Jeśli używasz niezaufanego kodu, możesz włączyć dodatkowe funkcje zabezpieczeń wewnątrz maszyny wirtualnej lub usługi w chmurze. Równolegle upewnij się, że system operacyjny jest aktualny, aby włączyć funkcje zabezpieczeń wewnątrz maszyny wirtualnej lub usługi w chmurze

### <a name="windows"></a>Windows 

Docelowy system operacyjny musi być aktualny, aby umożliwić korzystanie z tych dodatkowych funkcji zabezpieczeń. Chociaż liczne środki zaradcze są domyślnie włączone, dodatkowe funkcje opisane w tym miejscu muszą być włączone ręcznie i mogą spowodować wpływ na wydajność. 


**Krok 1. wyłączenie funkcji wielowątkowości na maszynie wirtualnej** — klienci z uruchomionym niezaufanym kodem na maszynie wirtualnej z funkcją Hyper-Threading będą musieli wyłączyć funkcję Hyper-Threading lub przenieść ją do rozmiaru maszyny wirtualnej bez funkcji Hyper-Threading. Odwołuje się do [tego dokumentu](../articles/virtual-machines/acu.md) , aby uzyskać listę rozmiarów maszyn wirtualnych z funkcją Hyper-Threading (stosunek vCPU do rdzeń to 2:1). Aby sprawdzić, czy maszyna wirtualna ma włączone wielowątkowość, zapoznaj się z poniższym skryptem przy użyciu wiersza polecenia systemu Windows z poziomu maszyny wirtualnej.

Wpisz `wmic` , aby wprowadzić interfejs interaktywny. Następnie wpisz poniższe polecenie, aby wyświetlić ilość procesorów fizycznych i logicznych na maszynie wirtualnej.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Jeśli liczba procesorów logicznych jest większa niż procesorów fizycznych (rdzenie), funkcja Hyper-Threading jest włączona.  W przypadku korzystania z maszyny wirtualnej z funkcją Hyper-Threading należy [skontaktować się z pomocą techniczną platformy Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) w celu wyłączenia funkcji Hyper-Threading.  Gdy funkcja Hyper-Threading zostanie wyłączona, **Obsługa będzie wymagała pełnego ponownego uruchomienia maszyny wirtualnej**. Zapoznaj się z artykułem [Core Count](#core-count) , aby dowiedzieć się, dlaczego liczba rdzeni maszyny wirtualnej zmniejszyła się.


**Krok 2**. równolegle z krok 1, postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) , aby sprawdzić, czy ochrona jest włączona przy użyciu modułu [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell.

> [!NOTE]
> Jeśli wcześniej pobrano ten moduł, konieczne będzie zainstalowanie najnowszej wersji.
>


Dane wyjściowe skryptu programu PowerShell powinny mieć poniższe wartości, aby sprawdzić poprawność włączonych ochrony przed tymi lukami w zabezpieczeniach:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Jeśli dane wyjściowe są widoczne `MDS mitigation is enabled: False` , [skontaktuj się z pomocą techniczną platformy Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) , aby uzyskać dostępne opcje zaradcze.



**Krok 3**. Aby włączyć obsługę systemu operacyjnego jądra (Kvas) i system operacyjny wtrysku docelowej gałęzi (BTI), postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) , aby włączyć ochronę przy użyciu `Session Manager` kluczy rejestru. Wymagany jest ponowny rozruch.


**Krok 4**. w przypadku wdrożeń korzystających z [wirtualizacji zagnieżdżonej](../articles/virtual-machines/windows/nested-virtualization.md) (tylko D3 i E3): te instrukcje są stosowane w ramach maszyny wirtualnej, która jest używana jako host funkcji Hyper-V.

1.  Postępuj zgodnie z instrukcjami w [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) , aby włączyć ochronę przy użyciu `MinVmVersionForCpuBasedMitigations` kluczy rejestru.
2.  Ustaw typ harmonogramu funkcji hypervisor, `Core` postępując zgodnie z instrukcjami znajdującymi się [tutaj](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Włączenie zestawu dodatkowych funkcji zabezpieczeń w programie wymaga pełnej Aktualności systemu operacyjnego. Niektóre środki zaradcze zostaną domyślnie włączone. W poniższej sekcji opisano funkcje, które są domyślnie wyłączone i/lub oparte na obsłudze sprzętu (włączenia mikrokodu). Włączenie tych funkcji może spowodować wpływ na wydajność. Dodatkowe instrukcje zawiera dokumentacja dostawcy systemu operacyjnego


**Krok 1. wyłączenie funkcji wielowątkowości na maszynie wirtualnej** — klienci z uruchomionym niezaufanym kodem na maszynie wirtualnej z funkcją Hyper-Threading będą musieli wyłączyć funkcję Hyper-Threading lub przenieść ją na maszynę wirtualną, która nie jest wielowątkowa.  Odwołuje się do [tego dokumentu](../articles/virtual-machines/acu.md) , aby uzyskać listę rozmiarów maszyn wirtualnych z funkcją Hyper-Threading (stosunek vCPU do rdzeń to 2:1). Aby sprawdzić, czy uruchomiono maszynę wirtualną z funkcją Hyper-threaded, uruchom `lscpu` polecenie na maszynie wirtualnej z systemem Linux. 

Jeśli `Thread(s) per core = 2` Funkcja Hyper-Threading została włączona. 

Jeśli `Thread(s) per core = 1` Funkcja Hyper-Threading została wyłączona. 

 
Przykładowe dane wyjściowe dla maszyny wirtualnej z włączonym wielowątkowością: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

W przypadku korzystania z maszyny wirtualnej z funkcją Hyper-Threading należy [skontaktować się z pomocą techniczną platformy Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) w celu wyłączenia funkcji Hyper-Threading.  Gdy funkcja Hyper-Threading zostanie wyłączona, **Obsługa będzie wymagała pełnego ponownego uruchomienia maszyny wirtualnej**. Zapoznaj się z artykułem [Core Count](#core-count) , aby dowiedzieć się, dlaczego liczba rdzeni maszyny wirtualnej zmniejszyła się.



**Krok 2**. Aby wyeliminować poniższe luki w zabezpieczeniach, Skorzystaj z dokumentacji dostawcy systemu operacyjnego:   
 
- [RedHat i CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Liczba rdzeni

Po utworzeniu maszyny wirtualnej z funkcją Hyper-Threading platforma Azure przydziela 2 wątki na rdzeń — są one nazywane procesorów wirtualnych vCPU. Gdy funkcja Hyper-Threading jest wyłączona, platforma Azure usuwa wątek i wyświetla w górę pojedyncze wątki (rdzenie fizyczne). Stosunek vCPU do procesora CPU to 2:1, więc gdy funkcja Hyper-Threading zostanie wyłączona, liczba procesorów w maszynie wirtualnej zostanie obniżona o połowę. Na przykład maszyna wirtualna D8_v3 jest maszyną wirtualną z funkcją Hyper-Threading działającą w 8 procesorów wirtualnych vCPU (2 wątki na rdzeń x 4 rdzeni).  Gdy funkcja Hyper-Threading jest wyłączona, procesor CPU powróci do 4 rdzeni fizycznych z 1 wątkiem na rdzeń. 

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wskazówki dotyczące poniższych ataków na kanał po stronie wykonywania spekulacyjnych, które mają wpływ na wiele nowoczesnych procesorów:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 — iniekcja docelowa (BTI)  
- CVE-2017-5754 — izolacja tabeli stron jądra (KPTI)
- CVE-2018-3639 – spekulacyjnego obejścia magazynu (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) — informacje o jądrze systemu Windows — Variant of Spectre Variant 1
 
[Błąd terminalu L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 — rozszerzenia firmy Intel do ochrony oprogramowania (Intel SGX)
- CVE-2018-3620 — systemy operacyjne i tryb zarządzania systemem (SMM)
- CVE-2018-3646 — wpływ na Virtual Machine Manager (VMM)

[Próbkowanie danych mikroarchitektury](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091-mikroarchitekturze próbkowanie pamięci (MDSUM)
- CVE-2018-12126-próbkowanie danych buforu magazynu mikroarchitektury (MSBDS)
- CVE-2018-12127 — próbkowanie danych portu ładowania mikroarchitektury (MLPDS)
- CVE-2018-12130 — próbkowanie danych buforu z mikroarchitekturą (MFBDS)

Asynchroniczne przerywanie transakcji rozszerzeń synchronizacji transakcyjnej (Intel® TSX):  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX asynchroniczne przerwanie transakcji (TAA)