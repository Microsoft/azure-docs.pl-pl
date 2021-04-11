---
title: Rozszerzenia dla Cloud Services (obsługa rozszerzona)
description: Rozszerzenia dla Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220921"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Rozszerzenia dla Cloud Services (obsługa rozszerzona)

Rozszerzenia to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na rolach. Można na przykład włączyć połączenie Pulpit zdalny w roli w ramach wdrożenia usługi w chmurze (obsługa rozszerzona) przy użyciu rozszerzenia Pulpit zdalny.  

## <a name="remote-desktop-extension"></a>Pulpit zdalny rozszerzenie

Pulpit zdalny umożliwia dostęp do pulpitu roli działającej na platformie Azure. Za pomocą połączenia pulpitu zdalnego można rozwiązywać problemy i diagnozować je w trakcie działania aplikacji.

Możesz włączyć Podłączanie pulpitu zdalnego w swojej roli podczas opracowywania, dołączając moduły pulpitu zdalnego w definicji usługi lub za pomocą rozszerzenia pulpitu zdalnego. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pulpitu zdalnego na podstawie Azure Portal](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Rozszerzenie Diagnostyka Azure systemu Windows

Kluczowe metryki wydajności można monitorować dla każdej usługi w chmurze. Każda rola usługi w chmurze zbiera minimalne dane: użycie procesora, użycie sieci i użycie dysku. Jeśli usługa w chmurze ma rozszerzenie Microsoft. Azure. Diagnostics zastosowane do roli, ta rola może zbierać dodatkowe punkty danych. 

W przypadku podstawowego monitorowania dane liczników wydajności z wystąpień ról są próbkowane i zbierane w 3-minutowych interwałach. Te podstawowe dane monitorowania nie są przechowywane na koncie magazynu i nie są z nim skojarzone żadne dodatkowe koszty. 

Dzięki zaawansowanemu monitorowaniu dodatkowe metryki są próbkowane i zbierane z interwałem wynoszącym 5 minut, 1 godzina i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu w tabelach i usuwane po upływie 10 dni. Używane konto magazynu jest konfigurowane przez rolę; dla różnych ról można użyć różnych kont magazynu. 

Aby uzyskać więcej informacji, zobacz [stosowanie rozszerzenia diagnostyki systemu Windows Azure w Cloud Services (obsługa rozszerzona)](enable-wad.md)

## <a name="anti-malware-extension"></a>Rozszerzenie chroniące przed złośliwym oprogramowaniem
Aplikacja lub usługa platformy Azure może włączać i konfigurować usługi Microsoft chroniące przed złośliwym kodem dla platformy Azure Cloud Services przy użyciu poleceń cmdlet programu PowerShell. Należy pamiętać, że program Microsoft chroniący przed złośliwym kodem jest instalowany w stanie wyłączonym na platformie Cloud Services z systemem Windows Server 2012 R2 i starszym, który wymaga działania aplikacji platformy Azure, aby go włączyć. W przypadku systemu Windows Server 2016 i nowszych usługa Windows Defender jest domyślnie włączona, dlatego te polecenia cmdlet mogą służyć do konfigurowania ochrony przed złośliwym kodem.

Aby uzyskać więcej informacji, zobacz [Dodawanie firmy Microsoft chroniącej przed złośliwym kodem do usługi w chmurze platformy Azure przy użyciu rozszerzonej pomocy technicznej (CS-ES)](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)

Aby dowiedzieć się więcej o usłudze Azure chroniącej przed złośliwym kodem, odwiedź [tutaj](https://docs.microsoft.com/azure/security/fundamentals/antimalware)



## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
