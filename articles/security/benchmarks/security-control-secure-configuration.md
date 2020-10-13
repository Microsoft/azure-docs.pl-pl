---
title: Kontrola zabezpieczeń Azure — bezpieczna konfiguracja
description: Bezpieczna konfiguracja kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 347a63cc77c565d800328c19d1d543c2c9efafc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400096"
---
# <a name="security-control-secure-configuration"></a>Kontrola zabezpieczeń: Konfiguracja zabezpieczona

Ustanów, Implementuj i aktywnie Zarządzaj (śledź, Przetwarzaj, Popraw) konfigurację zabezpieczeń zasobów platformy Azure, aby uniemożliwić osobom atakującym wykorzystanie zagrożonych usług i ustawień.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7.1 | 5,1 | Klient |

Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji zasobów platformy Azure. Możesz również użyć wbudowanych definicji Azure Policy.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Zalecenia dotyczące zabezpieczeń — Przewodnik referencyjny](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7.2 | 5,1 | Klient |

Użyj zaleceń Azure Security Center, aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.  Ponadto możesz użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation konfiguracji stanu, aby określić konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia dotyczące Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Zalecenia dotyczące zabezpieczeń — Przewodnik referencyjny](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Przegląd konfiguracji stanu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Przekazywanie wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7.3 | 5.2 | Klient |

Aby wymusić bezpieczne ustawienia w ramach zasobów platformy Azure, użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje].  Ponadto możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie efektów Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Przegląd szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,4 | 5.2 | Shared |

Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach dotyczących zasobów obliczeniowych platformy Azure.  Ponadto można używać szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu Azure Automation, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.   Szablony maszyn wirtualnych firmy Microsoft połączone z Azure Automation konfiguracją żądanego stanu mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń. 

Należy również pamiętać, że obrazy maszyn wirtualnych portalu Azure Marketplace opublikowane przez firmę Microsoft są zarządzane i obsługiwane przez firmę Microsoft. 

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Przegląd konfiguracji stanu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informacje na temat pobierania szablonu maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,5 | 5.3 | Klient |

Za pomocą usługi Azure DevOps bezpiecznie przechowuj swój kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, i zarządzaj nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,6 | 5.3 | Klient |

W przypadku używania obrazów niestandardowych Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usług lub grupom usługi AD w organizacji.  W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Skorzystaj z usługi Azure RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.  

- [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Informacje na temat usługi Azure RBAC dla Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Jak skonfigurować usługę Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Omówienie galerii obrazów udostępnionych](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,7 | 5.4 | Klient |

Zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Mogą również korzystać z wbudowanych definicji zasad związanych z konkretnymi zasobami.  Ponadto można użyć Azure Automation do wdrożenia zmian konfiguracji.

- [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,8 | 5.4 | Klient |

Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem. 

- [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,9 | 5,5 | Klient |

Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure.  Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,10 | 5,5 | Klient |

Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla ustawień systemu operacyjnego i platformy Docker dla kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,11 | 13,1 | Klient |

Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć tajne Zarządzanie aplikacjami w chmurze.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Jak utworzyć Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu Key Vault](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,12 | 4.1 | Klient |

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,13 | 18,1, 18,7 | Klient |

Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń:  [obrona złośliwego oprogramowania](security-control-malware-defense.md)
