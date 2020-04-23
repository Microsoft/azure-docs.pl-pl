---
title: Przenoszenie wystąpienia systemu Windows AWS EC2 na platformę Azure
description: Przenoszenie wystąpienia systemu Windows amazon web services (AWS) EC2 na maszynę wirtualną platformy Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 59d1bf08c0680d222710b55c6d6bdb4d5745da56
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084519"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Przenoszenie maszyny Wirtualnej systemu Windows z usług Amazon Web Services (AWS) na maszynę wirtualną platformy Azure

Jeśli oceniasz maszyny wirtualne platformy Azure pod kątem obsługi obciążeń, możesz wyeksportować istniejące wystąpienie maszyny wirtualnej systemu Windows Amazon Web Services (AWS) EC2, a następnie przekazać wirtualny dysk twardy (VHD) na platformę Azure. Po przekazaniu dysku VHD można utworzyć nową maszynę wirtualną na platformie Azure z dysku VHD. 

W tym artykule opisano przenoszenie pojedynczej maszyny Wirtualnej z usługi AWS na platformę Azure. Jeśli chcesz przenieść maszyny wirtualne z usługi AWS na platformę Azure na dużą skalę, zobacz [Migrowanie maszyn wirtualnych w usługach Amazon Web Services (AWS) na platformę Azure za pomocą usługi Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej 
 
Można przekazać zarówno uogólnione, jak i wyspecjalizowane wirtualne identyfikatory wirtualne na platformę Azure. Każdy typ wymaga przygotowania maszyny Wirtualnej przed wyeksportowaniem z usługi AWS. 

- **Uogólniony VHD** - uogólniony VHD miał wszystkie dane osobowe zostały usunięte za pomocą Sysprep. Jeśli zamierzasz użyć dysku wirtualnego jako obrazu do tworzenia nowych maszyn wirtualnych z, należy: 
 
    * [Przygotowanie maszyny Wirtualnej systemu Windows](prepare-for-upload-vhd-image.md).  
    * Uogólniaj maszynę wirtualną przy użyciu programu Sysprep.  

 
- **Specialized VHD** - wyspecjalizowany dysk wirtualny przechowuje konta użytkowników, aplikacje i inne dane o stanie z oryginalnej maszyny Wirtualnej. Jeśli zamierzasz użyć dysku wirtualnego w stanie as-is, aby utworzyć nową maszynę wirtualną, upewnij się, że następujące kroki zostały wykonane.  
    * [Przygotuj dysk VHD systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md). **Nie** uogólniaj maszyny Wirtualnej przy użyciu programu Sysprep. 
    * Usuń wszystkie narzędzia wirtualizacji gości i agentów zainstalowanych na maszynie wirtualnej (tj. narzędzia VMware). 
    * Upewnij się, że maszyna wirtualna jest skonfigurowana do ściągania jej adresu IP i ustawień DNS za pośrednictwem usługi DHCP. Gwarantuje to, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania.  


## <a name="export-and-download-the-vhd"></a>Eksportowanie i pobieranie dysku VHD 

Wyeksportuj wystąpienie EC2 do dysku VHD w wiadrze Amazon S3. Postępuj zgodnie z instrukcjami w artykule dokumentacji Amazon [Eksportowanie wystąpienia jako maszyny Wirtualnej przy użyciu maszyny Wirtualnej Import/Eksport](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) i uruchom polecenie [create-instance-export-task,](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) aby wyeksportować wystąpienie EC2 do pliku VHD. 

Wyeksportowany plik VHD jest zapisywany w wiaderku Amazon S3, które określisz. Podstawowa składnia eksportowania dysku VHD znajduje się poniżej, \<wystarczy zamienić tekst zastępczy w nawiasach> informacjami.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Po wyeksportowanie dysku VHD postępuj zgodnie z instrukcjami w [jak pobrać obiekt z zasobnika S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) 

> [!IMPORTANT]
> AWS pobiera opłaty za transfer danych za pobranie dysku VHD. Zobacz [Ceny Amazon S3, aby](https://aws.amazon.com/s3/pricing/) uzyskać więcej informacji.


## <a name="next-steps"></a>Następne kroki

Teraz możesz przekazać dysk VHD na platformę Azure i utworzyć nową maszynę wirtualną. 

- Jeśli uruchomiono narzędzie Sysprep w źródle, aby **uogólnić** go przed eksportem, zobacz [Przekazywanie uogólnionego dysku wirtualnego i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure](upload-generalized-managed.md)
- Jeśli program Sysprep nie został uruchomiony przed eksportem, dysk VHD jest uważany za **wyspecjalizowany**, zobacz [Przekazywanie specjalistycznego dysku VHD na platformę Azure i tworzenie nowej maszyny Wirtualnej](create-vm-specialized.md)

 
