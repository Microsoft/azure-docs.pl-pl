---
title: Przenoszenie wystąpienia systemu Windows AWS EC2 na platformę Azure
description: Przenieś Amazon Web Services (AWS) EC2 wystąpienie systemu Windows na maszynę wirtualną platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 86d3e67b121bf7d66651e1e823e5f137e918a171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550794"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Przenoszenie maszyny wirtualnej z systemem Windows z Amazon Web Services (AWS) na maszynę wirtualną platformy Azure

Jeśli oceniasz maszyny wirtualne platformy Azure do hostowania obciążeń, możesz wyeksportować istniejące Amazon Web Services (AWS) EC2 wystąpienie maszyny wirtualnej systemu Windows, a następnie przekazać wirtualny dysk twardy (VHD) do platformy Azure. Po przekazaniu wirtualnego dysku twardego można utworzyć nową maszynę wirtualną na platformie Azure na podstawie wirtualnego dysku twardego. 

W tym artykule omówiono przeniesienie pojedynczej maszyny wirtualnej z AWS na platformę Azure. Jeśli chcesz przenieść maszyny wirtualne z AWS na platformę Azure w odpowiedniej skali, zobacz [Migrowanie maszyn wirtualnych w Amazon Web Services (AWS) na platformę Azure z Azure Site Recovery](../../site-recovery/migrate-tutorial-aws-azure.md).

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej 
 
Można przekazać zarówno uogólnione, jak i wyspecjalizowane dyski VHD na platformie Azure. Każdy typ wymaga przygotowania maszyny wirtualnej przed eksportowaniem z AWS. 

- **Uogólniony wirtualny** dysk twardy — uogólniony wirtualny dysk twardy otrzymał wszystkie informacje o koncie osobistym, które zostały usunięte przy użyciu programu Sysprep. Jeśli zamierzasz używać dysku VHD jako obrazu do tworzenia nowych maszyn wirtualnych, należy: 
 
    * [Przygotuj maszynę wirtualną z systemem Windows](prepare-for-upload-vhd-image.md).  
    * Uogólnij maszynę wirtualną przy użyciu programu Sysprep.  

 
- **Wyspecjalizowany wirtualny** dysk twardy — wyspecjalizowany wirtualny dysk twardy obsługuje konta użytkowników, aplikacje i inne dane stanu z oryginalnej maszyny wirtualnej. Jeśli zamierzasz utworzyć nową maszynę wirtualną przy użyciu dysku VHD, upewnij się, że zostały wykonane następujące czynności.  
    * [Przygotuj wirtualny dysk twardy systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md). **Nie należy** UOGÓLNIAĆ maszyny wirtualnej przy użyciu programu Sysprep. 
    * Usuń wszystkie narzędzia i agenci wirtualizacji gościa, które są zainstalowane na maszynie wirtualnej (tj. narzędzia VMware). 
    * Upewnij się, że maszyna wirtualna jest skonfigurowana do ściągania adresów IP i ustawień DNS za pośrednictwem protokołu DHCP. Dzięki temu serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania.  


## <a name="export-and-download-the-vhd"></a>Eksportowanie i pobieranie dysku VHD 

Wyeksportuj wystąpienie EC2 do dysku VHD w zasobniku usługi Amazon S3. Wykonaj kroki opisane w artykule dotyczącym usługi Amazon, [eksportując wystąpienie jako maszynę wirtualną przy użyciu importowania/eksportowania maszyny wirtualnej](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) i uruchom polecenie [Create-instance-Export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) , aby wyeksportować wystąpienie EC2 do pliku VHD. 

Wyeksportowany plik VHD zostanie zapisany w określonym zasobniku Amazon S3. Poniżej znajduje się podstawowa składnia eksportowania wirtualnego dysku twardego, po prostu Zastąp tekst symbolu zastępczego \<brackets> informacjami.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Po wyeksportowaniu wirtualnego dysku twardego postępuj zgodnie z instrukcjami podanymi w temacie [jak pobrać obiekt z zasobnika S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) aby pobrać plik VHD z zasobnika S3. 

> [!IMPORTANT]
> AWS opłaty za transfer danych na potrzeby pobierania wirtualnego dysku twardego. Zobacz [Cennik usługi Amazon S3](https://aws.amazon.com/s3/pricing/) , aby uzyskać więcej informacji.


## <a name="next-steps"></a>Następne kroki

Teraz możesz przekazać wirtualny dysk twardy do platformy Azure i utworzyć nową maszynę wirtualną. 

- Jeśli uruchomiono narzędzie Sysprep w źródle, aby **uogólnić** je przed eksportowaniem, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure](upload-generalized-managed.md)
- Jeśli przed wyeksportowaniem nie uruchomiono narzędzia Sysprep, dysk VHD jest traktowany jako **wyspecjalizowany**, zobacz [przekazywanie wyspecjalizowanego wirtualnego dysku twardego do platformy Azure i tworzenie nowej maszyny wirtualnej](create-vm-specialized.md)

 
