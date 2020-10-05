---
title: Interfejs wiersza polecenia platformy Azure — ograniczanie dostępu Import/Export do dysków zarządzanych za pomocą linków prywatnych
description: Włącz prywatne linki dla dysków zarządzanych za pomocą interfejsu wiersza polecenia platformy Azure. Umożliwienie bezpiecznego eksportowania i importowania dysków tylko w obrębie sieci wirtualnej.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5c846bc126d6a6a8b0a8ed4a599c6d43a4d83616
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421224"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Interfejs wiersza polecenia platformy Azure — ograniczanie dostępu do usługi Import/Export dla dysków zarządzanych przy użyciu linków prywatnych

Obsługa linków prywatnych z dyskami zarządzanymi jest obecnie dostępna w wersji zapoznawczej. Możesz użyć [prywatnych punktów końcowych](../../private-link/private-endpoint-overview.md) , aby ograniczyć eksport i importowanie dysków zarządzanych i bezpiecznie uzyskiwać dostęp do danych za pośrednictwem [prywatnego linku](../../private-link/private-link-overview.md) klientów w sieci wirtualnej platformy Azure. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi Managed Disks. Ruch sieciowy między klientami w sieci wirtualnej i dyskami zarządzanymi odbywa się tylko przez sieć wirtualną oraz link prywatny w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Aby użyć prywatnych linków do eksportowania/importowania dysków zarządzanych, należy najpierw utworzyć zasób dostępu do dysku i połączyć go z siecią wirtualną w tej samej subskrypcji, tworząc prywatny punkt końcowy. Następnie skojarz dysk lub migawkę z wystąpieniem dostępu do dysku. Na koniec ustaw właściwość NetworkAccessPolicy dysku lub migawki na `AllowPrivate` . Spowoduje to ograniczenie dostępu do sieci wirtualnej. 

Właściwość NetworkAccessPolicy można ustawić tak, aby `DenyAll` uniemożliwić każdy eksportu danych dysku lub migawki. Wartość domyślna właściwości NetworkAccessPolicy to `AllowAll` .

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Zaloguj się do subskrypcji i Ustaw zmienne

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Tworzenie dostępu do dysku przy użyciu interfejsu wiersza polecenia platformy Azure
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zasady sieciowe, takie jak sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń), nie są obsługiwane dla prywatnych punktów końcowych. Aby można było wdrożyć prywatny punkt końcowy w danej podsieci, w tej podsieci jest wymagane jawne ustawienie Disable. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Wyłącz zasady prywatnego punktu końcowego podsieci

Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, dlatego należy zaktualizować podsieć w celu wyłączenia zasad sieci prywatnych punktów końcowych. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Utwórz prywatny punkt końcowy dla obiektu dostępu do dysku

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS

Utwórz strefę Prywatna strefa DNS dla domeny obiektów blob magazynu, Utwórz link powiązania z Virtual Network i Utwórz grupę stref DNS w celu skojarzenia prywatnego punktu końcowego ze strefą Prywatna strefa DNS. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Tworzenie dysku chronionego za pomocą linków prywatnych
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Tworzenie migawki dysku chronionego za pomocą linków prywatnych
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania dotyczące linków prywatnych](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Eksportowanie/kopiowanie zarządzanych migawek jako dysku VHD do konta magazynu w innym regionie przy użyciu interfejsu wiersza polecenia](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)
