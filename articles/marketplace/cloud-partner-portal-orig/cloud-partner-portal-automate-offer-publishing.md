---
title: Automatyzuj publikowanie ofert | Portal Azure Marketplace
description: Wyjaśnia, jak programowo zautomatyzować przepływ pracy publikowania maszyn wirtualnych.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 25c7429dc369fb8fc70a135950b16c0a5997656b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280341"
---
<a name="automate-offer-publishing"></a>Automatyzowanie publikowania oferty
=========================

Można również programowo zautomatyzować przepływ pracy publikowania maszyn wirtualnych przy użyciu interfejsów API w sekcji [Dokumentacja interfejsu API](./cloud-partner-portal-api-overview.md) . Istnieją dwa różne scenariusze, które należy wziąć pod uwagę podczas planowania automatyzacji: oferowanie publikowania początkowego i późniejszej publikacji oferty.


<a name="offer-initial-publishing"></a>Oferuj publikację początkową
-------------------------

Po opublikowaniu oferty po raz pierwszy wymagane jest wykonanie kilku dodatkowych czynności przed przekazaniem ich do portalu Marketplace.  Na przykład należy przygotować metadane i utworzyć wersję roboczą oferty. Początkowy przepływ pracy publikowania jest pokazywany na poniższym diagramie.

![Interakcje publikacji początkowej oferty](media/cloud-partner-portal-automate-offer-publishing/first-time-offer-publishing.png)

Poniższy przykładowy kod demonstruje te kroki.

``` csharp
  CreateOfferAndPublish()
  {
      offer = CreateOfferObject()
      // Set all offer attributes
      offer.offerTypeId = "microsoft-azure-virtualmachines",
      // Create offer on server
     result = CloudPartnerPortal.Client.PutOffer(offer);
      if(result.status != success)
      {
        // Do failure actions here
      }
      // Publish created offer
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
        /*Queuing the publish failed, check error message and dofailure actions*/
        return failure;
      }
      // Return success as operation is successfully queued
      return success;
  }

  ValidateAndGoLive()    
  {
      // Confirm the version in preview slot is the version that needs to go live
      offer = CloudPartnerPortal.Client.GetOffer(offerName, "Preview");
      if(!offer[skuName].containsVersion(VMDisk.Version))
      {
          UpdateOfferAndPublish()
      }
      // Go Live on offer and check result
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
          // Queuing the publish failed, check error message and do failure actions
          return failure;
      }  
     
      // Return success as operation is successfully queued
      return success;
  }
```


<a name="subsequent-offer-publishing"></a>Kolejna publikacja oferty
---------------------------

Po zintegrowaniu oferty maszyny wirtualnej z potokiem ciągłej integracji można zautomatyzować przepływ pracy publikowania do uruchamiania za każdym razem, gdy tworzony jest nowy wirtualny dysk twardy (VHD).  Ten przepływ pracy jest zilustrowany na poniższym diagramie i przykładowym kodzie.

![Interakcje kolejnych publikacji oferty](media/cloud-partner-portal-automate-offer-publishing/update-offer-and-publish.png)

``` csharp
    UpdateOfferAndPublish()
    {
        // The routine wakes up after a build finishes. 
        // It determines if the current offer is undergoing publishing and take appropriate action
        // Wake up and get draft offer using offer name
        offer = CloudPartnerPortal.Client.GetOffer(offerName);
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
                // Update the list of VMs with new virtual machine. 
                // This assumes that less than 8 disks are present in the SKU
                // If 8 disks are already present, the client must remove one of the previous disks before adding new disk
                offer[skuName].addDisk(VMDisk)
                // Update the offer on server with new VMDisk
                CloudPartnerPortal.Client.PutOffer(offer);
        }
        // Publish offer and check result. Publish is idempotent, so if a publish was already in progress with latest version, this would return success.
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        
        // Return success as operation is successfully queued
        return success;
    }

    CheckLastOperationStatus()
    {
        Operation op = CloudPartnerPortal.Client.GetLastOpertation(offer.offerName)
        if(op.status == completed)
        {
            return success;
        }
        if(op.status == failure)
           // Do failure actions here
        
        else 
           // Last operation is still running
           // Go to sleep.           
    }

    ValidateAndGoLive()
    {
        // Confirm the version in preview slot is the version that needs to go live
        offer = CloudPartnerPortal.Client.GetOffer(offerName, "Preview");
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
            UpdateOfferAndPublish()
        }
        // Go Live on offer and check result
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        // Return success as operation is successfully queued
        return success;
    }
```
