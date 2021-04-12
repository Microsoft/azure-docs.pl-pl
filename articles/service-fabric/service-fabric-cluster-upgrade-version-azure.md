---
title: Zarządzanie uaktualnieniami klastra Service Fabric
description: Określ, kiedy i w jaki sposób środowisko uruchomieniowe klastra Service Fabric jest aktualizowane
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731171"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Zarządzanie uaktualnieniami klastra Service Fabric

Klaster usługi Azure Service Fabric to posiadany zasób, ale jest on częściowo zarządzany przez firmę Microsoft. Poniżej przedstawiono sposób zarządzania tym, kiedy i w jaki sposób firma Microsoft aktualizuje klaster usługi Azure Service Fabric.

Aby uzyskać dalsze omówienie pojęć i procesów uaktualniania klastra, zobacz [uaktualnianie i aktualizowanie klastrów Service Fabric platformy Azure](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>Ustaw tryb uaktualniania

Można ustawić, aby klaster odbierał automatyczne uaktualnienia Service Fabric, gdy zostaną wydane przez firmę Microsoft, lub można ręcznie wybrać opcję z listy aktualnie obsługiwanych wersji, ustawiając tryb uaktualniania klastra. Można to zrobić za pomocą kontrolki *tryb uaktualniania sieci szkieletowej* w Azure Portal lub `upgradeMode` Ustawienia w szablonie wdrożenia klastra.

### <a name="azure-portal"></a>Azure Portal

Korzystając z Azure Portal, podczas tworzenia nowego klastra Service Fabric należy wybrać automatyczne lub ręczne uaktualnienia.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Wybór między automatycznym lub ręcznym uaktualnianiem podczas tworzenia nowego klastra w Azure Portal z poziomu opcji &quot;Zaawansowane&quot;":::

Można również przełączać się między automatycznym lub ręcznym uaktualnianiem z sekcji **uaktualnienia sieci szkieletowej** istniejącego zasobu klastra.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Wybierz opcję uaktualnienia automatyczne lub ręczne w sekcji &quot;uaktualnienia sieci szkieletowej&quot; zasobu klastra w Azure Portal":::

### <a name="manual-upgrades-with-azure-portal"></a>Ręczne uaktualnianie przy użyciu Azure Portal

Po wybraniu opcji uaktualniania ręcznego wszystkie czynności, które są potrzebne do zainicjowania uaktualnienia, należy wybrać z listy rozwijanej dostępne wersje, a następnie *zapisać*. Z tego miejsca uaktualnienie klastra zostanie natychmiast przerwane.

[Zasady kondycji klastra](#custom-policies-for-manual-upgrades) (połączenie kondycji węzła i kondycji wszystkich aplikacji działających w klastrze) są przestrzegane podczas uaktualniania. Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie zostanie wycofane.

Po rozwiązaniu problemów, które spowodowały wycofanie, należy ponownie zainicjować uaktualnienie, wykonując te same kroki jak wcześniej.

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Aby zmienić tryb uaktualniania klastra przy użyciu szablonu Menedżer zasobów, należy określić opcję *Automatyczne* lub *Ręczne* dla  `upgradeMode` właściwości definicji zasobu *Microsoft. servicefabric/klastrów* . W przypadku wybrania opcji uaktualnienia ręczne należy również ustawić `clusterCodeVersion` do aktualnie [obsługiwanej wersji sieci szkieletowej](#query-for-supported-cluster-versions).

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Zrzut ekranu przedstawia szablon, który jest w postaci zwykłego tekstu, aby odzwierciedlić strukturę. Właściwości &quot;clusterCodeVersion&quot; i &quot;upgrademode&quot; są wyróżnione.":::

Po pomyślnym wdrożeniu szablonu zostaną zastosowane zmiany w trybie uaktualniania klastra. Jeśli klaster działa w trybie ręcznym, uaktualnienie klastra rozpocznie się automatycznie.

[Zasady kondycji klastra](#custom-policies-for-manual-upgrades) (połączenie kondycji węzła i kondycji wszystkich aplikacji działających w klastrze) są przestrzegane podczas uaktualniania. Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane.

Po rozwiązaniu problemów, które spowodowały wycofanie, należy ponownie zainicjować uaktualnienie, wykonując te same kroki jak wcześniej.

## <a name="wave-deployment-for-automatic-upgrades"></a>Wdrożenie usługi Wave na potrzeby automatycznych uaktualnień

W trybie uaktualniania automatycznego istnieje możliwość włączenia klastra do wdrożenia Wave. W przypadku wdrażania przy użyciu usługi Wave można utworzyć potok do uaktualniania klastrów testowych, etapowych i produkcyjnych w kolejności, oddzielony przez wbudowany tworzenie "czas do zweryfikowania przyszłych wersji Service Fabric przed aktualizacją klastrów produkcyjnych.

### <a name="enable-wave-deployment"></a>Włącz wdrożenie Wave

> [!NOTE]
> Wdrożenie Wave wymaga `2020-12-01-preview` wersji interfejsu API (lub nowszej) dla zasobu *Microsoft. servicefabric/klastrów* .

Aby włączyć wdrożenie Wave na potrzeby automatycznego uaktualniania, najpierw określ, która fala ma zostać przypisana do klastra:

* **Wave 0** ( `Wave0` ): klastry są aktualizowane zaraz po wydaniu nowej kompilacji Service Fabric. Przeznaczone dla klastrów testowych/deweloperskich.
* **Fala 1** ( `Wave1` ): klastry są aktualizowane jeden tydzień (siedem dni) po wydaniu nowej kompilacji. Przeznaczone dla klastrów przedprodukcyjnych/produkcyjnych.
* **Fala 2** ( `Wave2` ): klastry są aktualizowane po upływie dwóch tygodni (14 dni) po wydaniu nowej kompilacji. Przeznaczone dla klastrów produkcyjnych.

Następnie po prostu Dodaj `upgradeWave` Właściwość do szablonu zasobów klastra z jedną z wartości Wave wymienionych powyżej. Upewnij się, że wersja interfejsu API zasobów klastra jest `2020-12-01-preview` lub nowsza.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Po wdrożeniu zaktualizowanego szablonu klaster zostanie zarejestrowany w określonej fazie dla następnego okresu uaktualniania i po nim.

Możesz [zarejestrować się w celu otrzymywania powiadomień e-mail](#register-for-notifications) z linkami, aby uzyskać pomoc w przypadku niepowodzenia uaktualniania klastra.

### <a name="register-for-notifications"></a>Rejestrowanie do otrzymywania powiadomień

Możesz zarejestrować się w celu otrzymywania powiadomień, gdy uaktualnienie klastra zakończy się niepowodzeniem. Do wskazanych adresów e-mail zostanie wysłana wiadomość e-mail z dodatkowymi szczegółami dotyczącymi błędu uaktualnienia i linki do dalszej pomocy.

> [!NOTE]
> Rejestracja w usłudze Wave Deployment nie jest wymagana do otrzymywania powiadomień dotyczących błędów uaktualnienia.

Aby zarejestrować się w powiadomieniach, należy dodać `notifications` sekcję do szablonu zasobów klastra i wyznaczyć co najmniej jeden adres e-mail (*odbiorcy*) do odbierania powiadomień:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Po wdrożeniu zaktualizowanego szablonu użytkownik zostanie zarejestrowany na potrzeby powiadomień o niepowodzeniu uaktualnienia.

## <a name="custom-policies-for-manual-upgrades"></a>Zasady niestandardowe dla uaktualnień ręcznych

Możesz określić niestandardowe zasady dotyczące kondycji na potrzeby ręcznego uaktualniania klastra. Te zasady są stosowane przy każdym wybraniu nowej wersji środowiska uruchomieniowego, która wyzwala system w celu uruchomienia uaktualnienia klastra. Jeśli nie zastąpisz zasad, zostaną użyte wartości domyślne.

Możesz określić niestandardowe zasady dotyczące kondycji lub przejrzeć bieżące ustawienia w sekcji **uaktualnienia sieci szkieletowej** zasobu klastra w Azure Portal, wybierając opcję opcja *niestandardowa* dla **zasad uaktualniania**.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Wybierz opcję zasady uaktualniania niestandardowego w sekcji &quot;uaktualnienia sieci szkieletowej&quot; zasobu klastra w Azure Portal w celu ustawienia niestandardowych zasad kondycji podczas uaktualniania":::

## <a name="query-for-supported-cluster-versions"></a>Zapytanie o obsługiwane wersje klastra

Korzystając z [interfejsu API REST platformy Azure](/rest/api/azure/) , można wyświetlić listę wszystkich dostępnych wersji środowiska uruchomieniowego Service Fabric ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) dostępnych dla określonej lokalizacji i subskrypcji.

Aby uzyskać więcej informacji na temat obsługiwanych wersji i systemów operacyjnych, można także zapoznać się z [wersjami Service Fabric](service-fabric-versions.md) .

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

`supportExpiryUtc`W raportach wyjściowych, gdy dana wersja wygaśnie lub wygasła. Najnowsze wersje nie będą miały prawidłowej daty, ale zamiast wartości *9999-12-31T23:59:59.9999999*, co oznacza, że data wygaśnięcia nie jest jeszcze ustawiona.


## <a name="next-steps"></a>Następne kroki

* [Zarządzanie uaktualnieniami Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Dostosuj [Ustawienia klastra Service Fabric](service-fabric-cluster-fabric-settings.md)
* [Skalowanie klastra do i wychodzącego](service-fabric-cluster-scale-in-out.md)
* Informacje o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
