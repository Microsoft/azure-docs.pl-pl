---
title: Resetowanie poświadczeń vSphere dla rozwiązania Azure VMware
description: Dowiedz się, jak zresetować poświadczenia vSphere dla chmury prywatnej rozwiązania Azure VMware i upewnić się, że łącznik HCX ma najnowsze poświadczenia vSphere.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603198"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Resetowanie poświadczeń vSphere dla rozwiązania Azure VMware

W tym artykule omówiono procedurę resetowania poświadczeń vSphere dla chmury prywatnej rozwiązania Azure VMware. Pozwoli to zagwarantować, że łącznik HCX ma najnowsze poświadczenia vSphere.

## <a name="reset-your-vsphere-credentials"></a>Zresetuj poświadczenia vSphere

 Najpierw Zresetuj poświadczenia vSphere. Poświadczenia administratora programu vCenter CloudAdmin i NSX-T nie wygasną; można jednak wykonać poniższe kroki, aby wygenerować nowe hasła dla tych kont.

> [!NOTE]
> Jeśli używasz poświadczeń CloudAdmin dla usług połączonych, takich jak HCX, vCenter Orchestrator, vCloud Director lub vRealize, połączenia przestaną działać po aktualizacji hasła.  Te usługi powinny zostać zatrzymane przed zainicjowaniem rotacji haseł.  Niewykonanie tej czynności może spowodować tymczasowe blokowanie kont administratora vCenter CloudAdmin i NSX-T, ponieważ te usługi będą w sposób ciągły dzwonić przy użyciu starych poświadczeń.  Aby uzyskać więcej informacji na temat konfigurowania oddzielnych kont dla podłączonych usług, zobacz [pojęcia dotyczące dostępu i tożsamości](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. W portalu rozwiązań VMware platformy Azure Otwórz wiersz polecenia.

2. Uruchom następujące polecenie, aby zaktualizować hasło programu vCenter CloudAdmin.  Musisz zastąpić {Binding}, {resourceName} i {PrivateCloudName} wartościami rzeczywistymi chmury prywatnej, do której należy konto CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Uruchom następujące polecenie, aby zaktualizować hasło administratora NSX-T. Należy zastąpić {Binding}, {resourceName} i {PrivateCloudName} wartościami rzeczywistymi chmury prywatnej, do której należy konto administratora NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>Upewnij się, że łącznik HCX ma Twoje Najnowsze poświadczenia vSphere

Po zresetowaniu poświadczeń wykonaj następujące kroki, aby upewnić się, że łącznik HCX ma zaktualizowane poświadczenia.

1. Po zmianie hasła przejdź do interfejsu sieci Web lokalnego łącznika HCX przy użyciu protokołu https://{IP urządzenia łącznika HCX}: 443. Upewnij się, że korzystasz z portu 443. Zaloguj się przy użyciu nowych poświadczeń.

2. Na pulpicie nawigacyjnym VMware HCX wybierz pozycję **parowanie lokacji**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Zrzut ekranu pulpitu nawigacyjnego VMware HCX z wyróżnionym parowaniem lokacji.":::
 
3. Wybierz odpowiednie połączenie do automatycznej synchronizacji (jeśli istnieje więcej niż jedna) i wybierz pozycję **Edytuj połączenie**.
 
4. Podaj nowe poświadczenia vSphere i wybierz pozycję **Edytuj**, co spowoduje zapisanie poświadczeń. Zapisywanie powinno być wyświetlane pomyślnie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały omówione Resetowanie poświadczeń usługi vSphere dla rozwiązania Azure VMware, warto zapoznać się z tematem:

- [Konfigurowanie składników sieciowych NSX w rozwiązaniu VMware platformy Azure](configure-nsx-network-components-azure-portal.md).
- [Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Wdrażanie odzyskiwania po awarii maszyn wirtualnych przy użyciu rozwiązania Azure VMware](disaster-recovery-for-virtual-machines.md).
