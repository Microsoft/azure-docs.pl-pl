---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: e64e6b6abc921b1db6614ed36ba2e9c04fc86b1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87451333"
---
Ten artykuł zawiera zalecenia dotyczące zabezpieczeń dla usługi Azure Virtual Machines. Postępuj zgodnie z tymi zaleceniami, aby pomóc w spełnieniu obowiązków związanych z zabezpieczeniami opisanymi w naszym modelu dla współdzielonej odpowiedzialności. Zalecenia ułatwią również poprawę ogólnego bezpieczeństwa rozwiązań aplikacji sieci Web. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, zobacz [udostępnianie obowiązków w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Niektóre z tych zaleceń w tym artykule mogą być automatycznie rozwiązywane przez Azure Security Center. Azure Security Center to pierwszy wiersz obrony dla zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Następnie zaleca się rozwiązanie tych luk w zabezpieczeniach. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące zabezpieczeń w Azure Security Center](../articles/security-center/security-center-recommendations.md).

Aby uzyskać ogólne informacje na temat Azure Security Center, zobacz [co to jest Azure Security Center?](../articles/security-center/security-center-intro.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Podczas tworzenia niestandardowych obrazów maszyn wirtualnych należy zastosować najnowsze aktualizacje. | Przed utworzeniem obrazów Zainstaluj najnowsze aktualizacje systemu operacyjnego i dla wszystkich aplikacji, które będą częścią obrazu.  | - |
| Utrzymuj aktualność maszyn wirtualnych. | [Update Management](../articles/automation/update-management/update-mgmt-overview.md) rozwiązanie w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego na komputerach z systemem Windows i Linux na platformie Azure. | [Tak](../articles/security-center/security-center-apply-system-updates.md) |
| Utwórz kopię zapasową maszyn wirtualnych. | [Azure Backup](../articles/backup/backup-overview.md) pomaga chronić dane aplikacji i ma minimalne koszty operacyjne. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą wprowadzać usterki do aplikacji. Azure Backup chroni maszyny wirtualne z systemem Windows i Linux. | - |
| Używaj wielu maszyn wirtualnych w celu uzyskania większej odporności i dostępności. | Jeśli na maszynie wirtualnej są uruchomione aplikacje, które muszą mieć wysoką dostępność, Użyj wielu maszyn wirtualnych lub [zestawów dostępności](../articles/virtual-machines/windows/manage-availability.md). | - |
| Zastosuj strategię ciągłości działania i odzyskiwania po awarii (BCDR). | Azure Site Recovery umożliwia wybór spośród różnych opcji, które są przeznaczone do obsługi ciągłości biznesowej. Obsługuje inne scenariusze replikacji i trybu failover. Aby uzyskać więcej informacji, zobacz  [Informacje o Site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Bezpieczeństwo danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Szyfruj dyski systemu operacyjnego. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ułatwia szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS. Bez wymaganych kluczy zawartość szyfrowanych dysków nie jest czytelna. Szyfrowanie dysków chroni przechowywane dane przed nieautoryzowanym dostępem, które w przeciwnym razie byłyby możliwe w przypadku skopiowania dysku.| [Tak](../articles/security-center/security-center-apply-disk-encryption.md) |
| Szyfruj dyski danych. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ułatwia szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS. Bez wymaganych kluczy zawartość szyfrowanych dysków nie jest czytelna. Szyfrowanie dysków chroni przechowywane dane przed nieautoryzowanym dostępem, które w przeciwnym razie byłyby możliwe w przypadku skopiowania dysku.| -  |
| Ogranicz zainstalowane oprogramowanie. | Ogranicz zainstalowane oprogramowanie do tego, co jest wymagane, aby pomyślnie zastosować rozwiązanie. Te wskazówki ułatwiają zmniejszenie podatności na ataki. | - |
| Używaj oprogramowania antywirusowego lub chroniącego przed złośliwym kodem. | Na platformie Azure można korzystać z oprogramowania chroniącego przed złośliwym kodem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky pomogą. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, programami reklamujące i innymi zagrożeniami. Program Microsoft chroniący przed złośliwym kodem można wdrożyć na podstawie obciążeń aplikacji. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest dostępne tylko dla maszyn z systemem Windows. Użyj podstawowej bezpiecznej lub zaawansowanej konfiguracji niestandardowej. Aby uzyskać więcej informacji, zobacz artykuł [Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Bezpieczne przechowywanie kluczy i wpisów tajnych. | Uprość zarządzanie wpisami tajnymi i kluczami, dostarczając swoim właścicielom aplikacji bezpieczną, centralnie zarządzaną opcję. To zarządzanie zmniejsza ryzyko przypadkowego naruszenia lub wycieku. Azure Key Vault mogą bezpiecznie przechowywać klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń), które są certyfikowane do poziomu 2 trybu FIPS 140-2. Jeśli musisz użyć poziomu 3 trybu FIPs 140,2 do przechowywania kluczy i wpisów tajnych, możesz użyć [dedykowanego modułu HSM platformy Azure](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem 

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Scentralizowane uwierzytelnianie maszyn wirtualnych. | Uwierzytelnianie maszyn wirtualnych z systemami Windows i Linux można scentralizować przy użyciu [uwierzytelniania Azure Active Directory](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Monitoruj maszyny wirtualne. | Za pomocą [Azure monitor dla maszyn wirtualnych](../articles/azure-monitor/insights/vminsights-overview.md) można monitorować stan maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych. Problemy z wydajnością maszyny wirtualnej mogą prowadzić do przerw w działaniu usługi, co narusza zasadę zabezpieczeń. | - |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Ogranicz dostęp do portów zarządzania. | Osoby atakujące skanują zakresy adresów IP chmury publicznej pod kątem otwartych portów zarządzania i podejmują próby ataków typu "łatwe", takie jak typowe hasła i znane luki w zabezpieczeniach. Można użyć [dostępu do maszyny wirtualnej just-in-Time (JIT)](../articles/security-center/security-center-just-in-time.md) do blokowania ruchu przychodzącego na maszynach wirtualnych platformy Azure, co zmniejsza narażenie na ataki, zapewniając łatwe połączenia z maszynami wirtualnymi w razie potrzeby. | - |
| Ogranicz dostęp do sieci. | Sieciowe grupy zabezpieczeń pozwalają ograniczyć dostęp do sieci i kontrolować liczbę narażonych punktów końcowych. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby dowiedzieć się więcej o dodatkowych wymaganiach dotyczących zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz [dokumentację dotyczącą bezpiecznego programowania](../articles/security/fundamentals/abstract-develop-secure-apps.md).
