---
title: Kontrola zabezpieczeń Azure — bezpieczna konfiguracja
description: Bezpieczna konfiguracja kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f945699256b4de9bd87ee4ab7500268302e93089
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880173"
---
# <a name="security-control-secure-configuration"></a>Kontrola zabezpieczeń: Konfiguracja zabezpieczona

Ustanów, Implementuj i aktywnie Zarządzaj (śledź, Przetwarzaj, Popraw) konfigurację zabezpieczeń zasobów platformy Azure, aby uniemożliwić osobom atakującym wykorzystanie zagrożonych usług i ustawień.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7.1 | 5,1 | Customer |

Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji zasobów platformy Azure. Możesz również użyć wbudowanych definicji Azure Policy.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7.2 | 5,1 | Customer |

Użyj zaleceń Azure Security Center, aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.  Ponadto możesz użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation konfiguracji stanu, aby określić konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../../security-center/security-center-recommendations.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../../security-center/recommendations-reference.md)

- [Przegląd konfiguracji stanu Azure Automation](../../automation/automation-dsc-overview.md)

- [Przekazywanie wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](../../virtual-machines/windows/upload-generalized-managed.md)

- [Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7.3 | 5.2 | Customer |

Aby wymusić bezpieczne ustawienia w ramach zasobów platformy Azure, użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje].  Ponadto możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie efektów Azure Policy](../../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,4 | 5.2 | Udostępniona |

Postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach dotyczących zasobów obliczeniowych platformy Azure.  Ponadto można używać szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu Azure Automation, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.   Szablony maszyn wirtualnych firmy Microsoft połączone z Azure Automation konfiguracją żądanego stanu mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń. 

Należy również pamiętać, że obrazy maszyn wirtualnych portalu Azure Marketplace opublikowane przez firmę Microsoft są zarządzane i obsługiwane przez firmę Microsoft. 

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu Azure Resource Manager](../../virtual-machines/windows/ps-template.md)

- [Przegląd konfiguracji stanu Azure Automation](../../automation/automation-dsc-overview.md)

- [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

- [Informacje na temat pobierania szablonu maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template)

- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,5 | 5.3 | Customer |

Za pomocą usługi Azure DevOps bezpiecznie przechowuj swój kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, i zarządzaj nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,6 | 5.3 | Customer |

W przypadku używania obrazów niestandardowych Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. Za pomocą udostępnionej galerii obrazów możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usług lub grupom usługi AD w organizacji.  W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Skorzystaj z usługi Azure RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.  

- [Opis kontroli RBAC platformy Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Informacje na temat usługi Azure RBAC dla Container Registry](../../container-registry/container-registry-roles.md)

- [Jak skonfigurować usługę Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Omówienie galerii obrazów udostępnionych](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,7 | 5.4 | Customer |

Zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla zasobów platformy Azure przy użyciu Azure Policy. Użyj aliasów Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci zasobów platformy Azure. Mogą również korzystać z wbudowanych definicji zasad związanych z konkretnymi zasobami.  Ponadto można użyć Azure Automation do wdrożenia zmian konfiguracji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,8 | 5.4 | Customer |

Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem. 

- [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,9 | 5,5 | Customer |

Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure.  Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak skorygować zalecenia w Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,10 | 5,5 | Customer |

Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla ustawień systemu operacyjnego i platformy Docker dla kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,11 | 13,1 | Customer |

Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć tajne Zarządzanie aplikacjami w chmurze.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../../key-vault/secrets/quick-create-portal.md)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu Key Vault](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,12 | 4.1 | Customer |

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 7,13 | 18,1, 18,7 | Customer |

Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń:  [obrona złośliwego oprogramowania](security-control-malware-defense.md)