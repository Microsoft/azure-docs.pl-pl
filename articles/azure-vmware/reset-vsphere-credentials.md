---
title: Resetowanie poświadczeń vSphere dla rozwiązania Azure VMware
description: Dowiedz się, jak zresetować poświadczenia vSphere dla chmury prywatnej rozwiązania Azure VMware i upewnić się, że łącznik HCX ma najnowsze poświadczenia vSphere.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 1376b6322250da506d32b8ced0a62ddbf60ba9f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587631"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Resetowanie poświadczeń vSphere dla rozwiązania Azure VMware

W tym artykule omówiono procedurę resetowania poświadczeń Menedżera vCenter Server i NSX-T dla chmury prywatnej rozwiązania Azure VMware. Pozwoli to zagwarantować, że łącznik HCX ma najnowsze poświadczenia vCenter Server.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Zresetuj poświadczenia rozwiązania VMware dla platformy Azure

 Najpierw Zresetuj poświadczenia składników rozwiązania Azure VMare. Poświadczenia administratora vCenter Server CloudAdmin i NSX-T nie wygasną; można jednak wykonać poniższe kroki, aby wygenerować nowe hasła dla tych kont.

> [!NOTE]
> Jeśli używasz poświadczeń CloudAdmin dla usług połączonych, takich jak HCX, vRealize Orchestrator, vRealizae Operations Manager lub VMware Horizon, połączenia przestaną działać po aktualizacji hasła.  Te usługi powinny zostać zatrzymane przed zainicjowaniem rotacji haseł.  Niewykonanie tej czynności może spowodować tymczasowe blokowanie kont administratora vCenter CloudAdmin i NSX-T, ponieważ te usługi będą w sposób ciągły dzwonić przy użyciu starych poświadczeń.  Aby uzyskać więcej informacji na temat konfigurowania oddzielnych kont dla podłączonych usług, zobacz [pojęcia dotyczące dostępu i tożsamości](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. W Azure Portal Otwórz sesję Azure Cloud Shell.

2. Uruchom następujące polecenie, aby zaktualizować hasło programu vCenter CloudAdmin.  Musisz zastąpić {Binding}, {resourceName} i {PrivateCloudName} wartościami rzeczywistymi chmury prywatnej, do której należy konto CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Uruchom następujące polecenie, aby zaktualizować hasło administratora NSX-T. Należy zastąpić {Binding}, {resourceName} i {PrivateCloudName} wartościami rzeczywistymi chmury prywatnej, do której należy konto administratora NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Upewnij się, że łącznik HCX ma najnowsze poświadczenia vCenter Server

Po zresetowaniu poświadczeń wykonaj następujące kroki, aby upewnić się, że łącznik HCX ma zaktualizowane poświadczenia.

1. Po zmianie hasła przejdź do interfejsu sieci Web lokalnego łącznika HCX przy użyciu protokołu https://{IP urządzenia łącznika HCX}: 443. Upewnij się, że korzystasz z portu 443. Zaloguj się przy użyciu nowych poświadczeń.

2. Na pulpicie nawigacyjnym VMware HCX wybierz pozycję **parowanie lokacji**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Zrzut ekranu pulpitu nawigacyjnego VMware HCX z wyróżnionym parowaniem lokacji.":::
 
3. Wybierz prawidłowe połączenie z rozwiązaniem VMware platformy Azure (jeśli istnieje więcej niż jeden) i wybierz pozycję **Edytuj połączenie**.
 
4. Podaj nowe vCenter Server CloudAdmin poświadczenia użytkownika i wybierz pozycję **Edytuj**, co spowoduje zapisanie poświadczeń. Zapisywanie powinno być wyświetlane pomyślnie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały omówione Resetowanie poświadczeń vCenter Server i NSX-T Menedżera dla rozwiązań VMware platformy Azure, warto zapoznać się z tematem:

- [Konfigurowanie składników sieciowych NSX w rozwiązaniu VMware platformy Azure](configure-nsx-network-components-azure-portal.md).
- [Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Wdrażanie odzyskiwania po awarii maszyn wirtualnych przy użyciu rozwiązania Azure VMware](disaster-recovery-for-virtual-machines.md).
